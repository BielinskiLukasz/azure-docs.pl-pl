---
title: 'Szybki start: tworzenie klastra Platformy Spark apache w usłudze Azure HDInsight za pomocą programu Azure PowerShell'
description: W tym przewodniku Szybki start pokazano, jak za pomocą programu Azure PowerShell utworzyć klaster Apache Spark w usłudze Azure HDInsight i uruchomić proste zapytanie Spark SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: 7416c25128da8dcaf803a9f03144110941200ab2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049167"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Szybki start: tworzenie klastra Platformy Spark apache w usłudze Azure HDInsight przy użyciu programu PowerShell

W tym przewodniku Szybki start można użyć programu Azure PowerShell do utworzenia klastra Platformy Spark Apache w usłudze Azure HDInsight. Następnie należy utworzyć notes Jupyter i używać go do uruchamiania zapytań Spark SQL względem tabel gałęzi Apache. Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Struktura Apache Spark dla usługi Azure HDInsight umożliwia szybką analizę danych i przetwarzanie klastrów przy użyciu przetwarzania w pamięci. Notebook Jupyter umożliwia interakcję z danymi, łączenie kodu z tekstem znaczników i proste wizualizacje.

[Omówienie: Apache Spark w notesie usługi Azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter](https://jupyter.org/)

## <a name="prerequisite"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Moduł [PowerShell Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Tworzenie klastra platformy Apache Spark w usłudze HDInsight

> [!IMPORTANT]  
> Opłaty za klastry usługi HDInsight są naliczane proporcjonalnie za minutę, niezależnie od ich użycia. Pamiętaj o usunięciu klastra po zakończeniu korzystania z niego. Aby uzyskać więcej informacji, zobacz sekcję [Czyszczenie zasobów](#clean-up-resources) w tym artykule.

Tworzenie klastra usługi HDInsight obejmuje tworzenie następujących obiektów i zasobów platformy Azure:

- Grupa zasobów platformy Azure. Grupa zasobów platformy Azure to kontener dla zasobów platformy Azure.
- Konto magazynu platformy Azure lub usługa Azure Data Lake Storage.  Każdy klaster usługi HDInsight wymaga zależnego magazynu danych. W tym przewodniku Szybki start utworzysz klaster, który używa obiektów blob usługi Azure Storage jako magazynu klastra. Aby uzyskać więcej informacji na temat korzystania z usługi Data Lake Storage 2. generacji, zobacz [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
- Klaster różnych typów klastrów w programie HDInsight.  W tym przewodniku Szybki start utworzysz klaster Spark w wersji 2.3.

Te zasoby zostaną utworzone za pomocą skryptu programu PowerShell. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Po uruchomieniu skryptu programu PowerShell zostanie wyświetlony monit o wprowadzenie następujących wartości:

|Parametr|Wartość|
|------|------|
|Nazwa grupy zasobów platformy Azure | Podaj unikatową nazwę grupy zasobów.|
|Lokalizacja| Określ region świadczenia usługi Azure, na przykład „Środkowe stany USA”. |
|Domyślna nazwa konta magazynu | Podaj unikatową nazwę konta magazynu. |
|Nazwa klastra | Podaj unikatową nazwę klastra HDInsight.|
|Poświadczenia logowania klastra | To konto służy do nawiązania połączenia z pulpitem nawigacyjnym klastra w dalszej części przewodnika Szybki start.|
|Poświadczenia użytkownika protokołu SSH | Klienci SSH mogą być używane do tworzenia zdalnej sesji wiersza polecenia z klastrów w HDInsight.|

1. Wybierz **pozycję Wypróbuj** w prawym górnym rogu, aby otworzyć usługę Azure Cloud [Shell](../../cloud-shell/overview.md)w prawym górnym rogu, a następnie postępuj zgodnie z instrukcjami, aby połączyć się z platformą Azure.

2. Skopiuj i wklej następujący skrypt programu PowerShell w powłoki chmury.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   Utworzenie klastra trwa około 20 minut. Przed przejściem do następnej sesji należy utworzyć klaster.

Jeśli napotkasz problem z tworzeniem klastrów HDInsight, może to oznaczać, że nie masz odpowiednich uprawnień, aby to zrobić. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

[Jupyter Notebook](https://jupyter.org/) to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Notes pozwala na interakcję z danymi, łączenie kodu z tekstem markdown i wykonywanie prostych wizualizacji.

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **klastery HDInsight**.
   
   ![Otwieranie klastra usługi HDInsight w witrynie Azure portal](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png)
   
1. Z listy wybierz utworzony klaster.
   
   ![Otwieranie klastra usługi HDInsight w witrynie Azure portal](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png)
   
1. Na stronie **Przegląd klastra** wybierz pozycję **Pulpity nawigacyjne klastra**, a następnie wybierz pozycję **Notes jupyter .** Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

   ![Otwórz notes Jupyter, aby uruchomić interaktywną kwerendę SQL platformy Spark](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otwórz notes Jupyter, aby uruchomić interaktywną kwerendę SQL platformy Spark")

1. Wybierz **pozycję Nowy** > **pyspark,** aby utworzyć notes.

   ![Tworzenie notesu Jupyter w celu uruchomienia interaktywnej kwerendy SQL platformy Spark](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie notesu Jupyter w celu uruchomienia interaktywnej kwerendy SQL platformy Spark")

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Uruchamianie instrukcji APACHE Spark SQL

SQL (Structured Query Language) to najczęściej używany język służący do definiowania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Sprawdź, czy jądro jest gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    ![stan jądra](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "stan jądra")

    Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe. 
1. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Korzystając z notesu Jupyter z klastrem Platformy Spark w `sqlContext` programie HDInsight, można uzyskać ustawienie wstępne, którego można użyć do uruchamiania zapytań hive przy użyciu programu Spark SQL. Wyrażenie `%%sql` informuje notes Jupyter o konieczności użycia ustawienia wstępnego `sqlContext` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Uzyskanie wyników zajmuje około 30 sekund. Dane wyjściowe wyglądają następująco:

    ![Apache Hive kwerendy w Spark na HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png "Zapytanie gałęzi w programie HDInsight Spark")

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.

1. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    ![Dane wyjściowe zapytania gałęzi w programie HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png "Dane wyjściowe zapytania gałęzi w programie HDInsight")

1. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamknięcie notesu spowoduje zwolnienie zasobów klastra.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usługa HDInsight zapisuje Twoje dane w usłudze Azure Storage lub Azure Data Lake Storage, więc możesz bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane. Jeśli planujesz natychmiastowe rozpoczęcie pracy z samouczkiem z listy [Następne kroki](#next-steps), warto zachować klaster.

Przejdź z powrotem do witryny Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usługa Azure portal usuwa klaster usługi HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, można usunąć zarówno klaster HDInsight, jak i domyślne konto magazynu.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>Piecemeal oczyścić z modułem PowerShell Az

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster Platformy Spark Apache w programie HDInsight i uruchomić podstawową kwerendę SQL platformy Spark. Przejdź do następnego samouczka, aby dowiedzieć się, jak używać klastra HDInsight do uruchamiania zapytań interaktywnych na przykładowych danych.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)
