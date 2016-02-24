<properties 
   pageTitle="サービス構成ファイルでの DNS 設定の指定 | Microsoft Azure"
   description="仮想ネットワークのサービス構成ファイルを使用してカスタム DNS 設定を指定する"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# サービス構成ファイルでの DNS 設定の指定

## DNS 要素

サービス構成ファイルでは、サービスが使用するドメイン ネーム システム (DNS) サーバーの IPv4 アドレスのリストである DnsServers 要素を指定できます。 サービス構成ファイルの設定は、ネットワーク構成ファイルの設定より優先されます。 詳細については、次を参照してください。 [Azure サービス構成スキーマ (.cscfg ファイル)](https://msdn.microsoft.com/library/azure/ee758710.aspx)します。

**NetworkConfiguration 要素**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING]  **名前** 属性、 **DnsServer** 要素は、参照名としてのみ使用します。 DNS サーバーのホスト名を表してはいません。 各 **DnsServer** 属性の値は、Microsoft Azure サブスクリプション全体にわたって一意でなければなりません。

## 関連項目

[Azure サービス構成スキーマ (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure Virtual Network の構成スキーマ](http://go.microsoft.com/fwlink/?LinkId=248093)

[ネットワーク構成ファイルを使用した Virtual Network の構成](http://go.microsoft.com/fwlink/?LinkId=248094)

[管理ポータルでの Virtual Network の設定について](http://go.microsoft.com/fwlink/?LinkId=248092)


