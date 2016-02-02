
### SharePoint 2010 を SharePoint 2013 にアップグレードして SharePoint 用 StorSimple アダプターをインストールする

>[AZURE.IMPORTANT] 以前に RBS 経由で外部ストレージに移動されたファイルは、アップグレードが完了し、RBS 機能を再び有効にするまでは使用できません。 ユーザーへの影響を限定するために、計画されたメンテナンス期間にアップグレードまたは再インストールを実行してください。

#### SharePoint 2010 を SharePoint 2013 にアップグレードしてアダプターをインストールするには

1. SharePoint 2010 ファームで、外部化された BLOB の BLOB ストア パスと RBS が有効にされているコンテンツ データベースをメモします。

2. 新しい SharePoint 2013 ファームをインストールして構成します。

3. データベース、アプリケーション、およびサイト コレクションを SharePoint 2010 ファームから新しい SharePoint 2013 ファームに移動します。 手順についてを参照してください [SharePoint 2013 へのアップグレード プロセスの概要](https://technet.microsoft.com/library/cc262483.aspx)します。

4. 新しいファームに SharePoint 用 StorSimple アダプターをインストールします。 移動して [SharePoint 用 StorSimple アダプターをインストール](#install-the-storsimple-adapter-for-sharepoint) プロシージャです。

5. 手順 1. でメモした情報を使用して、同じコンテンツ データベース セットの RBS を有効にし、SharePoint 2010 のインストール時に使用したのと同じ BLOB ストア パスを指定します。 移動して [RBS の構成](#configure-rbs) プロシージャです。 この手順を完了すると、あらかじめ外部化されているファイルが新しいファームからアクセス可能になります。

### SharePoint 用 StorSimple アダプターをアップグレードする

>[AZURE.IMPORTANT] このアップグレードは、以下の理由で、計画的なメンテナンス期間中に実行されるようスケジュールする必要があります。
>
>-以前外部化されたコンテンツはいない限り使用できません、アダプターが再インストールします。
>
>- が、新しいバージョンをインストールする前に、SharePoint 用 StorSimple アダプターの以前のバージョンをアンインストールした後に、サイトにアップロードされたコンテンツは、コンテンツ データベースに格納されます。 そのコンテンツは、新しいアダプターをインストールした後、StorSimple デバイスに移動する必要があります。 Microsoft を使用する` RBS Migrate()` コンテンツを移行するように SharePoint に含まれている PowerShell コマンドレット。 詳細については、次を参照してください。 [内または RBS 外にコンテンツを移行](https://technet.microsoft.com/library/ff628255.aspx)します。 


#### SharePoint 用 StorSimple アダプターをアップグレードするには

1. SharePoint 用 StorSimple アダプターの以前のバージョンをアンインストールします。
    >[AZURE.NOTE] これにより、コンテンツ データベースの RBS が自動的に無効になります。 ただし、既存の BLOB は StorSimple デバイスに残ります。 RBS は無効になり、BLOB はコンテンツ データベースに移行されていないため、これらの BLOB に対する要求はいずれも失敗します。 

2. 新しい SharePoint 用 StorSimple アダプターをインストールします。 新しいアダプターは、以前 RBS に対して有効または無効にされたコンテンツ データベースを自動的に認識し、以前の設定を使用します。





