---
title: 'Szybki start: tworzenie wizualnego zapytania wyszukiwania, Node.js — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Sposób przekazywania obrazu do interfejsu API wyszukiwania wizualnego Bing i uzyskiwanie szczegółowych informacji zwrotnych o obrazie.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 553d068d70f7e722f3c8e4de3978f3583b941963
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442536"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-javascript"></a>Szybki start: pierwsze zapytanie wyszukiwania wizualnego Bing w języku JavaScript

Interfejs API wyszukiwania wizualnego Bing zwraca informacje o udostępnionym obrazie. Obraz można udostępnić przy użyciu adresu URL obrazu, tokenu szczegółowych informacji lub przez przekazanie obrazu. Aby uzyskać informacje o tych opcjach, zobacz [Czym jest interfejs API wyszukiwania wizualnego Bing?](../overview.md) W tym artykule opisano przekazywanie obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, w których po zrobieniu zdjęcia znanego charakterystycznego elementu krajobrazu zwracane są informacje na jego temat. Szczegółowe informacje mogą na przykład zawierać ciekawostki na temat charakterystycznego elementu krajobrazu. 

W przypadku przekazywania lokalnego obrazu w treści żądania POST konieczne jest podanie pokazanych poniżej danych formularza. Dane formularza muszą zawierać nagłówek Content-Disposition. Jego parametr `name` musi mieć wartość "image", a parametr `filename` może być ustawiony na dowolny ciąg. Zawartością formularza jest plik binarny obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

W tym artykule zawarto prostą aplikację konsolową, która wysyła żądanie interfejsu API wyszukiwania wizualnego Bing i wyświetla wyniki wyszukiwania w formacie JSON. Ta aplikacja została napisana w języku JavaScript, a interfejs API jest usługą internetową zgodną z wzorcem REST i każdym językiem programowania, który może wysyłać żądania HTTP i analizować dane JSON. 

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki start wymaga rozpoczęcia subskrypcji w warstwie cenowej S9, jak pokazano w temacie [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Aby rozpocząć subskrypcję w witrynie Azure Portal:
1. Wprowadź tekst „BingSearchV7” w polu tekstowym w górnej części witryny Azure Portal, w którym wyświetlany jest komunikat `Search resources, services, and docs`.  
2. W witrynie Marketplace z listy rozwijanej wybierz pozycję `Bing Search v7`.
3. Wprowadź wartość `Name` dla nowego zasobu.
4. Wybierz subskrypcję `Pay-As-You-Go`.
5. Wybierz warstwę cenową `S9`.
6. Kliknij pozycję `Enable`, aby rozpocząć subskrypcję.

Aby uruchomić ten kod, potrzebne jest środowisko [Node.js 6](https://nodejs.org/en/download/).

## <a name="running-the-application"></a>Uruchamianie aplikacji

Poniżej przedstawiono sposób wysyłania komunikatu przy użyciu żądania FormData na platformie Node.js.

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz folder dla projektu (lub użyj swojego ulubionego środowiska IDE lub edytora).
2. W wierszu polecenia lub terminalu przejdź do właśnie utworzonego folderu.
3. Zainstaluj moduły żądania:  
  ```  
  npm install request  
  ```  
3. Zainstaluj moduły form-data:  
  ```  
  npm install form-data  
  ```  
4. Utwórz plik o nazwie GetVisualInsights.js i dodaj do niego następujący kod.
5. Zastąp wartość elementu `subscriptionKey` kluczem subskrypcji.
6. Zastąp wartość elementu `imagePath` ścieżką obrazu do przekazania.
7. Uruchom program.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>Następne kroki

[Uzyskiwanie szczegółowych informacji o obrazie przy użyciu tokenu szczegółowych informacji](../use-insights-token.md)  
[Samouczek dotyczący przekazywania obrazów na potrzeby wyszukiwania wizualnego Bing](../tutorial-visual-search-image-upload.md)
[Samouczek dotyczący aplikacji jednostronicowej wyszukiwania wizualnego Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Omówienie wyszukiwania wizualnego Bing](../overview.md)  
[Wypróbuj!](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz dostępu do bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Dokumentacja interfejsu API wyszukiwania wizualnego Bing](https://aka.ms/bingvisualsearchreferencedoc)
