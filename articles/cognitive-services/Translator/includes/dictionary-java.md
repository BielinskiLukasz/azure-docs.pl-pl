---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 63c524880a47c6e519649bd871a6216d6faeefce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69907117"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Inicjowanie projektu za pomocą narzędzia Gradle

Zaczniemy od utworzenia katalogu roboczego dla tego projektu. W wierszu polecenia (lub terminalu) uruchom następujące polecenie:

```console
mkdir alt-translation-sample
cd alt-translation-sample
```

Następnie zainicjujesz projekt narzędzia Gradle. To polecenie spowoduje utworzenie niezbędnych plików kompilacji dla narzędzia Gradle, a w szczególności najważniejszego pliku `build.gradle.kts`, który jest używany czasie wykonywania do utworzenia i skonfigurowania aplikacji. Uruchom następujące polecenie w katalogu roboczym:

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

## <a name="configure-the-build-file"></a>Konfigurowanie pliku kompilacji

Znajdź plik `build.gradle.kts` i otwórz go za pomocą ulubionego środowiska IDE lub edytora tekstów. Następnie skopiuj go do tej konfiguracji kompilacji:

```
plugins {
    java
    application
}
application {
    mainClassName = "AltTranslation"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Weź pod uwagę, że ten przykład zależy od bibliotek OkHttp (obsługa żądań HTTP) i Gson (obsługa i analizowanie kodu JSON). Jeśli chcesz dowiedzieć się więcej na temat konfiguracji kompilacji, zobacz [Creating New Gradle Builds (Tworzenie nowych kompilacji narzędzia Gradle)](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Tworzenie pliku języka Java

Utworzymy folder dla przykładowej aplikacji. W katalogu roboczym uruchom polecenie:

```console
mkdir -p src\main\java
```

Następnie utwórz w tym folderze plik o nazwie `AltTranslation.java`.

## <a name="import-required-libraries"></a>Importowanie wymaganych bibliotek

Otwórz plik `AltTranslation.java` i dodaj następujące instrukcje import:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definiowanie zmiennych

Najpierw należy utworzyć klasę publiczną dla projektu:

```java
public class AltTranslation {
  // All project code goes here...
}
```

Dodaj następujące wiersze do klasy `AltTranslation`. Po pierwsze klucz subskrypcji i punkt końcowy są odczytywane ze zmiennych środowiskowych. Następnie zauważysz, że wraz `api-version`z , dwa dodatkowe parametry `url`zostały dołączone do . Te parametry są używane do ustawiania danych wejściowych i wyjściowych tłumaczenia. W tym przykładzie jest to język angielski (`en`) i hiszpański (`es`).

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/dictionary/lookup?api-version=3.0&from=en&to=es";
```

Jeśli korzystasz z subskrypcji wielu usług usług Cognitive Services, należy również uwzględnić `Ocp-Apim-Subscription-Region` parametry żądania. [Dowiedz się więcej o uwierzytelnieniu za pomocą subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Tworzenie klienta i budowanie żądania

Dodaj następujący wiersz do klasy `AltTranslation` w celu utworzenia wystąpienia klasy `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Następnie utworzymy żądanie POST. Możesz dowolnie zmienić tekst do tłumaczenia.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Pineapples\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Tworzenie funkcji analizowania odpowiedzi

Ta prosta funkcja analizuje i formatuje odpowiedź JSON z usługi tłumaczenia tekstu w usłudze Translator.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wykonanie żądania i pobranie odpowiedzi. Dodaj następujące wiersze do projektu:

```java
public static void main(String[] args) {
    try {
        AltTranslation altTranslationRequest = new AltTranslation();
        String response = altTranslationRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalu) przejdź do katalogu głównego używanego katalogu roboczego, a następnie uruchom polecenie:

```console
gradle build
```

Po zakończeniu kompilacji uruchom:

```console
gradle run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

```json
[
  {
    "normalizedSource": "pineapples",
    "displaySource": "pineapples",
    "translations": [
      {
        "normalizedTarget": "piñas",
        "displayTarget": "piñas",
        "posTag": "NOUN",
        "confidence": 0.7016,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 5,
            "frequencyCount": 158
          },
          {
            "normalizedText": "cones",
            "displayText": "cones",
            "numExamples": 5,
            "frequencyCount": 13
          },
          {
            "normalizedText": "piña",
            "displayText": "piña",
            "numExamples": 3,
            "frequencyCount": 5
          },
          {
            "normalizedText": "ganks",
            "displayText": "ganks",
            "numExamples": 2,
            "frequencyCount": 3
          }
        ]
      },
      {
        "normalizedTarget": "ananás",
        "displayTarget": "ananás",
        "posTag": "NOUN",
        "confidence": 0.2984,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 2,
            "frequencyCount": 16
          }
        ]
      }
    ]
  }
]
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z odwołaniem do interfejsu API, aby zrozumieć wszystko, co można zrobić z interfejsem API tekstu translatora.

> [!div class="nextstepaction"]
> [Odwołanie API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
