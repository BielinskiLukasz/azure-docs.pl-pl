## リソース マネージャー テンプレート向けの PowerShell の設定

リソース マネージャーで Azure PowerShell を使用する前に、適切なバージョンの Windows PowerShell と Azure PowerShell を用意する必要があります。

### PowerShell のバージョンを確認する

Windows PowerShell Version 3.0 または 4.0 があることを確認します。 Windows PowerShell のバージョンを確認するには、Windows PowerShell コマンド プロンプトで次のコマンドを入力します。

    $PSVersionTable

次の種類の情報が表示されます。

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

**PSVersion** の値が 3.0 または 4.0 であることを確認します。 ない場合を参照してください。 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)します。

### Azure アカウントとサブスクリプションを設定する

アクティブ化する Azure サブスクリプションがない場合、 [MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) サインアップ、または、 [無料評価版](http://azure.microsoft.com/pricing/free-trial/)します。

Azure PowerShell コマンド プロンプトを開き、次のコマンドで Azure にログオンします。

    Login-AzureRmAccount

Azure サブスクリプションが複数ある場合は、次のコマンドで、Azure サブスクリプションの一覧を表示できます。

    Get-AzureRmSubscription

次の種類の情報が表示されます。

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Azure PowerShell コマンド プロンプトで次のコマンドを実行して、現在の Azure サブスクリプションを設定します。置換を含む引用符内のすべての < と > 適切な名前の文字。

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Azure サブスクリプションとアカウントの詳細については、次を参照してください。 [方法: サブスクリプションへの接続](powershell-install-configure.md#Connect)します。





