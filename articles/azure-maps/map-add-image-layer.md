---
title: Dodawanie warstwy obrazu do mapy | Mapy Microsoft Azure
description: Ten artykuł zawiera informacje na temat nałożenia obrazu na mapie przy użyciu zestawu Microsoft Azure mapy sieci Web.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfdf7dfd4c16f70065e338f8983d2124d3f6f0ef
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933215"
---
# <a name="add-an-image-layer-to-a-map"></a>Dodawanie warstwy obrazu do mapy

W tym artykule pokazano, jak można nałożyć obraz na stały zestaw współrzędnych na mapie. Poniżej przedstawiono kilka przykładów typu obrazów, które często są nałożone na mapy:

* Obrazy przechwycone z dronom
* Kompilowanie Floorplans
* Historyczne lub inne wyspecjalizowane obrazy map
* Plany dotyczące lokacji zadań
* Obrazy radaru pogody

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) to prosty sposób nakładki obrazu na mapie. Należy pamiętać, że przeglądarki mogą mieć problemy z ładowaniem dużego obrazu. W takim przypadku Rozważ przerwanie obrazu na kafelkach i załadowanie ich do mapy jako [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Warstwa obrazu obsługuje następujące formaty obrazów:

- JPEG
- PNG
- BMP
- GIF (bez animacji)

## <a name="add-an-image-layer"></a>Dodawanie warstwy obrazów

Poniższy kod nakłada obraz [mapy Newark, nowej Jersey, z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapie. [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) jest tworzony przez przekazanie adresu URL do obrazu i współrzędne dla czterech rogów w formacie `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Oto kompletny przykładowy kod dla poprzedniego kodu.

<br/>

<iframe height='500' scrolling='no' title='Prosta warstwa obrazu' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/eQodRo/'>prostą warstwą obrazu</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importowanie pliku KML jako nakładki gruntowej

Ten następny przykład pokazuje, jak nałożyć KMLe informacje o nakładki naziemnej jako warstwę obrazu na mapie. Nakładki naziemne KML zapewniają współrzędne północne, Południowe, wschodnie i zachodnie oraz obrót w prawo, natomiast warstwa obrazu oczekuje współrzędnych dla każdego rogu obrazu. Nakładka KML w tym przykładzie jest Chartres Cathedral i pochodzi od [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Poniższy kod używa funkcji static `getCoordinatesFromEdges` klasy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) . Oblicza cztery narożniki obrazu z obszaru północ, południe, wschód i zachód oraz informacje o obrocie z nakładki naziemnej KML.

<br/>

<iframe height='500' scrolling='no' title='KML nakładki jako warstwa obrazu' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z KML piórem, <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>jak warstwa obrazu</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Dostosowywanie warstwy obrazu

Warstwa obrazu ma wiele opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy obrazu' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opcje warstwy obrazu</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodaj warstwę kafelków](./map-add-tile-layer.md)