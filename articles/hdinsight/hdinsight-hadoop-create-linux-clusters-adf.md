---
title: 'Samouczek: Tworzenie klastrów usługi Apache Hadoop na żądanie w usłudze Azure HDInsight przy użyciu usługi fabryka danych '
description: Dowiedz się, jak tworzyć klastry Apache Hadoop na żądanie w HDInsight przy użyciu usługi Azure Data Factory.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: hrasheed
ms.openlocfilehash: c7ec0b29e200710070cb1243ff8bfadd5e31e8eb
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879413"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Samouczek: Tworzenie na żądanie klastry platformy Apache Hadoop w HDInsight przy użyciu usługi Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

W tym artykule dowiesz się, jak utworzyć [Apache Hadoop](https://hadoop.apache.org/) klastra na żądanie w usłudze Azure HDInsight przy użyciu usługi Azure Data Factory. Następnie użyj potoków danych w usłudze Azure Data Factory do uruchamiania zadań Hive i usunąć klaster. Do końca tego samouczka dowiesz się, jak operacjonalizować zadania obsługi dużych ilości danych, uruchamianie, gdzie tworzenia klastra, uruchom zadanie i usuwanie klastra są wykonywane zgodnie z harmonogramem.

Ten samouczek obejmuje następujące zadania: 

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Zrozumienie działania usługi Azure Data Factory
> * Tworzenie fabryki danych przy użyciu witryny Azure portal
> * Tworzenie połączonych usług
> * Tworzenie potoku
> * Wyzwalanie potoku
> * Monitorowanie potoku
> * Sprawdzanie danych wyjściowych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- Azure PowerShell. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

- Jednostki usługi Azure Active Directory. Po utworzeniu nazwy głównej usługi, pamiętaj pobrać **identyfikator aplikacji** i **klucz uwierzytelniania** zgodnie z instrukcjami w artykule połączone. Te wartości będą potrzebne w dalszej części tego samouczka. Ponadto upewnij się, nazwa główna usługi jest członkiem *Współautor* roli subskrypcji lub grupy zasobów, w której został utworzony klaster. Aby uzyskać instrukcje dotyczące pobierania wymaganych wartości i przypisać odpowiednie role, zobacz [Tworzenie jednostki usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

W tej sekcji utworzysz konto magazynu, która będzie służyć jako magazynem domyślnym dla klastra HDInsight, utworzone na żądanie. To konto magazynu zawiera również przykładowy [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) skryptu (**hivescript.hql**) umożliwia symulowanie próbkę [Apache Hive](https://hive.apache.org/) zadanie, które działa w klastrze.

Ta sekcja używa skrypt programu Azure PowerShell do tworzenia konta magazynu i kopiowanie wymaganych plików w ramach konta magazynu. Przykładowy skrypt programu Azure PowerShell w tej sekcji wykonuje następujące zadania:

1. Dzienniki w na platformie Azure.
1. Tworzy grupę zasobów platformy Azure.
1. Tworzy konto usługi Azure Storage.
1. Tworzy kontener obiektów Blob na koncie magazynu
1. Kopiuje przykładowy skrypt HiveQL (**hivescript.hql**) kontenera obiektów Blob. Skrypt znajduje się w temacie [ https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql ](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Przykładowy skrypt jest już dostępna w innym publicznym kontenerze obiektów Blob. Poniższego skryptu programu PowerShell tworzy kopię tych plików do konta magazynu platformy Azure, który on generuje.


**Aby utworzyć konto magazynu, a następnie skopiuj pliki przy użyciu programu Azure PowerShell:**
> [!IMPORTANT]
> Określ nazwy grupy zasobów platformy Azure i konto usługi Azure storage, która zostanie utworzona przy użyciu skryptu.
> Zapisz **nazwy grupy zasobów**, **nazwa konta magazynu**, i **klucza konta magazynu** zwrócone przez skrypt. Będą one potrzebne w następnej sekcji.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
Login-AzureRmAccount
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

**Aby sprawdzić, tworzenie konta magazynu**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **grup zasobów** w okienku po lewej stronie.
1. Kliknij dwukrotnie nazwę grupy zasobów utworzonej w skrypcie programu PowerShell. Jeśli masz zbyt wiele grup zasobów, na liście, użyj filtru.
1. Na **zasobów** kafelka, zostanie wyświetlony jeden zasób, chyba że udostępnić grupę zasobów z innymi projektami na liście. Ten zasób jest konto magazynu o nazwie określonej wcześniej. Wybierz nazwę konta magazynu.
1. Wybierz **obiektów blob** kafelków.
1. Wybierz **adfgetstarted** kontenera. Zostanie wyświetlony w folderze o nazwie **hivescripts**.
1. Otwórz folder i upewnij się, że zawiera on przykładowy plik skryptu **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Zrozumienie działania usługi Azure Data Factory

[Usługa Azure Data Factory](../data-factory/introduction.md) organizuje i automatyzuje operacje przenoszenia i przekształcania danych. Usługa Azure Data Factory można tworzyć usługi HDInsight Hadoop klaster just-in-time przetworzyć wycinek danych wejściowych i usunąć klaster, po zakończeniu przetwarzania. 

W usłudze Azure Data Factory fabryki danych może mieć jeden lub wiele potoków danych. Potok danych zawiera jeden lub więcej działań. Istnieją dwa typy działań:

- [Działania przenoszenia danych](../data-factory/copy-activity-overview.md) -używasz działania przenoszenia danych do przenoszenia danych z magazynu danych źródłowych do docelowego magazynu danych.
- [Działania przekształcania danych](../data-factory/transform-data.md). Działania przekształcania danych umożliwiają przekształcanie i przetwarzanie danych. Działanie usługi HDInsight Hive jest jednym z działania przekształcania, obsługiwane przez usługę Data Factory. Działanie przekształcania Hive są używane w tym samouczku.

W tym artykule możesz skonfigurować działanie programu Hive, aby utworzyć klaster usługi HDInsight Hadoop na żądanie. Gdy działanie zostanie uruchomione do przetwarzania danych, Oto, co się dzieje:

1. Klaster usługi HDInsight Hadoop jest tworzona automatycznie dla możesz just-in-time przetworzenie wycinka przez. 

1. Dane wejściowe są przetwarzane przez uruchomienie skryptu HiveQL w klastrze. W tym samouczku skrypt HiveQL, skojarzone za pomocą działania hive wykonuje następujące czynności:
    
    - Używa istniejącej tabeli (*hivesampletable*) do tworzenie innej tabeli **HiveSampleOut**.
    - Wypełnia **HiveSampleOut** tabelę zawierającą tylko określone kolumny od oryginału *hivesampletable*.
    
1. Klaster usługi HDInsight Hadoop jest usuwany po zakończeniu przetwarzania i klastra jest w stanie bezczynności skonfigurowanym czasie (ustawienie timeToLive). Jeśli dalej wycinek danych jest dostępne do przetwarzania przy użyciu w tym okresie bezczynności timeToLive, tego samego klastra służy do przetwarzania wycinka.  

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

1. W witrynie Azure portal wybierz **Utwórz zasób** > **dane + analiza** > **usługi Data Factory**.

    ![Usługa Azure Data Factory w witrynie portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "usługi Azure Data Factory w witrynie portal")

1. Wprowadź lub wybierz wartości, jak pokazano na poniższym zrzucie ekranu:

    ![Tworzenie fabryki danych Azure przy użyciu witryny Azure portal](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "Tworzenie usługi Azure Data Factory przy użyciu witryny Azure portal")

    Wprowadź lub wybierz poniższe wartości:
    
    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa** |  Wprowadź nazwę dla fabryki danych. Ta nazwa musi być unikatowa w skali globalnej.|
    |**Subskrypcja**     |  Wybierz swoją subskrypcję platformy Azure. |
    |**Grupa zasobów**     | Wybierz **Użyj istniejącej** a następnie wybierz grupę zasobów, które zostały utworzone za pomocą skryptu programu PowerShell. |
    |**Wersja**     | Wybierz **V2 (wersja zapoznawcza)** |
    |**Lokalizacja**     | Lokalizacja jest automatycznie ustawiona do lokalizacji, w której określono podczas tworzenia grupy zasobów wcześniej. W tym samouczku lokalizacja jest ustawiana **wschodnie stany USA 2**. |
    

1. Wybierz **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Utwórz**. Na pulpicie nawigacyjnym portalu powinien zostać wyświetlony nowy kafelek zatytułowany **Przekazywanie wdrożenia**. Tworzenie fabryki danych może potrwać od 2 do 4 minut.

    ![Postęp wdrażania szablonu](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "postęp wdrażania szablonu") 
 
1. Po utworzeniu fabryki danych w portalu jest wyświetlany Przegląd dla usługi data factory.

    ![Omówienie usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Omówienie usługi Azure Data Factory")

1. Wybierz **tworzenie i monitorowanie** można uruchomić usługi Azure Data Factory do tworzenia i monitorowania w portalu.

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tej sekcji zredagujesz dwie połączone usługi w fabryce danych.

- **Połączoną usługę Azure Storage**, która łączy konto usługi Azure Storage z fabryką danych. Ten magazyn jest używany przez klaster usługi HDInsight na żądanie. Zawiera on także skrypt Hive, która działa w klastrze.
- **Połączoną usługę HDInsight dostępną na żądanie**. Usługa Azure Data Factory automatycznie tworzy klaster usługi HDInsight i uruchamia skrypt Hive. Następnie usuwa klaster usługi HDInsight, gdy jest on bezczynny przez wstępnie skonfigurowany czas.

###  <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. W okienku po lewej stronie **zaczynajmy** wybierz opcję **Edytuj** ikony.

    ![Tworzenie usługi połączonej usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Tworzenie usługi połączonej usługi Azure Data Factory")

1. Wybierz **połączeń** w lewym dolnym rogu okna, a następnie wybierz pozycję **+ nowy**.

    ![Tworzenie połączenia w usłudze Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "tworzyć połączenia usługi Azure Data Factory")

1. W **nowa połączona usługa** okno dialogowe, wybierz opcję **usługi Azure Blob Storage** , a następnie wybierz **Kontynuuj**.

    ![Tworzenie usługi Azure Storage połączonej usługi dla usługi Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Tworzenie usługi Azure Storage połączonej usługi dla usługi Data Factory")

1. Podaj nazwę dla połączonej usługi storage, wybierz utworzone konto usługi Azure Storage jako część skryptu programu PowerShell, a następnie wybierz **Zakończ**.

    ![Podaj nazwę dla usługi Azure Storage połączoną usługę](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Podaj nazwę dla usługi Azure Storage połączona usługa")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight na żądanie

1. Wybierz ponownie przycisk **+ Nowy**, aby utworzyć kolejną połączoną usługę.

1. W oknie **Nowa połączona usługa** wybierz kolejno pozycje **Compute** > **Azure HDInsight**, a następnie kliknij pozycję **Kontynuuj**.

    ![Create HDInsight połączonej usługi dla usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "HDInsight do tworzenia połączonej usługi dla usługi Azure Data Factory")

1. W **nowa połączona usługa** okna, podaj wartości wymagane.

    ![Podaj wartości dla HDInsight połączoną usługę](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Podaj wartości dla HDInsight połączona usługa")

    Wprowadź następujące wartości i pozostaw resztę jako domyślny.

    | Właściwość | Opis |
    | --- | --- |
    | Name (Nazwa) | Wprowadź nazwę usługi połączonej HDInsight |
    | Typ | Wybierz **HDInsight na żądanie** |
    | Połączona usługa Azure Storage | Wybieranie połączonej usługi Storage utworzonego wcześniej. |
    | Typ klastra | Wybierz **usługi hadoop** |
    | Czas wygaśnięcia | Podaj czas trwania, dla którego chcesz klastra HDInsight, które mają być dostępne, zanim zostaną automatycznie usunięte.|
    | Identyfikator jednostki usługi | Podaj identyfikator aplikacji nazwy głównej usługi Azure Active Directory, utworzony jako część wymagań wstępnych |
    | Klucz jednostki usługi | Podaj klucz uwierzytelniania jednostki usługi Azure Active Directory |
    | Prefiks nazwy klastra | Podaj wartość, która będzie zaczynała się do wszystkich typów klastra, które są tworzone przez usługę data factory |
    | Grupa zasobów | Wybierz utworzoną grupę zasobów jako część skryptu programu PowerShell, które było wcześniej używane| 
    | Nazwa użytkownika SSH klastra | Wprowadź nazwę użytkownika SSH |
    | Hasło SSH klastra | Podaj hasło dla użytkownika SSH |

    Wybierz pozycję **Finish** (Zakończ).

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz przycisk **+** (znak plus), a następnie wybierz pozycję **Potok**.

    ![Tworzenie potoku w usłudze Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "utworzyć potok w usłudze Azure Data Factory")

1. W **działania** przybornika, rozwiń węzeł **HDInsight**, a następnie przeciągnij **Hive** działania do powierzchni projektanta potoku. W **ogólne** kartę, podaj nazwę działania.

    ![Dodawanie działań do potoku usługi Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "dodawanie działań do potoku usługi fabryka danych")

1. Upewnij się, że masz wybrane działanie Hive, wybierz opcję **klastra usługi HDI** karcie i **połączonej usługi HDInsight** listę rozwijaną, wybierz połączona usługa została wcześniej utworzona dla HDInsight.

    ![Podaj szczegóły klastra HDInsight w potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "szczegóły klastra HDInsight zapewniają potoku")

1. Wybierz **skryptu** kartę i wykonaj następujące czynności:
    
    1. Aby uzyskać **połączona usługa skryptu**, wybierz opcję **HDIStorageLinkedService**. Ta wartość jest połączonej usługi storage utworzonego wcześniej.
    
    1. Aby uzyskać **ścieżka pliku**, wybierz opcję **Przeglądaj magazyn** i przejdź do lokalizacji, w którym znajduje się przykładowy skrypt programu Hive. Jeśli skrypt programu PowerShell został przeprowadzony wcześniej, ta lokalizacja powinna być `adfgetstarted/hivescripts/hivescript.hql`.
    
        ![Podaj szczegóły skryptu programu Hive w potoku](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Hive Podaj szczegóły skryptu dla potoku")
    
    1. W obszarze **zaawansowane** > **parametry**, wybierz opcję **automatycznie Wypełniaj ze skryptu**. Ta opcja wyszukuje wszystkie parametry w skrypcie programu Hive, które wymagają wartości w czasie wykonywania. Skrypt używasz (**hivescript.hql**) ma **dane wyjściowe** parametru. Podaj wartość w formacie `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` wskaż istniejący folder w usłudze Azure Storage. W ścieżce jest rozróżniana wielkość liter. Jest to ścieżka, gdzie będą przechowywane dane wyjściowe skryptu.
    
        ![Podaj parametry dla skryptu Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "podać parametry do skryptu programu Hive")

1. Wybierz **weryfikacji** Aby zweryfikować potok. Wybierz przycisk **>>** (strzałka w prawo), aby zamknąć okno weryfikacji.

    ![Weryfikowanie potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "weryfikowanie potoku usługi Azure Data Factory")

1. Na koniec wybierz pozycję **Opublikuj wszystkie** Aby opublikować artefakty do usługi Azure Data Factory.

    ![Publikowanie potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "potoku usługi Azure Data Factory publikowania")

## <a name="trigger-a-pipeline"></a>Wyzwalanie potoku

1. Na pasku narzędzi na powierzchni projektowej wybierz **wyzwalacza** > **Wyzwól teraz**.

    ![Wyzwalanie potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "wyzwalanie potoku usługi Azure Data Factory")

1. Wybierz **Zakończ** w pasku bocznym wyskakujących.

## <a name="monitor-a-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Uruchomienie potoku zostanie wyświetlone na liście **Uruchomienia potoku**. Zwróć uwagę, stan potrzebne do uruchomienia **stan** kolumny.

    ![Monitorowanie potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "monitorowanie potoku usługi Azure Data Factory")

1. Wybierz pozycję **Odśwież**, aby odświeżyć stan.

1. Możesz również wybrać **Wyświetl uruchomienia działania** ikonę, aby zobaczyć uruchomienia działania skojarzone z potoku. Na poniższym zrzucie ekranu zobaczysz tylko jedno uruchomienie działania, ponieważ istnieje tylko jedno działanie w potoku, który został utworzony. Aby wrócić do poprzedniego widoku, wybierz **potoki** w górnej części strony.

    ![Monitorowanie działania potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorowanie działania potoku usługi Azure Data Factory")


## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

1. Aby sprawdzić, czy dane wyjściowe, w witrynie Azure portal przejdź do konta magazynu, który używany na potrzeby tego samouczka. Powinny zostać wyświetlone następujące foldery lub kontenerów:

    - Zostanie wyświetlony **adfgerstarted/outputfolder** zawierający dane wyjściowe skryptu programu Hive, która została uruchomiona jako część potoku.

    - Zostanie wyświetlony **adfhdidatafactory -\<połączone service-name >-\<sygnatura czasowa >** kontenera. Ten kontener jest domyślną lokalizację przechowywania klaster HDInsight, który został utworzony w ramach uruchomienia potoku.

    - Zostanie wyświetlony **adfjobs** dzienniki kontenera, który jest zadanie usługi Azure Data Factory.  

        ![Sprawdź dane wyjściowe z potoku usługi Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Sprawdź dane wyjściowe z potoku usługi Azure Data Factory")


## <a name="clean-up-the-tutorial"></a>Czyszczenie na koniec samouczka

Za pomocą na deman Tworzenie klastra HDInsight, nie trzeba jawnie usunąć klaster HDInsight. Klaster jest usuwany, na podstawie konfiguracji podane podczas tworzenia potoku. Jednak mimo klaster jest usuwany, wszystkie konta magazynu skojarzone z klastrem nadal istnieje. To zachowanie jest celowe, dzięki czemu możesz zachować swoje dane bez zmian. Jednak jeśli chcesz zachować dane, możesz usunąć utworzone konto magazynu.

Alternatywnie można usunąć całą grupę zasobów utworzoną na potrzeby tego samouczka. Spowoduje to usunięcie konta magazynu i usługi Azure Data Factory, który został utworzony.

### <a name="delete-the-resource-group"></a>Usuń grupę zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **grup zasobów** w okienku po lewej stronie.
1. Wybierz nazwę grupy zasobów utworzonej w skrypcie programu PowerShell. Jeśli masz zbyt wiele grup zasobów, na liście, użyj filtru. Spowoduje to otwarcie grupy zasobów.
1. Na **zasobów** kafelka, powinna mieć domyślne konto magazynu i data factory, chyba że udostępnić grupę zasobów z innymi projektami na liście.
1. Wybierz pozycję **Usuń grupę zasobów**. Spowoduje to usunięcie, konto magazynu i danych przechowywanych na koncie magazynu.

    ![Usuń grupę zasobów](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Usuń grupę zasobów")

1. Wprowadź nazwę grupy zasobów, aby potwierdzić usunięcie, a następnie wybierz **Usuń**.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób użycia usługi Azure Data Factory do utworzenia klastra HDInsight na żądanie i uruchom [Apache Hive](https://hive.apache.org/) zadania. Przejdź do następnego artciel, aby dowiedzieć się, jak utworzyć klastrów HDInsight za pomocą konfiguracji niestandardowej.

> [!div class="nextstepaction"]
>[Tworzenie klastrów usługi Azure HDInsight za pomocą konfiguracji niestandardowej](hdinsight-hadoop-provision-linux-clusters.md)


