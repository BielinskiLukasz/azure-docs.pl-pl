---
title: Kopiowanie danych z źródła REST przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z chmury lub lokalnego źródła REST do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 1178c18b29c5e38d33e51ff0da5db683990daed3
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546966"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego REST przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z punktu końcowego REST. Artykuł jest oparty na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md), co przedstawia ogólne omówienie działania kopiowania.

Różnica między tym łącznikiem REST, [łącznika http](connector-http.md) i [łącznikiem tabeli sieci Web](connector-web-table.md) :

- **Łącznik REST** obsługuje kopiowanie danych z interfejsów API RESTful; 
- **Łącznik http** jest ogólny do pobierania danych z dowolnego punktu końcowego http, np. do pobrania pliku. Przed udostępnieniem tego łącznika REST może wystąpić potrzeba użycia łącznika HTTP do kopiowania danych z interfejsu API RESTful, który jest obsługiwany, ale mniej funkcjonalny jest porównywany z łącznikiem REST.
- **Łącznik tabeli sieci Web** wyodrębnia zawartość tabeli z strony html.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Dane można kopiować ze źródła REST do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).

W przypadku tego ogólnego łącznika REST obsługiwane są następujące funkcje:

- Pobieranie danych z punktu końcowego REST przy użyciu metod **Get** i **post** .
- Pobieranie danych przy użyciu jednego z następujących uwierzytelnień: **anonimowe**, **podstawowe**, nazwa **główna usługi AAD**i **zarządzane tożsamości dla zasobów platformy Azure**.
- **[Podział na strony](#pagination-support)** w interfejsach API REST.
- Kopiowanie odpowiedzi JSON [w formacie REST jako-is](#export-json-response-as-is) lub analizowanie jej przy użyciu [mapowania schematu](copy-activity-schema-and-type-mapping.md#schema-mapping). Obsługiwany jest tylko ładunek odpowiedzi w formacie **JSON** .

> [!TIP]
> Aby przetestować żądanie pobrania danych przed skonfigurowaniem łącznika REST w Data Factory, Dowiedz się więcej na temat specyfikacji interfejsu API dla wymagań dotyczących nagłówka i treści. Aby sprawdzić poprawność, można użyć narzędzi, takich jak program Poster lub przeglądarka sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpocznij

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania jednostek Data Factory, które są specyficzne dla łącznika REST.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi REST są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** musi być ustawiona na wartość **RestService**. | Tak |
| url | Podstawowy adres URL usługi REST. | Tak |
| enableServerCertificateValidation | Czy sprawdzać poprawność certyfikatu protokołu SSL po stronie serwera podczas nawiązywania połączenia z punktem końcowym. | Nie<br /> (wartość domyślna to **true**) |
| authenticationType | Typ uwierzytelniania używany do nawiązywania połączenia z usługą REST. Dozwolone wartości to **Anonymous**, **Basic**, **AadServicePrincipal** i **ManagedServiceIdentity**. Zapoznaj się z odpowiednimi sekcjami poniżej, aby uzyskać więcej właściwości i przykładów. | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, ta właściwość używa Azure Integration Runtime domyślnego. |Nie |

### <a name="use-basic-authentication"></a>Użyj uwierzytelniania podstawowego

Ustaw właściwość **AuthenticationType** na wartość **podstawowa**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| Uż | Nazwa użytkownika, która ma być używana w celu uzyskania dostępu do punktu końcowego REST. | Tak |
| hasło | Hasło użytkownika (wartość **username** ). Oznacz to pole jako typ **SecureString** , aby bezpiecznie przechowywać go w Data Factory. Możesz również [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
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

### <a name="use-aad-service-principal-authentication"></a>Użyj uwierzytelniania podstawowego usługi AAD

Ustaw właściwość **AuthenticationType** na wartość **AadServicePrincipal**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji Azure Active Directory. | Tak |
| servicePrincipalKey | Określ klucz aplikacji Azure Active Directory. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| dzierżaw | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Pobierz go, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak |
| aadResourceId | Określ zasób usługi AAD, którego żądasz do autoryzacji, np. `https://management.core.windows.net`.| Tak |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Korzystanie z tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure

Ustaw właściwość **AuthenticationType** na wartość **ManagedServiceIdentity**. Oprócz ogólnych właściwości, które są opisane w poprzedniej sekcji, określ następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| aadResourceId | Określ zasób usługi AAD, którego żądasz do autoryzacji, np. `https://management.core.windows.net`.| Tak |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych REST. 

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z usługi REST, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** zestawu danych musi być ustawiona na wartość **RestResource**. | Tak |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. Łącznik protokołu HTTP kopiuje dane ze połączonego adresu URL: `[URL specified in linked service]/[relative URL specified in dataset]`. | Nie |

Jeśli ustawienia `requestMethod`, `additionalHeaders`, `requestBody` i `paginationRules` w zestawie danych, nadal są obsługiwane w taki sposób, że jest to możliwe, podczas gdy sugerowane jest użycie nowego modelu w źródle aktywności.

**Przykład:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło REST.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>RESZTA jako źródło

W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **RestSource**. | Tak |
| requestMethod | Metoda HTTP. Dozwolone wartości to **Get** (default) i **post**. | Nie |
| additionalHeaders | Dodatkowe nagłówki żądań HTTP. | Nie |
| Elemencie requestbody | Treść żądania HTTP. | Nie |
| paginationRules | Zasady dzielenia na strony w celu redagowania żądań kolejnych stron. Szczegółowe informacje znajdują się w sekcji [Obsługa podziału na strony](#pagination-support) . | Nie |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Ta wartość jest przekroczeniem limitu czasu w celu uzyskania odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. Wartość domyślna to **00:01:40**.  | Nie |
| requestInterval | Czas oczekiwania przed wysłaniem żądania na następną stronę. Wartość domyślna to **00:00:01** |  Nie |

>[!NOTE]
>Łącznik REST ignoruje wszystkie nagłówki "Accept" określone w `additionalHeaders`. Ponieważ łącznik REST obsługuje tylko odpowiedź w formacie JSON, zostanie automatycznie wygenerowany nagłówek `Accept: application/json`.

**Przykład 1: użycie metody get z podziałem na strony**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Przykład 2: użycie metody post**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Obsługa stronicowania

Zwykle interfejs API REST ogranicza swój rozmiar ładunku odpowiedzi pojedynczego żądania w ramach rozsądnej liczby; w celu zwrócenia dużej ilości danych dzieli wynik na wiele stron i wymaga, aby wywołujący wysyłali kolejne żądania, aby uzyskać następną stronę wyniku. Zwykle żądanie jednej strony jest dynamiczne i składa się z informacji zwracanych z odpowiedzi poprzedniej strony.

Ten ogólny łącznik REST obsługuje następujące wzorce stronicowania: 

* Bezwzględny lub względny adres URL (wartość właściwości) w bieżącej treści odpowiedzi
* Bezwzględny lub względny adres URL = wartość nagłówka w bieżących nagłówkach odpowiedzi
* Parametr zapytania następnego żądania = wartość właściwości w bieżącej treści odpowiedzi
* Wartość nagłówka kolejnego żądania w nagłówkach bieżącego odpowiedzi
* Nagłówek następnego żądania = wartość właściwości w bieżącej treści odpowiedzi
* Nagłówek następnego żądania = wartość nagłówka w bieżących nagłówkach odpowiedzi

**Reguły stronicowania** są zdefiniowane jako słownik w zestawie danych, który zawiera jedną lub więcej par klucz-wartość z uwzględnieniem wielkości liter. Konfiguracja zostanie użyta do wygenerowania żądania rozpoczynającego się od drugiej strony. Łącznik przestanie iterację, gdy pobiera kod stanu HTTP 204 (brak zawartości) lub którekolwiek z wyrażeń wykryto w "paginationRules" zwróci wartość null.

**Obsługiwane klucze** w regułach dzielenia na strony:

| Klucz | Opis |
|:--- |:--- |
| AbsoluteUrl | Wskazuje adres URL, na który ma zostać wystawione następne żądanie. Może to być **bezwzględny adres URL lub względny adres URL**. |
| QueryParameters. *request_query_parameter* LUB QueryParameters ["request_query_parameter"] | "request_query_parameter" jest zdefiniowany przez użytkownika, który odwołuje się do jednej nazwy parametru zapytania w następnym adresie URL żądania HTTP. |
| Nagłówka. *request_header* LUB nagłówki ["request_header"] | "request_header" jest zdefiniowany przez użytkownika, który odwołuje się do jednej nazwy nagłówka w następnym żądaniu HTTP. |

**Obsługiwane wartości** w regułach dzielenia na strony:

| Wartość | Opis |
|:--- |:--- |
| Nagłówka. *response_header* LUB nagłówki ["response_header"] | "response_header" jest zdefiniowany przez użytkownika, który odwołuje się do jednej nazwy nagłówka w bieżącej odpowiedzi HTTP, wartość, która zostanie użyta do wygenerowania następnego żądania. |
| Wyrażenie wykryto rozpoczynające się od "$" (reprezentujące element główny treści odpowiedzi) | Treść odpowiedzi powinna zawierać tylko jeden obiekt JSON. Wyrażenie wykryto powinno zwracać pojedynczą wartość pierwotną, która zostanie użyta do wygenerowania następnego żądania. |

**Przykład:**

Interfejs API programu Graph w serwisie Facebook zwraca odpowiedź w następującej strukturze, w której przypadku adres URL następnej strony jest reprezentowany w ***stronicowaniu. Next***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Odpowiednia konfiguracja źródła działania kopiowania REST w szczególności `paginationRules` jest następująca:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="export-json-response-as-is"></a>Eksportuj odpowiedź JSON jako-is

Za pomocą tego łącznika REST można wyeksportować odpowiedź JSON interfejsu API REST w taki sam sposób, jak w przypadku różnych magazynów opartych na plikach. Aby uzyskać taką kopię schematu niezależny od, Pomiń sekcję "struktura" (nazywaną również *schematem*) w temacie zestaw danych i schemat w działaniu kopiowania.

## <a name="schema-mapping"></a>mapowanie schematu

Aby skopiować dane z punktu końcowego REST do obiektu sink tabelarycznych, zapoznaj się z [mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia w Azure Data Factory, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).
