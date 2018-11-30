---
title: Tworzenie filtrów za pomocą usługi Azure Media Services — interfejs API REST | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów, więc klienta można ich używać do określonych sekcji strumienia strumienia. Usługa Media Services tworzy manifestów dynamicznych w celu uzyskania tego selektywne przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 6b0ef646ba9ea535038f181ebfff5bf7639afdf8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633626"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Tworzenie filtrów za pomocą interfejsu API REST usługi Media Services

Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe wydarzeń na żywo lub wideo na żądanie) klienta może wymagać większej elastyczności niż opisane w pliku manifestu zasobu domyślnego. Usługa Azure Media Services umożliwia definiowanie filtrów kont i zasobów filtry dla zawartości. Aby uzyskać więcej informacji, zobacz [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).

W tym temacie pokazano, jak zdefiniować filtr dla wideo na żądanie zasobów i utworzyć za pomocą interfejsów API REST [filtrów kont](https://docs.microsoft.com/rest/api/media/accountfilters) i [filtry zasobów](https://docs.microsoft.com/rest/api/media/assetfilters). 

## <a name="prerequisites"></a>Wymagania wstępne 

Aby wykonać kroki opisane w tym temacie, musisz:

- Przegląd [filtrów i manifestów dynamicznych](filters-dynamic-manifest-overview.md).
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md). Upewnij się, że do zapamiętania nazwę grupy zasobów i nazwę konta usługi Media Services. 
- [Konfigurowanie narzędzia Postman dla wywołań interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md)/

## <a name="define-a-filter"></a>Określa filtr  

Poniżej przedstawiono **treść żądania** przykładu, który definiuje warunki wybór ścieżki, które są dodawane do manifestu. Ten filtr zawiera ścieżki audio, które są w języku angielskim z 3 WE i wideo ścieżek, które mają szybkości transmisji bitów w 0-1000000 zakresu.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "Language",
                        "value": "en",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "NotEqual"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Tworzenie filtrów konta

W kolekcji Postman, która został pobrany, wybierz **filtrów kont**->**tworzenia lub aktualizacji Filtr konta**.

**Umieścić** metoda żądania HTTP jest podobny do:

PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01

Wybierz **treści** kartę i wklej dane json kod [wcześniej zdefiniowaną](#define-a-filter).

Wybierz pozycję **Wyślij**. 

Filtr został utworzony.

Aby uzyskać więcej informacji, zobacz [tworzenia lub aktualizacji](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Zobacz też [JSON Przykłady filtrów](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Tworzenie filtrów zasobów  

W kolekcji Postman "Usługi Media Services v3", który został pobrany, wybierz **zasoby**-> ** tworzenia lub aktualizacji filtru zasobów.

**Umieścić** metoda żądania HTTP jest podobny do:

PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01

Wybierz **treści** kartę i wklej dane json kod [wcześniej zdefiniowaną](#define-a-filter).

Wybierz pozycję **Wyślij**. 

Filtr zasób został utworzony.

Aby uzyskać szczegółowe informacje na temat Utwórz lub zaktualizuj zasób filtrów, zobacz [tworzenia lub aktualizacji](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Zobacz też [JSON Przykłady filtrów](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Kolejne kroki

[Stream filmów wideo](stream-files-tutorial-with-rest.md) 
