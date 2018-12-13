---
title: Dodaj znacznik HTML do usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać znacznik HTML do mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 21689cf7fe3df7ca7e362ba49824fadd5daaa3ea
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888778"
---
# <a name="add-html-markers-to-the-map"></a>Dodaj znaczniki HTML do mapy

W tym artykule przedstawiono sposób dodawania niestandardowego kodu HTML, takich jak plik obrazu do mapy jako znacznik HTML.

> [!NOTE]
> Znaczniki HTML nie Podłączaj się do źródeł danych. Zamiast tego pozycję informacje są dodawane bezpośrednio do znacznika i znacznika jest dodawana do mapy `markers` właściwość, która jest [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> W przeciwieństwie do większości warstw w kontrolki mapy platformy Azure w sieci Web, które używają WebGL renderowanie znaczniki HTML na użytek tradycyjnych elementów DOM renderowania. W efekcie więcej znaczników HTML dodane stroną więcej elementów modelu DOM, istnieją. Wydajność może się zmniejszyć po dodaniu kilku znaczników hundered HTML. Dla większych zestawów danych należy wziąć pod uwagę klastrowanie danych albo za pomocą warstwy symboli lub bąbelka.

## <a name="add-an-html-marker"></a>Dodaj znacznik HTML

Klasa HtmlMarker ma domyślnego stylu. Możesz dostosować znacznika przez ustawienie opcji koloru i tekstu znacznika. Domyślny styl klasy HtmlMarker jest szablon SVG, który ma kolor i tekst symbolu zastępczego. Ustaw właściwości koloru i tekst w opcjach HtmlMarker szybkiego dostosowywania. 

<br/>

<iframe height='500' scrolling='no' title='Dodaj znacznik HTML do mapy' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Dodaj znacznik HTML do mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Dodaje drugi blok kodu [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) na mapie za pomocą [znaczniki](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) właściwość [mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) klasy. HtmlMarker zostanie dodany do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, czy jest wyświetlana po mapy ładuje pełni.

## <a name="create-svg-templated-html-marker"></a>Utwórz oparte na szablonach znacznik HTML SVG

Wartość domyślna `htmlContent` Html znacznika jest szablonem SVG z folderami miejscu `{color}` i `{text}` w nim. Można tworzyć niestandardowe ciągi SVG i Dodaj te symbole zastępcze w tej samej do użytkownika SVG w taki sposób, że ustawienie `color` i `text` opcje znacznika zaktualizować te symbole zastępcze w swojej SVG.

<br/>

<iframe height='500' scrolling='no' title='Znacznik HTML przy użyciu szablonu niestandardowego SVG' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>znaczników HTML za pomocą szablonu niestandardowej SVG</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Dodaj arkusz CSS styl znacznika HTML

Jedną z zalet znaczniki HTML zakłada, że wiele wspaniałych dostosowania, które można osiągnąć przy użyciu CSS. W tym przykładzie zawartość HtmlMarker składa się z HTML i CSS, Utwórz animowany numer pin, który rozwija się w miejscu i impulsów.

<br/>

<iframe height='500' scrolling='no' title='Źródło danych w formacie HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="draggable-html-markers"></a>Przeciąganego znaczniki HTML

Niniejszy przykład pokazuje sposób wprowadzania przeciąganego znaczników HTML. Obsługa znaczników HTML `drag`, `dragstart` i `dragend` zdarzenia.

<br/>

<iframe height='500' scrolling='no' title='Przeciąganego znacznik HTML' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>przeciąganego znacznik HTML</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Dodawanie zdarzeń myszy do znaczników HTML

Te przykłady pokazują, jak dodać zdarzeń myszy do znaczników HTML, dodając standardowych zdarzeń języka JavaScript w zawartości HTML znacznika. 

Jeśli `htmlContent` znacznika jest elementu modelu DOM (div, img...), można dodać zdarzenia bezpośrednio do nich. 

<br/>

<iframe height='500' scrolling='no' title='Dodawanie zdarzeń myszy do znaczników HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Dodawanie zdarzeń myszy do znaczników HTML</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Jeśli `htmlContent` to ciąg (tj.) jak wartość domyślna), można utworzyć elementu modelu DOM i dodaj ciąg jako wewnętrzny kod HTML do niej, a następnie dodaj DOM element jako htmlContent. Jeśli `htmlContent` jest ciągiem oparte na szablonach, konieczne będzie Znajdź/Zamień `{text}` i `{color}` wartości w ciągu jako pierwsze.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie zdarzeń myszy do domyślnego znacznika HTML' src='//codepen.io/azuremaps/embed/ZmZLBa/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZmZLBa/'>Dodawanie zdarzenia myszy domyślne znacznik HTML</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkowych](./map-add-bubble-layer.md)