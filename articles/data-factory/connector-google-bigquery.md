---
title: Kopiowanie danych z usługi Google BigQuery przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z usługi Google BigQuery do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Fabryka danych.
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
ms.openlocfilehash: d57260db74a1373eb461c560e013be620910eee9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680939"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Google BigQuery przy użyciu Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z usługi Google BigQuery. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Google BigQuery jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi Google BigQuery można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Data Factory udostępnia wbudowany sterownik umożliwiający nawiązywanie połączeń. W związku z tym nie trzeba ręcznie instalować sterownika, aby można było używać tego łącznika.

>[!NOTE]
>Ten łącznik usługi Google BigQuery został utworzony na podstawie interfejsów API BigQuery. Należy pamiętać, że BigQuery ogranicza maksymalną częstotliwość żądań przychodzących i wymusza odpowiednie przydziały dla poszczególnych projektów, zapoznaj się z [Przydziałami & limity — żądania interfejsu API](https://cloud.google.com/bigquery/quotas#api_requests). Upewnij się, że nie Wyzwalasz zbyt wielu współbieżnych żądań do konta.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika usługi Google BigQuery.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Google BigQuery są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **GoogleBigQuery**. | Tak |
| projektu | Identyfikator projektu domyślnego projektu BigQuery, względem którego ma być wysyłana kwerenda.  | Tak |
| additionalProjects | Rozdzielana przecinkami lista identyfikatorów projektów publicznych projektów BigQuery do dostępu.  | Nie |
| requestGoogleDriveScope | Czy zażądać dostępu do usługi dysk Google. Umożliwienie dostępu do usługi dysk Google umożliwia obsługę tabel federacyjnych, które łączą dane BigQuery z danymi z dysku Google. Wartość domyślna to **false**.  | Nie |
| authenticationType | Mechanizm uwierzytelniania OAuth 2,0 używany do uwierzytelniania. Funkcji serviceauthentication można używać tylko w przypadku samodzielnej Integration Runtime. <br/>Dozwolone wartości to **UserAuthentication** i **serviceauthentication**. Zapoznaj się z sekcjami poniżej tej tabeli, aby uzyskać więcej właściwości i próbek JSON odpowiednio dla tych typów uwierzytelniania. | Tak |

### <a name="using-user-authentication"></a>Korzystanie z uwierzytelniania użytkowników

Ustaw właściwość "AuthenticationType" na wartość **UserAuthentication**, a następnie określ następujące właściwości wraz z właściwościami ogólnymi opisanymi w poprzedniej sekcji:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| clientId | Identyfikator aplikacji użytej do wygenerowania tokenu odświeżania. | Nie |
| clientSecret | Wpis tajny aplikacji użyty do wygenerowania tokenu odświeżania. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| refreshToken | Token odświeżania uzyskany od firmy Google używany do autoryzacji dostępu do usługi BigQuery. Dowiedz się, jak uzyskać [token dostępu OAuth 2,0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) i [ten blog dla społeczności](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |

**Przykład:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Korzystanie z uwierzytelniania usługi

Ustaw właściwość "AuthenticationType" na wartość **serviceauthentication**i określ następujące właściwości wraz z właściwościami ogólnymi opisanymi w poprzedniej sekcji. Tego typu uwierzytelniania można używać tylko w przypadku samodzielnej obsługi Integration Runtime.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| email | Identyfikator poczty e-mail konta usługi, który jest używany na potrzeby usługi serviceauthentication. Może być używany tylko w Integration Runtime samodzielnego udostępniania.  | Nie |
| Atrybut keyfilepath | Pełna ścieżka do pliku klucza. P12, który jest używany do uwierzytelniania adresu e-mail konta usługi. | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM zawierającego certyfikaty zaufanych urzędów certyfikacji używane do weryfikacji serwera podczas łączenia za pośrednictwem protokołu SSL. Tę właściwość można ustawić tylko w przypadku korzystania z protokołu SSL na samodzielnym Integration Runtime. Wartość domyślna to plik cacerts. pem instalowany z programem Integration Runtime.  | Nie |
| useSystemTrustStore | Określa, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu czy z określonego pliku PEM. Wartość domyślna to **false**.  | Nie |

**Przykład:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Google BigQuery.

Aby skopiować dane z usługi Google BigQuery, ustaw właściwość Type zestawu danych na **GoogleBigQueryObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **GoogleBigQueryObject** | Tak |
| Zestawu | Nazwa zestawu danych usługi Google BigQuery. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| tabele | Nazwa tabeli. |Nie (Jeśli określono parametr "query" w źródle działania)  |
| tableName | Nazwa tabeli. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowych obciążeń Użyj `dataset` i `table`. | Nie (Jeśli określono parametr "query" w źródle działania) |

**Przykład**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez typ źródła Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource jako typ źródła

Aby skopiować dane z usługi Google BigQuery, ustaw typ źródła w działaniu Copy na **GoogleBigQuerySource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość **GoogleBigQuerySource**. | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Może to być na przykład `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
