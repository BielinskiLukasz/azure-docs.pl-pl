---
title: Kopiowanie danych z programu SAP Business Warehouse za pośrednictwem usługi Open Hub przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o kopiowaniu danych z programu SAP Business Warehouse (BW) za pośrednictwem usługi Open Hub do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
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
ms.openlocfilehash: a35246aff99ec78e665e3be4afd47409959bef63
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089721"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiowanie danych z programu SAP Business Warehouse za pośrednictwem usługi Open Hub przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z programu SAP Business Warehouse (BW) za pośrednictwem usługi Open Hub. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

>[!TIP]
>Aby poznać ogólną pomoc techniczną w scenariuszu integracji danych w systemie SAP, zobacz [integracja danych SAP przy użyciu Azure Data Factory oficjalny dokument](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) z szczegółowym wprowadzeniem, comparsion i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten magazyn SAP Business Warehouse za pośrednictwem łącznika Open Hub jest obsługiwany przez następujące działania:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z programu SAP Business Warehouse można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W ramach tego łącznika centrum danych programu SAP Business Warehouse obsługuje następujące rozwiązania:

- SAP Business Warehouse **w wersji 7,01 lub nowszej (w ostatnim stosie pakietów pomocy technicznej SAP wydanej po roku 2015)** .
- Kopiowanie danych za pośrednictwem lokalnej tabeli docelowej centrum, która znajduje się poniżej, może być DSO, InfoCube, wieloelementowy, DataSource itd.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.
- Łączenie z serwerem aplikacji.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Otwórz integrację z centrum 

[SAP BW otwieranie usługi centrum](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) jest wydajnym sposobem wyodrębnienia danych z SAP BW. Na poniższym diagramie przedstawiono jeden z typowych przepływów, które znajdują się w systemie SAP, w tym przypadku dane są przesyłane z modułu SAP ECC — > PSA-> module DSO->.

SAP BW Otwórz lokalizację docelową (OHD) określa miejsce docelowe, do którego są przekazywane dane SAP. Wszystkie obiekty obsługiwane przez proces Transfer danych SAP (DTP) mogą być używane jako otwarte źródła danych, na przykład DSO, InfoCube, DataSource itp. Typ miejsca docelowego typu Open Hub — miejsce przechowywania danych przekazywanych — może to być tabela bazy danych (lokalna lub zdalna) i pliki proste. Ten SAP BW otworzyć łącznik centrum obsługi kopiowania danych z lokalnej tabeli OHD w BW. W przypadku korzystania z innych typów można połączyć się bezpośrednio z bazą danych lub systemem plików przy użyciu innych łączników.

![SAP BW otworzyć Centrum](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Przepływ ekstrakcji różnicowej

Moduł ADF SAP BW otwarty łącznik centrum udostępnia dwie opcjonalne właściwości `excludeLastRequest` : `baseRequestId` i, które mogą być używane do obsługi obciążenia różnicowego z otwartego centrum. 

- **excludeLastRequestId**: Określa, czy mają zostać wykluczone rekordy ostatniego żądania. Wartość domyślna to true. 
- **baseRequestId**: Identyfikator żądania dla ładowania różnicowego. Po jego ustawieniu zostaną pobrane tylko dane z identyfikatorem żądania większym niż wartość tej właściwości. 

Ogólnie, wyodrębnianie z oprogramowania SAP InfoProviders do Azure Data Factory (ADF) składa się z dwóch kroków: 

1. **Proces Transfer danych SAP BW (DTP)** Ten krok powoduje skopiowanie danych z SAP BW InfoProvider do SAP BW otwartej tabeli usługi Hub 

1. **Kopiowanie danych ADF** W tym kroku zostanie odczytana tabela "Otwórz Centrum" w ramach łącznika ADF 

![Przepływ ekstrakcji różnicowej](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

W pierwszym kroku jest wykonywane DTP. Każde wykonanie powoduje utworzenie nowego identyfikatora żądania SAP. Identyfikator żądania jest przechowywany w tabeli Open Hub, a następnie używany przez łącznik ADF do identyfikowania różnic. Dwa kroki są wykonywane asynchronicznie: DTP jest wyzwalany przez SAP, a kopiowanie danych ADF jest wyzwalane za pomocą ADF. 

Domyślnie moduł ADF nie odczytuje najnowszych różnic z otwartej tabeli centrum (opcja "Wyklucz ostatnie żądanie" ma wartość true). W takim przypadku dane w podajniku APD nie są aktualne na 100% z danymi w otwartej tabeli centrum (brakuje ostatniej delty). W przypadku powrotu ta procedura gwarantuje, że żadne wiersze nie zostaną utracone z powodu asynchronicznego wyodrębniania. Działa dobrze nawet wtedy, gdy moduł ADF odczytuje otwartą tabelę centrów, podczas gdy DTP nadal zapisuje w tej samej tabeli. 

Zwykle przechowujesz maksymalny identyfikator kopiowanego żądania w ostatnim przebiegu przez ADF w tymczasowym magazynie danych (takim jak obiekt blob platformy Azure na wyższym diagramie). W związku z tym to samo żądanie nie jest odczytywane po raz drugi przez ADF w kolejnym przebiegu. Należy zauważyć, że dane nie są automatycznie usuwane z otwartej tabeli centrum.

W celu zapewnienia prawidłowej obsługi różnic nie można mieć identyfikatorów żądań z różnych DTPs w tej samej otwartej tabeli. W związku z tym nie należy tworzyć więcej niż jednego DTP dla każdego otwartego miejsca docelowego (OHD). Gdy wymagana jest Ekstrakcja pełna i różnicowa z tego samego InfoProvider, należy utworzyć dwa OHDs dla tego samego InfoProvider. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika centrum danych SAP Business Warehouse, należy wykonać następujące czynności:

- Skonfiguruj własne Integration Runtime w wersji 3,13 lub nowszej. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.

- Pobierz **64-bitowy [Łącznik SAP .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html)**  z witryny sieci Web SAP i zainstaluj go na samoobsługowej maszynie IR. W przypadku instalowania programu w oknie opcjonalne kroki instalacji upewnij się, że wybrano opcję **Zainstaluj zestawy do GAC** , jak pokazano na poniższej ilustracji. 

    ![Instalowanie łącznika SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP używany w łączniku Data Factory BW musi mieć następujące uprawnienia: 

    - Autoryzacja dla RFC i SAP BW. 
    - Uprawnienia do działania "Execute" obiektu autoryzacji "S_SDSAUTH".

- Utwórz typ docelowy typu "Open Hub" SAP jako **tabelę bazy danych** z zaznaczoną opcją "klucz techniczny".  Zaleca się również pozostawienie usunięcia danych z tabeli jako niezaznaczone, chociaż nie jest to wymagane. Użyj DTP (bezpośrednio wykonaj lub Zintegruj do istniejącego łańcucha procesów), aby wystawić dane z obiektu źródłowego (na przykład modułu), które zostały wybrane do tabeli docelowej centrum.

## <a name="getting-started"></a>Wprowadzenie

> [!TIP]
>
> Aby zapoznać się ze wskazówkami dotyczącymi używania łącznika SAP BW Open Hub, zobacz temat [ładowanie danych z oprogramowania SAP Business Warehouse (BW) przy użyciu Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla programu SAP Business Warehouse Open Hub Connector.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi SAP Business Warehouse Open Hub połączonej:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć ustawioną wartość: **SapOpenHub** | Tak |
| server | Nazwa serwera, na którym znajduje się wystąpienie SAP BW. | Tak |
| systemNumber | Numer systemu SAP BW.<br/>Dozwolona wartość: dwucyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| clientId | Identyfikator klienta klienta w systemie SAP w.<br/>Dozwolona wartość: 3-cyfrowa liczba dziesiętna reprezentowana jako ciąg. | Tak |
| język | Język, którego używa System SAP. | Nie (wartość domyślna to **EN**)|
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Tak |
| password | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano [](#prerequisites)w wymaganiach wstępnych. |Tak |

**Przykład:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez SAP BW otwartego centrum danych.

Aby skopiować dane z i do SAP BW Otwórz Centrum, ustaw właściwość Type zestawu danych na **SapOpenHubTable**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **SapOpenHubTable**.  | Tak |
| openHubDestinationName | Nazwa miejsca docelowego typu Open Hub, z którego mają zostać skopiowane dane. | Tak |

Jeśli ustawienia `excludeLastRequest` i `baseRequestId` w zestawie danych, nadal są obsługiwane w stanie takim, w jakim jest zalecane użycie nowego modelu w źródle aktywności.

**Przykład:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez SAP BW Otwórz źródło centrum.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW otworzyć Centrum jako źródło

Aby skopiować dane z SAP BW Otwórz Centrum, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **SapOpenHubSource**. | Tak |
| excludeLastRequest | Określa, czy mają zostać wykluczone rekordy ostatniego żądania. | Nie (wartość domyślna to **true**) |
| baseRequestId | Identyfikator żądania dla ładowania różnicowego. Po jego ustawieniu zostaną pobrane tylko dane z identyfikatorem żądania **większym niż** wartość tej właściwości.  | Nie |

>[!TIP]
>Jeśli otwarta tabela centrów zawiera tylko dane wygenerowane przez pojedynczy identyfikator żądania, na przykład zawsze należy wykonać pełne ładowanie i zastąpić istniejące dane w tabeli, lub uruchomić DTP tylko raz dla testu, pamiętaj o usunięciu zaznaczenia opcji "excludeLastRequest" w celu skopiowania d ATA out.

Aby przyspieszyć ładowanie danych, można ustawić [`parallelCopies`](copy-activity-performance.md#parallel-copy) działanie kopiowania w celu załadowania danych z SAP BW otwartego centrum. Jeśli na przykład ustawisz `parallelCopies` cztery, Data Factory współbieżnie wykonuje cztery wywołania RFC, a każde wywołanie RFC pobierze część danych z SAP BW otwartej tabeli z podziałem na partycje według identyfikatora żądania DTP i identyfikatora pakietu. Ma to zastosowanie, gdy liczba unikatowych identyfikatorów żądań DTP i identyfikator pakietu jest większa niż wartość `parallelCopies`. Podczas kopiowania danych do magazynu danych opartego na plikach, jest również ponownie wykonywane polecenie zapisu do folderu jako wiele plików (Określ nazwę folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapowanie typu danych dla SAP BW Open Hub

Podczas kopiowania danych z SAP BW otwartego centrum następujące mapowania są używane z poziomu SAP BW danych do Azure Data Factory pośrednich typów danych. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ SAP ABAP | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| C (String) | String |
| I (integer) | Int32 |
| F (Float) | Double |
| D (Date) | String |
| T (Time) | String |
| P (BCD Packed, Currency, Decimal, Qty) | Decimal |
| N (NUMC) | String |
| X (Binary and Raw) | String |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
