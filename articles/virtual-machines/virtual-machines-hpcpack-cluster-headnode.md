<properties
 pageTitle="Azure VM で HPC Pack ヘッド ノードを作成する | Microsoft Azure"
 description="Azure クラシック ポータルと従来のデプロイ モデルを利用し、Azure VM で Microsoft HPC Pack ヘッド ノードを作成する方法について説明します。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Marketplace イメージを利用し、Azure VM で HPC Pack クラスターのヘッド ノードを作成する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


この記事は、使用する方法を示します、 [Microsoft HPC Pack バーチャル マシンのイメージ](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) Azure Marketplace で
クラシック (サービス管理) の配置モデルでは、Azure の Windows HPC クラスターのヘッド ノードを作成します。 ヘッド ノードは Azure virtual network の Active Directory ドメインに参加させる必要があります。 このヘッド ノードを Azure の HPC Pack の概念実証デプロイとして利用し、クラスターにコンピューティング リソースを追加して HPC ワークロードを実行できます。


![HPC Pack ヘッド ノート][headnode]

>[AZURE.NOTE] HPC Pack では現在
VM イメージは Windows Server 2012 R2 Datacenter HPC に基づいてください。
Pack 2012 R2 Update 2 が事前にインストールします。 Microsoft SQL Server 2014 Express
事前にインストールされています。


Azure で HPC Pack クラスターの運用環境のデプロイをお勧めする自動展開方法など、 [HPC Pack IaaS デプロイメント
スクリプト](virtual-machines-hpcpack-cluster-powershell-script.md) または Azure リソース マネージャー [クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)します。

## 計画に関する考慮事項

* **Active Directory ドメイン** -HPC サービスを開始する前に、HPC Pack ヘッド ノードは Azure Active Directory ドメインに参加する必要があります。 1 つの選択肢としては、別個のドメイン コントローラー/フォレストを Azure にデプロイし、それに VM を参加させます。 概念実証デプロイとして、HPC サービスを開始する前に、ヘッド ノードに作成した VM をドメイン コントローラーとして昇格できます。

* **Azure の仮想ネットワーク** - クラスター コンピューティング ノード Vm を HPC クラスターに追加する場合、またはクラスターの別のドメイン コント ローラーを作成する Azure の仮想ネットワーク (VNet) でヘッド ノードを展開する必要があります。 VNet がなくても、Azure の「バースト」ノードをクラスターに追加できます。

## ヘッド ノードの作成手順

次に、HPC の Azure VM を作成する手順の概要
ヘッド ノードをパックします。 Azure の従来の (サービス管理) デプロイ モデルでは、さまざまな Azure ツールを利用してこれらの手順を実行できます。


1. ヘッド ノード VM の VNet を作成する場合を参照してください。 [Azure ポータルを使用して仮想ネットワーク (従来のもの) を作成](../virtual-networks/virtual-networks-create-vnet-classic-pportal.md)します。

    **考慮事項**

    * 仮想ネットワークのアドレス空間とサブネットは初期設定をそのまま利用できます。

    * HPC Pack ヘッド ノードに多くのコンピューティング処理を要するインスタンス サイズ (A8 – A11) を使用する場合、あるいは後でコンピューティング リソースをクラスターに追加するとき、インスタンスを利用できるリージョンを選択します。 MPI ワークロードに A8 または A9 インスタンスを使用するとき、Azure の RDMA ネットワークで予約されているアドレス空間 (172.16.0.0/12) と仮想ネットワークのアドレス空間が重複しないことも確認します。 詳細については、次を参照してください。 [A8、A9、A10、A11 コンピューティング集中型インスタンス](virtual-machines-a8-a9-a10-a11-specs.md)します。

2. 別の VM に新しい Active Directory フォレストを作成する必要がある場合は、次を参照してください。 [Azure の仮想ネットワークで Active Directory フォレストのインストール](../active-directory/active-directory-new-forest-virtual-machine.md)します。

    **考慮事項**

    * 多くのテスト デプロイのために、Azure でドメイン コント ローラーを 1 つ作成できます。 Active Directory ドメインの高い可用性を維持するために、バックアップ用のドメイン コント ローラーを追加でデプロイできます。

    * 単純な概念実証デプロイの場合、この手順を省略し、後でヘッド ノード VM をドメイン コントローラーとして昇格できます。

3. Azure クラシック ポータルまたは Azure ポータルで、Azure Marketplace から HPC Pack 2012 R2 イメージを選択し、従来の VM を作成します。 (Azure クラシック ポータルのための手順を参照してください [ここ](virtual-machines-windows-tutorial-classic-portal.md).)

    **考慮事項**

    * A4 以上の VM サイズを選択します。

    * VNet にヘッド ノードをデプロイする場合、必ず VM 構成で VNet を指定します。

    * VM に新しいクラウド サービスを作成することをお勧めします。

4. VM を作成し、VM を実行したら、VM を既存のドメイン フォレストに参加させるか、VM で新しいドメイン フォレストを作成します。

    **考慮事項**

    * 既存のドメイン フォレストで Azure VNet で VM を作成した場合、VM に接続します。 次に、標準のサーバー マネージャーまたは Windows PowerShell ツールを使用し、それをドメイン フォレストに参加させます。 それから再起動します。

    * Azure VNet で VM が作成されていない場合、あるいは既存のドメイン フォレストのない VNet で作成された場合、ドメイン コントローラーとしてそれを昇格します。 これを行うには、VM に接続し、標準のサービス マネージャーまたは Windows PowerShell ツールを使用します。 詳細については、次を参照してください。 [新しい Windows Server 2012 Active Directory フォレストのインストール](https://technet.microsoft.com/library/jj574166.aspx)します。

5. VM が実行され、Active Directory フォレストに参加したら、ヘッド ノードで HPC Pack サービスを開始します。 これを行うには、次の手順を実行します。

    a. ローカル管理者グループに属するドメイン アカウントを利用し、VM に接続します。 たとえば、ヘッド ノード VM を作成したときに設定した管理者アカウントを使用できます。

    b. 既定のヘッド ノード構成の場合、Windows PowerShell を管理者として起動し、次を入力します。

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    HPC Pack サービスが開始するまでに数分かかることがあります。

    追加のヘッド ノード構成オプションについては、「`get-help HPCHNPrepare.ps1`」を入力します。


## 次のステップ

* これで、Windows HPC クラスターのヘッド ノードを操作できます。 次に
例では、HPC クラスター マネージャーを起動したり、操作を開始します
HPC PowerShell コマンドレット。

* [コンピューティング ノード Vm を追加](virtual-machines-hpcpack-cluster-node-manage.md) をクラスターに追加または [Azure バースト ノード](virtual-machines-hpcpack-cluster-node-burst.md) でクラウド サービスです。

* クラスターでテスト ワークロードを実行してみます。 例については、HPC Pack を参照してください。 [ファースト ステップ ガイド](https://technet.microsoft.com/library/jj884144)します。

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png


