<properties 
   pageTitle="Azure Government イメージ ギャラリー" 
   description="この記事では、Azure Government イメージ ギャラリーと、含まれるイメージの概要について説明します。" 
   services="Azure-Government" 
   documentationCenter="" 
   authors="joharve2" 
   manager="chrisnie" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government" 
   ms.date="05/20/2015"
   ms.author="jharve"/>



# Microsoft Azure Government イメージ ギャラリー

<p> Microsoft Azure Government イメージ ギャラリーは、パブリックの Azure ポータルへのようなエクスペリエンスです。Microsoft や Microsoft のパートナーが事前に構築したイメージのデプロイを選択し、自身の VHD にアップロードできます。これによって、必要に応じて自身のニーズに応じて標準化したイメージを自由にデプロイできるようになります。

下の図は Azure Government イメージ ギャラリーで利用できるイメージのリストを示しています。 事前に構成されたイメージの内いくつかでは、特定のソフトウェアのライセンスに対する支払いも生じます。 参照してください <a href="http://azure.microsoft.com/pricing/details/virtual-machines/">仮想マシンの料金</a> ページの詳しいガイダンスについては、Microsoft アカウント チームや Azure Government 特有の料金について再販業者を使用します。


## Azure Government イメージ ギャラリー

 発行元| イメージ名| 説明| ディスク サイズ
---|---|---|---
 Barracuda Networks, Inc.| Barracuda Web Application Firewall (WAF) 7.8 PatchLevel 4| 注: このアプライアンスは Web UI で管理され、TCP/8000 で転送される必要があります。詳細については、デプロイの README https://cloudvm.cudasvc.com/azure/deployment-readme-waf.html を参照して...| Linux| 50 GB
 Barracuda Networks, Inc.| Barracuda NG Firewall 5.4.3-182 PatchLevel 4| 注: このアプライアンスはクライアント アプリケーションで管理され、TCP/807 で転送される必要があります。詳細については、デプロイの README https://cloudvm.cudasvc.com/azure/deployment-readme-ng.html を参照して...| Linux| 80 GB
 Bitnami| Drupal 7| Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの 1 つです。次の...| Linux| 30 GB
 Bitnami| Drupal 6| Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの 1 つです。次の...| Linux| 30 GB
 Bitnami| Ruby Stack 2.0| Bitnami RubyStack は、Rails アプリケーションにおける Ruby の開発やデプロイの手間を大幅に削減します。Ruby on Rails は、データベースを備えた Web アプリケーション向けのフルスタックの MVC フレームワークで...| Linux| 30 GB
 Bitnami| Nginx Stack 1.6| Bitnami Nginx Stack は、包括的で、完全に統合された、すぐに使用できる PHP、MySQL、Nginx の開発環境です。さらに、phpMyAdmin、SQLite、ImageMagick、FastCGI,、Memcache、GD...| Linux| 30 GB
 Bitnami| Drupal 8| Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの 1 つです。次の...| Linux| 30 GB
 Bitnami| Ruby Stack 2.1| Bitnami RubyStack は、Rails アプリケーションにおける Ruby の開発やデプロイの手間を大幅に削減します。Ruby on Rails は、データベースを備えた Web アプリケーション向けのフルスタックの MVC フレームワークで...| Linux| 30 GB
 Bitnami| LAMP Stack 5.6| Bitnami LAMPStack は、PHP アプリケーションの開発やデプロイの手間を大幅に削減します。すぐに使用できる Apache、MySQL、PHP、phpMyAdmin、他のソフトウェアが含まれ...| Linux| 30 GB
 Bitnami| LAMP Stack 5.4| Bitnami LAMPStack は、PHP アプリケーションの開発やデプロイの手間を大幅に削減します。すぐに使用できる Apache、MySQL、PHP、phpMyAdmin、他のソフトウェアが含まれ...| Linux| 30 GB
 CloudLink| CloudLink SecureVM| CloudLink® SecureVM は、仮想マシンのボリュームの暗号化と VM イメージの整合性の検証による起動前の承認を提供します。これにより、仮想マシンを起動するタイミングと場所を制御できます。SecureVM は、ネイティブの Windows のロックを解除...| Linux| 30 GB
 Microsoft Open Technologies, Inc.| Oracle Database 12c Standard Edition on Windows Server 2012| Oracle Database (http://www.oracle.com/database) 12c Standard Edition (12.1.0.1.0) は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。最小の...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Windows Server 2012 R2 の JDK 7| Java プラットフォーム (http://www.oracle.com/java) の Standard Edition 7 (update 71) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Windows Server 2012 R2 の JDK 8| Java プラットフォーム (http://www.oracle.com/java) の Standard Edition 8 (update 25) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2| Oracle Database (http://www.oracle.com/database) 11g R2 Enterprise Edition (11.2.0.4.0) は、きわめて負荷の高いトランザクション処理の管理を容易にする包括的な機能や...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2| Oracle WebLogic Server (http://www.oracle.com/weblogicserver) 11g Standard Edition (10.3.6) は、あらゆる規模の企業向けの優れた Java アプリケーション サーバーで、開発者に...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2| Oracle WebLogic Server (http://www.oracle.com/weblogicserver) 11g Enterprise Edition (10.3.6) は最新のデータ センター向けの優れた Java アプリケーション サーバーです。最新の機能を...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle WebLogic Server 12c Standard Edition on Windows Server 2012| Oracle WebLogic Server (http://www.oracle.com/weblogicserver) 12c Standard Edition (12.1.2.0) は優れた Java EE アプリケーション サーバーで、次世代アプリケーションをミッション クリティカルな...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012| Oracle WebLogic Server (http://www.oracle.com/weblogicserver) 12c Enterprise Edition (12.1.2.0) は優れた Java EE アプリケーション サーバーで、次世代アプリケーションをミッション クリティカルな...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012| Oracle Database (http://www.oracle.com/database) 12c Standard Edition (12.1.0.1.0) は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。Oracle WebLogic...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012| Oracle Database (http://www.oracle.com/database) 12c Enterprise Edition (12.1.0.1.0) は、クラウド向けの次世代データベースで、新しいマルチテナント アーキテクチャを...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Windows Server 2012 の JDK 6| Java プラットフォーム (http://www.oracle.com/java) の Standard Edition 6 (update 85) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle Database 12c Enterprise Edition on Windows Server 2012| Oracle Database (http://www.oracle.com/database) 12c Enterprise Edition (12.1.0.1.0) は、クラウド向けの次世代データベースで、新しいマルチテナント アーキテクチャを...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle Database 11g R2 と WebLogic Server 11g Standard Edition on Windows Server 2008 R2| Oracle Database (http://www.oracle.com/database) 11g R2 Standard Edition (11.2.0.4.0) は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。Oracle WebLo...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle Database 11g R2 と WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2| Oracle Database (http://www.oracle.com/database) 11g R2 Enterprise Edition (11.2.0.4.0) は、きわめて負荷の高いトランザクション処理の管理を容易にする包括的な機能や...| Windows| 128 GB
 Microsoft Open Technologies, Inc.| Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2| Oracle Database (http://www.oracle.com/database) 11g R2 Standard Edition (11.2.0.4.0) は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。最小の...| Windows| 128 GB
 Microsoft SQL Server グループ| SQL Server 2014 RTM Enterprise on Windows Server 2012 R2| このイメージには、SQL Server の完全バージョンが含まれています。A3 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成されています。構成には CEIP を有効に...| Windows| 127 GB
 Microsoft SQL Server グループ| SQL Server 2012 SP2 Enterprise on Windows Server 2012| このイメージには、SQL Server の完全バージョンが含まれています。一部の SQL Server コンポーネントでは、使用前ｊに設定や構成を追加する必要があります。A3 以上のサイズの仮想マシンを使用することをお勧めします。...| Windows| 127 GB
 Microsoft SQL Server グループ| SQL Server 2014 RTM Enterprise on Windows Server 2012 R2| このイメージには、SQL Server の完全バージョンが含まれています。A3 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成されています。構成には CEIP を有効に...| Windows| 127 GB
 Microsoft SQL Server グループ| SQL Server 2014 RTM Standard on Windows Server 2012 R2| このイメージには、SQL Server の完全バージョンが含まれています。A2 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成されています。構成には CEIP を有効に...| Windows| 127 GB
 Microsoft SQL Server グループ| SQL Server 2012 SP2 Standard on Windows Server 2012| 一部の SQL Server コンポーネントでは、使用前ｊに設定や構成を追加する必要があります。A2 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成され...| Windows| 127 GB
 Microsoft SQL Server グループ| SQL Server 2014 RTM Standard on Windows Server 2012 R2| このイメージには、SQL Server の完全バージョンが含まれています。A2 以上のサイズの仮想マシンを使用することをお勧めします。このイメージは、Azure 向けに事前に構成されています。構成には CEIP を有効に...| Windows| 127 GB
 Microsoft Windows Server グループ| Windows Server 2012 R2 Datacenter、2014 年 6 月| Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 Datacenter、2014 年 6 月| Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2008 R2 SP1、2014 年 6 月| Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2008 R2 SP1、2014 年 9 月| Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2008 R2 SP1、2014 年 10 月| Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2008 R2 SP1、2014 年 11 月| Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2008 R2 SP1、2015 年 4 月| Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。また...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 Datacenter、2014 年 9 月| Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 Datacenter、2014 年 11 月| Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 Datacenter、2014 年 10 月| Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 R2 Datacenter、2014 年 9 月| Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 R2 Datacenter、2014 年 10 月| Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 R2 Datacenter、2014 年 11 月| Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 R2 Datacenter、2015 年 4 月| Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。そのパフォーマンスは企業規模の...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server Technical Preview| Windows Server は Microsoft Cloud Platform の中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのデータセンターのインフラストラクチャにお届けします。Windows S...| Windows| 128 GB
 Microsoft Windows Server グループ| Windows Server 2012 Datacenter、2015 年 4 月| Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。スケーラブルでダイナミック、マルチ テナント...| Windows| 128 GB
 Oracle| Oracle WebLogic Server 12.1.2 on Oracle Linux 6.4.0.0.0| Oracle WebLogic Server 12c Enterprise Edition は優れた Java EE アプリケーション サーバーで、次世代アプリケーションをミッション クリティカルなクラウド プラットフォームで提供し、ネイティブなクラウド管理、...| Linux| 30 GB
 Oracle| Oracle Linux 7.0.0.0.0| Oracle Linux 7.0.0.0 は、エンタープライズ アプリケーションおよびシステムに対して、きわめて高いパフォーマンス、スケーラビリティ、信頼性を提供します。エンタープライズのワークロードに最適化されている Oracle Linux は、...| Linux| 30 GB
 Oracle| Oracle Database 12.1.0.1 Enterprise Edition on Oracle Linux 6.4.0.0.0| Oracle Database 12c Enterprise Edition は、クラウド向けの次世代データベースで、新しいマルチテナント アーキテクチャを、速度、拡張性、信頼性、安全性に優れたデータベース プラットフォームで提供...| Linux| 40 GB
 Oracle| Oracle Database 12.1.0.1 Standard Edition on Oracle Linux 6.4.0.0.0| Oracle Database 12c Standard Edition は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。詳細については、http://www.oracle.com/database を参照してください。| Linux| 40 GB
 SUSE| SUSE Linux Enterprise Server 11 SP3| Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます...| Linux| 30 GB
 SUSE| SUSE Linux Enterprise Server 11 SP3 (Premium Image)| Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます...| Linux| 30 GB
 SUSE| SUSE Linux Enterprise Server 12 (Premium Image)| Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます...| Linux| 30 GB
 SUSE| SUSE Linux Enterprise Server 12| Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます...| Linux| 30 GB



## <a name="next"></a>次のステップ

イメージ ギャラリーからのデプロイについて、また VHD の作成についての詳細は、次をご覧ください。 参照してください、 <a href="../azure-government-developer-guide">Azure Government 開発者向けガイド</a> Azure Government 使用時にエンドポイントを持つプログラムでの相違点を明らかにするためです。

### その他のリソース:

- [Windows 仮想マシンを展開します。](../virtual-machines/virtual-machines-windows-tutorial.md)

- [Linux 仮想マシンを展開します。](../virtual-machines/virtual-machines-linux-tutorial.md)

- [仮想マシンに関する FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

- [作成し、独自の VHD をアップロード](../virtual-machines-create-upload-vhd-windows-server/)

- [Linux VM イメージをキャプチャします。](../virtual-machines/virtual-machines-linux-capture-image.md)










[1]: ./media/azure-government-developer-guide/publisherguide.png 
[2]: ./media/azure-government-overview/azure-gov-overview.jpg 
[link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md 
[link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md 
[link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md 

