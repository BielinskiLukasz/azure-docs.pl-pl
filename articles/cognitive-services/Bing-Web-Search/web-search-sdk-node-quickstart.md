---
title: 'Szybki start: używanie zestawu SDK wyszukiwania w sieci Web Bing dla platformy Node.js'
titleSuffix: Azure Cognitive Services
description: Zestaw SDK wyszukiwania w sieci Web Bing ułatwia integrowanie wyszukiwania w sieci Web Bing w aplikacji Node.js. Z tego przewodnika Szybki start dowiesz się, jak utworzyć wystąpienie klienta, wysłać żądanie i wyświetlić odpowiedź.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: b27c11a69c7d16f38c2448d380b611940078c501
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121927"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Szybki start: używanie zestawu SDK wyszukiwania w sieci Web Bing dla platformy Node.js

Zestaw SDK wyszukiwania w sieci Web Bing ułatwia integrowanie wyszukiwania w sieci Web Bing w aplikacji Node.js. Z tego przewodnika Szybki start dowiesz się, jak utworzyć wystąpienie klienta, wysłać żądanie i wyświetlić odpowiedź.

Chcesz zobaczyć kod teraz? [Przykłady zastosowania zestawu SDK wyszukiwania w sieci Web Bing dla platformy Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) są dostępne w witrynie GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Oto kilka rzeczy, które są potrzebne przed rozpoczęciem tego przewodnika Szybki start:

* [Środowisko Node.js 6](https://nodejs.org/en/download/) lub nowsze
* Klucz subskrypcji  

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Zacznijmy od skonfigurowania środowiska deweloperskiego dla projektu Node.js.

1. Utwórz nowy katalog dla projektu:

    ```console
    mkdir YOUR_PROJECT
    ```

2. Utwórz nowy plik pakietu:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

3. Teraz zainstalujmy kilka modułów platformy Azure i dodajmy je do pliku `package.json`:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Tworzenie projektu i deklarowanie wymaganych modułów

W tym samym katalogu, w którym znajduje się plik `package.json`, utwórz nowy projekt Node.js w ulubionym środowisku IDE lub edytorze. Na przykład: `sample.js`.

Następnie skopiuj ten kod do projektu. Ładuje on moduły zainstalowane w poprzedniej sekcji.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Tworzenie wystąpienia klienta

Ten kod tworzy wystąpienie klienta i używa modułu `azure-cognitiveservices-websearch`. Przed kontynuowaniem upewnij się, że został wprowadzony prawidłowy klucz subskrypcji dla konta platformy Azure.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Wysyłanie żądania i wyświetlanie wyników

Za pomocą klienta wyślij zapytanie wyszukiwania do wyszukiwania w sieci Web Bing. Jeśli odpowiedź zawiera wyniki dla jakichkolwiek elementów w tablicy `properties`, w konsoli zostanie wyświetlona wartość `result.value`.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Uruchamianie programu

Ostatnim krokiem jest uruchomienie programu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pamiętaj, aby po zakończeniu pracy z tym projektem usunąć klucz subskrypcji z kodu programu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przykłady zastosowania zestawu SDK dla platformy Node.js usług Cognitive Services](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja zestawu Azure Node SDK](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-websearch/)
