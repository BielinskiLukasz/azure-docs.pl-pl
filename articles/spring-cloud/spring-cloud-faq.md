---
title: Często zadawane pytania dotyczące chmury wiosennej platformy Azure | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące chmury wiosennej platformy Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: e4fbeef06ae49ffe24f84b1a12dbcdfe0a5f1fec
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278569"
---
# <a name="azure-spring-cloud-faq"></a>Azure Wiosenna — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące chmury wiosennej platformy Azure. 

## <a name="general"></a>Ogólne

### <a name="why-azure-spring-cloud"></a>Dlaczego chmura sprężynowa platformy Azure?

Chmura Wiosnowa platformy Azure udostępnia platformę jako usługę (PaaS) dla deweloperów chmury z chmurą. Chmura sprężynowa platformy Azure zarządza infrastrukturą aplikacji, dzięki czemu możesz skupić się na kodzie aplikacji i logice biznesowej. Podstawowe funkcje wbudowane w chmurę Azure wiosną obejmują: Eureka, config Server, serwer rejestru usług, przestawianie usług kompilacji, tworzenie niebieskich i zielonych wdrożeń. Ta usługa umożliwia również deweloperom powiązanie aplikacji z innymi usługami platformy Azure, takimi jak Azure Cosmos DB, Azure Database for MySQL i pamięć podręczna Azure dla Redis.

Chmura sprężynowa platformy Azure rozszerza środowisko diagnostyki aplikacji dla deweloperów i operatorów przez integrację Azure Monitor, Application Insights i Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Jakie plany usług są oferowane przez chmurę Azure ze sprężyną?

Chmura Wiosenna Azure oferuje jeden plan usługi w okresie zapoznawczym.  Wdrożenie z chmurą wiosenną zawiera 16 rdzeni vCPU i 32 gigabajtów (GB) pamięci.  Górna granica każdego wystąpienia mikrousług w ramach wdrożenia wynosi 4 vCPU rdzeni z 8 GB pamięci.

Zasób | Ilość
------- | -------
Wystąpienia aplikacji na wiosnę | 20
Łączna liczba wystąpień aplikacji na usługę Azure sprężynowego wystąpienia usługi w chmurze | 500
Wystąpienia usługi w chmurze ze sprężyną na platformie Azure na region na subskrypcję | 10
Woluminy trwałe | 10 x 50 GBytes

\* _, aby podnieść limit, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/faq/)._

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Jak bezpieczny jest chmura Wiosenna platformy Azure?

Bezpieczeństwo i ochrona prywatności należą do najważniejszych priorytetów dla klientów korzystających z platformy Azure i usługi Azure wiosny Cloud. System Azure pomaga upewnić się, że tylko klienci mają dostęp do danych, dzienników lub konfiguracji aplikacji, bezpiecznie szyfrując wszystkie te dane. Wszystkie wystąpienia usługi w chmurze Azure wiosennej są odizolowane od siebie nawzajem.

Chmura sprężynowa platformy Azure oferuje pełne zarządzanie protokołami SSL i certyfikatami.

Krytyczne poprawki zabezpieczeń dla środowisk uruchomieniowych w chmurze OpenJDK i wiosny są stosowane do chmury wiosennej platformy Azure najszybciej, jak to możliwe.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>W jakich regionach jest dostępna chmura Wiosenna platformy Azure?

Wschodnie stany USA, zachodnie stany USA 2, Europa Zachodnia i Azja Południowo-Wschodnia.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Jakie są znane ograniczenia chmury wiosennej platformy Azure?

W wersji zapoznawczej chmura sprężynowa platformy Azure ma następujące znane ograniczenia:

* `spring.application.name` zostanie przesłonięta przez nazwę aplikacji, która jest używana do tworzenia poszczególnych aplikacji.
* nie można `server.port` w pliku konfiguracji z repozytorium git. Dodanie go do pliku konfiguracji prawdopodobnie spowoduje niedostępność aplikacji z innych aplikacji lub Internetu.
* Szablony Azure Portal i Azure Resource Manager nie obsługują przekazywania pakietów aplikacji. Pakiety aplikacji można przekazywać tylko przez wdrożenie aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure.
* Aby dowiedzieć się więcej o ograniczeniach przydziałów, zobacz [jakie plany usługi są oferowane w chmurze Azure wiosennej?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Jak mogę przekazać Opinie i zgłosić problemy?

Jeśli wystąpią problemy z chmurą Azure wiosną, Utwórz [żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Aby przesłać żądanie funkcji lub przekazać opinię, przejdź do obszaru [opinii na platformie Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Tworzenie oprogramowania

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jestem deweloperem chmury w chmurze, ale nowym na platformie Azure. Co to jest najszybszy sposób, aby dowiedzieć się, jak utworzyć aplikację w chmurze ze sprężyną Azure?

Aby szybko rozpocząć pracę z chmurą wiosenną platformy Azure, postępuj zgodnie z instrukcjami w [przewodniku szybki start: uruchamianie aplikacji w chmurze ze sprężyną Azure przy użyciu Azure Portal](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Jakie środowisko uruchomieniowe języka Java obsługuje chmurę Azure?

Chmura sprężynowa platformy Azure obsługuje środowisko Java 8 i 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Gdzie mogę wyświetlić sprężynowe dzienniki i metryki aplikacji w chmurze?

Znajdź metryki na karcie Przegląd aplikacji i karcie [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Chmura sprężynowa platformy Azure obsługuje eksportowanie dzienników aplikacji i metryk w chmurze z usługą Azure Storage, centrum EventHub i [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Nazwa tabeli w Log Analytics to *AppPlatformLogsforSpring*. Aby dowiedzieć się, jak włączyć tę funkcję, zobacz [usługi diagnostyczne](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Czy chmura Wiosenna platformy Azure obsługuje śledzenie rozproszone?

Tak. Aby uzyskać więcej informacji, zobacz [Samouczek: używanie rozproszonego śledzenia w chmurze Azure wiosennej](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Jakie typy zasobów obsługuje usługa powiązania usługi?

Obecnie są obsługiwane trzy usługi: Azure Cosmos DB, Azure Database for MySQL i pamięć podręczna platformy Azure dla Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Czy mogę wyświetlać, dodawać i przenosić woluminy trwałe z wewnątrz aplikacji?

Tak.

## <a name="deployment"></a>Wdrażanie

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Czy usługa Azure Wiosenna Cloud obsługuje wdrożenie Blue-Green?
Tak. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska przejściowego](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Czy mogę uzyskać dostęp do programu Kubernetes w celu manipulowania kontenerami aplikacji?

Nie.  Chmura ze sprężyną platformy Azure jest abstrakcyjna dla deweloperów z podstawowej architektury, co pozwala skoncentrować się na kodzie aplikacji i logice biznesowej.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Czy chmura sprężynowa platformy Azure obsługuje tworzenie kontenerów ze źródła?

Tak. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji w chmurze wiosny z poziomu kodu źródłowego](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Czy chmura sprężynowa platformy Azure obsługuje skalowanie automatyczne w wystąpieniach aplikacji?

Nie.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Jakie są najlepsze rozwiązania dotyczące migrowania istniejących mikrousług w chmurze do usługi Azure wiosny?

Podczas migrowania istniejących mikrousług w chmurze wiosny do chmury wiosennej platformy Azure dobrym pomysłem jest przestrzeganie następujących najlepszych rozwiązań:
* Należy rozwiązać wszystkie zależności aplikacji.
* Przygotuj wpisy konfiguracji, zmienne środowiskowe i JVM parametry, aby można było je porównać z wdrożeniem w chmurze Azure wiosennej.
* Jeśli chcesz użyć powiązania usługi, przejdź przez usługi platformy Azure i upewnij się, że ustawiono odpowiednie uprawnienia dostępu.
* Zalecamy usunięcie lub wyłączenie usług osadzonych, które mogą powodować konflikt z usługami zarządzanymi przez chmurę z usługą Azure wiosną, taką jak nasza usługa odnajdowania usług, serwer konfiguracji i tak dalej.
* Zalecamy używanie oficjalnych, stabilnych bibliotek sprężyny Pivot. Wersje nieoficjalne, beta lub rozwidlenia z nieoficjalnymi wersjami bibliotek sprężynowych nie są objęte umową dotyczącą poziomu usług.

Po migracji Monitoruj metryki procesora/pamięci RAM i ruch sieciowy, aby upewnić się, że wystąpienia aplikacji są odpowiednio skalowane.

## <a name="next-steps"></a>Następne kroki

Jeśli masz więcej pytań, zobacz [Przewodnik rozwiązywania problemów z chmurą Azure wiosną](spring-cloud-troubleshoot.md).
