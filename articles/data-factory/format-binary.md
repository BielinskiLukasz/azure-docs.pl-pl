---
title: Format binarny w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem binarnym w Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: jingwang
ms.openlocfilehash: 9dc2cee785b28db7446eb8ed8b89e27f4516aba2
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84736904"
---
# <a name="binary-format-in-azure-data-factory"></a>Format binarny w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Format binarny jest obsługiwany dla następujących łączników [: Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).

Można użyć binarnego zestawu danych w działaniach [kopiowania](copy-activity-overview.md), [działania GetMetadata](control-flow-get-metadata-activity.md)lub [Usuń działanie](delete-activity.md). W przypadku korzystania z binarnego zestawu danych ADF nie analizuje zawartości pliku, ale traktuje ją jako-is. 

>[!NOTE]
>W przypadku korzystania z binarnego zestawu danych w działaniu kopiowania można kopiować tylko z binarnego zestawu danych do binarnego zestawu danych.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez binarny zestaw danych.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Właściwość Type zestawu danych musi być ustawiona na wartość **binarną**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location` . **Zobacz szczegóły w sekcji łącznik — > właściwości zestawu danych**. | Tak      |
| kompresja | Grupa właściwości do konfigurowania kompresji plików. Skonfiguruj tę sekcję, jeśli chcesz przeprowadzić kompresję/dekompresowanie podczas wykonywania działania. | Nie |
| typ | Koder-dekoder kompresji używany do odczytu/zapisu plików binarnych. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**. do użycia podczas zapisywania pliku.<br>**Uwaga** podczas korzystania z działania kopiowania w celu dekompresowania plików **ZipDeflate** i zapisywania w magazynie danych ujścia opartych na plikach, domyślnie pliki są wyodrębniane do folderu: `<path specified in dataset>/<folder named as source zip file>/` , użyj funkcji `preserveZipFileNameAsFolder` w [źródle działania Copy](#binary-as-source) , aby określić, czy zachować nazwę pliku zip jako strukturę folderów.| Nie       |
| poziom | Współczynnik kompresji. Zastosuj, gdy zestaw danych jest używany w ujścia działania kopiowania.<br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **Najszybsze:** Operacja kompresji powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **Optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nie       |

Poniżej znajduje się przykładowy binarny zestaw danych na platformie Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło danych binarnych i ujścia.

>[!NOTE]
>W przypadku korzystania z binarnego zestawu danych w działaniu kopiowania można kopiować tylko z binarnego zestawu danych do binarnego zestawu danych.

### <a name="binary-as-source"></a>Plik binarny jako źródło

W sekcji *** \* Źródło \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **BinarySource**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z tabelą **ustawień odczytu danych binarnych** poniżej. | Nie       |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia odczytu danych binarnych** w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musi być ustawiony na **BinaryReadSettings**. | Tak      |
| compressionProperties | Grupa właściwości na temat sposobu dekompresowania danych dla danego kodera kompresji. | Nie       |
| preserveZipFileNameAsFolder<br>(*w `compressionProperties` obszarze *) | Stosuje się, gdy zestaw danych wejściowych jest skonfigurowany z kompresją **ZipDeflate** . Wskazuje, czy podczas kopiowania zachować nazwę źródłowego pliku zip jako strukturę folderów. Po ustawieniu na wartość true (domyślnie) Data Factory zapisuje niespakowane pliki do `<path specified in dataset>/<folder named as source zip file>/` ; po ustawieniu na wartość false Data Factory zapisuje niespakowane pliki bezpośrednio do `<path specified in dataset>` .  | Nie |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>Dane binarne jako ujścia

W sekcji *** \* ujścia \* *** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi być ustawiona na wartość **BinarySink**. | Tak      |
| storeSettings | Grupa właściwości do zapisywania danych w magazynie danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia zapisu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

## <a name="next-steps"></a>Następne kroki

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)
