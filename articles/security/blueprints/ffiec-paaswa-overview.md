---
title: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla FFIEC
description: Zabezpieczenia platformy Azure i zgodności planu — aplikacja sieci Web PaaS dla FFIEC
services: security
author: meladie
ms.assetid: a552e313-2b46-4001-b6e2-bed0b7757ac5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: e9aa3939d0fd36ec55d1156d8a5fd38a1cee4279
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985048"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-ffiec-financial-services"></a>Zabezpieczenia platformy Azure i zgodności planu: aplikacja sieci Web PaaS dla usług finansowych FFIEC

## <a name="overview"></a>Przegląd

Ten zabezpieczeń platformy Azure i zgodności planu automatyzacji znajdują się wskazówki dotyczące wdrażania to platforma jako usługa (PaaS) środowiska odpowiednie do zbierania, przechowywania i pobierania danych finansowych prowadzące działalność według federalnych instytucji finansowej Badanie Council (FFIEC). To rozwiązanie pozwala zautomatyzować wdrażanie i konfigurowanie zasobów platformy Azure dla typowych architektury referencyjnej, demonstrując sposób, w którym klienci mogą sprostać określonych wymagań dotyczących zabezpieczeń i zgodności i służy jako podstawa klienci mogą tworzyć i Skonfiguruj swoich rozwiązań na platformie Azure. Rozwiązanie implementuje podzbiór wymagań podręczników FFIEC. Aby uzyskać więcej informacji o wymaganiach FFIEC i tego rozwiązania, zobacz [dokumentację zgodności](#compliance-documentation) sekcji.

Ta zabezpieczeń platformy Azure i automatyzację planu zgodności wdraża architektury referencyjnej aplikacji sieci web PaaS przy użyciu wstępnie skonfigurowane zabezpieczenia, aby ułatwić klientom osiągnąć zgodność z wymaganiami FFIEC. To rozwiązanie składa się z szablonami usługi Azure Resource Manager i skryptów programu PowerShell, które przewodnik dotyczący wdrażania zasobów i konfiguracji.

Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku, bez żadnych modyfikacji nie jest wystarczające, aby całkowicie wymagań FFIEC cele. Pamiętaj o następujących kwestiach:
- Ta architektura zapewnia plan bazowy pomagała klientom platformy Azure w sposób zgodny z FFIEC.
- Klienci są zobowiązani do przeprowadzania rzutem i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić w oparciu szczegółowe informacje na temat implementacji każdego klienta.

Osiągnięcie zgodności ze standardem FFIEC wymaga, że kwalifikowaną audytorów certyfikować rozwiązania klienta produkcyjnego. Inspekcje są nadzorowane przez ekspertów z agencji elementu członkowskiego FFIEC firmy, włącznie z Radą agencje federalne rezerwy systemu (FRB), Federal złożenia ubezpieczeniowe Corporation (FDIC), krajowych administracji Credit Union (NCUA), Urząd Comptroller Waluty (OCC) i Biuro finansowych ochrony konsumenta (CFPB). Te ekspertami oświadczam, że inspekcje odbywa się przez ekspertów, którzy obsługują niezależność od instytucji inspekcji. Klienci są zobowiązani do przeprowadzania odpowiednie zabezpieczenia i oceny zgodności dla dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat implementacji każdego klienta.

Kliknij przycisk [tutaj](https://aka.ms/ffiec-paaswa-repo) instrukcje wdrożenia.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składników

Ta zabezpieczeń platformy Azure i automatyzację planu zgodności wdraża architektury referencyjnej dla aplikacji sieci web PaaS za pomocą wewnętrznej bazy danych Azure SQL Database. Aplikacja sieci web znajduje się w izolowanej Azure App Service Environment, który jest prywatnym, dedykowanym środowisku w centrum danych platformy Azure. Środowisko równoważy obciążenie dla aplikacji sieci web na maszynach wirtualnych zarządzanych przez platformę Azure. Taka architektura obejmuje również sieciowych grup zabezpieczeń, usługa Application Gateway, usługi Azure DNS i modułu równoważenia obciążenia.

Ulepszoną analizę i raportowanie baz danych Azure SQL Database można skonfigurować na indeksach magazynu kolumn. Bazy danych SQL platformy Azure można skalować w górę i w dół lub całkowicie wyłączony w odpowiedzi do użycia przez klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL przez dołączenie certyfikaty z podpisem własnym. Najlepszym rozwiązaniem jest Azure zaleca się korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.

Rozwiązanie używa konta usługi Azure Storage, w których klienci mogą skonfigurować na potrzeby zachowania poufności danych magazynowanych szyfrowanie usługi Storage. Azure przechowuje trzy kopie danych w ramach wybranego centrum danych klienta pod kątem odporności. Geograficzne magazyn nadmiarowy gwarantuje danych replikowana do pomocniczego centrum danych setki mil natychmiast i ponownie przechowywane jako trzy kopie w ramach tego centrum danych, uniemożliwiając zdarzenie niepożądane, u klienta podstawowe centrum danych co spowoduje utratę dane.

Aby zwiększyć bezpieczeństwo wszystkie zasoby w tym rozwiązaniu są zarządzane jako grupa zasobów za pomocą usługi Azure Resource Manager. Usługa Azure Active Directory, kontrola dostępu oparta na rolach jest używana do kontrolowania dostępu do wdrożonych zasobów, w tym do kluczy w usłudze Azure Key Vault. Kondycja systemu jest monitorowany za pośrednictwem usługi Azure Monitor. Klienci, skonfigurować zarówno usług monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwo można nawigować pulpitu nawigacyjnego.

Usługa Azure SQL Database zwykle odbywa się za pomocą programu SQL Server Management Studio, który jest uruchamiany z komputera lokalnego, która jest skonfigurowana do dostępu do bazy danych SQL Azure za pośrednictwem bezpiecznego połączenia sieci VPN lub usługi ExpressRoute.

Ponadto usługa Application Insights zapewnia zarządzanie wydajnością aplikacji w czasie rzeczywistym i analizy przy użyciu usługi Log Analytics. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub usługi ExpressRoute do importowania danych i zarządzania do podsieci architektury odwołanie.**

![Aplikacja sieci Web PaaS dla FFIEC referencyjny diagram architektury](images/ffiec-paaswa-architecture.png "aplikacji sieci Web PaaS dla FFIEC referencyjny diagram architektury")

To rozwiązanie korzysta z poniższych usług platformy Azure. Szczegółowe informacje na temat architektury wdrożenia [architektura wdrożenia](#deployment-architecture) sekcji.

- Application Gateway
    - Zapora aplikacji internetowej
        - Tryb zapory: zapobieganie
        - Zestaw reguł: OWASP
        - Port odbiornika: 443
- Application Insights
- Usługa Azure Active Directory
- Środowiska usługi aplikacji Azure w wersji 2
- Azure Automation
- System DNS platformy Azure
- W usłudze Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Virtual Network
    - (1) /16 sieci
    - (4) sieci prefiksie/24
    - Grupy zabezpieczeń sieci
- Aplikacja sieci Web platformy Azure

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji przedstawiono elementy wdrożenia i implementacji.

**Usługa Azure Resource Manager**: [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwia klientom pracę z zasobami w rozwiązaniu jako grupa. Klientów można wdrożyć, zaktualizować lub usunąć wszystkie zasoby dla rozwiązania w jednej, skoordynowanej operacji. Klienci dla wdrożenia wykonuje się przy użyciu szablonu, którego można używać w różnych środowiskach, takich jak testowanie, etap przejściowy i produkcja. Menedżer zasobów zapewnia zabezpieczeń, inspekcji i znakowania funkcje, aby pomóc klientom w zarządzaniu zasobami po wdrożeniu.

**Host bastionu**: hostem bastionu jest pojedynczym punktem wejścia, który umożliwia użytkownikom dostęp do zasobów wdrożonych w tym środowisku. Host bastionu zapewnia bezpieczne połączenie do wdrożonych zasobów poprzez zezwolenie wyłącznie na zdalny ruch z publicznych adresów IP na liście bezpiecznych. Zezwalaj ruchu (RDP) pulpitu zdalnego, źródło ruchu musi być zdefiniowany w sieciowej grupie zabezpieczeń.

To rozwiązanie tworzy maszynę wirtualną jako host bastionu przyłączone do domeny w następujący sposób:
-   [Rozszerzenia ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozszerzenie diagnostyki platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) przy użyciu usługi Azure Key Vault
-   [Zasady automatycznego zamykania](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) można ograniczyć zużycie zasobów maszyny wirtualnej nieużywane
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) włączone tak, aby poświadczeń i innych wpisów tajnych są uruchamiane w środowisku chronionego, która jest odizolowana od system operacyjny

**App Service Environment w wersji 2**: Azure App Service Environment to funkcja usługi App Service udostępniająca w pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania aplikacji usługi App Service w dużej skali. Funkcja ta Izolacja jest wymagany do spełnienia wymagań dotyczących zgodności FFIEC.

Środowiska usługi App Service są izolowane do uruchamiania tylko jednego klienta aplikacji i są zawsze wdrażane w sieci wirtualnej. Ta funkcja izolacji umożliwia architektury referencyjnej zapewnienie izolacji dzierżawców pełną, usunięcie go z platformy Azure środowiska z wieloma dzierżawami zabronienia tych wielu dzierżaw z wyliczania wdrożonych zasobów w środowisku usługi App Service. Klienci mają precyzyjną kontrolę nad zarówno aplikacji dla ruchu przychodzącego i wychodzącego ruchu sieciowego i aplikacje mogą nawiązywać bezpieczne szybkie połączenia za pośrednictwem wirtualnych sieci lokalnych zasobów firmowych. Klienci mogą "automatycznego skalowania" za pomocą środowiska App Service Environment na podstawie metryk obciążenia, dostępnego budżetu lub zdefiniowany harmonogram.

Umożliwia użycie środowiska App Service Environment w przypadku tej architektury następujące konfiguracje:

- Hostowanie w zabezpieczonej sieci wirtualnej platformy Azure i reguł zabezpieczeń sieciowych
- Certyfikat modułu równoważenia obciążenia z podpisem własnym wewnętrznego dla komunikacji HTTPS. Najlepszym rozwiązaniem firma Microsoft zaleca korzystanie z zaufanego urzędu certyfikacji w celu uzyskania zwiększonych zabezpieczeń.
- [Tryb równoważenia obciążenia wewnętrznego](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Wyłącz [protokołu TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Zmiana [szyfrowania TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Kontrolka [ruchu N/M porty wejściowe](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Zapora aplikacji sieci Web — ograniczanie danych](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Zezwalaj na [ruch usługi Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Usługa Azure Web Apps**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) umożliwia klientom tworzenie i hostowanie wybranych przez nich aplikacji sieci web w języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje systemy Windows i Linux, a także umożliwia używanie wdrożeń zautomatyzowanych z usług GitHub i Azure DevOps oraz dowolnych repozytoriów Git.

### <a name="virtual-network"></a>Virtual Network

Architektura definiuje prywatnej sieci wirtualnej przy użyciu przestrzeni adresowej 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zawierają listy kontroli dostępu, które blokują lub zezwalają na ruch w sieci wirtualnej. Sieciowe grupy zabezpieczeń może służyć do zabezpieczenia ruchu na poziomie poszczególnych maszyn wirtualnych lub podsieci. Istnieją następujące grupy zabezpieczeń sieci:

- 1 sieciowej grupy zabezpieczeń w usłudze Application Gateway
- 1 sieciowej grupy zabezpieczeń dla środowiska App Service Environment
- 1 sieciowej grupy zabezpieczeń usługi Azure SQL Database
- 1 sieciowe grupy zabezpieczeń dla hostem bastionu

Każdej z grup zabezpieczeń sieci ma określone porty i protokoły Otwórz rozwiązanie może pracować bezpiecznie i poprawnie. Ponadto następujące konfiguracje są włączone dla każdej sieciowej grupy zabezpieczeń:

- [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
- Usługa log Analytics jest połączony z [sieciowej grupy zabezpieczeń&#39;s dzienniki diagnostyczne](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: każdej podsieci jest skojarzona z sieciową grupą zabezpieczeń odpowiednich.

**Usługa Azure DNS**: System nazw domen lub DNS, odpowiada za tłumaczenia (lub rozpoznawanie) nazwę witryny sieci Web lub usługi na jej adres IP. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to Usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostowanie domen na platformie Azure, użytkownicy mogą zarządzać rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co inne usługi platformy Azure. Usługa DNS platformy Azure obsługuje również prywatne domen DNS.

**Usługa Azure Load Balancer**: [usługi Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) pozwala klientom na skalowanie swoich aplikacji i zapewniać wysoką dostępność usług. Modułu równoważenia obciążenia obsługuje scenariusze dla ruchu przychodzącego, a także ruchu wychodzącego i zapewnia małe opóźnienia i wysoką przepływność oraz skaluje nawet miliony przepływów dla wszystkich aplikacji TCP i UDP.

### <a name="data-in-transit"></a>Przesyłane dane

Azure szyfruje cała komunikacja do i z centrów danych platformy Azure, domyślnie. Wszystkie transakcje w usłudze Azure Storage za pośrednictwem witryny Azure portal występują za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane za pomocą funkcji szyfrowania, inspekcja bazy danych i innych miar.

**Usługa Azure Storage**: Aby spełnić zaszyfrowanych danych na pozostałe wymagania, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używa [szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Pomaga to chronić i ochrony danych na rzecz organizacji bezpieczeństwa i zdefiniowane przez FFIEC wymagań dotyczących zgodności.

**Usługa Azure Disk Encryption**: [usługi Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker Windows, aby zapewnić szyfrowanie woluminów dla dysków z danymi. To rozwiązanie integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysków.

**Usługa Azure SQL Database**: wystąpienie usługi Azure SQL Database używa następujące środki bezpieczeństwa bazy danych:

- [Uwierzytelnianie usługi Active Directory i autoryzacji](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) śledzi zdarzenia bazy danych i zapisuje je do inspekcji dzienniku na konto magazynu platformy Azure.
- Usługa Azure SQL Database jest skonfigurowany do używania [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), który wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i rest usługi pliki dziennika transakcji, aby chronić informacje o. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywanych danych nie została poddana przed nieautoryzowanym dostępem.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwia wszelki dostęp do serwerów bazy danych, dopóki nie zostały przyznane odpowiednie uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Funkcja wykrywania zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i odpowiedzi na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach, atakami polegającymi na iniekcji SQL i dostęp do bazy danych nietypowe wzorce.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie pojawia się jako zwykły tekst wewnątrz system bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy można dostęp do danych w postaci zwykłego tekstu.
- [Baza danych SQL dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez maskowanie danych nieuprzywilejowanych użytkowników lub aplikacji. Dynamiczne maskowanie danych można automatycznie wykryć potencjalnie poufnych danych i zaproponuj odpowiednie maski mają być stosowane. Pomaga to identyfikować i Ogranicz dostęp do danych w taki sposób, że nie istnieje w bazie danych za pośrednictwem przed nieautoryzowanym dostępem. Klienci są zobowiązani do dostosowywania dane dynamiczne maskowanie ustawienia, aby stosować się do ich schemat bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają możliwości, aby zarządzać dostępem do danych w środowisku platformy Azure:

- [Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Microsoft&#39;s wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami usługi zarządzania. Wszyscy użytkownicy w tym rozwiązaniu są tworzone w usługi Azure Active Directory, w tym użytkowników uzyskujących dostęp do usługi Azure SQL Database.
- Do aplikacji uwierzytelniania przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Ponadto szyfrowania kolumny bazy danych używa usługi Azure Active Directory do uwierzytelniania aplikacji w usłudze Azure SQL Database. Aby uzyskać więcej informacji, zobacz instrukcje [chronić poufne dane w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umożliwia administratorom zdefiniować uprawnienia dostępu w zakresie, aby udzielać uprawnień dostępu potrzebnym użytkownikom do wykonywania swoich zadań. Zamiast wszystkie uprawnienia użytkownika bez ograniczeń dla zasobów platformy Azure, Administratorzy mogą zezwalać tylko określone akcje do uzyskiwania dostępu do danych. Dostęp do subskrypcji jest ograniczona do administratora subskrypcji.
- [Usługa Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umożliwia klientom zminimalizować liczbę użytkowników, którzy mają dostęp do niektórych informacji. Administratorzy mogą używać usługi Azure Active Directory Privileged Identity Management w celu odnajdywania, ograniczanie i monitorowanie uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Ta funkcja może również wymuszać na żądanie, just-in-time dostęp administracyjny w razie.
- [Usługa Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) wykrywa potencjalnych luk w zabezpieczeniach wpływających na organizacji&#39;tożsamości s umożliwia skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane akcje powiązane z organizacji&#39;tożsamości s i bada podejrzane zdarzenia podejmowanie odpowiednich działań, aby je rozwiązać.

### <a name="security"></a>Bezpieczeństwo

**Zarządzanie wpisami tajnymi**: rozwiązanie używa [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomóc klientom chronić i uzyskać dostęp do takich danych:

- Zaawansowane zasady dostępu są skonfigurowane na podstawie potrzeb.
- Zasady dostępu magazynu kluczy są definiowane za pomocą minimalnych wymaganych uprawnień do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w usłudze Key Vault ma daty wygaśnięcia.
- Wszystkie klucze w usłudze Key Vault są chronione przez specjalistyczne sprzętowych modułów zabezpieczeń. Typ klucza jest HSM chronione 2048-bitowego klucza RSA.
- Wszyscy użytkownicy i tożsamości są przyznawane minimalnych wymaganych uprawnień za pomocą kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne usługi Key Vault są włączone z okresem przechowywania, co najmniej 365 dni.
- Dozwolone operacje kryptograficzne klucze są ograniczone do tych wymagane.

**Usługa Azure Security Center**: za pomocą [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), klienci mogą centralnie zastosować i zarządzać zasadami zabezpieczeń na potrzeby różnych obciążeń, ograniczenia narażenia na zagrożenia i wykrywanie oraz reagowanie na ataki. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zalecenia dotyczące usługi w celu zwiększenia poziomu bezpieczeństwa i ochrony danych.

Usługa Azure Security Center używa różnych możliwości wykrywania powiadamia klientów przed potencjalnymi atakami wymierzonymi w ich środowiskach. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center zawiera zbiór [wstępnie zdefiniowane alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanej aktywności. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Centrum zabezpieczeń Azure umożliwiają klientom Definiowanie nowych alertów zabezpieczeń, w oparciu o dane, które zostały już zebrane ze swoim środowisku.

Azure Security Center oferuje alerty zabezpieczeń z określonymi priorytetami i zdarzenia, dzięki czemu łatwiejsze dla klientów odkryć i rozwiązać potencjalne problemy z zabezpieczeniami. A [raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdej wykrytej przed zagrożeniami, aby pomóc zespołom reagowania na zdarzenia w badanie i korygowanie działań na podstawie zagrożeń.

**Usługa Azure Application Gateway**: Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu usługi Azure Application Gateway przy użyciu zapory aplikacji sieci web, skonfigurowane i włączone zestaw reguł OWASP. Dodatkowe funkcje obejmują:

- [Koniec na końcu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [protokołu TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (tryb zapobiegania)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) przy użyciu reguł OWASP 3.0
- Włącz [rejestrowania diagnostycznego](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Niestandardowe sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Usługa Azure Security Center](https://azure.microsoft.com/services/security-center) i [usługi Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) zapewnia dodatkową ochronę i powiadomienia. Usługa Azure Security Center zapewnia także system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure często dziennika systemu i aktywności użytkownika, a także kondycji systemu:
- **Dzienniki aktywności**: [dzienników aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) udostępniają szczegółowe dane operacji wykonywanych na zasobach w subskrypcji. Dzienniki aktywności można określić inicjatora operacji czasu wystąpienie i stan.
- **Dzienniki diagnostyczne**: [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń Windows, dzienniki usługi Azure Storage, dzienników inspekcji usługi Key Vault i usługa Application Gateway Dzienniki dostępu i zapory. Wszystkie dzienniki diagnostyczne zapisu do konta usługi Azure storage scentralizowany i zaszyfrowane w celu archiwizacji. Okres przechowywania jest konfigurowanych przez użytkownika, się do 730 dni, spełniają wymagania specyficzne dla organizacji przechowywania.

**Log Analytics**: tych dzienników i dalszych są skonsolidowane w [usługi Log Analytics](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowanie na pulpicie nawigacyjnym. Po zebraniu dane są organizowane w oddzielnych tabelach dla każdego typu danych w obszarach roboczych pakietu Operations Management Suite, który zezwala na wszystkie dane mogą być analizowane razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z usługą Log Analytics, umożliwiając klientom zapytań usługi Log Analytics umożliwia dostęp do swoich danych zdarzeń zabezpieczeń i łączyć je z danymi z innych usług.

Następujące usługi Log Analytics [rozwiązań do zarządzania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) są uwzględniane w ramach tej architektury:
-   [Oceny usługi Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory Health Check rozwiązanie ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Ocena SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): rozwiązania SQL Health Check ocenia ryzyko i kondycję środowisk serwerów programu w regularnych odstępach czasu i zapewnia klientom priorytetową listą zalecenia dotyczące infrastruktury serwera wdrożone.
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): rozwiązanie Agent Health raporty są wdrażane liczby agentów i ich rozmieszczenie geograficzne, a także liczby agentów, które są nie odpowiada i liczbę agentów, które są przesyłanie danych operacyjnych.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): rozwiązanie Activity Log Analytics obsługuje analizy dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klientów.

**Usługa Azure Automation**: [usługi Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowywane i zarządza elementami runbook i jest uruchamiany. W tym rozwiązaniu elementów runbook pomagają zbieranie dzienników z usługi Azure SQL Database. Automatyzację [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) rozwiązanie umożliwia klientom łatwo identyfikować zmiany w środowisku.

**Usługa Azure Monitor**: [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenia wydajności, zapewniania bezpieczeństwa i identyfikowania trendów, umożliwiając organizacjom inspekcję, tworzyć alerty i archiwizować dane, w tym śledzenia wywołań interfejsu API w ich systemie Azure zasoby.

**Usługa Application Insights**: [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna Usługa zarządzania wydajnością aplikacji dla deweloperów sieci web na wielu platformach. Usługa Application Insights wykrywa anomalie wydajność i klienci mogą używać go do monitorowania aplikacji sieci web. Obejmuje ona zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów z klientami i aby zrozumieć, jak użytkownicy w rzeczywistości korzystają z aplikacji. Jego&#39;s zaprojektowane, aby pomóc klientom w ciągłym udoskonalaniu wydajności i użyteczności.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępne dla [Pobierz](https://aka.ms/ffiec-paaswa-tm) lub znajdują się poniżej. Ten model może pomóc klientom w zrozumieniu punkty potencjalne ryzyko w ramach infrastruktury systemu podczas wprowadzania zmian.

![Aplikacja sieci Web PaaS dla model zagrożeń FFIEC](images/ffiec-paaswa-threat-model.png "aplikacji sieci Web PaaS dla FFIEC model zagrożeń")

## <a name="compliance-documentation"></a>Dokumentacja zgodności

[Zabezpieczeń platformy Azure i zgodności planu — macierz odpowiedzialności klienta FFIEC](https://aka.ms/ffiec-crm) Wyświetla listę wszystkich celów zabezpieczeń wymagane przez FFIEC. Ta macierz szczegółowe informacje, czy odpowiedzialność firmy Microsoft, klienta i wykonania każdego celu lub współużytkowana przez dwa.

[Zabezpieczeń platformy Azure i zgodności planu — FFIEC PaaS Web Application implementacji Matrix](https://aka.ms/ffiec-paaswa-cim) udostępnia informacje, na które FFIEC wymagania są rozwiązywane przez architektury aplikacji sieci web PaaS, w tym szczegółowe opisy jak wdrożenia spełnia wymagania każdego celu objętego usługą.

## <a name="deploy-this-solution"></a>Wdróż to rozwiązanie
Ten zabezpieczeń platformy Azure i zgodności planu automatyzacji składa się z pliki konfiguracji JSON i skryptów programu PowerShell, które są obsługiwane przez usługę interfejsu API usługi Azure Resource Manager do wdrażania zasobów na platformie Azure. Dostępne są instrukcje wdrożenia są szczegółowo [tutaj](https://aka.ms/ffiec-paaswa-repo).

#### <a name="quickstart"></a>Szybki start
1. Klonuj lub Pobierz [to](https://aka.ms/ffiec-paaswa-repo) repozytorium GitHub na lokalnej stacji roboczej.

2. Przejrzyj AdministrativeAccountAndPermission.md-0-instalacji, a następnie uruchom podanego polecenia.

3. Wdróż rozwiązanie testu z firmy Contoso przykładowe dane lub projekt pilotażowy w środowisku produkcyjnym początkowej.
    - 1a ContosoWebStoreDemoAzureResources.ps1
        - Ten skrypt służy do wdrażania zasobów platformy Azure do pokazania sklepu webstore przy użyciu Contoso przykładowych danych.
    - 1 DeployAndConfigureAzureResources.ps1
        - Ten skrypt służy do wdrażania zasobów platformy Azure służące do obsługi środowiska produkcyjnego dla aplikacji sieci web należącej do klienta. To środowisko powinno dodatkowo dostosowywać przez klienta na podstawie wymagań organizacyjnych.

## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Bezpieczny tunel sieci VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi zostać skonfigurowany nawiązać bezpiecznego połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci web PaaS. Konfigurując odpowiednie sieci VPN lub usługi ExpressRoute, klientów można dodać warstwę ochrony danych podczas przesyłania.

Wdrażając bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualnego prywatne połączenie między siecią lokalną i usługą Azure Virtual Network. To połączenie odbywa się przez Internet oraz umożliwia klientom bezpieczne &quot;tunelu&quot; informacji o wewnątrz zaszyfrowanych powiązanie klienta&#39;s siecią i platformą Azure. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. [Trybu tunelowania IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w przypadku tej opcji jako mechanizmu szyfrowania.

Ponieważ ruch sieciowy w ramach tunelu VPN przechodzą przez Internet przy użyciu sieci VPN lokacja lokacja, firma Microsoft oferuje opcję inny, jeszcze bardziej bezpieczne połączenia. Usługa ExpressRoute systemu Azure jest dedykowany sieci WAN łącze między Azure i lokacją lokalną lub hostingu dostawcy usług Exchange. Jak połączeń usługi ExpressRoute nie odbywają się za pośrednictwem Internetu, te połączenia oferują więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto ponieważ jest to bezpośrednie połączenie klienta&#39;s dostawcy usług telekomunikacyjnych, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest uwidaczniana, do niego.

Najlepsze rozwiązania dotyczące wdrażania bezpieczną sieć hybrydową, która rozszerza sieć lokalną na platformę Azure są [dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, W ODNIESIENIU DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany &quot;jako-to.&quot; Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odnośniki do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci odczytu ten dokument jest odpowiedzialny za jej pomocą.
- W tym dokumencie nie zapewnia klientom żadnych praw do własności intelektualnej w dowolnym produkcie firmy Microsoft lub rozwiązań.
- Klienci kopiować i używać tego dokumentu do celów referencyjnych wewnętrznego.
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie, może spowodować od zwiększoną danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć klient&#39;s licencja na usługę Azure i subskrypcję kosztów z góry.
- Ta architektura jest przeznaczona do służyć jako podstawa dla klientów dostosować ich określonych wymagań i nie może być używana jako — w środowisku produkcyjnym.
- Ten dokument jest opracowana jako odwołanie i nie należy używać do definiowania wszystkich oznacza, że przez co klient spełnia wymagania w zakresie wymagań dotyczących określonych zgodności i przepisów. Klienci, powinny zwrócić prawne obsługi ze swojej organizacji w implementacji zatwierdzone klienta.
