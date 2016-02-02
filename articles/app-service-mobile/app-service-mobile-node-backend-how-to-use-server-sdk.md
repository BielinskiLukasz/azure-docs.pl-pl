<properties
    pageTitle="Mobile Apps 用 Node.js バックエンド サーバー SDK を操作する方法 | Azure App Service"
    description="Azure App Service Mobile Apps 用の Node.js バックエンド サーバー SDK を操作する方法について説明します。"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="node"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="adrianhall"/>


# Azure Mobile Apps Node.js SDK の使用方法

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

この記事では、Azure App Service Mobile Apps で Node.js バックエンドを使用する方法についての詳細な情報と例を提供します。
> [AZURE.NOTE] この SDK はプレビュー段階です。 そのため、運用環境でこの SDK を使用することは推奨されません。 このドキュメントの例では、[azure mobile apps] の v2.0.0 beta1 を使用します。

## <a name="Introduction"></a>概要

Azure App Service Mobile Apps は、Web アプリケーションにモバイルに最適化されたデータ アクセス Web API を追加する機能を提供します。 Azure のアプリケーション
ASP.NET および Node.js web アプリケーション サービス Mobile Apps SDK を提供しています。 この SDK を使用すると、次の処理を実行できます。

- データ アクセスのためのテーブル操作 (読み取り、挿入、更新、削除)
- カスタム API 操作

両方の操作は、認証のソーシャル id を含む、Azure App Service で許可されているすべての id プロバイダーを通じて提供します。
Facebook、Twitter、Google、Microsoft と Azure Active Directory など、エンタープライズ id 用のプロバイダー。

[サンプル ディレクトリに GitHub] の各ユース ケースのサンプルを検索できます。

### <a name="howto-cmdline-basicapp"></a>方法: コマンドラインを使用して基本的な Node.js バックエンドを作成します。

Azure App Service Mobile Apps の Node.js バックエンドはすべて ExpressJS アプリケーションとして開始されます。 ExpressJS は、最も人気のある web サービス フレームワークです。
Node.js に対して使用できます。 次のように、basic [Express] アプリケーションを作成できます。

1. コマンドまたは PowerShell ウィンドウで、プロジェクト用の新しいディレクトリを作成します。

        mkdir basicapp

2. npm init を実行して、パッケージの構造を初期化します。

        cd basicapp
        npm init

    npm init コマンドでは、プロジェクトを初期化するための一連の質問が示されます。 以下の出力例を参照してください。

    ![npm init の出力][0]

3. npm リポジトリから express および azure-mobile-apps ライブラリをインストールします。

        npm install --save express azure-mobile-apps

4. app.js ファイルを作成して、基本的なモバイル サーバーを実装します。

     var express = require('express'),
         azureMobileApps = require('azure-mobile-apps');
    
     var app = express(),
         mobile = azureMobileApps();
    
     // Define a TodoItem table
     mobile.tables.add('TodoItem');
    
     // Add the mobile API so it is accessible as a Web API
     app.use(mobile);
    
     // Start listening on HTTP
     app.listen(process.env.PORT || 3000);


このアプリケーションは、1 つのエンドポイントを持つ単純なモバイルに最適化された web Api を作成 (`/テーブル/TodoItem`) を提供します。
基になる SQL データへの認証されていないアクセスは、動的スキーマを使用して格納します。 適して次に、
クライアント ライブラリのクイック スタート:

- [クイック スタートの android クライアント]
- [iOS クライアント クイック スタート]
- [Windows ストア クライアントのクイック スタート]
- [クイック スタート Xamarin.iOS クライアント]
- [クイック スタート Xamarin.Android クライアント]
- [Xamarin.Forms のクライアント クイック スタート]


この基本的なサンプル アプリケーションで、[basicapp github] のコードを検索できます。

### <a name="howto-vs2015-basicapp"></a>方法: Visual Studio 2015 でのノードのバックエンドを作成します。

Visual Studio 2015 には、IDE 内で Node.js アプリケーションを開発するための拡張機能が必要です。 開始するには、ダウンロードし、[Node.js ツール 1.1 には、Visual studio] をインストールします。 Node.js Tools for Visual Studio をインストールしたら、Express 4.x アプリケーションを作成します。

1. **[新しいプロジェクト]** ダイアログを開きます (**[ファイル]**、**[新規作成]**、**[プロジェクト...]** の順にクリック)。

2. **[テンプレート]**、**[JavaScript]**、**[Node.js]** の順に展開します。

3. **[Basic Azure Node.js Express 4 Application]** を選択します。

4. プロジェクト名を入力します。 *[OK]* をクリックします。

    ![Visual Studio 2015 の新しいプロジェクト][1]

5. **npm** ノードを右クリックし、**[新しい npm パッケージのインストール]** を選択します。

6. 最初の Node.js アプリケーションの作成時に、npm カタログを更新することが必要な場合があります。 更新が必要な場合は、更新するよう求められるので、**[更新]** をクリックします。

7. 検索ボックスに「_azure-mobile-apps_」と入力します。 **azure-mobile-apps 2.0.0** パッケージをクリックしてから、**[パッケージのインストール]** をクリックします。

    ![新しい npm パッケージのインストール][2]

8. **[閉じる]** をクリックします。

9. _app.js_ ファイルを開き、Azure Mobile Apps SDK のサポートを追加します。 ライブラリの require ステートメントの下の 6 行目に、次のコードを追加します。

     var bodyParser = require('body-parser');
     var azureMobileApps = require('azure-mobile-apps');

 他の app.use ステートメントの後の約 27 行目に、次のコードを追加します。

     app.use('/users', users);
    
     // Azure Mobile Apps Initialization
     var mobile = azureMobileApps();
     mobile.tables.add('TodoItem');
     app.use('mobile');

 ファイルを保存します。

10. (この API は http://localhost:3000 に処理できる)、アプリケーションをローカルで実行するかまたは Azure に発行します。

### <a name="download-quickstart"></a>方法: Node.js バックエンド クイック スタート コードを Git を使用してプロジェクトのダウンロード

ポータルの **[クイック スタート]** ブレードを使用して新しい Node.js モバイル アプリ バックエンドを作成すると、新しい Node.js プロジェクトが作成され、サイトにデプロイされます。 ポータルでは、テーブルと API を追加したり、Node.js バックエンドのコード ファイルを編集したりできます。 また、さまざまなデプロイメント ツールの 1 つを使用してバックエンド プロジェクトをダウンロードすると、テーブルと API を追加または変更した後でプロジェクトを再発行できます。 詳細については、[Azure アプリ サービス展開ガイド] を参照してください。 次の手順では、Git リポジトリを使用して、クイック スタート プロジェクトのコードをダウンロードします。

1. Git をまだインストールしていない場合はインストールします。 Git をインストールするために必要な手順は、オペレーティング システムによって異なります。 参照してください [Git のインストール](http://git-scm.com/book/en/Getting-Started-Installing-Git) オペレーティング システム固有の配布およびインストールのガイダンスを提供します。

2. 手順に従います [web アプリのリポジトリを有効にする](../app-service-web/web-sites-publish-source-control.md#Step4) バックエンド サイトで、デプロイ ユーザー名とパスワードの変更の Git リポジトリを有効にします。

3. モバイル アプリ バックエンドのブレードで、**[Git クローン URL]** の設定をメモしておきます。

4.  実行、 `git クローン` コマンド Git クローン URL が表示され、必要に応じて、次の例のように、パスワードの入力を使用して、Git 対応のコマンド ライン ツールを実行します。

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. ローカル ディレクトリ (上の例では /todolist) を参照し、プロジェクト ファイルがダウンロード済みであることを確認します。 /tables サブフォルダーには、todoitem.json ファイル (テーブルに対するアクセス許可を定義します) と、todoitem.js ファイル (テーブルの CRUD 操作スクリプトを定義します) があります。

6. プロジェクト ファイルに変更を加えた後に、次のコマンドを実行して、変更をサイトに追加、コミット、アップロードします。

        $ git commit -m "updated the table script"
        $ git push origin master

    最初に実行する必要がありますがプロジェクトに新しいファイルを追加する場合、 `git を追加します。` コマンドです。

一連の新しいコミットがサイトにプッシュされるたびに、そのサイトは再発行されます。

### <a name="howto-publish-to-azure"></a>方法: Node.js バックエンドを Azure に発行します。

Microsoft Azure では、Azure サービスに Azure App Service Mobile Apps Node.js バックエンドを発行するための多数のメカニズムが提供されます。 Visual Studio に統合されたデプロイメント ツール、コマンドライン ツールおよびソース管理に基づく継続的なデプロイメント オプションも利用します。 このトピックの詳細については、[Azure アプリ サービス展開ガイド] を参照してください。

Azure App Service には、デプロイ前に確認する必要がある Node.js アプリケーションに関する以下の特定のアドバイスがあります。

- [ノードのバージョンを指定] する方法
- [ノード モジュールを使用] する方法

## <a name="TableOperations"></a>テーブル操作

azure-mobile-apps Node.js Server SDK では、WebAPI として Azure SQL Database に格納されたデータ テーブルを公開するためのメカニズムが提供されます。 以下の 5 つの操作が提供されます。

| 操作| 説明|
| --------- | ----------- |
| GET/tables/_tablename_| テーブルのすべてのレコードを取得します。|
| GET/tables/_tablename_/:id| テーブルの特定のレコードを取得します。|
| POST/tables/_tablename_| テーブルに新しいレコードを作成します。|
| 修正プログラム/tables/_tablename_/:id| テーブルの既存のレコードを更新します。|
| DELETE/tables/_tablename_/:id| テーブルのレコードを削除します。|

この web Api では、[OData] をサポートし、[オフライン データの同期] をサポートするために、テーブル スキーマを拡張します。

### <a name="howto-dynamicschema"></a>方法: 動的スキーマを使用してテーブルを定義します。

テーブルを使用する前に定義する必要があります。 テーブルは、静的スキーマで (開発者がスキーマ内の列を定義する場合)、または動的に (SDK で受信要求に基づいてスキーマを制御する場合) 定義できます。 さらに、開発者は、定義に Javascript コードを追加することで、WebAPI の特定の側面を制御できます。

ベスト プラクティスとして、テーブル ディレクトリ内の Javascript ファイルに各テーブルを定義し、tables.import() メソッドを使用してテーブルをインポートする必要があります。
basic-app を拡張すると、app.js ファイルが次のように調整されます。

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');
    
    var app = express(),
        mobile = azureMobileApps();
    
    // Define the database schema that is exposed
    mobile.tables.import('./tables');
    
    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);
    
        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

次のように ./tables/TodoItem.js にテーブルを定義します。

    var azureMobileApps = require('azure-mobile-apps');
    
    var table = azureMobileApps.table();
    
    // Additional configuration for the table goes here
    
    module.exports = table;

テーブルでは、既定で動的スキーマが使用されます。 動的スキーマをグローバルに無効にするには、Azure ポータル内でアプリ設定の **MS_DynamicSchema** を false に設定します。

[Todo サンプル github] の完全な例が表示されます。

### <a name="howto-staticschema"></a>方法: 静的なスキーマを使用してテーブルを定義します。

WebAPI を使用して公開する列を明示的に定義することができます。 azure-mobile-apps Node.js SDK では、指定した一覧にオフライン データ同期に必要な他の列が自動的に追加されます。 たとえば、クイック スタート クライアント アプリケーションには、text (文字列) と complete (ブール値) という 2 つの列を持つテーブルが必要になります。 これは、テーブル定義 JavaScript ファイル (テーブル ディレクトリにある) に次のように定義できます。

    var azureMobileApps = require('azure-mobile-apps');
    
    var table = azureMobileApps.table();
    
    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    
    // Turn off dynamic schema
    table.dynamicSchema = false;
    
    module.exports = table;

静的にテーブルを定義する場合は、tables.initialize() メソッドを呼び出して起動時にデータベース スキーマを作成する必要もあります。 Tables.initialize() メソッドには、[Promise] が返されます。-これは、web サービスが初期化されているデータベースの前に要求を処理しないことを確認に使用します。

### <a name="howto-sqlexpress-setup"></a>方法: ローカル コンピューターで開発データ ストアとして SQL Express を使用します。

Azure Mobile Apps Node SDK には、データをすぐに使用できる 3 つのオプションが用意されています。

- **メモリ** ドライバーを使用して、非永続ストアの例を提供します。
- **mssql** ドライバーを使用して、開発用の SQL Express データ ストアを提供します。
- **mssql** ドライバーを使用して、実稼働用の Azure SQL Database データ ストアを提供します。

Azure Mobile Apps Node.js SDK を設定し、SQL Express と SQL データベースへの接続を使用して、[mssql Node.js のパッケージ] を使用します。 このパッケージでは、SQL Express インスタンスで TCP 接続を有効にする必要があります。
> [AZURE.TIP] メモリ ドライバーでは、テスト用の完全な機能セットは提供されません。 バックエンドをローカルでテストする場合
SQL Express のデータ ストアと mssql ドライバーの使用はお勧めします。

1. ダウンロードし、[Microsoft SQL Server 2014 Express] をインストールします。 必ず、SQL Server 2014 Express with Tools エディションをインストールしてください。 しない限り、明示的に
64 ビット サポートを必要な 32 ビット バージョンを実行しているときより少ないメモリは消費します。

2. SQL Server 2014 構成マネージャーを実行します。

  1. 左側のツリー メニューにある **[SQL Server ネットワークの構成]** ノードを展開します。
  2. **[SQLEXPRESS のプロトコル]** をクリックします。
  3. **[TCP/IP]** を右クリックし、**[有効化]** を選択します。 ポップアップ ダイアログで **[OK]** をクリックします。
  4. **[TCP/IP]** を右クリックし、**[プロパティ]** を選択します。
  5. **[IP アドレス]** タブをクリックします。
  6. **[IPAll]** ノードを見つけます。 **[TCP ポート]** フィールドに、「**1433**」と入力します。

         ![TCP/IP の SQL Express の構成][3]
  7. **[OK]** をクリックします。 ポップアップ ダイアログで **[OK]** をクリックします。
  8. 左側のツリー メニューにある **[SQL Server のサービス]** をクリックします。
  9. **[SQL Server (SQLEXPRESS)]** を右クリックし、**[再起動]** を選択します。
  10. SQL Server 2014 構成マネージャーを閉じます。

3. SQL Server 2014 Management Studio を実行して、ローカルの SQL Express インスタンスに接続します。

  1. オブジェクト エクスプローラーでインスタンスを右クリックし、**[プロパティ]** を選択します。
  2. **[セキュリティ]** ページを選択します。
  3. **[SQL Server 認証モードと Windows 認証モード]** が選択されていることを確認します。
  4. **[OK]** をクリックします。

        ![SQL Express 認証の構成][4]

  5. オブジェクト エクスプローラーで、**[セキュリティ]**、**[ログイン]** の順に展開します。
  6. **[ログイン]** を右クリックし、**[新しいログイン]** を選択します。
  7. ログイン名を入力します。 **[SQL Server 認証]** を選択します。 パスワードを入力し、**[パスワードの確認入力]** に同じパスワードを入力します。 パスワードは、Windows の複雑さの要件を満たしている必要があります。
  8. **[OK]** をクリックします。

        ![SQL Express に新しいユーザーを追加する][5]

  9. 新しいログインを右クリックし、**[プロパティ]** を選択します。
  10. **[サーバー ロール]** ページを選択します。
  11. **dbcreator** サーバー ロールの横にあるチェック ボックスをオンにします。
  12. **[OK]** をクリックします。
  13. SQL Server 2015 Management Studio を閉じます。

選択したユーザー名とパスワードは必ず記録してください。 特定のデータベース要件に応じて、他のサーバーの役割またはアクセス許可の割り当てが必要になる場合があります。

Node.js アプリケーションは、**SQLCONNSTR_MS_TableConnectionString** 環境変数を読み取って、このデータベースの接続文字列を確認します。 ご使用の環境内にこれを設定することができます。 たとえば、PowerShell を使用して、以下のようにこの環境変数を設定することができます。

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

TCP/IP 接続を介して、データベースにアクセスし、接続用のユーザー名とパスワードを入力する必要があることに注意してください。

### <a name="howto-config-localdev"></a>方法: ローカル開発用プロジェクトを構成します。

Azure Mobile Apps は、ローカル ファイルシステムから _azureMobile.js_ という JavaScript ファイルを読み取ります。 実稼働環境で Azure Mobile Apps SDK を構成する - [Azure ポータル] 内のアプリの設定を使うには、このファイルを使用する必要があります。 _azureMobile.js_ ファイルでは構成オブジェクトをエクスポートする必要があります。 最も一般的な設定は次のとおりです。

- データベース設定
- 診断ログ設定
- 代替 CORS 設定

上記のデータベース設定を実装する _azureMobile.js_ ファイルの例を次に示します。

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

追加することをお勧め _azureMobile.js_ に、 _.gitignore_ ファイル (またはファイルを無視するその他のソース コード管理) からパスワードを防止するには
クラウドに格納されています。 [Azure ポータル] アプリ設定] で、運用環境の設定を必ず設定します。

### <a name="howto-appsettings"><a>モバイル アプリを構成するためのアプリ設定

ほとんどの設定、 _azureMobile.js_ ファイル [Azure ポータル] で、対応するアプリケーション設定があります。 次のリストを使用して、構成、
アプリケーション設定では app:

| アプリ設定| _azureMobile.js_ 設定| 説明| 有効な値|
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS_MobileAppName**| name| アプリの名前| string|
| **MS_MobileLoggingLevel**| logging.level| ログ記録するメッセージの最小ログ レベル| error、warning、info、verbose、debug、silly|
| **MS_DebugMode**| debug| デバッグ モードの有効化または無効化| true、false|
| **MS_TableSchema**| data.schema| SQL テーブルの既定のスキーマ名| string (既定: dbo)|
| **MS_DynamicSchema**| data.dynamicSchema| デバッグ モードの有効化または無効化| true、false|
| **MS_DisableVersionHeader**| version (undefined に設定)| X-ZUMO-Server-Version ヘッダーの無効化| true、false|
| **MS_SkipVersionCheck**| skipversioncheck| クライアント API バージョン チェックの無効化| true、false|

アプリ設定を設定するには:

1. [Azure ポータル] にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、モバイル アプリの名前をクリックします。
3. 既定で [設定] ブレードが開きます。開かない場合は、**[設定]** をクリックします。
4. [全般] メニューで、**[アプリケーション設定]** をクリックします。
5. [アプリ設定] セクションまでスクロールします。
6. アプリ設定が既に存在する場合は、アプリ設定の値をクリックして値を編集します。
7. アプリ設定が存在しない場合は、[キー] ボックスにアプリ設定を入力し、[値] ボックスに値を入力します。
8. 完了したら、**[保存]** をクリックします。

ほとんどのアプリ設定を変更した場合、サービスの再起動が必要になります。

### <a name="howto-use-sqlazure"></a>方法: 実稼働データ ストアとして SQL データベースを使用

Azure SQL Database をデータ ストアとして使用する方法は、Azure App Service アプリケーションのすべての種類で同じです。 モバイル アプリ バックエンドをまだ作成していない場合は、次の手順に従って新しいものを作成します。

1. [Azure ポータル] にログインします。

2. ウィンドウの左上で、**[+ 新規]**、**[Web + モバイル]**、**[モバイル アプリ]** の順にクリックし、モバイル アプリ バックエンドの名前を指定します。

3. **[リソース グループ]** ボックスで、アプリと同じ名前を入力します。

4. 既定の App Service プランが選択されます。 App Service プランを変更するには、[App Service プラン]、**[+ 新規作成]** の順にクリックします。 新しい App Service プランの名前を指定し、適切な場所を選択します。 [価格レベル] をクリックし、サービスに適切な価格レベルを選択します。 **[すべて表示]** を選択して、**Free** や **Shared** などの価格オプションをさらに表示します。 価格レベルを選択したら、**[選択]** をクリックします。 **[App Service プラン]** ブレードに戻り、**[OK]** をクリックします。

5. **[作成]** をクリックします。 これにより、モバイル アプリ バックエンドが作成され、後でサーバー プロジェクトをデプロイすることができます。 モバイル アプリ バックエンドのプロビジョニングには数分かかる場合があります。 モバイル アプリ バックエンドのプロビジョニングが完了すると、ポータルでモバイル アプリ バックエンドの **[設定]** ブレードが開きます。

モバイル アプリ バックエンドが作成されたら、既存の SQL データベースをモバイル アプリ バックエンドに接続するか、新しい SQL データベースを作成するかを選択できます。 このセクションでは、新しい SQL データベースを作成します。
> [AZURE.NOTE] 新しいモバイル アプリ バックエンドと同じ場所に、既にデータベースがある場合は、**[既存のデータベースを使用する]** を選ぶと、そのデータベースを選択できます。 別の場所にあるデータベースを使用することは、帯域幅コストと待機時間が増加するため、お勧めしません。

6. 新しいモバイル アプリ バックエンドで、**[設定]**、**[モバイル アプリ]**、**[データ]**、**[+ 追加]** の順にクリックします。

7. **[データ接続の追加]** ブレードで、**[SQL Database - 必要な設定の構成]**、**[新しいデータベースの作成]** の順にクリックします。 **[名前]** フィールドに新しいデータベースの名前を入力します。

8. **[サーバー]** をクリックします。 **[新しいサーバー]** ブレードで、**[サーバー名]** フィールドに一意のサーバー名を入力し、**[サーバー管理ログイン]** と **[パスワード]** に適切な内容を指定します。 **[Azure サービスにサーバーへのアクセスを許可する]** チェック ボックスがオンになっていることを確認します。 **[OK]** をクリックします。

    ![Azure SQL Database の作成][6]

9. **[新しいデータベース]** ブレードで、**[OK]** をクリックします。

10. **[データ接続の追加]** ブレードに戻り、**[接続文字列]** を選択して、データベースの作成時に指定したログイン名とパスワードを入力します。 既存のデータベースを使用する場合は、そのデータベースのログイン資格情報を入力します。 入力したら、**[OK]** をクリックします。

11. もう一度 **[データ接続の追加]** ブレードに戻り、**[OK]** をクリックしてデータベースを作成します。



データベースの作成には数分かかる場合があります。 **[通知]** 領域を使用して、デプロイの進行状況を監視します。 データベースのデプロイが正常に完了するまでは、先に進まないでください。 正常にデプロイされると、モバイル バックエンドの [アプリ設定] で、SQL Database インスタンスの接続文字列が作成されます。 このアプリ設定を確認するには、**[設定]**、**[アプリケーション設定]**、**[接続文字列]** を順に選択します。

### <a name="howto-tables-auth"></a>方法: テーブルへのアクセスに対して認証を要求

テーブルのエンドポイントでアプリ サービスの認証を使用する場合は、まず [Azure ポータル] でアプリ サービスの認証を構成する必要があります。 次に
詳細については、Azure App Service で認証を構成する方法を使用する id プロバイダーの構成ガイドを確認します。

- [Azure Active Directory 認証を構成する方法]
- [Facebook 認証を構成する方法]
- [Google 認証を構成する方法]
- [Microsoft 認証を構成する方法]
- [Twitter 認証を構成する方法]

各テーブルには、テーブルへのアクセスを制御するために使用できる access プロパティがあります。 次のサンプルでは、認証を必要とする静的に定義されたテーブルを示します。

    var azureMobileApps = require('azure-mobile-apps');
    
    var table = azureMobileApps.table();
    
    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    
    // Turn off dynamic schema
    table.dynamicSchema = false;
    
    // Require authentication to access the table
    table.access = 'authenticated';
    
    module.exports = table;

access プロパティには、次の 3 つの値を使用できます。

  - *anonymous* は、認証なしでデータを読み取る許可をクライアント アプリケーションに与えることを示します。
  - *authenticated* は、クライアント アプリケーションが要求で有効な認証トークンを送信する必要があることを示します。
  - *disabled* は、このテーブルが現在無効になっていることを示します。

access プロパティが定義されていない場合は、非認証アクセスが許可されます。

### <a name="howto-tables-disabled"></a>方法: 特定のテーブル操作へのアクセスを無効にします。

テーブルでの表示だけでなく、個々の操作を制御するために access プロパティを使用できます。 操作には以下の 4 つがあります。

  - *read* は、テーブルに対する RESTful GET 操作です。
  - *insert* は、テーブルに対する RESTful POST 操作です。
  - *update* は、テーブルに対する RESTful PATCH 操作です。
  - *delete* は、テーブルに対する RESTful DELETE 操作です。

たとえば、読み取り専用の非認証テーブルを指定するとします。 これは、次のようなテーブル定義で指定することができます。

    var azureMobileApps = require('azure-mobile-apps');
    
    var table = azureMobileApps.table();
    
    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';
    
    module.exports = table;

### <a name="howto-tables-query"></a>方法: テーブル操作で使用されるクエリを調整します。

テーブル操作の一般的な要件は、データの制限付きビューを提供することです。 たとえば、あるテーブルが提供します。
ユーザーが読み取りのみ、または独自のレコードを更新できるように、認証されたユーザー ID でタグ付けします。 次のテーブルの定義
この機能を提供します。

    var azureMobileApps = require('azure-mobile-apps');
    
    var table = azureMobileApps.table();
    
    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    
    // Require authentication for this table
    table.access = 'authenticated';
    
    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });
    
    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    }
    
    module.exports = table;

通常はクエリを実行する操作には、where 句で調整できる query プロパティがあります。 クエリのプロパティは
OData のクエリをデータ バックエンドが処理できるものに変換するために使用する [QueryJS] オブジェクトを返します。 単純な等値の場合
(前述したよう)、マップを使用していることができます。 特定の SQL 句の追加も比較的容易です。

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>方法: テーブルの論理削除を構成します。

論理削除では実際にレコードは削除されません。 代わりに、削除列を true に設定して、データベース内のレコードを削除済みとしてマークします。 Mobile Client SDK で IncludeDeleted() が使用されない限り、Azure Mobile Apps SDK によって結果から論理削除レコードが自動的に削除されます。 論理削除のテーブルを構成するには、テーブル定義ファイルで softDelete プロパティを設定します。 例を以下に示します。

    var azureMobileApps = require('azure-mobile-apps');
    
    var table = azureMobileApps.table();
    
    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    
    // Turn off dynamic schema
    table.dynamicSchema = false;
    
    // Turn on Soft Delete
    table.softDelete = true;
    
    // Require authentication to access the table
    table.access = 'authenticated';
    
    module.exports = table;

クライアント アプリケーションから、WebJob またはカスタム メカニズムを使用して、レコードを消去するためのメカニズムを確立する必要があります。

### <a name="howto-tables-seeding"></a>方法: データを使用してデータベースをシード

新しいアプリケーションを作成する場合、データでのテーブルのシード処理が必要になることがあります。 これは、として、テーブル定義の JavaScript ファイル内で行うことができます。
ページが開きます。

    var azureMobileApps = require('azure-mobile-apps');
    
    var table = azureMobileApps.table();
    
    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];
    
    // Turn off dynamic schema
    table.dynamicSchema = false;
    
    // Require authentication to access the table
    table.access = 'authenticated';
    
    module.exports = table;

データのシード処理は、テーブルが Azure Mobile Apps SDK で作成されている場合にのみ行われることに注意してください。 場合は既にテーブル
存在する、データベース内でデータに挿入されていないテーブルです。 動的スキーマが有効な場合、スキーマから推論されます。
シード データです。

initialize() メソッドを明示的に呼び出して、サービスの実行開始時にテーブルを作成することをお勧めします。

## <a name="CustomAPI"></a>カスタム Api

/tables エンドポイント経由のデータ アクセス API に加え、Azure Mobile Apps ではカスタム API も提供できます。 カスタム Api が定義されています。
テーブルの定義と同様の方法とまったく同じ認証などの機能にアクセスできます。

アプリ サービスの認証、カスタム API を使用する場合は、まず [Azure ポータル] でアプリ サービスの認証を構成する必要があります。 次に
詳細については、Azure App Service で認証を構成する方法を使用する id プロバイダーの構成ガイドを確認します。

- [Azure Active Directory 認証を構成する方法]
- [Facebook 認証を構成する方法]
- [Google 認証を構成する方法]
- [Microsoft 認証を構成する方法]
- [Twitter 認証を構成する方法]

### <a name="howto-customapi-basic"></a>方法: 単純なカスタム API を定義します。

カスタム API は、テーブル API とほぼ同じ方法で定義されます。

1. **api** ディレクトリを作成します。
2. **api** ディレクトリに API 定義 JavaScript ファイルを作成します。
3. import メソッドを使用して、**api** ディレクトリをインポートします。

前に使用した basic-app サンプルに基づくプロトタイプの api 定義を以下に示します。

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');
    
    var app = express(),
        mobile = azureMobileApps();
    
    // Import the Custom API
    mobile.api.import('./api');
    
    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);
    
    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

ここでは、_Date.now()_ メソッドを使用してサーバーの日付を返す単純な API を使用します。 api/date.js ファイルを以下に示します。

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    
    module.exports = api;

各パラメーターは、標準的な RESTful 動詞 (GET、POST、PATCH または DELETE) のいずれかです。 メソッドは、必要な出力を送信する標準的な [ExpressJS ミドルウェア] 機能です。

### <a name="howto-customapi-auth"></a>方法: カスタム API へのアクセスに対して認証を要求

Azure Mobile Apps SDK では、テーブル エンドポイントとカスタム API の両方に対して同じ方法で認証を実装します。 前のセクションで開発した API に認証を追加するには、**access** プロパティを追加します。

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';
    
    module.exports = api;

以下のように、特定の操作で認証を指定することもできます。

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';
    
    module.exports = api;

認証を必要とするカスタム API には、テーブル エンドポイントで使用されるものと同じトークンを使用する必要があります。

## <a name="Debugging"></a>デバッグとトラブルシューティング

Azure App Service では、Node.js アプリケーションに関するいくつかのデバッグとトラブルシューティングの手法が提供されます。
これらの手法をすべて使用できます。

- [Azure App Service の監視]
- [Azure App Service での診断ログを有効にする]
- [トラブルシューティングを行う Visual Studio での Azure の App Service]

### <a name="howto-diagnostic-logs"></a>方法: Azure Mobile Apps の診断ログに書き込む

Node.js アプリケーションは、広範囲の診断ログ ツールにアクセスできます。 内部的には、Azure Mobile Apps Node.js SDK は、診断ログを [ウィンストン] を使用します。 これは、自動的に有効になっているデバッグ モードを有効にするか、設定、 **MS_DebugMode** アプリ設定を [Azure ポータル] で true にします。 生成されたログは、[Azure ポータル] [診断ログに表示されます。

## <a name="in-portal-editing"></a>ポータルでのコード編集機能

Azure ポータルに用意されている特殊なツールを使用すると、コード プロジェクトをダウンロードしなくても、Node.js バックエンド プロジェクトを簡単に操作できます。 ポータルの [テーブルの簡単操作] と [API の簡単操作] を使用すると、ポータル内でテーブルとカスタム API をすぐに作成して操作できます。 Visual Studio Team Services の "Monaco" エディターを使用して、ポータル内ですぐにテーブル操作と API スクリプトを編集することもできます。

### <a name="work-easy-tables"></a>方法: Azure ポータルで簡単にテーブルを操作します。

バックエンド サイトの設定で **[テーブルの簡単操作]** をクリックすると、新しいテーブルを追加したり、既存のテーブルを変更または削除したりできます。 さらに、テーブル内のデータを表示することもできます。

![Work with Easy Tables](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

テーブル用のコマンド バーには、次のコマンドが用意されています。

+ **[アクセス許可の変更]** - テーブルに対する読み取り、挿入、更新、削除操作のアクセス許可を変更します。 匿名アクセスを許可するオプション、認証を要求するオプション、操作に対するすべてのアクセスを無効にするオプションがあります。 これにより、table.json プロジェクト コード ファイルは変更されます。
+ **[スクリプトの編集]** - テーブルのスクリプト ファイルが Visual Studio Team Services エディターで開かれます。
+ **[スキーマの管理]** - 列の追加または削除やテーブルのインデックスの変更を実行します。
+ **[テーブルのクリア]** - 既存のテーブルですべてのデータ行が削除されるように切り捨てます。ただし、スキーマは変更されません。
+ **[行の削除]** - 個々のデータ行を削除します。
+ **[ストリーミング ログの表示]** - サイトのストリーミング ログ サービスに接続します。

### <a name="work-easy-apis"></a>方法: Azure ポータルの簡単な Api の使用

バックエンド サイトの設定で **[API の簡単操作]** をクリックすると、新しいカスタム API エンドポイントを追加したり、既存の API エンドポイントを変更または削除したりできます。

![Work with Easy APIs](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

ポータルでは、特定の HTTP アクションのアクセス許可を変更できます。また、Visual Studio Team Services エディターで API スクリプト ファイルをしたり、ストリーミング ログを表示したりできます。

### <a name="online-editor"></a>方法: Visual Studio チームのサービスのコードを編集

Azure ポータルでは、ローカル コンピューターにプロジェクトをダウンロードする必要なく、Visual Studio Team Services を使用して Node.js バックエンド スクリプト ファイルを編集できます。 オンライン エディターでスクリプト ファイルを編集するには、次の手順に従います。

1. モバイル アプリ バックエンドのブレードで、**[すべての設定]**、**[テーブルの簡単操作]** または **[API の簡単操作]** の順にクリックし、テーブルまたは API をクリックしてから、**[スクリプトの編集]** をクリックします。 スクリプト ファイルが Visual Studio Team Services エディターで開かれます。

    ![Visual Studio Team Services コード エディター](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. オンライン エディターで、コード ファイルを変更します。 変更内容は、入力時に自動的に保存されています。

エディターから、サイト上のコードを実行することもできます。





[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png 
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png 
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png 
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png 
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png 
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png 
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png 
[android client quickstart]: app-service-mobile-android-get-started.md 
[ios client quickstart]: app-service-mobile-ios-get-started.md 
[xamarin.ios client quickstart]: app-service-mobile-xamarin-ios-get-started.md 
[xamarin.android client quickstart]: app-service-mobile-xamarin-android-get-started.md 
[xamarin.forms client quickstart]: app-service-mobile-xamarin-forms-get-started.md 
[windows store client quickstart]: app-service-mobile-windows-store-dotnet-get-started.md 
[html/javascript client quickstart]: app-service-html-get-started.md 
[offline data sync]: app-service-mobile-offline-data-sync.md 
[how to configure azure active directory authentication]: app-service-mobile-how-to-configure-active-directory-authentication.md 
[how to configure facebook authentication]: app-service-mobile-how-to-configure-facebook-authentication.md 
[how to configure google authentication]: app-service-mobile-how-to-configure-google-authentication.md 
[how to configure microsoft authentication]: app-service-mobile-how-to-configure-microsoft-authentication.md 
[how to configure twitter authentication]: app-service-mobile-how-to-configure-twitter-authentication.md 
[azure app service deployment guide]: ../app-service-web/web-site-deploy.md 
[monitoring an azure app service]: ../app-service-web/web-sites-monitor.md 
[enable diagnostic logging in azure app service]: ../app-service-web/web-sites-enable-diagnostic-log.md 
[toubleshoot an azure app service in visual studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md 
[specify the node version]: ../nodejs-specify-node-version-azure-apps.md 
[use node modules]: ../nodejs-use-node-mobiles-azure-apps.md 
[create a new azure app service]: ../app-service-web/ 
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps 
[express]: http://expressjs.com/ 
[azure portal]: https://portal.azure.com/ 
[odata]: http://www.odata.org 
[promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise 
[basicapp sample on github]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app 
[todo sample on github]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo 
[samples directory on github]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples 
[static-schema sample on github]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema 
[queryjs]: https://github.com/Azure/queryjs 
[node.js tools 1.1 for visual studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1 
[mssql node.js package]: https://www.npmjs.com/package/mssql 
[microsoft sql server 2014 express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx 
[expressjs middleware]: http://expressjs.com/guide/using-middleware.html 
[winston]: https://github.com/winstonjs/winston 

