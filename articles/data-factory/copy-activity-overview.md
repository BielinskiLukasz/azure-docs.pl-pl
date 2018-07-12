---
title: Działanie kopiowania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o działania kopiowania w usłudze Azure Data Factory, umożliwia kopiowanie danych z obsługiwanego źródłowego magazynu danych do obsługiwanego magazynu danych ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 8e34b0823b7f10455ac0b66fb0614d3946f2382e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38542707"
---
# <a name="copy-activity-in-azure-data-factory"></a>Działanie kopiowania w usłudze Azure Data Factory

## <a name="overview"></a>Przegląd

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-data-movement-activities.md)
> * [Bieżąca wersja](copy-activity-overview.md)

W usłudze Azure Data Factory działanie kopiowania służy do kopiowania danych między danych magazynach znajdujących się lokalnie i w chmurze. Po skopiowaniu danych można je dodatkowo przekształcane i analizowane. Działanie kopiowania umożliwia również publikować wyniki analizy do analizy biznesowej (BI) i użycie aplikacji i przekształcania.

![Rola działania kopiowania](media/copy-activity-overview/copy-activity.png)

Działanie kopiowania jest wykonywane na [środowiska Integration Runtime](concepts-integration-runtime.md). W scenariuszu kopii dla innych danych nadającego się do różnych wersji środowiska Integration Runtime:

* Jeśli kopiowanie danych między danymi przechowuje oba są dostępne publicznie, działanie kopiowania może upoważnionego przez **Azure Integration Runtime**, co jest bezpieczne, niezawodne i skalowalne i [dostępnie](concepts-integration-runtime.md#integration-runtime-location).
* Podczas kopiowania danych z/do magazynów danych działającego lokalnie lub w sieci przy użyciu kontroli dostępu (na przykład, Azure Virtual Network), musisz skonfigurować **może być samodzielnie hostowane zintegrowanego środowiska uruchomieniowego** umożliwiające kopiowanie danych.

Środowisko Integration Runtime musi być skojarzone z każdego źródła i ujścia magazynu danych. Dowiedz się więcej informacji na temat działania kopiowania [Określa, które środowisko IR wybrać](concepts-integration-runtime.md#determining-which-ir-to-use).

Działanie kopiowania przechodzi przez następujące etapy do kopiowania danych ze źródła do ujścia. Usługa, która obsługuje działania kopiowania:

1. Odczytuje dane z magazynu danych źródłowych.
2. Wykonuje serializacji/deserializacji, kompresji i dekompresji, mapowania kolumn itd. Robi te operacje na podstawie konfiguracji zestaw wejściowy, wyjściowy zestaw danych i działania kopiowania.
3. Zapisuje dane w magazynie danych będących ujściem/docelowym.

![Omówienie działania kopiowania](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Formaty i obsługiwane magazyny danych

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Obsługiwane formaty plików

Można użyć działania kopiowania do **skopiuj pliki — jest** między dwoma magazynami danych opartych na plikach, w których przypadku dane są kopiowane efektywnie bez żadnych serializacji/deserializacji.

Działanie kopiowania obsługuje również odczytywanie z oraz zapisywanie do plików w określonym formaty: **tekstu, JSON, Avro, ORC i Parquet**i kodera-dekodera kompresji **GZip, Deflate, BZip2 i ZipDeflate** są obsługiwane. Zobacz [obsługiwane formaty plików i kompresji](supported-file-formats-and-compression-codecs.md) ze szczegółowymi informacjami.

Na przykład należy wykonać następujące działania kopiowania:

* Kopiowanie danych na lokalnym serwerze SQL i zapisywać do usługi Azure Data Lake Store w formacie ORC.
* Skopiuj pliki w formacie tekstowym (CSV) w systemie plików w środowisku lokalnym i zapisywać do obiektów Blob platformy Azure, w formacie Avro.
* Skopiuj pliki zip z systemu plików w środowisku lokalnym i następnie Dekompresuj ziemi do usługi Azure Data Lake Store.
* Kopiowanie danych w formacie tekstowym skompresowany (CSV) GZip z obiektów Blob platformy Azure i zapisu do usługi Azure SQL Database.

## <a name="supported-regions"></a>Obsługiwane regiony

To usługa zapewniająca działania kopiowania jest dostępna globalnie w regionach i lokalizacjach geograficznych na liście [lokalizacji środowiska Azure Integration Runtime](concepts-integration-runtime.md#integration-runtime-location). Dostępnie topologii zapewnia efektywne przenoszenia, zazwyczaj pozwala uniknąć przeskoków między regionami. Zobacz [usługi według regionów](https://azure.microsoft.com/regions/#services) dostępność usługi Data Factory i przenoszenia danych w regionie.

## <a name="configuration"></a>Konfigurowanie

Aby użyć działania kopiowania w usłudze Azure Data Factory, musisz:

1. **Utwórz połączone usługi dla źródłowego magazynu danych i magazynu danych ujścia.** Zobacz artykuł dotyczący łącznika "Właściwości połączonej usługi" sekcję na temat sposobu konfigurowania i obsługiwanych właściwości. Można znaleźć na liście obsługiwanych łączników w [obsługiwane magazyny danych i formatów](#supported-data-stores-and-formats) sekcji.
2. **Tworzenie zestawów danych dla źródła i ujścia.** Zapoznaj się z źródła i ujścia sekcję "Dataset properties" łącznik artykuły na temat sposobu konfigurowania i obsługiwanych właściwości.
3. **Tworzenie potoku za pomocą działania kopiowania.** Następna sekcja zawiera przykład.  

### <a name="syntax"></a>Składnia

Następujący szablon działania kopiowania zawiera stanowi wyczerpującej listy obsługiwanych właściwości. Określ te, które dopasowania ich do scenariusza.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>Szczegóły składni

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa właściwości typu działania kopiowania: **kopiowania** | Yes |
| dane wejściowe | Określ zestaw danych utworzony wskazującą na źródło danych. Działanie kopiowania obsługuje tylko jednego danych wejściowych. | Yes |
| wyjścia | Określ zestaw danych został utworzony, które punkty danych ujścia. Działanie kopiowania obsługuje tylko pojedynczego wyjścia. | Yes |
| typeProperties | Grupa właściwości, aby skonfigurować działanie kopiowania. | Yes |
| source | Określ typ źródła kopiowania i odpowiednie właściwości dotyczące sposobu pobierania danych.<br/><br/>Dowiedz się więcej szczegółów w sekcji "Właściwości działania kopiowania" w artykule łącznika na liście [obsługiwane magazyny danych i formatów](#supported-data-stores-and-formats). | Yes |
| obiekt sink | Określ typ obiektu sink kopiowania i odpowiednie właściwości dotyczące sposobu zapisywania danych.<br/><br/>Dowiedz się więcej szczegółów w sekcji "Właściwości działania kopiowania" w artykule łącznika na liście [obsługiwane magazyny danych i formatów](#supported-data-stores-and-formats). | Yes |
| translator | Określ mapowania kolumn jawne ze źródła do ujścia. Ma zastosowanie, gdy domyślne zachowanie kopiowania nie może spełnić Twoje potrzeby.<br/><br/>Dowiedz się, szczegółowe informacje z [schemat i dane mapowania typów](copy-activity-schema-and-type-mapping.md). | Nie |
| dataIntegrationUnits | Określ powerfulness z [Azure Integration Runtime](concepts-integration-runtime.md) umożliwiające kopiowanie danych. Wcześniej znane jako chmury jednostek przenoszenia danych (DMU). <br/><br/>Dowiedz się, szczegółowe informacje z [jednostek integracji danych](copy-activity-performance.md#data-integration-units). | Nie |
| parallelCopies | Określanie równoległości, który ma działanie kopiowania do użycia podczas odczytu danych ze źródła i zapisywania danych do ujścia.<br/><br/>Dowiedz się, szczegółowe informacje z [równoległych kopii](copy-activity-performance.md#parallel-copy). | Nie |
| enableStaging<br/>stagingSettings | Wybierz etap przejściowy danych w magazynie obiektów blob aa zamiast bezpośrednio kopiowania danych ze źródła do ujścia.<br/><br/>Dowiedz się przydatne w scenariuszach i szczegółów konfiguracji z [kopiowania etapowego](copy-activity-performance.md#staged-copy). | Nie |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Wybierz sposób obsługi niezgodnych wierszy podczas kopiowania danych ze źródła do ujścia.<br/><br/>Dowiedz się, szczegółowe informacje z [odporność na uszkodzenia](copy-activity-fault-tolerance.md). | Nie |

## <a name="monitoring"></a>Monitorowanie

Możesz monitorować działanie kopiowania służy do uruchamiania w usłudze Azure Data Factory "Tworzenie i monitorowanie" w interfejsie użytkownika lub programowo. Możesz następnie porównać wydajność i konfiguracji scenariusza z działaniem kopiowania [dotyczące wydajności](copy-activity-performance.md#performance-reference) z testów wewnętrznych.

### <a name="monitor-visually"></a>Monitorowanie wizualne

Aby wizualnego monitorowania uruchomienia działania kopiowania, przejdź do usługi data factory -> **tworzenie i monitorowanie** -> **kartę Monitor**, zobaczysz listę potok jest uruchamiany za pomocą łącza "Wyświetl uruchomienia działań"  **Akcje** kolumny. 

![Monitorowanie uruchomień potoku](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Kliknij, aby wyświetlić listę działań w tym przebiegu potoku. W **akcje** kolumny, masz łącza do działania kopiowania w danych wejściowych, dane wyjściowe, błędy (w przypadku niepowodzenia uruchomienia działania kopiowania) i szczegóły.

![Monitorowanie uruchomień działania](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Kliknij przycisk "**szczegóły**" link w obszarze **akcje** Aby wyświetlić szczegóły wykonania działania kopiowania i charakterystyk wydajności. Przedstawia on informacje takie jak wolumin/wiersze/pliki danych skopiowane ze źródła do ujścia, przepływności, kroki przechodzi przez odpowiedni czas trwania i używane konfiguracje dla danego scenariusza kopiowania.

**Przykład: skopiuj z usługi Amazon S3 do usługi Azure Data Lake Store**
![szczegóły uruchamiania działania monitora](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Przykład: kopiowanie danych z usługi Azure SQL Database za pomocą usługi Azure SQL Data Warehouse kopiowania etapowego**
![szczegóły uruchamiania działania monitora](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Programowe monitorowanie

Szczegóły wykonania działania kopiowania i charakterystyk wydajności są także zwracany w wyniku uruchomienia działania kopiowania -> sekcji danych wyjściowych. Poniżej znajduje się wyczerpujący; zostaną wyświetlone tylko te mające zastosowanie do danego scenariusza kopiowania. Dowiedz się, jak monitorować działania uruchamiane z [szybkiego startu w sekcji monitorowanie](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nazwa właściwości  | Opis | Jednostka |
|:--- |:--- |:--- |
| DataRead | Rozmiar danych odczytu ze źródła | Wartość Int64 **bajtów** |
| DataWritten | Rozmiar danych zapisany do ujścia | Wartość Int64 **bajtów** |
| filesRead | Liczba plików kopiowanych podczas kopiowania danych z usługi file storage. | Wartość Int64 (Brak jednostki) |
| filesWritten | Liczba plików kopiowanych podczas kopiowania danych do usługi file storage. | Wartość Int64 (Brak jednostki) |
| rowsCopied | Liczba wierszy, kopiowane (nie dotyczy kopia binarna). | Wartość Int64 (Brak jednostki) |
| rowsSkipped | Liczba niezgodnych wierszy jest pomijane. Tę funkcję można włączyć przez zestaw "enableSkipIncompatibleRow" na wartość true. | Wartość Int64 (Brak jednostki) |
| Przepływność | Współczynnik szybkości transferu danych | W liczbę zmiennoprzecinkową **KB/s** |
| copyDuration | Czas trwania kopiowania | Wartość Int32 w ciągu kilku sekund |
| sqlDwPolyBase | Jeśli program PolyBase jest używany podczas kopiowania danych do usługi SQL Data Warehouse. | Wartość logiczna |
| redshiftUnload | Jeśli zwolnienie jest używany podczas kopiowania danych z usługi Redshift. | Wartość logiczna |
| hdfsDistcp | Jeśli narzędzia DistCp jest używany podczas kopiowania danych z systemu plików HDFS. | Wartość logiczna |
| effectiveIntegrationRuntime | Pokazują, które umożliwia zwiększenie możliwości dostępnych dla działania uruchamiania w formacie Runtime(s) integracji `<IR name> (<region if it's Azure IR>)`. | Tekst (ciąg) |
| usedDataIntegrationUnits | Skuteczne jednostek integracji danych podczas kopiowania. | Wartość Int32 |
| usedParallelCopies | Skuteczne parallelCopies podczas kopiowania. | Wartość Int32|
| redirectRowPath | Ścieżka do dziennika pominięto niezgodnych wierszy w magazynie obiektów blob, należy skonfigurować w elemencie "redirectIncompatibleRowSettings". Poniżej przedstawiono przykład. | Tekst (ciąg) |
| executionDetails | Szczegółowe informacje na temat poszczególnych etapów działania kopiowania przechodzi przez, i odpowiadających jej krokach, czas trwania, konfiguracje używane, itp. Nie zaleca się przeanalizować w tej sekcji, ponieważ mogą ulec zmianie. | Tablica |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedDataIntegrationUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedDataIntegrationUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Mapowanie typu danych i schematu

Zobacz [schemat i dane mapowania typów](copy-activity-schema-and-type-mapping.md), który opisuje sposób mapowania dane źródła do ujścia w działaniu kopiowania.

## <a name="fault-tolerance"></a>Odporność na uszkodzenia

Domyślnie działanie kopiowania zatrzymuje kopiowania danych i zwraca błąd, gdy napotkają niezgodny danych między źródła i ujścia. Jawnie można skonfigurować w celu pominięcia dziennika niezgodnych wierszy i kopiować tylko tych zgodnych danych do utworzenia kopii zakończyło się pomyślnie. Zobacz [działania kopiowania odporności na uszkodzenia](copy-activity-fault-tolerance.md) na więcej szczegółów.

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](copy-activity-performance.md), która opisuje kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory. Ponadto Wyświetla zaobserwowanego podczas testowania wewnętrznego i w tym artykule omówiono różne sposoby, aby zoptymalizować wydajność działania kopiowania.

## <a name="incremental-copy"></a>Przyrostowa kopia 
Usługa Data Factory obsługuje scenariusze dla przyrostowego kopiowania danych różnicowych z magazynu danych źródłowych do docelowego magazynu danych. Zobacz [samouczek: przyrostowe kopiowanie danych](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Odczyt i zapis danych partycjonowanych
W wersji 1 usługi Azure Data Factory obsługiwane Odczyt lub zapis danych podzielonych na partycje, korzystając z parametru SliceStart/SliceEnd/WindowStart/WindowEnd zmiennych systemowych. W bieżącej wersji tego zachowania można osiągnąć przy użyciu parametrów potoku i czas/zaplanowana godzina rozpoczęcia wyzwalacza jako wartość parametru. Aby uzyskać więcej informacji, zobacz [jak Odczyt lub zapis partycjonowania danych](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące Przewodniki Szybki Start, samouczków i przykładów:

- [Kopiowanie danych z jednej lokalizacji do innej lokalizacji w tej samej usłudze Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Kopiowanie danych z usługi Azure Blob Storage do usługi Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiowanie danych z lokalnego programu SQL Server na platformie Azure](tutorial-hybrid-copy-powershell.md)
