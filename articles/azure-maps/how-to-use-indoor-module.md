---
title: Korzystanie z Azure Maps module Maps
description: Dowiedz się, w jaki sposób używać modułu Maps mapy Microsoft Azure Maps do renderowania map przez osadzenie bibliotek JavaScript modułu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c0b043bdb20cad508950a11853403958340acadf
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434202"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Korzystanie z Azure Maps module Maps

Zestaw SDK sieci Web Azure Maps zawiera *Azure Maps modułem obmieszczeń* . Moduł *Azure Maps pomieszczeń* umożliwia renderowanie map pomieszczeń utworzonych w programie Azure Maps Creator.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Tworzenie zasobu twórcy](how-to-manage-creator.md)
3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.
4. Uzyskaj i a, wykonując `tilesetId` `statesetId` [Samouczek dotyczący tworzenia map pomieszczeń](tutorial-creator-indoor-maps.md).
 Musisz użyć tych identyfikatorów, aby renderować mapy wewnętrzne przy użyciu modułu Azure Maps Maps.

## <a name="embed-the-indoor-maps-module"></a>Osadź moduł Maps

Można zainstalować i osadzić moduł *Azure Mapsowy* w jeden z dwóch sposobów.

Aby używać globalnie hostowanej wersji platformy Azure Content Delivery Network w module *Azure Maps* , należy odwołać się do następującego odwołania do arkusza JavaScript i stylów w `<head>` elemencie pliku HTML:

```html
<script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
```

 Lub można pobrać moduł *Azure Mapsowy* . Moduł *Azure Maps pomieszczeń* zawiera bibliotekę kliencką do uzyskiwania dostępu do usług Azure Maps. Wykonaj poniższe kroki, aby zainstalować i *załadować moduł pozostały do aplikacji* sieci Web.  
  
  1. Pobierz [pakiet Azure-Maps-pomieszczeń](https://www.npmjs.com/package/azure-maps-indoor).
  
  2. Zainstaluj pakiet NPM. Upewnij się, że korzystasz z uprawnień administratora w konsoli programu:

      ```powershell
      >npm install azure-maps-control
      >npm install azure-maps-indoor
      ```

  3. Odwołuje się do *Azure Maps* JavaScript i arkusz stylów w `<head>` elemencie pliku HTML:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
      ```

## <a name="instantiate-the-map-object"></a>Tworzenie wystąpienia obiektu mapy

Najpierw Utwórz *obiekt mapy*. *Obiekt mapy* zostanie użyty w następnym kroku w celu utworzenia wystąpienia obiektu *Menedżera pomieszczeń* .  Poniższy kod pokazuje, jak utworzyć wystąpienie *obiektu mapy*:

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13315, 47.63637],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: { 
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Tworzenie wystąpienia kierownika pomieszczeń

Aby załadować tilesetsowy i styl mapy kafelków, należy utworzyć wystąpienie *kierownika pomieszczeń*. Utwórz wystąpienie *kierownika pomieszczeń* , dostarczając *obiekt mapy* i odpowiednie `tilesetId` . Jeśli chcesz obsługiwać [Style mapy dynamicznej](indoor-map-dynamic-styling.md), musisz przekazać `statesetId` . W `statesetId` nazwie zmiennej jest rozróżniana wielkość liter. Kod powinien wyglądać podobnie do poniższego kodu JavaScript.

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

Aby włączyć sondowanie danych stanu, które zapewniasz, należy podać `statesetId` wywołanie i `indoorManager.setDynamicStyling(true)` . Dane stanu sondowania umożliwiają dynamiczne aktualizowanie stanu właściwości dynamicznych lub *Stanów*. Na przykład funkcja, taka jak pomieszczenie, może mieć wywołaną Właściwość dynamiczną (*State*) `occupancy` . Aplikacja może chcieć przeprowadzić sondowanie w poszukiwaniu zmian *stanu* , aby odzwierciedlić zmiany wewnątrz mapy wizualizacji. Poniższy kod pokazuje, jak włączyć sondowanie stanu:

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Kontrolka selektora poziomu pośredniego

 Kontrolka *selektora poziomu wewnątrz* umożliwia zmianę poziomu renderowanej mapy. Opcjonalnie można zainicjować kontrolkę *selektora poziomu* pośredniego za pośrednictwem *Menedżera pomieszczeń*. Oto kod, aby zainicjować selektor kontroli poziomu:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Zdarzenia wewnętrzne

 Moduł *Azure Mapsa obmieszczeń* obsługuje zdarzenia *obiektów map* . Detektory zdarzeń *obiektu mapy* są wywoływane, gdy poziom lub obiekt został zmieniony. Jeśli chcesz uruchomić kod w przypadku zmiany poziomu lub obiektu, umieść swój kod wewnątrz odbiornika zdarzeń. Poniższy kod pokazuje, jak można dodawać detektory zdarzeń do *obiektu mapy*.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

`eventData`Zmienna przechowuje informacje o poziomie lub funkcji, która wywołała `levelchanged` `facilitychanged` zdarzenie lub. Po zmianie poziomu `eventData` obiekt będzie zawierać `facilityId` , nowe `levelNumber` i inne metadane. Po zmianie `eventData` obiektu obiekt będzie zawierać nowe `facilityId` , nowe `levelNumber` i inne metadane.

## <a name="example-use-the-indoor-maps-module"></a>Przykład: Użycie modułu Maps

W tym przykładzie pokazano, jak używać modułu *obAzure Mapsego* w aplikacji sieci Web. Chociaż przykład jest ograniczony do zakresu, zawiera podstawowe informacje o tym, co należy zrobić, aby rozpocząć pracę przy użyciu modułu *Azure Mapsu pomieszczeń* . Pełny kod HTML jest poniżej tych kroków.

1. Użyj [opcji](#embed-the-indoor-maps-module) Content Delivery Network platformy Azure, aby zainstalować moduł *Azure Maps* .

2. Utwórz nowy plik HTML

3. W nagłówku HTML odwołują się do *Azure Maps* stylu JavaScript i style arkusza stylów.

4. Zainicjuj *obiekt mapy*. *Obiekt mapy* obsługuje następujące opcje:
    - `Subscription key`jest Azure Maps podstawowym kluczem subskrypcji.
    - `center`definiuje szerokość geograficzną i długość geograficzną dla lokalizacji centralnego centrum mapy. Podaj wartość dla `center` , jeśli nie chcesz podawać wartości dla `bounds` . Format powinien wyglądać następująco `center` : [-122,13315, 47,63637].
    - `bounds`jest najmniejszym prostokątnym kształtem, który zawiera dane mapy tileset. Ustaw wartość dla `bounds` opcji, jeśli nie chcesz ustawiać wartości dla `center` . Granice mapy można znaleźć, wywołując [interfejs API listy tileset](https://docs.microsoft.com/rest/api/maps/tileset/listpreview). Interfejs API listy tileset zwraca `bbox` , który można analizować i przypisywać do `bounds` . Format powinien wyglądać następująco `bounds` : [# zachód, # południe, # wschód, # północ].
    - `style`umożliwia ustawienie koloru tła. Aby wyświetlić białe tło, zdefiniuj `style` jako "puste".
    - `zoom`umożliwia określenie minimalnych i maksymalnych poziomów powiększenia dla mapy.

5. Następnie Utwórz moduł *Menedżera pomieszczeń* . Przypisz *Azure Maps* `tilesetId` , a opcjonalnie Dodaj `statesetId` .

6. Utwórz wystąpienie formantu *selektora poziomu* pośredniego.

7. Dodaj detektory zdarzeń *obiektu mapy* .  

Plik powinien teraz wyglądać podobnie do poniższego kodu HTML.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Aby wyświetlić mapę pomieszczeń, Załaduj ją do przeglądarki sieci Web. Powinien wyglądać podobnie do poniższego obrazu. Jeśli klikniesz funkcję Stairwell, *Selektor poziomu* zostanie wyświetlony w prawym górnym rogu.

  ![obraz mapy pomieszczeń](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsami API, które są powiązane z Azure Maps modułem *obmieszczeń* :

> [!div class="nextstepaction"]
> [Wymagania dotyczące pakietu do rysowania](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Kreator dla map pomieszczeń](creator-indoor-maps.md)

Dowiedz się więcej na temat dodawania większej ilości danych do mapy:

> [!div class="nextstepaction"]
> [Dynamiczne style map](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)