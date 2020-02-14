---
title: 'Samouczek: Migrowanie aplikacji internetowej z usługi Google Maps | Mapy Microsoft Azure'
description: Jak przeprowadzić migrację aplikacji internetowej z usługi Google Maps do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: b954c812bea6c2abf4376c2cee38a3789461ad01
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208747"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrowanie aplikacji internetowej z usługi Google Maps

Większość aplikacji sieci Web korzystających z usługi Google Maps korzysta z zestawu SDK języka JavaScript usługi Google Maps v3. Zestaw SDK sieci Web Azure Maps to odpowiedni zestaw SDK oparty na platformie Azure do migracji. Zestaw SDK sieci Web Azure Maps umożliwia dostosowywanie interaktywnych map przy użyciu własnej zawartości i obrazów. Aplikację można uruchomić zarówno w sieci Web, jak i aplikacji mobilnych. Kontrolka korzysta z technologii WebGL, co umożliwia renderowanie dużych zestawów danych z wysoką wydajnością. Utwórz ten zestaw SDK przy użyciu języka JavaScript lub TypeScript.

W przypadku migrowania istniejącej aplikacji sieci Web sprawdź, czy jest ona używana przez bibliotekę kontroli mapy Open Source. Przykłady biblioteki formantów typu "open source" to: cesium, ulotce i OpenLayers. Nadal można przeprowadzić migrację aplikacji, nawet jeśli korzysta ona z biblioteki kontroli mapy Open Source i nie chcesz używać Azure Maps Web SDK. W takim przypadku Połącz aplikację z usługami kafelków Azure Maps ([kafelki drogowe](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [kafelki satelitarne](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Poniższe punkty szczegółowo opisują sposób używania Azure Maps w niektórych najczęściej używanych bibliotekach kontroli mapy Open Source.

- Cesium — kontrolka mapy 3D dla sieci Web. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Dokumentacja](https://cesiumjs.org/)
- Ulotek — uproszczona kontrolka mapy 2D dla sieci Web. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Dokumentacja](https://leafletjs.com/)
- OpenLayers — kontrolka mapy 2D dla sieci Web, która obsługuje projekcje. [Przykładowy kod](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Dokumentacja](https://openlayers.org/)

## <a name="key-features-support"></a>Obsługa kluczowych funkcji

W tabeli wymieniono kluczowe funkcje interfejsu API w usłudze Google Maps v3 JavaScript SDK oraz obsługiwane funkcje interfejsu API w zestawie SDK sieci Web Azure Maps.

| Funkcja usługi Google Maps     | Azure Maps obsługa zestawu SDK sieci Web |
|-------------------------|:--------------------------:|
| Wyświetla                 | ✓                          |
| Znaczniki klastra       | ✓                          |
| Linie łamane & wielokąty    | ✓                          |
| Warstwy danych             | ✓                          |
| Nakładki uziemienia         | ✓                          |
| Mapy cieplne               | ✓                          |
| Kafelki warstw             | ✓                          |
| Warstwa KML               | ✓                          |
| Narzędzia do rysowania           | ✓                          |
| Usługa geocode        | ✓                          |
| Usługa wskazówek      | ✓                          |
| Usługa macierzy odległości | ✓                          |
| Usługa podniesienia uprawnień       | Planowany                    |

## <a name="notable-differences-in-the-web-sdks"></a>Istotne różnice w zestawach SDK sieci Web

Poniżej przedstawiono kilka najważniejszych różnic między usługą Mapy Google i Azure Maps zestawy SDK sieci Web, które mają być świadome:

- Oprócz udostępniania hostowanego punktu końcowego do uzyskiwania dostępu do zestawu Web SDK Azure Maps jest dostępny pakiet NPM. Osadź pakiet zestawu SDK sieci Web w aplikacjach. Aby uzyskać więcej informacji, zobacz tę [dokumentację](how-to-use-map-control.md). Ten pakiet zawiera również definicje języka TypeScript.
- Najpierw musisz utworzyć wystąpienie klasy map w Azure Maps. Poczekaj, aż usługi Maps `ready` lub zdarzenia `load`, zanim będzie można programowo korzystać z mapy. Ta kolejność zapewni, że wszystkie zasoby mapy zostały załadowane i są gotowe do uzyskania dostępu.
- Obie platformy używają podobnego systemu do rozmieszczania dla map podstawowych. Kafelki w usłudze Google Maps są 256 pikseli w wymiarze. jednak kafelki w Azure Maps są 512 pikseli w wymiarze. Aby uzyskać ten sam widok mapy w Azure Maps jako Google Maps, Odejmij poziom powiększenia usługi Google Maps o numer jeden w Azure Maps.
- Współrzędne w usłudze Google Maps są określane jako "Latitude, Długość geograficzna", podczas gdy Azure Maps używa "długości geograficznej". Format Azure Maps jest wyrównany do `[x, y]`standardowego, po którym następuje większość platform GIS.
- Kształty w Azure Maps Web SDK są oparte na schemacie GEOJSON. Klasy pomocników są udostępniane za pomocą [przestrzeni nazw *Atlas. Data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Jest również w [*Atlasie. Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) — Klasa. Użyj tej klasy do zawijania obiektów GEOJSON, aby ułatwić aktualizowanie i konserwowanie danych w sposób możliwy do powiązania.
- Współrzędne w Azure Maps są zdefiniowane jako obiekty położenia. Współrzędna jest określona jako tablica liczbowa w formacie `[longitude,latitude]`. Lub jest on określony przy użyciu nowego Atlas. Data. Position (Długość geograficzna, Szerokość geograficzna).
    > [!TIP]
    > Klasa Position ma statyczną metodę pomocniczą do importowania współrzędnych w formacie "szerokości geograficznej". Często można zastąpić metodę [Atlas. Data. Position. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) za pomocą metody `new google.maps.LatLng` w kodzie usługi Google Maps.
- Azure Maps oddziela style od danych. Oddzielenie danych i stylów jest wydajniejsze niż Określanie informacji o stylu dla każdego kształtu, który jest dodawany do mapy. Dane są przechowywane w źródłach danych, które są połączone z warstwami renderowania. Kod Azure Maps używa źródeł danych do renderowania danych. Takie podejście zapewnia zwiększoną korzyść w wydajności. Ponadto wiele warstw obsługuje Style oparte na danych, w których można dodać logikę biznesową do opcji stylu warstwy. Ta obsługa zmienia sposób renderowania poszczególnych kształtów wewnątrz warstwy w oparciu o właściwości zdefiniowane w kształcie.

## <a name="web-sdk-side-by-side-examples"></a>Przykłady obok siebie zestawu SDK sieci Web

Ta kolekcja zawiera przykłady kodu dla każdej platformy, a każdy przykład obejmuje typowy przypadek użycia. Ma ona na celu ułatwienie migracji aplikacji sieci Web z usługi Google Maps v3 JavaScript SDK do Azure Maps Web SDK. Przykłady kodu związane z aplikacjami sieci Web są udostępniane w języku JavaScript. Jednakże Azure Maps udostępnia również definicje języka TypeScript jako dodatkową opcję za poorednictwem [modułu npm](how-to-use-map-control.md).

### <a name="load-a-map"></a>Załaduj mapę

Oba zestawy SDK mają te same kroki w celu załadowania mapy:

- Dodaj odwołanie do zestawu SDK mapy.
- Dodaj tag `div` do treści strony, która będzie pełnić rolę symbolu zastępczego mapy.
- Utwórz funkcję języka JavaScript, która jest wywoływana po załadowaniu strony.
- Utwórz wystąpienie odpowiedniej klasy map.

**Różnice między kluczami**

- Usługi Mapy Google wymagają określenia klucza konta w odniesieniu do skryptu interfejsu API. Poświadczenia uwierzytelniania dla Azure Maps są określone jako opcje klasy map. To poświadczenie może być kluczem subskrypcji lub Azure Active Directory informacji.
- Usługi Google Maps akceptują funkcję wywołania zwrotnego w odniesieniu do skryptu interfejsu API, który jest używany do wywołania funkcji inicjowania w celu załadowania mapy. Przy Azure Maps należy użyć zdarzenia OnLoad dla strony.
- Podczas odwoływania się do `div` elementu, w którym mapa będzie renderowana, Klasa `Map` w Azure Maps tylko wymaga wartości `id`, natomiast usługa Google Maps wymaga obiektu `HTMLElement`.
- Współrzędne w Azure Maps są zdefiniowane jako obiekty położenia, które można określić jako prostą tablicę liczbową w formacie `[longitude, latitude]`.
- Poziom powiększenia w Azure Maps to poziom niższy od poziomu powiększenia w usłudze mapy Google. Rozbieżność wynika z tego, że różnica w rozmiarach systemu rozmieszczania dwóch platform.
- Azure Maps nie dodaje żadnych kontrolek nawigacji do kanwy mapy. Tak więc domyślnie mapa nie ma przycisków powiększenia i przycisków stylu mapy. Jednak dostępne są opcje kontrolne umożliwiające dodanie selektora stylu mapy, przycisków powiększenia, kontrolki kompasu lub obrotu oraz kontrolki gęstość.
- Procedura obsługi zdarzeń została dodana w Azure Maps do monitorowania zdarzenia `ready` wystąpienia mapy. To zdarzenie zostanie wyzwolone po zakończeniu ładowania przez mapę kontekstu WebGL i wszystkich wymaganych zasobów. Dodaj dowolny kod, który ma zostać uruchomiony po zakończeniu ładowania mapy, do tej procedury obsługi zdarzeń.

Poniższe podstawowe przykłady używają usługi Google Maps do załadowania mapy wyśrodkowanej przez Nowy Jork przy współrzędnych. Długość geograficzna:-73,985, Szerokość geograficzna: 40,747, a mapa jest na poziomie powiększenia 12.

**Wcześniej: Google Maps**

Wyświetlanie mapy Google w środku i powiększonej do lokalizacji.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

<center>

![proste usługi Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Po: Azure Maps**

Załaduj mapę z tym samym widokiem w Azure Maps wraz z kontrolką stylu mapy i przyciskami powiększenia.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

<center>

![proste Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Szczegółowe informacje na temat konfigurowania i używania kontrolki mapy Azure Maps w aplikacji sieci Web można znaleźć, klikając [tutaj](how-to-use-map-control.md).

> [!NOTE]
> W przeciwieństwie do usługi Google Maps Azure Maps nie wymaga początkowego centrum oraz poziomu powiększenia do załadowania mapy. Jeśli te informacje nie zostaną podane podczas ładowania mapy, usługa Azure Maps spróbuje określić miasto użytkownika. Spowoduje to wyśrodkowanie i powiększanie mapy w tym miejscu.

**Dodatkowe zasoby:**

- Azure Maps udostępnia również kontrolki nawigacji umożliwiające obracanie i pochylenia widoku mapy, jak opisano [tutaj](map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalizowanie mapy

Jeśli odbiorcy są rozproszeni w wielu krajach lub mówisz w różnych językach, lokalizacja jest ważna.

**Wcześniej: Google Maps**

Aby zlokalizować usługi Google Maps, Dodaj parametry języka i regionu.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Oto przykład mapy Google z ustawionym językiem "fr-FR".

<center>

![lokalizację usługi Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Po: Azure Maps**

Azure Maps oferuje dwa różne sposoby ustawiania języka i widoku regionalnego mapy. Pierwsza opcja polega na dodaniu tych informacji do globalnej przestrzeni nazw *szczytu* . Spowoduje to, że wszystkie wystąpienia kontrolki mapy w aplikacji domyślnie przeprowadzą do tych ustawień. Poniżej ustawia język francuski ("fr-FR") i widok regionalny na "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Druga opcja polega na przejściu tych informacji do opcji mapy podczas ładowania mapy. Jak to:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Za pomocą Azure Maps można załadować wiele wystąpień map na tej samej stronie z różnymi ustawieniami języka i regionu. Można również zaktualizować te ustawienia na mapie po jej załadowaniu. 

Szczegółowa lista [obsługiwanych języków](supported-languages.md) znajduje się w Azure Maps.

Oto przykład Azure Maps z językiem ustawionym na "fr", a regionem użytkownika ustawionym na "fr-FR".

<center>

![lokalizacji Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Ustawianie widoku mapy

W ramach obu Azure Maps i usługi Google Maps mapy dynamiczne można programistycznie przenieść do nowych lokalizacji geograficznych. Aby to zrobić, wywołaj odpowiednie funkcje w języku JavaScript. W przykładzie pokazano, jak utworzyć mapę wyświetlania zdjęć satelitarnych, Wyśrodkuj mapę na miejscu i zmień poziom powiększenia. Używane są następujące współrzędne lokalizacji: Długość geograficzna:-111,0225 i Szerokość geograficzna: 35,0272.

> [!NOTE]
> Usługa mapy Google używa kafelków, które są 256 pikseli w wymiarach, podczas gdy Azure Maps zużywa większy kafelek 512 pikseli. W takim przypadku Azure Maps wymaga mniejszej liczby żądań sieciowych do załadowania tego samego obszaru mapy co Google Maps. Ze względu na sposób, w jaki diagramy kafelków działają w kontrolkach mapy, należy odjęciu poziomu powiększenia używanego w usłudze mapy Google według numeru, który jest używany Azure Maps. Ta operacja arytmetyczna gwarantuje, że większe kafelki w Azure Maps renderują ten sam obszar mapy jak w usłudze mapy Google.

**Wcześniej: Google Maps**

Przenieś formant mapy usługi Google Maps przy użyciu metody `setOptions`. Ta metoda pozwala określić środek mapy i poziom powiększenia.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

](media/migrate-google-maps-web-app/google-maps-set-view.png)</center> widok zestawu usługi Google Maps ![

**Po: Azure Maps**

W Azure Maps Zmień położenie mapy za pomocą metody `setCamera` i Zmień styl mapy za pomocą metody `setStyle`. Współrzędne w Azure Maps są w formacie "Długość geograficzna", a wartość poziomu powiększenia jest odejmowana według jednego.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![widok zestawu Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Dodatkowe zasoby:**

- [Wybieranie stylu mapy](choose-map-style.md)
- [Obsługiwane style mapy](supported-map-styles.md)

### <a name="adding-a-marker"></a>Dodawanie znacznika

W Azure Maps istnieje wiele sposobów renderowania danych punktu na mapie:

- **Znaczniki HTML** — renderuje punkty przy użyciu tradycyjnych elementów dom. Znaczniki HTML obsługują przeciąganie.
- **Warstwa symboli** — renderuje punkty z ikoną lub tekstem w kontekście WebGL.
- **Warstwa bąbelków** — renderuje punkty jako okręgi na mapie. Promienie okręgów mogą być skalowane na podstawie właściwości danych.

Renderowanie warstw symboli i warstw bąbelków w kontekście WebGL. Obie warstwy mogą renderować duże zestawy punktów na mapie. Te warstwy wymagają, aby dane były przechowywane w źródle danych. Po uruchomieniu zdarzenia `ready` należy dodać do mapy źródła danych i warstwy renderowania. Znaczniki HTML są renderowane jako elementy DOM na stronie i nie korzystają ze źródła danych. Im więcej elementów DOM na stronie, tym mniejsza strona. W przypadku renderowania więcej niż stu punktów na mapie zaleca się użycie jednej z warstw renderowania zamiast.

Dodajmy znacznik do mapy o liczbie 10 nałożonej jako etykieta. Użyj długości geograficznej:-0,2 i szerokości geograficznej: 51,5.

**Wcześniej: Google Maps**

Za pomocą usługi Google Maps Dodaj znaczniki do mapy przy użyciu klasy `google.maps.Marker` i określ mapę jako jedną z opcji.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

znacznik ![Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Po: Azure Maps przy użyciu znaczników HTML**

W Azure Maps Użyj znaczników HTML, aby wyświetlić punkt na mapie. Znaczniki HTML są zalecane w przypadku aplikacji, które wymagają wyświetlania niewielkiej liczby punktów na mapie. Aby użyć znacznika HTML, Utwórz wystąpienie klasy `atlas.HtmlMarker`. Ustaw opcje tekstu i pozycji, a następnie Dodaj znacznik do mapy za pomocą metody `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps znacznika HTML](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Po: Azure Maps przy użyciu warstwy symboli**

Dla warstwy symboli Dodaj dane do źródła danych. Dołącz źródło danych do warstwy. Ponadto przed uruchomieniem zdarzenia `ready` należy dodać do mapy źródło danych i warstwę. Aby renderować unikatową wartość tekstową nad symbolem, informacje tekstowe muszą być przechowywane jako właściwość punktu danych. Do właściwości musi być przywoływana opcja `textField` warstwy. Takie podejście jest nieco bardziej wydajne niż używanie znaczników HTML, ale lepsza wydajność.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps warstwę symboli](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Dodatkowe zasoby:**

- [Tworzenie źródła danych](create-data-source-web-sdk.md)
- [Dodaj warstwę symboli](map-add-pin.md)
- [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Dodawanie znaczników HTML](map-add-custom-html.md)
- [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)
- [Opcje ikon warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opcja tekstu warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Klasa znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opcje znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Dodawanie znacznika niestandardowego

Możesz użyć niestandardowych obrazów do reprezentowania punktów na mapie. Mapa poniżej używa obrazu niestandardowego do wyświetlania punktu na mapie. Punkt jest wyświetlany na szerokości geograficznej: 51,5 i długości geograficznej: – 0,2. Kotwica przesunięcia położenia znacznika, tak aby punkt ikony pinezki wyrównany do poprawnego położenia na mapie.

<center>

![żółty obraz pinezki](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_pinezki. png</center>

**Wcześniej: Google Maps**

Utwórz niestandardowy znacznik, określając obiekt `Icon`, który zawiera `url` do obrazu. Określ punkt `anchor`, aby wyrównać punkt obrazu pinezki ze współrzędną na mapie. Wartość zakotwiczenia w usłudze Google Maps jest określana względem lewego górnego rogu obrazu.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![znacznika niestandardowego usługi Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Po: Azure Maps przy użyciu znaczników HTML**

Aby dostosować znacznik HTML, Przekaż `string` HTML lub `HTMLElement` do opcji `htmlContent` znacznika. Użyj opcji `anchor`, aby określić względne położenie znacznika względem współrzędnej położenia. Przypisz jeden z dziewięciu zdefiniowanych punktów odwołania do opcji `anchor`. Te zdefiniowane punkty to: "Center", "Top", "Bottom", "Left", "Right", "Top-Left", "Top-Right", "Bottom-Left", "dolny prawy". Zawartość jest domyślnie zakotwiczona do dolnego centrum zawartości HTML. Aby ułatwić migrację kodu z usługi Google Maps, należy ustawić `anchor` na "z lewej strony", a następnie użyć opcji `pixelOffset` z tym samym przesunięciem używanym w usłudze mapy Google. Przesunięcia w Azure Maps poruszają się w odwrotnym kierunku przesunięć w usłudze Google Maps. W związku z tym pomnóż przesunięcia przez minus jeden.

> [!TIP]
> Dodaj `pointer-events:none` jako styl w zawartości HTML, aby wyłączyć domyślne zachowanie przeciągania w przeglądarce Microsoft Edge, która będzie zawierać niechcianą ikonę.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps niestandardowego znacznika HTML](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Po: Azure Maps przy użyciu warstwy symboli**

Warstwy symboli w Azure Maps obsługują również obrazy niestandardowe. Najpierw Załaduj obraz do zasobów mapy i przypisz go z unikatowym IDENTYFIKATORem. Odwołuje się do obrazu w warstwie symboli. Użyj opcji `offset`, aby wyrównać obraz do poprawnego punktu na mapie. Użyj opcji `anchor`, aby określić względne położenie symbolu względem współrzędnych położenia. Użyj jednego z dziewięciu zdefiniowanych punktów odniesienia. Te punkty to: "Center", "Top", "Bottom", "Left", "Right", "Top-Left", "Top-Right", "Bottom-Left", "dolny prawy". Zawartość jest domyślnie zakotwiczona do dolnego centrum zawartości HTML. Aby ułatwić migrację kodu z usługi Google Maps, należy ustawić `anchor` na "z lewej strony", a następnie użyć opcji `offset` z tym samym przesunięciem używanym w usłudze mapy Google. Przesunięcia w Azure Maps poruszają się w odwrotnym kierunku przesunięć w usłudze Google Maps. W związku z tym pomnóż przesunięcia przez minus jeden.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps niestandardowej warstwy symboli](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Aby renderować zaawansowane punkty niestandardowe, należy użyć wielu warstw renderowania jednocześnie. Załóżmy na przykład, że chcesz mieć wiele pinezki, które mają tę samą ikonę w różnych kolorach koła. Zamiast tworzyć wiele obrazów dla każdej nakładki kolorów, Dodaj warstwę symboli na górze warstwy bąbelków. Czy pinezki odwołują się do tego samego źródła danych. Takie podejście będzie bardziej wydajne niż tworzenie i konserwowanie wielu różnych obrazów.

**Dodatkowe zasoby:**

- [Tworzenie źródła danych](create-data-source-web-sdk.md)
- [Dodaj warstwę symboli](map-add-pin.md)
- [Dodawanie znaczników HTML](map-add-custom-html.md)
- [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)
- [Opcje ikon warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opcja tekstu warstwy symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Klasa znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opcje znacznika HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Dodawanie linii łamanej

Użyj linii łamanej do reprezentowania linii lub ścieżki na mapie. Utwórzmy kreskowaną linię łamaną na mapie.

**Wcześniej: Google Maps**

Klasa linii łamanej akceptuje zestaw opcji. Przekaż tablicę współrzędnych w opcji `path` linii łamanej.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![linię łamaną w usłudze Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Po: Azure Maps**

Linie łamane są nazywane obiektami `LineString` lub `MultiLineString`. Te obiekty mogą być dodawane do źródła danych i renderowane przy użyciu warstwy liniowej. Dodaj `LineString` do źródła danych, a następnie Dodaj źródło danych do `LineLayer`, aby je renderować.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure Maps łamaną](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Dodatkowe zasoby:**

- [Dodawanie wierszy do mapy](map-add-line-layer.md)
- [Opcje warstwy linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Dodawanie wielokątu

Usługi Azure Maps i Google Maps zapewniają podobną obsługę wielokątów. Wielokąty są używane do reprezentowania obszaru na mapie. W poniższych przykładach pokazano, jak utworzyć wielokąt, który tworzy Trójkąt w oparciu o współrzędne środkowe mapy.

**Wcześniej: Google Maps**

Klasa Wielokąt akceptuje zestaw opcji. Przekaż tablicę współrzędnych do opcji `paths` wielokąta.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Wielokąt usługi Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Po: Azure Maps**

Dodawanie `Polygon` lub obiektów `MultiPolygon` do źródła danych. Renderowanie obiektu na mapie przy użyciu warstw. Renderowanie obszaru wielokąta przy użyciu warstwy wielokąta. I, renderowanie konspektu wielokąta przy użyciu warstwy liniowej.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Wielokąt Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Dodatkowe zasoby:**

- [Dodawanie wielokąta do mapy](map-add-shape.md)
- [Dodaj okrąg do mapy](map-add-shape.md#add-a-circle-to-the-map)
- [Opcje warstw wielokątów](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opcje warstwy linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Wyświetl okno informacji

Dodatkowe informacje na temat jednostki mogą być wyświetlane na mapie jako Klasa `google.maps.InfoWindow` w usłudze mapy Google. W Azure Maps funkcje te można osiągnąć przy użyciu klasy `atlas.Popup`. Następne przykłady umożliwiają dodanie znacznika do mapy. Po kliknięciu znacznika zostanie wyświetlone okno informacji lub menu podręczne.

**Wcześniej: Google Maps**

Tworzenie wystąpienia okna informacji przy użyciu konstruktora `google.maps.InfoWindow`.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![menu podręczne usługi Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Po: Azure Maps**

Użyjmy okna podręcznego, aby wyświetlić dodatkowe informacje o lokalizacji. Przekaż `string` HTML lub obiekt `HTMLElement` do opcji `content` podręcznej. Jeśli chcesz, okna podręczne mogą być wyświetlane niezależnie od dowolnego kształtu. W ten sposób okna podręczne wymagają określenia wartości `position`. Określ wartość `position`. Aby wyświetlić okno podręczne, wywołaj metodę `open` i przekaż `map`, w którym ma być wyświetlane okno podręczne.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps podręcznym](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Można to zrobić w taki sam sposób, jak w przypadku symboli, bąbelków, linii lub wielokątów, przekazując wybraną warstwę do kodu zdarzenia Maps zamiast znacznika.

**Dodatkowe zasoby:**

- [Dodawanie okna podręcznego](map-add-popup.md)
- [Podręczne z zawartością multimedialną](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Okna podręczne dla kształtów](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Używanie podręcznego z wieloma numerami PIN](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup — Klasa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Opcje wyskakujące](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importowanie pliku GEOJSON

Usługi Mapy Google obsługują ładowanie i dynamiczne określanie stylów GEOJSON za pośrednictwem klasy `google.maps.Data`. Funkcje tej klasy są bardziej wyrównane przy użyciu stylów opartych na danych Azure Maps. Jednak istnieje kluczowa różnica. Za pomocą usługi Google Maps należy określić funkcję wywołania zwrotnego. Logika biznesowa do napełniania stylów każdej funkcji przetwarzanej pojedynczo w wątku interfejsu użytkownika. Ale w Azure Maps warstwy obsługują Określanie wyrażeń opartych na danych jako opcje stylów. Te wyrażenia są przetwarzane w czasie renderowania w osobnym wątku. Podejście Azure Maps poprawia wydajność renderowania. Ta korzyść jest zauważalna, gdy duże zestawy danych muszą być renderowane szybko.

Poniższe przykłady ładują strumieniowe źródło danych ze wszystkich trzęsienia, w ciągu ostatnich siedmiu dni od agencji USGS. Dane dotyczące trzęsienia ziemi są renderowane jako okręgi skalowane na mapie. Kolor i skala każdego okręgu bazują na wielkości poszczególnych ziemi, które są przechowywane we właściwości `"mag"` każdej funkcji w zestawie danych. Jeśli wartość jest większa lub równa pięciu, okrąg będzie czerwony. Jeśli jest większa lub równa trzy, ale mniej niż pięć, okrąg zostanie pomarańczowy. Jeśli wartość jest mniejsza niż trzy, okrąg będzie zielony. Promień każdego okręgu będzie wykładniczy wielkości pomnożonej przez 0,1.

**Wcześniej: Google Maps**

Określ pojedynczą funkcję wywołania zwrotnego w metodzie `map.data.setStyle`. Wewnątrz funkcji wywołania zwrotnego należy zastosować logikę biznesową do każdej funkcji. Załaduj kanał informacyjny GEOJSON przy użyciu metody `map.data.loadGeoJson`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![GEOJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center> usługi Google Maps

**Po: Azure Maps**

GEOJSON jest podstawowym typem danych w Azure Maps. Zaimportuj go do źródła danych przy użyciu metody `datasource.importFromUrl`. Użyj warstwy bąbelkowej. Warstwa bąbelków zapewnia funkcję renderowania okręgów skalowanych na podstawie właściwości funkcji w źródle danych. Zamiast korzystać z funkcji wywołania zwrotnego, logika biznesowa jest konwertowana na wyrażenie i przenoszona do opcji stylu. Wyrażenia definiują sposób działania logiki biznesowej. Wyrażenia mogą być przesyłane do innego wątku i oceniane względem danych funkcji. Wiele źródeł danych i warstw można dodać do Azure Maps, z których każda ma inną logikę biznesową. Ta funkcja umożliwia renderowanie wielu zestawów danych na mapie na różne sposoby.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GEOJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Dodatkowe zasoby:**

- [Dodaj warstwę symboli](map-add-pin.md)
- [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Znaczniki klastra

Podczas wizualizacji wielu punktów danych na mapie punkty mogą nakładać się na siebie nawzajem. Nakładanie się sprawia, że mapa jest nieczytelna, a mapa staje się trudna do odczytania i użycia. Dane punktu klastrowania to proces łączenia punktów danych, które znajdują się blisko siebie i reprezentują je na mapie jako pojedyncze klastrowane punkty danych. Gdy użytkownik powiększa mapę, klastry rozdzielą się na poszczególne punkty danych. Punkty danych klastra w celu poprawy środowiska użytkownika i mapy wydajności.

W poniższych przykładach kod ładuje Źródło GEOJSON danych z ziemi z zeszłego tygodnia i dodaje je do mapy. Klastry są renderowane jako przeskalowane i kolorowe koła. Skala i kolor kół są zależne od liczby punktów, które zawierają.

> [!NOTE]
> Usługi Google Maps i Azure Maps używają nieco różnych algorytmów klastrowania. W związku z tym czasami dystrybucja punktów w klastrach różni się.

**Wcześniej: Google Maps**

Użyj biblioteki MarkerCluster do znaczników klastra. Ikony klastra są ograniczone do obrazów, które mają numery od jednej do pięciu jako nazwy. Są one hostowane w tym samym katalogu.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![klastrowanie usługi Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Po: Azure Maps**

Dodawanie danych i zarządzanie nimi w źródle danych. Połącz źródła danych i warstwy, a następnie Renderuj dane. Klasa `DataSource` w Azure Maps oferuje kilka opcji klastrowania.

- `cluster` — informuje źródło danych o danych punktu klastra.
- `clusterRadius` — promień w pikselach, aby wspólnie wskazywał punkty klastra.
- `clusterMaxZoom` — maksymalny poziom powiększenia, w którym występuje klaster. W przypadku powiększania więcej niż na tym poziomie wszystkie punkty są renderowane jako symbole.
- `clusterProperties` — definiuje niestandardowe właściwości, które są obliczane przy użyciu wyrażeń dla wszystkich punktów w każdym klastrze i dodawane do właściwości każdego punktu klastra.

Po włączeniu klastrowania źródło danych wyśle klastrowane i nieklastrowane punkty danych do warstw na potrzeby renderowania. Źródło danych umożliwia klastrowanie setek tysięcy punktów danych. Klastrowany punkt danych ma następujące właściwości:

| Nazwa właściwości             | Typ    | Opis   |
|---------------------------|---------|---------------|
| `cluster`                 | wartość logiczna | Wskazuje, czy funkcja reprezentuje klaster. |
| `cluster_id`              | ciąg  | Unikatowy identyfikator klastra, który może być używany z metodami DataSource `getClusterExpansionZoom`, `getClusterChildren`i `getClusterLeaves`. |
| `point_count`             | numer  | Liczba punktów, które zawiera klaster.  |
| `point_count_abbreviated` | ciąg  | Ciąg, który skróci wartość `point_count`, jeśli jest długi. (na przykład 4 000 to 4K)  |

Klasa `DataSource` ma następujące funkcje pomocnika do uzyskiwania dostępu do dodatkowych informacji o klastrze przy użyciu `cluster_id`.

| Metoda | Zwracany typ | Opis |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Funkcja Promise&lt;Array&lt;funkcji&lt;geometria&gt; \|&gt;&gt; | Pobiera elementy podrzędne danego klastra na następnym poziomie powiększenia. Te elementy podrzędne mogą być kombinacją kształtów i podklastrów. Podklastry będą funkcjami o właściwościach pasujących do ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Numer&lt;Promise&gt; | Oblicza poziom powiększenia, przy którym klaster rozpocznie rozszerzanie lub przerywanie. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Funkcja Promise&lt;Array&lt;funkcji&lt;geometria&gt; \|&gt;&gt; | Pobiera wszystkie punkty w klastrze. Ustaw `limit`, aby zwracał podzbiór punktów, i użyj `offset` do strony za pomocą punktów. |

Podczas renderowania danych klastrowanych na mapie często najlepiej jest używać co najmniej dwóch warstw. W poniższym przykładzie zastosowano trzy warstwy. Warstwa bąbelkowa służąca do rysowania skalowanego kolorowego koła na podstawie rozmiaru klastrów. Warstwa symboli służąca do renderowania rozmiaru klastra jako tekstu. I używa drugiej warstwy symbol do renderowania punktów nieklastrowanych. Istnieje wiele sposobów renderowania danych klastrowanych. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [danych punktu klastra](clustering-point-data-web-sdk.md) .

Bezpośrednio Importuj dane GEOJSON przy użyciu funkcji `importDataFromUrl` na klasie `DataSource`, wewnątrz mapy Azure Maps.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps klastrowanie](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Dodatkowe zasoby:**

- [Dodaj warstwę symboli](map-add-pin.md)
- [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)
- [Dane punktu klastra](clustering-point-data-web-sdk.md)
- [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Dodawanie mapy cieplnej

Mapy cieplne, znane także jako mapy gęstości punktów, są typem wizualizacji danych. Są one używane do reprezentowania gęstości danych przy użyciu szeregu kolorów. Są one często używane do wyświetlania danych "gorąca" na mapie. Mapy cieplne to świetny sposób renderowania zestawów danych z dużymi punktami.

Poniższe przykłady ładują strumieniowe źródło danych ze wszystkich trzęsienia, w ciągu ostatniego miesiąca, z agencji USGS i renderuje je jako ważoną mapy cieplnej. Właściwość `"mag"` jest używana jako waga.

**Wcześniej: Google Maps**

Aby utworzyć mapę cieplną, Załaduj bibliotekę "Wizualizacja", dodając `&libraries=visualization` do adresu URL skryptu interfejsu API. Warstwa mapy cieplnej w usłudze mapy Google nie obsługuje bezpośrednio danych GEOJSON. Najpierw Pobierz dane i Przekonwertuj je na tablicę ważonych punktów danych:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

Mapa cieplna usługi ![Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Po: Azure Maps**

Załaduj dane GEOJSON do źródła danych i połącz je ze źródłem danych w warstwie mapy cieplnej. Właściwość, która będzie używana dla wagi, może być przenoszona do opcji `weight` przy użyciu wyrażenia. Bezpośrednio Importuj dane GEOJSON do Azure Maps przy użyciu funkcji `importDataFromUrl` na klasie `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps Mapa cieplna](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Dodatkowe zasoby:**

- [Dodaj warstwę mapy cieplnej](map-add-heat-map-layer.md)
- [Klasa warstwy mapy cieplnej](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opcje warstwy mapy cieplnej](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Nałóż warstwę kafelków

Warstwy kafelków w Azure Maps są znane jako nakładki obrazów w usłudze mapy Google. Warstwy kafelków umożliwiają nakładanie dużych obrazów, które zostały podzielone na mniejsze obrazy z podziałem na fragmenty, które są wyrównane do systemu fragmentów mapy. To podejście jest często używane do nakładania dużych obrazów lub dużych zestawów danych.

Poniższe przykłady nakładają warstwę kafelków radarowych z Iowa środowiska Mesonet of Iowa State University.

**Wcześniej: Google Maps**

W usłudze mapy Google można tworzyć warstwy kafelków przy użyciu klasy `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

warstwa kafelków ![Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Po: Azure Maps**

Dodaj warstwę kafelków do mapy podobnie jak każda inna warstwa. Użyj sformatowanego adresu URL, który zawiera symbole zastępcze x, y powiększenia; `{x}`, `{y}`, `{z}`, aby określić warstwę, w której mają być dostępne kafelki. Azure Maps warstw kafelków obsługują również symbole zastępcze `{quadkey}`, `{bbox-epsg-3857}`i `{subdomain}`.

> [!TIP]
> W Azure Maps warstwach można łatwo renderować poniżej innych warstw, w tym warstw mapy podstawowej. Często wskazane jest renderowanie warstw kafelków poniżej etykiet mapy, dzięki czemu można je łatwo odczytać. Metoda `map.layers.add` przyjmuje drugi parametr, który jest identyfikatorem warstwy, w której ma zostać wstawiona Nowa warstwa poniżej. Aby wstawić warstwę kafelków pod etykietami mapy, użyj następującego kodu: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps warstwy kafelków](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Żądania kafelków można przechwytywać przy użyciu opcji `transformRequest` mapy. Pozwoli to na modyfikację lub dodanie nagłówków do żądania w razie potrzeby.

**Dodatkowe zasoby:**

- [Dodawanie warstw kafelków](map-add-tile-layer.md)
- [Klasa kafelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opcje warstwy kafelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Wyświetlanie ruchu

Dane o ruchu można przemieścić zarówno na platformie Azure, jak i w usłudze Google Maps.

**Wcześniej: Google Maps**

Nałóż dane ruchu na mapie za pomocą warstwy ruchu.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![ruch usługi Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Po: Azure Maps**

Azure Maps oferuje kilka różnych opcji wyświetlania ruchu. Wyświetlaj zdarzenia dotyczące ruchu, takie jak zamknięcie dróg i wypadki jako ikony na mapie. Nałóż przepływ ruchu i kolorowe zakodowane drogi na mapie. Kolory można modyfikować w zależności od normalnego oczekiwanego opóźnienia lub bezwzględnego opóźnienia. Dane o zdarzeniach w Azure Maps są aktualizowane co minutę i przepływają aktualizacje danych co dwie minuty.

Przypisz odpowiednie wartości dla opcji `setTraffic`.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps ruch](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Po kliknięciu jednej z ikon ruchu w Azure Maps w oknie podręcznym zostaną wyświetlone dodatkowe informacje.

<center>

![Azure Maps zdarzenie dotyczące ruchu](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Dodatkowe zasoby:**

- [Pokaż ruch na mapie](map-show-traffic.md)
- [Opcje nakładki ruchu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Dodawanie nakładki uziemienia

Zarówno platforma Azure, jak i usługa Google Maps obsługują nałożenie obrazów ze spisem na mapie. Obrazy geograficzne są przesuwane i skalowane w miarę kadrowania i powiększania mapy. W usłudze Google Maps obrazy geograficzne są znane jako nakładki naziemne w Azure Maps są one nazywane warstwami obrazu. Doskonale nadaje się do kompilowania planów piętra, nakładania starych map lub obrazów z drona.

**Wcześniej: Google Maps**

Określ adres URL obrazu, który chcesz nałożyć, i obwiednię, aby powiązać obraz na mapie. Ten przykład nakłada obraz mapy [Newark nowej Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapie.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Uruchomienie tego kodu w przeglądarce spowoduje wyświetlenie mapy, która wygląda jak na poniższej ilustracji:

<center>

Nakładka obrazu ![Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Po: Azure Maps**

Użyj klasy `atlas.layer.ImageLayer`, aby nakładać obrazy ze spisem. Ta klasa wymaga adresu URL do obrazu oraz zestawu współrzędnych dla czterech rogów obrazu. Obraz musi być hostowany w tej samej domenie lub mieć włączony mechanizm CORs.

> [!TIP]
> Jeśli masz tylko informacje o północy, południe, wschód, zachód i rotacja, a nie masz współrzędnych dla każdego rogu obrazu, możesz użyć statycznej metody [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) .

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

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
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps nakładki obrazu](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Dodatkowe zasoby:**

- [Nałóż obraz](map-add-image-layer.md)
- [Klasa warstwy obrazu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Dodatkowe przykłady kodu

Poniżej przedstawiono kilka dodatkowych przykładów kodu związanych z migracją usługi Google Maps:

- [Narzędzia do rysowania](map-add-drawing-toolbar.md)
- [Ogranicz mapowanie do dwóch panoramowania palca](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Ogranicz powiększenie kółka przewijania](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Tworzenie kontrolki na pełnym ekranie](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Services**

- [Korzystanie z modułu Azure Maps Services](how-to-use-services-module.md)
- [Wyszukiwanie punktów orientacyjnych](map-search-location.md)
- [Uzyskiwanie informacji z współrzędnych (odwrotne geokod)](map-get-information-from-coordinate.md)
- [Pokaż kierunki od A do B](map-route.md)
- [Wyszukaj automatyczne sugerowanie przy użyciu interfejsu użytkownika JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapowanie wersji v3 usługi Google Maps do Azure Maps Web SDK

Poniższy dodatek zawiera krzyżowe odwołanie do najczęściej używanych klas w usłudze Google Maps v3 i równoważnej Azure Maps Web SDK.

### <a name="core-classes"></a>Klasy podstawowe

| Mapy Google   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Elementy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. Data. Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [Atlas. Data. BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Opcje](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. Pikseli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Klasy nakładki

| Mapy Google  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[Atlas. Data. punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [Atlas. Data. Wielokąt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [Atlas. Data. LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Zobacz [Dodawanie okręgu do mapy](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Klasy usług

Zestaw SDK sieci Web Azure Maps zawiera moduł usług, który można załadować osobno. Ten moduł zawija Azure Maps usług REST przy użyciu internetowego interfejsu API i może być używany w aplikacjach JavaScript, TypeScript i Node. js.

| Mapy Google | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [Atlas. Service. RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [Atlas. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Biblioteki

Biblioteki dodają dodatkowe funkcje do mapy. Wiele z tych bibliotek znajduje się w podstawowym zestawie SDK Azure Maps. Poniżej przedstawiono niektóre równoważne klasy używane zamiast tych bibliotek usługi Google Maps

| Mapy Google           | Azure Maps   |
|-----------------------|--------------|
| Biblioteka rysowania       | [Moduł narzędzi do rysowania](set-drawing-options.md) |
| Biblioteka geometrii      | [Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Biblioteka wizualizacji | [Warstwa mapy cieplnej](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zestawie SDK sieci Web Azure Maps.

> [!div class="nextstepaction"]
> [Jak używać kontrolki mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Jak używać modułu usług](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Jak używać modułu narzędzi do rysowania](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

