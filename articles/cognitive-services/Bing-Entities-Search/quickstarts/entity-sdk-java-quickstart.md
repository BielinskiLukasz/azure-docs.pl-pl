---
title: 'Szybki start: Wyszukiwanie jednostek za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Java'
titlesuffix: Azure Cognitive Services
description: Użyj tego poradnika Szybki start, aby wyszukiwać jednostki za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Java
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 5911719e0277d0ac842b285aebc03369aa82621f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757838"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Szybki start: Wysyłanie żądania wyszukiwania za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Java

Użyj tego poradnika Szybki start, aby zacząć wyszukiwać jednostki za pomocą zestawu SDK wyszukiwania jednostek Bing dla języka Java. Mimo że wyszukiwanie jednostek Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Wymagania wstępne

* Zestaw [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* Zestaw SDK wyszukiwania jednostek Bing dla języka Java

Zainstaluj zależności zestawu SDK wyszukiwania jednostek Bing przy użyciu oprogramowania Maven, Gradle lub innego systemu zarządzania zależnościami. Plik POM systemu Maven wymaga deklaracji:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Tworzenie zmiennej dla klucza subskrypcji

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Tworzenie klienta wyszukiwania

2. Zaimplementuj klienta `dominantEntityLookup`, który wymaga punktu końcowego interfejsu API i wystąpienia klasy `ServiceClientCredentials`.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Aby zaimplementować klasę `ServiceClientCredentials`, wykonaj następujące kroki:

    1. Przesłoń funkcję `applyCredentialsFilter()`, używając obiektu `OkHttpClient.Builder` jako parametru. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. W ramach funkcji `applyCredentialsFilter()` wywołaj funkcję `builder.addNetworkInterceptor()`. Utwórz nowy obiekt `Interceptor` i przesłoń jego metodę `intercept()` tak, aby przyjmowała obiekt interceptora `Chain`.

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. W funkcji `intercept` utwórz zmienne dla żądania. Użyj funkcji `Request.Builder()`, aby utworzyć żądanie. Dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key` i zwróć funkcję `chain.proceed()` w obiekcie żądania.
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```
## <a name="send-a-request-and-receive-a-response"></a>Wysyłanie żądania i odbieranie odpowiedzi

1. Utwórz nowe wystąpienie klienta wyszukiwania z kluczem subskrypcji. Użyj funkcji `client.entities().search()`, aby wysłać żądanie wyszukiwania dla zapytania wyszukiwania `satya nadella` i uzyskać odpowiedź. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

2. Jeśli zwrócono jakiekolwiek jednostki, przekonwertuj je na listę. Przeiteruj przez jednostki, a następnie wypisz dominującą jednostkę.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }

## Next steps

> [!div class="nextstepaction"]
> [Build a single-page web app](../tutorial-bing-entities-search-single-page-app.md)

* [What is the Bing Entity Search API?](../overview.md )