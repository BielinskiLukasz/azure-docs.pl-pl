---
title: Wyświetlanie instrukcji dotyczących trasy z usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wyświetlić kierunkach między dwiema lokalizacjami na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 4f999df23b5c08125b107f15091d90d85a045908
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586294"
---
# <a name="show-directions-from-a-to-b"></a>Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B

W tym artykule pokazano, jak utworzyć żądanie trasy i wyświetlania trasy na mapie.

Istnieją dwa sposoby, aby to zrobić. Pierwszy sposób to zapytanie [interfejsu API usługi Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) przez moduł usługi. Druga metoda jest zapewnienie [XMLHttpRequest](https://xhr.spec.whatwg.org/) do interfejsu API. Obie metody zostały podane poniżej.

## <a name="query-the-route-via-service-module"></a>Zapytanie tras za pomocą modułu usług

<iframe height='500' scrolling='no' title='Wyświetlanie instrukcji dotyczących trasy od A do B na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Wyświetlanie instrukcji dotyczących trasy od A do B na mapie (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Wiersz w drugim bloku kodu tworzy wystąpienie usługi klienta.

Trzeci tworzy i dodaje [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) obiektu do mapy.

 Wiersz jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z LineString. A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderuje wiersz obiektów w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako wiersze na mapie. Czwarty bloku kodu tworzy i Dodaj warstwę linii do mapy. Zobacz właściwości warstwy linii w [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie. Piąty bloku kodu tworzy i dodać symbol warstwę do mapy.

Szósty bloku kodu tworzy początkową i końcową [punktów](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) obiektu i dodaje je do obiektu dataSource.

Siódmego bloku kodu ustawia granice mapy za pomocą mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) właściwości.

Ostatni blok kodu zapytania usługi Azure Maps usługą routingu, który jest częścią programu [moduł usługi](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). [GetRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metoda jest używana do pobierania trasy między początkowego i punktu końcowego. Odpowiedź następnie jest analizowany w formacie GeoJSON, używając [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routegeojson?view=azure-iot-typescript-latest) metody. Renderuje następnie odpowiedzi jako trasy na mapie. Aby uzyskać więcej informacji na temat dodawania wiersz do mapy, zobacz [Dodaj wiersz na mapie](./map-add-shape.md#addALine).

Utworzenia i ustawić w mapie zapytania trasy, źródła danych, symboli i warstwy wiersza i granice aparatu [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, że wyniki są wyświetlane po mapy ładuje pełni.

## <a name="query-the-route-via-xmlhttprequest"></a>Zapytanie tras za pomocą XMLHttpRequest

<iframe height='500' scrolling='no' title='Wyświetlanie instrukcji dotyczących trasy od A do B na mapie' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Wyświetlanie instrukcji dotyczących trasy od A do B na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Tworzy drugi blok kodu, a także dodaje [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) obiektu do mapy.

Trzeci blok kodu tworzy punkty początkowy i miejsce docelowe dla danej trasy i dodaje je do źródła danych. Możesz zobaczyć [Dodawanie numeru pin na mapie](map-add-pin.md) instrukcje dotyczące obsługi [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

 A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderuje wiersz obiektów w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako wiersze na mapie. Czwarty bloku kodu tworzy i dodaje warstwę linii do mapy. Zobacz właściwości warstwy linii w [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie. Piąty bloku kodu tworzy i dodać symbol warstwę do mapy. Zobacz właściwości warstwy symboli w [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Tworzy kolejny blok kodu `SouthWest` i `NorthEast` punkty z punkty początkowy i docelowym i ustawia granice mapy za pomocą mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) właściwości.

Wysyła ostatniego bloku kodu [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejsu API usługi Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Analizuje przychodzącą odpowiedź. I pomyślnej odpowiedzi zbiera informacje o długości i szerokości geograficznej dla każdego punktu trasy i tworzy tablicę wierszy, nawiązując połączenie tych punktów. Dodaje te wiersze na źródło danych w celu renderowania trasy na mapie. Możesz zobaczyć [Dodaj wiersz na mapie](./map-add-shape.md#addALine) instrukcje.

Utworzenia i ustawić w mapie zapytania trasy, źródła danych, symboli i warstwy wiersza i granice aparatu [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, że wyniki są wyświetlane po mapy ładuje pełni.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Wyświetlanie ruchu na mapie](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakcja z mapą — zdarzenia myszy](./map-events.md)
