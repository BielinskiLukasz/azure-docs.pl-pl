<properties
    pageTitle="VM の可用性管理 | Microsoft Azure"
    description="複数の仮想マシンを使って Azure アプリケーションの高い可用性を確保する方法について説明します。"
    services="virtual-machines"
    documentationCenter=""
    authors="kenazk"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/23/2015"
    ms.author="kenazk"/>

# 仮想マシンの可用性管理

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 計画済み、または計画外メンテナンスについて理解する
仮想マシンの可用性に影響を与える可能性のある Microsoft Azure プラットフォーム イベントには、計画的なメンテナンスと計画外のメンテナンスの 2 種類があります。

- **計画済みメンテナンス イベント** に対するマイクロソフトによって基になる Azure プラットフォーム全体の信頼性、パフォーマンス、および仮想マシンで実行しているプラットフォーム インフラストラクチャのセキュリティを向上させるために定期的に更新されします。 これらの更新のほとんどは、仮想マシンやクラウド サービスに影響を及ぼすことなく実行されます。 ただし、プラットフォーム インフラストラクチャに必要な更新を適用するため、仮想マシンの再起動が求められる場合もあります。

- **計画外メンテナンス イベント** ハードウェアまたは仮想マシンの基になる物理インフラストラクチャが何らかの方法で障害が発生したときに発生します。 こうした不具合には、ネットワーク障害、ローカル ディスク障害、またはその他のラック レベルでの障害が挙げられます。 そのような障害が検知されると、Azure プラットフォームは、仮想マシンをホストしている異常な物理マシンから正常な物理マシンへと、仮想マシンを自動的に移行します。 このような例が発生することはまれですが、この場合も仮想マシンの再起動が求められる場合があります。

## ベスト プラクティスに基づいて高可用性のアプリケーションを設計する
前述のようなイベントが 1 つ以上発生した場合にダウンタイムの影響を低減するため、下記のような高可用性のためのベスト プラクティスを仮想マシンに適用することをお勧めします。

* [冗長性実現のための可用性セットには、複数の仮想マシンを構成する]
* [に対して別々 の可用性セットを各アプリケーション層を構成する]
* [ロード バランサーと可用性セットを結合]
* [可用性セットで仮想マシンが 1 つの回避]

### 冗長性実現のために複数の仮想マシンを可用性セット内に構成する
アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 このような構成により、計画済み、または計画外メンテナンス イベント中に、少なくとも 1 つの仮想マシンが利用可能となり、99.95% の Azure SLA を満たします。 サービス レベル契約の詳細については、「クラウド サービス、仮想マシンと仮想ネットワーク」のセクションを参照してください。 [サービス レベル アグリーメント](../../../support/legal/sla/)します。

基盤を成す Azure プラットフォームにより、可用性セット内の各仮想マシンに更新ドメイン (UD) と障害ドメイン (FD) が割り当てられます。 所定の可用性セットに対して、同時に再起動される仮想マシンのグループと物理ハードウェアを示す UD (ユーザーによる構成不可) が 5 つ割り当てられます。 1 つの可用性セット内に 5 つ以上の仮想マシンが構成されているとき、6 つ目の仮想マシンは 1 つ目の仮想マシンと同じ UD 内に配置され、7 つ目は 2 つ目の仮想マシンと同じ UD 内に配置されるという方法で処理されます。 計画済みメンテナンス中、UD の再起動は順番に処理されない場合がありますが、一度に再起動される UD は 1 つのみです。

FD は電源とネットワーク スイッチを共有する仮想マシンのグループを定義します。 既定では、可用性セット内に構成された仮想マシンは、2 つの FD に分けられます。 仮想マシンを可用性セットに配置しても、アプリケーションがオペレーティング システムやアプリケーションの障害から保護されるわけではありませんが、潜在的な物理ハードウェア障害、ネットワーク障害、または電力の中断の影響を低下させることができます。

<!--Image reference-->
   ![UD FD configuration](./media/virtual-machines-manage-availability/ud-fd-configuration.png)

>[AZURE.NOTE] 手順については、の [仮想マシンの可用性セットの構成方法] を参照してください。

### 各アプリケーション層に対して別々の可用性セットを構成する
可用性セット内の仮想マシンがすべてほぼ同一で、アプリケーションに対する役割が同じである場合は、アプリケーションの各層に対して別々の可用性セットを構成することをお勧めします。  同じ可用性セットの中に 2 つの異なる層を配置した場合、同じアプリケーション層内にあるすべての仮想マシンを一度に再起動できます。 各層に対する別々の可用性セット内に少なくとも 2 つの仮想マシンを構成することで、各層あたり 1 つ以上の仮想マシンの可用性が確保されます。

たとえば、ISS、Apache、Nginx などを実行しているアプリケーションのフロントエンド内のすべての仮想マシンを、1 つの可用性セットに配置します。 このとき、フロントエンド仮想マシンのみが同じ可用性セット内に配置されるようにします。 同様に、別の可用性セットには、複製された SQL Server 仮想マシンまたは MySQL 仮想マシンのように、データ層の仮想マシンのみが配置されるようにします。

<!--Image reference-->
   ![Application tiers](./media/virtual-machines-manage-availability/application-tiers.png)


### ロード バランサーと可用性セットを結合する
Azure ロード バランサーと可用性セットを結合することで、アプリケーションの復元性を最大化できます。 Azure ロード バランサーは、複数の仮想マシンにトラフィックを振り分けます。 当社の標準層の仮想マシンには Azure ロード バランサーが含まれています。 すべての仮想マシン層に Azure Load Balancer が含まれているわけではないことに注意してください。 仮想マシンの負荷分散に関する詳細については、「 [仮想マシンの負荷分散](../load-balance-virtual-machines.md)します。

複数の仮想マシン間でトラフィックを分散するためのロード バランサーが構成されていない場合、計画済みメンテナンス イベントによって、トラフィックを提供している仮想マシンのみに影響が生じ、アプリケーション層の機能停止が生じます。 同じ層の複数の仮想マシンを、同じロード バランサーと可用性セット以下に配置することで、少なくとも 1 つのインスタンスによってトラフィックの提供を継続することができます。

### 可用性セット内の仮想マシンが 1 つのみにならないようにする
可用性セット内の仮想マシンが 1 つのみにならないようにしてくだい。 この構成の仮想マシンは、SLA 保証の対象とはならず、Azure の計画されたメンテナンス イベント時にダウンタイムが発生します。 可用性セット内の仮想マシンのインスタンスが 1 つのみの場合、複数インスタンスの仮想マシン用に計画されたメンテナンスを通知する詳細な電子メールも受信することに注意してください。 

<!-- Link references -->
[Configure multiple virtual machines in an Availability Set for redundancy]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configure each application tier into separate Availability Sets]: #configure-each-application-tier-into-separate-availability-sets
[Combine the Load Balancer with Availability Sets]: #combine-the-load-balancer-with-availability-sets
[Avoid single instance virtual machines in Availability Sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[How to Configure An Availability Set for virtual machines]: virtual-machines-how-to-configure-availability.md

