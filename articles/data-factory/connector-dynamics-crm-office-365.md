---
title: Skopiuj dane z programu i do programu Dynamics CRM lub Dynamics 365 (Common Data Service) za pomocą Azure Data Factory
description: Informacje o kopiowaniu danych z programu Microsoft Dynamics CRM lub Microsoft Dynamics 365 (Common Data Service) do obsługiwanych magazynów danych ujścia lub z obsługiwanych magazynów danych źródłowych do programu Dynamics CRM lub Dynamics 365 przy użyciu działania kopiowania w potoku usługi Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: c9adcf72eeec82fd4b8f1805fca1f284c0b953b7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680980"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Skopiuj dane z programu i do usługi Dynamics 365 (Common Data Service) lub Dynamics CRM przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do kopiowania danych z programów i do programu Microsoft Dynamics 365 lub Microsoft Dynamics CRM. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi Dynamics 365 (Common Data Service) lub Dynamics CRM można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Możesz również skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do usługi Dynamics 365 (Common Data Service) lub Dynamics CRM. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik Dynamics obsługuje system Dynamics w wersji 7. x do 9. x zarówno w trybie online, jak i lokalnie. Dokładniej mówiąc,

- Wersja 7. x Maps do programu Dynamics CRM 2015
- Wersja 8. x Maps do programu Dynamics CRM 2016 i wczesnej wersji systemu Dynamics 365
- Wersja 9. x mapuje do nowszej wersji systemu Dynamics 365

Zapoznaj się z poniższą tabelą dotyczącą obsługiwanych typów i konfiguracji uwierzytelniania dla odpowiednich wersji i produktów systemu Dynamics. (IFD jest krótkie dla wdrożenia dostępnego z Internetu).

| Wersje systemu Dynamics | Typy uwierzytelniania | Przykłady połączonej usługi |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office | [Dynamics Online i uwierzytelnianie w usłudze Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 w środowisku lokalnym z IFD <br> Program Dynamics CRM 2016 w środowisku lokalnym z IFD <br> Program Dynamics CRM 2015 w środowisku lokalnym z IFD | IFD | [Lokalne uwierzytelnianie przy użyciu programu IFD + IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Dla systemu Dynamics 365 obsługiwane są następujące typy aplikacji:

- Dynamics 365 for Sales
- Dynamics 365 dla usługi klienta
- Dynamics 365 dla usługi Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Inne typy aplikacji, np. Finanse i operacje, talent itp., nie są obsługiwane przez ten łącznik.

Ten łącznik systemu Dynamics jest oparty na [narzędziach programu Dynamics Xrm](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Aby skopiować dane z systemu **dynamics 365 Finanse i operacje**, można użyć [łącznika programu Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla programu Dynamics.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Dynamics są obsługiwane następujące właściwości.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 i Dynamics CRM Online

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **Dynamics**, **DynamicsCrm**lub **CommonDataServiceForApps**. | Tak |
| Typ wdrożenia | Typ wdrożenia wystąpienia programu Dynamics. Musi to być **"online"** dla usługi Dynamics online. | Tak |
| serviceUri | Adres URL usługi wystąpienia usługi Dynamics, np. `https://adfdynamics.crm.dynamics.com`. | Tak |
| authenticationType | Typ uwierzytelniania służący do nawiązywania połączenia z serwerem Dynamics. Określ **wartość "Office 365"** dla usługi Dynamics online. | Tak |
| nazwa użytkownika | Określ nazwę użytkownika, aby połączyć się z usługą Dynamics. | Tak |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie dla źródła, tak dla ujścia, jeśli źródłowa usługa nie ma środowiska Integration Runtime |

>[!NOTE]
>Łącznik Dynamics używany do używania opcjonalnej właściwości "organizationName" do identyfikowania wystąpienia usługi Dynamics CRM/365 online. Mimo że nadal pracuje, zaleca się określenie nowej właściwości "serviceUri", aby uzyskać lepszą wydajność odnajdywania wystąpień.

**Przykład: Dynamics Online przy użyciu uwierzytelniania usługi Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 i Dynamics CRM w środowisku lokalnym z IFD

*Dodatkowe właściwości, które są porównywane z usługą Dynamics Online, to "hostName" i "Port".*

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **Dynamics**, **DynamicsCrm**lub **CommonDataServiceForApps**. | Tak |
| Typ wdrożenia | Typ wdrożenia wystąpienia programu Dynamics. Musi to być **"OnPremisesWithIfd"** dla platformy Dynamics w środowisku lokalnym z IFD.| Tak |
| Nazw | Nazwa hosta lokalnego serwera Dynamics. | Tak |
| port | Port lokalnego serwera Dynamics. | Nie, wartość domyślna to 443 |
| Organizacja | Nazwa organizacji wystąpienia programu Dynamics. | Tak |
| authenticationType | Typ uwierzytelniania do nawiązywania połączenia z serwerem Dynamics. Określ **"IFD"** dla platformy Dynamics lokalnie z IFD. | Tak |
| nazwa użytkownika | Określ nazwę użytkownika, aby połączyć się z usługą Dynamics. | Tak |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Możesz oznaczyć to pole jako element SecureString, aby bezpiecznie przechowywać go w podajniku APD, lub przechowywać hasło w Azure Key Vault i pozwolić działaniu kopiowania ściągania podczas wykonywania kopii danych — Dowiedz się więcej z [poświadczeń magazynu w Key Vault](store-credentials-in-key-vault.md). | Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie dla źródła, tak dla ujścia |

**Przykład: Dynamics lokalnego z IFD przy użyciu uwierzytelniania IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Dynamics.

Aby skopiować dane z i do systemu Dynamics, obsługiwane są następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość **DynamicsEntity**, **DynamicsCrmEntity**lub **CommonDataServiceForAppsEntity**. |Tak |
| entityName | Logiczna Nazwa jednostki do pobrania. | Nie dla źródła (Jeśli określono "zapytanie" w źródle aktywności), tak dla ujścia |

**Przykład:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez typy obiektów źródłowych i obiektów ujścia systemu Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics jako typ źródła

Aby skopiować dane z systemu Dynamics, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi mieć wartość **DynamicsSource**, **DynamicsCrmSource**lub **CommonDataServiceForAppsSource**. | Tak |
| query | FetchXML to własny język zapytań używany w usłudze Dynamics (online i lokalna). Zobacz poniższy przykład. Aby dowiedzieć się więcej, zobacz [Tworzenie zapytań za pomocą FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nie (Jeśli określono "EntityName" w zestawie danych) |

>[!NOTE]
>Kolumna klucza podstawowego będzie zawsze kopiowana nawet wtedy, gdy rzutowanie kolumn skonfigurowane w zapytaniu FetchXML nie zawiera go.

> [!IMPORTANT]
>- Podczas kopiowania danych z systemu Dynamics jawne Mapowanie kolumn z elementu Dynamics do ujścia jest opcjonalne, ale wysoce ponownie podpolecenia, aby zapewnić deterministyczny wynik kopiowania.
>- Podczas importowania schematu w interfejsie użytkownika tworzenia modułu ADF wnioskuje schemat przez próbkowanie najważniejszych wierszy z wyniku zapytania programu Dynamics w celu zainicjowania listy kolumn źródłowych, w którym przypadku kolumny bez wartości w górnych wierszach nie zostaną pominięte. Takie samo zachowanie ma zastosowanie w przypadku wykonywania kopii w przypadku braku jawnego mapowania. Możesz przejrzeć i dodać więcej kolumn do mapowania, które zostaną uznane za środowisko uruchomieniowe kopiowania.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
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
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Przykładowe zapytanie FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics jako typ ujścia

Aby skopiować dane do programu Dynamics, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type ujścia działania Copy musi mieć wartość **DynamicsSink**, **DynamicsCrmSink**lub **CommonDataServiceForAppsSink**. | Tak |
| WriteBehavior | Zachowanie zapisu operacji.<br/>Dozwolona wartość to **"upsert"** . | Tak |
| alternateKeyName | Określ alternatywną nazwę klucza zdefiniowaną w jednostce, aby wykonać operację "upsert". | Nie |
| writeBatchSize | Liczba wierszy danych zapisywana w Dynamics w każdej partii. | Nie (wartość domyślna to 10) |
| ignoreNullValues | Wskazuje, czy ignorować wartości null z danych wejściowych (z wyjątkiem pól klucza) podczas operacji zapisu.<br/>Dozwolone wartości to **true** i **false**.<br>- **true**: pozostawienie danych w obiekcie docelowym nie zmienia się po wykonaniu operacji upsert/Update. Wstaw zdefiniowaną wartość domyślną podczas wykonywania operacji wstawiania.<br/>- **false**: zaktualizuj dane w obiekcie docelowym do wartości null po wykonaniu operacji upsert/Update. Wstaw wartość NULL po wykonaniu operacji wstawiania. | Nie (wartość domyślna to false) |

>[!NOTE]
>Wartość domyślna ujścia "**writeBatchSize**" i działanie Copy " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " dla usługi Dynamics sink to 10. W związku z tym rekordy 100 są przesyłane do systemu Dynamics współbieżnie.

W przypadku usługi Dynamics 365 online obowiązuje limit [2 współbieżnych wywołań wsadowych na organizację](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). W przypadku przekroczenia tego limitu zostanie zgłoszony błąd "serwer zajęty", zanim pierwsze żądanie zostanie kiedykolwiek wykonane. Pozostawienie "writeBatchSize" mniejszej lub równej 10 pozwoli uniknąć tego ograniczenia współbieżnych wywołań.

Optymalna kombinacja parametrów "**writeBatchSize**" i "**parallelCopies**" zależy od schematu jednostki, np. liczby kolumn, rozmiaru wierszy, liczby wtyczek/przepływów pracy/przepływów działania połączonych z tymi wywołaniami itp. Domyślnym ustawieniem 10 writeBatchSize * 10 parallelCopies jest zalecenie zależne od usługi Dynamics, które będzie działać w przypadku większości jednostek systemu Dynamics, chociaż może to nie być Najlepsza wydajność. Możesz dostosować wydajność przez dostosowanie kombinacji w ustawieniach działania kopiowania.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapowanie typu danych dla systemu Dynamics

Podczas kopiowania danych z systemu Dynamics następujące mapowania są używane z typów danych systemu Dynamics do Data Factory danych pośrednich. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

Skonfiguruj odpowiedni typ danych Data Factory w strukturze zestawu danych na podstawie typu danych Dynamics, korzystając z następującej tabeli mapowania.

| Typ danych Dynamics | Data Factory typ danych pośrednich | Obsługiwane jako źródło | Obsługiwane jako ujścia |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Długie | ✓ | ✓ |
| AttributeTypeCode. Boolean | Wartość logiczna | ✓ | ✓ |
| AttributeType. Customer | Identyfikator GUID | ✓ | |
| AttributeType. DateTime | Datę | ✓ | ✓ |
| AttributeType. Decimal | Dokładności | ✓ | ✓ |
| AttributeType. Double | Double | ✓ | ✓ |
| AttributeType. EntityName | Ciąg | ✓ | ✓ |
| AttributeType. Integer | Elementem | ✓ | ✓ |
| AttributeType. Lookup | Identyfikator GUID | ✓ | ✓ (z skojarzonym pojedynczym elementem docelowym) |
| AttributeType. ManagedProperty | Wartość logiczna | ✓ | |
| AttributeType. MEMO | Ciąg | ✓ | ✓ |
| AttributeType. Money | Dokładności | ✓ | ✓ |
| AttributeType. Owner | Identyfikator GUID | ✓ | |
| AttributeType. Lista wyboru | Elementem | ✓ | ✓ |
| AttributeType. unikatowy identyfikator | Identyfikator GUID | ✓ | ✓ |
| AttributeType. String | Ciąg | ✓ | ✓ |
| AttributeType. State | Elementem | ✓ | ✓ |
| AttributeType. status | Elementem | ✓ | ✓ |

> [!NOTE]
> Typy danych Dynamics, attributeType. CalendarRules, AttributeType. MultiSelectPicklist i AttributeType. PartyList nie są obsługiwane.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
