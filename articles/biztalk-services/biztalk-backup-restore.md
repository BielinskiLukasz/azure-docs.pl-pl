<properties 
    pageTitle="BizTalk Services でのバックアップの作成と復元 | Microsoft Azure" 
    description="BizTalk Services には、バックアップと復元の機能が備わっています。 バックアップを作成して復元する方法を説明すると共に、何がバックアップされるのかを確認します。 MABS、WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="dwrede" 
    editor="cgronlun"/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/08/2015" 
    ms.author="mandia"/>


# BizTalk サービス: バックアップと復元に関するページ

Azure BizTalk サービスには、バックアップ機能と復元機能が備わっています。 このトピックでは、Azure クラシック ポータルを使用して BizTalk サービスをバックアップおよび復元する方法について説明しています。

BizTalk サービスを使用してバックアップすることも、 [BizTalk サービスの REST API](http://go.microsoft.com/fwlink/p/?LinkID=325584)します。 

> [AZURE.NOTE] ハイブリッド接続は、エディションにかかわらず、をバックアップされません。 ハイブリッド接続を再作成する必要があります。

## 開始する前に

- バックアップと復元は、どのエディションでもできるとは限りません。 参照してください [BizTalk サービス: エディションのチャート](biztalk-editions-feature-chart.md)します。

- Azure クラシック ポータルを使用して、オンデマンド バックアップを作成することも、スケジュールされたバックアップを作成することもできます。 

- バックアップ コンテンツを同じ BizTalk サービスに復元することも、新しい BizTalk サービスに復元することもできます。 同じ名前を使用して BizTalk サービスを復元するには、既存の BizTalk サービスを削除する必要があります。さらにその名前が使用可能である必要があります。 BizTalk サービスを削除した後、同じ名前が使用可能になるまで長い時間がかかる場合があります。 同じ名前が使用可能になるまで待つことができない場合は、新しい BizTalk サービスに復元します。

- BizTalk サービスの復元先は、同じエディション以上となります。 バックアップを実行したときよりも下位のエディションへの BizTalk サービスの復元はサポートされていません。

    たとえば、Basic Edition を使用したバックアップは Premium Edition に復元できます。 Premium Edition を使用したバックアップを Standard Edition に復元することはできません。

- コントロール番号の連続性を維持するために、EDI コントロール番号がバックアップされます。 前回のバックアップ後にメッセージが処理された場合、このバックアップ コンテンツを復元するとコントロール番号が重複する可能性があります。

- バッチにアクティブなメッセージがある場合、バッチを処理して **する前に** 、バックアップを実行します。 バックアップを作成するときに (オンデマンドかスケジュールされたものかにかかわらず)、バッチの中のメッセージが保存されることはありません。 

    **アクティブなメッセージがバッチになっている状態で、それらに対応するバックアップを取得しようとしても、それらのメッセージはバックアップされないため、失われます。**

- オプション: BizTalk サービス ポータルで、管理操作をすべて停止します。


## バックアップの作成

バックアップはいつでも取得でき、完全にユーザーによって制御されます。 このセクションでは、Azure クラシック ポータルを使用してバックアップを作成する手順を次の各項に分けて説明します。

[オンデマンド バックアップ](#backupnow)

[バックアップのスケジュール](#backupschedule)

#### <a name="backupnow"></a>オンデマンド バックアップ
1. Azure クラシック ポータルで [ **BizTalk サービス**, 、BizTalk サービスを選択してバックアップします。
2.  **ダッシュ ボード** ] タブで [ **バックアップ** ページの下部にあります。
3. バックアップ名を入力します。 たとえば、入力 *myBizTalkService*BU*日付*します。
4. BLOB ストレージ アカウントを選択し、チェックマークを選択してバックアップを開始します。

バックアップが完了すると、入力したバックアップ名が付けられたコンテナーがストレージ アカウントの下に作成されます。 このコンテナーには、BizTalk サービスのバックアップ構成が含まれています。

#### <a name="backupschedule"></a>バックアップのスケジュール

1. Azure クラシック ポータルで [ **BizTalk サービス**, 、バックアップをスケジュールし、選択する BizTalk サービス名を選択、 **構成** ] タブをクリックします。
2. 設定、 **状態をバックアップ** に **自動**です。 
3. 選択、 **ストレージ アカウント** バックアップを保存するには入力、 **頻度** 、バックアップを作成して、バックアップを保持する期間 (**保持日数**)。

    ![][AutomaticBU]

    **メモ**   
    -  **保持日数**, 、保有期間のバックアップ頻度よりも大きくする必要があります。
    - 選択 **常に少なくとも 1 つのバックアップを保持する**, 保有期間が過ぎている場合でも、します。
    

4. 選択 **保存**します。


スケジュールされたバックアップ ジョブを実行すると、入力したストレージ アカウント内に、(バックアップ データを格納するための) コンテナーが作成されます。 コンテナーの名前は *BizTalk サービス名-日付-時刻*します。 

BizTalk サービス ダッシュ ボードが表示されている場合、 **失敗** ステータス。

![前回のスケジュールされたバックアップの状態][BackupStatus] 

このリンクをクリックすると、トラブルシューティングに役立つ管理サービス操作ログのページが開きます。 参照してください [BizTalk サービス: 操作ログを使用したトラブルシューティング](http://go.microsoft.com/fwlink/p/?LinkId=391211)します。

## 復元

バックアップを復元するには、Azure クラシック ポータルまたはから、 [BizTalk サービスの REST API](http://go.microsoft.com/fwlink/p/?LinkID=325582)します。 このセクションでは、クラシック ポータルを使用して復元する手順を説明します。

#### バックアップを復元する前に

- BizTalk サービスを復元するときに、トラッキング、アーカイブ、および監視のための新しい保存場所を入力できます。

- 同じ EDI ランタイム データが復元されます。 EDI ランタイム バックアップでは、コントロール番号が保存されます。 復元されるコントロール番号は、バックアップの時点から順番に付けられます。 前回のバックアップ後にメッセージが処理された場合、このバックアップ コンテンツを復元するとコントロール番号が重複する可能性があります。

#### バックアップの復元

1. Azure クラシック ポータルで [ **新規** > **App Services** > **BizTalk サービス** > **復元**:

    ![バックアップの復元][Restore]

2.  **バックアップ URL**, フォルダー アイコンを選択して、BizTalk サービス構成バックアップが保存されている Azure ストレージ アカウントを展開します。 コンテナーを展開し、右のウィンドウで対応するバックアップの .txt ファイルを選択します。 
<br/><br/>
選択 **開く**します。

3.  **BizTalk サービスの復元** ページで、入力、 **BizTalk サービス名** ことを確認し、 **ドメイン URL**, 、**Edition**, と **領域** 、復元された BizTalk サービスのです。 **新しい SQL データベース インスタンスを作成する** 追跡データベースの。

    ![][RestoreBizTalkService]

    次へ進む矢印を選択します。

4.  SQL データベースの名前を確認し、SQL データベースの作成場所となる物理サーバーを入力し、そのサーバーのユーザー名とパスワードを入力します。


    If you want to configure the SQL database edition, size, and other properties, select  **Configure Advanced Database Settings**. 

    Select the next arrow.

5. 新しいストレージ アカウントを作成するか、BizTalk サービス用の既存のストレージ アカウントを入力します。

7. チェックマークを選択して復元を開始します。

復元が正常に完了すると、新しい BizTalk サービスが Azure クラシック ポータルの [BizTalk サービス] ページに "中断" という状態で表示されます。



### <a name="postrestore"></a>バックアップの復元後

BizTalk サービスは常に復元、 **保留** 状態です。 この状態のときは、新しい環境を機能させる前に次のような構成変更を行うことができます。

- Azure BizTalk Services SDK を使用して BizTalk サービス アプリケーションを作成した場合は、そのアプリケーション内の Access Control (ACS) 資格情報が復元後の環境で動作するように更新することが必要になる可能性があります。

- 既存の BizTalk サービス環境の複製を目的として BizTalk サービスを復元します。 これに該当するときに、元の BizTalk Services ポータルでソース FTP フォルダーを使用するように契約が構成されている場合は、新たに復元された環境で別のソース FTP フォルダーを使用するように契約を更新することが必要になる可能性があります。 更新しなかった場合は、2 つの異なる契約が同じメッセージを取り出そうとする可能性があります。

- 復元操作の結果として複数の BizTalk サービス環境が存在することになった場合は、Visual Studio アプリケーション、PowerShell コマンドレット、REST API、取引先管理オブジェクト モデル (TPM OM) API でのターゲットとして必ず正しい環境を指定してください。

- 新しく復元した BizTalk サービス環境で自動バックアップを構成することをお勧めします。

Azure クラシック ポータルで BizTalk サービスを起動する、復元された BizTalk サービスを **再開** タスク バーにします。 



## バックアップ対象

バックアップが作成されるとき、次の項目がバックアップされます。

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>バックアップされる項目</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk サービス ポータル</strong></td>
</tr> 
<tr>
<td>構成とランタイム</td> 
<td>
<ul>
<li>パートナーとプロファイルの詳細</li>
<li>パートナー契約</li>
<li>デプロイされているカスタム アセンブリ</li>
<li>デプロイされているブリッジ</li>
<li>証明書</li>
<li>デプロイされている変換</li>
<li>パイプライン</li>
<li>BizTalk Services ポータルで作成および保存されたテンプレート</li>
<li>X12 ST01 および GS01 マッピング</li>
<li>コントロール番号 (EDI)</li>
<li>AS2 メッセージ MIC 値</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Azure BizTalk サービス</strong></td>
</tr> 
<tr>
<td>SSL 証明書</td> 
<td>
<ul>
<li>SSL 証明書データ</li>
<li>SSL 証明書のパスワード</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk サービスの設定</td> 
<td>
<ul>
<li>スケール ユニット数</li>
<li>エディション</li>
<li>製品バージョン</li>
<li>リージョン/データセンター</li>
<li>Access Control Service (ACS) の名前空間とキー</li>
<li>トラッキング データベースの接続文字列</li>
<li>アーカイブ ストレージ アカウントの接続文字列</li>
<li>監視ストレージ アカウントの接続文字列</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>その他の項目</strong></td>
</tr> 
<tr>
<td>トラッキング データベース</td> 
<td>BizTalk サービスを作成するときに、トラッキング データベースの詳細 (Azure SQL Database サーバーやトラッキング データベース名など) が入力されます。 トラッキング データベースは自動的にはバックアップされません。
<br/><br/>
<strong>重要</strong><br/>
トラッキング データベースが削除されてデータベースの復旧が必要である場合は、以前のバックアップが存在している必要があります。 バックアップが存在しない場合、トラッキング データベースとそのデータは復旧できません。 このような状況では、同じデータベース名の新しいトラッキング データベースを作成します。 geo レプリケーションが推奨されます。</td>
</tr> 
</table>

## 次へ

Azure クラシック ポータルで Azure BizTalk サービスを作成するには [BizTalk サービス: プロビジョニングを使用して Azure 旧ポータル](http://go.microsoft.com/fwlink/p/?LinkID=302280)します。 アプリケーションの作成を開始するには [Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=235197)します。

## 関連項目
- [BizTalk サービスのバックアップに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [バックアップからの BizTalk サービスの復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャートに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk サービス: Azure クラシック ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk サービス: プロビジョニングの状態のチャートに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk Services: 調整](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk サービス: 発行者名および発行者キーに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 


