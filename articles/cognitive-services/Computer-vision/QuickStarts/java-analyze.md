---
title: 'Szybki start: analizowanie obrazu zdalnego — REST, Java — przetwarzanie obrazów'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start przeprowadzisz analizę obrazu za pomocą interfejsu API przetwarzania obrazów w języku C#.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 2503d8cda05787885935ffab1be6b1efd85ad0cd
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852116"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-java-in-computer-vision"></a>Szybki start: analizowanie obrazu zdalnego przy użyciu interfejsu API REST i języka Java podczas przetwarzania obrazów

W tym przewodniku Szybki start przeprowadzisz analizę przechowywanego zdalnie obrazu w celu wyodrębnienia elementów wizualnych przy użyciu interfejsu API REST przetwarzania obrazów. Metoda [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) umożliwia wyodrębnienie elementów wizualnych na podstawie zawartości obrazu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć zainstalowaną platformę [Java&trade; i zestaw Standard Edition Development Kit 7 lub 8](https://aka.ms/azure-jdks) (JDK 7 lub 8).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Aby uzyskać klucz subskrypcji, zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Utwórz nowy projekt języka Java w ulubionym środowisku IDE lub edytorze. Jeśli ta opcja jest dostępna, utwórz projekt języka Java na podstawie szablonu aplikacji wiersza polecenia.
1. Zaimportuj poniższe biblioteki do projektu języka Java. Jeśli używasz programu Maven, współrzędne programu Maven są udostępniane w każdej bibliotece.
   - [Klient HTTP serwera Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Podstawowa wersja HTTP serwera Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Biblioteka JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Dodaj poniższe instrukcje `import` do pliku, który zawiera publiczną klasę `Main` dla projektu.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Zastąp klasę publiczną `Main` poniższym kodem, a następnie w odpowiednich miejscach wprowadź następujące zmiany kodu:
   1. Zastąp wartość `subscriptionKey` kluczem subskrypcji.
   1. W razie potrzeby zastąp wartość `uriBase` adresem URL punktu końcowego dla metody [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) w regionie świadczenia usługi Azure, z którego uzyskano klucze subskrypcji.
   1. Opcjonalnie zastąp wartość `imageToAnalyze` adresem URL innego obrazu, który chcesz analizować.
1. Zapisz, a następnie skompiluj projekt języka Java.
1. Jeśli używasz środowiska IDE, uruchom klasę `Main`. W przeciwnym razie otwórz okno wiersza polecenia, a następnie użyj polecenia `java`, aby uruchomić skompilowaną klasę. Na przykład `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa aplikacja analizuje i wyświetla pomyślną odpowiedź w oknie konsoli, podobnie jak w poniższym przykładzie:

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy projekt języka Java nie będzie już potrzebny, usuń go wraz ze skompilowaną klasą i zaimportowanymi bibliotekami.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z aplikacją w języku Java Swing, w której zastosowano interfejs API przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API Java Tutorial (Samouczek dla języka JavaScript dotyczący interfejsu API przetwarzania obrazów)](../Tutorials/java-tutorial.md)
