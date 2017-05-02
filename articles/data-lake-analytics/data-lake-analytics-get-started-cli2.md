---
title: "Rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia platformy Azure 2.0 | Microsoft Docs"
description: "Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure 2.0 utworzyć konto usługi Data Lake Analytics oraz utworzyć i przesłać zadanie usługi Data Lake Analytics, korzystając z języka U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 109460cecc4e11c729203af97c9bf1c22b90e61a
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20-preview"></a>Samouczek: rozpoczynanie pracy z usługą Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia platformy Azure 2.0 (wersja zapoznawcza)
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure 2.0 do tworzenia kont usługi Azure Data Lake Analytics, definiowania zadań usługi Data Lake Analytics w języku [U-SQL](data-lake-analytics-u-sql-get-started.md) i wysyłania ich do kont usługi Data Lake Analytics. Więcej informacji na temat usługi Data Lake Analytics można znaleźć w artykule [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md).

W ramach tego samouczka utworzysz zadanie, które odczytuje zawartość pliku z wartościami rozdzielanymi tabulatorami (TSV) i konwertuje je do pliku z wartościami rozdzielanymi przecinkami (CSV). Aby wykonać kroki opisane w tym samouczku, korzystając z innych obsługiwanych narzędzi, użyj listy rozwijanej w górnej części tej sekcji.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Interfejs wiersza polecenia platformy Azure 2.0**. Zobacz temat [Instalowanie i konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* **Włączony interfejs wiersza polecenia usługi Data Lake Store/Analytics 2.0 (wersja zapoznawcza)**. Interfejs wiersza polecenia usług Data Lake Store i Data Lake Analytics 2.0 jest nadal w wersji zapoznawczej. Uruchom następujące polecenia, aby włączyć oba te interfejsy:

    ```azurecli
    az component update --add dls
    az component update --add dla 
    ```

## <a name="log-in-to-azure"></a>Logowanie się do platformy Azure

Aby zalogować się do subskrypcji platformy Azure:

```azurecli
az login
```

Zostanie wyświetlona prośba o przejście do adresu URL i wpisanie kodu uwierzytelniania.  Następnie postępuj zgodnie z instrukcjami, aby wprowadzić swoje poświadczenia.

Po zalogowaniu polecenie logowania spowoduje wyświetlenie listy subskrypcji.

Aby użyć określonej subskrypcji:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Tworzenie konta usługi Data Lake Analytics
Aby można było uruchomić jakiekolwiek zadanie, musisz mieć konto usługi Data Lake Analytics. Aby utworzyć takie konto, należy określić następujące elementy:

* **Grupa zasobów platformy Azure**. Konto usługi Data Lake Analytics należy utworzyć w grupie zasobów platformy Azure. Usługa [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) umożliwia pracę z zasobami w aplikacji jak z grupą. Wszystkie zasoby aplikacji można wdrożyć, zaktualizować lub usunąć w jednej, skoordynowanej operacji.  
  
    Aby wyświetlić listę istniejących grup zasobów w ramach subskrypcji:
  
    ```azurecli
    az group list 
    ```

    Aby utworzyć nową grupę zasobów:
  
    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **Nazwa konta usługi Data Lake Analytics**. Każde konto usługi Data Lake Analytics ma nazwę.
* **Lokalizacja**. Użyj centrum danych platformy Azure, które obsługuje usługę Data Lake Analytics.
* **Domyślne konto usługi Data Lake Store**: każde konto usługi Data Lake Analytics ma domyślne konto usługi Data Lake Store.
  
    Aby wyświetlić istniejące konto usługi Data Lake Store:

    ```azurecli
    az dls account list
    ```
  
    Aby utworzyć nowe konto usługi Data Lake Store:
  
    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

Użyj następującej składni, aby utworzyć konto usługi Data Lake Analytics:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Po utworzeniu konta można użyć następujących poleceń w celu wyświetlenia listy kont i szczegółów konta:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Przekazywanie danych do usługi Data Lake Store
W ramach tego samouczka przetworzysz wybrane dzienniki wyszukiwania.  Dziennik wyszukiwania może być przechowywany w usłudze Data Lake Store lub w usłudze Azure Blob Storage. 

Witryna Azure Portal udostępnia interfejs użytkownika umożliwiający skopiowanie przykładowych plików danych na domyślne konto usługi Data Lake Store. Pliki te obejmują również dziennik wyszukiwania. Zobacz temat [Przygotowanie danych źródłowych](data-lake-analytics-get-started-portal.md#prepare-source-data), aby przekazać dane na domyślne konto usługi Data Lake Store.

Aby przekazać pliki, używając interfejsu wiersza polecenia 2.0, skorzystaj z następujących poleceń:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Usługa Data Lake Analytics może także uzyskiwać dostęp do usługi Azure Blob Storage.  Aby uzyskać informacje o przekazywaniu danych do usługi Azure Blob Storage, zobacz temat [Korzystanie z interfejsu wiersza polecenia platformy Azure w usłudze Azure Storage](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Przesyłanie zadań usługi Data Lake Analytics
Zadania usługi Data Lake Analytics są napisane w języku U-SQL. Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL](data-lake-analytics-u-sql-get-started.md) i [Dokumentację języka U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Aby utworzyć skrypt zadania usługi Data Lake Analytics**

Utwórz plik tekstowy zawierający następujący skrypt w języku U-SQL i zapisz go na swojej stacji roboczej:
  
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
  
Ten skrypt U-SQL odczytuje źródłowy plik danych przy użyciu ekstraktora **Extractors.Tsv()**, a następnie tworzy plik csv przy użyciu ekstraktora **Outputters.Csv()**. 

Nie należy modyfikować tych dwóch ścieżek, jeśli plik źródłowy nie został skopiowany do innej lokalizacji.  Jeśli folder wyjściowy nie istnieje, usługa Data Lake Analytics go utworzy.

Użycie ścieżek względnych jest łatwiejsze w przypadku plików przechowywanych na domyślnych kontach usługi Data Lake Store. Można także użyć ścieżek bezwzględnych.  Na przykład:

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

Aby uzyskać dostęp do plików w połączonych kontach magazynu, należy użyć ścieżek bezwzględnych.  Składnia dla plików przechowywanych na połączonym koncie usługi Azure Storage jest następująca:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > Uprawnienia dostępu do kontenerów obiektów Blob platformy Azure z publicznymi obiektami lub publicznymi kontenerami nie są obecnie obsługiwane.      
  > 
  > 

**Aby przesłać zadania**

Użyj następującej składni, aby przesłać zadanie.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Na przykład:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Aby wyświetlić listę zadań i szczegóły zadania**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Aby anulować zadania**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>Pobieranie wyników zadania

Po zakończeniu zadania można użyć następujących poleceń, aby wyświetlić pliki wyjściowe i pobrać pliki:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" 
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Na przykład:

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić ten samouczek przy użyciu innych narzędzi, kliknij odpowiedni selektor karty w górnej części strony.
* Aby wyświetlić dokument referencyjny dotyczący interfejsu wiersza polecenia usługi Data Lake Analytics 2.0, zobacz [Data Lake Analytics — az dla](https://docs.microsoft.com/cli/azure/dla).
* Aby wyświetlić dokument referencyjny dotyczący interfejsu wiersza polecenia usługi Data Lake Store 2.0, zobacz [Data Lake Store — az dls](https://docs.microsoft.com/cli/azure/dls).
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby rozpocząć tworzenie aplikacji w języku U-SQL, zobacz artykuł [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools dla Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informacje o zadaniach zarządzania znajdziesz w artykule [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md).
* Aby zapoznać się z omówieniem usługi Data Lake Analytics, zobacz [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md).


