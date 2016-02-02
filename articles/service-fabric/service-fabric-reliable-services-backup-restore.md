<properties
   pageTitle="Reliable Service のバックアップと復元 | Microsoft Azure"
   description="Service Fabric Reliable Service のバックアップと復元の概念をまとめた文書"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/01/2015"
   ms.author="mcoskun"/>


# Reliable Services のバックアップと復元

Service Fabric は高可用性プラットフォームであり、複数のノードにわたりステートを複製し、その高可用性を維持します。 つまり、クラスター内の 1 つのノードに障害が発生した場合でも、サービスは引き続き利用できます。 このプラットフォームに組み込まれている冗長性で十分な場合もありますが、(外部ストアに) サービスのデータをバックアップすることが望ましくなります。

たとえば、次のシナリオではサービスのデータをバックアップすることが望まれます。

* Service Fabric クラスター全体または特定のパーティションを実行しているすべてのノードが永久に失われた場合。 これは、たとえば、geo レプリケーションされないとき、クラスター全体が 1 か所のデータ センターにあり、データ センター全体が停止する場合などに起こります。

* ステートが誤って削除される/壊れるなどの管理エラー。 これは、十分な権限を持つ管理者が誤ってサービスを削除した場合に起こる可能性があります。

* サービスのバグが引き起こしたデータの破損。 これは、サービス コードのアップグレードが、Reliable Collection に不正なデータを書き込み始めると起こる可能性があります。 このような場合は、コードとデータの両方を、以前の状態に復元する必要があります。

* オフライン データ処理 データを生成するサービスから離れているビジネス インテリジェンスのためにデータのオフライン処理を用意しておけば便利です。

バックアップ/復元機能では、 Reliable Services API を基盤とするサービスでバックアップを作成し、復元できます。 このプラットフォームが提供するバックアップ API では、読み書き操作をブロックせずにパーティションのステートをバックアップできます。 復元 API では、選択したバックアップからパーティションのステートを復元できます。


## バックアップの方法

サービスの作成者は、バックアップのタイミングと保管場所を完全に制御できます。

バックアップを開始するには、サービスで **IReliableStateManager.BackupAsync** を呼び出す必要があります。 バックアップはプライマリ レプリカ以外からは実行できません。書き込みステータスを与える必要があります。

最も単純なオーバー ロードは、次のように **BackupAsync** Func で<< BackupInfo, bool >> と呼ばれる **backupCallback**します。

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo** は、ランタイムがバックアップを保存したフォルダー (BackupInfo.Directory) など、バックアップに関する情報を提供します。 このコールバック関数は BackupInfo.Directory が外部ストアまたは別の場所に移動することを要求します。 この関数はまた、バックアップ フォルダーがそのターゲット フォルダーに移動したかどうかを示すブール値を返します。

次のコードは、backupCallback を利用し、バックアップを Azure Storage にアップロードするしくみを示しています。

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

上の例では、**ExternalBackupStore** は Azure BLOB Storage と連動するためのサンプル クラスであり、**UploadBackupFolderAsync** はフォルダーを圧縮し、Azure BLOB ストアに配置するメソッドです。

注意:

- ある時点で配信できる **BackupAsync** はレプリカあたり 1 つに限られます。 一度に複数の **BackupAsync** 呼び出しを行うと、**FabricBackupInProgressException** がスローされ、配信バックアップが 1 つに制限されます。

- バックアップの途中でレプリカにエラーが発生した場合、バックアップは完了しないことがあります。 そのため、フェールオーバーが完了すると、サービスは必要に応じて **BackupAsync** を呼び出し、バックアップを再開する必要があります。

## データの復元方法

通常、復元操作が必要になるケースは、次のカテゴリのいずれかに分類されます。


1. サービス パーティションのデータが失われた場合。 たとえば、パーティションの 3 つのレプリカのうちの 2 つのディスク (プライマリ レプリカを含む) が壊れた/消去された場合です。 新しいプライマリに、バックアップからデータを復元する必要がある場合。

2. サービス全体が失われた場合。 たとえば、管理者がサービス全体を削除し、サービスとデータを復元する必要がある場合です。

3. (たとえば、アプリケーション バグに起因して) サービスが壊れたアプリケーション データを複製した場合。 その場合、サービスをアップグレードするか、元の状態に戻し、データが壊れるの原因を取り除き、壊れていないデータを復元する必要があります。

さまざまな方法が可能ですが、RestoreAsync で上記のシナリオから復元する方法について紹介します。

## パーティション データの損失

この場合、ランタイムが自動的にデータ損失を検出し、**OnDataLossAsync** API を呼び出します。

サービス作成者は、次を実行して回復する必要があります。
- オーバーライド **IReliableStateManager** を新しいのする ReliableStateManager を返して、データ損失の場合に呼び出されるコールバック関数の呼び出しを提供します。
- サービスのバックアップを格納する外部の場所で最新のバックアップを見つけます。
- 最新のバックアップの状態が新しいプライマリの背後にある場合は、false を返します。 これにより、新しいプライマリが書き込まれないこと取得過剰古いデータが保証されます。
- 最新のバックアップをダウンロードします (およびそれが圧縮されている場合は、バックアップ フォルダーにバックアップを解凍します)。
- バックアップ フォルダーのパスで **IReliableStateManager.RestoreAsync** を呼び出します。
- 復元が成功した場合は、true を返します。

次の例では、**IReliableStateManager** のオーバーライドと共に **OnDataLossAsync** メソッドを実装しています。

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(new ReliableStateManagerConfiguration(
            onDataLossEvent: this.OnDataLossAsync));
}

protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    await this.StateManager.RestoreAsync(backupFolder);

    return true;
}
```

>[AZURE.NOTE] RestorePolicy は既定で「Safe」に設定されます。 つまり、バックアップ フォルダーにこのレプリカに含まれているステートと同じか、それより古いステートが含まれていることが検出された場合、RestoreAsync API は失敗し、ArgumentException をスローします。 RestorePolicy.Force でこの安全性チェックを省略できます。

## サービスの削除または紛失

サービスが削除された場合、先にサービスを作成し直さないとデータを復元できません。 データを途切れなく復元するために、サービスを同じ構成 (パーティショニング スキームなど) で作成することが重要です。 サービスが起動したら、そのサービスのあらゆるパーティションで、データを復元する API (上記の **OnDataLossAsync**) を呼び出す必要があります。 その方法の 1 つは、すべてのパーティションで **FabricClient.ServiceManager.InvokeDataLossAsync** を使用することです。

この時点から、実装は上記のシナリオと同じになります。 各パーティションで、外部ストアから最新の関連バックアップを復元する必要があります。 1 つ注意してことがあります。ランタイムが (動的に) パーティション ID を作成するため、パーティション ID が変更されている可能性があります。 そのため、各パーティションで復元する最新バックアップを識別するために、サービスでパーティション情報とサービス名を格納しておく必要があります。


## 壊れたアプリケーション データの複製

新しくデプロイしたアプリケーションのアップグレードにバグがある場合、データが壊れてしまうことがあります。たとえば、アプリケーション アップグレードによって、Reliable Dictionary にあるすべての電話番号の記録が無効な局番で更新されてしまいます。 そのような場合、Service Fabric は保存されるデータの性質を認識しないため、無効な電話番号が複製されます。

データを壊すこのような重大なバグが検出された場合、最初にするべきことは、サービスをアプリケーション レベルで停止し、可能であれば、バグのないバージョンのアプリケーション コードにアップグレードします。 しかしながら、サービス コードを修正しても、データは壊れたままであり、復元する必要があります。 そのような場合、最新バックアップも壊れている可能性があるので、最新バックアップを復元するだけでは不十分です。 そのため、データが壊れる前に行われた最後のバックアップを探す必要があります。

どのバックアップが破損しているかがわからない場合は、新しい Service Fabric クラスターをデプロイし、上記の "サービスの削除" のシナリオと同様に、影響を受けたパーティションのバックアップを復元できます。 各パーティションで、最新から一番古いものの順でバックアップの復元を開始します。 破損していないバックアップを見つけたら、このパーティションの (そのバックアップより) 新しいすべてのバックアップを移動、削除します。 各パーティションでこの手順を繰り返します。 ここで、運用クラスターのパーティションで **OnDataLossAsync** が呼び出されたら、外部ストアで見つかった最後のバックアップが上記の手順で選択されたバックアップとなります。

「サービスの削除」の手順を利用し、サービス バックアップのステートをバグのあるコードがステートを壊す前のステートに戻すことができます。

以下の点に注意してください。

- 復元の際、復元されるバックアップがデータ損失前のパーティションの状態よりも古くなっている可能性があります。 そのため、復元は、可能な限り多くのデータを復元するための最後の手段として使用するべきです。

- バックアップ フォルダー パスとバックアップ フォルダー内のファイルのパスを表す文字列は、FabricDataRoot パスや Application Type 名の長さによっては 255 文字を超える可能性があります。 これにより、**Directory.Move** などの一部の .Net メソッドで **PathTooLongException** がスローされることがあります。 回避策の 1 つは、**CopyFile** などの kernel32 API を直接呼び出すことです。


## 内部: バックアップと復元に関する追加情報

### Backup

Reliable State Manager には、読み書き操作を中断することなく、一貫性のあるバックアップを作成する機能があります。 そのために、チェックポイントとログ永続化のメカニズムが活用されます。 Reliable State Manager は特定の時点でファジー (簡易) チェックポイントを作成し、トランザクション ログからの負荷を軽減し、復元時間を早めます。 IReliableStateManager.**BackupAsync** が呼び出されると、Reliable State Manager はすべての Reliable Object に、最新のチェックポイント ファイルをローカルのバックアップ フォルダーにコピーするように指示します。 次に、Reliable State Manager は「開始ポインター」から最新のログ レコードまですべてのログ レコードをバックアップ フォルダーにコピーします。 ログ レコードが、最新のログ レコードまで、すべてバックアップに追加され、Reliable State Manager が Write Ahead Logging を維持するため、Reliable State Manager では、コミットされた (CommitAsync が正常に返ります) すべてのトランザクションがバックアップに含まれることが保証されます。

**BackupAsync** が呼び出された後にコミットされるトランザクションは、バックアップに含まれていることもあれば、含まれていないこともあります。 プラットフォームによりローカルのバックアップ フォルダーにデータが入力されると (すなわち、ローカルのバックアップがランタイムにより完了すると)、サービスのバックアップ コールバックが呼び出されます。 このコールバックは、Azure Storage などの外部の場所にバックアップ フォルダーを移動する役割を担います。

### 復元

Reliable State Manager には、IReliableStateManager.RestoreAsync API でバックアップから復元する機能があります。 RestoreAsync メソッドは **OnDataLossAsync** メソッドの中でしか呼び出すことができません。 **OnDataLossAsync** により返されるブール値は、サービスの状態が外部ソースから復元されたかどうかを示すものです。 **OnDataLossAsync** が true を返した場合、Service Fabric はこのプライマリから他のすべてのレプリカを再構築します。 Service Fabric により、**OnDataLossAsync** を受信するレプリカは最初にプライマリ ロールに切り替わるものの、読み取り状態または書き込み状態にはなりません。 これは、StatefulService の実装機能に関して、**OnDataLossAsync** が正常に完了するまで RunAsync が呼び出されないことを意味します。 次に、新しいプライマリで **OnDataLossAsync** が呼び出されます。 サービスが (true または false を返し) この API を完了し、関連再構成を完了するまで、API は 1 つずつ呼び出され続けます。


RestoreAsync は最初、それが呼び出されたプライマリ レプリカの既存ステートをすべて削除します。 次に、Reliable State Manager はバックアップ フォルダーに存在するすべての Reliable Objects を作成します。 次に、Reliable Objects はバックアップ フォルダーのチェックポイントから復元するように指示されます。 最後に、Reliable State Manager はバックアップ フォルダーのログ レコードからそれ自体のステートを復元し、復元を実行します。 復元プロセスの一環として、バックアップ フォルダーにコミット ログ レコードがあり、「開始ポイント」から始まる操作が Reliable Objects でリプレイされます。 この手順により、復元したステートに一貫性が与えられます。





