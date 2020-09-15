---
title: Strategie dostępu do danych
description: Azure Data Factory teraz obsługuje zakresy statycznych adresów IP.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 76181f089511a6645a51707f9a8537c1589d82bf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484956"
---
# <a name="data-access-strategies"></a>Strategie dostępu do danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Istotnym celem zabezpieczeń organizacji jest ochrona swoich magazynów danych przed przypadkowym dostępem przez Internet. może to być magazyn danych lokalnych lub w chmurze/SaaS. 

Zwykle magazyn danych w chmurze kontroluje dostęp przy użyciu poniższych mechanizmów:
* Prywatne łącze z Virtual Network do prywatnych punktów końcowych z włączonymi źródłami danych
* Reguły zapory ograniczające łączność przy użyciu adresu IP
* Mechanizmy uwierzytelniania, które wymagają, aby użytkownicy udowadniali swoją tożsamość
* Mechanizmy autoryzacji, które ograniczają użytkowników do określonych akcji i danych

> [!TIP]
> [Wprowadzając zakres statycznego adresu IP](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses), możesz teraz zezwolić na listę zakresów adresów IP dla określonego regionu Azure Integration Runtime, aby upewnić się, że nie trzeba zezwalać na wszystkie adresy IP platformy Azure w magazynach danych w chmurze. W ten sposób można ograniczyć adresy IP, które mogą uzyskiwać dostęp do magazynów danych.

> [!NOTE] 
> Zakresy adresów IP są blokowane dla Azure Integration Runtime i są obecnie używane tylko w przypadku przenoszenia danych, potoku i działań zewnętrznych. Przepływy pracy i Azure Integration Runtime, które umożliwiają zarządzanie Virtual Network teraz nie używają tych zakresów adresów IP. 

Ta czynność powinna działać w wielu scenariuszach. wiemy, że unikatowy statyczny adres IP dla środowiska Integration Runtime jest pożądany, ale nie będzie można użyć Azure Integration Runtime obecnie, który jest bezserwerowy. W razie potrzeby można zawsze skonfigurować samodzielny Integration Runtime i korzystać z niego przy użyciu statycznego adresu IP. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Strategie dostępu do danych za poorednictwem Azure Data Factory

* **[Link prywatny](https://docs.microsoft.com/azure/private-link/private-link-overview)** — można utworzyć Azure Integration Runtime w ramach Azure Data Factory zarządzanym Virtual Network i wykorzystać prywatne punkty końcowe do bezpiecznego łączenia się z obsługiwanymi magazynami danych. Ruch między zarządzanymi Virtual Networkami i źródłami danych porusza się w sieci szkieletowej firmy Microsoft i nie jest narażony na sieć publiczną.
* **[Usługa zaufana](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** — Magazyn Azure (Blob, ADLS Gen2) obsługuje konfigurację zapory, która umożliwia bezpieczne wybieranie zaufanych usług platformy Azure w celu bezpiecznego uzyskiwania dostępu do konta magazynu. Usługi zaufane wymuszają uwierzytelnianie tożsamości zarządzanej, co gwarantuje, że żadna inna Fabryka danych nie może połączyć się z tym magazynem, chyba że listy dozwolonych się to przy użyciu tożsamości zarządzanej. Więcej szczegółów można znaleźć w **[tym blogu](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)**. W związku z tym jest to niezwykle bezpieczne i zalecane. 
* **Unikatowy statyczny adres IP** — należy skonfigurować własne środowisko Integration Runtime, aby uzyskać statyczny adres IP dla łączników Data Factory. Dzięki temu mechanizmowi można zablokować dostęp ze wszystkich innych adresów IP. 
* **[Statyczny zakres adresów IP](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** — można użyć adresów IP Azure Integration Runtime, aby zezwolić na wyświetlanie listy w magazynie (wypowiedz S3, Salesforce itp.). Na pewno ogranicza adresy IP, które mogą łączyć się z magazynami danych, ale również opierają się na regułach uwierzytelniania/autoryzacji.
* **[Tag usługi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** — tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure (np. Azure Data Factory). Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Jest to przydatne, gdy listy dozwolonych dostęp do danych w hostowanych magazynach danych IaaS w Virtual Network.
* **Zezwalaj na korzystanie z usług platformy Azure** — niektóre usługi umożliwiają nawiązywanie połączenia z usługą Azure w przypadku wybrania tej opcji. 

Aby uzyskać więcej informacji na temat obsługiwanych mechanizmów zabezpieczeń sieci w magazynach danych w Azure Integration Runtime i samoobsługowe Integration Runtime, zobacz poniżej dwóch tabel.  
* **Azure Integration Runtime**

    | Magazyny danych                  | Obsługiwany mechanizm zabezpieczeń sieci w magazynach danych | Private Link     | Usługa zaufana     | Statyczny zakres adresów IP | Tagi usługi | Zezwalaj na usługi platformy Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Magazyny danych Azure PaaS       | Azure Cosmos DB                                     | Tak              | -                   | Tak             | -            | Tak                  |
    |                              | Azure Data Explorer                                 | -                | -                   | Tak*            | Tak*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Tak             | -            | Tak                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL       | -                | -                   | Tak             | -            | Tak                  |
    |                              | Azure File Storage                                  | Tak              | -                   | Tak             | -            | .                    |
    |                              | Azure Storage (BLOB, ADLS Gen2)                     | Tak              | Tak (tylko uwierzytelnianie MSI) | Tak             | -            | .                    |
    |                              | Azure SQL DB, Azure Synapse Analytics), SQL ml  | Tak (tylko usługa Azure SQL DB/DW)        | -                   | Tak             | -            | Tak                  |
    |                              | Azure Key Vault (na potrzeby pobierania kluczy tajnych/parametrów połączenia) | tak      | Tak                 | Tak             | -            | -                    |
    | Inne magazyny danych PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage itd.    | -                | -                   | Tak             | -            | -                    |
    | Azure laaS                   | SQL Server, Oracle itd.                          | -                | -                   | Tak             | Tak          | -                    |
    | Lokalna laaS              | SQL Server, Oracle itd.                          | -                | -                   | Tak             | -            | -                    |
    
    **Dotyczy tylko sytuacji, gdy usługa Azure Eksplorator danych jest wstrzykiwana przez sieć wirtualną i zakres adresów IP można zastosować do sieciowej grupy zabezpieczeń/zapory.* 

* **Samodzielna Integration Runtime (w sieciach wirtualnych i lokalnych)**
    
    | Magazyny danych                  | Obsługiwany mechanizm zabezpieczeń sieci w magazynach danych         | Statyczny adres IP | Usługi zaufane  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Magazyny danych Azure PaaS       | Azure Cosmos DB                                               | Tak       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Tak       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | Tak       | -                   |
    |                                | Azure File Storage                                            | Tak       | -                   |
    |                                | Azure Storage (blog, ADLS Gen2)                             | Tak       | Tak (tylko uwierzytelnianie MSI) |
    |                                | Azure SQL DB, Azure Synapse Analytics), SQL ml          | Tak       | -                   |
    |                                | Azure Key Vault (na potrzeby pobierania kluczy tajnych/parametrów połączenia) | Tak       | Tak                 |
    | Inne magazyny danych PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage itd.              | Tak       | -                   |
    | Azure laaS                     | SQL Server, Oracle itd.                                  | Tak       | -                   |
    | Lokalna laaS              | SQL Server, Oracle itd.                                  | Tak       | -                   |    

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły pokrewne:
* [Obsługiwane magazyny danych](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Azure Key Vault "zaufanych usług"](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Usługa Azure Storage "zaufane usługi firmy Microsoft"](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Tożsamość zarządzana dla Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
