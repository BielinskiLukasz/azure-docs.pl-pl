---
title: " Tworzenie aplikacji sieci Web jednej strony — wyszukiwania wizualnego Bing"
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zintegrować API wyszukiwania wizualnego Bing jednostronicowa aplikacja sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: aahi
ms.openlocfilehash: 084aad5540a2bd56d98e343639a45c16f786e599
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496560"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Tworzenie aplikacji internetowej z jednej strony wyszukiwania wizualnego

Interfejs API wyszukiwania wizualnego Bing zwraca szczegółowe informacje o obrazie. Możesz przekazać obraz lub podaj adres URL do jednego. Szczegółowe informacje będą podobnych obrazach, źródła zakupów, stron sieci Web, który zawiera obraz i nie tylko. Szczegółowe informacje zwrócone przez interfejs API wyszukiwania wizualnego Bing są podobne do takich, które jest wyświetlane na Bing.com/images.

W tym samouczku wyjaśniono, jak rozszerzyć aplikacji jednej strony sieci web dla interfejsu API wyszukiwania obrazów Bing. Aby wyświetlić ten samouczek lub pobrać kodu źródłowego użytego w tym miejscu, zobacz [samouczka: Tworzenie aplikacji jednostronicowej dla interfejsu API wyszukiwania obrazów Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Pełny kod źródłowy dla tej aplikacji (po rozszerzeniu go do korzystania z interfejsu API wyszukiwania wizualnego Bing), jest dostępna w [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Wywołaj interfejs API wyszukiwania wizualnego Bing i obsługiwać odpowiedzi

Edytuj samouczek wyszukiwania obrazów Bing i Dodaj następujący kod na końcu `<script>` — element (i przed zamknięciem `</script>` tagu). Poniższy kod obsługuje odpowiedzi z interfejsu API wyszukiwania wizualnego, wykonuje iteracje przez wyniki i wyświetla je:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

Poniższy kod wysyła żądanie wyszukiwania do interfejsu API przy użyciu odbiornik zdarzeń do wywołania `handleVisualSearchResponse()`:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Przechwytywanie tokenu szczegółowych informacji

Dodaj następujący kod do `searchItemsRenderer` obiektu. Ten kod dodaje link **znajdź podobne**, który po kliknięciu wywołuje funkcję `bingVisualSearch`. Funkcja otrzymuje `imageInsightsToken` jako argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Wyświetlenie podobnych obrazów

Dodaj następujący kod HTML w wierszu 601. Ten kod znaczników dodaje element, aby wyświetlić wyniki wywołań interfejsu API wyszukiwania wizualnego Bing:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Po umieszczeniu na miejscu całego nowego kodu JavaScript i wszystkich elementów HTML wyniki wyszukiwania są wyświetlane z linkiem **znajdź podobne**. Kliknij ten link, aby wypełnić sekcję **Podobne** obrazami podobnymi do obrazu wybranego przez Ciebie. W celu wyświetlenia tych obrazów może być konieczne rozwinięcie sekcji **Podobne**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Przytnij obraz za pomocą usługi Bing wyszukiwania zestawu SDK programu Visual dlaC#](tutorial-visual-search-crop-area-results.md)
