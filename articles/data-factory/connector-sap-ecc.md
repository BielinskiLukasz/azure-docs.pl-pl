---
title: Kopiowanie danych z systemu SAP ECC
description: Informacje o kopiowaniu danych z systemu SAP ECC do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81413794"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopiowanie danych z systemu SAP ECC przy użyciu Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z oprogramowania SAP Enterprise Central Component (ECC). Aby uzyskać więcej informacji, zobacz [Omówienie działania kopiowania](copy-activity-overview.md).

>[!TIP]
>Aby poznać ogólną pomoc techniczną w scenariuszu integracji danych w systemie SAP, zobacz [integracja danych SAP przy użyciu Azure Data Factory oficjalny dokument](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) z szczegółowym wprowadzeniem, comparsion i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik SAP ECC jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z protokołu SAP ECC można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W przypadku tego łącznika SAP ECC obsługiwane są następujące rozwiązania:

- Kopiowanie danych z systemu SAP ECC w oprogramowaniu SAP NetWeaver w wersji 7,0 lub nowszej.
- Kopiowanie danych z dowolnych obiektów udostępnianych przez usługi SAP ECC OData, takie jak:

  - Tabele lub widoki SAP.
  - Obiekty interfejsu programowania aplikacji biznesowej [BAPI].
  - Wyodrębnianie danych.
  - Dane lub dokumenty pośrednie (IDOCs) wysyłane do integracji procesów SAP (PI), które można odbierać jako OData za pośrednictwem kart względnych.

- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

>[!TIP]
>Aby skopiować dane z systemu SAP ECC za pośrednictwem tabeli lub widoku SAP, użyj łącznika [tabeli SAP](connector-sap-table.md) , który jest szybszy i bardziej skalowalny.

## <a name="prerequisites"></a>Wymagania wstępne

Ogólnie rzecz biorąc, SAP ECC uwidacznia jednostki za pośrednictwem usług OData za pośrednictwem bramy SAP. Aby użyć tego łącznika SAP ECC, należy wykonać następujące:

- **Skonfiguruj bramę SAP Gateway**. W przypadku serwerów z systemem SAP NetWeaver w wersji nowszej niż 7,4 Brama SAP jest już zainstalowana. W przypadku wcześniejszych wersji należy zainstalować osadzoną bramę SAP lub system centrów SAP Gateway przed udostępnieniem danych SAP ECC za pomocą usług OData. Aby skonfigurować bramę SAP, zobacz [Przewodnik instalacji](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktywuj i skonfiguruj usługę SAP OData**. Usługę OData można aktywować za pomocą TCODE SICF w ciągu kilku sekund. Można również skonfigurować, które obiekty muszą być uwidocznione. Aby uzyskać więcej informacji, zobacz [wskazówki krok po kroku](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika SAP ECC.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi SAP ECC są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `type` | `type` Właściwość musi być ustawiona na `SapEcc`wartość. | Tak |
| `url` | Adres URL usługi SAP ECC OData. | Tak |
| `username` | Nazwa użytkownika używana do nawiązywania połączenia z funkcją SAP ECC. | Nie |
| `password` | Hasło w postaci zwykłego tekstu używane do nawiązywania połączenia z funkcją SAP ECC. | Nie |
| `connectVia` | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie określisz środowiska uruchomieniowego, zostanie użyta domyślna usługa Azure Integration Runtime. | Nie |

### <a name="example"></a>Przykład

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [zestawy danych](concepts-datasets-linked-services.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP ECC.

Aby skopiować dane z protokołu SAP ECC, ustaw `type` Właściwość zestawu danych na `SapEccResource`.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `path` | Ścieżka jednostki usługi SAP ECC OData. | Tak |

### <a name="example"></a>Przykład

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby zapoznać się z pełną listą sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez źródło SAP ECC.

### <a name="sap-ecc-as-a-source"></a>System SAP ECC jako źródło

Aby skopiować dane z protokołu SAP ECC, należy `type` ustawić właściwość w `source` sekcji działania kopiowania na `SapEccSource`.

W `source` sekcji działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `type` | Właściwość sekcji działania kopiowania musi być ustawiona na `SapEccSource`wartość. `source` `type` | Tak |
| `query` | Opcje zapytania OData służące do filtrowania danych. Przykład:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Łącznik SAP ECC kopiuje dane ze połączonego adresu URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Aby uzyskać więcej informacji, zobacz [składniki URL usługi OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

### <a name="example"></a>Przykład

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mapowania typu danych dla SAP ECC

Podczas kopiowania danych z systemu SAP ECC następujące mapowania są używane z typów danych OData dla danych SAP ECC do Azure Data Factory pośrednich typów danych. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych OData | Data Factory typ danych pośrednich |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Złożone typy danych nie są obecnie obsługiwane.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
