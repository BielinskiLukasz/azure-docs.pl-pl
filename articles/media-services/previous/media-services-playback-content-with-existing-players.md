---
title: Korzystanie z istniejących odtwarzaczy do odtwarzania zawartości — Azure | Microsoft Docs
description: Ten artykuł zawiera listę istniejących odtwarzaczy, których można użyć do odtwarzania zawartości.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2d3c22e17c37bc46c16a9cc80eb3cf4b9ec93ecf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686922"
---
# <a name="playing-your-content-with-existing-players"></a>Odtwarzanie zawartości w istniejących odtwarzaczach
Azure Media Services obsługuje wiele popularnych formatów przesyłania strumieniowego, takich jak Smooth Streaming, HTTP Live Streaming i MPEG-kreska. Ten temat prowadzi do istniejących graczy, których można użyć do testowania strumieni.

### <a name="the-azure-portal-media-services-content-player"></a>Azure Portal Media Services odtwarzacz zawartości
Witryna **Azure** Portal udostępnia odtwarzacz zawartości, którego można użyć do testowania wideo.

Kliknij żądany film wideo (Upewnij się, że został [opublikowany](media-services-portal-publish.md)), a następnie kliknij przycisk **Odtwórz** w dolnej części portalu.

Zagadnienia do rozważenia:

* **Odtwarzacz zawartości MEDIA SERVICES** odtwarza z domyślnego punktu końcowego przesyłania strumieniowego. Do odtwarzania z punktu końcowego przesyłania strumieniowego innego niż domyślny należy użyć innego odtwarzacza Na przykład [Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

Użyj [Azure Media Player](https://aka.ms/azuremediaplayer) do odtwarzania zawartości (wyczyść lub chroniona) w jednym z następujących formatów:

* Smooth Streaming
* MPEG DASH
* HLS
* Pliki MP4 progresywne

### <a name="flash-player"></a>Program Flash Player

#### <a name="playready-with-token"></a>PlayReady z tokenem

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Odtwarzacze PAUZ

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Inne
Aby przetestować adresy URL HLS, można również użyć:

* **Przeglądarka Safari** na urządzeniu z systemem iOS lub
* **3ivx HLS Player** w systemie Windows.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
