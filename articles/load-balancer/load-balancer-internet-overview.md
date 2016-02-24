
<properties 
   pageTitle="インターネットに接続するロード バランサーの概要 |Microsoft Azure "
   description="インターネットに接続するロード バランサーとその機能の概要 ロード バランサーが仮想マシンとクラウド サービスを使用して Azure で機能する方法"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/16/2015"
   ms.author="joaoma" />


# 複数の Virtual Machines またはサービス間でインターネットに接続するロード バランサー

エンドポイントの 1 つの使用方法は、Azure Load Balancer を構成して、複数の仮想マシンまたはサービス間で特定の種類のトラフィックを分散することです。 たとえば、複数の Web サーバーまたは Web ロール間で Web 要求のトラフィックの負荷を分散できます。

Azure Load Balancer は、着信トラフィックのパブリック IP アドレスとポート番号を仮想マシンのプライベート IP アドレスとポート番号にマップし、仮想マシンからの応答トラフィックはその逆にマップします。

>[AZURE.NOTE] Azure ロード バランサーは、既定の設定を使用して複数の仮想マシン インスタンス間でハッシュ配布ネットワーク トラフィック (でハッシュ分散方法の詳細について [ロード バランサーの機能](load-balancer-overview.md#load-balancer-features) です。 セッション アフィニティを探している場合はチェック アウト [ロード バランサー分散モード](load-balancer-distribution-mode.md)します。

クラウド サービスに Web ロールまたは Worker ロールのインスタンスが含まれる場合、サービス定義でパブリック エンドポイントを定義できます。
 
Servicedefinition.csdef ファイルにはエンドポイント構成が含まれ、Web ロールまたは Worker ロールのデプロイメント用の複数のロール インスタンスがある場合は、ロード バランサーをセットアップできます。 インスタンスをクラウドのデプロイメントに追加するには、サービス構成ファイル (.csfg) のインスタンス数を変更します。  

次の図は、暗号化された Web トラフィック用の負荷分散されたエンドポイントを示しています。このエンドポイントは、パブリックとプライベートの TCP ポートが 443 である 3 台の仮想マシン間で共有されています。 この 3 台の仮想マシンは、1 つの負荷分散セット内にあります。


![パブリックのロード バランサーの例](./media/load-balancer-internet-overview/IC727496.png))



複数のインターネット クライアントがクラウド サービスのパブリック IP アドレスと TCP ポート 443 に Web ページ要求を送信すると、Azure Load Balancer は、負荷分散セット内の 3 台の仮想マシン間でこれらの要求のハッシュ ベースの負荷分散を行います。 ロード バランサー アルゴリズムについての詳細を取得できます [ロード バランサーの概要] ページ](load-balancer-overview.md#load-balancer-features)します。


## 次のステップ

[内部ロード バランサーの概要](load-balancer-internal-overview.md)

[インターネットに接続するロード バランサーの構成の開始](load-balancer-internet-getstarted.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)


 

