<properties 
   pageTitle="CLI を使用した DNS ゾーンに対する操作 | Microsoft Azure" 
   description="Azure CLI を使用して DNS ゾーンを管理できます。 Azure DNS の DNS ゾーンを更新、削除、および作成する方法" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/10/2015"
   ms.author="joaoma"/>

# CLI を使用して DNS ゾーンを管理する方法

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)

このガイドでは、DNS ゾーンの管理方法を説明します。 このガイドは、DNS ゾーンを管理するために実行する一連の操作を理解するのに役立ちます。

>[AZURE.NOTE] Azure DNS は、Azure リソース マネージャー専用サービスです。  ASM API はありません。  したがって、"azure config mode arm" コマンドを使用して、リソース マネージャー モードを使用するように Azure CLI を構成するようにする必要があります。

>"エラー: 'dns' は、Azure のコマンドではありません" と表示される場合は、多くの場合、リソース マネージャー モードではなく、ASM モードで Azure CLI を使用していることが原因です。
 
## 新しい DNS ゾーンの作成

ドメインをホストする新しい DNS ゾーンを作成するには、`azure network dns zone create` を使用します。

    azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

この操作は、Azure DNS に新しい DNS ゾーンを作成します。 参照先の Azure リソース マネージャーのタグの配列を指定することができます必要に応じて [Etag とタグ](dns-getstarted-create-dnszone.md#Etags-and-tags)します。

ゾーンの名前がリソース グループ内で一意であること、および作成するゾーンがまだ存在していないことが必要です。そうでない場合、操作は失敗します。

同じゾーン名は、別のリソース グループまたは別の Azure サブスクリプション内で再利用できます。  複数のゾーンで同じ名前を共有できますが、各インスタンスには異なるネーム サーバー アドレスが割り当てられます。また、親ドメインから委任できるインスタンスは 1 つだけです。 参照してください [Azure DNS へのドメインの委任](dns-domain-delegation.md) の詳細。

## DNS ゾーンの取得

DNS ゾーンを取得するには、`azure network dns zone show` を使用します。

    azure network dns zone show myresourcegroup contoso.com

この操作は、DNS ゾーンとその ID、レコード セットの数、タグを返します。


## DNS ゾーンの一覧表示

リソース グループ内の DNS ゾーンを取得するには、`azure network dns zone list` を使用します。

    azure network dns zone list myresourcegroup


## DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、`azure network dns zone set` を使用して行うことができます。  これは更新するゾーン内で DNS レコード セット (を参照してください [DNS レコードの管理方法](dns-operations-recordsets.md))。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 現時点では、この操作は、ゾーンのリソースの Azure リソース マネージャーの "タグ" に限定されています。 参照してください [Etag とタグ](dns-getstarted-create-dnszone.md#Etags-and-tags) の詳細。

    azure network dns zone set myresourcegroup contoso.com -t prod=value2

## DNS ゾーンの削除

DNS ゾーンは、`azure network dns zone delete` を使用して削除できます。
 
Azure DNS の DNS ゾーンを削除する前に、ゾーンの作成時に自動的にゾーンのルートに作成された NS レコードと SOA レコードを除くすべてのレコード セットを削除する必要があります。  

    azure network dns zone delete myresourcegroup contoso.com 

この操作では、オプションの "-q" スイッチが使用されます。これにより、DNS ゾーンを削除するかどうかの確認メッセージは表示されなくなります。


## 次のステップ


[DNS レコードの管理](dns-operations-recordsets-cli.md)

[.NET SDK を使用した操作の自動化](dns-sdk.md) 

