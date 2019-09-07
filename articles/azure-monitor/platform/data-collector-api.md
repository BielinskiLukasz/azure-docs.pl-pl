---
title: Azure Monitor interfejs API modułu zbierającego dane HTTP | Microsoft Docs
description: Korzystając z Azure Monitor interfejsu API modułu zbierającego dane HTTP, można dodać dane po JSON do Log Analytics obszaru roboczego z dowolnego klienta, który może wywołać interfejs API REST. W tym artykule opisano sposób korzystania z interfejsu API i przedstawiono przykłady sposobu publikowania danych przy użyciu różnych języków programowania.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: bwren
ms.openlocfilehash: a34faeb42fce0a1ee7960f71ffce176492495f9c
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744512"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Wysyłanie danych dziennika do Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)
W tym artykule pokazano, jak za pomocą interfejsu API modułu zbierającego dane HTTP wysyłać dane dziennika do Azure Monitor z klienta interfejsu API REST.  Opisano w nim sposób formatowania danych zbieranych przez skrypt lub aplikację, uwzględniania ich w żądaniu oraz żądania autoryzowane przez Azure Monitor.  Przykłady dla programu PowerShell, C#i języka Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Interfejs API modułu zbierającego dane HTTP Azure Monitor jest w publicznej wersji zapoznawczej.

## <a name="concepts"></a>Pojęcia
Za pomocą interfejsu API modułu zbierającego dane HTTP można wysyłać dane dziennika do obszaru roboczego Log Analytics w Azure Monitor z dowolnego klienta, który może wywołać interfejs API REST.  Może to być element Runbook w Azure Automation, który zbiera dane zarządzania z platformy Azure lub innej chmury, lub może być alternatywnym systemem zarządzania, który używa Azure Monitor do konsolidowania i analizowania danych dziennika.

Wszystkie dane w obszarze roboczym Log Analytics są przechowywane jako rekord z określonym typem rekordu.  Dane można sformatować w celu wysłania do interfejsu API modułu zbierającego dane HTTP jako wielu rekordów w formacie JSON.  Podczas przesyłania danych w repozytorium dla każdego rekordu w ładunku żądania tworzony jest pojedynczy rekord.


![Moduł zbierający dane HTTP — Omówienie](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Utwórz żądanie
Aby użyć interfejsu API modułu zbierającego dane HTTP, należy utworzyć żądanie POST zawierające dane do wysłania w JavaScript Object Notation (JSON).  W następnych trzech tabelach wymieniono atrybuty, które są wymagane dla każdego żądania. Opiszemy każdy atrybut bardziej szczegółowo w dalszej części artykułu.

### <a name="request-uri"></a>Identyfikator URI żądania
| Atrybut | Właściwość |
|:--- |:--- |
| Metoda |POST |
| Identyfikator URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Typ zawartości |application/json |

### <a name="request-uri-parameters"></a>Parametry identyfikatora URI żądania
| Parametr | Opis |
|:--- |:--- |
| Identyfikator |Unikatowy identyfikator obszaru roboczego Log Analytics. |
| Resource |Nazwa zasobu interfejsu API:/API/logs. |
| Wersja interfejsu API |Wersja interfejsu API, która ma być używana z tym żądaniem. Obecnie jest to 2016-04-01. |

### <a name="request-headers"></a>Nagłówki żądań
| nagłówek | Opis |
|:--- |:--- |
| Authorization |Podpis autoryzacji. W dalszej części artykułu można zapoznać się z informacjami na temat tworzenia nagłówka HMAC-SHA256. |
| Typ dziennika |Określ typ rekordu przesyłanego danych. Może zawierać tylko litery, cyfry i znaki podkreślenia (_) i nie może przekraczać 100 znaków. |
| x-ms-date |Data przetworzenia żądania w formacie RFC 1123. |
| x-ms-AzureResourceId | Identyfikator zasobu zasobu platformy Azure, z którym mają być skojarzone dane. Spowoduje to wypełnienie właściwości [_ResourceId](log-standard-properties.md#_resourceid) i umożliwi uwzględnienie danych w zapytaniach [kontekstu zasobów](design-logs-deployment.md#access-mode) . Jeśli to pole nie zostanie określone, dane nie zostaną uwzględnione w zapytaniach kontekstu zasobów. |
| time-generated-field | Nazwa pola w danych, które zawiera sygnaturę czasową elementu danych. Jeśli określisz pole, jego zawartość zostanie użyta dla **TimeGenerated**. Jeśli to pole nie zostanie określone, wartością domyślną dla **TimeGenerated** jest czas, w którym wiadomość zostanie pozyskana. Zawartość pola komunikat powinna być zgodna z formatem ISO 8601 RRRR-MM-DDTgg: mm: SSS. |

## <a name="authorization"></a>Authorization
Każde żądanie do Azure Monitor interfejsu API modułu zbierającego dane HTTP musi zawierać nagłówek autoryzacji. Aby uwierzytelnić żądanie, należy podpisać żądanie przy użyciu klucza podstawowego lub pomocniczego obszaru roboczego, który wysyła żądanie. Następnie Przekaż ten podpis jako część żądania.   

Oto format nagłówka autoryzacji:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*Identyfikator obszaru roboczego* jest unikatowym identyfikatorem obszaru roboczego log Analytics. *Sygnatura* jest kod uwierzytelniania wiadomości oparty na wykorzystaniu [skrótu (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) , który jest zbudowany z żądania, a następnie obliczany przy użyciu [algorytmu SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Następnie kodujesz go za pomocą kodowania base64.

Użyj tego formatu, aby zakodować ciąg podpisu **SharedKey** :

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Oto przykład ciągu podpisu:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Jeśli masz ciąg podpisu, zakoduj go przy użyciu algorytmu HMAC-SHA256 w ciągu kodowania UTF-8, a następnie zakoduj wynik jako Base64. Użyj tego formatu:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Przykłady w następnych sekcjach zawierają przykładowy kod ułatwiający utworzenie nagłówka autoryzacji.

## <a name="request-body"></a>Treść żądania
Treść wiadomości musi być w formacie JSON. Musi zawierać co najmniej jeden rekord o nazwie właściwości i par wartości w następującym formacie. Nazwa właściwości może zawierać tylko litery, cyfry i znaki podkreślenia (_).

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Wiele rekordów można wsadowo w pojedynczym żądaniu, używając następującego formatu. Wszystkie rekordy muszą być tego samego typu rekordów.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Typ rekordu i właściwości
Typ rekordu niestandardowego można zdefiniować podczas przesyłania danych za pośrednictwem interfejsu API modułu zbierającego dane HTTP Azure Monitor. Obecnie nie można zapisywać danych do istniejących typów rekordów, które zostały utworzone przez inne typy danych i rozwiązania. Azure Monitor odczytuje dane przychodzące, a następnie tworzy właściwości, które pasują do typów danych wprowadzonych wartości.

Każde żądanie do interfejsu API modułu zbierającego dane musi zawierać nagłówek **log-Type** o nazwie typu rekordu. Sufiks **_CL** jest automatycznie dołączany do wprowadzonej nazwy, aby odróżnić ją od innych typów dzienników jako dziennika niestandardowego. Na przykład, jeśli wprowadzisz nazwę **MyNewRecordType**, Azure monitor tworzy rekord z typem **MyNewRecordType_CL**. Pozwala to zagwarantować, że nie występują żadne konflikty między nazwami typów utworzonych przez użytkownika i tymi, które są dostarczane w bieżących lub przyszłych rozwiązaniach firmy Microsoft.

Aby zidentyfikować typ danych właściwości, Azure Monitor dodaje sufiks do nazwy właściwości. Jeśli właściwość zawiera wartość null, właściwość nie jest uwzględniona w tym rekordzie. Ta tabela zawiera listę typów danych właściwości i odpowiadających im sufiksów:

| Typ danych właściwości | Suffix |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Data/godzina |_t |
| GUID |_g |

Typ danych, który Azure Monitor używa dla każdej właściwości, zależy od tego, czy typ rekordu dla nowego rekordu już istnieje.

* Jeśli typ rekordu nie istnieje, Azure Monitor tworzy nowy, przy użyciu wnioskowania o typie JSON, aby określić typ danych dla każdej właściwości nowego rekordu.
* Jeśli typ rekordu istnieje, Azure Monitor próbuje utworzyć nowy rekord na podstawie istniejących właściwości. Jeśli typ danych właściwości w nowym rekordzie nie jest zgodny i nie można go przekonwertować na istniejący typ lub jeśli rekord zawiera właściwość, która nie istnieje, Azure Monitor tworzy nową właściwość o odpowiednim sufiksie.

Na przykład ten wpis przesłania spowoduje utworzenie rekordu z trzema właściwościami, **number_d**, **boolean_b**i **string_s**:

![Przykładowy rekord 1](media/data-collector-api/record-01.png)

Jeśli następnie Następny wpis zostanie przesłany ze wszystkimi wartościami sformatowanymi jako ciągi, właściwości nie zostaną zmienione. Te wartości można przekonwertować na istniejące typy danych:

![Przykładowy rekord 2](media/data-collector-api/record-02.png)

Ale jeśli następnie zostanie wykonane następne zgłoszenie, Azure Monitor utworzy nowe właściwości **boolean_d** i **string_d**. Nie można przekonwertować tych wartości:

![Przykładowy rekord 3](media/data-collector-api/record-03.png)

Jeśli po utworzeniu typu rekordu zostanie przesłana następująca pozycja, Azure Monitor utworzy rekord z trzema właściwościami: **liczba_s**, **boolean_s**i **string_s**. W tym wpisie każda z wartości początkowych jest formatowana jako ciąg:

![Przykładowy rekord 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Właściwości zastrzeżone
Następujące właściwości są zarezerwowane i nie powinny być używane w niestandardowym typie rekordu. Jeśli ładunek zawiera dowolne z tych nazw właściwości, zostanie wyświetlony komunikat o błędzie.

- tenant

## <a name="data-limits"></a>Limity danych
Istnieją pewne ograniczenia dotyczące danych ogłoszonych w interfejsie API zbierania danych Azure Monitor.

* Maksymalnie 30 MB na wpis do Azure Monitor interfejsu API modułu zbierającego dane. Jest to limit rozmiaru pojedynczego wpisu. Jeśli dane z jednego wpisu, który przekracza 30 MB, należy podzielić dane na fragmenty o mniejszych rozmiarach i wysyłać je współbieżnie.
* Maksymalny limit 32 KB dla wartości pól. Jeśli wartość pola jest większa niż 32 KB, dane zostaną obcięte.
* Zalecana maksymalna liczba pól dla danego typu to 50. Jest to praktyczny limit z perspektywy użyteczności i wyszukiwania.  
* Tabela w obszarze roboczym Log Analytics obsługuje tylko do 500 kolumn (określanych jako pole w tym artykule). 
* Maksymalna liczba znaków w nazwie kolumny to 500.

## <a name="return-codes"></a>Kody powrotne
Kod stanu HTTP 200 oznacza, że żądanie zostało odebrane do przetworzenia. Oznacza to, że operacja została ukończona pomyślnie.

W tej tabeli przedstawiono pełny zestaw kodów stanu, które mogą zostać zwrócone przez usługę:

| Kod | State | Kod błędu | Opis |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Żądanie zostało pomyślnie zaakceptowane. |
| 400 |Nieprawidłowe żądanie |InactiveCustomer |Obszar roboczy został zamknięty. |
| 400 |Nieprawidłowe żądanie |InvalidApiVersion |Określona wersja interfejsu API nie została rozpoznana przez usługę. |
| 400 |Nieprawidłowe żądanie |InvalidCustomerId |Określony identyfikator obszaru roboczego jest nieprawidłowy. |
| 400 |Nieprawidłowe żądanie |InvalidDataFormat |Przesłano nieprawidłowy plik JSON. Treść odpowiedzi może zawierać więcej informacji na temat sposobu rozwiązania błędu. |
| 400 |Nieprawidłowe żądanie |InvalidLogType |Określony typ dziennika zawiera znaki specjalne lub wartości numeryczne. |
| 400 |Nieprawidłowe żądanie |MissingApiVersion |Nie określono wersji interfejsu API. |
| 400 |Nieprawidłowe żądanie |MissingContentType |Typ zawartości nie został określony. |
| 400 |Nieprawidłowe żądanie |MissingLogType |Nie określono wymaganego typu dziennika wartości. |
| 400 |Nieprawidłowe żądanie |UnsupportedContentType |Typ zawartości nie został ustawiony na wartość **Application/JSON**. |
| 403 |Zabroniony |InvalidAuthorization |Usługa nie może uwierzytelnić żądania. Sprawdź, czy identyfikator obszaru roboczego i klucz połączenia są prawidłowe. |
| 404 |Nie znaleziono | | Podany adres URL jest nieprawidłowy lub żądanie jest zbyt duże. |
| 429 |Zbyt wiele żądań | | W usłudze występuje duża ilość danych z Twojego konta. Spróbuj ponownie wykonać żądanie później. |
| 500 |Wewnętrzny błąd serwera |UnspecifiedError |Usługa napotkała błąd wewnętrzny. Spróbuj ponownie wykonać żądanie. |
| 503 |Usługa niedostępna |ServiceUnavailable |Usługa jest obecnie niedostępna do odbierania żądań. Spróbuj ponownie wykonać żądanie. |

## <a name="query-data"></a>Zapytania o dane
Aby wykonać zapytanie o dane przesyłane przez Azure Monitor interfejs API modułu zbierającego dane HTTP, Wyszukaj rekordy o **typie** , który jest równy podanej wartości **LogType** , dołączonej do **_CL**. Na przykład jeśli użyto **MyCustomLog**, zwróć wszystkie rekordy z `MyCustomLog_CL`.

## <a name="sample-requests"></a>Przykładowe żądania
W następnych sekcjach znajdziesz przykłady przesyłania danych do Azure Monitor interfejsu API modułu zbierającego dane HTTP przy użyciu różnych języków programowania.

Dla każdego przykładu wykonaj następujące kroki, aby ustawić zmienne nagłówka autoryzacji:

1. W Azure Portal zlokalizuj obszar roboczy Log Analytics.
2. Wybierz pozycję **Ustawienia zaawansowane** i **połączone źródła**.
2. Z prawej strony **identyfikatora obszaru roboczego**wybierz ikonę kopiowania, a następnie wklej identyfikator jako wartość zmiennej **identyfikatora klienta** .
3. Na prawo od **klucza podstawowego**wybierz ikonę kopiowania, a następnie wklej identyfikator jako wartość zmiennej **klucza współużytkowanego** .

Alternatywnie można zmienić zmienne dla typu dziennika i danych JSON.

### <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Przykład w języku C#
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Przykładowa Python 2
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>Alternatywy i zagadnienia
Interfejs API modułu zbierającego dane powinien obejmować większość potrzeb związanych z zbieraniem danych w usłudze Azure logs, ale istnieją sytuacje, w których alternatywa może być wymagana do pokonania niektórych ograniczeń interfejsu API. Wszystkie dostępne opcje są następujące:

| Różne | Opis | Najlepiej dopasowane do |
|---|---|---|
| [Zdarzenia niestandardowe](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Pozyskiwanie oparte na natywnym zestawie SDK w Application Insights | Application Insights, zazwyczaj Instrumentacja w ramach zestawu SDK w aplikacji, oferuje możliwość wysyłania niestandardowych danych za pomocą niestandardowych zdarzeń. | <ul><li> Dane, które są generowane w aplikacji, ale nie są pobierane przez zestaw SDK przy użyciu jednego z domyślnych typów danych (żądania, zależności, wyjątki itd.).</li><li> Dane, które najczęściej są skorelowane z innymi danymi aplikacji w Application Insights </li></ul> |
| [Interfejs API modułu zbierającego dane](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) w dziennikach Azure monitor | Interfejs API modułu zbierającego dane w dziennikach Azure Monitor jest całkowicie otwartym sposobem pozyskiwania danych. Wszystkie dane sformatowane w obiekcie JSON mogą być wysyłane w tym miejscu. Po wysłaniu zostanie on przetworzony i udostępniony w dziennikach w celu skorelowania z innymi danymi w dziennikach lub w odniesieniu do innych danych Application Insights. <br/><br/> Można stosunkowo łatwo przekazać dane jako pliki do obiektu blob platformy Azure, z którego te pliki zostaną przetworzone i przekazane do Log Analytics. Zobacz [ten](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) artykuł, aby zapoznać się z przykładową implementacją tego potoku. | <ul><li> Dane, które nie są generowane w aplikacji w Application Insights.</li><li> Przykłady obejmują wyszukiwanie i tabele faktów, dane referencyjne, statystyki wstępnie zagregowane i tak dalej. </li><li> Zamierzone dla danych, które będą odwoływać się do innych Azure Monitor danych (Application Insights, inne typy danych dzienników, Security Center, Azure Monitor dla kontenerów/maszyn wirtualnych itd.). </li></ul> |
| [Eksplorator danych platformy Azure](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Azure Eksplorator danych (ADX) to platforma danych, która umożliwia Application Insights analiz i Azure Monitor dzienników. Teraz ogólnie dostępne ("GA") korzystanie z platformy danych w jego pierwotnej postaci zapewnia pełną elastyczność (ale wymaganie obciążenia zarządzania) w ramach klastra (RBAC, szybkość przechowywania, schemat itp.). ADX zapewnia wiele [opcji](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) pozyskiwania [, w tym pliki CSV, TSV i JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) . | <ul><li> Dane, które nie zostaną skorelowane do żadnych innych danych w Application Insights lub dzienników. </li><li> Dane wymagające zaawansowanych możliwości pozyskiwania lub przetwarzania nie są obecnie dostępne w dziennikach Azure Monitor. </li></ul> |


## <a name="next-steps"></a>Następne kroki
- Użyj [interfejsu API przeszukiwania dzienników](../log-query/log-query-overview.md) , aby pobrać dane z obszaru roboczego log Analytics.

- Dowiedz się więcej na temat [tworzenia potoku danych za pomocą interfejsu API modułu zbierającego dane](create-pipeline-datacollector-api.md) przy użyciu przepływu pracy Logic Apps do Azure monitor.
