<properties
   pageTitle="Visual Studio Code を使用した ASP.NET 5 Web アプリの作成"
   description="このチュートリアルでは、Visual Studio Code を使用して ASP.NET 5 Web アプリを作成する方法について説明します。"
   services="app-service\web"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/14/2015" 
    ms.author="erikre"/>


# Visual Studio Code を使用した ASP.NET 5 Web アプリの作成

## 概要

このチュートリアルは、ASP.NET 5 web アプリを使用して、作成する方法を示します [Visual Studio コード (VS コード)](http://code.visualstudio.com//Docs/whyvscode) デプロイ [Azure App Service](../app-service/app-service-value-prop-what-is.md)します。 ASP.NET 5 は、ASP.NET の刷新版です。 ASP.NET 5 は、.NET を使用して最新のクラウドベースの Web アプリを構築するための、新しいオープン ソースのクロスプラットフォーム フレームワークです。 詳細については、次を参照してください。 [ASP.NET 5 の概要](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html)します。 Azure App Service web アプリについては、次を参照してください。 [Web Apps の概要](app-service-web-overview.md)します。

[AZURE.INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## 前提条件

* Install [VS Code](http://code.visualstudio.com/Docs/setup).
* インストール [Node.js](http://nodejs.org) -Node.js、JavaScript を使用して高速かつスケーラブルなサーバー アプリケーションを構築するためのプラットフォームです。 Node はランタイム (ノード) と [npm](http://www.npmjs.com/) は Node モジュールのパッケージ マネージャー。 このチュートリアルでは、npm を使用して、ASP.NET 5 Web アプリをスキャフォールディングします。
* Git をインストールいずれかの場所からインストールすることができます: [Chocolatey](https://chocolatey.org/packages/git) または [git-scm.com](http://git-scm.com/downloads)します。 Git に慣れていない場合は、選択 [git-scm.com](http://git-scm.com/downloads) オプションを選択し、 **Windows コマンド プロンプトから Git**します。 Git をインストールした後、(VS コードからコミットを実行する場合に) チュートリアルの後半で必要になるため、Git のユーザー名と電子メールも設定する必要があります。

## ASP.NET 5 と DNX のインストール

ASP.NET 5 と DNX (.NET Execution Environment) は、OS X、Linux、Windows 上で動作する最新のクラウドや Web アプリを構築するための、効率の優れた .NET スタックです。 ASP.NET 5 および DNX は、一から設計し直され、クラウドにデプロイされるアプリまたはオンプレミスで実行されるアプリ用に最適化された開発フレームワークを提供します。 オーバーヘッドを最小に抑えたモジュラー コンポーネントから構成されるため、ソリューションを構築するときに柔軟性を保つことができます。

このチュートリアルでは、最新の開発バージョンの ASP.NET 5 と DNX を使用してアプリケーションの構築を開始する方法について説明します。 次の手順は、Windows に固有の手順です。 OS X、Linux、および Windows のインストール手順の詳細を参照してください。 [をインストールする ASP.NET 5 と DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)します。

1. .NET Version Manager (DNVM) をインストールするには、コマンド プロンプトを開き、次のコマンドを実行します。

        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

    DNVM スクリプトがダウンロードされ、ユーザー プロファイル ディレクトリに配置されます。

2. DNVM のインストールを完了するには、**Windows を再起動**します。

    Windows を再起動した後、コマンド プロンプトを開き、以下を入力して、DNVM の場所を確認できます。

        where dnvm

    コマンド プロンプトで、パスは次のように表示されます。

    ![dnvm の場所](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

3. これで、DNVM を利用できるようになりました。アプリケーションを実行するには、これを使って DNX をダウンロードする必要があります。 コマンド プロンプトで、次のコマンドを実行します。

        dnvm upgrade

    コマンド プロンプトで次のコマンドを実行して、DNVM を確認し、アクティブなランタイムを表示します。

        dnvm list

    コマンド プロンプトに、アクティブなランタイムの詳細が表示されます。

    ![DNVM の場所](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

    複数の DNX ランタイムが表示されている場合は、コマンド プロンプトで以下 (またはこれ以降のバージョン) を入力してアクティブな DNX ランタイムを設定することを選択できます。 このチュートリアルの後半で Web アプリを作成するときに、ASP.NET 5 ジェネレーターによって使用される同じバージョンに設定します。 *最新バージョンに設定されている場合は、アクティブなランタイムを変更する必要はありません。*

        dnvm use 1.0.0-update1 –p


> [AZURE.NOTE] OS X、Linux、および Windows のインストール手順の詳細を参照してください。 [をインストールする ASP.NET 5 と DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)します。 

## Web アプリの作成

このセクションでは、新しい ASP.NET Web アプリをスキャフォールディングする方法について説明します。 ノード パッケージ マネージャー (npm) を使用してインストールする [Yeoman](http://yeoman.io/) (アプリケーション スキャフォールディング ツール - Visual Studio に相当する VS コード **ファイル > [新しいプロジェクト** 操作)、 [Grunt](http://gruntjs.com/) (JavaScript タスク ランナー)、および [Bower](http://bower.io/) (クライアント側のパッケージ マネージャー)。

1. 管理者権限でコマンド プロンプトを開き、ASP.NET プロジェクトを作成する場所に移動します。 たとえば、作成、 *vscodeprojects* C:\ のルートにあるディレクトリ。

2. コマンド プロンプトで次のコマンドを入力して、Yeoman とサポート ツールをインストールします。

        npm install -g yo grunt-cli generator-aspnet bower

    > [AZURE.NOTE] npm バージョンが古くなっていること示す警告が表示される場合があります。 この警告は、このチュートリアルには影響しません。

3. コマンド プロンプトで次のコマンドを入力して、プロジェクト フォルダーを作成し、アプリをスキャフォールディングします。

        yo aspnet

4. 方向キーを使用して、 **Web アプリケーションの基本的な** ASP.NET 5 ジェネレーター メニューのおよびキーを押してから型 **< Enter >**します。

    ![Yeoman - ASP.NET 5 ジェネレーター](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. 新しい ASP.NET Web アプリの名前を **SampleWebApp** に設定します。 この名前はチュートリアル全体で使用されるため、別の名前を選択する場合は、**SampleWebApp** をすべてその名前に置き換える必要があります。 押すと **< Enter >**, 、Yeoman という名前の新しいフォルダーが作成されます **SampleWebApp** と新しいアプリに必要なファイルです。

6. コマンド プロンプトで、ディレクトリを新しいプロジェクト フォルダーに変更します。

        cd SampleWebApp

7. また、コマンド プロンプトで、必要な NuGet パッケージをインストールしてアプリケーションを実行し、次のコマンドを入力します。

        dnu restore

8. コマンド プロンプトで次のコマンドを入力して、VS コードを開きます。

        code .


## ローカルでの Web アプリの実行

Web アプリが作成され、アプリのすべての NuGet パッケージが取得されたため、Web アプリをローカルで実行できます。

1. VS コードの**コマンド パレット**で、次のコマンドを入力して使用可能な実行コマンド オプションを表示します。

        dnx: Run Command

    > [AZURE.NOTE] Omnisharp サーバーが実行されていない場合は、起動されます。 上のコマンドを再入力します。

    次に、次のコマンドを選択して Web アプリを実行します。

        dnx web - (SampleWebApp)

    コマンド ウィンドウに、アプリケーションが起動したことが示されます。 コマンド ウィンドウにこのメッセージが表示されない場合は、VS コードの左下隅にプロジェクトのエラーが示されていないかどうかを確認します。
    > [AZURE.NOTE] **コマンド パレット**からコマンドを発行するには、コマンド ラインの先頭に **>** 文字が必要です。 **web** コマンドに関する詳細は *project.json* ファイルで確認できます。 

2. ブラウザーを開き、次の URL に移動します。

    **http://localhost:5000**

    Web アプリの既定のページが次のように表示されます。

    ![ブラウザーでのローカル Web アプリ](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

3. ブラウザーを閉じます。 **コマンド ウィンドウ**で、**Ctrl+C** キーを押してアプリケーションをシャットダウンするか、**コマンド ウィンドウ**を閉じます。

## Azure ポータルで Web アプリを作成する

次の手順では、Azure ポータルでの Web アプリの作成について説明します。

1. ログイン、 [Azure ポータル](https://portal.azure.com)します。

2. ポータルの左上にある **[新規]** をクリックします。

3. **[Web アプリ] > [Web アプリ]** をクリックします。

    ![Azure の新しい Web アプリ](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. **[名前]** に **SampleWebAppDemo** などの値を入力します。 この名前は、一意にする必要があります。ポータルで名前を入力しようとするときに、この一意性が要求されます。 したがって、別の値を選択する場合は、このチュートリアルに表示されるすべての **SampleWebAppDemo** をこの値に置き換える必要があります。

5. 既存の **App Service プラン**を選択するか、新しいプランを作成します。 新しいプランを作成する場合は、価格レベル、および場所などのオプションを選択します。 App Service プランの詳細については、記事を参照してください。 [Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)します。

    ![Azure の新しい  Web アプリ ブレード](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. **[作成]** をクリックします。

    ![Web アプリ ブレード](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## 新しい Web アプリの Git 発行の有効化

Git は、Azure App Service の Web アプリをデプロイするために使用できる分散型バージョン コントロール システムです。 Web アプリ用に記述したコードはローカルの Git リポジトリに格納されます。このコードをリモート リポジトリにプッシュして Azure にデプロイします。

1. ログイン、 [Azure ポータル](https://portal.azure.com)します。

2. **[参照]** をクリックします。

3. **[Web アプリ]** をクリックして 、Azure サブスクリプションに関連付けられている Web アプリの一覧を表示します。

4. このチュートリアルで作成した Web アプリを選択します。

5. Web アプリのブレードで、**[デプロイ]** セクションにスクロールし、**[継続的デプロイの設定]** をクリックします。

    ![Azure Web アプリ ホスト](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. **[ソースの選択]、[ローカル Git リポジトリ]** の順にクリックします。

7. **[OK]** をクリックします。

    ![Azure のローカル Git リポジトリ](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. Web アプリまたはその他の App Service アプリを発行するためのデプロイメント資格情報をまだ設定していない場合は、ここで設定します。

    * **[設定]**、**[デプロイ資格情報]** の順にクリックします。 **[デプロイ資格情報の設定]** ブレードが表示されます。

    * ユーザー名とパスワードを作成します。 このパスワードは、後で Git をセットアップするときに必要になります。

    * **[保存]** をクリックします。

9. Web アプリのブレードで、**[設定]、[プロパティ]** の順にクリックします。 デプロイ先のリモート Git リポジトリの URL は、**[GIT URL]** の下に表示されます。

10. チュートリアルで後で使用するために、**GIT URL** の値をコピーします。

    ![Azure Git の URL](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## Azure App Service への Web アプリの発行

このセクションでは、ローカル Git リポジトリを作成し、そのリポジトリから  Azure にプッシュして、Web アプリを Azure にデプロイします。

1. VS コードの左側のナビゲーション バーで、**[Git]** オプションを選択します。

    ![VS コードでの Git アイコン](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. **[Git リポジトリの初期化]** を選択して、ワークスペースが Git によるソース管理の対象になるように設定します。

    ![Git の初期化](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. コマンド ウィンドウを開き、ディレクトリを Web アプリのディレクトリに変更します。 次のコマンドを入力します。

        git config core.autocrlf false

    このコマンドは、末尾に CRLF と LF が含まれているテキストに関する問題を防ぎます。

4. VS コードで、コミット メッセージを追加し、**[すべてコミット]** チェック アイコンをクリックします。

    ![Git すべてコミット](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

5. Git の処理が完了した後には、[Git] ウィンドウの **[変更]** の下にいかなるファイルも表示されません。

    ![Git 変更なし](./media/web-sites-create-web-app-using-vscode/no-changes.png)

6. コマンド プロンプトが Web アプリがあるディレクトリを指しているコマンド ウィンドウに戻します。

7. コピーしておいた (".git" で終わる) Git URL を使用して、Web アプリに更新をプッシュするためのリモート参照を作成します。

        git remote add azure [URL for remote repository]

8. 資格情報をローカルに保存するように Git を構成して、VS コードから生成される push コマンドに資格情報が自動的に追加されるようにします。

        git config credential.helper store

9. 次のコマンドを入力して、変更内容を Azure にプッシュします。 この Azure への初回の push を実行した後、VS コードからすべての push コマンドを実行できます。

        git push -u azure master

    Azure で以前に作成したパスワードを入力するように求められます。 **注: パスワードは表示されません。**

    このコマンドからは、デプロイメントが成功したというメッセージが最後に出力されます。

        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
        [new branch]      master -> master


> [AZURE.NOTE] アプリを変更した場合は、組み込み Git 機能を使用して VS コードに直接再パブリッシュできます。これを行うには、**[すべてコミット]** オプションに続けて **[プッシュ]** オプションを選択します。 **[プッシュ]** オプションは、**[すべてコミット]** ボタンと **[更新]** ボタンの隣のドロップダウン メニューに含まれています。

プロジェクトで共同作業を行う必要がある場合は、Azure へのプッシュの間に GitHub へのプッシュを実行することを考慮してください。

## Azure でのアプリの実行

これで Web アプリがデプロイされたため、Azure でホストされているアプリを実行してみましょう。

これは、2 つの方法で実行できます。

* ブラウザーを開き、次のように、Web アプリの名前を入力します。

        http://SampleWebAppDemo.azurewebsites.net

* Azure ポータルで、Web アプリの Web アプリ ブレードを見つけ、**[参照]** をクリックして、既定のブラウザーで
* アプリを表示します。

![Azure の Web アプリ](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## 概要

このチュートリアルでは、VS コードで、Web アプリを作成し、Azure にデプロイする方法を学習しました。 VS コードの詳細については、記事を参照してください [Visual Studio コードではなぜですか?](https://code.visualstudio.com/Docs/)。App Service web アプリについては、次を参照してください。 [Web Apps の概要](app-service-web-overview.md)します。




