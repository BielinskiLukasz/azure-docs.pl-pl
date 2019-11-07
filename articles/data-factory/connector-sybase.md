---
title: Kopiowanie danych z programu Sybase przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z programu Sybase do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 4877706c487f389539e8e5c73bb984b01d200f38
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680119"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Kopiowanie danych z programu Sybase przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-sybase-connector.md)
> * [Bieżąca wersja](connector-sybase.md)

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych programu Sybase. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik programu Sybase jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z bazy danych programu Sybase można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W tym przypadku ten łącznik programu Sybase obsługuje:

- SAP Sybase SQL w dowolnym miejscu (ASA) **w wersji 16 lub nowszej**; IQ i ASE nie są obsługiwane.
- Kopiowanie danych przy użyciu uwierzytelniania **podstawowego** lub **systemu Windows** .

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika programu Sybase, należy wykonać następujące:

- Skonfiguruj samodzielny Integration Runtime. Aby uzyskać szczegółowe informacje, zobacz artykuł [Integration Runtime samodzielny](create-self-hosted-integration-runtime.md) .
- Zainstaluj [dostawcę danych dla programu Sybase iAnywhere. Data. SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 lub nowszego na maszynie Integration Runtime.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika programu Sybase.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Sybase są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **Sybase** | Tak |
| serwer | Nazwa serwera programu Sybase. |Tak |
| baza danych | Nazwa bazy danych programu Sybase. |Tak |
| authenticationType | Typ uwierzytelniania używany do nawiązywania połączenia z bazą danych programu Sybase.<br/>Dozwolone wartości to: **Basic**i **Windows**. |Tak |
| nazwa użytkownika | Określ nazwę użytkownika, aby połączyć się z bazą danych programu Sybase. |Tak |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Tak |

**Przykład:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Sybase.

Aby skopiować dane z programu Sybase, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **Sybase** | Tak |
| tableName | Nazwa tabeli w bazie danych programu Sybase. | Nie (Jeśli określono parametr "query" w źródle działania) |

**Przykład**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz zestawu danych `RelationalTable` z określonym typem, nadal jest on obsługiwany w przypadku, gdy zamierzasz korzystać z nowego.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło programu Sybase.

### <a name="sybase-as-source"></a>Sybase jako źródło

Aby skopiować dane z programu Sybase, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **SybaseSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz źródła z wpisaną `RelationalSource`, jest ono nadal obsługiwane w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="data-type-mapping-for-sybase"></a>Mapowanie typu danych dla programu Sybase

Podczas kopiowania danych z programu Sybase następujące mapowania są używane z typów danych programu Sybase do Azure Data Factory danych pośrednich. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

Program Sybase obsługuje typy T-SQL. Aby uzyskać tabelę mapowania z typów SQL w celu Azure Data Factory pośrednich typów danych, zobacz sekcję [Mapowanie typów danych z łącznika Azure SQL Database](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) .

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).



## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
