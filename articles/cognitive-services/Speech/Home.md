---
title: Usługa rozpoznawania mowy Bing firmy Microsoft | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu API mowy usługi Microsoft, aby dodać głosem do aplikacji, w tym w czasie rzeczywistym interakcji z użytkownikami.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 3b4ab55f4bd8bbd66f2edc8c262524169c79d7f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975640"
---
# <a name="what-is-bing-speech"></a>Co to jest rozpoznawanie mowy Bing?

API rozpoznawania mowy Bing firmy Microsoft oparte na chmurze oferuje deweloperom łatwy sposób tworzyć zaawansowane funkcje obsługujące rozpoznawanie mowy w swoich aplikacjach, takich jak formant polecenia głosowe, użytkownika okna dialogowego, używając konwersacji naturalnych mowy i transkrypcja mowy i dyktowania. Interfejs API mowy firmy Microsoft obsługuje zarówno *zamiana mowy na tekst* i *zamiany tekstu na mowę* konwersji.

- **Zamiana mowy na tekst** interfejs API konwertuje ludzkiej mowy na tekst, który może służyć jako dane wejściowe i polecenia do kontrolowania aplikacji.
- **Zamiana tekstu na mowę** interfejs API konwertuje tekst do strumieni audio, które może zostać odtworzony użytkownikowi aplikacji.

> [!NOTE] 
> W maju 2018 roku opublikowaliśmy nową [usługa rozpoznawania mowy](../speech-service/overview.md) w publicznej wersji zapoznawczej. Firma Microsoft zachęca do [za darmo wypróbować](../speech-service/get-started.md).

## <a name="speech-to-text-speech-recognition"></a>Zamiana mowy na tekst (rozpoznawanie mowy)

Interfejs API rozpoznawania mowy Microsoft *transcribes* strumieni audio na tekst, który aplikacji można wyświetlić użytkownikowi lub działać jako polecenia dane wejściowe. Udostępnia dwie metody dla deweloperów dodać mowy do aplikacji: interfejsy API REST **lub** bibliotek klienckich opartych na protokołu Websocket.

- [Interfejsów API REST](GetStarted/GetStartedREST.md): deweloperzy mogą używać połączeń HTTP z ich aplikacji do usługi rozpoznawania mowy.
- [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md): zaawansowane funkcje, deweloperzy mogą pobrać bibliotek klienta Microsoft Speech i link do ich aplikacji.  Biblioteki klienckie są dostępne na różnych platformach (Windows, Android, iOS) przy użyciu różnych języków, (C#, Java, JavaScript, ObjectiveC). W przeciwieństwie do interfejsów API REST bibliotek klienckich korzystanie z protokołu opartego na protokole Websocket.

| Przypadki zastosowań | [Interfejsy API REST](GetStarted/GetStartedREST.md) | [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konwertuj krótko wypowiedzi audio, na przykład poleceń (audio długość < 15 s) bez wyników pośrednich | Yes | Yes |
| Konwertuj długie audio (> 15 s) | Nie | Yes |
| Dźwięk Stream z wyników pośrednich żądanego | Nie | Yes |
| Zrozumienie tekstu skonwertowaniu dźwięku za pomocą usługi LUIS | Nie | Yes |

Niezależnie od deweloperów podejście wybierz (interfejsy API REST lub bibliotek klienckich), usługa Microsoft speech service obsługuje następujące:

- Zaawansowanych technologii rozpoznawania mowy, od firmy Microsoft, używane przez Cortany, dyktowania pakietu Office, pakietu Office w usłudze Translator i innych produktów firmy Microsoft.
- W czasie rzeczywistym rozpoznawania ciągłe. Interfejs API rozpoznawania mowy umożliwia użytkownikom wykonać transkrypcji audio na tekst w czasie rzeczywistym i obsługuje do odbierania wyników pośrednich wyrazy, które zostały uznane do tej pory. Usługa rozpoznawania mowy obsługuje również wykrywanie zakończenia mowy. Ponadto użytkownicy mogą wybrać dodatkowe możliwości formatowania, takich jak wielkość liter i znaków interpunkcyjnych, maskowanie wulgaryzmów i normalizacji tekstu.
- Obsługuje zoptymalizowane pod kątem wyników rozpoznawania mowy *interaktywne*, *konwersacji*, i *dyktowanie* scenariuszy. Dla scenariuszy użytkownika, które wymagają modeli języków niestandardowych i modeli akustycznych [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) można tworzyć modele mowy dopasowane dostosowane do aplikacji i użytkowników.
- Obsługuje wiele języków, w wielu dialekty. Aby uzyskać pełną listę obsługiwanych języków w trybie rozpoznawania, zobacz [języków rozpoznawania](api-reference-rest/supportedlanguages.md).
- Integracja z usługą interpretacji języka. Oprócz konwertowanie dane wejściowe audio na tekst, *zamiana mowy na tekst* zapewnia dodatkowe możliwości, aby zrozumieć, co oznacza tekst w aplikacji. Używa ona [Language Understanding Intelligent Service(LUIS)](../LUIS/what-is-luis.md) można wyodrębnić intencje i podmioty z rozpoznany tekst.

### <a name="next-steps"></a>Kolejne kroki

- Wprowadzenie do użycia usługi rozpoznawania mowy firmy Microsoft przy użyciu [interfejsów API REST](GetStarted/GetStartedREST.md) lub [biblioteki klienckie](GetStarted/GetStartedClientLibraries.md).
- Zapoznaj się z [przykładowe aplikacje](samples.md) w preferowanym przez Ciebie języku programowania.
- Przejdź do sekcji odwołania, aby znaleźć [protokołu rozpoznawania mowy firmy Microsoft](API-Reference-REST/websocketprotocol.md) szczegółowe informacje i dokumentację interfejsu API.

## <a name="text-to-speech-speech-synthesis"></a>Zamiana tekstu na mowę (synteza mowy)

*Zamiana tekstu na mowę* interfejsów API przy użyciu architektury REST do konwertowania tekstu ze strukturą do strumienia audio. Interfejsy API zapewniają konwersja szybka tekstu na mowę w różnych językach i głosów. Ponadto użytkownicy mają również możliwość zmiany audio cechy, takie jak wymowa, woluminu, gęstość itp. za pomocą SSML tagi.

### <a name="next-steps"></a>Kolejne kroki

- Wprowadzenie do używania usługi zamiany tekstu na mowę firmy Microsoft: [tekstu, aby dokumentacja interfejsu API rozpoznawania mowy](api-reference-rest/bingvoiceoutput.md). Aby uzyskać pełną listę języków i głosy są obsługiwane przez zamiany tekstu na mowę, zobacz [obsługiwanych ustawień regionalnych i czcionki głosowe](api-reference-rest/bingvoiceoutput.md#SupLocales).
