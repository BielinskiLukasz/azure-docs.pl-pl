---
title: "Ładowanie zasobów do funkcji tworzenia wycinków Azure Media | Dokumentacja firmy Microsoft"
description: "Kroki dotyczące ładowania zasobów do funkcji tworzenia wycinków multimediów Azure"
services: media-services
keywords: "klip subclip; kodowanie; nośnika"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6062e6ae22971055e186b5d1068ba78c900a7a4c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2017
---
# <a name="loading-assets-into-azure-media-clipper"></a>Ładowanie zasobów do funkcji tworzenia wycinków multimediów Azure
Zasoby mogą zostać załadowane do funkcji tworzenia wycinków multimediów dla platformy Azure za pomocą dwóch metod:
1. Statycznie przekazując biblioteki zasobów
2. Dynamiczne generowanie listy zasobów za pomocą interfejsu API

## <a name="loading-static-asset-library"></a>Podczas ładowania biblioteki statycznej zasobów
W takim przypadku należy przekazać statycznego zestawu zasobów do funkcji tworzenia wycinków. Każdy zasobów zawiera identyfikator zasobów/filter AMS, nazwa, opublikowanego adresu URL przesyłania strumieniowego. Jeśli to konieczne, ochrony zawartości tokenu uwierzytelniania lub tablicę miniatur adresy URL mogą być przekazywane w. Jeśli przekazano miniatur są umieszczane w interfejsie. W zastosowaniach zasobów biblioteki statyczne i w małych można przekazać w kontrakcie zasobów dla każdego zasobu w bibliotece.

Aby załadować biblioteki statycznej zasobów, należy użyć **załadować** można przekazać w JSON reprezentację każdego zasobu. Poniższy przykładowy kod przedstawia reprezentacja JSON dla jednego elementu zawartości.

```javascript
var assets = 
{
  /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
  "id": "my-asset-or-dynamic-manifest-asset-filter-id",

  /* Required: represents the asset name as shown in the Clipper interface */
  "name": "My Asset or Dynamic Manifest Asset Filter Name",

  /* Required: must be one of the following values: "asset" or "filter" */
  /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
  "type": "asset",

  /* Required */
  "source": {

    /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
    "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",

    /* Optional: default value "application/vnd.ms-sstr+xml" */
    "type": "application/vnd.ms-sstr+xml",

    /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
    "protectionInfo": [{
        "type": "AES",
        "authenticationToken": "Bearer aes-token-placeholder"
      },
      {
        "type": "PlayReady",
        "authenticationToken": "Bearer playready-token-placeholder"
      },
      {
        "type": "Widevine",
        "authenticationToken": "Bearer widevine-token-placeholder"
      },
      {
        "type": "FairPlay",
        "certificateUrl": "//example/path/to/myfairplay.der",
        "authenticationToken": "Bearer fairplay-token-placeholder"
      }
    ]
  },

  /* Optional: array containing thumbnail URLs for the video. */
  /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
  "thumbnails": [
    "//example/path/thumbnail_001.jpg",
    "//example/path/thumbnail_002.jpg",
    "//example/path/thumbnail_003.jpg",
    "//example/path/thumbnail_004.jpg",
    "//example/path/thumbnail_005.jpg"
  ]
};
var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.load(assets)
```

> [!NOTE]
> Do miniatury adresów URL będzie działać zgodnie z oczekiwaniami osi czasu funkcji tworzenia wycinków musi być równomiernie rozłożony między wideo (oparte na czas trwania) w kolejności chronologicznej w tablicy. Poniższy fragment predefiniowanych JSON jako odwołanie próbki służy do generowania obrazów za pomocą procesor "Media Encoder Standard":

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="loading-dynamic-asset-library"></a>Podczas ładowania biblioteki zawartości dynamicznej
Alternatywnie można załadować zasoby dynamicznie za pośrednictwem wywołania zwrotnego. W sytuacjach, gdy zasoby są generowane dynamicznie lub biblioteki jest duży należy załadować za pośrednictwem wywołania zwrotnego. Aby załadować trwałego dynamicznie, musi implementować onLoadAssets opcjonalna funkcja wywołania zwrotnego. Ta funkcja została przekazana do funkcji tworzenia wycinków podczas inicjowania. Rozwiązany zasobów należy stosować się do sam kontrakt, co statycznie załadować zasoby. Poniższy przykładowy kod przedstawia podpis metody, oczekiwane dane wejściowe i oczekiwane dane wyjściowe.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: perform the back-end AJAX request to get the assets using the filter parameters (search, skip, take).
            var assets = [{
                // asset (input contract)
            }, {
                // asset (input contract)
            }];

            if (/* everything turned out fine */ assets !== null) {
                resolve({
                    total: 100,
                    assets: assets
                });
            }
            else {
                reject(Error("error details"));
            }
        });

        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```