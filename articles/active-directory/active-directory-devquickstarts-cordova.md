<properties
    pageTitle="Azure AD Cordova の概要 | Microsoft Azure"
    description="サインインし、OAuth を使用して Azure AD で保護されている API を呼び出すために Azure AD と統合する Cordova アプリケーションを構築する方法を説明します。"
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/13/2015"
    ms.author="vittorib"/>

# Azure AD と Apache Cordova アプリとの統合

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova により、完全なネイティブ アプリケーションとしてモバイル デバイス上で実行できる、HTML5/JavaScript アプリケーションを開発できます。
Azure AD を使用すると、Cordova アプリケーションにエンタープライズ レベルの認証機能を追加できます。 iOS、Android、Windows ストア、および Windows Phone 上で Azure AD のネイティブ SDK をラップする Cordova プラグインにより、ユーザーの AD アカウントを用いたサインオンのサポート、Office 365 と Azure API へのアクセス権の取得、さらにはユーザー独自のカスタム Web API の呼び出し保護を行うように、アプリケーションを拡張できます。

このチュートリアルでは、Active Directory 認証ライブラリ (ADAL) の Apache Cordova プラグインを使用して、簡単なアプリケーションに次の機能を付加して能力を向上させます。

-   少数行のコードで、AD ユーザーを認証し、Azure AD Graph API を呼び出すためのトークンを取得する。
-   そのトークンを使用して Graph API を呼び出し、そのディレクトリのクエリを実行して結果を表示する。  
-   ユーザーに対する認証プロンプトを最小限に抑えるために、ADAL トークン キャッシュを利用する。

これを行うには、次の手順を実行する必要があります。

2. アプリケーションを Azure AD に登録する
2. トークンを要求するコードをアプリケーションに追加する
3. トークンを使用して Graph API のクエリを実行して結果を表示するコードを追加する
4. 対象とするすべてのプラットフォームでの Cordova デプロイメント プロジェクトと、Cordova ADAL プラグインを作成し、エミュレーターでソリューションをテストする

## *0.前提条件*

このチュートリアルを実行するには、以下が必要です。

- アプリケーション開発権限があるアカウントが登録されている Azure AD テナント
- Apache Cordova を使用するように構成された開発環境  

両方とも設定済みの場合は、手順 1. に直接進んでください。

Azure AD テナントをお持ちでない場合 [いずれかを取得する方法については、ここ](active-directory-howto-tenant.md)します。

Apache Cordova がコンピューターにセットアップされていない場合は、以下をインストールしてください。

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/)
  (NPM パッケージ マネージャーを使用して簡単にインストールされていることができます: `npm install -g cordova`)

これらは PC と Mac のどちらでも動作することに注意してください。

各ターゲット プラットフォームには、それぞれ異なる前提条件があります。

- Windows タブレット/PC またはスマートフォンのアプリケーション バージョンを構築して実行する場合
    - [Windows Update 2 以降の visual Studio 2013](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express または別のバージョン)。
- iOS 向けに構築して実行する場合
    -   Xcode 5.x 以上。 Http://developer.apple.com/downloads にダウンロードまたは [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
    -   [ios sim](https://www.npmjs.org/package/ios-sim) – コマンドラインから iOS シミュレーターで iOS アプリケーションを起動することができます (、端末経由で簡単にインストールされていることができます: `npm install -g ios-sim`)

- Android 向けにアプリケーションを構築して実行する場合
    - インストール [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) またはそれ以降。 `JAVA_HOME` (環境変数) が JDK インストール パス (たとえば、C:\Program Files\Java\jdk1.7.0_75) に従って正しく設定されていることを確認します。
    - インストール [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) 追加 `<android-sdk-location>\tools` 場所 (たとえば、C:\tools\Android\android-sdk\tools) を `PATH` 環境変数です。
    - Android SDK Manager を開き (たとえば、端末 `android` 経由で)、以下をインストールします。
    - *Android 5.0.1 (API 21)* プラットフォーム SDK
    - *Android SDK ビルド ツール* バージョン 19.1.0 以上
    - *Android Support Repository* (Extras)

  Android SDK は、既定のエミュレーター インスタンスを提供しません。 実行して、新しいものを作成 `android avd` を選択し、ターミナルから *作成...]* エミュレーターで Android アプリを実行する場合。 推奨 *Api レベル* は Android エミュレーターおよび作成のオプションの詳細については 19 以上を参照 [AVD Manager] (http://developer.android.com/tools/help/avd-manager.html)。


## *1.アプリケーションを Azure AD に登録する*

注: この手順は省略可能です。 このチュートリアルでは、事前に準備された値を用意しています。これにより独自のテナントで何もプロビジョニングしなくても、実行するサンプルを参照できます。 ただし、この手順を必ず実行し、プロセスをよく理解することをお勧めします。このプロセスは、独自のアプリケーションを作成する際に必要になります。

Azure AD は、認識しているアプリケーションに対してのみトークンを発行します。 アプリケーションから Azure AD を使用するには、アプリケーション用のエントリをテナントで事前に作成しておく必要があります。  新しいアプリケーションをテナントに登録するには、次の手順を実行します。

- Microsoft Azure の管理ポータルにサインインします。
- 左側のナビゲーションで [Active Directory] をクリックします。
- アプリケーションの登録先となるテナントを選択します。
- [アプリケーション] タブをクリックし、下部のドロアーで [追加] をクリックします。
- 画面の指示に従い、新しい "ネイティブ クライアント アプリケーション" を作成します。
    - アプリケーションの [名前] には、エンドユーザーがアプリケーションの機能を把握できるような名前を設定します。
    -   "リダイレクト URI" は、トークンをアプリに返すために使用される URI です。 「`http://MyDirectorySearcherApp`」を入力します。

登録が完了すると、AAD により、アプリに一意のクライアント ID が割り当てられます。  この値は、以降のセクションで必要になります。この値は、新しく作成されたアプリの [構成] タブに表示されます。

## *2.このチュートリアルに必要なリポジトリを複製します。*

シェルまたはコマンド ラインから、次のコマンドを入力します。

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-cordova.git
    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3.Cordova アプリを作成します。*

Cordova アプリケーションを作成するには、複数の方法を使用できます。 このチュートリアルでは、Cordova コマンド ライン インターフェイス (CLI) を使用します。
シェルまたはコマンド ラインから、次のコマンドを入力します。


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

このコマンドは、Cordova プロジェクト用のフォルダー構造とスキャフォールディングを作成し、スタート プロジェクトのコンテンツを www サブフォルダーにコピーします。
新しい DirSearchClient フォルダーに移動します。

    cd .\DirSearchClient

Graph API を呼び出すために必要な、ホワイトリスト プラグインを追加します。

     cordova plugin add https://github.com/apache/cordova-plugin-whitelist.git

次に、すべてのサポート対象プラットフォームを追加します。 実稼働するサンプルにするために、少なくとも次の 1 つのコマンドを実行する必要があります。 iOS を Windows 上で、または Windows/Windows Phone を Mac 上でエミュレートすることはできないことに注意してください。

    cordova platform add android@97718a0a25ec50fedf7b023ae63bfcffbcfafb4b
    cordova platform add ios
    cordova platform add windows

最後に、Cordova プラグイン用の ADAL をプロジェクトに追加できます。

    cordova plugin add ../azure-activedirectory-library-for-cordova

## *3.ユーザーを認証し、AAD からトークンを取得するコードを追加します。*

このチュートリアルで開発するアプリケーションは、ベアボーン ディレクトリ検索機能を提供します。この機能を使用すると、エンド ユーザーは、ディレクトリ内の任意のユーザーのエイリアスを入力し、いくつかの基本的な属性を表示できます。  スタート プロジェクトには、アプリケーションの基本的なユーザー インターフェイスの定義が (www/index.html に) 含まれています。さらに基本的なアプリケーションのイベント サイクル、ユーザー インターフェイスのバインド、結果表示ロジックを関連付けるスキャフォールディングが (www/js/index.js に) 含まれています。 残されている作業は、ID タスクを実装するロジックを追加することだけです。

最初に、アプリと対象のリソースとを識別するために AAD が使用するプロトコル値を、コードに設定する必要があります。 これらの値は、後でトークン要求を作成するために使用されます。 index.js ファイルの最上部に、以下のスニペットを挿入します。

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri` と `clientId` の値は、AAD でアプリを記述する値と一致している必要があります。 これらの値は、このチュートリアルの手順 1 で説明しているように、Azure ポータルの [構成] タブに表示されます。
注: 独自のテナントに新しいアプリを登録しないことを選択した場合は、上記の事前構成値を単に貼り付けるだけで、サンプルを実行することができます。ただし、運用環境向けのアプリには、必ず独自の項目を作成する必要があります。

次に、実際のトークン要求コードを追加する必要があります。 `search ` と `renderdata ` の定義の間に以下のスニペットを挿入します。

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
この関数を、2 つの主要な部分に分解することによって調べてみましょう。
このサンプルは、特定の 1 テナントに関連付けるのではなく、どのテナントでも機能するように設計されています。 これは "/common" エンドポイントを使用します。これによってユーザーは、認証時に任意のアカウントを入力することができ、認証要求は、そのアカウントが所属しているテナントに渡されます。
メソッドの最初の部分では、ADAL キャッシュに既に保管されたトークンが存在するかどうかを検査し、存在する場合には、そのトークンの送信元のテナントを使用して ADAL を再初期化します。 これは追加のプロンプトを回避するために必要です。"/common" を使用すると、必ずユーザーに新しいアカウントの入力が求められるからです。
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
メソッドの 2 番目の部分では、適切な tokewn 要求を実行します。
`acquireTokenSilentAsync` メソッドは、ADAL に、UX を表示せずに指定されたリソースのトークンを返すように要求します。 これが起きる可能性があるのは、キャッシュに既に適切なアクセス トークンが保管されているか、またはプロンプトを表示させずに新しいアクセス トークンを取得するために使用できる更新トークンがある場合です。
試行に失敗する場合は、`acquireTokenAsync` に戻ります。これはユーザーにはっきりと認証を求めるプロンプトを表示します。
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
あるので、トークン、お最後に、Graph API を呼び出しし、必要な検索クエリを実行することができます。 次のスニペットを、`authenticate` 定義のすぐ下に挿入します。

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
開始ポイント ファイルにより、テキスト ボックスにユーザーのエイリアスを入力するためのベアボーン UX が提供されています。 このメソッドではその値を使用して、クエリを作成し、アクセス トークンと組み合わせ、Graph に送信し、結果を解析します。 開始ポイント ファイル内に事前に存在している renderData メソッドによって、結果が処理されて表示されます。

## *4.実行*
これでアプリは実行準備完了です。 操作は非常に簡単です。アプリが起動したら、表示したいユーザーのエイリアスをテキスト ボックスに入力して、ボタンをクリックするだけです。 認証情報が求められます。 認証と検索に成功すると、検索対象のユーザーの属性が表示されます。 その後の実行では、前に取得したトークンがキャッシュ内に存在しているので、プロンプトが表示されずに検索が行われます。
アプリを実行するための具体的な手順は、プラットフォームごとに異なります。


##### Windows タブレット/PC アプリケーション バージョンを構築して実行する場合

   `cordova run windows`

   __注__: 最初の実行が、開発者ライセンスのサインインを求められる場合があります。 参照してください [開発者用ライセンス](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) 詳細です。


##### Windows Phone 8.1 でアプリケーションを構築して実行する場合

   コネクテッド デバイスで実行するには、`cordova run windows --device -- --phone` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate windows -- --phone` を実行します。

   使用 `cordova run windows --list -- --phone` 利用可能なすべてのターゲットを表示して `cordova run windows --target=<target_name> -- --phone` 特定のデバイスまたはエミュレーターでアプリケーションを実行する (たとえば、  `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`)。
##### Android デバイスで構築して実行する場合

   コネクテッド デバイスで実行するには、`cordova run android --device` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate android` を実行します。

   __注__: 確認エミュレーター インスタンスを使用して作成した *AVD Manager* での説明に従って *の前提条件* セクションです。

   使用 `cordova run android --list` 利用可能なすべてのターゲットを表示して `cordova run android --target=<target_name>` 特定のデバイスまたはエミュレーターでアプリケーションを実行する (たとえば、  `cordova run android --target="Nexus4_emulator"`)。

##### iOS デバイスで構築して実行する場合

   コネクテッド デバイスで実行するには、`cordova run ios --device` を実行します。

   既定のエミュレーターで実行するには、`cordova emulate ios` を実行します。

   __注__: した確認 `ios-sim` エミュレーターで実行するインストール パッケージです。 参照してください *の前提条件* 詳細についてです。

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

`cordova run --help` を使用すると、追加の構築オプションと実行オプションを参照できます。

参照用の完全なサンプル (構成値を除く) が、ここで提供されています。  ここからは上級のさらに興味深いシナリオに移動することができます。  次のチュートリアルを試してみてください。

[Azure AD による Node.JS Web API のセキュリティ保護 >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

