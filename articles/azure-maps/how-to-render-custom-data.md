---
title: Renderowanie niestandardowych danych na mapie rastrowej | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak renderować dane niestandardowe na mapie rastrowej za pomocą usługi Microsoft Azure Maps static Image Service.
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: f036847a9d46231d65d150cd4e0a76471d1ad612
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766042"
---
# <a name="render-custom-data-on-a-raster-map"></a>Renderowanie niestandardowych danych na mapie rastrowej

W tym artykule wyjaśniono, jak używać [statycznej usługi obrazu](https://docs.microsoft.com/rest/api/maps/render/getmapimage) z funkcją kompozycji obrazu, aby zezwalać na nakładki na górze mapy rastrowej. Kompozycja obrazów obejmuje możliwość uzyskania kafelka rastrowego, z dodatkowymi danymi, takimi jak niestandardowe pinezki, etykiety i nakładki geometryczne.

Aby renderować niestandardowe pinezki, etykiety i nakładki geometryczne, można użyć aplikacji programu Poster. Za pomocą [interfejsów API usługi danych](https://docs.microsoft.com/rest/api/maps/data) Azure Maps można przechowywać i renderować nakładki.

> [!Tip]
> Często jest znacznie bardziej kosztowne użycie zestawu SDK sieci Web Azure Maps do wyświetlania prostej mapy na stronie sieci Web niż w przypadku korzystania z usługi obrazu statycznego. Zestaw SDK sieci Web używa kafelków mapy, chyba że użytkownik Pans i powiększa mapę, często generują jedynie część transakcji na obciążenie mapy. Należy pamiętać, że zestaw SDK sieci Web Azure Maps zawiera opcje wyłączania panoramowania i powiększania. Ponadto zestaw SDK sieci Web Azure Maps zapewnia bogatszy zestaw opcji wizualizacji danych niż usługa sieci Web mapy statycznej.  

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby wykonać procedury opisane w tym artykule, musisz najpierw utworzyć konto Azure Maps i pobrać klucz konta Maps. Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) , aby utworzyć subskrypcję konta Azure Maps, i wykonaj kroki opisane w sekcji [Uzyskiwanie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) , aby uzyskać klucz podstawowy dla Twojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Renderowanie pinezki z etykietami i obrazem niestandardowym

> [!Note]
> Procedura opisana w tej sekcji wymaga konta Azure Maps w warstwie cenowej S0 lub S1.

Warstwa S0 konta Azure Maps obsługuje tylko jedno wystąpienie parametru `pins`. Umożliwia renderowanie do pięciu pinezki, określonych w żądaniu adresu URL, przy użyciu obrazu niestandardowego.

Aby renderować pinezki z etykietami i obrazem niestandardowym, wykonaj następujące kroki:

1. Utwórz kolekcję, w której mają być przechowywane żądania. W aplikacji Poster wybierz pozycję **Nowy**. W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja**. Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** . 

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** dla pinezki. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, w której ma zostać zapisane żądanie, a następnie wybierz pozycję **Zapisz**.
    
    ![Utwórz żądanie w programie Poster](./media/how-to-render-custom-data/postman-new.png)

3. Wybierz metodę GET HTTP na karcie Konstruktor i wprowadź następujący adres URL, aby utworzyć żądanie GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Oto obraz przedstawiający wyniki:

    ![Niestandardowa Pinezka z etykietą](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Pobieranie danych z Azure Maps magazynu danych

> [!Note]
> Procedura opisana w tej sekcji wymaga konta Azure Maps w warstwie cenowej S1.

Możesz również uzyskać informacje o ścieżce i lokalizacji numeru PIN przy użyciu [interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Wykonaj poniższe kroki, aby przekazać dane ścieżki i pinezki.

1. W aplikacji Poster Otwórz nową kartę w kolekcji utworzonej w poprzedniej sekcji. Wybierz metodę POST HTTP na karcie Konstruktor i wprowadź następujący adres URL, aby wykonać żądanie POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Na karcie **Parametry** wprowadź następujące pary klucz/wartość, które są używane w adresie URL żądania post. Zastąp wartość `subscription-key` kluczem subskrypcji Azure Maps.
    
    ![Parametry klucza/wartości w programie Poster](./media/how-to-render-custom-data/postman-key-vals.png)

3. Na karcie **treść** wybierz format nieprzetworzonych danych wejściowych, a następnie wybierz pozycję JSON jako format danych wejściowych z listy rozwijanej. Podaj ten kod JSON jako dane do przekazania:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Wybierz pozycję **Wyślij** i przejrzyj nagłówek odpowiedzi. Po pomyślnym żądaniu nagłówek lokalizacji będzie zawierać identyfikator URI stanu, aby sprawdzić bieżący stan żądania przekazywania. Identyfikator URI stanu będzie mieć następujący format.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Skopiuj identyfikator URI stanu i Dołącz do niego parametr Subscription-Key z wartością klucza subskrypcji konta Azure Maps. Użyj tego samego klucza subskrypcji konta, który został użyty do przekazania danych. Format identyfikatora URI stanu powinien wyglądać podobnie do przedstawionego poniżej:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Aby uzyskać udId, Otwórz nową kartę w aplikacji Poster i wybierz pozycję Pobierz metodę HTTP na karcie Konstruktor i wprowadź żądanie GET w identyfikatorze URI stanu. Jeśli przekazywanie danych zakończyło się pomyślnie, otrzymasz udId w treści odpowiedzi. Skopiuj udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Użyj wartości `udId` otrzymanej z interfejsu API przekazywania danych w celu renderowania funkcji na mapie. Aby to zrobić, Otwórz nową kartę w kolekcji utworzonej w poprzedniej sekcji. Wybierz metodę GET HTTP na karcie Konstruktor, Zastąp wartości {Subscription-Key} i {udId} wartościami, a następnie wprowadź ten adres URL, aby uzyskać żądanie GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Oto obraz odpowiedzi:

    ![Pobieranie danych z Azure Maps magazynu danych](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renderuj wielokąt z kolorami i nieprzezroczystością

> [!Note]
> Procedura opisana w tej sekcji wymaga konta Azure Maps w warstwie cenowej S1.


Możesz zmodyfikować wygląd wielokąta, używając modyfikatorów stylu z [parametrem Path](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. W aplikacji Poster Otwórz nową kartę w utworzonej wcześniej kolekcji. Wybierz metodę GET HTTP na karcie Konstruktor i wprowadź następujący adres URL w celu skonfigurowania żądania GET w celu renderowania wielokąta z kolorami i nieprzezroczystością:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Oto obraz odpowiedzi:

    ![Renderuj nieprzezroczysty Wielokąt](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Renderuj okrąg i pinezki z etykietami niestandardowymi

> [!Note]
> Procedura opisana w tej sekcji wymaga konta Azure Maps w warstwie cenowej S1.


Możesz zmodyfikować wygląd pinów przez dodanie modyfikatorów stylów. Na przykład aby zwiększyć lub zmniejszyć pinezki i ich etykiety, użyj modyfikatora `sc` "Style skalowania". Ten modyfikator przyjmuje wartość większą od zera. Wartość 1 jest skalą standardową. Wartości większe niż 1 spowodują, że numery PIN będą większe, a wartości mniejsze od 1 staną się mniejsze. Aby uzyskać więcej informacji na temat modyfikatorów stylu, zobacz [Parametry ścieżki usługi obrazu statycznego](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Wykonaj następujące kroki, aby renderować okrąg i pinezki z etykietami niestandardowymi:

1. W aplikacji Poster Otwórz nową kartę w utworzonej wcześniej kolekcji. Wybierz metodę GET HTTP na karcie Konstruktor i wprowadź ten adres URL w celu uzyskania żądania GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Oto obraz odpowiedzi:

    ![Renderuj okrąg z niestandardowymi pinezkami](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Aby zmienić kolor pinezki z ostatniego kroku, Zmień modyfikator stylu "co". Spójrz na `pins=default|la15+50|al0.66|lc003C62|co002D62|`, bieżący kolor zostałby określony jako #002D62 w CSS. Załóżmy, że chcesz zmienić go na #41d42a. Zapisz nową wartość koloru Po specyfikatorze "co", w tym: `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. Utwórz nowe żądanie GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Poniżej znajduje się obraz odpowiedzi po zmianie kolorów numerów PIN:

    ![Renderuj okrąg z zaktualizowanymi pinezkami](./media/how-to-render-custom-data/circle-updated-pins.png)

Analogicznie, można zmieniać, dodawać i usuwać inne Modyfikatory stylów.

## <a name="next-steps"></a>Następne kroki


* Zapoznaj się z dokumentacją [interfejsu API pobierania obrazu Azure Maps](https://docs.microsoft.com/rest/api/maps/render/getmapimage) .
* Aby dowiedzieć się więcej na temat usługi danych Azure Maps, zapoznaj się z [dokumentacją usługi](https://docs.microsoft.com/rest/api/maps/data).

