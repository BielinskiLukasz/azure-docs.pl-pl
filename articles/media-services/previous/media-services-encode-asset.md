---
title: Przegląd i porównanie platformy Azure na żądanie koderów multimediów | Dokumentacja firmy Microsoft
description: Ten temat zawiera przegląd i porównanie platformy Azure na żądanie koderów multimediów.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a1b6fe38087975838d99ca4fe45ee0d0638c56c3
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58187748"
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Przegląd i porównanie platformy Azure na żądanie koderów multimediów 

## <a name="encoding-overview"></a>Omówienie kodowania
Usługa Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze.

Gdy zaczynasz pracę z usługą Media Services, należy zrozumieć różnicę między formatami koderów-dekoderów i plików.
Kodery-dekodery to oprogramowanie, które implementuje algorytmy kompresji i dekompresji formaty plików są kontenery zawierające skompresowane wideo.

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać zawartość z adaptacyjną szybkością transmisji bitów w formacie MP4 lub Smooth Streaming, kodowane w formatach transmisji strumieniowej obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatów przesyłania strumieniowego.

> [!NOTE]
> Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Usługa Media Services obsługuje następujące czynności na koderów na żądanie, które są opisane w tym artykule:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Przepływ pracy usługi Media Encoder w warstwie Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Ten artykuł zawiera krótkie omówienie na żądanie koderów multimediów i zawiera linki do artykułów, które zapewniają więcej szczegółowych informacji. Temat ten zawiera również porównanie koderów.

>[!NOTE]
>Domyślnie każde konto usługi Media Services może mieć jedno aktywne zadanie kodowania w danym momencie. Możesz zarezerwować jednostek kodowania, które umożliwiają posiadanie wielu zadań kodowania, działających jednocześnie, jeden dla każdego kodowania zastrzeżona jednostka, jaką można nabyć. Aby uzyskać informacje, zobacz [skalowania jednostek kodowania](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Usługa Media Encoder Standard
### <a name="how-to-use"></a>Jak stosować
[Jak kodowanie za pomocą usługi Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formaty
[Formaty i kodery-dekodery](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Ustawienia wstępne
Usługi Media Encoder Standard jest skonfigurowany przy użyciu jednej wstępne kodera opisem [tutaj](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadane wejściowe i wyjściowe
Opisano metadanych wejściowych koderów [tutaj](media-services-input-metadata-schema.md).

Opisano metadanych danych wyjściowych koderów [tutaj](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generowanie miniatur
Aby uzyskać informacje, zobacz [generowanie miniatur przy użyciu usługi Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Przytnij wideo (wycinka)
Aby uzyskać informacje, zobacz [jak przyciąć filmów wideo przy użyciu usługi Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Tworzenie nakładek
Aby uzyskać informacje, zobacz [Tworzenie nakładek za pomocą usługi Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Zobacz także
[Blog usługi Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Przepływ pracy usługi Media Encoder w warstwie Premium
### <a name="overview"></a>Przegląd
[Wprowadzenie do kodowania w usłudze Azure Media Services w warstwie Premium](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Jak stosować
Media Encoder Premium Workflow jest skonfigurowany przy użyciu złożonych przepływów pracy. Pliki przepływu pracy może zostać utworzony i zaktualizowany przy użyciu [projektanta przepływów pracy](media-services-workflow-designer.md) narzędzia.

[Jak używać kodowania w usłudze Azure Media Services w warstwie Premium](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Znane problemy
Jeśli wejściowy plik wideo zawiera napisy kodowane dane wyjściowe zasobów będzie nadal zawierać pusty plik TTML.


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywania zaawansowanych zadań kodowania, dostosowywanie ustawień wstępnych usługi Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
