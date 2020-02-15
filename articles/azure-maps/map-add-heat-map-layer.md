---
title: Dodawanie warstwy mapy cieplnej do mapy | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać warstwę mapy cieplnej do mapy za pomocą Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4a853871ef5f66881235e5a6ffec0886b81f5a92
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208543"
---
# <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Mapy cieplne, znane także jako mapy gęstości punktów, są typem wizualizacji danych. Są one używane do reprezentowania gęstości danych przy użyciu różnych kolorów i pokazujące dane "gorąca" na mapie. Mapy cieplne to doskonały sposób renderowania zestawów danych z dużą liczbą punktów. 

Renderowanie dziesiątek tysięcy punktów, ponieważ symbole mogą obejmować większość obszaru mapy. W takim przypadku najkorzystniej powstaje wiele symboli. Utrudnia to lepsze zrozumienie danych. Wizualizacja tego samego zestawu danych jako mapy cieplnej pozwala jednak łatwo sprawdzić gęstość i względną gęstość każdego punktu danych.

W wielu różnych scenariuszach można używać map cieplnych, takich jak:

- **Dane temperatury**: zawiera przybliżenia między dwoma punktami danych.
- **Dane dla czujników szumu**: pokazuje nie tylko intensywność hałasu, gdzie czujnik jest, ale może także zapewnić wgląd w rozproszenie na odległość. Poziom szumu w jednej lokacji może być niewysoki. Jeśli obszar pokrycia szumów z wielu czujników nakłada się na siebie, istnieje możliwość, że ten obszar nakładający się może mieć wyższy poziom szumu. W związku z tym, nakładający się obszar będzie widoczny na mapie cieplnej.
- **Śledzenie GPS**: zawiera szybkość jako mapę ważonej wysokości, w której intensywność każdego punktu danych jest oparta na szybkości. Na przykład ta funkcja zapewnia sposób, aby zobaczyć, gdzie nastąpiło przyspieszenie pojazdu.

> [!TIP]
> Warstwy mapy cieplnej domyślnie renderują współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę tak, aby była renderowana tylko funkcja geometrii punktu, ustaw właściwość `filter` warstwy na `['==', ['geometry-type'], 'Point']`. Jeśli chcesz również uwzględnić funkcje systemu MultiPoint, ustaw właściwość `filter` warstwy na `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Aby renderować źródło danych punktów jako mapę cieplną, Przekaż źródło danych do wystąpienia klasy `HeatMapLayer` i Dodaj je do mapy.

W poniższym kodzie każdy punkt ciepła ma promień 10 pikseli na wszystkich poziomach powiększenia. Aby zapewnić lepsze środowisko użytkownika, Mapa cieplna znajduje się poniżej warstwy etykiet. Etykiety pozostają wyraźnie widoczne. Dane w tym przykładzie pochodzą z [programu agencji usgse zagrożenia ziemią](https://earthquake.usgs.gov/). Jest to przeznaczone dla dużych ziemi, które wystąpiły w ciągu ostatnich 30 dni.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Oto kompletny przykładowy kod dla poprzedniego kodu.

<br/>

<iframe height='500' scrolling='no' title='Prosta warstwa mapy cieplnej' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>warstwą prostej mapy ciepła</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Dostosowywanie warstwy mapy cieplnej

Poprzedni przykład dostosowany do mapy cieplnej przez ustawienie opcji promień i nieprzezroczystość. Warstwa mapy cieplnej oferuje kilka opcji dostosowywania, takich jak:

* `radius`: Określa promień w pikselach, w którym ma być renderowany każdy punkt danych. Promień można ustawić jako liczbę stałą lub jako wyrażenie. Za pomocą wyrażenia można skalować promień na podstawie poziomu powiększenia i reprezentować spójny obszar przestrzenny mapy (na przykład promień 5-milowej).
* `color`: określa sposób kolorowania mapy cieplnej. Gradient koloru jest popularną funkcją map ciepła. Efekt można osiągnąć za pomocą wyrażenia `interpolate`. Możesz również użyć wyrażenia `step`, aby kolorować mapę cieplną, dzieląc gęstość na wizualizację na zakresy podobne do mapy w stylu konturu lub wykresu radarowego. Te palety kolorów definiują kolory z minimum do wartości maksymalnej gęstości. 

  Należy określić wartości koloru dla map cieplnych jako wyrażenie dla wartości `heatmap-density`. Kolor obszaru, w którym nie ma żadnych danych, jest zdefiniowany przy indeksie 0 wyrażenia "Interpolacja" lub domyślnym kolorem wyrażenia "". Tej wartości można użyć do zdefiniowania koloru tła. Często ta wartość jest ustawiona na przezroczysty lub półprzezroczysty kolor przezroczysty. 
   
  Oto przykłady wyrażeń koloru:

  | Wyrażenie koloru interpolacji | Wyrażenie koloru z możliwością | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Interpolacja",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["liniowy"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapę cieplną-gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "przezroczyste",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "purpurowy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Step",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapę cieplną-gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"przezroczyste",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "Granat",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zielony",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "żółty",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "Red"<br/>\] |   

- `opacity`: określa sposób nieprzezroczystości lub przezroczystości warstwy mapy cieplnej.
- `intensity`: stosuje mnożnik do wagi każdego punktu danych w celu zwiększenia całkowitego intensywności mapę cieplną. Powoduje to różnicę wagi punktów danych, ułatwiając wizualizację.
- `weight`: Domyślnie wszystkie punkty danych mają wagę 1 i są ważone w równym stopniu. Opcja wagi pełni rolę mnożnika i można ją ustawić jako liczbę lub wyrażenie. Jeśli liczba jest ustawiona jako waga, to równoważność umieszczania każdego punktu danych na mapie dwa razy. Na przykład, jeśli waga wynosi 2, to gęstość podwaja się. Ustawienie opcji wagi na liczbę renderuje mapę cieplną w podobny sposób, aby użyć opcji intensywność. 

  Jeśli jednak używasz wyrażenia, waga każdego punktu danych może opierać się na właściwościach poszczególnych punktów danych. Załóżmy na przykład, że każdy punkt danych reprezentuje trzęsienie ziemi. Wartość wielkości jest ważną metryką dla każdego punktu danych trzęsienia. Zdarza się cały czas, ale większość z nich ma niską wartość i nie są zauważalne. Użyj wartości wielkości w wyrażeniu, aby przypisać wagę do każdego punktu danych. Przy użyciu wartości wielkości do przypisywania wagi, uzyskuje się lepszą reprezentację znaczenia ziemi w ramach mapy cieplnej.
- `source` i `source-layer`: umożliwia zaktualizowanie źródła danych.

Oto narzędzie do testowania różnych opcji warstwy mapy cieplnej.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy cieplnej' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opcje warstwy mapy ciepła</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Spójna Mapa cieplna z powiększaniem

Domyślnie promienie punktów danych renderowane w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. W miarę powiększania mapy dane zagregowane i warstwy mapy cieplnej wyglądają inaczej. 

Użyj wyrażenia `zoom`, aby skalować promień dla każdego poziomu powiększenia, w taki sposób, że każdy punkt danych obejmuje ten sam obszar fizyczny mapy. To wyrażenie sprawia, że warstwa mapy cieplnej jest bardziej statyczna i spójna. Każdy poziom powiększenia mapy ma dwa piksele w pionie i poziomie jako poprzedni poziom powiększenia. 

Skalowanie promienia tak, aby podwajał się przy każdym poziomie powiększenia tworzy mapę cieplną, która wygląda spójnie na wszystkich poziomach powiększenia. Aby zastosować to skalowanie, należy użyć `zoom` z wyrażeniem `exponential interpolation` Base 2, jak pokazano w poniższym przykładzie. Powiększ mapę, aby zobaczyć, jak mapa cieplna jest skalowana z poziomem powiększenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Spójna Mapa cieplna z powiększaniem" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>spójną mapę termiczną o powiększeniu powiększenia</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Po włączeniu klastrowania w źródle danych punkty, które są blisko siebie, są pogrupowane jako punkt klastrowany. Można użyć liczby punktów każdego klastra jako wyrażenia wagi dla mapy cieplnej. Może to znacznie zmniejszyć liczbę punktów, które mają być renderowane. Liczba punktów klastra jest przechowywana we właściwości `point_count` funkcji Point: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Jeśli promień klastrowania ma tylko kilka pikseli, w renderowaniu będzie niewielka różnica wizualna. Większa liczba grup usługi RADIUS zwiększa liczbę punktów w każdym klastrze i poprawia wydajność mapę cieplną.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do usługi Maps, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)
