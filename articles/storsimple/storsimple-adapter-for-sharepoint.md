<properties 
   pageTitle="SharePoint 用 StorSimple アダプター | Microsoft Azure"
   description="SharePoint サーバー ファームに SharePoint 用 StorSimple アダプターをインストールして構成または削除する方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/17/2015"
   ms.author="v-sharos" />

# SharePoint 用 StorSimple アダプターをインストールして構成する

## 概要

SharePoint 用 StorSimple アダプターは、SharePoint サーバー ファームに Microsoft Azure StorSimple の柔軟なストレージとデータ保護を提供できるコンポーネントです。 このアダプターを使用して、バイナリ ラージ オブジェクト (BLOB) コンテンツを SQL Server コンテンツ データベースから Microsoft Azure StorSimple ハイブリッド クラウド ストレージ デバイスに移動できます。

SharePoint 用 StorSimple アダプターはリモート BLOB ストレージ (RBS) プロバイダーとして機能し、SQL Server のリモート BLOB ストレージ機能を使用して、StorSimple デバイスでサポートされるファイル サーバー上に、構造化されていない SharePoint コンテンツ (BLOB の形式) を格納します。

>[AZURE.NOTE] SharePoint 用 StorSimple アダプターは、SharePoint Server 2010 Remote BLOB ストレージ (RBS) をサポートします。 SharePoint Server 2010 の External BLOB ストレージ (EBS) はサポートしていません。

- SharePoint 用 StorSimple アダプターをダウンロードするには [SharePoint 用 StorSimple アダプター][1] Microsoft ダウンロード センターからです。

- RBS の計画と RBS の制限事項についてを参照してください [SharePoint 2013 で RBS を使用するかを決める][2] または [RBS を計画 (SharePoint Server 2010)][3]します。

この概要の残りの部分では、SharePoint 用 StorSimple アダプターのロールのほか、アダプターをインストールおよび構成する前に認識しておく必要がある SharePoint の容量とパフォーマンスの制限事項について簡単に説明します。 この情報を確認した後に移動 [SharePoint インストール用 StorSimple アダプター](#storsimple-adapter-for-sharepoint-installation) アダプター設定を開始します。

### SharePoint 用 StorSimple アダプターの利点

SharePoint サイトのコンテンツは、1 つまたは複数のコンテンツ データベースに非構造化 BLOB データとして保存されます。 これらのデータベースは、既定で SQL Server を実行しているコンピューターでホストされ、SharePoint サーバー ファームに配置されます。 BLOB のサイズは急増し、大量のオンプレミス ストレージを消費する可能性があります。 このような理由から、場合によっては別の低コストのストレージ ソリューションが必要になります。 SQL Server は、リモート BLOB ストレージ (RBS) というテクノロジを提供しています。RBS を使用すると、SQL Server データベースの外部にあるファイル システムに BLOB コンテンツを保存できるようになります。 また、RBS を使用すると、BLOB の保存先を SQL Server を実行しているコンピューターのファイル システムか、別のサーバー コンピューターのファイル システムにすることができます。

RBS を使用する場合、SharePoint 用 StorSimple アダプターなどの RBS プロバイダーを使用して、SharePoint で RBS を有効にする必要があります。 SharePoint 用 StorSimple アダプターは RBS と連携しているため、Microsoft Azure StorSimple システムでバックアップされるサーバーに BLOB を移動できます。 Microsoft Azure StorSimple は、使用状況に応じてローカルまたはクラウドに BLOB データを格納します。 非常にアクティブな BLOB (通常、層 1 またはホット データと呼ばれます) は、ローカルに配置されます。 あまりアクティブではないデータとアーカイブ データは、クラウドに配置されます。 コンテンツ データベースで RBS を有効にすると、SharePoint に作成された新しい BLOB コンテンツは、コンテンツ データベースではなく、StorSimple デバイスに保存されるようになります。

RBS の Microsoft Azure StorSimple 実装には、次の利点があります。

- BLOB コンテンツを別のサーバーに移動すると、SQL Server にかかるクエリの負荷が軽減され、SQL Server の応答性が改善されます。 

- Azure StorSimple は、重複除去と圧縮を使用してデータ サイズを削減します。

- Azure StorSimple は、ローカル スナップショットとクラウド スナップショットの形式でデータ保護を提供しています。 また、データベース自体を StorSimple デバイスに配置すると、クラッシュ整合方式でコンテンツ データベースと BLOB の両方をバックアップできます  (デバイスへのコンテンツ データベースの移動は、StorSimple 8000 シリーズ デバイスについてのみサポートされます。 5000 または 7000 シリーズについてはこの機能はサポートされません)。

- Azure StorSimple には、フェールオーバー、ファイルとボリュームの回復 (テスト回復を含む)、データの迅速な復元などの災害復旧機能が含まれています。

- Kroll Ontrack PowerControls などのデータ復旧ソフトウェアと BLOB データの StorSimple スナップショットを使用することで、SharePoint コンテンツをアイテムレベルで復旧できます  (このデータ復旧ソフトウェアは別途購入します)。

- SharePoint 用 StorSimple アダプターは SharePoint サーバーの全体管理ポータルに接続されるため、1 か所で SharePoint ソリューション全体を管理できます。

BLOB コンテンツをファイル システムに移動すると、さらにコストの節約や利点があります。 たとえば、RBS を使用すると、コストの高い層 1 ストレージの必要性が軽減されます。また、コンテンツ データベースのサイズが小さくなるため、RBS により SharePoint サーバー ファームで必要なデータベース数を減らすことができます。 ただし、データベース サイズの制限や RBS 以外のコンテンツの量など、他の要因がストレージの要件に影響する可能性もあります。 コストおよび RBS を使用する利点の詳細については、次を参照してください。 [RBS を計画 (SharePoint Foundation 2010)][4] と [SharePoint 2013 で RBS を使用するかを決める][5]します。

### 容量とパフォーマンスに関する制限事項

SharePoint ソリューションで RBS を使用することを検討する前に、SharePoint Server 2010 と SharePoint Server 2013 でテストされたパフォーマンスと容量に関する制限事項と、その制限事項と許容可能なパフォーマンスとの関連について注意する必要があります。 詳細については、次を参照してください。 [ソフトウェアの境界と SharePoint 2013 の制限](https://technet.microsoft.com/library/cc262787.aspx)します。

RBS を構成する前に、次の点を確認してください。

- コンテンツの合計サイズ (コンテンツ データベースのサイズと、関連する外部化された BLOB のサイズの合計) が、SharePoint でサポートされている RBS サイズの上限を超えていないことを確認します。 上限は 200 GB です。 

    **コンテンツ データベースと BLOB のサイズを測定するには**

     1. 全体管理 WFE でこのクエリを実行します。 SharePoint 管理シェルを開始し、次の Windows PowerShell コマンドを入力すると、コンテンツ データベースのサイズが表示されます。

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         上記の手順で、ディスク上のコンテンツ DB のサイズを取得します。

     2. 各コンテンツ データベースで、SQL Management Studio の SQL サーバー ボックスを使用して次の SQL クエリのいずれかを実行し、その結果を手順 1. で取得したサイズに追加します。

        SharePoint 2013 コンテンツ データベースで、以下を入力します。

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        SharePoint 2010 コンテンツ データベースで、以下を入力します。

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        上記の手順で、外部化されている BLOB のサイズを取得します。

- すべての BLOB とデータベースのコンテンツは、StorSimple デバイスのローカルに保存することをお勧めします。 StorSimple デバイスは、高可用に対応する 2 ノード クラスターです。 StorSimple デバイスにコンテンツ データベースと BLOB を配置することで、高可用を実現できます。

    コンテンツ データベースを StorSimple デバイスに移動するには、従来の SQL Server の移行に関するベスト プラクティスを利用してください。 必ず、データベースのすべての BLOB コンテンツが RBS 経由でファイル共有に移動してから、データベースを移動してください。 コンテンツ データベースを StorSimple デバイスに移動する場合は、デバイスのコンテンツ データベース ストレージをプライマリ ボリュームとして構成することをお勧めします。

- StorSimple デバイスのローカルに保存されているコンテンツが Microsoft Azure クラウド ストレージに移動されないようにする方法は、Microsoft Azure StorSimple にはありません。 コンテンツ データベースが StorSimple デバイスに残り、Microsoft Azure に移動されないようにするために (移動されると、SharePoint トランザクションの応答時間に悪影響があります)、StorSimple デバイスの他のワークロードを理解し、管理することが重要です。 デバイスが SharePoint コンテンツ データベースのワークロードと SharePoint ファイル共有のワークロードを既にホストしている場合、データの書き込みレートが高いワークロードをホストするように StorSimple デバイスを構成しないことをお勧めします。

- StorSimple デバイスでコンテンツ データベースを保存していない場合、RBS をサポートする、SQL Server の高可用性に関する従来のベスト プラクティスを利用します。 SQL Server クラスタリングでは RBS がサポートされますが、SQL Server ミラーリングではサポートされません。 

>[AZURE.WARNING] RBS を有効にしない場合は、コンテンツ データベースを StorSimple デバイスに移動して避けてください。 このような構成はテストされていません。
 
## SharePoint 用 StorSimple アダプターのインストール

SharePoint 用 StorSimple アダプターをインストールする前に、StorSimple デバイスを構成し、SharePoint サーバー ファームと SQL Server がすべての前提条件を満たすことを確認する必要があります。 このチュートリアルでは、SharePoint 用 StorSimple アダプターのインストールとアップグレードに関する構成要件と手順について説明します。 

## 構成の前提条件

SharePoint 用 StorSimple アダプターをインストールする前に、StorSimple デバイス、SharePoint サーバー ファーム、SQL Server が次の前提条件を満たしていることを確認してください。

### システム要件

SharePoint 用 StorSimple アダプターは、次のハードウェアとソフトウェアで動作します。

- サポート対象のオペレーティング システム – Windows Server 2008 R2 SP1、Windows Server 2012、Windows Server 2012 R2 

- サポート対象の SharePoint のバージョン – SharePoint Server 2010、SharePoint Server 2013

- サポート対象の SQL Server のバージョン – SQL Server 2008 Enterprise Edition、SQL Server 2008 R2 Enterprise Edition、SQL Server 2012 Enterprise Edition

- サポート対象の StorSimple デバイス – StorSimple 8000 シリーズ、StorSimple 7000 シリーズ、StorSimple 5000 シリーズ

### StorSimple デバイスの構成に関する前提条件

StorSimple デバイスはブロック デバイスなので、データをホストできるファイル サーバーが必要です。 SharePoint ファームの既存のサーバーではなく、別のサーバーを使用することをお勧めします。 このファイル サーバーは、コンテンツ データベースをホストする SQL Server コンピューターと同じローカル エリア ネットワーク (LAN) 上にある必要があります。 

>[AZURE.TIP] 
>
>- 高可用性用の SharePoint ファームを構成する場合は、高可用性用のファイル サーバーもデプロイする必要があります。
>
>- StorSimple デバイスにコンテンツ データベースを保存しない場合は、RBS をサポートする、従来の高可用性のベストプラクティスを使用してください。 SQL Server クラスタリングでは RBS がサポートされますが、SQL Server ミラーリングではサポートされません。 

StorSimple デバイスが適切に構成されていることと、SharePoint のデプロイをサポートできる適切なボリュームが構成され、SQL Server コンピューターからアクセスできることを確認します。 移動して [オンプレミス StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md) まだデプロイして、StorSimple デバイスを構成した場合。 StorSimple デバイスの IP アドレスをメモしてください。SharePoint 用 StorSimple アダプターのインストール時に必要になります。 

また、BLOB の外部化に使用するボリュームが、次の要件を満たしていることを確認します。

- ボリュームは、64 KB アロケーション ユニット サイズでフォーマットする必要があります。

- Web フロント エンド (WFE) とアプリケーション サーバーは、汎用名前付け規則 (UNC) パスを介してボリュームにアクセスできる必要があります。 

- また、このボリュームに書き込めるように SharePoint サーバー ファームを構成する必要があります。

>[AZURE.NOTE] すべての BLOB の外部化する必要があります、StorSimple デバイスを通過インストールして、アダプターを構成すると、(デバイスはボリュームを SQL Server に提示し、記憶域階層の管理)。 BLOB の外部化に他の対象は使用できません。
 
StorSimple Snapshot Manager を使用して BLOB とデータベース データのスナップショットを作成する予定の場合、データベース サーバーに StorSimple Snapshot Manager をインストールし、SQL ライター サービスを使用して Windows ボリューム シャドウ コピー サービス (VSS) を実装できるようにします。 

>[AZURE.IMPORTANT] StorSimple Snapshot Manager は、SharePoint VSS ライターをサポートしていないと、SharePoint データのアプリケーション整合性スナップショットを取得できません。 SharePoint のシナリオで StorSimple Snapshot Manager が提供するのは、クラッシュ整合バックアップのみです。 
 
## SharePoint ファームの構成に関する前提条件

SharePoint サーバー ファームが次のように正しく構成されていることを確認してください。

- SharePoint サーバー ファームが正常な状態であることを確認し、次の点を確認します。 

- ファームに登録されているすべての SharePoint WFE とアプリケーション サーバーが実行されており、SharePoint 用 StorSimple アダプターをインストールするサーバーから PING で検出できる。

- SharePoint Timer サービス (SPTimerV3 または SPTimerV4) が各 WFE サーバーとアプリケーション サーバーで実行されている。

- SharePoint サーバーの全体管理サイトが実行されている SharePoint Timer サービスと IIS アプリケーション プールの両方に、管理者特権がある。 

- Internet Explorer セキュリティ強化の構成 (IE ESC) が無効になっていることを確認します。 次の手順で IE ESC を無効にします。

    1. Internet Explorer のすべてのインスタンスを閉じます。

    2. サーバー マネージャーを開きます。

    3. 左のウィンドウで **ローカル サーバー**します。

    4. 右側のウィンドウ] の横に **IE セキュリティ強化の構成**, をクリックして **に**です。

    5.  **管理者**, をクリックして **オフ**します。

    6. Click **OK**.

## リモート BLOB ストレージ (RBS) の前提条件

サポート対象バージョンの SQL Server を使用していることを確認します。 次のバージョンのみがサポート対象であり、RBS を使用できます。

- SQL Server 2008 Enterprise Edition

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

BLOB は、StorSimple デバイス が SQL Server に提示しているボリュームでのみ外部化できます。 その他の対象では、BLOB の外部化はサポートされていません。

すべての前提条件の構成手順を完了する場合に移動して [SharePoint 用 StorSimple アダプターをインストール](#install-the-storsimple-adapter-for-sharepoint)します。

## SharePoint 用 StorSimple アダプターをインストールする

SharePoint 用 StorSimple アダプターをインストールするには、次の手順に従います。 ソフトウェアを再インストールする場合は、次を参照してください。 [アップグレードまたは SharePoint 用 StorSimple アダプターを再インストール](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)します。 インストールに必要な時間は、SharePoint サーバー ファームでの SharePoint データベースの合計数によって異なります。

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## RBS の構成

SharePoint 用 StorSimple アダプターをインストールしたら、次の手順に従って RBS を構成します。

>[AZURE.TIP] SharePoint 用 StorSimple アダプターは、RBS を有効になっているか、SharePoint ファーム内の各コンテンツ データベースで無効にできるように SharePoint サーバーの全体管理ページに接続します。 ただし、コンテンツ データベースの RBS を有効または無効にすると、IIS がリセットされ、ファームの構成によっては、SharePoint Web フロントエンド (WFE) の可用性が一時的に中断する可能性があります  (フロントエンド Load Balancer の使用、現在のサーバーのワークロードなどの要素によって、この中断時間を短くしたり、中断をなくしたりすることができます)。ユーザーを中断から保護するために、計画的な保守期間にのみ RBS を有効または無効にすることをお勧めします。

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## ガベージ コレクションを構成する

SharePoint サイトからオブジェクトを削除しても、RBS ストア ボリュームからは自動的に削除されません。 その代わり、非同期のバックグラウンド メンテナンス プログラムにより、孤立した BLOB がファイル ストアから削除されます。 システム管理者はこのプロセスをスケジュールして定期的に実行するか、必要に応じていつでも開始できます。

このメンテナンス プログラム (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) は、RBS を有効にするとすべての SharePoint WFE サーバーとアプリケーション サーバーに自動的にインストールされます。 プログラムは、次の場所にインストールされます。 <boot drive>: \Program Files\Microsoft SQL リモート Blob ストレージ 10.50\Maintainer\

構成およびメンテナンス プログラムの使用に関する詳細については、次を参照してください。 [SharePoint Server 2013 で RBS の維持][8]します。

>[AZURE.IMPORTANT] RBS メンテナンス ツール プログラムは、負荷の高いリソースです。 この実行をスケジュールするのは、SharePoint ファームでのアクティビティが少ない時間帯のみにしてください。

### 孤立した BLOB をすぐに削除する

孤立した BLOB をすぐに削除する必要がある場合は、次の手順を使用します。 この手順は、以下のコンポーネントを備えた SharePoint 2013 環境で孤立した BLOB をすぐに削除する方法の例です。

- WSS_Content という名前のコンテンツ データベース
- SHRPT13 SQL12\SHRPT13 という名前の SQL Server
- SharePoint – 80 という名前の Web アプリケーション

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## SharePoint 用 StorSimple アダプターをアップグレードまたは再インストールする

次の手順を実行することで、SharePoint サーバーをアップグレードしてから SharePoint 用 StorSimple アダプターを再インストールするか、または既存の SharePoint サーバー ファームで単にアダプターのアップグレードまたは再インストールを行います。 

>[AZURE.IMPORTANT] SharePoint ソフトウェアやアップグレードをアップグレードするか、SharePoint 用 StorSimple アダプターを再インストールしようとする前に、次の情報を確認します。
>
>- 以前に RBS 経由で外部ストレージに移動されたファイルは、再インストールが終了し、RBS 機能を再び有効にするまでは使用できません。 ユーザーへの影響を限定するために、計画されたメンテナンス期間にアップグレードまたは再インストールを実行してください。
>
>- アップグレード/再インストールに必要な時間は、SharePoint サーバー ファームでの SharePoint データベースの合計数によって異なります。
>
>- アップグレード/再インストールが完了したら、コンテンツ データベースに対して RBS を有効にする必要があります。 参照してください [RBS の構成](#configure-rbs) の詳細。
>
>- 非常に大量のデータベース (200 を超える) 場合は、SharePoint ファームに対して RBS を構成しているかどうか、 **SharePoint サーバーの全体管理** ページには、タイムアウトが場合もあります。 その場合は、ページを更新します。 ページを更新しても構成プロセスに影響はありません。

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
## SharePoint 用 StorSimple アダプターの削除

次の手順では、BLOB を SQL Server のコンテンツ データベースに戻した後、SharePoint 用 StorSimple アダプター をアンインストールする方法について説明します。 

>[AZURE.IMPORTANT] アダプター ソフトウェアをアンインストールする前にコンテンツ データベースに戻す、Blob を移動する必要があります。 

### 開始する前に 

データを SQL Server コンテンツ データベースに戻してアダプター削除プロセスを開始する前に、以下の情報を収集してください。

- RBS が有効になっているすべてのデータベースの名前
- 構成されている BLOB ストアの UNC パス

### BLOB をコンテンツ データベースに戻す

SharePoint 用 StorSimple アダプター ソフトウェアをアンインストールする前に、外部化されていたすべての BLOB を、再度 SQL Server コンテンツ データベースに移行する必要があります。 すべての BLOB をコンテンツ データベースに戻す前に SharePoint 用 StorSimple アダプターをアンインストールしようとすると、次の警告メッセージが表示されます。

![警告メッセージ](./media/storsimple-adapter-for-sharepoint/sasp1.png)
 
####BLOB をコンテンツ データベースに戻すには 

1. 外部化された各オブジェクトをダウンロードします。

2. 開いている、 **SharePoint サーバーの全体管理** ] ページを見つけて **システム設定**します。 

3.  **Azure StorSimple**, 、クリックして **StorSimple アダプターの構成**します。

4.  **StorSimple アダプターの構成** ] ページで、をクリックして、 **を無効にする** 各外部の BLOB ストレージから削除するコンテンツ データベースの下のボタンをクリックします。 

5. SharePoint からオブジェクトを削除し、再度アップロードします。

または、SharePoint に用意されている Microsoft ` RBS Migrate()` PowerShell コマンドレットを使用することもできます。 詳細については、次を参照してください。 [内または RBS 外にコンテンツを移行](https://technet.microsoft.com/library/ff628255.aspx)します。

コンテンツ データベースに戻す、Blob を移動した後は、次の手順に進みます。 [、アダプターをアンインストールして](#uninstall-the-adapter)します。

### アダプターのアンインストール

BLOB を SQL Server コンテンツ データベースに戻したら、次のいずれかのオプションを使用して、SharePoint 用 StorSimple アダプターをアンインストールします。

#### インストール プログラムを使用してアダプターをアンインストールするには 

1. 管理者特権を持つアカウントを使用して、Web フロント エンド (WFE) サーバーにログオンします。
2. SharePoint 用 StorSimple アダプター インストーラーをダブルクリックします。 セットアップ ウィザードが起動します。

    ![セットアップ ウィザード](./media/storsimple-adapter-for-sharepoint/sasp2.png)

3. クリックして **次**します。 次のページが表示されます。

    ![セットアップ ウィザードの削除ページ](./media/storsimple-adapter-for-sharepoint/sasp3.png)

4. クリックして **削除** 削除プロセスを選択します。 次のページが表示されます。

    ![セットアップ ウィザードの確認ページ](./media/storsimple-adapter-for-sharepoint/sasp4.png)

5. クリックして **削除** 削除を確定します。 次の進行状況ページが表示されます。

    ![セットアップ ウィザードの進行状況ページ](./media/storsimple-adapter-for-sharepoint/sasp5.png)

6. 削除が完了すると、完了ページが表示されます。 をクリックして **完了** セットアップ ウィザードを終了します。

#### コントロール パネルを使用してアダプターをアンインストールするには 

1. コントロール パネルを開き、クリックして **プログラムと機能**です。

2. 選択 **SharePoint 用 StorSimple アダプター**, 、クリックして **アンインストール**します。 

## 次のステップ

[StorSimple について](storsimple-overview.md)します。

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx

