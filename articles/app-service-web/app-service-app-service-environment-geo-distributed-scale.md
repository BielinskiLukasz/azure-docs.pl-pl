<properties 
    pageTitle="App Service 環境を使用した geo 分散スケール" 
    description="Traffic Manager および App Service 環境による geo 分散を使用してアプリを水平方向にスケールする方法について説明します。" 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/08/2015" 
    ms.author="stefsch"/>   


# App Service 環境を使用した geo 分散スケール

## 概要

きわめて高いスケールを必要とするアプリケーション シナリオでは、単一のアプリ デプロイメントで使用できるコンピューティング リソース容量では足りないことがあります。 投票アプリケーション、スポーツ イベント、テレビ放送される娯楽イベントは、いずれも非常に高いスケールを必要とするシナリオの例です。 高スケール要件を満たすには、極端に負荷の大きい要件に対応できるように、単一のリージョン内、または複数のリージョンにわたって、複数のアプリ デプロイメントを使用してアプリを水平方向に拡張する必要があります。

App Service 環境は、水平方向のスケールアウトに最適なプラットフォームです。 既知の要求レートをサポートできる App Service 環境構成を選択していれば、開発者は追加の App Service 環境を "ひな型" 方式でデプロイして、必要なピーク時負荷容量を確保できます。

たとえば、App Service 環境構成で実行されるアプリが 20K RPS (1 秒あたりの要求数) を処理することがテストでわかっているとします。 必要なピーク時負荷容量が 100K RPS である場合、5 つの App Service 環境を作成して、予想される最大負荷をアプリケーションが処理できるように構成できます。

顧客は通常、カスタム ドメイン (またはバニティ ドメイン) を使用してアクセスするため、開発者は App Service 環境のすべてのインスタンスにアプリの要求を分散する方法が必要になります。 これを実現する優れた方法は、カスタム ドメインを使用して、解決するのには、 [Azure Traffic Manager プロファイル ][azuretrafficmanagerprofile]します。 Traffic Manager プロファイルは、個々の App Service 環境をすべてポイントするように構成できます。 すべての App Service 環境を対象とした顧客の分散は、Traffic Manager が Traffic Manager プロファイルの負荷分散設定に基づいて自動的に処理します。 このアプローチは、すべての App Service 環境が単一の Azure リージョンにデプロイされている場合はもちろん、世界の複数の Azure リージョンに展開されている場合でも、問題なく機能します。

さらに、顧客はバニティ ドメインを使用してアプリにアクセスするため、アプリを実行する App Service 環境の数を意識することがありません。 これにより、開発者は、観測されたトラフィック負荷に基づいて、App Service 環境の追加や削除を迅速かつ簡単に実行できます。

次の概念図は、単一リージョン内の 3 つの App Service 環境で水平方向にスケールアウトされたアプリを示しています。

![Conceptual Architecture][conceptualarchitecture]

このトピックの残りの部分では、複数の App Service 環境を使用してサンプル アプリの分散トポロジを設定するのに必要な手順について説明します。

## トポロジを計画する

分散アプリケーションのフットプリントを構築する前に、いくつかの情報を前もって用意しておくと作業がスムーズになります。

- **アプリのカスタム ドメインの管理:**  お客様は、アプリケーションにアクセスを使用してカスタム ドメイン名は何ですか。 サンプル アプリでは、カスタム ドメイン名は *www.scalableasedemo.com* です。
- **Traffic Manager ドメイン:**  ドメイン名を作成するときに選択する必要があります、 [Azure Traffic Manager プロファイル ][azuretrafficmanagerprofile]します。 この名前は、Traffic Manager が管理するドメイン エントリを登録する際に、*trafficmanager.net* サフィックスと組み合わされます。 サンプル アプリでは、選択される名前は *scalable-ase-demo* です。 そのため、Traffic Manager で管理される完全なドメイン名は、*scalable-ase-demo.trafficmanager.net* になります。
- **アプリケーションのフット プリントを拡張するための戦略:**  アプリケーションのフット プリントを単一のリージョンでの複数の App Service 環境間で分散されますか。 複数のリージョンなのか、 両方のアプローチの最適な組み合わせなのか。 この決定は、顧客のトラフィックが発生する場所に加えて、アプリをサポートするバックエンド インフラストラクチャの他の要素のスケーラビリティに関する期待事項に基づく必要があります。 たとえば、100% のステートレスなアプリケーションでは、アプリ大規模にスケールできます App Service 環境の複数の Azure リージョンにまたがってデプロイで乗算の Azure リージョンごとの複数の App Service 環境の組み合わせを使用しています。 選択できるパブリック Azure リージョンは 15 以上あるため、顧客はスケーラビリティのきわめて高いアプリケーション フットプリントを世界規模で構築できます。 この記事のサンプル アプリでは、単一の Azure リージョン (米国中南部) に 3 つの App Service 環境が作成されています。
- **App Service 環境の名前付け規則:**  各 App Service 環境には、一意の名前が必要です。 1 つや 2 つではなく数の多い App Service 環境では、各 App Service 環境を識別しやすい命名規則があると便利です。 サンプル アプリでは、シンプルな命名規則が使用されています。 3 つの App Service 環境の名前は *fe1ase*、*fe2ase*、*fe3ase* です。
- **、アプリの名前付け規則:**  、配置されたアプリの各インスタンスの名前が必要なため、アプリケーションの複数のインスタンスを展開します。 App Service 環境のあまり知られていない便利な特長の 1 つですが、同じアプリ名を複数の App Service 環境で使用できます。 App Service 環境ごとに一意のドメイン サフィックスがあるため、開発者は各環境でまったく同じアプリ名を再利用できます。 たとえば、開発者は次のようにという名前のアプリケーションがある:  *myapp.foo1.p.azurewebsites.net*, 、*myapp.foo2.p.azurewebsites.net*, 、*myapp.foo3.p.azurewebsites.net*, など.ただし、サンプル アプリでは、各アプリ インスタンスにも一意の名前を設定しています。 使用されているアプリ インスタンス名は *webfrontend1*、*webfrontend2*、*webfrontend3* です。


## Traffic Manager プロファイルを設定する

アプリの複数のインスタンスは、複数の App Service 環境に展開される後、個々 のアプリケーション インスタンスが Traffic Manager によるを登録することができます。 サンプル アプリでは、顧客を次のデプロイ済みアプリ インスタンスのいずれかにルーティングするための *scalable-ase-demo.trafficmanager.net* 用 Traffic Manager プロファイルが必要です。

- **webfrontend1.fe1ase.p.azurewebsites.net:**  最初の App Service 環境に展開されているサンプル アプリケーションのインスタンス。
- **webfrontend2.fe2ase.p.azurewebsites.net:**  2 つ目の App Service 環境に展開されているサンプル アプリケーションのインスタンス。
- **webfrontend3.fe3ase.p.azurewebsites.net:**  3 つ目の App Service 環境に展開されているサンプル アプリケーションのインスタンス。

Azure App Service、複数のエンドポイントで実行中のすべてを登録する最も簡単な方法、 **同じ** プレビュー Powershell での Azure リージョンは、 [Traffic Manager の Azure リソース マネージャー (ARM) のサポート ][armtrafficmanager]します。

最初の手順では、Azure Traffic Manager プロファイルの作成です。 次のコードでは、サンプル アプリ用プロファイルの作成方法を示しています。

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

*RelativeDnsName* パラメーターが *scalable-ase-demo* に設定されていることに注意してください。 ここでは、ドメイン名として *scalable-ase-demo.trafficmanager.net* が作成され、Traffic Manager プロファイルに関連付けられています。

*TrafficRoutingMethod* パラメーターは負荷分散ポリシーを定義しており、Traffic Manager はこのポリシーを使用して、顧客の負荷を利用可能なすべてのエンドポイントに分散する方法を判断します。 この例では、*重み付け*方式が選択されています。 この方式では、顧客の要求が、各エンドポイントに関連付けられている相対的な重みに基づいて、登録済みのすべてのアプリケーション エンドポイント間で分散されます。

作成されるプロファイルに対して、各アプリ インスタンスが*外部エンドポイント*として追加されます。 次のコードは、プロファイルに追加される 3 つのアプリ インスタンスの各 URL を示しています。

    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend1.fe1ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend2.fe2ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend3.fe3ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

アプリ インスタンスごとに *Add-AzureTrafficManagerEndpointConfig* が　1 回ずつ呼び出されている点に注意してください。 各 Powershell コマンドの*Target* パラメーターは、デプロイされた 3 つのアプリ インスタンスの完全修飾ドメイン名 (FQDN) を指しています。 これらの FQDN を使用して *scalable-ase-demo.trafficmanager.net* の DNS CNAME チェーンを処理し、トラフィックの負荷を Traffic Manager プロファイルに登録されているすべてのエンドポイント間で分散させます。

3 つのエンドポイントすべてで *Weight* (重み) パラメーターに同じ値 (10) が使用されています。 これにより、顧客の要求が Traffic Manager によって 3 つのアプリケーションのすべてのインスタンス間で比較的均等に分散されます。

*注:*  ARM トラフィック マネージャーのためのサポートは現在プレビュー段階で、Azure App Service のエンドポイントを設定する必要がある、 *型* パラメーターを *ExternalEndpoints*します。 将来的に、Azure App Service エンドポイントは、ARM 版の Traffic Manager によってエンドポイントの種類の 1 つとしてネイティブでサポートされます。

## アプリのカスタム ドメインが Traffic Manager ドメインをポイントするように設定する

最後の必須手順として、アプリのカスタム ドメインが Traffic Manager ドメインをポイントするように設定します。 サンプル アプリでは、*www.scalableasedemo.com* を *scalable-ase-demo.trafficmanager.net* にポイントさせています。 この手順は、カスタム ドメインを管理するドメイン レジストラーを通じて実行する必要があります。

利用しているレジストラーのドメイン管理ツールを使用して、カスタム ドメインが Traffic Manager ドメインをポイントする CNAME レコードを作成します。 次の図に、この CNAME の構成の例を示します。

![CNAME for Custom Domain][cnameforcustomdomain]

このトピックでは説明しませんが、アプリ インスタンスごとにカスタム ドメインを登録する必要があることに注意してください。 これを忘れた場合、要求がアプリ インスタンスに到達したときに、アプリケーションにカスタム ドメインが登録されていないと、要求は失敗します。

この例では、カスタム ドメインは *www.scalableasedemo.com* で、各アプリケーション インスタンスにカスタム ドメインが関連付けられています。

![カスタム ドメイン][customdomain]

用の Azure App Service アプリでカスタム ドメインの登録の要約で、次の記事を参照してください。 [[registercustomdomain] のカスタム ドメインを登録する][registercustomdomain]します。

## 分散トポロジを試す

Traffic Manager と DNS を構成すると、最終的に、*www.scalableasedemo.com* への要求は次の順で処理されます。

1. ブラウザーまたはデバイスで *www.scalableasedemo.com* の DNS 参照が実行されます。
2. ドメイン レジストラーの CNAME エントリによって、DNS 参照が Azure Traffic Manager にリダイレクトされます。
3. Azure Traffic Manager の DNS サーバーのいずれかに対して、*scalable-ase-demo.trafficmanager.net* の DNS 参照が実行されます。
4. Traffic Manager が、負荷分散ポリシー (前半で Traffic Manager プロファイルを作成するときに使用した *TrafficRoutingMethod* パラメーター) に基づいて、構成済みのエンドポイントのいずれかを選択し、ブラウザーまたはデバイスにそのエンドポイントの FQDN を返します。
5.  エンドポイントの FQDN は App Service 環境で実行されているアプリ インスタンスの URL であるため、ブラウザーまたはデバイスは FQDN を IP アドレスに解決するよう Microsoft Azure の DNS サーバーに要請します。
6. ブラウザーまたはデバイスはその IP アドレスに HTTP/S 要求を送信します。
7. 要求は、App Service 環境のいずれかで実行されているアプリ インスタンスのいずれかに到達します。

次のコンソールのスクリーンショットは、サンプル アプリのカスタム ドメインの DNS 参照が、3 つあるサンプル App Service 環境の 1 つ (この場合は、3 つの App Service 環境の 2 つ目) で実行されているアプリ インスタンスに正しく解決されたところを示しています。

![DNS Lookup][dnslookup]

## その他のリンクおよび情報

プレビューの Powershell に関するドキュメント [Traffic Manager の Azure リソース マネージャー (ARM) のサポート ][armtrafficmanager]します。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]




[azuretrafficmanagerprofile]: https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/ 
[armtrafficmanager]: https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/ 
[registercustomdomain]: https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/ 
[conceptualarchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png 
[cnameforcustomdomain]: ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png 
[dnslookup]: ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png 
[customdomain]: ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 

