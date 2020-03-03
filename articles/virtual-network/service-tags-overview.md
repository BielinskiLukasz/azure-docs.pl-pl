---
title: Omówienie tagów usługi platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o tagach usługi. Tagi usług pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 2af887f529d57052d8b729fa1a613e8eeb9003ea
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228291"
---
# <a name="virtual-network-service-tags"></a>Tagi usługi sieci wirtualnej
<a name="network-service-tags"></a>

Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych.

Możesz użyć tagów usługi do definiowania kontroli dostępu do sieci w [sieciowych grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) lub [Zapora platformy Azure](https://docs.microsoft.com/azure/firewall/service-tags). Podczas tworzenia reguł zabezpieczeń należy używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **ApiManagement**) w odpowiednim polu *źródłowym* lub *miejscu docelowym* reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić.

Tagi usług umożliwiają uzyskanie izolacji sieci i ochronę zasobów platformy Azure z ogólnego Internetu podczas uzyskiwania dostępu do usług platformy Azure z publicznymi punktami końcowymi. Utwórz przychodzące/wychodzące reguły sieciowej grupy zabezpieczeń, aby odmówić ruch do/z **Internetu** i zezwolić na ruch do/z **AzureCloud** lub innych [dostępnych tagów usługi](#available-service-tags) określonych usług platformy Azure.

## <a name="available-service-tags"></a>Dostępne Tagi usług
Poniższa tabela zawiera wszystkie Tagi usługi dostępne do użycia w regułach [sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Kolumny wskazują, czy tag:

- Jest odpowiednie dla reguł obejmujących ruch przychodzący lub wychodzący.
- Obsługuje zakres [regionalny](https://azure.microsoft.com/regions) .
- Jest użyteczny w regułach [zapory platformy Azure](https://docs.microsoft.com/azure/firewall/service-tags) .

Domyślnie Tagi usług odzwierciedlają zakresy dla całej chmury. Niektóre Tagi usług umożliwiają również dokładniejszą kontrolę poprzez ograniczenie odpowiednich zakresów adresów IP do określonego regionu. Na przykład **Magazyn** znacznika usługi reprezentuje usługę Azure Storage dla całej chmury, ale w obszarze **Magazyn. Zachodnie** zawęża zakres tylko do zakresów adresów IP magazynu z regionu zachód. Poniższa tabela wskazuje, czy każdy tag usługi obsługuje taki zakres regionalny.  

| Tag | Przeznaczenie | Może korzystać z ruchu przychodzącego lub wychodzącego? | Może być regionalna? | Czy można używać z zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Ruch związany z zarządzaniem dla wdrożeń dedykowanych API Management platformy Azure. <br/><br/>*Uwaga:* Ten tag reprezentuje punkt końcowy usługi Azure API Management dla płaszczyzny kontroli na region. Dzięki temu klienci mogą wykonywać operacje związane z zarządzaniem w ramach interfejsów API, operacji, zasad, NamedValues skonfigurowanych w usłudze API Management.  | Przychodzący | Yes | Yes |
| **ApplicationInsightsAvailability** | Dostępność Application Insights. | Przychodzący | Nie | Nie |
| **AppService**    | Usługa Azure App Service. Ten tag jest zalecany w przypadku wychodzących reguł zabezpieczeń do frontonów aplikacji sieci Web. | Wychodzący | Yes | Yes |
| **AppServiceManagement** | Ruch związany z zarządzaniem dla wdrożeń przeznaczonych dla App Service Environment. | Obie | Nie | Yes |
| **Usługi azureactivedirectory** | Azure Active Directory. | Wychodzący | Nie | Yes |
| **AzureActiveDirectoryDomainServices** | Ruch związany z zarządzaniem dla wdrożeń przeznaczonych dla Azure Active Directory Domain Services. | Obie | Nie | Yes |
| **AzureAdvancedThreatProtection** | Zaawansowana ochrona przed zagrożeniami na platformie Azure. | Wychodzący | Nie | Nie |
| **AzureBackup** |Azure Backup.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **Storage** i **usługi azureactivedirectory** . | Wychodzący | Nie | Yes |
| **AzureBotService** | Azure Bot Service. | Wychodzący | Nie | Nie |
| **AzureCloud** | Wszystkie [publiczne adresy IP centrum](https://www.microsoft.com/download/details.aspx?id=56519)danych. | Wychodzący | Yes | Yes |
| **AzureCognitiveSearch** | Wyszukiwanie poznawcze platformy Azure. <br/><br/>Ten tag lub adresy IP objęte tym tagiem mogą służyć do przyznawania indeksatorów bezpiecznego dostępu do źródeł danych. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą połączenia indeksatora](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) . | Przychodzący | Nie | Nie |
| **AzureConnectors** | Azure Logic Apps łączniki dla połączeń sondy/zaplecza. | Przychodzący | Yes | Yes |
| **AzureContainerRegistry** | Azure Container Registry. | Wychodzący | Yes | Yes |
| **AzureCosmosDB** | Azure Cosmos DB. | Wychodzący | Yes | Yes |
| **AzureDatabricks** | Azure Databricks. | Obie | Nie | Nie |
| **AzureDataExplorerManagement** | Zarządzanie Eksplorator danych platformy Azure. | Przychodzący | Nie | Nie |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Wychodzący | Nie | Yes |
| **AzureEventGrid** | Azure Event Grid. <br/><br/>*Uwaga:* Ten tag obejmuje Azure Event Grid punkty końcowe w regionach Południowo-środkowe stany USA, Wschodnie stany USA 2, zachodnie stany USA 2 i stany USA. | Obie | Nie | Nie |
| **AzureFrontDoor** | Moje drzwi platformy Azure. | Obie | Nie | Nie |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **usługi azureactivedirectory** i **AzureFrontDoor. frontonu** . Dozwolonych również następujące adresy IP (ta zależność zostanie usunięta wkrótce): 13.107.6.181 & 13.107.9.181. | Wychodzący | Nie | Nie |
| **AzureIoTHub** | IoT Hub platformy Azure. | Wychodzący | Nie | Nie |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Uwaga:* Ten tag ma zależność od znacznika **usługi azureactivedirectory** . | Wychodzący | Yes | Yes |
| **AzureLoadBalancer** | Moduł równoważenia obciążenia infrastruktury platformy Azure. Tag tłumaczy na [wirtualny adres IP hosta](security-overview.md#azure-platform-considerations) (168.63.129.16), z którego pochodzą sondy kondycji platformy Azure. Nie obejmuje to ruchu do zasobu Azure Load Balancer. Jeśli nie używasz Azure Load Balancer, możesz zastąpić tę regułę. | Obie | Nie | Nie |
| **AzureMachineLearning** | Azure Machine Learning. | Obie | Nie | Yes |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon i metryki niestandardowe (punkty końcowe rdzeniowym procesorem).<br/><br/>*Uwaga:* W przypadku Log Analytics ten tag ma zależność od tagu **magazynu** . | Wychodzący | Nie | Yes |
| **AzurePlatformDNS** | Podstawowa infrastruktura (domyślna) usługi DNS.<br/><br>Za pomocą tego tagu można wyłączyć domyślny system DNS. Należy zachować ostrożność w przypadku użycia tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Zalecamy również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), czyli podstawowa usługa infrastruktury.<br/><br/>Ten tag służy do wyłączania domyślnego IMDS. Należy zachować ostrożność w przypadku użycia tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Zalecamy również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformLKM** | Licencjonowanie systemu Windows lub usługa zarządzania kluczami.<br/><br/>Ten tag służy do wyłączania ustawień domyślnych dla licencjonowania. Należy zachować ostrożność w przypadku użycia tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Zalecamy również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzureResourceManager** | Azure Resource Manager. | Wychodzący | Nie | Nie |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **Storage**, **usługi azureactivedirectory**i **EventHub** . | Wychodzący | Nie | Nie |
| **AzureTrafficManager** | Adresy IP badania Traffic Manager platformy Azure.<br/><br/>Aby uzyskać więcej informacji na temat Traffic Manager adresów IP sondowania, zobacz [często zadawane pytania dotyczące usługi Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Przychodzący | Nie | Yes |  
| **BatchNodeManagement** | Ruch związany z zarządzaniem dla wdrożeń przeznaczonych dla Azure Batch. | Obie | Nie | Yes |
| **CognitiveServicesManagement** | Zakresy adresów dla ruchu dla Cognitive Services platformy Azure. | Wychodzący | Nie | Nie |
| **Dynamics365ForMarketingEmail** | Zakresy adresów dla usługi marketingowej poczty programu Dynamics 365. | Wychodzący | Yes | Nie |
| **ElasticAFD** | Elastyczne drzwi platformy Azure. | Obie | Nie | Nie |
| **EventHub** | Event Hubs platformy Azure. | Wychodzący | Yes | Yes |
| **Bramamanager** | Ruch związany z zarządzaniem dla wdrożeń przeznaczonych dla usługi Azure VPN Gateway i Application Gateway. | Przychodzący | Nie | Nie |
| **GuestAndHybridManagement** | Azure Automation i konfiguracja gościa. | Wychodzący | Nie | Yes |
| **HDInsight** | Usługa Azure HDInsight. | Przychodzący | Yes | Nie |
| **Internet** | Przestrzeń adresów IP, która znajduje się poza siecią wirtualną i jest dostępna przez publiczny Internet.<br/><br/>Zakres adresów obejmuje [publiczną przestrzeń adresów IP należącą do platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Obie | Nie | Nie |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Wychodzący | Nie | Nie |
| **MicrosoftContainerRegistry** | Rejestr kontenerów dla obrazów kontenerów firmy Microsoft. <br/><br/>*Uwaga:* Dozwolonych również następujący adres IP (ta zależność zostanie usunięta wkrótce): 204.79.197.219. | Wychodzący | Yes | Yes |
| **ServiceBus** | Azure Service Bus ruch korzystający z warstwy usługi Premium. | Wychodzący | Yes | Yes |
| **ServiceFabric** | Service Fabric platformy Azure.<br/><br/>*Uwaga:* Ten tag reprezentuje punkt końcowy usługi Service Fabric dla płaszczyzny kontroli na region. Dzięki temu klienci mogą wykonywać operacje związane z zarządzaniem Service Fabric klastrami z sieci wirtualnej (np. https://westus.servicefabric.azure.com) | Obie | Nie | Nie |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL i Azure SQL Data Warehouse.<br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure SQL Database, ale nie konkretną bazę danych lub serwer SQL. Ten tag nie ma zastosowania do wystąpienia zarządzanego SQL. | Wychodzący | Yes | Yes |
| **Xmlmanagement** | Ruch związany z zarządzaniem dla wdrożeń dedykowanych przez program SQL Server. | Obie | Nie | Yes |
| **Storage** | Azure Storage. <br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure Storage, ale nie konkretne konto usługi Azure Storage. | Wychodzący | Yes | Yes |
| **VirtualNetwork** | Przestrzeń adresowa sieci wirtualnej (wszystkie zakresy adresów IP zdefiniowane dla sieci wirtualnej), wszystkie połączone lokalne przestrzenie adresowe, [równorzędne](virtual-network-peering-overview.md) sieci wirtualne, sieci wirtualne połączone z [bramą sieci wirtualnej](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), [wirtualny adres IP hosta](security-overview.md#azure-platform-considerations)i prefiksy adresów używane na [trasach zdefiniowanych przez użytkownika](virtual-networks-udr-overview.md). Ten tag może również zawierać trasy domyślne. | Obie | Nie | Nie |

>[!NOTE]
>W klasycznym modelu wdrażania (przed Azure Resource Manager) jest obsługiwany podzestaw tagów wymienionych w poprzedniej tabeli. Te Tagi są napisane inaczej:
>
>| Pisownia klasyczna | Odpowiednik tagu Menedżer zasobów |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Tagi usług platformy Azure oznaczają prefiksy adresów z określonej chmury. Na przykład bazowe zakresy adresów IP, które odpowiadają wartości tagu **SQL** w chmurze publicznej platformy Azure, będą inne niż bazowe zakresy w chmurze platformy Azure w Chinach.

> [!NOTE]
> W przypadku zaimplementowania [punktu końcowego usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md) dla usługi takiej jak usługa Azure Storage lub Azure SQL Database, platforma Azure dodaje [trasę](virtual-networks-udr-overview.md#optional-default-routes) do podsieci sieci wirtualnej dla usługi. Prefiksy adresów w marszrucie to te same prefiksy adresów lub zakresy CIDR, jak w przypadku odpowiedniego tagu usługi.

## <a name="service-tags-on-premises"></a>Lokalne znaczniki usług  
Możesz uzyskać informacje o bieżącym znaczniku i zakresie usługi, aby uwzględnić je w ramach konfiguracji zapory lokalnej. Te informacje to bieżąca lista punktów adresów IP, które odpowiadają każdemu tagowi usługi. Możesz uzyskać informacje programowo lub za pomocą pobierania pliku JSON, zgodnie z opisem w poniższych sekcjach.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Korzystanie z interfejsu API odnajdywania tagów usługi (publiczna wersja zapoznawcza)
Można programowo pobrać bieżącą listę tagów usługi wraz ze szczegółami zakresu adresów IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> W publicznej wersji zapoznawczej interfejs API odnajdywania może zwracać informacje, które są mniej aktualne niż informacje zwrócone przez pobrane dane JSON. (Zobacz następną sekcję).


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Odnajdywanie tagów usługi przy użyciu plików JSON do pobrania 
Możesz pobrać pliki JSON zawierające bieżącą listę tagów usługi wraz ze szczegółami zakresu adresów IP. Te listy są aktualizowane i publikowane co tydzień. Lokalizacje dla każdej chmury są następujące:

- [Usługa Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Wersja platformy Azure dla administracji USA](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Chińska wersja platformy Azure](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Podzestaw tych informacji został opublikowany w plikach XML dla usług [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Chiny](https://www.microsoft.com/download/details.aspx?id=42064)i [Azure (Niemcy](https://www.microsoft.com/download/details.aspx?id=54770)). Te pobrania XML będą przestarzałe do 30 czerwca 2020 i nie będą już dostępne po tej dacie. Należy przeprowadzić migrację do programu przy użyciu interfejsu API odnajdywania lub plików JSON, jak opisano w poprzednich sekcjach.

### <a name="tips"></a>Porady 
- Możesz wykryć aktualizacje z jednej publikacji do następnej, zwracając więcej wartości *changeNumber* w pliku JSON. Każda podsekcja (na przykład **Storage. zachodni**) ma własne *changeNumber* , które są zwiększane, gdy wystąpią zmiany. Najwyższy poziom *changeNumber* pliku jest zwiększany, gdy dowolna z podsekcji jest zmieniana.
- Aby zapoznać się z przykładami sposobu analizowania informacji o tagu usługi (na przykład Pobierz wszystkie zakresy adresów dla magazynu w zachodnich), zobacz dokumentację [interfejsu API odnajdywania tagów usług](https://aka.ms/discoveryapi_powershell) w programie PowerShell.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [utworzyć sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md).
