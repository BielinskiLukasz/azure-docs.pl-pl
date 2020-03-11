---
title: Jak używać usługi Azure API Management z sieciami wirtualnymi
description: Dowiedz się, jak skonfigurować połączenie z siecią wirtualną w usłudze Azure API Management i uzyskać dostęp do usług sieci Web.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: 62e8c174cd10a003657093b805291e003a9ede1b
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968180"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Jak używać usługi Azure API Management z sieciami wirtualnymi
Sieci wirtualne platformy Azure umożliwiają umieszczanie dowolnych zasobów platformy Azure w sieci nieobsługującej routingu internetowego, do której kontrolujesz dostęp. Te sieci mogą następnie być połączone z sieciami lokalnymi przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej na temat sieci wirtualnych platformy Azure, Zacznij od informacji tutaj: [Omówienie usługi azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Usługę Azure API Management można wdrożyć w sieci wirtualnej (VNET), dzięki czemu można uzyskać dostęp do usług zaplecza w sieci. Portal dla deweloperów i Brama interfejsu API można skonfigurować tak, aby były dostępne zarówno z Internetu, jak i tylko w obrębie sieci wirtualnej.

> [!NOTE]
> Adres URL dokumentu importowania interfejsu API musi być obsługiwany przez publicznie dostępny adres internetowy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie APIM. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Włącz połączenie sieci wirtualnej

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Włączanie łączności sieci wirtualnej przy użyciu Azure Portal

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby znaleźć wystąpienie usługi API Management. Wyszukaj i wybierz **usługi API Management Services**.

2. Wybierz wystąpienie API Management.

3. Wybierz pozycję **Sieć wirtualna**.
4. Skonfiguruj wystąpienie API Management, które ma zostać wdrożone w sieci wirtualnej.

    ![Menu sieci wirtualnej API Management][api-management-using-vnet-menu]
5. Wybierz żądany typ dostępu:

    * **Wyłączone**: jest to ustawienie domyślne. API Management nie jest wdrożona w sieci wirtualnej.

    * **Zewnętrzny**: Brama API Management i Portal dla deweloperów są dostępni z publicznej sieci Internet za pośrednictwem zewnętrznego modułu równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

        ![Publiczna komunikacja równorzędna][api-management-vnet-public]

    * **Wewnętrzne**: Brama API Management i Portal dla deweloperów są dostępni tylko z poziomu sieci wirtualnej za pośrednictwem wewnętrznego modułu równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

        ![Prywatna komunikacja równorzędna][api-management-vnet-private]

6. W przypadku wybrania wartości **zewnętrzny** lub **wewnętrzny**zostanie wyświetlona lista wszystkich regionów, w których Zainicjowano obsługę usługi API Management. Wybierz **lokalizację**, a następnie wybierz jej **sieć wirtualną** i **podsieć**. Lista Sieć wirtualna jest wypełniana zarówno klasycznymi, jak i Menedżer zasobów sieciami wirtualnymi dostępnymi w ramach subskrypcji platformy Azure, które są skonfigurowane w konfigurowanym regionie.

    > [!IMPORTANT]
    > W przypadku wdrażania wystąpienia usługi Azure API Management w sieci wirtualnej Menedżer zasobów usługa musi znajdować się w dedykowanej podsieci, która nie zawiera żadnych innych zasobów z wyjątkiem wystąpień API Management platformy Azure. Jeśli podjęto próbę wdrożenia wystąpienia usługi Azure API Management w sieci wirtualnej Menedżer zasobów zawierającej inne zasoby, wdrożenie zakończy się niepowodzeniem.

    Następnie wybierz pozycję **Zastosuj**. Strona **Sieć wirtualna** wystąpienia API Management jest aktualizowana przy użyciu nowych opcji sieci wirtualnej i podsieci.

    ![Wybieranie sieci VPN][api-management-setup-vpn-select]

7. Na górnym pasku nawigacyjnym wybierz pozycję **Zapisz**, a następnie wybierz pozycję **Zastosuj konfigurację sieci**.

> [!NOTE]
> Adres VIP wystąpienia API Management zmieni się za każdym razem, gdy sieć wirtualna jest włączona lub wyłączona.
> Adres VIP zostanie również zmieniony, gdy API Management jest przenoszona z **zewnątrz** do **wewnętrznego**lub odwrotnie.
>

> [!IMPORTANT]
> Jeśli usuniesz API Management z sieci wirtualnej lub zmienisz ją wdrożoną w programie, wcześniej użyta Sieć wirtualna może pozostać zablokowana przez maksymalnie sześć godzin. W tym czasie nie będzie można usunąć sieci wirtualnej ani wdrożyć w niej nowego zasobu. To zachowanie jest prawdziwe w przypadku klientów korzystających z interfejsu API w wersji 2018-01-01 lub starszej. Klienci korzystający z interfejsu API w wersji 2019-01-01 lub nowszej sieci wirtualnej są zwalniane od razu po usunięciu skojarzonej usługi API Management.

## <a name="enable-vnet-powershell"> </a>Włączanie połączenia sieci wirtualnej przy użyciu poleceń cmdlet programu PowerShell
Połączenie sieci wirtualnej można również włączyć za pomocą poleceń cmdlet programu PowerShell.

* **Tworzenie usługi API Management wewnątrz sieci wirtualnej**: za pomocą polecenia cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) Utwórz usługę Azure API Management w sieci wirtualnej.

* **Wdróż istniejącą usługę API Management w sieci wirtualnej**: Użyj polecenia cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) , aby przenieść istniejącą usługę Azure API Management do Virtual Network.

## <a name="connect-vnet"> </a>Nawiązywanie połączenia z usługą sieci Web hostowaną w sieci wirtualnej
Gdy usługa API Management jest połączona z siecią wirtualną, dostęp do usług zaplecza nie różni się od uzyskiwania dostępu do usług publicznych. Po prostu wpisz adres IP lub nazwę hosta (Jeśli serwer DNS jest skonfigurowany dla sieci wirtualnej) w polu **adres URL usługi** sieci Web podczas tworzenia nowego interfejsu API lub edytowania istniejącego.

![Dodawanie interfejsu API z sieci VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Typowe problemy z konfiguracją sieci
Poniżej znajduje się lista typowych problemów z błędami konfiguracji, które mogą wystąpić podczas wdrażania usługi API Management w Virtual Network.

* **Konfiguracja niestandardowego serwera DNS**: usługa API Management zależy od kilku usług platformy Azure. Gdy API Management jest hostowana w sieci wirtualnej z niestandardowym serwerem DNS, musi rozpoznać nazwy hostów tych usług platformy Azure. Postępuj zgodnie z [tymi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wskazówkami dotyczącymi NIESTANDARDOWEJ konfiguracji DNS. Zapoznaj się z poniższą tabelą porty i innymi wymaganiami dotyczącymi sieci.

> [!IMPORTANT]
> Jeśli planujesz używać niestandardowych serwerów DNS dla sieci wirtualnej, skonfiguruj ją **przed** wdrożeniem usługi API Management. W przeciwnym razie należy zaktualizować usługę API Management przy każdej zmianie serwerów DNS przez uruchomienie [operacji Zastosuj konfigurację sieci](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty wymagane do API Management**: ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management, może być kontrolowany przy użyciu [sieciowej grupy zabezpieczeń][Network Security Group]. Jeśli którykolwiek z tych portów jest niedostępny, API Management może nie działać prawidłowo i może stać się niedostępna. Jeśli co najmniej jeden z tych portów jest zablokowany, jest to inny typowy problem z konfiguracją podczas korzystania z API Management z siecią wirtualną.

<a name="required-ports"></a> Gdy wystąpienie usługi API Management jest hostowane w sieci wirtualnej, używane są porty w poniższej tabeli.

| Porty źródłowe/docelowe | Kierunek          | Protokół transportowy |   [Tagi usług](../virtual-network/security-overview.md#service-tags) <br> Źródło/miejsce docelowe   | Cel (*)                                                 | Typ Virtual Network |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Przychodzący            | TCP                | INTERNET/VIRTUAL_NETWORK            | Komunikacja z klientem do API Management                      | Zewnętrzna             |
| * / 3443                     | Przychodzący            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Punkt końcowy zarządzania dla Azure Portal i programu PowerShell         | Wewnętrzna & zewnętrzna  |
| * / 80, 443                  | Wychodzący           | TCP                | VIRTUAL_NETWORK/magazyn             | **Zależność od usługi Azure Storage**                             | Wewnętrzna & zewnętrzna  |
| * / 80, 443                  | Wychodzący           | TCP                | VIRTUAL_NETWORK/usługi azureactivedirectory | [Azure Active Directory](api-management-howto-aad.md) (jeśli dotyczy)                   | Wewnętrzna & zewnętrzna  |
| * / 1433                     | Wychodzący           | TCP                | VIRTUAL_NETWORK/SQL                 | **Dostęp do punktów końcowych usługi Azure SQL**                           | Wewnętrzna & zewnętrzna  |
| */5671, 5672, 443          | Wychodzący           | TCP                | VIRTUAL_NETWORK/EventHub            | Zależność dla [dziennika do zasad usługi Event Hub](api-management-howto-log-event-hubs.md) i agenta monitorowania | Wewnętrzna & zewnętrzna  |
| * / 445                      | Wychodzący           | TCP                | VIRTUAL_NETWORK/magazyn             | Zależność od udziału plików platformy Azure dla usługi [git](api-management-configuration-repository-git.md)                      | Wewnętrzna & zewnętrzna  |
| * / 1886                     | Wychodzący           | TCP                | VIRTUAL_NETWORK/INTERNET            | Potrzeba opublikowania stanu kondycji w celu Resource Health          | Wewnętrzna & zewnętrzna  |
| * / 443                     | Wychodzący           | TCP                | VIRTUAL_NETWORK/AzureMonitor         | Publikowanie [dzienników diagnostycznych i metryk](api-management-howto-use-azure-monitor.md)                       | Wewnętrzna & zewnętrzna  |
| * / 25                       | Wychodzący           | TCP                | VIRTUAL_NETWORK/INTERNET            | Nawiązywanie połączenia z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Wewnętrzna & zewnętrzna  |
| */587                      | Wychodzący           | TCP                | VIRTUAL_NETWORK/INTERNET            | Nawiązywanie połączenia z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Wewnętrzna & zewnętrzna  |
| * / 25028                    | Wychodzący           | TCP                | VIRTUAL_NETWORK/INTERNET            | Nawiązywanie połączenia z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Wewnętrzna & zewnętrzna  |
| * / 6381 - 6383              | Przychodzące & wychodzące | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Dostęp do usługi Redis na potrzeby zasad [limitu szybkości](api-management-access-restriction-policies.md#LimitCallRateByKey) między maszynami         | Wewnętrzna & zewnętrzna  |
| * / *                        | Przychodzący            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Load Balancer infrastruktury platformy Azure                          | Wewnętrzna & zewnętrzna  |

>[!IMPORTANT]
> Porty, dla których *przeznaczenie* jest **pogrubienie** , są wymagane do pomyślnego wdrożenia usługi API Management. Zablokowanie innych portów spowoduje jednak obniżenie wydajności i monitorowanie uruchomionej usługi.

+ **Funkcja SSL**: Aby włączyć tworzenie i sprawdzanie poprawności łańcucha certyfikatu SSL, usługa API Management wymaga łączności sieciowej wychodzącej z ocsp.msocsp.com, mscrl.microsoft.com i CRL.Microsoft.com. Ta zależność nie jest wymagana, jeśli dowolny certyfikat przekazywany do API Management zawiera pełny łańcuch do katalogu głównego urzędu certyfikacji.

+ **Dostęp do usługi DNS**: dostęp wychodzący na porcie 53 jest wymagany do komunikacji z serwerami DNS. Jeśli na drugim końcu bramy sieci VPN istnieje niestandardowy serwer DNS, serwer DNS musi być osiągalny z API Management hostowania podsieci.

+ **Monitorowanie metryk i kondycji**: wychodząca łączność sieciowa z punktami końcowymi monitorowania platformy Azure, które są rozwiązywane w następujących domenach:

    | Środowisko platformy Azure | Punkty końcowe                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net (**Nowy**)</li><li>prod.warmpath.msftcloudes.com (**jako przestarzałe**)</li><li>shoebox2.metrics.microsoftmetrics.com (**Nowy**)</li><li>shoebox2.metrics.nsatc.net (**jako przestarzałe**)</li><li>prod3.metrics.microsoftmetrics.com (**Nowy**)</li><li>prod3.metrics.nsatc.net (**jako przestarzałe**)</li><li>prod3-black.prod3.metrics.microsoftmetrics.com (**Nowy**)</li><li>prod3-black.prod3.metrics.nsatc.net (**jako przestarzałe**)</li><li>prod3-red.prod3.metrics.microsoftmetrics.com (**Nowy**)</li><li>prod3-red.prod3.metrics.nsatc.net (**jako przestarzałe**)</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com, gdzie `East US 2` jest eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com (**Nowy**)</li><li>shoebox2.metrics.nsatc.net (**jako przestarzałe**)</li><li>prod3.metrics.microsoftmetrics.com (**Nowy**)</li><li>prod3.metrics.nsatc.net (**jako przestarzałe**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure w Chinach — 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com (**Nowy**)</li><li>shoebox2.metrics.nsatc.net (**jako przestarzałe**)</li><li>prod3.metrics.microsoftmetrics.com (**Nowy**)</li><li>prod3.metrics.nsatc.net (**jako przestarzałe**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

>[!IMPORTANT]
> Zmiana klastrów powyżej przy użyciu strefy DNS **. nsatc.NET** do **. microsoftmetrics.com** jest przede wszystkim zmianą DNS. Adres IP klastra nie ulegnie zmianie.

+ **Przekaźnik SMTP**: wychodząca łączność sieciowa dla przekaźnika SMTP, która jest rozpoznawana w obszarze `smtpi-co1.msn.com`hosta, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` i `ies.global.microsoft.com`

+ **Portal deweloperów CAPTCHA**: wychodząca łączność sieciowa dla CAPTCHA portalu deweloperów, która jest rozpoznawana w obszarze hosty `client.hip.live.com` i `partner.hip.live.com`.

+ **Azure Portal Diagnostics**: aby umożliwić przepływ dzienników diagnostycznych z Azure Portal przy użyciu rozszerzenia API Management z wewnątrz Virtual Network, wymagany jest dostęp wychodzący do `dc.services.visualstudio.com` na porcie 443. Pomaga to w rozwiązywaniu problemów, które mogą wystąpić podczas korzystania z rozszerzenia.

+ **Wymuś tunelowanie ruchu w zaporze Premium za pomocą usługi Express Route lub sieciowego urządzenia wirtualnego**: typową konfiguracją klienta jest definiowanie własnej trasy domyślnej (0.0.0.0/0), która wymusza cały ruch z API Management delegowanej podsieci do przepływu za pośrednictwem lokalnej zapory lub sieciowego urządzenia wirtualnego. Ten przepływ ruchu niezmiennie zrywa łączność z platformą Azure API Management, ponieważ ruch wychodzący jest blokowany lokalnie lub NAT na nierozpoznawalny zbiór adresów, które nie działają już z różnymi punktami końcowymi platformy Azure. Rozwiązanie wymaga wykonania kilku czynności:

  * Włącz punkty końcowe usługi w podsieci, w której wdrożono usługę API Management. [Punkty końcowe usługi][ServiceEndpoints] muszą być włączone dla usług Azure SQL, Azure Storage, Azure EventHub i Azure ServiceBus. Włączenie punktów końcowych bezpośrednio z API Management delegowanej podsieci do tych usług pozwala im korzystać z sieci szkieletowej Microsoft Azure zapewniającej optymalny Routing ruchu usług. W przypadku korzystania z punktów końcowych usługi przy użyciu wymuszonego tunelowego zarządzania interfejsem API powyższy ruch usług platformy Azure nie jest wymuszany. Inne API Management ruchu zależności usługi są wymuszane tunelowanie i nie można go utracić lub usługa API Management nie działa prawidłowo.
    
  * Cały ruch płaszczyzny kontroli z Internetu do punktu końcowego zarządzania usługi API Management jest kierowany przez określony zestaw przychodzących adresów IP hostowanych przez API Management. Gdy ruch wymuszony jest tunelowany, odpowiedzi nie będą symetryczne mapowanie z powrotem na te adresy IP źródła przychodzącego. Aby przezwyciężyć ograniczenie, musimy dodać następujące trasy zdefiniowane przez użytkownika ([UDR][UDRs]) w celu kierowania ruchu z powrotem do platformy Azure przez ustawienie miejsca docelowego tych tras hosta na wartość "Internet". Zbiór przychodzących adresów IP dla ruchu płaszczyzny kontroli jest udokumentowany [płaszczyzny kontroli adresy IP](#control-plane-ips)

  * W przypadku innych zależności usługi API Management, które są wymuszane tunelowanie, powinien istnieć sposób, aby rozpoznać nazwę hosta i skontaktować się z punktem końcowym. Obejmują one
      - Metryki i monitorowanie kondycji
      - Diagnostyka Azure Portal
      - Przekaźnik SMTP
      - Portal dla deweloperów CAPTCHA

## <a name="troubleshooting"> </a>Rozwiązywanie problemów
* **Początkowa konfiguracja**: Jeśli początkowe wdrożenie usługi API Management w podsieci nie powiedzie się, zaleca się najpierw wdrożyć maszynę wirtualną w tej samej podsieci. Następne pulpit zdalny do maszyny wirtualnej i sprawdza, czy istnieje połączenie z jednym z poniższych zasobów w ramach subskrypcji platformy Azure
    * Obiekt BLOB usługi Azure Storage
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > Po zweryfikowaniu łączności upewnij się, że usunięto wszystkie zasoby wdrożone w podsieci przed wdrożeniem API Management w podsieci.

* **Aktualizacje przyrostowe**: w przypadku wprowadzania zmian w sieci należy zapoznać się z [interfejsem API NetworkStatus](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus), aby sprawdzić, czy usługa API Management nie utraciła dostępu do żadnych krytycznych zasobów, od których zależy. Stan łączności powinien być aktualizowany co 15 minut.

* **Linki nawigacji zasobów**: podczas wdrażania do Menedżer zasobów stylu sieci wirtualnej, API Management rezerwuje podsieć przez utworzenie linku nawigacji do zasobów. Jeśli podsieć zawiera już zasób od innego dostawcy, wdrożenie zakończy **się niepowodzeniem**. Podobnie po przeniesieniu usługi API Management do innej podsieci lub usunięciu jej zostanie usunięte łącze nawigacyjne tego zasobu.

## <a name="subnet-size"></a> Wymagania dotyczące rozmiaru podsieci
Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach i nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów oraz trzy kolejne adresy używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure, każde wystąpienie usługi API Management w podsieci używa dwóch adresów IP na jednostkę SKU Premium lub jeden adres IP dla jednostki SKU dewelopera. Każde wystąpienie rezerwuje dodatkowy adres IP dla zewnętrznego modułu równoważenia obciążenia. Podczas wdrażania w wewnętrznej sieci wirtualnej wymaga dodatkowego adresu IP dla wewnętrznego modułu równoważenia obciążenia.

W przypadku obliczenia przekraczającego minimalny rozmiar podsieci, w której można wdrożyć API Management to/29, które udostępniają trzy użyteczne adresy IP.

## <a name="routing"></a> Routing
+ Publiczny adres IP ze zrównoważonym obciążeniem (VIP) zostanie zarezerwowany w celu zapewnienia dostępu do wszystkich punktów końcowych usługi.
+ Adres IP z zakresu adresów IP podsieci (DIP) będzie używany do uzyskiwania dostępu do zasobów w sieci wirtualnej, a publiczny adres IP (VIP) będzie używany do uzyskiwania dostępu do zasobów poza siecią wirtualną.
+ Publiczny adres IP ze zrównoważonym obciążeniem można znaleźć w bloku przegląd/podstawy w Azure Portal.

## <a name="limitations"> </a>Ograniczenia
* Podsieć zawierająca wystąpienia API Management nie może zawierać żadnych innych typów zasobów platformy Azure.
* Podsieć i usługa API Management muszą znajdować się w tej samej subskrypcji.
* Podsieć zawierająca wystąpienia API Management nie może zostać przeniesiona między subskrypcjami.
* W przypadku wdrożeń wieloregionowych API Management skonfigurowanych w trybie wewnętrznej sieci wirtualnej użytkownicy są odpowiedzialni za Zarządzanie równoważeniem obciążenia w wielu regionach, które są właścicielami routingu.
* Połączenie z zasobu w sieci wirtualnej sieci równorzędnej w innym regionie w celu API Management usługi w trybie wewnętrznym nie będzie działało z powodu ograniczenia platformy. Aby uzyskać więcej informacji, zobacz [zasoby w jednej sieci wirtualnej nie mogą komunikować się z wewnętrznym modułem równoważenia obciążenia platformy Azure w równorzędnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ips"></a> Adresy IP płaszczyzny kontroli

Adresy IP są podzielone przez **środowisko platformy Azure**. W przypadku zezwolenia na adres IP żądań przychodzących oznaczony przy użyciu **szablonu globalnego** musi być listy dozwolonych wraz z adresem IP specyficznym dla **regionu** .

| **Środowisko platformy Azure**|   **Region**|  **Adres IP**|
|-----------------|-------------------------|---------------|
| Azure Public| Południowo-środkowe stany USA (globalne)| 104.214.19.224|
| Azure Public| Północno-środkowe stany USA (globalne)| 52.162.110.80|
| Azure Public| Zachodnio-środkowe stany USA| 52.253.135.58|
| Azure Public| Korea Środkowa| 40.82.157.167|
| Azure Public| Zachodnie Zjednoczone Królestwo| 51.137.136.0|
| Azure Public| Japonia Zachodnia| 40.81.185.8|
| Azure Public| Północno-środkowe stany USA| 40.81.47.216|
| Azure Public| Południowe Zjednoczone Królestwo| 51.145.56.125|
| Azure Public| Indie Zachodnie| 40.81.89.24|
| Azure Public| Wschodnie stany USA| 52.224.186.99|
| Azure Public| Europa Zachodnia| 51.145.179.78|
| Azure Public| Japonia Wschodnia| 52.140.238.179|
| Azure Public| Francja Środkowa| 40.66.60.111|
| Azure Public| Kanada Wschodnia| 52.139.80.117|
| Azure Public| Północne Zjednoczone Emiraty Arabskie| 20.46.144.85|
| Azure Public| Brazylia Południowa| 191.233.24.179|
| Azure Public| Azja Południowo-Wschodnia| 40.90.185.46|
| Azure Public| Północna Republika Południowej Afryki| 102.133.130.197|
| Azure Public| Kanada Środkowa| 52.139.20.34|
| Azure Public| Korea Południowa| 40.80.232.185|
| Azure Public| Indie Środkowe| 13.71.49.1|
| Azure Public| Zachodnie stany USA| 13.64.39.16|
| Azure Public| Australia Południowo-Wschodnia| 20.40.160.107|
| Azure Public| Australia Środkowa| 20.37.52.67|
| Azure Public| Indie Południowe| 20.44.33.246|
| Azure Public| Środkowe stany USA| 13.86.102.66|
| Azure Public| Australia Wschodnia| 20.40.125.155|
| Azure Public| Zachodnie stany USA 2| 51.143.127.203|
| Azure Public| Wschodnie stany USA 2 — EUAP| 52.253.229.253|
| Azure Public| Środkowe stany USA — EUAP| 52.253.159.160|
| Azure Public| Południowo-środkowe stany USA| 20.188.77.119|
| Azure Public| Wschodnie stany USA 2| 20.44.72.3|
| Azure Public| Europa Północna| 52.142.95.35|
| Azure Public| Azja Wschodnia| 52.139.152.27|
| Azure Public| Francja Południowa| 20.39.80.2|
| Azure Public| Szwajcaria Zachodnia| 51.107.96.8|
| Azure Public| Australia Środkowa 2| 20.39.99.81|
| Azure Public| Środkowy Zjednoczone Emiraty Arabskie| 20.37.81.41|
| Azure Public| Szwajcaria Północna| 51.107.0.91|
| Azure Public| Zachodnia Republika Południowej Afryki| 102.133.0.79|
| Azure Public| Niemcy Środkowo-Zachodnie| 51.116.96.0|
| Azure Public| Niemcy Północne| 51.116.0.0|
| Azure Public| Norwegia Wschodnia| 51.120.2.185|
| Azure Public| Norwegia Zachodnia| 51.120.130.134|
| Azure w Chinach — 21Vianet| Chiny Północne (globalna)| 139.217.51.16|
| Azure w Chinach — 21Vianet| Chiny Wschodnie (globalna)| 139.217.171.176|
| Azure w Chinach — 21Vianet| Chiny Północne| 40.125.137.220|
| Azure w Chinach — 21Vianet| Chiny Wschodnie| 40.126.120.30|
| Azure w Chinach — 21Vianet| Chiny Północne 2| 40.73.41.178|
| Azure w Chinach — 21Vianet| Chiny Wschodnie 2| 40.73.104.4|
| Azure Government| USGov Wirginia (globalna)| 52.127.42.160|
| Azure Government| USGov Texas (globalna)| 52.127.34.192|
| Azure Government| USGov Wirginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD środkowe| 52.182.32.132|
| Azure Government| USDoD wschód| 52.181.32.192|

## <a name="related-content"> </a>Powiązana zawartość
* [Łączenie Virtual Network z zapleczem przy użyciu usługi VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Łączenie Virtual Network z różnych modeli wdrażania](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Jak śledzić wywołania na platformie API Management Azure za pomocą Inspektora interfejsu API](api-management-howto-api-inspector.md)
* [Virtual Network często zadawane pytania](../virtual-network/virtual-networks-faq.md)
* [Tagi usług](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
