---
title: Tworzenie źródła danych dla mapy | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak utworzyć źródło danych i dodać je do mapy za pomocą Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190227"
---
# <a name="create-a-data-source"></a>Tworzenie źródła danych

Azure Maps Web SDK przechowuje dane w źródłach danych. Użycie źródeł danych optymalizuje operacje na danych na potrzeby wykonywania zapytań i renderowania. Obecnie istnieją dwa typy źródeł danych:

**Źródło danych GEOJSON**

Źródło danych GEOJSON służy do ładowania i przechowywania danych lokalnie przy użyciu klasy `DataSource`. Dane GEOJSON można utworzyć ręcznie lub utworzyć przy użyciu klas pomocnika w przestrzeni nazw [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . Klasa `DataSource` udostępnia funkcje do importowania lokalnych lub zdalnych plików GeoJSON. Pliki zdalnego GEOJSON muszą być hostowane w punkcie końcowym z obsługą mechanizmu CORs. Klasa `DataSource` zapewnia funkcjonalność dla danych punktu klastrowania. I można łatwo dodawać, usuwać i aktualizować dane przy użyciu klasy `DataSource`.


> [!TIP]
> Pozwala powiedzieć, że chcesz zastąpić wszystkie dane w `DataSource`. W przypadku wykonywania wywołań do `clear` następnie `add` funkcje, mapa może być ponownie renderowana, co może spowodować nieco opóźnienia. Zamiast tego należy użyć funkcji `setShapes`, która spowoduje usunięcie i zastąpienie wszystkich danych w źródle danych i wyzwolenie tylko jednego ponownego renderowania mapy.

**Źródło kafelków wektora**

Źródło kafelka wektorowego opisuje, jak uzyskać dostęp do warstwy kafelków wektorowych. Użyj klasy [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) , aby utworzyć wystąpienie źródła kafelka Vector. Warstwy kafelków wektorowych są podobne do warstw kafelków, ale nie są one takie same. Warstwa kafelków jest obrazem rastrowym. Warstwy kafelków wektorowych są skompresowanym plikiem w formacie PBF. Ten skompresowany plik zawiera dane mapy wektorowej i co najmniej jedną warstwę. Plik może być renderowany i ustalany na podstawie stylu każdej warstwy. Dane w kafelku wektorowym zawierają funkcje geograficzne w postaci punktów, linii i wielokątów. Istnieje kilka zalet używania warstw kafelków wektorowych zamiast warstw kafelków rastrowych:

 - Rozmiar pliku kafelka wektora jest zwykle znacznie mniejszy niż odpowiednik kafelka rastrowego. W związku z tym jest używana mniejsza przepustowość. Oznacza to małe opóźnienia, szybsze mapowanie i lepszy komfort pracy użytkowników.
 - Ponieważ kafelki wektorowe są renderowane na kliencie, dostosowują się do rozdzielczości urządzenia, na którym są one wyświetlane. W związku z tym renderowane mapy pojawiają się dokładniej i są bardziej zdefiniowane przy użyciu funkcji Crystal Clear labels.
 - Zmiana stylu danych w mapach wektorów nie wymaga ponownego pobierania danych, ponieważ nowy styl można zastosować na kliencie. Natomiast zmiana stylu warstwy kafelków rastrowych zazwyczaj wymaga załadowania kafelków z serwera, a następnie zastosowania nowego stylu.
 - Ponieważ dane są dostarczane w formie wektorowej, do przygotowania danych nie jest wymagane przetwarzanie po stronie serwera. W związku z tym nowsze dane można szybciej udostępnić.

Wszystkie warstwy używające źródła wektora muszą określać wartość `sourceLayer`.

Po utworzeniu źródła danych można dodać do mapy za pomocą właściwości `map.sources`, która jest obiektem [sourcemanager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Poniższy kod przedstawia sposób tworzenia `DataSource` i dodawania go do mapy.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps jest zgodna ze [specyfikacją kafelka wektora MapBox](https://github.com/mapbox/vector-tile-spec), otwartym standardem.

## <a name="connecting-a-data-source-to-a-layer"></a>Łączenie źródła danych z warstwą

Dane są renderowane na mapie przy użyciu warstw renderowania. Pojedynczemu źródle danych może być przywoływana jedna lub więcej warstw renderowania. Następujące warstwy renderowania wymagają źródła danych:

- [Bąbelkowa warstwa](map-add-bubble-layer.md) renderuje dane punktowe jako skalowane kółka na mapie.
- [Warstwa symboli](map-add-pin.md) — renderuje dane punktu jako ikony lub tekst.
- [Warstwa mapy cieplnej](map-add-heat-map-layer.md) — renderuje dane punktu jako mapę cieplną gęstości.
- [Warstwa linii](map-add-shape.md) — Renderuj linię i Renderuj konspekt wielokątów. 
- [Warstwa wielokątów](map-add-shape.md) — wypełnia obszar wielokąta przy użyciu pełnego koloru lub wzorca obrazu.

Poniższy kod przedstawia sposób tworzenia źródła danych, dodawania go do mapy i łączenia go z warstwą bąbelkową. A następnie zaimportuj dane punktu GEOJSON z lokalizacji zdalnej do źródła danych. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Istnieją dodatkowe warstwy renderowania, które nie łączą się z tymi źródłami danych, ale bezpośrednio ładują dane do renderowania. 

- [Warstwa obrazu](map-add-image-layer.md) — nakłada pojedynczy obraz na mapę i wiąże jego rogi z zestawem określonych współrzędnych.
- [Warstwa kafelków](map-add-tile-layer.md) nakłada warstwę kafelków rastrowych na podstawie mapy.

## <a name="one-data-source-with-multiple-layers"></a>Jedno źródło danych z wieloma warstwami

Wiele warstw można podłączyć do pojedynczego źródła danych. Istnieje wiele różnych scenariuszy, w których ta opcja jest przydatna. Rozważmy na przykład scenariusz, w którym użytkownik rysuje wielokąt. Powinienmy renderować i wypełnić obszar Wielokąt, gdy użytkownik doda punkty do mapy. Dodanie linii z stylem do konturu Wielokąt sprawia, że krawędzie wielokąta są łatwiej widoczne podczas rysowania przez użytkownika. Aby wygodnie edytować poszczególne położenia w wielokąta, możemy dodać uchwyt, taki jak numer PIN lub znacznik, nad każdą pozycją.

![Mapa pokazująca wiele warstw renderowania danych z jednego źródła danych](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

W większości platform mapowania potrzebny jest obiekt wielokątny, obiekt linii i numer PIN dla każdej pozycji w wielokąta. Gdy Wielokąt jest modyfikowany, trzeba ręcznie zaktualizować linię i numery PIN, co może szybko stać się skomplikowany.

W przypadku Azure Maps, wystarczy, że jest to pojedynczy Wielokąt w źródle danych, jak pokazano w poniższym kodzie.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie okna podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę wielokątów](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie mapy cieplnej](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)