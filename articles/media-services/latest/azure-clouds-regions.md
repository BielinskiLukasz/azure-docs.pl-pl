---
title: Chmury i regiony, w których jest dostępny Azure Media Services wersja 3
description: Ten artykuł zawiera informacje o chmurach i regionach platformy Azure, w których jest dostępny Azure Media Services wersja 3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 5/28/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: cefd366e29fe1c6f68ec38816247f78bb53cd6e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84196238"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Chmury i regiony, w których istnieje Azure Media Services v3

Azure Media Services V3 jest dostępny za pośrednictwem Azure Resource Manager manifestu na platformie Azure, Azure Government, Azure (Niemcy), Azure Chiny 21Vianet. Jednak nie wszystkie funkcje Media Services są dostępne we wszystkich chmurach platformy Azure. W tym dokumencie przedstawiono dostępność głównych składników Media Services w wersji 3.

## <a name="feature-availability-in-azure-clouds"></a>Dostępność funkcji w chmurach platformy Azure

| Cecha|Globalne regiony platformy Azure | Azure Government|Azure (Niemcy)|Azure w Chinach — 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure Event Grid](reacting-to-media-services-events.md) | Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Dostępne | Niedostępne | Niedostępne | Niedostępne |
| [StandardEncoderPreset](encoding-concept.md) | Dostępne | Dostępne | Dostępne | Dostępne |
| [LiveEvents](live-streaming-overview.md) | Dostępne | Dostępne | Dostępne | Dostępne |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Dostępne | Dostępne | Dostępne | Dostępne |

## <a name="feature-availability-in-preview"></a>Dostępność funkcji w wersji zapoznawczej

[LiveTranscription](live-transcription.md) jest dostępny w następujących regionach:

- Azja Południowo-Wschodnia
- Europa Zachodnia
- Europa Północna
- Wschodnie stany USA
- Środkowe stany USA
- Południowo-środkowe stany USA
- Zachodnie stany USA 2
- Brazylia Południowa

## <a name="regionsgeographieslocations"></a>Regiony/lokalizacje geograficzne/lokalizacje

[Regiony, w których jest wdrażana usługa Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Nazwa kodu regionu

Jeśli konieczne jest podanie parametru **Location** , należy podać nazwę kodu regionu jako wartość **lokalizacji** . Aby uzyskać nazwę kodu regionu, w którym znajduje się Twoje konto, a połączenie powinno być kierowane, możesz uruchomić następujący wiersz w [interfejsie wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Po uruchomieniu pokazanego powyżej wiersza otrzymujesz listę wszystkich regionów świadczenia usługi Azure. Przejdź do regionu platformy Azure, dla którego szukasz *DisplayName* , i użyj jego wartości *nazwy* dla parametru **Location** .

Na przykład dla regionu Azure zachodnie stany USA 2 (wyświetlone poniżej) dla parametru **Location** zostanie użyta wartość "westus2".

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Punkty końcowe  

Następujące punkty końcowe są ważne, aby wiedzieć, jak nawiązać połączenie z kontami Media Services z różnych krajowych chmur platformy Azure.

### <a name="global-azure"></a>Globalny platformę Azure

|Punkty końcowe||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` |
| Odbiorcy tokenu | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Punkty końcowe||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` |
| Odbiorcy tokenu | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure (Niemcy)

| Punkty końcowe ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Odbiorcy tokenu | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure w Chinach — 21Vianet

|Punkty końcowe||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Odbiorcy tokenu |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Zobacz także

* [Regiony świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Obszary geograficzne platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Następne kroki

[Media Services wersja 3 — Omówienie](media-services-overview.md)
