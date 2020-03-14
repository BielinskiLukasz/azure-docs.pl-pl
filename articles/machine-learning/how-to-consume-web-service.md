---
title: Utwórz klienta dla modelu wdrożonego jako usługa sieci Web
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z usługi sieci Web, która została wygenerowana, gdy model został wdrożony z modelem Azure Machine Learning. Usługa sieci Web uwidacznia interfejs API REST. Tworzenie klientów dla tego interfejsu API przy użyciu wybranego języka programowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: 4c3e60e9c296dc8e3a1e31a52a262d8462237407
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283695"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Korzystanie z modelu usługi Azure Machine Learning, wdrożyć jako usługę sieci web
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Wdrażanie modelu usługi Azure Machine Learning w postaci usługi sieci web tworzy interfejs API REST. Można wysyłać dane do tego interfejsu API i odbierać prognozowania zwracane przez model. W tym dokumencie dowiesz się, jak tworzyć klientów dla usługi sieci Web za C#pomocą, go, Java i Python.

Usługa sieci Web jest tworzona podczas wdrażania obrazu do Azure Container Instances, usługi Azure Kubernetes lub tablic opartych na programowalnych polach (FPGA). Tworzysz obrazy z zarejestrowanych modeli i plików oceniania. Pobrano identyfikator URI służący do uzyskiwania dostępu do usługi sieci Web przy użyciu [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Jeśli jest włączone uwierzytelnianie, można również pobrać klucze uwierzytelniania lub tokeny przy użyciu zestawu SDK.

Ogólny przepływ pracy służący do tworzenia klienta korzystającego z usługi sieci Web Machine Learning to:

1. Użyj zestawu SDK, aby uzyskać informacje o połączeniu.
1. Określ typ danych żądania używanych przez model.
1. Utwórz aplikację, która wywołuje usługę sieci Web.

> [!TIP]
> Przykłady w tym dokumencie są tworzone ręcznie bez użycia specyfikacji OpenAPI (Swagger). Jeśli została włączona Specyfikacja OpenAPI dla danego wdrożenia, można użyć narzędzi, takich jak [Swagger-codegen](https://github.com/swagger-api/swagger-codegen) , aby utworzyć biblioteki klienckie dla usługi.

## <a name="connection-information"></a>informacje o połączeniu

> [!NOTE]
> Użyj zestawu SDK Azure Machine Learning, aby uzyskać informacje o usłudze sieci Web. To jest zestaw SDK języka Python. Możesz użyć dowolnego języka, aby utworzyć klienta dla usługi.

Klasa [Azure. Core. WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) zawiera informacje potrzebne do utworzenia klienta. Następujące właściwości `Webservice` są przydatne do tworzenia aplikacji klienckich:

* `auth_enabled` — Jeśli uwierzytelnianie klucza jest włączone, `True`; w przeciwnym razie `False`.
* `token_auth_enabled` — Jeśli uwierzytelnianie tokenu jest włączone, `True`; w przeciwnym razie `False`.
* `scoring_uri` — adres interfejsu API REST.
* `swagger_uri` — adres specyfikacji OpenAPI. Ten identyfikator URI jest dostępny po włączeniu automatycznego generowania schematu. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md#schema).

Istnieją trzy sposoby, aby pobrać te informacje dotyczące wdrożonymi usługami sieci web:

* Podczas wdrażania modelu obiekt `Webservice` jest zwracany z informacjami o usłudze:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Za pomocą `Webservice.list` można pobrać listę wdrożonych usług sieci Web dla modeli w obszarze roboczym. Można dodać filtry, aby zawęzić listę informacje zwrócone. Aby uzyskać więcej informacji o tym, co można filtrować, zobacz dokumentację dotyczącą usługi [WebService. list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) .

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Jeśli znasz nazwę wdrożonej usługi, możesz utworzyć nowe wystąpienie `Webservice`i podać obszar roboczy i nazwę usługi jako parametry. Nowy obiekt zawiera informacje o wdrożonej usługi.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Zabezpieczona usługa sieci Web

Jeśli wdrożona usługa sieci Web została zabezpieczona przy użyciu certyfikatu SSL, można użyć [protokołu HTTPS](https://en.wikipedia.org/wiki/HTTPS) , aby nawiązać połączenie z usługą za pomocą identyfikatora URI oceny lub struktury Swagger. Protokół HTTPS pomaga w zabezpieczeniu komunikacji między klientem a usługą sieci Web przez szyfrowanie komunikacji między nimi. Szyfrowanie używa [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokół TLS jest czasami nazywany *SSL* (SSL), który był POPRZEDNIKIEM protokołu TLS.

> [!IMPORTANT]
> Usługi sieci Web wdrożone przez Azure Machine Learning obsługują tylko protokół TLS w wersji 1,2. Podczas tworzenia aplikacji klienckiej upewnij się, że jest ona obsługiwana w tej wersji.

Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSL w celu zabezpieczenia usługi sieci Web za pośrednictwem Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Uwierzytelnianie dla usług

Azure Machine Learning zapewnia dwa sposoby kontroli dostępu do usług sieci Web.

|Metoda uwierzytelniania|ACI|AKS|
|---|---|---|
|Klucz|Domyślnie wyłączone| Domyślnie włączona|
|Token| Niedostępny| Domyślnie wyłączone |

Podczas wysyłania żądania do usługi, która jest zabezpieczona za pomocą klucza lub tokenu, użyj nagłówka __autoryzacji__ w celu przekazania klucza lub tokenu. Klucz lub token musi być sformatowany jako `Bearer <key-or-token>`, gdzie `<key-or-token>` jest wartością klucza lub tokenu.

#### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Po włączeniu uwierzytelniania dla wdrożenia automatycznie tworzone są klucze uwierzytelniania.

* Uwierzytelnianie jest domyślnie włączone podczas wdrażania w usłudze Azure Kubernetes Service.
* Podczas wdrażania programu w celu Azure Container Instances uwierzytelnianie jest domyślnie wyłączone.

Aby kontrolować uwierzytelnianie, użyj parametru `auth_enabled` podczas tworzenia lub aktualizowania wdrożenia.

W przypadku włączenia uwierzytelniania można użyć metody `get_keys`, aby pobrać podstawowy i pomocniczy klucz uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli musisz ponownie wygenerować klucz, użyj [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Uwierzytelnianie przy użyciu tokenów

Po włączeniu uwierzytelniania tokenów dla usługi sieci Web użytkownik musi podać Azure Machine Learning token JWT do usługi sieci Web, aby uzyskać do niej dostęp. 

* Uwierzytelnianie tokenu jest domyślnie wyłączone podczas wdrażania w usłudze Azure Kubernetes Service.
* Uwierzytelnianie tokenu nie jest obsługiwane w przypadku wdrażania do Azure Container Instances.

Aby kontrolować uwierzytelnianie tokenu, użyj parametru `token_auth_enabled` podczas tworzenia lub aktualizowania wdrożenia.

Jeśli jest włączone uwierzytelnianie tokenu, można użyć metody `get_token`, aby pobrać token okaziciela i czas wygaśnięcia tokenów:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Musisz zażądać nowego tokenu po upływie `refresh_by` tokenu. 

## <a name="request-data"></a>Żądanie danych

Interfejs API REST oczekuje, że treść żądania jako dokument JSON o następującej strukturze:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Struktury danych wymaga do dopasowania, jakie oceniania skrypt i modelu w oczekiwanej usługi. Skrypt oceniania może modyfikować danych przed przekazaniem go do modelu.

Na przykład model w [pouczeniu w ramach](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) przykładu w notesie oczekuje tablicy o wartości 10 cyfr. Skrypt oceniania w tym przykładzie tworzy tablicę numpy z żądania i przekazuje ją do modelu. Poniższy przykład przedstawia dane, które oczekuje na tę usługę:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
```

Usługa sieci web może akceptować wiele zestawów danych w jednym żądaniu. Zwraca dokument JSON zawierający tablicę odpowiedzi.

### <a name="binary-data"></a>Dane binarne

Aby uzyskać informacje na temat włączania obsługi danych binarnych w usłudze, zobacz [dane binarne](how-to-deploy-and-where.md#binary).

### <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Aby uzyskać informacje na temat włączania obsługi mechanizmu CORS w usłudze, zobacz [udostępnianie zasobów między źródłami](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Wywołania tej usługi (C#)

Ten przykład pokazuje, jak używać C# do wywoływania usługi sieci Web utworzonej na podstawie [szkolenia w ramach przykładu notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) :

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Wywoływanie usługi (Przejdź do)

W tym przykładzie pokazano, jak używać języka go do wywoływania usługi sieci Web utworzonej na podstawie [szkolenia w ramach przykładu notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) :

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Wywoływanie usługi (Java)

W tym przykładzie pokazano, jak używać języka Java do wywoływania usługi sieci Web utworzonej na podstawie [szkolenia w ramach przykładu notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) :

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Wywoływanie usługi (Python)

W tym przykładzie pokazano, jak używać języka Python do wywoływania usługi sieci Web utworzonej na podstawie [szkolenia w ramach przykładu notesu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) :

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Zwrócone wyniki są podobne do następujących dokumentów JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Korzystanie z usługi z Power BI

Power BI obsługuje użycie usług sieci Web Azure Machine Learning do wzbogacania danych w Power BI z przewidywaniami. 

Aby wygenerować usługę sieci Web obsługiwaną do użycia w Power BI, schemat musi obsługiwać format wymagany przez Power BI. [Dowiedz się, jak utworzyć schemat obsługiwany przez Power BI](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script).

Po wdrożeniu usługi sieci Web jest ona zużywana z Power BI przepływów danych. [Dowiedz się, jak korzystać z usługi sieci web Azure Machine Learning z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić architekturę referencyjną dla oceny w czasie rzeczywistym dla modeli Python i głębokiego uczenia, przejdź do [centrum architektury platformy Azure](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
