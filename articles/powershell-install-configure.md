<properties
    pageTitle="Azure PowerShell のインストールおよび構成方法"
    description="Azure PowerShell のインストールと構成の方法について説明します。"
    editor="tysonn"
    manager="stevenka"
    documentationCenter=""
    services=""
    authors="coreyp-at-msft"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="powershell"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="coreyp"/>

# Azure PowerShell のインストールおよび構成方法#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##Azure PowerShell とは#
Azure PowerShell は、Windows PowerShell を使用して Azure を管理するためのコマンドレットを提供するモジュールです。 このコマンドレットを使用して、Azure Platform から配信されるソリューションやサービスを、作成、テスト、デプロイ、管理できます。 ほとんどの場合、コマンドレットを使用して Microsoft Azure 管理ポータルと同じタスク (クラウド サービス、仮想マシン、仮想ネットワーク、Web アプリの作成や構成など) を実行できます。

モジュールとソース コードは、公開されているリポジトリでダウンロードできます。

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Azure PowerShell 1.0.1 のソース コード](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## 手順 1: インストール
ダウンロードしてインストール [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
<a id="Connect"></a>

## 手順 2: 起動
モジュールをインストールすると、Azure PowerShell 用にカスタマイズされたコンソールもインストールされます。 コマンドレットは、標準の Windows PowerShell コンソールまたは Azure PowerShell コンソールから実行できます。

## 手順 3: 接続
コマンドレットでサービスを管理するには、サブスクリプションが必要です。 サブスクリプションがまだない場合は、Azure サブスクリプションを購入できます。 手順については、次を参照してください。 [Azure の購入方法](http://go.microsoft.com/fwlink/p/?LinkId=320795)します。

1. 型 **Add-azureaccount**

2. アカウントに関連付けられている電子メール アドレスとパスワードを入力します。 Azure により資格情報が認証および保存され、ウィンドウが閉じます。

または

職場または学校のアカウントにサインインします。

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

> [AZURE.NOTE] この非対話型のログイン方法は、職場または学校のアカウントでのみ機能します。 職場または学校のアカウントは、職場または学校によって管理されているユーザーで、職場または学校の Azure Active Directory インスタンスで定義されています。 現在、職場または学校のアカウントがなく、Microsoft アカウントを使用して Azure サブスクリプションにログインしている場合は、次の手順を使用して簡単に職場または学校のアカウントを作成できます。

> 1. ログイン、 [Azure 管理ポータル](https://manage.windowsazure.com), の [ **Active Directory**します。

> 2. ディレクトリが存在しない場合は、選択 **ディレクトリの作成** し、必要な情報を提供します。

> 3. ディレクトリを選択し、新しいユーザーを追加します。 この新しいユーザーは、職場または学校のアカウントを使用してサインインできます。 ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。 この情報は、以降の手順 5. で使用するため保管しておいてください。

> 4. 管理ポータルで、次のように選択します。 **設定** し、[ **管理者**します。 選択 **追加**, 、共同管理者として新しいユーザーを追加します。 これにより、職場または学校のアカウントで Azure サブスクリプションを管理できるようになります。

> 5. 最後に、Azure ポータルからログアウトし、職場または学校のアカウントを使用してログインし直します。 初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。

> 職場または学校のアカウントを使用して Microsoft Azure のサインアップの詳細については、次を参照してください。 [組織としての Microsoft Azure へのサインアップ](sign-up-organization.md)します。

### アカウントおよびサブスクリプションの詳細を表示する

Azure PowerShell で使用するためのアカウントとサブスクリプションは、複数所有することができます。 実行して、複数のアカウントを追加する **Add-azureaccount** 複数回です。

利用可能な Azure アカウントを表示する **Get-azureaccount**します。

Azure サブスクリプションを表示する **Get-azuresubscription**します。

## 手順 4: テスト<a id="Ex"></a>


モジュールをインストールし、サブスクリプションに接続するようにコンピューターを構成したら、Azure Web アプリを作成して、すべてが機能していることを確認できます。

1. Azure PowerShell コンソールを開きます。

2. Web アプリの名前を入力します。 DNS の名前付け規則に従った名前を付けてください。 名前には「a」から「z」の文字、「0」から「9」の数字、ハイフン「-」のみが使用できます。

    Web アプリの名前は、Azure 内で一意である必要があります。 この例では「mySite」を使用しますが、アカウント名に数字をプラスした名前などの別の名前を必ず指定してください。  

    名前を決めたら、次のようにコマンドを入力します。 "mySite" は、Web アプリの名前に置き換えてください。

        New-AzureWebsite mySite

    このコマンドレットは Web アプリを作成し、新しい Web アプリを表すオブジェクトを返します。 オブジェクトのプロパティには、Web アプリに関する有用な情報が含まれています。

3. Web アプリに関する情報を取得するには、このコマンドを入力します。 このコマンドは、先ほど作成したばかりのものも含む、サブスクリプション内のすべての Web アプリについての簡単な情報を返します。

        Get-AzureWebsite

4. Web アプリに関する詳しい情報を取得するには、コマンドに Web アプリ名を含めます。 "mySite" を Web アプリ名で置き換えてください。

        Get-AzureWebsite -Name mySite

5. Web アプリは、作成すると開始されます。 Web アプリを停止するには、Web アプリ名を含めてこのコマンドを入力します。

        Stop-AzureWebsite -Name mySite

6. サイトが停止状態 (stopped) であることを確認するには、Get-AzureWebsite コマンドをもう一度実行します。

        Get-AzureWebsite

7. このテストを完了するために、Web アプリを削除します。 次のコマンドを入力します。  

        Remove-AzureWebsite -Name mySite

7. タスクを完了するために、Web アプリが削除されていることを確認します。

        Get-AzureWebsite -Name mySite

##<a id="Help"></a>ヘルプの表示##

これらのリソースは、特定のコマンドレットに関するヘルプ情報を提供します。


-   コンソールから、組み込みのヘルプ システムを使用できます。  **Get-help** コマンドレットは、このシステムへのアクセスを提供します。 



- Azure PowerShell モジュールの参照情報は、Azure ライブラリからも利用できます。 詳細については、次を参照してください。 [Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn708514.aspx)します。

コミュニティの情報を参照する場合は、以下の人気フォーラムもご覧ください。

- [MSDN 上の Azure フォーラム]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

