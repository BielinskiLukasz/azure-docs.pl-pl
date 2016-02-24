<properties
    pageTitle="PHP の Web ロールと worker ロール | Microsoft Azure"
    description="Azure クラウド サービスで PHP Web ロールおよび worker ロールを作成し、PHP ランタイムを構成するためのガイド。"
    services=""
    documentationCenter="php"
    authors="tfitzmac"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="09/01/2015"
    ms.author="tomfitz"/>

#PHP Web ロールと worker ロールを作成する方法

## 概要

このガイドでは、Windows 開発環境で PHP Web ロールまたは worker ロールを作成し、使用可能な "ビルトイン" バージョンから特定バージョンの PHP を選択して、PHP 構成を変更し、拡張機能を有効にして、最後に Azure にデプロイする方法を示します。 また、指定した PHP ランタイムを (カスタムの構成および拡張機能と共に) 使用できるように Web ロールまたは worker ロールを構成する方法についても説明します。

## PHP Web ロールと worker ロールについて

Azure にはアプリケーションを実行するためのコンピューティング モデルとして、Azure App Service、Azure Virtual Machines、および Azure Cloud Services の 3 種類があります。 これら 3 つのモデルはすべて、PHP をサポートしています。 クラウド サービスは、web および worker ロールが含まれている提供 *サービス (PaaS) としてのプラットフォーム*します。 クラウド サービス内で、Web ロールはフロントエンド Web アプリケーションをホストする専用のインターネット インフォメーション サービス (IIS) Web サーバーを提供します。 worker ロールは、ユーザーの操作や入力とは関係なく、非同期タスク、長時間かかるタスク、または常駐タスクを実行できます。

これらのオプションの詳細については、次を参照してください。 [コンピューティング ホスティング オプションが Azure によって提供される](./cloud-services/fundamentals-application-models.md)します。

## Azure SDK for PHP をダウンロードする

 [Azure SDK for PHP] いくつかのコンポーネントで構成されています。 この記事では、そのうち Azure PowerShell と Azure エミュレーターの 2 つを使用します。 これら 2 つのコンポーネントは、Microsoft Web Platform Installer を使用してインストールできます。 詳細については、次を参照してください。 [をインストールして、Azure PowerShell を構成する方法](powershell-install-configure.md)します。

## Cloud Services プロジェクトを作成する

PHP web またはワーカー ロールを作成する最初の手順では、Azure サービス プロジェクトを作成します。 Azure サービス プロジェクトは、web および worker ロールの論理コンテナーとして機能し、プロジェクトが含まれている [service definition (.csdef)] と [service configuration (.cscfg)] ファイルです。

新しい Azure サービス プロジェクトを作成するには、Azure PowerShell を管理者として実行し、次のコマンドを実行します。

    PS C:\>New-AzureServiceProject myProject

このコマンドを実行すると、Web ロールおよび worker ロールを追加できる新しいディレクトリ (`myProject`) が作成されます。

## PHP Web ロールまたは worker ロールを追加する

PHP Web ロールをプロジェクトに追加するには、プロジェクトのルート ディレクトリで次のコマンドを実行します。

    PS C:\myProject> Add-AzurePHPWebRole roleName

worker ロールについては、次のコマンドを使用します。

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE]  `roleName` パラメーターは省略可能です。 省略した場合、ロール名は自動的に生成されます。 最初に作成された Web ロールは `WebRole1`、2 番目は `WebRole2` などとなります。 最初に作成された worker ロールは `WorkerRole1`、2 番目は `WorkerRole2` などとなります。

## ビルトイン PHP バージョンを指定する

PHP Web ロールまたは worker ロールをプロジェクトに追加すると、プロジェクトの構成ファイルが変更され、アプリケーションのデプロイ時に、アプリケーションの各 Web インスタンスまたはワーカー インスタンスに PHP がインストールされるように指定されます。 既定でインストールされる PHP のバージョンを確認するには、次のコマンドを実行します。

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

このコマンドからの出力は、下のような結果になります。 この例では、PHP 5.3.17 に対して `IsDefault` フラグが `true` に設定されており、インストールされる既定の PHP バージョンであることを示しています。

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

PHP ランタイム バージョンは、表示されている任意の PHP バージョンに設定できます。 たとえば、(`roleName` という名前のロールに対して) PHP バージョンを 5.4.0 に設定するには、次のコマンドを使用します。

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] 今後は、使用可能な PHP バージョンを変更できます。

## ビルトインの PHP ランタイムをカスタマイズする

前に示した手順に従ってインストールした PHP ランタイムの構成は、`php.ini` 設定の変更や拡張機能の有効化など、詳細に制御できます。

ビルトインの PHP ランタイムをカスタマイズするには、次の操作を行います。

1. Web ロールの `bin` ディレクトリに、`php` という名前の新しいフォルダーを追加します。 worker ロールについては、このフォルダーをロールのルート ディレクトリに追加します。
2. `php` フォルダー内に、`ext` という名前の別のフォルダーを作成します。 有効にする `.dll` 拡張ファイル (`php_mongo.dll` など) はすべて、このフォルダーに配置します。
3. `php` フォルダーに `php.ini` ファイルを追加します。 このファイル内では、カスタム拡張を有効にし、PHP ディレクティブを設定します。 たとえば、`display_errors` をオンにして `php_mongo.dll` 拡張機能を有効にする場合、`php.ini` ファイルの内容は次のようになります。

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] 明示的に設定していないすべての設定、 `php.ini` ファイルが自動的に提供することは、既定値に設定します。 ただし、すべてを指定した `php.ini` ファイルを追加できることも覚えておいてください。

## 独自の PHP ランタイムを使用する
必要に応じて、ビルトインの PHP ランタイムを選択して前の手順のように構成する代わりに、独自の PHP ランタイムを使用することができます。 たとえば、開発環境で使用するものと同じ PHP ランタイムを Web ロールまたは worker ロールで使用することができます。 これにより、運用環境でもアプリケーションの動作が変わらないことが容易に確認できます。

### 独自の PHP ランタイムが使用されるように Web ロールを構成する

指定した PHP ランタイムが使用されるように Web ロールを構成するには、次の手順に従います。

1. 前のトピックの説明に従って、Azure Service プロジェクトを追加し、PHP Web ロールを追加します。
2. Web ロールのルート ディレクトリにある `bin` フォルダー内に `php` フォルダーを作成し、この `php` フォルダーに PHP ランタイム (すべてのバイナリ、構成ファイル、サブフォルダーなど) を追加します。
3. (省略可能)PHP ランタイムで使用する場合、 [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers], をインストールする web ロールを構成する必要があります [SQL Server Native Client 2012][sql native client] プロビジョニング時にします。 これを行うには、追加、 [sqlncli.msi x64 installer] に、 `bin` web ロールのルート ディレクトリ内のフォルダーです。 次のステップで説明するスタートアップ スクリプトでは、ロールのプロビジョニング時にインストーラーが自動的に実行されます。 PHP ランタイムに Microsoft Drivers for PHP for SQL Server が使用されていない場合は、次のステップで示すスクリプトから、この行を削除できます。

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. 構成するスタートアップ タスクを定義する [インターネット インフォメーション サービス (IIS)][iis.net] 要求を処理する PHP ランタイムを使用する `.php` ページです。 これを行うには、`setup_web.cmd` ファイル (Web ロールのルート ディレクトリにある `bin` フォルダー内) をテキスト エディターで開き、内容を次のスクリプトに置き換えます。

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. アプリケーション ファイルを Web ロールのルート ディレクトリに追加します。 これは Web サーバーのルート ディレクトリになります。

6. アプリケーションの発行」の説明に従って、 [アプリケーションを発行](#how-to-publish-your-application) 以下のセクションです。

> [AZURE.NOTE]  `download.ps1` スクリプト (で、 `bin` web ロールのルート ディレクトリのフォルダー)、独自の PHP ランタイムを使用するための前述の手順を実行した後に削除することができます。

### 独自の PHP ランタイムが使用されるように worker ロールを構成する

指定した PHP ランタイムが使用されるように worker ロールを構成するには、次の手順に従います。

1. 前のトピックの説明に従って、Azure Service プロジェクトを追加し、PHP Web ロールを追加します。
2. worker ロールのルート ディレクトリに `php` フォルダーを作成し、この `php` フォルダーに PHP ランタイム (すべてのバイナリ、構成ファイル、サブフォルダーなど) を追加します。
3. (省略可能)PHP ランタイムで使用する場合 [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers], をインストールするワーカー ロールを構成する必要があります [SQL Server Native Client 2012][sql native client] プロビジョニング時にします。 これを行うには、追加、 [sqlncli.msi x64 installer] ワーカー ロールのルート ディレクトリにします。 次のステップで説明するスタートアップ スクリプトでは、ロールのプロビジョニング時にインストーラーが自動的に実行されます。 PHP ランタイムに Microsoft Drivers for PHP for SQL Server が使用されていない場合は、次のステップで示すスクリプトから、この行を削除できます。

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. ロールのプロビジョニング時に worker ロールの PATH 環境変数に `php.exe` 実行可能ファイルを追加するスタートアップ タスクを定義します。 これを行うには、`setup_worker.cmd` ファイル (worker ロールのルート ディレクトリ内) をテキスト エディターで開き、内容を次のスクリプトに置き換えます。

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. アプリケーション ファイルを worker ロールのルート ディレクトリに追加します。

6. アプリケーションの発行」の説明に従って、 [アプリケーションを発行](#how-to-publish-your-application) 以下のセクションです。

## コンピューティング エミュレーターとストレージ エミュレーターでアプリケーションを実行する

Azure エミュレーターは、Azure アプリケーションをクラウドにデプロイする前にテストできるローカル環境を提供します。 ただし、エミュレーターと Azure 環境では、いくつか相違点があります。 詳細については、次を参照してください。 [開発およびテスト用の Azure ストレージ エミュレーターを使用して](./storage/storage-use-emulator.md)します。

コンピューティング エミュレーターを使用する場合は、ローカルで PHP をインストールしておく必要があることに注意してください。 コンピューティング エミュレーターでは、アプリケーションを実行するためにローカルの PHP インストールが使用されます。

エミュレーターでプロジェクトを実行するには、プロジェクトのルート ディレクトリで次のコマンドを実行します。

    PS C:\MyProject> Start-AzureEmulator

出力は以下のようになります。

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Web ブラウザーを開き、出力に示されているローカル アドレス (上の出力例では `http://127.0.0.1:81`) に移動すると、アプリケーションがエミュレーターで実行されていることを確認できます。

エミュレーターを停止するには、次のコマンドを実行します。

    PS C:\MyProject> Stop-AzureEmulator

## アプリケーションの発行

アプリケーションを発行するには、最初にインポートする必要があります、発行の設定を使用して、 **Import-publishsettingsfile** コマンドレットです。 使用してアプリケーションを発行することができ、 [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) コマンドレットです。 署名に関する詳細については、次を参照してください。 [をインストールして、Azure PowerShell を構成する方法](powershell-install-configure.md)します。

## 次のステップ

詳細については、次を参照してください。、 [PHP デベロッパー センター](/develop/php/)します。

[Azure SDK for PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[service definition (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[service configuration (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 installer]: http://go.microsoft.com/fwlink/?LinkID=239648

