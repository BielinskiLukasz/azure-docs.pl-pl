---
title: Kopiowanie danych z tabeli SAP przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z tabeli SAP do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
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
ms.openlocfilehash: 9c4e22e997b4ad8c36d8aaf84d1bb8aacb5c5529
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680238"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Kopiowanie danych z tabeli SAP przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z tabeli SAP. Aby uzyskać więcej informacji, zobacz [Omówienie działania kopiowania](copy-activity-overview.md).

>[!TIP]
>Aby poznać ogólną pomoc techniczną w scenariuszu integracji danych w systemie SAP, zobacz [integracja danych SAP przy użyciu Azure Data Factory oficjalny dokument](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) z szczegółowym wprowadzeniem, comparsion i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik tabeli SAP jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z tabeli SAP można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W każdym przypadku ten łącznik tabeli SAP obsługuje:

- Kopiowanie danych z tabeli SAP w:

  - Składnik SAP ERP Central (SAP ECC) w wersji 7,01 lub nowszej (w ostatnim stosie pakietów pomocy technicznej SAP wydanej po 2015).
  - System SAP Business Warehouse (SAP BW) w wersji 7,01 lub nowszej (w ostatnim stosie pakietów pomocy technicznej SAP wydanej po 2015).
  - SAP S/4HANA.
  - Inne produkty w oprogramowaniu SAP Business Suite w wersji 7,01 lub nowszej (w ostatnim stosie pakietów obsługi SAP wydanej po 2015).

- Kopiowanie danych z zarówno przezroczystej tabeli SAP, tabeli w puli, tabeli klastrowanej, jak i widoku.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego lub bezpiecznej komunikacji sieciowej (SNC), jeśli SNC jest skonfigurowany.
- Nawiązywanie połączenia z serwerem aplikacji SAP lub serwerem komunikatów SAP.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika tabeli SAP, należy wykonać następujące:

- Skonfiguruj własne środowisko Integration Runtime (w wersji 3,17 lub nowszej). Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie własnego środowiska Integration Runtime](create-self-hosted-integration-runtime.md).

- Pobierz 64-bitowy [Łącznik SAP dla Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) z witryny sieci Web SAP i zainstaluj go na komputerze, na którym działa środowisko Integration Runtime. Podczas instalacji upewnij się, że wybrano opcję **Zainstaluj zestawy do GAC** w oknie **opcjonalne kroki instalacji** .

  ![Zainstaluj łącznik SAP dla platformy .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP używany na łączniku tabeli SAP Data Factory musi mieć następujące uprawnienia:

  - Autoryzacja przy użyciu miejsc docelowych zdalnego wywołania funkcji (RFC).
  - Uprawnienia do wykonywania działania obiektu autoryzacji S_SDSAUTH.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika tabeli SAP.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla SAP BW połączonej usługi:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `type` | Właściwość `type` musi mieć wartość `SapTable`. | Tak |
| `server` | Nazwa serwera, na którym znajduje się wystąpienie SAP.<br/>Służy do nawiązywania połączenia z serwerem aplikacji SAP. | Nie |
| `systemNumber` | Numer systemu SAP systemu.<br/>Służy do nawiązywania połączenia z serwerem aplikacji SAP.<br/>Dozwolona wartość: dwucyfrowa liczba dziesiętna reprezentowana jako ciąg. | Nie |
| `messageServer` | Nazwa hosta serwera komunikatów SAP.<br/>Służy do nawiązywania połączenia z serwerem komunikatów SAP. | Nie |
| `messageServerService` | Nazwa usługi lub numer portu serwera komunikatów.<br/>Służy do nawiązywania połączenia z serwerem komunikatów SAP. | Nie |
| `systemId` | Identyfikator systemu SAP, w którym znajduje się tabela.<br/>Służy do nawiązywania połączenia z serwerem komunikatów SAP. | Nie |
| `logonGroup` | Grupa logowania dla systemu SAP.<br/>Służy do nawiązywania połączenia z serwerem komunikatów SAP. | Nie |
| `clientId` | Identyfikator klienta w systemie SAP.<br/>Dozwolona wartość: 3-cyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| `language` | Język, którego używa System SAP.<br/>Wartość domyślna to `EN`.| Nie |
| `userName` | Nazwa użytkownika, który ma dostęp do serwera SAP. | Tak |
| `password` | Hasło użytkownika. Oznacz to pole typem `SecureString`, aby bezpiecznie przechowywać je w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| `sncMode` | Wskaźnik aktywacji SNC w celu uzyskania dostępu do serwera SAP, na którym znajduje się tabela.<br/>Użyj, jeśli chcesz używać SNC do nawiązywania połączenia z serwerem SAP.<br/>Dozwolone wartości to `0` (wyłączone, domyślne) lub `1` (włączone). | Nie |
| `sncMyName` | Nazwa SNC inicjatora do uzyskania dostępu do serwera SAP, na którym znajduje się tabela.<br/>Ma zastosowanie, gdy `sncMode` jest włączona. | Nie |
| `sncPartnerName` | Nazwa SNC partnera komunikacyjnego, aby uzyskać dostęp do serwera SAP, na którym znajduje się tabela.<br/>Ma zastosowanie, gdy `sncMode` jest włączona. | Nie |
| `sncLibraryPath` | Biblioteka produktu zabezpieczeń zewnętrznych w celu uzyskania dostępu do serwera SAP, na którym znajduje się tabela.<br/>Ma zastosowanie, gdy `sncMode` jest włączona. | Nie |
| `sncQop` | Poziom jakości ochrony SNC, który ma zostać zastosowany.<br/>Ma zastosowanie, gdy `sncMode` jest włączona. <br/>Dozwolone wartości to `1` (uwierzytelnianie), `2` (integralność), `3` (prywatność), `8` (wartość domyślna), `9` (maksimum). | Nie |
| `connectVia` | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Wymagane jest samodzielne środowisko Integration Runtime, jak wspomniano wcześniej w [wymaganiach wstępnych](#prerequisites). |Tak |

**Przykład 1: łączenie z serwerem aplikacji SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Przykład 2: Nawiązywanie połączenia z serwerem komunikatów SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Przykład 3: Łączenie przy użyciu SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości do definiowania zestawów danych, zobacz [zestawy danych](concepts-datasets-linked-services.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli SAP.

Aby można było skopiować dane z i do SAP BW połączonej z usługą Open Hub, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `type` | Właściwość `type` musi mieć wartość `SapTableResource`. | Tak |
| `tableName` | Nazwa tabeli SAP, z której mają zostać skopiowane dane. | Tak |

### <a name="example"></a>Przykład

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby zapoznać się z pełną listą sekcji i właściwości definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez źródło tabeli SAP.

### <a name="sap-table-as-source"></a>Tabela SAP jako źródło

Aby skopiować dane z tabeli SAP, obsługiwane są następujące właściwości:

| Właściwość                         | Opis                                                  | Wymagany |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | Właściwość `type` musi mieć wartość `SapTableSource`.         | Tak      |
| `rowCount`                         | Liczba wierszy do pobrania.                              | Nie       |
| `rfcTableFields`                   | Pola (kolumny) do skopiowania z tabeli SAP. Na przykład `column0, column1`. | Nie       |
| `rfcTableOptions`                  | Opcje filtrowania wierszy w tabeli SAP. Na przykład `COLUMN0 EQ 'SOMEVALUE'`. Zobacz również tabelę operatorów zapytań SAP w dalszej części tego artykułu. | Nie       |
| `customRfcReadTableFunctionModule` | Niestandardowy moduł funkcji RFC, który może służyć do odczytywania danych z tabeli SAP.<br>Możesz użyć niestandardowego modułu funkcji RFC, aby określić, jak dane są pobierane z systemu SAP i zwracane do Data Factory. Moduł funkcji niestandardowych musi mieć zaimplementowany interfejs (import, Export, tabele) podobny do `/SAPDS/RFC_READ_TABLE2`, który jest domyślnym interfejsem używanym przez Data Factory. | Nie       |
| `partitionOption`                  | Mechanizm partycji, który ma zostać odczytany z tabeli SAP. Obsługiwane są następujące opcje: <ul><li>`None`</li><li>`PartitionOnInt` (normalne wartości całkowite lub całkowite ze uzupełnieniem zero po lewej stronie, na przykład `0000012345`)</li><li>`PartitionOnCalendarYear` (4 cyfry w formacie "RRRR")</li><li>`PartitionOnCalendarMonth` (6 cyfr w formacie "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 cyfr w formacie "RRRRMMDD")</li></ul> | Nie       |
| `partitionColumnName`              | Nazwa kolumny używanej do partycjonowania danych.                | Nie       |
| `partitionUpperBound`              | Maksymalna wartość kolumny określona w `partitionColumnName`, która będzie używana do kontynuowania partycjonowania. | Nie       |
| `partitionLowerBound`              | Minimalna wartość kolumny określona w `partitionColumnName`, która będzie używana do kontynuowania partycjonowania. | Nie       |
| `maxPartitionsNumber`              | Maksymalna liczba partycji, do których mają zostać podzielone dane.     | Nie       |

>[!TIP]
>Jeśli tabela SAP zawiera dużą ilość danych, takich jak kilka miliardów wierszy, użyj `partitionOption` i `partitionSetting`, aby podzielić dane na mniejsze partycje. W takim przypadku dane są odczytywane na partycję, a każda partycja danych jest pobierana z serwera SAP za pośrednictwem pojedynczego wywołania RFC.<br/>
<br/>
>`partitionOption` jak `partitionOnInt` na przykład, liczba wierszy w każdej partycji jest obliczana z następującą formułą: (łącznie wierszy między `partitionUpperBound` i `partitionLowerBound`)/`maxPartitionsNumber`.<br/>
<br/>
>Aby równolegle ładować partycje danych w celu przyspieszenia kopiowania, stopień równoległy jest kontrolowany przez ustawienie [`parallelCopies`](copy-activity-performance.md#parallel-copy) w działaniu kopiowania. Na przykład jeśli ustawisz `parallelCopies` na cztery, Data Factory jednocześnie generuje i uruchamia cztery zapytania na podstawie określonej opcji partycji i ustawień, a każde zapytanie pobiera część danych z tabeli SAP. Zdecydowanie zalecamy, aby `maxPartitionsNumber` wielokrotność wartości właściwości `parallelCopies`. Podczas kopiowania danych do magazynu danych opartego na plikach, jest również ponownie wykonywane polecenie zapisu do folderu jako wiele plików (Określ nazwę folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

W `rfcTableOptions`można użyć następujących typowych operatorów zapytań SAP do filtrowania wierszy:

| Operator | Opis |
| :------- | :------- |
| `EQ` | Równa się |
| `NE` | Nie równa się |
| `LT` | Mniej niż |
| `LE` | Mniejsze niż lub równe |
| `GT` | Większe niż |
| `GE` | Większe niż lub równe |
| `LIKE` | Jak w `LIKE 'Emma%'` |

### <a name="example"></a>Przykład

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapowania typu danych dla tabeli SAP

Podczas kopiowania danych z tabeli SAP następujące mapowania są używane z typów danych tabeli SAP do Azure Data Factory danych pośrednich. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ SAP ABAP | Data Factory typ danych pośrednich |
|:--- |:--- |
| `C` (ciąg) | `String` |
| `I` (liczba całkowita) | `Int32` |
| `F` (float) | `Double` |
| `D` (Data) | `String` |
| `T` (Time) | `String` |
| `P` (rozpakowanego BCD, Currency, decimal, ilość) | `Decimal` |
| `N` (wartość numeryczna) | `String` |
| `X` (dane binarne i nieprzetworzone) | `String` |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
