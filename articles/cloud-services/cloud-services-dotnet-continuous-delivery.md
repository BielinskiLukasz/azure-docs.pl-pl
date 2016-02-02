<properties
    pageTitle="Azure での TFS を使用したクラウド サービスの継続的な配信 | Microsoft Azure"
    description="Azure クラウド アプリケーションの継続的な配信を設定する方法について説明します。MSBuild コマンド ライン ステートメントおよび PowerShell スクリプトのコード サンプル。"
    services="cloud-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor="tglee"/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/18/2015"
    ms.author="tarcher"/>


# Azure での Cloud Services の継続的な配信

この記事のプロセスでは、Azure クラウド アプリケーションの継続的な配信を
設定する方法について説明します。 このプロセスを使用すると、
パッケージを作成し、すべてのコードをチェックインした後にパッケージを Azure に
後のコードではチェックインごとです。 パッケージのビルド プロセスのこのトピックの説明
記事は、 **パッケージ** Visual Studio で、コマンド、
発行手順は対応する、 **発行** Visual Studio のコマンドです。
この記事では、MSBuild コマンド ライン ステートメントと Windows PowerShell スクリプトを使用して
ビルド サーバーを作成するためのいくつかの方法を紹介し、
オプションで Visual Studio Team Foundation Server を構成する方法
(MSBuild コマンドと PowerShell スクリプトを使用するためのチーム ビルド定義)
について説明します。 このプロセスは、ビルド環境や
Azure ターゲット環境に合わせてカスタマイズできます。

また、Visual Studio Team Services (Azure でホストされる TFS のバージョン) を使用すると、これらの操作をより簡単に実行できます。 詳細については、次を参照してください。 [を使用する Visual Studio Team Services [] による Azure への継続的な配信][]します。

作業を開始する前に、Visual Studio からアプリケーションを発行する必要があります。
これにより、発行プロセスの自動化を行う際にすべてのリソースが利用可能で、
初期化されることを確認できます。

## ステップ 1.: ビルド サーバーを構成する

MSBuild を使用して Azure パッケージを作成するには、
ビルド サーバーに必要なソフトウェアおよびツールを事前にインストールしておく必要があります。

ビルド サーバーには、Visual Studio をインストールする必要はありません。 もし
を使用してビルド サーバーを管理する場合は、
サーバー、の指示に従って、 [Team Foundation ビルド サービスの][]
従ってください。

1.  ビルド サーバーにインストール、 [.NET Framework 4.5.2:operator[]][], 、MSBuild が含まれます。
2.  最新のインストール [.NET 向け Azure Authoring Tools](https://azure.microsoft.com/develop/net/)します。
3.  インストール、 [Azure Libraries for .NET](http://go.microsoft.com/fwlink/?LinkId=623519)します。
4.  ビルド サーバーの Visual Studio インストールにある Microsoft.WebApplication.targets ファイルをコピーします。

    Visual Studio がインストールされているコンピューターでは、このファイルはディレクトリ C:\\Program Files (x86) \\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications にあります。 このファイルを、ビルド サーバー上の同じディレクトリにコピーします。
5.  インストール、 [Azure Tools for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx)します。

## ステップ 2.: MSBuild コマンドを使用してパッケージをビルドする

このセクションでは、Azure パッケージをビルドする MSBuild コマンドを作成する方法
について説明します。 ビルド サーバー上でこの手順を実行して、
すべてが適切に構成され、MSBuild コマンドが予定どおりに動作することを
目的必要になるとします。 既存のものにこのコマンド ラインを追加します。
既存のビルド スクリプトに追加することができます。また、次のセクションで説明するように、
TFS ビルド定義内でコマンド ラインを使用してもかまいません。 サブスクリプションの
コマンド ライン パラメーターおよび MSBuild をに関する情報を参照してください [MSBuild コマンド ライン リファレンス](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx)します。

1.  Visual Studio がビルド サーバーにインストールされている場合は、検索して選択
    **Visual Studio コマンド プロンプト]** で、 **Visual Studio ツール**
    Windows のフォルダーです。

    Visual Studio がビルド サーバーにインストールされていない場合は、
    コマンド プロンプトを開き、MSBuild.exe へのパスを
    確認します。 MSBuild は、.NET Framework と共に
    %WINDIR%\\Microsoft.NET\\Framework\\*バージョン*します。 たとえば、
    .NET Framework 4 をインストールしたときに MSBuild.exe を
    PATH 環境変数に追加するには、コマンド プロンプトで次のコマンドを
    入力します。

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  コマンド プロンプトで格納しているフォルダに移動します
    Azure プロジェクト ファイルが含まれているフォルダーに移動します。

3.  MSBuild/target:publish を実行します次のようにオプションのパブリッシュ。
    例:

        MSBuild /target:Publish

    このオプションは、/t:Publish のように短縮できます。 Azure SDK をインストールしている場合は、
    MSBuild の /t:Publish オプションと Visual Studio の
    Publish コマンドとを混同しないように
    注意してください。 /t:Publish オプションは、Azure パッケージを
    ビルドするのみです。 Visual Studio の Publish コマンドとは異なり、
    パッケージをデプロイしません。

    必要に応じて、プロジェクト名を MSBuild のパラメーターとして
    指定できます。 このパラメーターを指定しない場合は、現在のディレクトリが使用されます。 サブスクリプションの
    MSBuild コマンド ライン オプションについては、[MSBuild コマンドを参照してください。
    参照の行] [1] です。

4.  出力を見つけます。 既定では、プロジェクトのルート フォルダーに対して
    ディレクトリが作成されます (たとえば、
    *ProjectDir*\\bin\\*構成*\\app.publish\\ します。 Azure プロジェクトを
    ビルドすると、パッケージ ファイルとそれに対応する構成ファイルの
    2 つのファイルが生成されます。

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    既定で、各 Azure プロジェクトでは
    1 つのサービス構成ファイル (.cscfg ファイル) でローカル (デバッグ)
    ビルドとクラウド (ステージングまたは運用) ビルドに対応しますが、
    必要に応じてサービス構成ファイルを追加したり削除したりできます。 Azure プロジェクトを
    パッケージをビルドするときは、パッケージに含める
    サービス構成ファイルを指定するよう求められます。

5.  サービス構成ファイルを指定します。 MSBuild を使用してパッケージをビルドする場合、
    既定でローカル サービス構成ファイルが
    含められます。 別のサービス構成ファイルを含めるには、
    次の例に示すように、MSBuild コマンドの TargetProfile プロパティを
    例:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  出力先を指定します。 パスを
    /p:PublishDir =*ディレクトリ*\\ オプションを末尾に、
    次の例に示すように、末尾にバックスラッシュ区切り記号を指定します。

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    プロジェクトをビルドして Azure パッケージに結合するために使用する
    適切な MSBuild コマンド ラインの作成とテストが完了したら、
    このコマンド ラインをビルド スクリプトに追加します。 ビルド
    サーバーでカスタム スクリプトを使用する場合、このプロセスは、
    ビルド カスタム プロセスの特定の内容に依存します。 ビルド環境として TFS を
    使用している場合は、次のステップに従って
    Azure パッケージ ビルドをビルド プロセスに追加できます。

## ステップ 3.: TFS チーム ビルドを使用してパッケージをビルドする

ビルド コントローラーとして Team Foundation Server (TFS) を設定し、
ビルド サーバーが、TFS ビルド コンピューターとして設定し、必要に応じてセットアップすることができます。
Azure パッケージの自動化されたビルドを設定できます。 Team Foundation Server を
ビルド システムとして設定および使用する方法については、「
[スケール アウト ビルド システムの][]します。 特に、
次の手順では、
」の説明に従って [配置してビルド サーバーを構成する][], 、作成しました。
およびチーム プロジェクトを作成し、チーム プロジェクトにクラウド サービス プロジェクトを作成していることを前提としています。

Azure パッケージをビルドするために TFS を構成するには、次の手順を
実行します。

1.  開発コンピューターの Visual Studio で、[表示]
    メニューの [選択 **チーム エクスプ ローラー**, 、または Ctrl + \\、ctrl キーを押しながら M です。 [
    チーム エクスプ ローラー] ウィンドウで、[、 **ビルド** ノードかを選択して、 **ビルド**
    ページ、押し **ビルド定義の新規**します。

    ![][0]

2.  選択、 **トリガー** タブをクリックし、必要な条件を指定
    パッケージのビルドを実行する条件を指定します。 たとえば、
    **継続的インテグレーション** されるたびに、ソース パッケージを作成するには
    指定します。

3.  選択、 **ソース設定** ] タブし、プロジェクト フォルダーが表示されていることを確認
    **ソース管理フォルダー** 列、および状態は **Active**します。

4.  選択、 **ビルドの既定値** タブをクリックし、ビルド コント ローラーの下にあることを確認
    ビルド サーバーの名前を確認します。 また、オプションを選択 * * ビルドをコピー
    出力を次ドロップ フォルダー * * して、目的の格納
    指定します。

5.  **[プロセス]** タブを選択します。 [プロセス] タブで、既定の
    テンプレート [ **ビルド**, 、プロジェクトを選択して選択されていない場合
    展開し、 **詳細** セクション、 **ビルド** グリッドのセクションです。

6.  選択 **MSBuild 引数**, 、し、適切な MSBuild の設定
    上の手順 2 で説明したように適切な MSBuild コマンド ライン引数を設定します。 たとえば、次のように入力します。
    入力 **/t:/t:publish/p:PublishDir = \\\myserver\\drops\\** を構築する、
    入力します。
    \\\\myserver\\drops\\:

    ![][2]

    **注:** ファイルをパブリック共有にコピーしやすくには
    開発コンピューターからパッケージを手動でデプロイする場合に便利です。

5.  変更をプロジェクトにチェックインしてビルド手順が成功したかどうかをテストするか、
    新しいビルドをキューに配置します。 新しいビルドをキューに配置するには、
    チーム エクスプ ローラーを右クリックして **すべてのビルド定義** し、
    選択 **新しいビルドをキュー**します。

## ステップ 4.: PowerShell スクリプトを使用してパッケージを発行する

このセクションでは、オプション パラメーターを使用して、クラウド アプリケーション パッケージ出力を
Azure に発行する Windows PowerShell スクリプトを作成する方法について
説明します。 このスクリプトは、カスタム ビルド自動化のビルド手順の後に
呼び出すことができます。 このスクリプトは、
Visual Studio TFS チーム ビルドのプロセス テンプレート ワークフロー アクティビティから呼び出すこともできます。

1.  インストール、 [Azure PowerShell コマンドレット][] (v0.6.1 以降)。
    コマンドレット セットアップ フェーズで、スナップインとしてインストールすることを選択します。 注
    従来 CodePlex で提供されていたバージョンは 2.x.x でしたが、
    これはそれに代わる公式にサポートされたバージョンです。

2.  [スタート] メニューまたは [スタート] ページを使用して Azure PowerShell を起動します。 この方法で起動すると、
    Azure PowerShell コマンドレットが読み込まれます。

3.  PowerShell プロンプトで、PowerShell コマンドレットが読み込まれているかどうかを確認します。
    部分的なコマンドを入力して `Get Azure` ステートメントの Tab キーを押すと
    確認します。

    Tab キーを何度か押すと、さまざまな Azure PowerShell コマンドが表示されるのを確認できます。

4.  Azure サブスクリプションに接続できることを確認します。
    そのためには、サブスクリプション情報を .publishsettings ファイルからインポートします。

    `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

    次のコマンドを入力します。

    `Get-AzureSubscription`

    サブスクリプションに関する情報が表示されます。 すべての情報が正しいことを確認します。

4.  この記事の末尾に示されているスクリプト テンプレートを
    スクリプト フォルダーに
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**します。

5.  スクリプトのパラメーター セクションを見直します。 既定値を追加または
    変更します。 これらの値は、明示的なパラメーター値を渡すことで
    オーバーライドできます。

6.  発行スクリプトの対象となる有効なクラウド サービスおよびストレージ アカウントが
    サブスクリプションに作成されていることを確認します。 この
    アカウント (BLOB ストレージ) はアップロード用に使用され、
    デプロイを作成するときにデプロイ パッケージと config ファイルを
    一時的に格納します。

    -   新しいクラウド サービスを作成するには、このスクリプトを呼び出すか、
        Azure 管理ポータルです。 クラウド サービス名は、
        完全修飾ドメイン名のプレフィックスとして使用されるため、
        一意である必要があります。

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   新しいストレージ アカウントを作成するには、このスクリプトを呼び出すか、
        Azure 管理ポータルです。 ストレージ アカウント名。
        完全修飾ドメイン名のプレフィックスとして使用されるため、
        一意である必要があります。 クラウド サービスと同じ名前を使用しても
        かまいません。

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"


7.  スクリプトは、Azure PowerShell から直接呼び出す以外に、
    ホスト ビルド自動化に関連付けてパッケージがビルドされるたびに実行されるように
    設定できます。
    >[AZURE.IMPORTANT] スクリプトが常に削除または、既存の置換
    (既存のデプロイが検出された場合)。 これは、ユーザー/オペレーターへのプロンプトが
    不可能な自動化から継続的な配信を実現するために
    必要な操作です。

    **サンプル シナリオ 1:** 、ステージング環境に継続的なデプロイ
    継続的なデプロイ:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    通常は、この後にテスト実行検証と VIP スワップが
    行われます。 VIP スワップは、Azure の管理
    ポータルまたは Move-Deployment コマンドレットを使用して実行できます。

    **サンプル シナリオ 2:** 、運用環境への継続的なデプロイ
    継続的なデプロイ

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **リモート デスクトップ:**

    Azure プロジェクトでリモート デスクトップが有効になっている場合は、
    1 回限りの追加手順を実行して、
    適切なクラウド サービス証明書がこのスクリプトの対象となっているすべてのクラウド サービスにアップロードされることを
    確認する必要があります。

    自分のロールに該当する証明書の拇印値を見つけます。 この
    クラウド構成ファイル (ServiceConfiguration.Cloud.cscfg) の
    Certificates セクションに記載されています。 また、
    サムプリント値は、Visual Studio の [リモート デスクトップ構成] ダイアログで
    [オプションの表示] を選択し、選択した証明書を表示する場合にも表示されます。

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    1 回限りの設定手順として、次のコマンドレット スクリプトを使用して
    リモート デスクトップ証明書をアップロードします。

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    次に例を示します。

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    または、Azure の管理ポータルを使用して、証明書ファイル PFX を秘密キーと共にエクスポートし、
    証明書を各ターゲット クラウド サービスに
    Azure 管理ポータルを開きます。 詳細に
    ついては、
    [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx:operator[]][]します。

    **デプロイのアップグレードとします。削除の展開-\ > 新しい配置**

    スクリプトでは、パラメーターが渡されないとき、
    または値 1 が明示的に渡されたときに、既定でデプロイのアップグレード ($enableDeploymentUpgrade = 1)
    が実行されます。 単一のインスタンスに対しては、
    完全なデプロイに比べて処理に要する時間が少なくなるという利点があります。 また、
    高い可用性が求められるインスタンスに対しても、
    いくつかのインスタンスを実行中に (更新ドメインの) 他のインスタンスをアップグレードできることに加え、
    VIP が削除されないという利点があります

    デプロイのアップグレード操作は、スクリプトで指定する
    ($enableDeploymentUpgrade = 0) か、
    *-enabledeploymentupgrade 0* を変更する、パラメーターとして、
    スクリプトの動作が変更され、既存のデプロイが削除された後、
    新しいデプロイが作成されます。
    >[AZURE.IMPORTANT] スクリプトが常に削除または、既存の置換
    (既存のデプロイが検出された場合)。 これは、ユーザー/オペレーターへのプロンプトが
    不可能な自動化から継続的な配信を実現するために
    必要な操作です。

## ステップ 5.: TFS チーム ビルドを使用してパッケージを発行する

このオプションの手順に接続する TFS チーム ビルド、手順 4. で作成したスクリプト
TFS チーム ビルドを関連付けます。 この
ビルド定義によって使用されるプロセス テンプレートを変更して、
ワークフローの最後に発行アクティビティが実行されるようにする操作が含まれます。 発行
アクティビティでは、ビルドからのパラメーターを渡して PowerShell コマンドを
実行します。 MSBuild ターゲットと発行スクリプトの出力は、
パイプを使用して標準ビルド出力に送られます。

1.  継続的なデプロイのためのビルド定義を編集します。

2.  **[プロセス]** タブを選択します。

3.  次の [手順](http://msdn.microsoft.com/library/dd647551.aspx) を追加する、
    に従って、ビルド プロセス テンプレートのアクティビティ プロジェクトの追加、既定のテンプレートのダウンロードと
    プロジェクトにチェックインします。 ビルド プロセス テンプレートに新しい名前をなど指定します。
    (たとえば、AzureBuildProcessTemplate)。

3.  戻り、 **プロセス** タブをクリックし、使用して **詳細の表示** の利用可能な一覧を表示するには
    ビルド プロセス テンプレートの一覧を表示します。 選択、 **新規...** ボタンをクリックし、プロジェクトに移動します。
    だけ追加およびチェックインします。 作成したテンプレートを確認できたら、**[OK]** をクリックします。

4.  選択したプロセス テンプレートを編集するために開きます。 このファイルは
    XML エディターを使って XAML を操作することも
    できます。

5.  次の新しい引数を、ワークフロー デザイナーの引数タブに
別の行項目として追加します。 すべての引数について、
方向として [入力]、型として [文字列] を設定する必要があります。 これらは、ビルド定義のパラメーターを、
発行スクリプトを呼び出すワークフローに送るために
使用されます。

    SubscriptionName
    StorageAccountName
    CloudConfigLocation
    PackageLocation
    Environment
    SubscriptionDataFileLocation
    PublishScriptLocation
    ServiceName

![][3]

対応する XAML は次のようになります。

    <Activity  _ />
      <x:Members>
        <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
        <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
        <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
        <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
        <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
        <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
        <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
        <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
        <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
        <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
        <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
        <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
        <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
        <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
        <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
        <x:Property Name="GetVersion" Type="InArgument(x:String)" />
        <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
        <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
        <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
        <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
        <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
        <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
        <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
        <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
        <x:Property Name="Environment" Type="InArgument(x:String)" />
        <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
        <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
        <x:Property Name="ServiceName" Type="InArgument(x:String)" />
      </x:Members>
    
      <this:Process.MSBuildArguments>

6.  [エージェントで実行] の最後に新しいシーケンスを追加します。

    1.  最初に、有効なスクリプト ファイルの有無をチェックする If ステートメント アクティビティを
        追加します。 条件を次の値に設定します。

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  If ステートメントの Then ケースに、新しい Sequence アクティビティを
        追加します。 表示名を "Start publish" に設定します。

    3.  Start publish シーケンスが選択されている状態で、
        次の新しい変数を、ワークフロー デザイナーの変数タブに
        別の行項目として追加します。 すべての変数について、
        変数の型として [文字列]、スコープとして [Start publish] を設定する必要があります。 これらは、
        ビルド定義のパラメーターを、発行スクリプトを呼び出す
        ワークフローに送るために使用されます。

        -   SubscriptionDataFilePath、[文字列] 型

        -   PublishScriptFilePath、[文字列] 型

            ![][4]

    4.  TFS 2012 以前を使用している場合は、新しいシーケンスの先頭に ConvertWorkspaceItem アクティビティを
        追加します。 TFS 2013 以降を使用している場合は、新しいシーケンスの先頭に GetLocalPath アクティビティを追加します。 、ConvertWorkspaceItem に対してプロパティを次のように設定します。 方向 = ServerToLocal、DisplayName ='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace')。 GetLocalPath アクティビティに対して、IncomingPath プロパティを 'PublishScriptLocation' に設定し、Result プロパティを 'PublishScriptFilePath' に設定します。 この
        アクティビティは、TFS サーバーの場所 (該当する場合) からの発行スクリプトへのパスを
        標準ローカル ディスク パスに変換します。

    5.  TFS 2012 以前を使用している場合は、新しいシーケンスの末尾に ConvertWorkspaceItem アクティビティをもう 1 つ
        追加します。 (Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        結果 = 'SubscriptionDataFilePath'、Workspace = 'Workspace' です。 TFS 2013 以降を使用している場合は、GetLocalPath アクティビティをもう 1 つ追加します (IncomingPath='SubscriptionDataFileLocation'、Result='SubscriptionDataFilePath.')。

    6.  InvokeProcess アクティビティを新しいシーケンスの末尾に追加します。
        このアクティビティは、ビルド定義から渡された引数を使用して
        PowerShell.exe を呼び出します。

        1.  引数 = String.Format ("-""{0}""- serviceName \{1\} ファイル
            -storageAccountName \{2\} - packageLocation""\{3\}""
            -cloudConfigLocation""\{4\}""- subscriptionDataFile""\{5\}""
            -selectedSubscription \{6\}-環境""\{7\}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName = Execute publish script

        3.  FileName = "PowerShell" (引用符を含みます)

        4.  OutputEncoding=
            System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  **標準出力の処理** ] セクションのテキスト ボックスの
        InvokeProcess では、"data"をテキスト ボックスの値を設定します。 これは、
        標準出力データを格納する変数です。

    8.  WriteBuildMessage アクティビティを追加すぐ下、 **標準出力の処理**
        」セクションを参照してください。 重要度として
        'Microsoft.teamfoundation.build.client.buildmessageimportance.high'
        を設定し、メッセージとして 'data' を設定します。 これにより、スクリプトの標準出力が
        ビルド出力に書き込まれます。

    9.  **エラー出力の処理** ] セクションのテキスト ボックスの
        InvokeProcess では、"data"をテキスト ボックスの値を設定します。 これは、
        標準エラーのデータを格納する変数です。

    10. WriteBuildError アクティビティを追加すぐ下、 **エラー出力の処理**
        」セクションを参照してください。 メッセージとして "data" を設定します。 これにより、スクリプトの標準エラーが
        ビルド エラー出力に書き込まれます。

    11. 青色の感嘆符で示されたエラーを修正します。 感嘆符の上にポインターを置くと、
        エラーに関するヒントが表示されます。 ワークフローを保存して
        エラーをクリアします。

    ワークフローの発行アクティビティの最終的な結果は、
    デザイナーで次のように表示されます。

    ![][5]

    ワークフローの発行アクティビティの最終的な結果は、
    XAML で次のように表示されます。

        <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
            <If.Then>
              <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
                <Sequence.Variables>
                  <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                  <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                </Sequence.Variables>
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                  <mtbwa:InvokeProcess.ErrorDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.ErrorDataReceived>
                  <mtbwa:InvokeProcess.OutputDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.OutputDataReceived>
                </mtbwa:InvokeProcess>
              </Sequence>
            </If.Then>
          </If>
        </Sequence>

7.  ビルド プロセス テンプレート ワークフローを保存し、このファイルをチェックインします。

8.  ビルド定義を編集します (既に開いている場合は閉じます)。
    **新規** のプロセス テンプレートの一覧に新しいテンプレートがまだ表示されない場合のボタンをクリックします。

9.  [その他] セクションのパラメーター プロパティ値を次のように設定します。

    1.  CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'
        * この値の派生元:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
        *この値の派生元: ($PublishDir)($ProjectName) .cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'
        *ここで、適切なクラウド サービス名を使用します*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'
        *ここで、適切なストレージ アカウント名を使用します*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][6]

10. 変更内容をビルド定義に保存します。

11. パッケージのビルドと発行の両方を実行するために、ビルドをキューに配置します。 Visual Studio が
    [継続的インテグレーション] に設定している場合、
    チェックインのたびにこの動作が実行されます。

### PublishCloudService.ps1 スクリプト テンプレート

```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## 次のステップ

継続的な配信を使用する場合は、リモート デバッグを有効にするには、「 [継続的な配信を使用して Azure に発行するときに、リモート デバッグ有効にする](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md)します。


[continuous delivery to azure by using visual studio team services]: cloud-services-continuous-delivery-use-vso.md 
[team foundation build service]: https://msdn.microsoft.com/library/ee259687.aspx 
[.net framework 4]: https://www.microsoft.com/download/details.aspx?id=17851 
[.net framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653 
[.net framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643 
[scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx 
[deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx 
[azure powershell cmdlets]: powershell-install-configure.md 
[the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0 
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png 
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png 
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png 
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png 
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png 
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png 

