Azure レプリカをホストする各 VM に負荷分散されたエンドポイントを作成する必要があります。 複数のリージョンでレプリカがある場合は、そのリージョンの各レプリカが同じ VNet 内の同じクラウド サービスにある必要があります。 複数の Azure リージョンにまたがる可用性グループ レプリカを作成するには、複数の VNet を構成する必要があります。 クロス vnet 間の接続を構成する方法については、次を参照してください。  [VNet 間の接続を構成する](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)します。

1. Azure ポータルで、レプリカをホストする各 VM に移動し、詳細を表示します。

1. 各 VM の **[エンドポイント]** タブをクリックします。

1. 使用しようとしているリスナー エンドポイントの **[名前]** と **[パブリック ポート]** が未使用であることを確認します。 次の例では、名前が "MyEndpoint" で、ポートが “1433” とします。

1. ローカル クライアントでダウンロードしてインストール [最新の PowerShell モジュール](http://azure.microsoft.com/downloads/)します。

1. **Azure PowerShell** を起動します。 Azure 管理モジュールが読み込まれた状態で新しい PowerShell セッションが開きます。

1. **Get-AzurePublishSettingsFile**を実行します。 このコマンドレットにより、ブラウザーで発行設定ファイルをローカル ディレクトリにダウンロードするよう指示されます。 Azure サブスクリプションのログイン資格情報の入力を求められる場合があります。

1. ダウンロードした発行設定ファイルのパスを指定して、**Import-AzurePublishSettingsFile** コマンドを実行します。

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    発行設定ファイルがインポートされたら、PowerShell セッションで、Azure サブスクリプションを管理できます。




