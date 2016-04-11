<properties
   pageTitle="Azure DNS の概要 | Microsoft Azure"
   description="Azure DNS の DNS ゾーンを作成する方法について説明します。これは、PowerShell を使用して DNS ドメインのホストを開始するための最初の DNS ゾーンを作成する手順です。"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Powershell で Azure DNS の使用を開始する


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [PowerShell](dns-getstarted-create-dnszone.md)

ドメイン "contoso.com" には、"mail.contoso.com" (メール サーバー用) や "www.contoso.com" (Web サイト用) など、多数の DNS レコードが含まれている場合があります。  DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。<BR><BR>
ドメインのホストを開始するには、まず DNS ゾーンを作成する必要があります。 特定のドメイン用に作成された DNS レコードは、そのドメインの DNS ゾーン内に含まれます。<BR><BR>
以下の手順では、Microsoft Azure PowerShell を使用します。  Azure DNS コマンドレットを使用するために、必ず最新の Azure PowerShell に更新してください。 Microsoft Azure コマンド ライン インターフェイス、REST API または SDK を使用しても、同じ手順を実行できます。<BR><BR>

Azure PowerShell を使用して Azure DNS を管理するには、次の手順を完了しておく必要があります。


Azure DNS では、Azure リソース マネージャー (ARM) を使用します。 Azure PowerShell 1.0.0 以降の場合は次の手順を実行します。 詳細については、「 [リソース マネージャーで Windows Powershell を使用して](powershell-azure-resource-manager.md)います。<BR><BR>


### 手順 1.
Azure アカウントにログインする (資格情報を使用して認証を行うよう求められます)

    PS C:\> Login-AzureRmAccount

### 手順 2.
使用する Azure サブスクリプションを選択する

    PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

'Get-AzureRmSubscription' を使用して利用可能なサブスクリプションを一覧表示できます。

### 手順 3.
新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    PS C:\> New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

### 手順 4.
Azure DNS サービスは Microsoft.Network リソース プロバイダーによって管理されます。 Azure DNS を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。 この操作は、サブスクリプションごとに 1 回だけ実行します。

    PS c:\> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network



## Etag とタグ

### Etag
たとえば、2 人のユーザーまたは 2 つのプロセスが同時に DNS レコードを変更しようとするとします。  どちらの変更が優先されるでしょうか。  また、優先された側は、他のユーザーまたはプロセスによって行われた変更を上書きしたことに気付くのでしょうか。<BR><BR>
Azure DNS は、Etag を使用して同じリソースへの同時変更を安全に処理します。 各 DNS リソース (ゾーンまたはレコード セット) には、関連付けられている Etag があります。  リソースが取得されるときは、常に Etag も取得されます。  リソースを更新する場合は、Azure DNS がサーバー上の Etag の一致を確認できるように Etag を返すこともできます。  リソースを更新するたびに Etag が再生成されるため、Etag の不一致は同時変更が発生していることを示します。  Etag は、既存のリソースがないことを確認するために、新しいリソースの作成時にも使用されます。

Azure DNS PowerShell は、既定で、ゾーンおよびレコード セットへの同時変更のブロックに Etag を使用します。  オプションの "-Overwrite" スイッチを使用すると、Etag チェックを実行しないように設定できます。この場合、発生したすべての同時変更が上書きされます。<BR><BR>
Azure DNS REST API のレベルでは、HTTP ヘッダーを使用して Etag を指定します。  次の表に各ヘッダーの動作を示します。

|ヘッダー|動作|
|------|--------|
|なし|PUT は常に成功します (Etag チェックなし)。|
|If-match <etag>|PUT は、リソースが存在し、Etag が一致する場合にのみ成功します|
|If-match * |PUT はリソースが存在する場合にのみ成功します|
|If-none-match |* PUT は、リソースが存在しない場合にのみ成功します。|

### タグ
タグは Etag とは異なります。 タグは名前と値のペアのリストであり、Azure リソース マネージャーでは、課金またはグループ化のためのリソースのラベル付けに使用されます。 タグの詳細については、タグを使用した Azure リソースの整理に関するページを参照してください。
Azure DNS PowerShell では、ゾーンとレコード セットの両方でタグをサポートしており、オプションの "-Tag" パラメーターを使用して指定します。  次の例では、'project = demo' と 'env = test' の 2 つのタグを持つ DNS ゾーンの作成方法を示します。

    PS C:\> New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## DNS ゾーンの作成

DNS ゾーンは、New-AzureRmDnsZone コマンドレットを使用して作成します。 次の例では、"MyResourceGroup" というリソース グループに "contoso.com" という DNS ゾーンを作成します。<BR>

    PS C:\> New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

>[AZURE.NOTE] Azure dns を付けないでゾーン名を指定する '.' です。  たとえば、"contoso.com." ではなく "contoso.com" にします。<BR>


これで、Azure DNS に DNS ゾーンが作成されました。  DNS ゾーンを作成すると、次の DNS レコードも作成されます。<BR>



- 'Start of Authority' (SOA) レコード。  このレコードは、すべての DNS ゾーンのルートに存在します。
- 権威ネーム サーバー (NS) レコード。  このレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。  Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。  参照してください [Azure DNS へのドメインの委任](dns-domain-delegation.md) の詳細。<BR>

これらのレコードを表示するには、Get-AzureRmDnsRecordSet を使用します。

    PS C:\> Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}

>[AZURE.NOTE] DNS ゾーンの使用のルート (または '頂点') にあるレコード セット"@"、レコード セット名として。


最初の DNS ゾーンが作成されたため、アプリケーションをテストである nslookup などの DNS ツールを使用してを調べるか、 [Resolve-dnsname PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)します。<BR>

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する必要があります。 Get AzureRmDnsRecordSet 上記で一覧表示される、ゾーンのネーム サーバーが、NS レコードで指定されたする必要があります、代わりに、ゾーンは、次のコマンドを適切な値です。<BR>

        C:\> nslookup
        > set type=SOA
        > server ns1-01.azure-dns.com
        > contoso.com

        Server: ns1-01.azure-dns.com
        Address:  208.76.47.1

        contoso.com
                primary name server = ns1-01.azure-dns.com
                responsible mail addr = msnhst.microsoft.com
                serial  = 1
                refresh = 900 (15 mins)
                retry   = 300 (5 mins)
                expire  = 604800 (7 days)
                default TTL = 300 (5 mins)

## 次のステップ

[レコード セットとレコードの作成の概要します。](dns-getstarted-create-recordset.md)<BR>
[DNS ゾーンの管理方法](dns-operations-dnszones.md)<BR>
[DNS レコードの管理方法](dns-operations-recordsets.md)<BR>
[.NET SDK を使用した Azure の操作を自動化します。](dns-sdk.md)<BR>
[Azure DNS REST API リファレンス](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 


