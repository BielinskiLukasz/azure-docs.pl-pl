---
title: Zagadnienia dotyczące zabezpieczeń w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym artykule opisano podstawowe zabezpieczeń infrastruktury, używanego przez usługi przenoszenia danych w usłudze Azure Data Factory w celu zabezpieczania danych.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: 70225fd59248939c9ea1d5c7c267cdf0da3303e7
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342406"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Zagadnienia dotyczące zabezpieczeń w przypadku przenoszenia danych w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-data-movement-security-considerations.md)
> * [Bieżąca wersja](data-movement-security-considerations.md)

W tym artykule opisano podstawowe zabezpieczeń infrastruktury, używanego przez usługi przenoszenia danych w usłudze Azure Data Factory w celu zabezpieczania danych. Zasoby zarządzania usługi Data Factory są oparte na infrastrukturze zabezpieczeń platformy Azure i używać wszystkich możliwych zabezpieczenia oferowanych na platformie Azure.

W ramach rozwiązania fabryki danych jest tworzony co najmniej jeden [potok](concepts-pipelines-activities.md) danych. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Te potoki znajdują się w regionie, w której został utworzony z fabryką danych. 

Mimo, że usługi Data Factory jest dostępna tylko w kilku regionach, usługi data movement service jest [dostępna globalnie](concepts-integration-runtime.md#integration-runtime-location) w celu zapewnienia zgodności danych, wydajność i mniejsze sieci ruch wychodzący kosztów. 

Usługa Azure Data Factory nie przechowuje żadnych danych, z wyjątkiem poświadczeń połączonej usługi dla magazynami danych w chmurze, które są szyfrowane przy użyciu certyfikatów. Dzięki usłudze Data Factory, można tworzyć oparte na danych przepływy pracy do aranżowania przenoszenia danych między [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats)i przetwarzania danych przy użyciu [usług obliczeniowych](compute-linked-services.md) w innych regionach lub w w środowisku lokalnym. Można również monitorować i zarządzać przepływów pracy za pomocą zestawów SDK i usługi Azure Monitor.

Przenoszenie danych za pomocą usługi Data Factory jest certyfikowany dla:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Jeśli interesuje Cię zgodności platformy Azure i jak Azure zabezpiecza własną infrastrukturę, odwiedź stronę [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

W tym artykule omówimy zagadnienia dotyczące zabezpieczeń w następujących scenariuszach przenoszenia danych w dwóch: 

- **Scenariusz chmury**: W tym scenariuszu, zarówno w źródle, jak i w lokalizacji docelowej są publicznie dostępne za pośrednictwem Internetu. Obejmują one zarządzana usługa w chmurze usługi magazynu, np. usługi Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, usług SaaS, takich jak Salesforce i protokołów sieci web, takich jak FTP i OData. Znajdź pełną listę obsługiwanych źródeł danych w [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats).
- **Scenariusza hybrydowego**: W tym scenariuszu źródła lub lokalizacji docelowej jest za zaporą lub wewnątrz sieci firmowej w środowisku lokalnym. Lub magazynie danych jest w prywatnej sieci lub sieci wirtualnej (w większości przypadków źródła) i nie jest dostępny publicznie. Serwery bazy danych hostowanych na maszynach wirtualnych również wchodzą w zakres tego scenariusza.

## <a name="cloud-scenarios"></a>Scenariusze chmury

### <a name="securing-data-store-credentials"></a>Zabezpieczanie poświadczeń magazynu danych

- **Store zaszyfrowanych poświadczeń w magazynie usługi Azure Data Factory zarządzanych**. Data Factory pomaga chronić poświadczenia magazynu danych, szyfrując je za pomocą certyfikatów, zarządzany przez firmę Microsoft. Te certyfikaty są obracane dwa lata (obejmujących też odnawiania certyfikatu, a także możliwość migracji poświadczeń). Zaszyfrowane poświadczenia są bezpiecznie przechowywane w koncie magazynu platformy Azure, zarządzane przez usługę Azure Data Factory, usługi zarządzania. Aby uzyskać więcej informacji na temat zabezpieczeń usługi Azure Storage, zobacz [Omówienie zabezpieczeń usługi Azure Storage](../security/security-storage-overview.md).
- **Store poświadczeń w usłudze Azure Key Vault**. Można również przechowywać poświadczenia magazynu danych w [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Fabryka danych umożliwia pobranie poświadczeń podczas wykonywania działania. Aby uzyskać więcej informacji, zobacz [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Szyfrowanie danych podczas przesyłania
Jeśli magazynu danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie dane transferu między usługi przenoszenia danych w usłudze Data Factory się z magazynem danych w chmurze za pośrednictwem bezpiecznego kanału protokołu HTTPS lub TLS.

> [!NOTE]
> Wszystkie połączenia usługi Azure SQL Database i Azure SQL Data Warehouse wymagają szyfrowania (SSL/TLS), podczas gdy dane są przesyłane do i z bazy danych. Gdy jesteś tworzenie potoku przy użyciu pliku JSON, Dodaj właściwość szyfrowania i ustaw ją na **true** w parametrach połączenia. W przypadku usługi Azure Storage, można użyć **HTTPS** w parametrach połączenia.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku
Niektóre dane są przechowywane Obsługa szyfrowania nieużywanych danych. Firma Microsoft zaleca, aby włączyć mechanizm szyfrowania danych dla tych magazynów danych. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Przezroczystego szyfrowania danych (TDE) w usłudze Azure SQL Data Warehouse pomaga zapewnić ochronę przed złośliwymi działaniami, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie danych magazynowanych. To zachowanie jest niewidoczne dla klienta. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Usługa Azure SQL Database obsługuje również przezroczyste szyfrowanie danych (TDE), która pomaga chronić przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie danych, bez konieczności wprowadzania zmian do aplikacji. To zachowanie jest niewidoczne dla klienta. Aby uzyskać więcej informacji, zobacz [przezroczyste szyfrowanie danych dla bazy danych SQL Database i Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store umożliwia także szyfrowanie danych przechowywanych na koncie. Po włączeniu Data Lake Store automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje je przed pobierania, ustawiania przezroczystości do klienta, który uzyskuje dostęp do danych. Aby uzyskać więcej informacji, zobacz [zabezpieczeń w usłudze Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Usługa Azure Blob storage i Azure Table storage
Usługa Azure Blob storage i Azure Table storage obsługuje szyfrowanie usługi Storage (SSE), który automatycznie szyfruje dane przed utrwaleniem ich w magazynie i odszyfrowuje przed pobierania. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 obsługuje klienta i serwera szyfrowanie danych magazynowanych. Aby uzyskać więcej informacji, zobacz [ochrony danych przy użyciu szyfrowania](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Usługi Amazon Redshift obsługuje klaster szyfrowanie danych magazynowanych. Aby uzyskać więcej informacji, zobacz [szyfrowania bazy danych usługi Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>SalesForce
SalesForce obsługuje szyfrowanie platformy Shield, który umożliwia szyfrowanie wszystkich plików załączników i pola niestandardowe. Aby uzyskać więcej informacji, zobacz [zrozumienie przepływu uwierzytelniania OAuth serwera sieci Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Scenariusze hybrydowe
Hybrydowe scenariusze wymagają własnego środowiska integration runtime instalowanego w sieci lokalnej w sieci wirtualnej (Azure) lub w wirtualnej chmurze prywatnej (Amazon). Własne środowisko integration runtime musi mieć możliwość dostępu do magazynów danych lokalnych. Aby uzyskać więcej informacji na temat własnego środowiska integration runtime, zobacz [jak utworzyć i skonfigurować samodzielnie hostowane środowisko IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![Self-Hosted integration runtime kanałów](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kanał polecenia umożliwia komunikację między usługi przenoszenia danych w usłudze Data Factory i własne środowisko integration runtime. Komunikat zawiera informacje dotyczące działania. Kanał danych jest używana do przesyłania danych między magazynami danych w środowisku lokalnym i magazynami danych w chmurze.    

### <a name="on-premises-data-store-credentials"></a>Poświadczenia magazynu danych w środowisku lokalnym
Poświadczenia dla swoich lokalnych magazynów danych są zawsze zaszyfrowane i przechowywane. One mogą być przechowywane lokalnie na maszynie Self-Hosted integration runtime albo przechowywane w magazynie usługi Azure Data Factory zarządzane (tak samo jak poświadczenia magazynu w chmurze). 

- **Store poświadczenia lokalnie**. Szyfrowanie i przechowywanie poświadczeń lokalnie na własnym środowisku integration runtime, wykonaj kroki opisane w [szyfrowanie poświadczeń dla lokalnych magazynów danych w usłudze Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Wszystkie łączniki obsługuje tę opcję. Windows korzysta z własnego środowiska integration runtime [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) do szyfrowania informacji poufnych danych i poświadczeń. 

   Użyj **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** polecenia cmdlet do szyfrowania poświadczeń połączonej usługi i poufnych informacji w połączonej usłudze. Następnie można użyć do zwróconego kodu JSON (przy użyciu **EncryptedCredential** elementu w parametrach połączenia) aby utworzyć połączoną usługę przy użyciu **Set-AzureRmDataFactoryV2LinkedService** polecenia cmdlet.  

- **Store w magazynie usługi Azure Data Factory zarządzać**. Jeśli używasz bezpośrednio **Set-AzureRmDataFactoryV2LinkedService** polecenia cmdlet z połączeniem ciągi i poświadczeniami bezpośrednio w kodzie JSON, połączonej usługi jest zaszyfrowane i przechowywane w magazynie usługi Azure Data Factory zarządzane. Certyfikat nadal szyfrowany poufne informacje, a firma Microsoft zarządza tymi certyfikatami.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porty używane podczas szyfrowania połączoną usługę na własne środowisko integration runtime
Domyślnie program PowerShell używa portu 8050 na komputerze przy użyciu własnego środowiska integration runtime na potrzeby bezpiecznej komunikacji. Jeśli to konieczne, można zmienić ten port.  

![Port HTTPS dla bramy](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Wszystkie transfery danych są za pośrednictwem bezpiecznego kanału protokołu HTTPS i TLS za pośrednictwem protokołu TCP, aby zapobiec atakom man-in--middle podczas komunikowania się z usługami platformy Azure.

Można również użyć [protokołu IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) lub [usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md) dodatkowo zabezpieczyć kanał komunikacyjny między siecią lokalną i platformy Azure.

Usługa Azure Virtual Network jest logicznym odzwierciedleniem Twojej sieci w chmurze. Łączenie z sieci lokalnych z siecią wirtualną, konfigurując sieci VPN IPSec (site-to-site) lub usługi ExpressRoute (prywatnej komunikacji równorzędnej).    

Poniższa tabela zawiera podsumowanie sieci i zaleceń dotyczących Self-Hosted integration runtime konfiguracji na podstawie różnych kombinacji źródłowe i docelowe lokalizacje hybrydowe przenoszenie danych.

| Element źródłowy      | Element docelowy                              | Konfiguracja sieci                    | Instalacja środowiska Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | Sieć VPN IPSec (point-to-site lub site-to-site) | Własne środowisko integration runtime może być zainstalowana lokalnie lub na maszynie wirtualnej platformy Azure w sieci wirtualnej. |
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | ExpressRoute (prywatnej komunikacji równorzędnej)           | Własne środowisko integration runtime może być zainstalowana lokalnie lub na maszynie wirtualnej platformy Azure w sieci wirtualnej. |
| Lokalnie | Usługi oparte na platformie Azure, z publicznym punktem końcowym | ExpressRoute (publicznej komunikacji równorzędnej)            | Własne środowisko integration runtime musi być zainstalowane w środowisku lokalnym. |

Na poniższych ilustracjach przedstawiono użycie własnego środowiska integration runtime do przenoszenia danych między lokalną bazą danych i usług platformy Azure przy użyciu usługi ExpressRoute i sieci VPN IPSec (usługa Azure Virtual Network):

**ExpressRoute**

![Korzystać z usługi ExpressRoute za pomocą bramy](media/data-movement-security-considerations/express-route-for-gateway.png) 

**Sieć VPN IPSec**

![Sieć VPN IPSec za pomocą bramy](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Konfiguracje zapory i Lista dozwolonych adresów IP

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Wymagania dotyczące zapory w sieci o lokalne prywatny  
W przedsiębiorstwie zapory firmowej działa na routerze centralnej w organizacji. Zapora Windows działa jako demon na komputerze lokalnym, w którym zainstalowano własnego środowiska integration runtime. 

W poniższej tabeli przedstawiono wychodzącego wymagania dotyczące portów i domen dla zapór w firmach:

| Nazwy domen                  | Porty ruchu wychodzącego | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Wymagane przez własne środowisko integration runtime, aby nawiązać połączenie usługi przenoszenia danych w usłudze Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Wymagane przez własne środowisko integration runtime, aby nawiązać połączenie z usługą Data Factory. |
| `download.microsoft.com`    | 443            | Wymagane przez własne środowisko integration runtime do pobierania aktualizacji. Wyłączenie automatycznej aktualizacji następnie możesz pominąć ten. |
| `*.core.windows.net`          | 443            | Używane przez własne środowisko integration runtime do łączenia z kontem magazynu platformy Azure, korzystając z [kopiowania etapowego](copy-activity-performance.md#staged-copy) funkcji. |
| `*.database.windows.net`      | 1433           | (Opcjonalnie) Wymagana podczas kopiowania z lub do usługi Azure SQL Database lub Azure SQL Data Warehouse. Funkcja kopiowania przejściowego służy do kopiowania danych do usługi Azure SQL Database lub Azure SQL Data Warehouse bez konieczności otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Opcjonalnie) Wymagana podczas kopiowania z lub do usługi Azure Data Lake Store. |

> [!NOTE] 
> Trzeba zarządzać portów lub domen umieszczania na białej liście na poziomie zaporą firmową zgodnie z wymaganiami przez źródła odpowiednich danych. Ta tabela używa tylko usługi Azure SQL Database, Azure SQL Data Warehouse i Azure Data Lake Store jako przykłady.   

W poniższej tabeli przedstawiono wymagania dotyczące portów dla ruchu przychodzącego zapory Windows:

| Porty wejściowe | Opis                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Wymagane przez polecenie cmdlet programu PowerShell szyfrowania, zgodnie z opisem w [szyfrowanie poświadczeń dla lokalnych magazynów danych w usłudze Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md), jak również aplikacji Menedżer poświadczeń, aby bezpiecznie ustawić poświadczenia dla lokalnych magazynów danych na własne środowisko integration runtime. |

![Wymagania dotyczące portów bramy](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>Konfiguracje adresów IP i umieszczania na białej liście w magazynach danych
Niektóre magazyny danych w chmurze również wymagają tej należy umieścić na liście dozwolonych adres IP komputera, uzyskiwanie dostępu do magazynu. Upewnij się, że adres IP komputera Self-Hosted integration runtime jest umieszczona na białej liście, lub odpowiednio skonfigurowane w zaporze.

Następujące magazynami danych w chmurze wymagają, że możesz umieścić na liście dozwolonych adres IP komputera Self-Hosted integration runtime. Niektóre z tych magazynów danych, domyślnie, może nie wymagać umieszczania na białej liście. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Własne środowisko integration runtime można współdzielić w ramach fabryki danych?**

Firma Microsoft nie obsługuje tej funkcji jeszcze. Aktywnie pracujemy nad jej.

**Jakie są wymagania dotyczące portów dla własnego środowiska integration runtime do pracy?**

Własne środowisko integration runtime sprawia, że połączeń opartych na protokole HTTP, dostęp do Internetu. Porty ruchu wychodzącego 443 i 80 należy otworzyć dla własnego środowiska integration runtime to połączenie. Otwórz port wejściowy 8050 tylko na poziomie komputera (nie na poziomie zapory firmowej) dla aplikacji Menedżer poświadczeń. Azure SQL Database lub Azure SQL Data Warehouse jest używany jako źródło lub miejsce docelowe, należy otworzyć również port 1433. Aby uzyskać więcej informacji, zobacz [zapory, konfiguracje i listy dozwolonych adresów IP](#firewall-configurations-and-whitelisting-ip-address-of-gateway) sekcji. 


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje dotyczące wydajności działania kopiowania w fabryce danych Azure, zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](copy-activity-performance.md).

 
