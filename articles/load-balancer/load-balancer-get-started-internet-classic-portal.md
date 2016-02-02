<properties 
   pageTitle ="インターネットへの Azure クラシック ポータルを使用して従来のデプロイ モデル内のロード バランサーの作成の概要 |Microsoft Azure]
   description =「インターネットへの Azure クラシック ポータルを使用して従来のデプロイ モデル内のロード バランサーを作成する方法について説明します」
   サービス =「ロード バランサー」
   documentationCenter ="na"
   authors ="joaoma"
   manager ="carmonm"
   エディター =""
   タグ =「azure サービス管理」
/>
<tags  
   ms.service=「ロード バランサー」
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload=「インフラストラクチャ サービス」
   ms.date="12/07/2015"
   ms.author="joaoma"/>

# Azure クラシック ポータルを使用したインターネットに接続するロード バランサー (クラシック) の作成の開始

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] この記事では、従来のデプロイ モデルについて説明します。 こともできます [インターネットへの Azure リソース マネージャーを使用してロード バランサーを作成する方法について](load-balancer-get-started-internet-arm-ps.md)します。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## インターネットに接続するロード バランサーを仮想マシンに設定する

クラウド サービスの仮想マシン全体でインターネットからのネットワーク トラフィックを負荷分散するには、負荷分散セットを作成する必要があります。 この手順では、既に仮想マシンが作成されていて、これらがすべて同じクラウド サービス内にあることを前提としています。

**仮想マシンの負荷分散セットを構成するには**

1. Azure クラシック ポータルで、**[Virtual Machines]** をクリックし、負荷分散セットの仮想マシンの名前をクリックします。
2.  **[エンドポイント]** をクリックし、**[追加]** をクリックします。

4.  **[仮想マシンにエンドポイントを追加します]** ページで、右矢印をクリックします。

4.  **[エンドポイントの詳細を指定します]** ページで、次のように指定します。
    - **[名前]** にエンドポイントの名前を入力するか、共通プロトコルにあらかじめ定義されているエンドポイントの一覧から選択します。
    -  **[プロトコル]** で、エンドポイントの種類に応じて必要となるプロトコル (TCP または UDP) を選択します。
    -  **[パブリック ポート] と [プライベート ポート]** に、仮想マシンで使用するポート番号を必要に応じて入力します。 仮想マシンのプライベート ポートとファイアウォール ルールを使って、アプリケーションに適した方法でトラフィックをリダイレクトすることができます。 プライベート ポートはパブリック ポートと同じにできます。 たとえば、Web (HTTP) トラフィック用のエンドポイントの場合、パブリック ポートとプライベート ポートの両方にポート 80 を割り当てることができます。

5.  **[負荷分散セットの作成]** を選択し、右矢印をクリックします。

6.  **[負荷分散セットの構成]** ページで、負荷分散セットの名前を入力し、Azure Load Balancer のプローブ動作の値を割り当てます。
Load Balancer はプローブを使用して、負荷分散セット内の仮想マシンが着信トラフィックを受信できるかどうかを判断します。

7.  チェック マークをクリックして、負荷分散されたエンドポイントを作成します。 仮想マシンの **[エンドポイント]** ページの **[負荷分散セット名]** 列に **[はい]** が表示されます。

8.  ポータルで、**[Virtual Machines]**、負荷分散セットの追加の仮想マシンの名前、**[エンドポイント]**、**[追加]** の順にクリックします。

9.  **[仮想マシンにエンドポイントを追加します]** ページで、**[既存の負荷分散セットにエンドポイントを追加する]** をクリックし、負荷分散セットの名前を選択して、右矢印をクリックします。

10. **[エンドポイントの詳細を指定します]** ページで、エンドポイントの名前を入力し、チェック マークをクリックします。
負荷分散セットに仮想マシンを追加する場合は、手順 8. ～ 10. を繰り返します。



## 次のステップ

[内部ロード バランサーの構成の開始します。](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードを構成します。](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定を構成します。](load-balancer-tcp-idle-timeout.md)






