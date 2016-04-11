<properties
    pageTitle="Azure のハイブリッド クラウド テスト環境 | Microsoft Azure"
    description="Azure ベースのハイブリッド クラウドに対する開発/テストまたは概念実証を行う IT プロフェッショナル向けの環境を構築する方法が説明されている記事を示します。"
    documentationCenter=""
    services="virtual-machines"
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="Windows"
    ms.devlang="na"
    ms.topic="index-page"
    ms.date="09/16/2015"
    ms.author="josephd"/>

# Azure ハイブリッド クラウド テスト環境

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


開発/テスト環境または概念実証のハイブリッド クラウド テスト環境向けに、ローカルのインターネット接続といずれかのパブリック IP アドレスを使用して、実用的なクロスプレミス Azure Virtual Network (VNet) を段階的に設定します。 設定が完了したら、アプリケーションの開発とテストの実行や、簡略化された IT ワークロードでの実験ができます。また、サイト間の仮想プライベート ネットワーク (VPN) 接続のパフォーマンスをインターネット上の場所を基準に測定することができます。

## ハイブリッド クラウドの基本構成

 [ハイブリッド クラウドの基本構成](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) で構成されます。

- 4 つの仮想マシン (ドメイン コントローラー、アプリケーション サーバー、クライアント コンピューター、および Windows サーバーを実行してルーティングとリモート アクセスを行う VPN デバイス) を備えた、簡略化されたオンプレス ネットワーク
- レプリカ ドメイン コントローラーを備えた Azure 仮想ネットワーク
- サイト間 VPN 接続

## ハイブリッド クラウドでの SharePoint イントラネット ファーム

 [ハイブリッド クラウド テスト環境での SharePoint イントラネット ファーム](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) 、SQL Server 2014 サーバーと SharePoint Server 2013 のサーバーをハイブリッド クラウドの基本構成に追加します。 これによって、簡略化されたオンプレミス ネットワーク上のクライアント コンピューターからアクセスできる、2 層の SharePoint ファームが作成されます。

## ハイブリッド クラウドでの Web ベースの基幹業務 (LOB) アプリケーション

 [ハイブリッド クラウド テスト環境で web ベース LOB アプリケーション](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md) ハイブリッド クラウドの基本構成に SQL Server 2014 サーバーとインターネット インフォメーション サービス (IIS) サーバーを追加します。 これによって、Web ベースの階層型 LOB アプリケーションをデプロイしてテストできるインフラストラクチャが作成されます。

## ハイブリッド クラウドでの Office 365 ディレクトリ同期 (DirSync) サーバー

 [ハイブリッド クラウド テスト環境での Office 365 DirSync サーバー](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md) DirSync サーバーをハイブリッド クラウドの基本構成に追加し、評価版の Office 365 サブスクリプションに対してパスワード同期と Office 365 DirSync をデモンストレーションします。

## シミュレートされたハイブリッド クラウド テスト環境

組織や個人をインターネットに直接接続やパブリック IP アドレスがないすぐに使用できる、 [シミュレートされたハイブリッド クラウド テスト環境](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) 簡略化されたオンプレミス ネットワークを別の Azure 仮想ネットワークを構築して、VNet の VPN 接続で 2 つの仮想ネットワークを接続します。


## その他のリソース

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

[Azure インフラストラクチャ サービスのワークロード: 高可用な基幹業務アプリケーション](virtual-machines-workload-high-availability-LOB-application.md)

[Microsoft Azure での Office 365 ディレクトリ同期 (DirSync) のデプロイ](https://technet.microsoft.com/library/dn635310.aspx)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

