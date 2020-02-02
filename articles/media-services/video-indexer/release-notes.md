---
title: Informacje o wersji Azure Media Services Video Indexer | Microsoft Docs
description: Aby zachować aktualność najnowszych zmian, ten artykuł zawiera najnowsze aktualizacje Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 01/07/2020
ms.author: juliako
ms.openlocfilehash: f1387273f9736fea70682177d5d48dc2f141bbad
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933856"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Informacje o wersji Azure Media Services Video Indexer

>Otrzymuj powiadomienia o tym, kiedy ponownie odwiedzasz Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` do czytnika kanału informacyjnego RSS.

Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

* Zainstalowane najnowsze wersje
* Znane problemy
* Poprawki błędów
* Przestarzałe funkcje

## <a name="january-2020"></a>Styczeń 2020 r.
 
### <a name="custom-language-support-for-additional-languages"></a>Obsługa języków niestandardowych w dodatkowych językach

Video Indexer teraz obsługuje niestandardowe modele języka dla `ar-SY`, `en-UK`i `en-AU` (tylko interfejsy API).
 
### <a name="delete-account-timeframe-action-update"></a>Usuń aktualizację przedziału czasu konta

Akcja Usuń konto teraz usuwa konto w ciągu 90 dni, a nie 48 godzin.
 
### <a name="new-video-indexer-github-repository"></a>Nowe repozytorium usługi GitHub Video Indexer

Dostępna jest nowa Video Indexer GitHub z różnymi projektami, przewodniki z wprowadzeniem i przykłady kodu: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Aktualizacja struktury Swagger

Video Indexer ujednolicone **uwierzytelnianie** i **operacje** w ramach jednej [specyfikacji Video Indexer openapi (Swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). Develpers mogą znaleźć interfejsy API w [portalu dla deweloperów Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="update-transcript-with-the-new-api"></a>Aktualizowanie transkrypcji przy użyciu nowego interfejsu API

Zaktualizuj określoną sekcję w transkrypcji przy użyciu interfejsu API [Update-Video-index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) .

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Napraw konfigurację konta z poziomu portalu Video Indexer

Teraz możesz aktualizować Media Services konfigurację połączenia, aby samoobsługowo rozwiązywać problemy, takie jak: 

* Nieprawidłowy zasób Azure Media Services
* zmiany hasła
* Media Services zasoby zostały przeniesione między subskrypcjami  

Aby naprawić konfigurację konta, w portalu Video Indexer przejdź do Ustawienia Karta konto > (jako właściciel).

### <a name="configure-the-custom-vision-account"></a>Skonfiguruj niestandardowe konto wizji

Skonfiguruj niestandardowe konto wizji na płatnych kontach przy użyciu portalu Video Indexer (wcześniej było to obsługiwane tylko przez interfejs API). W tym celu zaloguj się do portalu Video Indexer, wybierz pozycję dostosowanie modelu > animowane znaki > Skonfiguruj. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Sceny, zrzuty i klatki kluczowe — teraz w jednym okienku szczegółowych informacji

Sceny, zrzuty i ramki kluczowe są teraz scalane w jeden wgląd w celu łatwiejszego użycia i nawigacji. Po wybraniu żądanej sceny można zobaczyć, jakie zrzuty i klatki kluczowe zawiera. 

### <a name="notification-about-a-long-video-name"></a>Powiadomienie dotyczące długiej nazwy wideo

Jeśli nazwa filmu wideo jest dłuższa niż 80 znaków, Video Indexer pokazuje opisowy błąd podczas przekazywania.

### <a name="streaming-endpoint-is-disabled-notification"></a>Powiadomienie o punkcie końcowym przesyłania strumieniowego jest wyłączone

Gdy punkt końcowy przesyłania strumieniowego jest wyłączony, Video Indexer wyświetli opisowy błąd na stronie odtwarzacza.

### <a name="error-handling-improvement"></a>Poprawa obsługi błędów

Kod stanu 409 będzie teraz zwracany przez [ponowne indeksowanie wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video? https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) i aktualizowanie interfejsów API [indeksu wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) w przypadku, gdy wideo jest aktywnie indeksowane, aby zapobiec zastępowaniu bieżących zmian w indeksie.

## <a name="november-2019"></a>Listopad 2019 r.
 
* Obsługa koreańskich niestandardowych modeli języków

    Indeksator wideo obsługuje teraz niestandardowe modele językowe w koreański (`ko-KR`) zarówno w interfejsie API, jak i w portalu. 
* Nowe języki obsługiwane w przypadku zamiany mowy na tekst (monitora STT)

    Interfejsy API Video Indexer obsługują teraz monitora STT w języku arabskim Levantine (AR-SY), dialekt angielskiej Brytanii (en-GB) i angielski dialekt australijski (en-AU).
    
    W przypadku przekazywania wideo zamieniono polecenie zh-HANS na zh-CN, obie są obsługiwane, ale zaleca się, aby Metoda zh-CN była zalecana i bardziej dokładna.
    
## <a name="october-2019"></a>Październik 2019 r.
 
* Wyszukaj animowane znaki w galerii

    W przypadku indeksowania animowanych znaków można teraz wyszukiwać je w postaci szpaltowej wideo na koncie. Aby uzyskać więcej informacji, zobacz [rozpoznawanie znaków animowanych](animated-characters-recognition.md).

## <a name="september-2019"></a>Wrzesień 2019 r.
 
Wiele zaliczek zapowiedziano w IBC 2019:
 
* Rozpoznawanie znaków animowanych (publiczna wersja zapoznawcza)

    Możliwość wykrywania grup AD rozpoznaje znaki w animowanej zawartości, poprzez integrację z niestandardową wizją. Aby uzyskać więcej informacji, zobacz [wykrywanie znaków animowanych](animated-characters-recognition.md).
* Identyfikacja w wielu językach (publiczna wersja zapoznawcza)

    Wykrywaj segmenty w wielu językach w ścieżce audio i twórz na ich podstawie wielojęzyczne transkrypcje. Wstępna pomoc techniczna: angielski, hiszpański, niemiecki i francuski. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrypcja zawartości w wielu językach](multi-language-identification-transcription.md).
* Wyodrębnianie jednostek nazwanych dla osób i lokalizacji

    Wyodrębnianie marek, lokalizacji i osób z mowy i tekstu wizualnego za pośrednictwem przetwarzania języka naturalnego (NLP).
* Klasyfikacja typu zrzutu redakcyjnego

    Tagowanie zrzutów z typami redakcyjnymi, takimi jak zamknięcie, średnie zrzuty, dwa zrzuty, wewnętrzne, zewnętrzne itd. Aby uzyskać więcej informacji, zobacz [wykrywanie typów zrzutów redakcyjnych](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Temat inferencing — teraz obejmujący poziom 2
    
    Temat inferencing model obsługuje teraz bardziej szczegółowy stopień szczegółowości taksonomii IPTC. Przeczytaj wszystkie szczegółowe informacje o [Azure Media Services nowych innowacyjności opartych na AI](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Sierpień 2019 r.
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer wdrożony w programie Południowe Zjednoczone Królestwo

Teraz możesz utworzyć konto płatne Video Indexer w regionie Południowo-południe.

### <a name="new-editorial-shot-type-insights-available"></a>Dostępne są nowe szczegółowe informacje o typie zrzutu redakcyjnego

Nowe Tagi dodawane do zrzutów wideo udostępniają redakcyjne "typy zrzutów", aby identyfikować je za pomocą wspólnych fraz redakcyjnych używanych w przepływie pracy tworzenia zawartości, takich jak: Extreme Closeup, Closeup, Wide, medium, dwa zrzuty, na zewnątrz, w lewo i w prawo (dostępne w JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Dostępne są nowe osoby i lokalizacje

Video Indexer identyfikuje nazwane lokalizacje i osoby za pośrednictwem przetwarzania języka naturalnego (NLP) na podstawie OCR i transkrypcji wideo. Video Indexer używa algorytmu uczenia maszynowego do rozpoznawania, gdy określone lokalizacje (na przykład Eiffel Tower) lub osoby (na przykład Jan Nowak) są wywoływane w filmie wideo.

### <a name="keyframes-extraction-in-native-resolution"></a>Wyodrębnianie klatek kluczowych w rozdzielczości natywnej

Ramki kluczowe wyodrębnione przez Video Indexer są dostępne w oryginalnej rozdzielczości wideo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Zaplanowanie szkolenia niestandardowych modeli czołowych z obrazów

Uczenie się zdjęć z obrazów przenoszonych z trybu podglądu do GA (dostępne za pośrednictwem interfejsu API i portalu).

> [!NOTE]
> Nie ma wpływu na ceny związane z przejściem "wersja zapoznawcza do wersji zapoznawczej".

### <a name="hide-gallery-toggle-option"></a>Ukryj opcję przełączania galerii

Użytkownik może wybrać opcję ukrycia karty Galeria w portalu (podobnie jak w przypadku ukrycia karty przykłady).
 
### <a name="maximum-url-size-increased"></a>Zwiększono maksymalny rozmiar adresu URL

Obsługa ciągu zapytania URL o 4096 (zamiast 2048) na indeksowaniu wideo.
 
### <a name="support-for-multi-lingual-projects"></a>Obsługa projektów wielojęzycznych

Projekty można teraz tworzyć w oparciu o wideo indeksowane w różnych językach (tylko interfejsy API).

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="editor-as-a-widget"></a>Edytor jako widżet

Edytor Video Indexer AI jest teraz dostępny jako widżet, który ma zostać osadzony w aplikacjach klienta.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualizowanie niestandardowego modelu języka z pliku napisów w portalu

Klienci mogą udostępniać formaty plików VTT, narzędzia SRT i TTML jako dane wejściowe dla modeli języka na stronie dostosowywania portalu.

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer wdrożone na Japonia Wschodnia

Teraz można utworzyć konto płatne Video Indexer w regionie Japonia Wschodnia.

### <a name="create-and-repair-account-api-preview"></a>Tworzenie i naprawa interfejsu API konta (wersja zapoznawcza)

Dodano nowy interfejs API, który umożliwia [zaktualizowanie punktu końcowego lub klucza połączenia usługi Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Ulepsz obsługę błędów podczas przekazywania 

Komunikat opisowy jest zwracany w przypadku nieprawidłowych konfiguracji bazowego konta Azure Media Services.

### <a name="player-timeline-keyframes-preview"></a>Klatki kluczowe programu Player — wersja zapoznawcza 

Możesz teraz zobaczyć podgląd obrazu za każdym razem na osi czasu odtwarzacza.

### <a name="editor-semi-select"></a>Wybór częściowo edytora

Teraz można wyświetlić podgląd wszystkich szczegółowych informacji, które są wybrane w wyniku wybrania konkretnego przedziału czasu wglądu w edytorze.

## <a name="may-2019"></a>Maj 2019 r.

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizowanie niestandardowego modelu języka z pliku napisów

[Tworzenie niestandardowego modelu języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) i [Aktualizowanie niestandardowych modeli języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) interfejsy API obsługują teraz formaty plików VTT, narzędzia SRT i ttml jako dane wejściowe dla modeli języka.

Podczas wywoływania [interfejsu API aktualizowania transkrypcji wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)transkrypcja jest dodawana automatycznie. Model szkoleniowy skojarzony z filmem wideo jest również aktualizowany automatycznie. Aby uzyskać informacje na temat dostosowywania i uczenia modeli języka, zobacz [Dostosowywanie modelu języka za pomocą Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nowe formaty pobierania transkrypcji — TXT i CSV

Oprócz obsługiwanego formatu napisów (narzędzia SRT, VTT i TTML), Video Indexer teraz obsługuje pobieranie transkrypcji w formatach TXT i CSV.

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)
