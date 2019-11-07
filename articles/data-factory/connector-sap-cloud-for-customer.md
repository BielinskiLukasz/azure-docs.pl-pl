---
title: Skopiuj dane z chmury SAP dla klienta przy użyciu Azure Data Factory
description: Dowiedz się, jak skopiować dane z chmury SAP dla klienta do obsługiwanych magazynów danych ujścia (lub) z obsługiwanych magazynów danych źródłowych do chmury SAP dla klienta przy użyciu Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 5c47eead8bc8428f533374b2d4892ed684c1f3c7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680261"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Skopiuj dane z chmury SAP dla klienta (C4C) przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z/do chmury SAP dla klienta (C4C). Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

>[!TIP]
>Aby poznać ogólną pomoc techniczną w scenariuszu integracji danych w systemie SAP, zobacz [integracja danych SAP przy użyciu Azure Data Factory oficjalny dokument](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) z szczegółowym wprowadzeniem, comparsion i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ta chmura SAP dla łącznika klienta jest obsługiwana w przypadku następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Możesz skopiować dane z chmury SAP dla klienta do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do chmury SAP dla klienta. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W szczególności ten łącznik umożliwia Azure Data Factory kopiowania danych z chmury SAP dla klienta, w tym usługi SAP Cloud for Sales, SAP Cloud for Service i SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla programu SAP Cloud for Customer Connector.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane w przypadku chmury SAP dla usługi połączonej z klientem:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **SapCloudForCustomer**. | Tak |
| url | Adres URL usługi SAP C4C OData. | Tak |
| nazwa użytkownika | Określ nazwę użytkownika, aby nawiązać połączenie z usługą SAP C4C. | Tak |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie dla źródła, tak dla ujścia |

>[!IMPORTANT]
>Aby skopiować dane do chmury SAP dla klienta, jawnie [utwórz Azure IR](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w chmurze SAP dla klienta i skojarz ją z połączoną usługą jako następujący przykład:

**Przykład:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez środowisko SAP Cloud dla zestawu danych klienta.

Aby skopiować dane z chmury SAP dla klienta, należy ustawić Właściwość Type zestawu danych na **SapCloudForCustomerResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **SapCloudForCustomerResource** |Tak |
| ścieżka | Określ ścieżkę jednostki usługi SAP C4C OData. |Tak |

**Przykład:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez chmurę SAP dla źródła klienta.

### <a name="sap-c4c-as-source"></a>SAP C4C jako źródło

Aby skopiować dane z chmury SAP dla klienta, ustaw typ źródła w działaniu Copy na **SapCloudForCustomerSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **SapCloudForCustomerSource**  | Tak |
| query | Określ niestandardowe zapytanie OData do odczytu danych. | Nie |

Przykładowe zapytanie umożliwiające pobieranie danych z określonego dnia: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C jako ujścia

Aby skopiować dane do chmury SAP dla klienta, ustaw typ ujścia w działaniu Copy na **SapCloudForCustomerSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **SapCloudForCustomerSink**  | Tak |
| WriteBehavior | Zachowanie zapisu operacji. Może to być "INSERT", "Update". | Nie. Domyślne "INSERT". |
| writeBatchSize | Rozmiar wsadu operacji zapisu. Rozmiar wsadu w celu uzyskania najlepszej wydajności może być różny dla różnych tabel lub serwerów. | Nie. Wartość domyślna to 10. |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapowanie typu danych dla chmury SAP dla klienta

Podczas kopiowania danych z chmury SAP dla klienta następujące mapowania są używane w programie SAP Cloud dla typów danych klienta do Azure Data Factory pośrednich typów danych. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych OData SAP C4C | Typ danych pośrednich fabryki danych |
|:--- |:--- |
| EDM. Binary | Byte [] |
| Edm.Boolean | Logiczna |
| EDM. Byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. Decimal | Dokładności |
| Edm.Double | Double |
| EDM. Single | Pojedyncze |
| EDM. GUID | Identyfikator GUID |
| EDM. Int16 | Int16 |
| Edm.Int32 | Elementem |
| Edm.Int64 | Int64 |
| EDM. | Int16 |
| Edm.String | Ciąg |
| EDM. Time | Czasu |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
