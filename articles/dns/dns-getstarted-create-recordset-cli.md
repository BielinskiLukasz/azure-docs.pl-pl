<properties
   pageTitle="CLI を使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Azure"
   description="Azure DNS のホスト レコードを作成する方法。CLI を使用したレコード セットとレコードの設定"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="Adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="joaoma"/>


# CLI を使用して DNS レコードを作成する

> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-recordset-cli.md)
- [PowerShell](dns-getstarted-create-recordset.md)



DNS ゾーンを作成した後は、ドメインの DNS レコードを追加する必要があります。  そのために、まずは DNS レコードとレコード セットについて理解することが必要です。


## レコード セットとレコードについて
各 DNS レコードには名前と種類があります。

A _の完全修飾_ 名には、ゾーンの名前が含まれますが、 _相対_ 名がないです。  たとえば、"contoso.com" ゾーン内の相対レコード名 "www" によって、完全修飾レコード名 "www.contoso.com" が示されます。

>[AZURE.NOTE] Azure DNS レコードは相対名を使用して指定します。

レコードは、含まれるデータによってさまざまな種類があります。  最も一般的な種類は "A" レコードで、名前が IPv4 アドレスにマップされます。  また、別の種類の "MX" レコードでは、名前がメール サーバーにマップされます。

Azure DNS では、一般的な DNS レコードの種類である A、AAAA、CNAME、MX、NS、SOA、SRV、TXT をすべてサポートしています。  (なお [TXT レコードの種類を使用して、SPF レコードを作成するか](http://tools.ietf.org/html/rfc7208#section-3.1).)

場合によっては、特定の名前と種類の DNS レコードを複数作成する必要があることもあります。  たとえば、www.contoso.com という Web サイトが 2 つの異なる IP アドレスでホストされているとします。  この場合は、IP アドレスごとに異なる A レコードが、合計 2 つ必要になります。

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

これはレコード セットの例です。  レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類の DNS レコードのコレクションです。  ほとんどのレコード セットには 1 つのレコードが含まれていますが、上の例のように複数のレコードが含まれているレコード セットも珍しくはありません   (SOA および CNAME という種類のレコード セットは例外で、DNS 標準により、これらの種類では同じ名前を持つ複数のレコードが許可されません)。

Time-to-Live (TTL) は、各レコードが再度照会されるまでクライアントによってキャッシュされる期間を指定します。  上の例では、TTL は 3600 秒、つまり 1 時間です。  TTL は、各レコードではなく、レコード セットに対して指定されるため、そのレコード セット内のすべてのレコードに同じ値が使用されます。

>[AZURE.NOTE] Azure DNS は、レコード セットを使用して DNS レコードを管理します。



## レコード セットとレコードの作成 

次の例では、レコード セットとレコードの作成方法を示します。  その他のレコードの種類を参照して、'A' の DNS レコードの種類を使用します [DNS レコードを管理する方法](dns-operations-recordsets-cli.md)


### 手順 1.

レコード セットを作成します。

    Usage: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

レコード セットには、DNS ゾーン "contoso.com" の相対名 "www" が含まれているため、レコードの完全修飾名は "www.contoso.com" になります。  レコードの種類は "A" で、TTL は 60 秒です。

>[AZURE.NOTE] ゾーンの頂点のレコード セットを作成する (この場合は、"contoso.com")、レコード名を使用して"@"、引用符を含みます。 これは、一般的な DNS の規則です。

レコード セットは空で、あるレコードを新しく作成した"www"レコード セットを使用できるように追加します。<BR>

### 手順 2.

次のコマンドを使用して IPv4 A レコードを "www" レコード セットに追加します。

    Usage: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46
    

変更が完了しました。  "azure network dns-record-set show" を使用して、Azure DNS からレコード セットを取得できます。


    azure network dns record-set show myresourcegroup "contoso.com" www A
    
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "www"
    data:    Id                              : /subscriptions/########################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 300
    data:    A records:
    data:        IPv4 address                : 134.170.185.46
    data:
    info:    network dns record-set show command OK


nslookup や他の DNS ツールを使用して、新しいレコード セットをクエリすることもできます。  

>[AZURE.NOTE] ように、Azure DNS ネーム サーバーにドメインを委任されていないする場合に、ゾーンを作成するときに、ゾーンのネーム サーバー アドレスを明示的に指定する必要があります。


    C:\> nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                



## 次のステップ
[DNS ゾーンの管理方法](dns-operations-dnszones-cli.md)

[DNS レコードを管理する方法](dns-operations-recordsets-cli.md)<BR>

[.NET SDK を使用した Azure の操作の自動化](dns-sdk.md)
 

