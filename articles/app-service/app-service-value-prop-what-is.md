<properties 
    pageTitle="Azure App Service とは" 
    description="Azure App Service が Web アプリおよびモバイル アプリを開発するために最適なプラットフォームである理由を説明します。" 
    keywords="app service, azure app service, app service cost, scale, scalable, app deployment, azure app deployment"
    services="app-service" 
    documentationCenter="" 
    authors="omarkmsft" 
    manager="dwrede" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/02/2015" 
    ms.author="omark"/>


# Azure App Service とは

Azure App Service は、任意のプラットフォームおよびデバイス向けに迅速かつ容易に Web アプリやモバイル アプリを構築するのに必要なすべてを統合する唯一のクラウド サービスです。 開発者向けに構築された App Service は、完全に管理されたプラットフォームで、組み込み DevOps、Visual Studio Team Services や GitHub との継続的インテグレーション、ステージング環境と運用環境のサポート、修正プログラム適用の自動化などの強力な機能を備えています。

Azure App Service は、Azure Websites、Azure Mobile Services、Azure Biztalk Services の既存の機能をすべて含み、さらに新しい機能を追加した単一のサービスです。 [詳細](http://azure.microsoft.com/documentation/services/app-service/) App Service とこれらの既存のサービスとの関連します。

## App Service を使用する理由

ビジネス プロセスの自動化だけでなく、Web アプリやモバイル アプリの迅速な開発にも重点を置いている Azure App Service は、単一の開発エクスペリエンスと管理エクスペリエンスを通じてエンタープライズ機能の統合セットを提供しており、次のような利点があります。

- **Web Apps と Mobile Apps の迅速な構築** - 単一のバックエンドを使用して、従業員や顧客向けに Web アプリやモバイル アプリを迅速に構築、デプロイ、および管理します。 .NET、Java、NodeJS、PHP、Python など、既存の言語スキルを使用します。 Azure Marketplace に用意されている豊富な API、コネクタ、ロジックのギャラリーへのアクセスにより、開発時間を短縮します。

- **任意のサービスへの接続とデータのロック解除** - 組み込みコネクタを使用して、Web アプリやモバイル アプリをエンタープライズ システムや SaaS に数分で接続します。 SAP、Siebel、Oracle などのエンタープライズ システムから Salesforce や Office 365 などの人気のエンタープライズ SaaS サービス、Facebook、Twitter、Dropbox などの人気のインターネット サービスまで、50 を超えるコネクタから選択できます。

- **統合をシンプルに** - シンプルなビジュアル デザイン エクスペリエンスを使用して、数分でクラウド全体のデータを統合し、ビジネス プロセスを自動化します。 標準の REST API を使用して、任意のモバイル アプリや Web アプリにロジックを簡単に統合します。 業界をリードする BizTalk プラットフォームに基づいて、高度なエンタープライズ アプリケーションの統合、EDI を使用した B2B ソリューション、ビジネス ポリシー (ルール エンジン) などを構築します。

- **他に例を見ない開発者の生産性** - DevOps 用に最適化されており、Visual Studio Team Services と Github の継続的インテグレーションがサポートされているため、インフラストラクチャを気にすることなく、短期間で集中してアプリの機能を向上させることができます。 組み込みのステージング、ロールバック、運用環境でのテスト機能を使用してアプリの更新をデプロイします。

- **エンタープライズ対応** - エンタープライズ レベルのセキュリティと管理により、安心して使用できます。 委任されたロール ベースの管理を提供します。モバイル アプリに送られるデータを簡単にセキュリティ保護して管理し、組み込みのバックアップと復元の機能を使用して資産を保護します。 App Service が PCI 準拠であることがわかっているため、信頼感があります。 専用環境と、パブリック クラウドおよびプライベート クラウド全体にデプロイする機能を使用してコントロールします。 25 年を超える Microsoft の経験とエンタープライズ サービス レベル アグリーメントの恩恵を受けられます。


## App Service の概念

以下の高レベルの概念は、App Service を使用して実現できることについて説明します。

### アプリの種類

App Service を使用すると、単一の開発エクスペリエンスで次の種類のアプリを作成することができます。

- [* * Web Apps * *](../app-service-web-overview) - 簡単に作成して、ビジネスに拡張できるミッション クリティカルな Web アプリをデプロイします。
- [* * モバイル アプリ * *](../app-service-mobile-value-prop-preview) -いつでも従業員、パートナーおよび顧客には、任意のデバイスを対象にします。
- [* * API アプリ * *](../app-service-api-apps-why-best-platform) - 簡単に構築し、クラウド Api を利用します。
- [* * ロジック アプリ * *](../app-service-logic-what-are-logic-apps) -クラウド全体のコードを記述することがなくアクセスとデータの使用を自動化します。

App Service は単一の統合サービスとして、上に挙げた種類のアプリを作成して単一のソリューションにすることを簡単にします。これにより、同じバックエンドを使用して Web クライアントとモバイル クライアントの両方を対象とするアプリを簡単に構築し、Office 365 や salesforce.com などの一般的なサービスと同様に、オンプレミスのシステムとも統合します。

### App Service プラン

App Service プランは、アプリ間で共有できる一連の機能と容量を表します。 App Service プランでは、いくつかの料金レベル (Free、Shared、Basic、Standard、および Premium など) がサポートされています。各レベルには独自の機能があります。 アプリがホストされる App Service プランを、ダウンタイムなしですばやく変更できます。 [App Service プランの詳細について](../web-sites-web-hosting-plan-overview.md)します。

## App Service の使用開始

App Service をぜひお試しください。 すぐに使用を開始しましょう。

Azure App Service なら、言語の選択と同じくらい簡単に使用できます。

[新しいアプリを作成する](http://go.microsoft.com/fwlink/?LinkId=523751) 無料ですぐです。 クレジット カードもコミットメントも不要です。面倒なことはありません。






