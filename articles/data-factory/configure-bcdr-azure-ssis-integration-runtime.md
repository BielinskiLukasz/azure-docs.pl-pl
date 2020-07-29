---
title: Konfigurowanie środowiska Azure-SSIS Integration Runtime dla SQL Database trybu failover
description: W tym artykule opisano sposób konfigurowania środowiska Azure-SSIS Integration Runtime z Azure SQL Database replikacją geograficzną i przełączeniem w tryb failover dla bazy danych SSISDB
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: e1b70e0e3eb54253972afded1bd37363d1a868e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84195709"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Konfigurowanie środowiska Azure-SSIS Integration Runtime z SQL Database replikacją geograficzną i trybem failover

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób konfigurowania środowiska Azure-SSIS Integration Runtime (IR) Azure SQL Database replikacji geograficznej dla bazy danych SSISDB. W przypadku przejścia w tryb failover można upewnić się, że Azure-SSIS IR nadal pracuje z pomocniczą bazą danych.

Aby uzyskać więcej informacji na temat replikacji geograficznej i trybu failover dla SQL Database, zobacz temat [przegląd: aktywnej replikacji geograficznej i grup Autotryb failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Azure-SSIS IR trybu failover z wystąpieniem zarządzanym SQL

### <a name="prerequisites"></a>Wymagania wstępne

Wystąpienie zarządzane Azure SQL używa *klucza głównego bazy danych (DMK)* , aby pomóc w zabezpieczeniu danych, poświadczeń i informacji o połączeniu, które są przechowywane w bazie danych. Aby włączyć automatyczne odszyfrowywanie DMK, kopia klucza jest szyfrowana za pomocą *klucza głównego serwera (klucza)*. 

KLUCZA nie jest replikowana w grupie trybu failover. Należy dodać hasło zarówno z wystąpienia podstawowego, jak i pomocniczego do odszyfrowania DMK po przejściu w tryb failover.

1. Uruchom następujące polecenie dla SSISDB na wystąpieniu podstawowym. Ten krok powoduje dodanie nowego hasła szyfrowania.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Utwórz grupę trybu failover w wystąpieniu zarządzanym SQL.

3. Uruchom **sp_control_dbmasterkey_password** w wystąpieniu pomocniczym przy użyciu nowego hasła szyfrowania.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scenariusz 1: Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu/zapisu

Jeśli Azure-SSIS IR ma wskazywać punkt końcowy odbiornika odczytu/zapisu, należy najpierw wskazać punkt końcowy serwera podstawowego. Po umieszczeniu SSISDB w grupie trybu failover można przejść do punktu końcowego odbiornika odczytu/zapisu i ponownie uruchomić Azure-SSIS IR.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover wykonaj następujące czynności:

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Edytuj Azure-SSIS IR za pomocą nowego regionu, sieci wirtualnej i identyfikatora URI sygnatury dostępu współdzielonego (SAS) dla konfiguracji niestandardowej w wystąpieniu pomocniczym. Ponieważ Azure-SSIS IR wskazuje odbiornik odczytu/zapisu, a punkt końcowy jest niewidoczny dla Azure-SSIS IR, nie trzeba edytować punktu końcowego.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Uruchom ponownie Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scenariusz 2. Azure-SSIS IR wskazuje na podstawowy punkt końcowy serwera

Ten scenariusz jest odpowiedni, jeśli Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover wykonaj następujące czynności:

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Edytuj Azure-SSIS IR przy użyciu nowego regionu, punktu końcowego i informacji o sieci wirtualnej dla wystąpienia pomocniczego.

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database endpoint" `
                    -CatalogAdminCredential "Azure SQL Database admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
        ```

3. Restart the Azure-SSIS IR.

### Scenario 3: Azure-SSIS IR is pointing to a public endpoint of a SQL Managed Instance

This scenario is suitable if the Azure-SSIS IR is pointing to a public endpoint of a Azure SQL Managed Instance and it doesn't join to a virtual network. The only difference from scenario 2 is that you don't need to edit virtual network information for the Azure-SSIS IR after failover.

#### Solution

When failover occurs, take the following steps:

1. Stop the Azure-SSIS IR in the primary region.

2. Edit the Azure-SSIS IR with the new region and endpoint information for the secondary instance.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Uruchom ponownie Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenariusz 4: dołączanie istniejącego wystąpienia SSISDB (katalog SSIS) do nowego Azure-SSIS IR

Ten scenariusz jest przydatny, jeśli chcesz, aby SSISDB pracował z nowym Azure-SSIS IR w nowym regionie, gdy wystąpi awaria Azure Data Factory lub Azure-SSIS IR w bieżącym regionie.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover wykonaj następujące czynności.

> [!NOTE]
> Użyj programu PowerShell dla kroku 4 (Tworzenie IR). Jeśli tego nie zrobisz, Azure Portal zgłosi błąd informujący o tym, że SSISDB już istnieje.

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Uruchom procedurę składowaną, aby zaktualizować metadane w programie SSISDB, aby akceptować połączenia z **\<new_data_factory_name\>** i **\<new_integration_runtime_name\>** .
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Utwórz nową fabrykę danych o nazwie **\<new_data_factory_name\>** w nowym regionie.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie fabryki danych Azure przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md).

4. Utwórz nową Azure-SSIS IR o nazwie **\<new_integration_runtime_name\>** w nowym regionie przy użyciu Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Aby uzyskać więcej informacji na temat tego polecenia programu PowerShell, zobacz temat [Tworzenie środowiska Azure SSIS Integration Runtime w Azure Data Factory](create-azure-ssis-integration-runtime.md).



## <a name="azure-ssis-ir-failover-with-sql-database"></a>Azure-SSIS IR trybu failover z SQL Database

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scenariusz 1: Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu/zapisu

Ten scenariusz jest odpowiedni w przypadku:

- Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu/zapisu grupy trybu failover.
- Serwer SQL Database *nie* jest skonfigurowany z regułą dla punktu końcowego usługi sieci wirtualnej.

Jeśli Azure-SSIS IR ma wskazywać punkt końcowy odbiornika odczytu/zapisu, należy najpierw wskazać punkt końcowy serwera podstawowego. Po umieszczeniu SSISDB w grupie trybu failover można przejść do punktu końcowego odbiornika odczytu/zapisu i ponownie uruchomić Azure-SSIS IR.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover jest on niewidoczny dla Azure-SSIS IR. Azure-SSIS IR automatycznie nawiązuje połączenie z nowym podstawowym grupą trybu failover. 

Jeśli chcesz zaktualizować region lub inne informacje w Azure-SSIS IR, możesz je zatrzymać, edytować i ponownie uruchomić.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scenariusz 2. Azure-SSIS IR wskazuje na podstawowy punkt końcowy serwera

Ten scenariusz jest odpowiedni, jeśli Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover wykonaj następujące czynności:

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Edytuj Azure-SSIS IR przy użyciu nowego regionu, punktu końcowego i informacji o sieci wirtualnej dla wystąpienia pomocniczego.

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                        -CatalogServerEndpoint "Azure SQL Database endpoint" `
                        -CatalogAdminCredential "Azure SQL Database admin credentials" `
                        -VNetId "new VNet" `
                        -Subnet "new subnet" `
                        -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Uruchom ponownie Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenariusz 3: dołączanie istniejącego SSISDB (katalog SSIS) do nowego Azure-SSIS IR

Ten scenariusz jest odpowiedni, jeśli chcesz zainicjować obsługę nowego Azure-SSIS IR w regionie pomocniczym. Jest ona również odpowiednia, jeśli chcesz, aby SSISDB pracował z nowym Azure-SSIS IR w nowym regionie, gdy Azure Data Factory lub Azure-SSIS IR awarii występuje w bieżącym regionie.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover wykonaj następujące czynności.

> [!NOTE]
> Użyj programu PowerShell dla kroku 4 (Tworzenie IR). Jeśli tego nie zrobisz, Azure Portal zgłosi błąd informujący o tym, że SSISDB już istnieje.

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Uruchom procedurę składowaną, aby zaktualizować metadane w programie SSISDB, aby akceptować połączenia z **\<new_data_factory_name\>** i **\<new_integration_runtime_name\>** .
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Utwórz nową fabrykę danych o nazwie **\<new_data_factory_name\>** w nowym regionie.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie fabryki danych Azure przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md).

4. Utwórz nową Azure-SSIS IR o nazwie **\<new_integration_runtime_name\>** w nowym regionie przy użyciu Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Aby uzyskać więcej informacji na temat tego polecenia programu PowerShell, zobacz temat [Tworzenie środowiska Azure SSIS Integration Runtime w Azure Data Factory](create-azure-ssis-integration-runtime.md).


## <a name="next-steps"></a>Następne kroki

Należy wziąć pod uwagę następujące inne opcje konfiguracji Azure-SSIS IR:

- [Konfigurowanie środowiska Azure-SSIS Integration Runtime w celu zapewnienia wysokiej wydajności](configure-azure-ssis-integration-runtime-performance.md)

- [Dostosowywanie konfiguracji środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Inicjowanie obsługi administracyjnej wersji Enterprise dla środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
