---
title: Omówienie przesyłania strumieniowego na żywo z Azure Media Services v3 | Microsoft Docs
description: Ten artykuł zawiera omówienie przesyłania strumieniowego na żywo przy użyciu Azure Media Services v3.
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
ms.date: 11/12/2019
ms.author: juliako
ms.openlocfilehash: af7bfe74d8df177650cea76a9bed7b0d7311f87e
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968999"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Przesyłanie strumieniowe na żywo z Azure Media Services v3

Azure Media Services pozwala na dostarczanie na żywo wydarzeń klientom w chmurze platformy Azure. Aby przesyłać strumieniowo zdarzenia na żywo za pomocą Media Services, potrzebne są następujące elementy:  

- Aparat, który jest używany do przechwytywania zdarzenia na żywo.<br/>Aby zapoznać się z pomysłami dotyczącymi konfiguracji, zapoznaj się z [konfiguracją prostego i przenośnego sprzętu wideo]( https://link.medium.com/KNTtiN6IeT).

    Jeśli nie masz dostępu do aparatu, narzędzia, takie jak [Wirecast](https://www.telestream.net/wirecast/overview.htm) , mogą służyć do generowania kanału informacyjnego na żywo z pliku wideo.
- Koder wideo na żywo, który konwertuje sygnały z aparatu (lub innego urządzenia, takiego jak laptop) do kanału informacyjnego, który jest wysyłany do Media Services. Kanał informacyjny udziału może obejmować sygnały związane z reklamą, takie jak znaczniki SCTE-35.<br/>Aby zapoznać się z listą zalecanych koderów przesyłania strumieniowego na żywo, zobacz [kodery przesyłania strumieniowego na żywo](recommended-on-premises-live-encoders.md). Zapoznaj się również z tym blogiem: [produkcja przesyłania strumieniowego na żywo z obs](https://link.medium.com/ttuwHpaJeT).
- Składniki w Media Services, które umożliwiają pozyskiwanie, przeglądanie, pakowanie, nagrywanie, szyfrowanie i emitowanie wydarzenia na żywo do klientów lub do usługi CDN w celu dalszej dystrybucji.

Ten artykuł zawiera omówienie i wskazówki dotyczące przesyłania strumieniowego na żywo z Media Services i linki do innych odpowiednich artykułów.
 
> [!NOTE]
> Za pomocą [Azure Portal](https://portal.azure.com/) można zarządzać [zdarzeniami na żywo](live-events-outputs-concept.md)v3, przeglądać [zasoby](assets-concept.md)v3 i uzyskać informacje o uzyskiwaniu dostępu do interfejsów API. W przypadku wszystkich innych zadań zarządzania (na przykład transformacji i zadań) należy użyć [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), interfejsu [wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging"></a>Pakowanie dynamiczne

Za pomocą Media Services można korzystać z funkcji [dynamicznego tworzenia pakietów](dynamic-packaging-overview.md), co pozwala na wyświetlanie podglądu i emitowanie strumieni na żywo w [FORMATACH MPEG, HLS i Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z kanału informacyjnego, który jest wysyłany do usługi. Osoby przeglądające mogą odtworzyć strumień na żywo za pomocą dowolnych HLS, ŁĄCZNIKów lub Smooth Streaming zgodnych graczy. Możesz użyć [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) w aplikacjach sieci Web lub aplikacji mobilnych, aby dostarczyć strumień do dowolnego z tych protokołów.

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

Szyfrowanie dynamiczne umożliwia dynamiczne szyfrowanie zawartości na żywo lub na żądanie za pomocą algorytmu AES-128 lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów. Aby uzyskać więcej informacji, zobacz [szyfrowanie dynamiczne](content-protection-overview.md).

> [!NOTE]
> Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="dynamic-manifest"></a>Manifest dynamiczny

Filtrowanie dynamiczne służy do kontrolowania liczby ścieżek, formatów, szybkości transmisji bitów i okien czasu prezentacji wysyłanych do graczy. Aby uzyskać więcej informacji, zobacz [filtry i manifesty dynamiczne](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Typy zdarzeń na żywo

[Wydarzenia na żywo](https://docs.microsoft.com/rest/api/media/liveevents) odpowiadają za pozyskiwanie i przetwarzanie strumieni wideo na żywo. Wydarzenie na żywo można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień o wielokrotnej szybkości transmisji bitów) lub *kodowanie na żywo* (lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów). Aby uzyskać szczegółowe informacje na temat przesyłania strumieniowego na żywo w Media Services v3, zobacz [zdarzenia na żywo i wyjście na żywo](live-events-outputs-concept.md).

### <a name="pass-through"></a>Przekazywanie

![Przekazywanie](./media/live-streaming/pass-through.svg)

Korzystając ze zdarzenia Pass-through **Live**, można polegać na lokalnym koderie na żywo w celu wygenerowania strumienia wideo o wielu szybkościach transmisji bitów i wysłania go jako źródła strumieniowego do zdarzenia na żywo (przy użyciu protokołu wejścia/wyjścia w formacie RTMP lub fragmentacji). Zdarzenie na żywo przeprowadzi następnie przez przychodzące strumienie wideo do Pakowarki dynamicznego (punkt końcowy przesyłania strumieniowego) bez konieczności dalszej transkodowania. Takie zdarzenie przekazywania na żywo jest zoptymalizowane pod kątem długotrwałych wydarzeń na żywo lub 24x365 liniowe przesyłanie strumieniowe na żywo. 

### <a name="live-encoding"></a>Kodowanie na żywo  

![Kodowanie na żywo](./media/live-streaming/live-encoding.svg)

W przypadku korzystania z kodowania w chmurze z Media Services należy skonfigurować lokalny koder na żywo, aby wysyłał wideo o pojedynczej szybkości transmisji bitów, jako źródło strumieniowe (do 32Mbps agregowania) do zdarzenia na żywo (przy użyciu protokołu wejścia/wyjścia w formacie RTMP lub fragmentacji). Zdarzenie na żywo transkoduje strumień przychodzącej pojedynczej szybkości transmisji bitów do [wielu strumieni wideo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) o różnej szybkości transmisji bitów w różnych rozdzielczościach w celu usprawnienia dostarczania i udostępnienie go do dostarczania urządzeniom do odtwarzania za pośrednictwem standardowych protokołów branżowych, takich jak MPEG-kreska, Apple http Live Streaming (HLS) i Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Transkrypcja dynamiczna (wersja zapoznawcza)

Transkrypcja dynamiczna to funkcja, której można używać w przypadku wydarzeń na żywo, które są przekazywane lub kodowane na żywo. Aby uzyskać więcej informacji, zobacz [transkrypcja dynamiczna](live-transcription.md). Gdy ta funkcja jest włączona, usługa korzysta z funkcji [zamiany mowy na tekst](../../cognitive-services/speech-service/speech-to-text.md) Cognitive Services, aby transkrypcja słowa mówione w przychodzącym audio do tekstu. Ten tekst jest następnie udostępniany do dostarczania wraz z wideo i dźwiękiem w protokołach MPEG-KRESKowych i HLS.

> [!NOTE]
> Obecnie transkrypcja na żywo jest dostępna jako funkcja w wersji zapoznawczej w regionie zachodnie stany USA 2.

## <a name="live-streaming-workflow"></a>Przepływ pracy przesyłania strumieniowego na żywo

Aby zrozumieć przepływ pracy przesyłania strumieniowego na żywo w Media Services v3, należy najpierw przejrzeć i zrozumieć następujące pojęcia: 

- [Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)
- [Wydarzenia i dane wyjściowe na żywo](live-events-outputs-concept.md)
- [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)

### <a name="general-steps"></a>Ogólne kroki

1. Na koncie Media Services upewnij się, że **punkt końcowy przesyłania strumieniowego** (Źródło) jest uruchomiony. 
2. Utwórz [wydarzenie na żywo](live-events-outputs-concept.md). <br/>Podczas tworzenia zdarzenia możesz określić, aby uruchomić je ponownie. Możesz też uruchomić zdarzenie, gdy wszystko jest gotowe do rozpoczęcia przesyłania strumieniowego.<br/> Gdy Autostart ma wartość true, zdarzenie na żywo zostanie uruchomione bezpośrednio po utworzeniu. Rozliczanie zaczyna się zaraz po rozpoczęciu uruchamiania zdarzenia na żywo. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia. Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Pobierz adresy URL pozyskiwania i skonfiguruj lokalny koder, aby użyć adresu URL do wysłania kanału informacyjnego udziału.<br/>Zobacz [zalecane kodery na żywo](recommended-on-premises-live-encoders.md).
4. Uzyskaj adres URL wersji zapoznawczej i użyj go do sprawdzenia, czy dane wejściowe kodera są faktycznie odbierane.
5. Utwórz nowy obiekt **zasobów** . 

    Wszystkie dane wyjściowe na żywo są skojarzone z zasobem, który jest używany do rejestrowania wideo w skojarzonym kontenerze usługi Azure Blob Storage. 
6. Utwórz na **żywo wyjście** i użyj utworzonej przez siebie nazwy zasobu, aby można było zarchiwizować strumień w elemencie zawartości.

    Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Po usunięciu danych wyjściowych na żywo nie jest usuwany podstawowy element zawartości i zawartość w elemencie zawartości.
7. Utwórz **lokalizator przesyłania strumieniowego** z [wbudowanymi typami zasad przesyłania strumieniowego](streaming-policy-concept.md).

    Aby opublikować dane wyjściowe na żywo, należy utworzyć lokalizator przesyłania strumieniowego dla skojarzonego elementu zawartości. 
8. Wyświetl listę ścieżek w **lokalizatorze przesyłania strumieniowego** , aby odzyskać adresy URL do użycia (są to deterministyczne).
9. Pobierz nazwę hosta dla **punktu końcowego przesyłania strumieniowego** (Źródło), z którego chcesz przesyłać strumieniowo.
10. Połącz adres URL z kroku 8 z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Jeśli chcesz zatrzymać wyświetlanie **wydarzenia na żywo** , musisz zatrzymać przesyłanie strumieniowe zdarzenia i usunąć **lokalizator przesyłania strumieniowego**.
12. Aby po zakończeniu strumieniowego przesyłania zdarzeń, wyczyścić udostępnione wcześniej zasoby, postępuj zgodnie z poniższą procedurą.

    * Zatrzymaj wypychanie strumienia z kodera.
    * Zatrzymaj wydarzenie na żywo. Po zatrzymaniu wydarzenia na żywo opłaty nie będą za nie naliczane. W razie potrzeby ponownego uruchomienia kanał będzie miał ten sam adres URL pozyskiwania, więc nie trzeba będzie ponownie konfigurować kodera.
    * Można zatrzymać punkt końcowy przesyłania strumieniowego, chyba że chcesz nadal udostępniać archiwum zdarzenia na żywo w formie przesyłania strumieniowego na żądanie. Jeśli wydarzenie na żywo będzie w stanie zatrzymanym, żadne opłaty nie będą naliczane.

Element zawartości, do której jest archiwizowany na żywo danych wyjściowych, automatycznie będzie zasobem na żądanie po usunięciu danych wyjściowych na żywo. Przed zatrzymaniem zdarzenia na żywo należy usunąć wszystkie dane wyjściowe na żywo. Można użyć opcjonalnej flagi [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) , aby automatycznie usuwać wyjścia na żywo po zatrzymaniu. 

> [!TIP]
> Zobacz [samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md), artykuł bada kod, który implementuje kroki opisane powyżej.

## <a name="other-important-articles"></a>Inne ważne artykuły

- [Zalecane kodery na żywo](recommended-on-premises-live-encoders.md)
- [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md)
- [Porównanie funkcji typów zdarzeń na żywo](live-event-types-comparison.md)
- [Stany i rozliczenia](live-event-states-billing.md)
- [Opóźnienie](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

* [Szybki start przesyłania strumieniowego na żywo] (live-events-wirecast-quickstart.md (
* [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
* [Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](migrate-from-v2-to-v3.md)
