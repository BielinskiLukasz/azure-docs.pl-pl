---
title: Kodowanie elementu zawartości przy użyciu Media Encoder Standard w Azure Portal | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki kodowania elementu zawartości przy użyciu Media Encoder Standard w Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7c147d99eaabee70316521d1f2bdc41933162ab1
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542609"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Kodowanie elementu zawartości przy użyciu Media Encoder Standard w Azure Portal

> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Jednym z najpopularniejszych scenariuszy pracy z Azure Media Services jest dostarczanie do klientów przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów. Media Services obsługuje następujące technologie przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming i dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (KRESKa, nazywane również MPEG-KRESKa). Aby przygotować wideo do przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów, należy najpierw zakodować źródłowy plik wideo jako pliki o wysokiej szybkości transmisji bitów. Możesz użyć Media Encoder Standard do kodowania filmów wideo.  

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów. Dzięki pakietowi dynamicznemu można dostarczać pliki MP4 o różnych szybkościach transmisji bitów w HLS, Smooth Streaming i MPEG-KRESKa bez ponownego pakowania w tych formatach. W przypadku korzystania z dynamicznego tworzenia pakietów można przechowywać i wpłacić pliki w formacie pojedynczego magazynu. Media Services kompiluje i obsługuje odpowiednią odpowiedź na podstawie żądania klienta.

Aby skorzystać z dynamicznego tworzenia pakietów, musisz zakodować swoje pliki źródłowe jako zestaw plików MP4 o różnych szybkościach transmisji bitów. Kroki kodowania przedstawiono w dalszej części tego artykułu.

Aby dowiedzieć się, jak skalować przetwarzanie multimediów, zobacz [Skalowanie przetwarzania multimediów przy użyciu Azure Portal](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Koduj w Azure Portal

Aby zakodować zawartość przy użyciu Media Encoder Standard:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz kolejno pozycje **Ustawienia** > **Elementy zawartości**. Wybierz element zawartości, który chcesz zakodować.
3. Wybierz przycisk **Koduj**.
4. W okienku **Kodowanie elementu zawartości** wybierz procesor **Media Encoder Standard** i ustawienia wstępne. Aby uzyskać informacje o ustawieniach wstępnych, zobacz [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Automatyczne generowanie drabiny szybkości transmisji bitów) i [Task presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Ustawienia wstępne zadań procesora Media Encoder Standard). Ważne jest, aby wybrać ustawienia wstępne, które będą najlepiej działać dla wejściowego pliku wideo. Na przykład: jeśli wejściowy plik wideo ma rozdzielczość 1920 &#215; 1080 pikseli, można użyć ustawienia wstępnego **Wielokrotna szybkość transmisji bitów H264 1080p**. Jeśli masz wideo w niskiej rozdzielczości (640 &#215; 360), ustawienie wstępne **Wielokrotna szybkość transmisji bitów H264 1080p** nie powinno być używane.
   
   Aby ułatwić sobie zarządzanie zasobami, możesz poddać edycji nazwę wyjściowego elementu zawartości i nazwę zadania.
   
   ![Kodowanie elementów zawartości](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Wybierz pozycję **Utwórz**.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
* [Monitoruj postęp zadania kodowania](media-services-portal-check-job-progress.md) w Azure Portal.  

