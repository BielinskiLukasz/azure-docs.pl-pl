---
title: Omówienie usługi App Service — Azure | Microsoft Docs
description: Dowiedz się, w jaki sposób usługa Azure App Service ułatwia tworzenie i hostowanie aplikacji internetowych
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5f3fed9255b86696b0e71c70adcd7ff23c0573d9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352448"
---
# <a name="app-service-overview"></a>Omówienie usługi App Service

*Azure App Service* to usługa do hostowania aplikacji internetowych, interfejsów API REST i zapleczy aplikacji mobilnych. Możesz programować w ulubionym języku — .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python. Aplikacje można łatwo uruchamiać i skalować w środowiskach opartych zarówno na systemie Windows, jak i Linux. W przypadku środowisk opartych na systemie Linux zobacz usługę App Service w artykule [Usługa App Service w systemie Linux](containers/app-service-linux-intro.md). 

Usługa App Service nie tylko dodaje do aplikacji możliwości platformy Microsoft Azure, takie jak zabezpieczenia, równoważenie obciążenia, automatyczne skalowanie i automatyczne zarządzanie. Umożliwia również korzystanie z funkcji metodyki DevOps, takich jak ciągłe wdrażanie z usług Azure DevOps, GitHub, Docker Hub i innych źródeł, zarządzanie pakietami, środowiska przejściowe, domena niestandardowa i certyfikaty SSL. 

Korzystając z usługi App Service, płacisz tylko za faktycznie wykorzystane zasoby obliczeniowe platformy Azure. Używane zasoby obliczeniowe są określane przez _Plan usługi App Service_, w ramach którego uruchamiasz swoje aplikacje. Aby uzyskać więcej informacji, zobacz [Omówienie planów usługi App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Dlaczego warto korzystać z usługi App Service?

Poniżej przedstawiono kilka najważniejszych funkcji usługi App Service:

* **Wiele języków i platform** — usługa App Service zapewnia najwyższej jakości obsługę środowisk ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP i Python. Można również uruchamiać [program PowerShell oraz inne skrypty lub pliki wykonywalne](webjobs-create.md) jako usługi w tle.
* **Optymalizacja metodyki DevOps** — konfigurowanie [ciągłej integracji i wdrażania](deploy-continuous-deployment.md) za pomocą usług Azure DevOps, GitHub, BitBucket, Docker Hub lub Azure Container Registry. Promowanie aktualizacji za pośrednictwem [środowisk testowych i przejściowych](deploy-staging-slots.md). Zarządzaj aplikacjami w usłudze App Service za pomocą programu [Azure PowerShell](/powershell/azureps-cmdlets-docs) lub [międzyplatformowego interfejsu wiersza polecenia](/cli/azure/install-azure-cli).
* **Globalne skalowanie i wysoka dostępność** — ręczne lub automatyczne skalowanie [w pionie](web-sites-scale.md) lub [w poziomie](../monitoring-and-diagnostics/insights-how-to-scale.md). Hostuj aplikacje w dowolnym miejscu globalnej infrastruktury centrum danych firmy Microsoft i korzystaj z wysokiej dostępności usługi App Service zapewnianej w umowie [SLA](https://azure.microsoft.com/support/legal/sla/app-service/).
* **Połączenia z platformami SaaS i danymi lokalnymi** — możliwość wyboru spośród ponad 50 [łączników](../connectors/apis-list.md) obsługujących systemy dla przedsiębiorstw (takie jak SAP), usługi SaaS (takie jak Salesforce) oraz serwisy internetowe (takie jak Facebook). Dostęp do danych lokalnych przy użyciu [połączeń hybrydowych](app-service-hybrid-connections.md) i [sieci wirtualnych platformy Azure](web-sites-integrate-with-vnet.md).
* **Bezpieczeństwo i zgodność** — usługa App Service jest [zgodna ze standardami ISO, SOC i PCI](https://www.microsoft.com/en-us/trustcenter). Uwierzytelnianie użytkowników za pomocą [usługi Azure Active Directory](configure-authentication-provider-aad.md) lub danych logowania z serwisów społecznościowych ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) i [ Microsoft](configure-authentication-provider-microsoft.md)). Utwórz [ograniczenia adresów IP](app-service-ip-restrictions.md) i [zarządzaj tożsamościami usługi](overview-managed-identity.md).
* **Szablony aplikacji** — możliwość wyboru z obszernej listy szablonów aplikacji dostępnych w portalu [Azure Marketplace](https://azure.microsoft.com/marketplace/), takich jak WordPress, Joomla i Drupal.
* **Integracja z programem Visual Studio** —dedykowane narzędzia w programie Visual Studio usprawniają procesy tworzenia, wdrażania i debugowania.
* **Interfejs API i funkcje mobilne** — usługa App Service zapewnia gotową do użycia obsługę mechanizmu CORS w scenariuszach interfejsów API RESTful i upraszcza scenariusze aplikacji mobilnych dzięki włączeniu uwierzytelniania, synchronizacji danych w trybie offline, powiadomień push itd.
* **Kod niewymagający serwera** — możliwość uruchamiania fragmentów kodu lub skryptów na żądanie bez konieczności jawnego aprowizowania infrastruktury i zarządzania nią, dzięki czemu płacisz tylko za czas obliczeniowy faktycznie używany przez kod (zobacz [Usługa Azure Functions](/azure/azure-functions/)).

Oprócz usługi App Service platforma Azure oferuje inne usługi, które mogą służyć do hostingu witryn i aplikacji internetowych. W przypadku większości scenariuszy najlepszym wyborem jest usługa App Service.  W przypadku architektury mikrousług należy rozważyć usługę [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). W razie konieczności sprawowania większej kontroli nad maszynami wirtualnymi, na których działa kod, należy wziąć pod uwagę usługę [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Aby uzyskać więcej informacji o tym, jak dokonać wyboru między usługami Azure, zobacz [Azure App Service, Virtual Machines, Service Fabric, and Cloud Services comparison](overview-compare.md) (Porównanie usług Azure App Service, Virtual Machines, Service Fabric i Cloud Services).

## <a name="next-steps"></a>Następne kroki

Utwórz swoją pierwszą aplikację internetową.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (w systemie Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (w systemie Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
