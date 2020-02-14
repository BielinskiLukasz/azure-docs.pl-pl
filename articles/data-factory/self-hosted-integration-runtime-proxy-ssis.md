---
title: Skonfiguruj własne środowisko Integration Runtime jako serwer proxy dla usług SSIS
description: Dowiedz się, jak skonfigurować własne środowisko Integration Runtime jako serwer proxy dla Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 02/06/2020
ms.openlocfilehash: 5f9e15b83c36c6c19fbe93c5f1df365f6f763c81
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187680"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Skonfiguruj własne środowisko IR jako serwer proxy dla Azure-SSIS IR w Azure Data Factory

W tym artykule opisano sposób uruchamiania pakietów SQL Server Integration Services (SSIS) na Azure-SSIS Integration Runtime (Azure-SSIS IR) w Azure Data Factory z własnym hostowanym środowiskiem Integration Runtime (samoobsługowy IR) skonfigurowanym jako serwer proxy. 

Korzystając z tej funkcji, można uzyskać dostęp do danych lokalnie bez konieczności [przyłączania Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Ta funkcja jest przydatna, gdy sieć firmowa ma zbyt złożoną konfigurację lub że zasady są zbyt restrykcyjne, aby móc wstrzyknąć do niej Azure-SSIS IR.

Ta funkcja dzieli pakiety zawierające zadanie przepływu danych z lokalnym źródłem danych na dwa zadania tymczasowe: 
* Pierwsze zadanie, które jest uruchamiane w ramach własnego środowiska IR, najpierw przenosi dane z lokalnego źródła danych do obszaru przejściowego w usłudze Azure Blob Storage.
* Drugie zadanie, które jest uruchamiane na Azure-SSIS IR, następnie przenosi dane z obszaru przejściowego do planowanego miejsca docelowego danych.

Inne korzyści i możliwości tej funkcji pozwalają na przykład skonfigurować własne środowisko IR w regionach, które nie są jeszcze obsługiwane przez Azure-SSIS IR i zezwalać na publiczny statyczny adres IP własnego środowiska IR na zaporze źródeł danych.

## <a name="prepare-the-self-hosted-ir"></a>Przygotowywanie samodzielnego środowiska IR

Aby użyć tej funkcji, należy najpierw utworzyć fabrykę danych i skonfigurować w niej Azure-SSIS IR. Jeśli jeszcze tego nie zrobiono, postępuj zgodnie z instrukcjami podanymi w temacie [konfigurowanie Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Następnie możesz skonfigurować własne środowisko IR w tej samej fabryce danych, w której skonfigurowano Azure-SSIS IR. Aby to zrobić, zobacz [Tworzenie własnego środowiska IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Na koniec pobierasz i instalujesz najnowszą wersję samoobsługowego środowiska IR, a także dodatkowe sterowniki i środowisko uruchomieniowe na maszynie lokalnej lub maszynie wirtualnej platformy Azure w następujący sposób:
- Pobierz i zainstaluj najnowszą wersję [samodzielnego środowiska IR](https://www.microsoft.com/download/details.aspx?id=39717).
- Jeśli używasz łączników obiektów i osadzania bazy danych (OLEDB) w pakietach, Pobierz i zainstaluj odpowiednie sterowniki OLEDB na tym samym komputerze, na którym zainstalowano własne środowisko IR, jeśli jeszcze tego nie zrobiono.  

  Jeśli używasz starszej wersji sterownika OLEDB dla SQL Server (SQL Server Native Client [SQLNCLI]), [Pobierz wersję 64-bitową](https://www.microsoft.com/download/details.aspx?id=50402).  

  Jeśli używasz najnowszej wersji sterownika OLEDB dla SQL Server (MSOLEDBSQL), [Pobierz wersję 64-bitową](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Jeśli używasz sterowników OLEDB dla innych systemów baz danych, takich jak PostgreSQL, MySQL, Oracle i tak dalej, możesz pobrać 64-bitowe wersje z ich witryn sieci Web.
- Jeśli jeszcze tego nie zrobiono, [Pobierz i zainstaluj 64-bitową wersję środowiska uruchomieniowego Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) na tym samym komputerze, na którym zainstalowano własne środowisko IR.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Przygotowywanie usługi połączonej Azure Blob Storage do przemieszczania

Jeśli jeszcze tego nie zrobiono, Utwórz połączoną usługę Azure Blob Storage w tej samej fabryce danych, w której skonfigurowano Azure-SSIS IR. Aby to zrobić, zobacz [Tworzenie połączonej usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Pamiętaj, aby wykonać następujące czynności:
- W obszarze **Magazyn danych**wybierz pozycję **Azure Blob Storage**.  
- W przypadku **połączenia za pośrednictwem środowiska Integration Runtime**wybierz pozycję **AutoResolveIntegrationRuntime**.  
- W obszarze **Metoda uwierzytelniania**wybierz pozycję **klucz konta**, **Identyfikator URI sygnatury dostępu współdzielonego**lub **nazwę główną usługi**.  

    >[!TIP]
    >W przypadku wybrania jednostki **usługi**Udziel co najmniej roli *współautor danych obiektów blob magazynu* rolę. Aby uzyskać więcej informacji, zobacz [Łącznik usługi Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties).

![Przygotowywanie usługi połączonej Azure Blob Storage do przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurowanie Azure-SSIS IR przy użyciu własnego środowiska IR jako serwera proxy

Po przygotowaniu samodzielnego środowiska IR i usługi połączonej Azure Blob Storage na potrzeby przemieszczania można teraz skonfigurować nowe lub istniejące Azure-SSIS IR przy użyciu samodzielnego środowiska IR jako serwera proxy w portalu lub aplikacji usługi Fabryka danych. Przed wykonaniem tej czynności, jeśli istniejący Azure-SSIS IR jest już uruchomiony, Zatrzymaj go, a następnie uruchom go ponownie.

1. W okienku **Konfiguracja środowiska Integration Runtime** Pomiń poprzednie sekcje **Ustawienia ogólne** i **Ustawienia SQL** , wybierając pozycję **dalej**. 

1. W sekcji **Ustawienia zaawansowane** wykonaj następujące czynności:

   1. Zaznacz pole wyboru **skonfiguruj Integration Runtime samodzielnego jako serwer proxy dla Azure-SSIS Integration Runtime** . 

   1. Z listy rozwijanej **Samodzielna Integration Runtime** Wybierz istniejące własne środowisko IR jako serwer proxy dla Azure-SSIS IR.

   1. Z listy rozwijanej **połączona usługa magazynu przemieszczania** wybierz istniejącą usługę Azure Blob Storage lub Utwórz nową na potrzeby przemieszczania.

   1. W polu **ścieżka przemieszczania** Określ kontener obiektów BLOB na wybranym koncie usługi Azure Blob Storage lub pozostaw to pole puste, aby użyć domyślnego ustawienia do przemieszczania.

   1. Wybierz przycisk **Kontynuuj**.

   ![Ustawienia zaawansowane przy użyciu samodzielnego środowiska IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Możesz również skonfigurować nowe lub istniejące Azure-SSIS IR za pomocą samoobsługowego środowiska IR jako serwera proxy przy użyciu programu PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Zezwól na łączenie pakietów SSIS z serwerem proxy

Przy użyciu najnowszej SSDT z rozszerzeniem projektów SSIS dla programu Visual Studio lub instalatora autonomicznego można znaleźć nową właściwość `ConnectByProxy`, która została dodana w menedżerach sieci OLEDB lub prostych połączeń plików.
* [Pobierz rozszerzenie SSDT z projektami SSIS dla programu Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Pobierz instalatora autonomicznego](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Podczas projektowania nowych pakietów zawierających zadania przepływu danych z OLEDB lub płaskimi źródłami plików, które umożliwiają dostęp do baz danych lub plików lokalnie, można włączyć tę właściwość, ustawiając dla niej *wartość true* w okienku **Właściwości** odpowiednich menedżerów połączeń.

![Włącz Właściwość ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Możesz również włączyć tę właściwość, gdy uruchamiasz istniejące pakiety, bez konieczności ręcznego zmiany ich po jednym.  Dostępne są dwie opcje:
- **Opcja A**: Otwórz, Odbuduj i ponownie Wdróż projekt zawierający te pakiety z najnowszą SSDTą do uruchomienia na Azure-SSIS IR. Następnie można włączyć właściwość przez ustawienie dla odpowiednich menedżerów połączeń *wartości true* . Gdy są uruchamiane pakiety z programu SSMS, te menedżerowie połączeń są wyświetlane na karcie **menedżerowie połączeń** okna podręcznego **Wykonaj pakiet** .

  ![Włącz ConnectByProxy Property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Możesz również włączyć właściwość przez *ustawienie dla odpowiednich* menedżerów połączeń, które są wyświetlane na karcie **menedżerowie połączeń** w [działaniu pakiet SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) , gdy są uruchomione pakiety w Data Factory potokach.
  
  ![Włącz ConnectByProxy Property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opcja B:** Wdróż ponownie projekt zawierający te pakiety do uruchomienia na urządzeniu SSIS IR. Następnie można włączyć właściwość, podając jej ścieżkę właściwości, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`i ustawiając ją na *wartość true* w przypadku zastąpienia właściwości na karcie **Zaawansowane** w oknie podręcznym **wykonywania pakietu** , gdy są uruchamiane pakiety z programu SSMS.

  ![Włącz ConnectByProxy Property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Możesz również włączyć właściwość, podając jej ścieżkę właściwości, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`i ustawiając ją na *wartość true* w przypadku zastąpienia właściwości na karcie **przesłonięcie** [działania pakietu SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) w przypadku uruchamiania pakietów w potokach Data Factory.
  
  ![Włącz ConnectByProxy Property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Debugowanie pierwszego i drugiego zadania przemieszczania

Na własnym hostowanym środowisku IR można znaleźć dzienniki środowiska uruchomieniowego w folderze *C:\ProgramData\SSISTelemetry* oraz dzienniki wykonywania pierwszych zadań przemieszczania w folderze *C:\ProgramData\SSISTelemetry\ExecutionLog* .  Dzienniki wykonywania drugiego zadania przemieszczania można znaleźć w SSISDB lub określonych ścieżkach rejestrowania, w zależności od tego, czy pakiety są przechowywane w SSISDB, czy w systemie plików, udziałach plików czy Azure Files. W dziennikach wykonywania drugiego zadania przemieszczania można także znaleźć unikatowe identyfikatory pierwszych zadań tymczasowych. 

![Unikatowy identyfikator pierwszego zadania przemieszczania](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Używanie uwierzytelniania systemu Windows w zadaniach przemieszczania

Jeśli zadania przemieszczania w samoobsługowym środowisku IR wymagają uwierzytelniania systemu Windows, [Skonfiguruj pakiety usług SSIS tak, aby korzystały z tego samego uwierzytelniania systemu Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Zadania przemieszczania zostaną wywołane przy użyciu samodzielnego konta usługi IR (domyślnie*NT SERVICE\DIAHostService*), a Twoje magazyny danych będą dostępne przy użyciu konta uwierzytelniania systemu Windows. Oba konta wymagają przypisania pewnych zasad zabezpieczeń do nich. Na komputerze z własnym obsługą podczerwieni przejdź do pozycji **zasady zabezpieczeń lokalnych** > **Zasady lokalne** > **przypisania praw użytkownika**, a następnie wykonaj następujące czynności:

1. Przypisz *limity przydziałów pamięci dla procesu* i *Zastąp zasady tokenów na poziomie procesu* do samodzielnego konta usługi IR. To powinno nastąpić automatycznie podczas instalacji własnego środowiska IR przy użyciu domyślnego konta usługi. W przypadku użycia innego konta usługi Przypisz do niego te same zasady.

1. Przypisz *Logowanie jako zasady usługi* do konta uwierzytelniania systemu Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Rozliczenia dotyczące pierwszego i drugiego zadania przemieszczania

Opłaty za pierwsze zadania przemieszczania uruchamiane w ramach własnego środowiska IR są rozliczane osobno, podobnie jak wszystkie działania związane z przenoszeniem danych, które są uruchamiane na samoobsługowym środowisku IR, są rozliczane. Ta wartość jest określona w artykule [ceny potoku danych Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

W przypadku drugiego zadania przemieszczania uruchomionego na Azure-SSIS IR nie są naliczane opłaty osobno, ale w przypadku uruchomionego Azure-SSIS IR zostanie naliczona wartość określona w artykule [cennik Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Bieżące ograniczenia

- Obecnie obsługiwane są tylko zadania przepływu danych z usługami Open Database Connectivity (ODBC), OLEDB lub płaskim połączeniem plików oraz ODBC, OLEDB i płaskimi źródłami plików ani lokalizacją docelową OLEDB. 
- Obecnie są obsługiwane tylko usługi połączone z usługą Azure Blob Storage, które są skonfigurowane przy użyciu *klucza konta*, *identyfikatora URI sygnatury dostępu współdzielonego (SAS)* lub uwierzytelniania jednostki *usługi* .

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu własnego środowiska IR jako serwera proxy dla Azure-SSIS IR można wdrożyć i uruchomić pakiety w celu uzyskania dostępu do danych lokalnych jako działania pakietu SSIS w Data Factory potoków. Aby dowiedzieć się, jak to zrobić, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w Data Factory potoków](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
