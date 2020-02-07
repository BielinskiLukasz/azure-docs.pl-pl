---
title: Informacje o wersji Azure Media Services v3 | Microsoft Docs
description: Aby zachować aktualność wraz z najnowszymi zmianami, ten artykuł zawiera najnowsze aktualizacje na Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 12/13/2019
ms.author: juliako
ms.openlocfilehash: b3d5e1f814a8eb083ab01623051f1b5b3723a9f1
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049624"
---
# <a name="azure-media-services-v3-release-notes"></a>Informacje o wersji Azure Media Services v3

>Otrzymuj powiadomienia o tym, kiedy ponownie odwiedzasz Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` do czytnika kanału informacyjnego RSS.

Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

* Zainstalowane najnowsze wersje
* Znane problemy
* Poprawki błędów
* Przestarzałe funkcje

## <a name="known-issues"></a>Znane problemy

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-sdk), interfejsu wiersza polecenia lub jednego z obsługiwanych zestawów SDK.

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="improvements-in-media-processors"></a>Ulepszenia procesorów multimediów

- Ulepszona obsługa źródeł z przeplotem w analizie wideo — taka zawartość jest teraz niewłaściwie odtworzona przed wysłaniem do aparatów wnioskowania.
- Podczas generowania miniatur z trybem "Najlepsza" koder wyszukuje teraz więcej niż 30 sekund, aby wybrać ramkę, która nie jest funkcją unchromatyczną.
 
## <a name="november-2019"></a>Listopad 2019 r.

### <a name="live-transcription-preview"></a>Podgląd transkrypcji na żywo

Transkrypcja na żywo jest teraz dostępna w publicznej wersji zapoznawczej i można jej używać w regionie zachodnie stany USA 2.

Transkrypcja dynamiczna została zaprojektowana tak, aby działała w połączeniu ze zdarzeniami na żywo jako funkcją dodatkową.  Jest on obsługiwany zarówno na żywo, jak i w standardowym i w warstwie Premium.  Gdy ta funkcja jest włączona, usługa korzysta z funkcji [zamiany mowy na tekst](../../cognitive-services/speech-service/speech-to-text.md) Cognitive Services, aby transkrypcja słowa mówione w przychodzącym audio do tekstu. Ten tekst jest następnie udostępniany do dostarczania wraz z wideo i dźwiękiem w protokołach MPEG-KRESKowych i HLS. Opłaty są naliczane na podstawie nowego miernika dodatku, który jest dodatkowym kosztem zdarzenia na żywo, gdy jest on w stanie "uruchomiona".  Aby uzyskać szczegółowe informacje na temat transkrypcji i rozliczeń na żywo, zobacz [transkrypcja dynamiczna](live-transcription.md)

> [!NOTE]
> Obecnie transkrypcja na żywo jest dostępna tylko jako funkcja w wersji zapoznawczej w regionie zachodnie stany USA 2. Obsługuje transkrypcję wyrazów mówionych w języku angielskim (EN-US) tylko w tym czasie.

### <a name="content-protection"></a>Ochrona zawartości

Funkcja *zapobiegania powtarzaniu tokenów* wydana w ograniczonych regionach z powrotem we wrześniu jest teraz dostępna we wszystkich regionach.
Media Services klienci mogą teraz ustawić limit liczby przypadków użycia tego samego tokenu do żądania klucza lub licencji. Aby uzyskać więcej informacji, zobacz [Zapobieganie powtarzaniu tokenu](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Nowi Polecani partnerzy na żywo

Dodano obsługę następujących nowych zalecanych koderów partnerów dla przesyłania strumieniowego RTMP na żywo:

- [Cambria na żywo 4,3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 i Maksymalna liczba kamer funkcjonalnych](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Ulepszenia kodowania plików

- Dostępne jest nowe ustawienie wstępne kodowania z obsługą zawartości. Tworzy zestaw pliki MP4 wyrównanych grupę GOP przy użyciu kodowania obsługującego zawartość. Mając daną zawartość wejściową, usługa wykonuje początkową analizę uproszczoną zawartości wejściowej. Używa tych wyników do określenia optymalnej liczby warstw, odpowiedniej szybkości transmisji bitów i ustawień rozdzielczości do dostarczenia przez adaptacyjne przesyłanie strumieniowe. To ustawienie wstępne jest szczególnie przydatne w przypadku wideo o niskiej złożoności i średniej złożoności, w przypadku których pliki wyjściowe są z niższymi szybkościami transmisji bitów, ale z jakością, która nadal zapewnia dobre doświadczenie dla osób przeglądających. Dane wyjściowe będą zawierać pliki MP4 z przeplotem wideo i audio. Aby uzyskać więcej informacji, zobacz informacje o [otwartych interfejsach API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Ulepszona wydajność i wielowątkowość dla ponownego rozmiaru w Media Encoder Standard. W określonych warunkach klient powinien zobaczyć zwiększenie wydajności między 5-40% kodowanie VOD. Zawartość o niskiej złożoności zakodowana na wiele szybkości transmisji bitów będzie mieć największy wzrost wydajności. 
- Kodowanie standardowe teraz zachowuje zwykłe grupę GOP erze dla zawartości zmiennej współczynnika klatek (VFR) podczas kodowania VOD przy użyciu ustawienia grupę GOP opartego na czasie.  Oznacza to, że klient przesyłający zawartość współczynnika klatek mieszanych, która różni się od 15-30 fps na przykład, powinien teraz widzieć regularne grupę GOPe odległości obliczone na podstawie danych wyjściowych do adaptacyjnego przesyłania strumieniowego plików MP4. Poprawi to możliwość bezproblemowego przełączania się między ścieżkami podczas dostarczania na HLS lub PAUZy. 
-  Ulepszona synchronizacja AV dla zawartości źródłowej współczynnika klatek (VFR)

### <a name="video-indexer-video-analytics"></a>Video Indexer, analiza wideo

- Ramki kluczowe wyodrębnione przy użyciu ustawień wstępnych VideoAnalyzer są teraz w oryginalnej rozdzielczości wideo zamiast zmiany rozmiaru. Funkcja wyodrębniania klatek kluczowych o wysokiej rozdzielczości zapewnia oryginalne obrazy z jakością i pozwala korzystać z sztucznych modeli analizy opartych na obrazach udostępnianych przez usługi Microsoft przetwarzanie obrazów i Custom Vision, aby uzyskać jeszcze więcej szczegółowych informacji na temat wideo.

## <a name="september-2019"></a>Wrzesień 2019 r.

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Kodowanie liniowe na żywo zdarzeń na żywo

Media Services v3 zapowiedźuje Podgląd 24 godzin x 365 dni aktywnego kodowania zdarzeń na żywo.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Wycofanie procesorów multimediów

Ogłaszamy przestarzałe *Azure Media Indexer* i *Azure Media Indexer 2 wersji zapoznawczej*. Aby uzyskać daty wycofania, zobacz temat [starsze składniki](../previous/legacy-components.md) . [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje te starsze procesory nośników.

Aby uzyskać więcej informacji, zobacz [Migrowanie z Azure Media Indexer i Azure Media Indexer 2 do Azure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Sierpień 2019 r.

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Para regionalna w Republice Południowej Afryki jest otwarta dla Media Services 

Media Services jest teraz dostępna w regionach Północna Republika Południowej Afryki i Zachodnia Republika Południowej Afryki.

Aby uzyskać więcej informacji, zobacz [chmury i regiony, w których istnieje Media Services v3](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Wycofanie procesorów multimediów

Ogłaszamy przestarzałe procesory nośników z *systemami Windows Azure Media Encoder* (WAME) i *Azure Media Encoder* (azwa), które są wycofywane. Aby uzyskać daty wycofania, zobacz temat ten [starszy składnik](../previous/legacy-components.md) .

Aby uzyskać szczegółowe informacje, zobacz [Migrowanie WAME do Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) i [Migrowanie azwa do Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="content-protection"></a>Ochrona zawartości

W przypadku przesyłania strumieniowego zawartości chronionej przy użyciu ograniczenia tokenu użytkownicy końcowi muszą uzyskać token, który jest wysyłany w ramach żądania dostarczenia klucza. Funkcja *zapobiegania powtarzaniu tokenów* umożliwia Media Services klientom Ustawianie limitu, ile razy można użyć tego samego tokenu do żądania klucza lub licencji. Aby uzyskać więcej informacji, zobacz [Zapobieganie powtarzaniu tokenu](content-protection-overview.md#token-replay-prevention).

Od lipca funkcja wersji zapoznawczej była dostępna tylko w regionach Środkowe stany USA i zachodnie stany USA.

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="video-subclipping"></a>Podcinanie wideo

Teraz można przyciąć lub podciąć klip wideo przy kodowaniu przy użyciu [zadania](https://docs.microsoft.com/rest/api/media/jobs). 

Ta funkcja działa z dowolnym [przekształceniem](https://docs.microsoft.com/rest/api/media/transforms) utworzonym przy użyciu ustawień wstępnych [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) lub predefiniowanych ustawień [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) . 

Zobacz przykłady:

* [Podcinanie wideo za pomocą platformy .NET](subclip-video-dotnet-howto.md)
* [Podcinanie wideo za pomocą interfejsu REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maj 2019 r.

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Obsługa Azure Monitor Media Services dzienników diagnostycznych i metryk

Teraz można używać Azure Monitor do wyświetlania danych telemetrycznych emitowanych przez Media Services.

* Użyj dzienników diagnostycznych Azure Monitor, aby monitorować żądania wysyłane przez Media Services punkt końcowy dostarczania klucza. 
* Monitoruj metryki emitowane przez Media Services [punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md).   

Aby uzyskać szczegółowe informacje, zobacz [monitorowanie metryk Media Services i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Obsługa połączeń z obsługą wiele dźwięków w dynamicznym pakowaniu 

W przypadku przesyłania strumieniowego zasobów zawierających wiele ścieżek audio z wieloma koderami-dekoder i językami [dynamiczne pakowanie](dynamic-packaging-overview.md) obsługuje teraz wiele ścieżek audio dla danych wyjściowych HLS (w wersji 4 lub nowszej).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Para regionalna Korei jest otwarta dla Media Services 

Media Services jest teraz dostępna w regionach Korei Środkowej i Korei Południowej. 

Aby uzyskać więcej informacji, zobacz [chmury i regiony, w których istnieje Media Services v3](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Ulepszenia wydajności

Dodano aktualizacje, które obejmują ulepszenia wydajności Media Services.

* Zaktualizowano maksymalny rozmiar pliku, który jest obsługiwany przez przetworzenie. Zobacz, [przydziały i ograniczenia](limits-quotas-constraints.md).
* [Ulepszenia szybkości kodowania](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Kwiecień 2019 r.

### <a name="new-presets"></a>Nowe ustawienia wstępne

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) został dodany do wbudowanych ustawień wstępnych analizatora.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) został dodany do wbudowanych ustawień wstępnych kodera. Aby uzyskać więcej informacji, zobacz [kodowanie z uwzględnieniem zawartości](content-aware-encoding.md). 

## <a name="march-2019"></a>Marzec 2019 r.

Pakowanie dynamiczne obsługuje teraz technologię Dolby Atmos. Aby uzyskać więcej informacji, zobacz [kodery-dekoder audio obsługiwane przez pakowanie dynamiczne](dynamic-packaging-overview.md#audio-codecs).

Teraz można określić listę filtrów zasobów lub kont, które mają zastosowanie do lokalizatora przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [kojarzenie filtrów z lokalizatorem przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Luty 2019 r.

Media Services wersja 3 jest teraz obsługiwana w chmurach narodowych platformy Azure. Nie wszystkie funkcje są jeszcze dostępne we wszystkich chmurach. Aby uzyskać szczegółowe informacje, zobacz [chmury i regiony, w których istnieje Azure Media Services v3](azure-clouds-regions.md).

Do schematów Azure Event Grid dla Media Services dodano zdarzenie [Microsoft. Media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) .

## <a name="january-2019"></a>Styczeń 2019 r.

### <a name="media-encoder-standard-and-mpi-files"></a>Pliki Media Encoder Standard i MPI 

Gdy kodowanie przy użyciu Media Encoder Standard do tworzenia plików MP4, nowy plik. MPI jest generowany i dodawany do wyjściowego elementu zawartości. Ten plik MPI jest przeznaczony do poprawiania wydajności dla scenariuszy [dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i przesyłania strumieniowego.

Nie należy modyfikować ani usuwać pliku MPI ani korzystać z jakichkolwiek zależności w usłudze w przypadku istnienia takiego pliku.

## <a name="december-2018"></a>Grudzień 2018 r.

Aktualizacje z wersji GA interfejsu API v3 obejmują:
       
* Właściwości **PresentationTimeRange** nie są już "wymagane" dla **filtrów zasobów** i **filtrów konta**. 
* Opcje zapytania $top i $skip dotyczące **zadań** i **transformacji** zostały usunięte, a $OrderBy został dodany. W ramach dodawania nowych funkcji określania kolejności okazało się, że opcje $top i $skip zostały przypadkowo ujawnione, mimo że nie zostały zaimplementowane.
* Rozszerzalność wyliczenia została włączona jeszcze raz. Ta funkcja została włączona w wersji zapoznawczej zestawu SDK i została przypadkowo wyłączona w wersji GA.
* Zmieniono nazwy dwóch wstępnie zdefiniowanych zasad przesyłania strumieniowego. **SecureStreaming** jest teraz **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** jest teraz **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Listopad 2018 r.

Moduł interfejsu wiersza polecenia 2,0 jest teraz dostępny dla [Azure Media Services v3 ga](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nowe polecenia

- [AZ AMS Account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [AZ AMS Account-Filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [AZ AMS — zasób](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [AZ AMS Asset-Filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [AZ AMS Content-Key-Policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [zadanie AZ AMS](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [AZ AMS Live-Event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [AZ AMS Live-Output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ AMS Streaming-Endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [AZ AMS Streaming-Locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [AZ AMS Account MRU](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) — umożliwia zarządzanie jednostkami zarezerwowanymi multimediów. Aby uzyskać więcej informacji, zobacz [skalowanie jednostek zarezerwowanych multimediów](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nowe funkcje i istotne zmiany

#### <a name="asset-commands"></a>Polecenia zasobów

- dodano argumenty ```--storage-account``` i ```--container```.
- Dodano wartości domyślne czasu wygaśnięcia (teraz + 23h) i uprawnienia (odczyt) w ```az ams asset get-sas-url``` polecenie.

#### <a name="job-commands"></a>Polecenia zadań

- dodano argumenty ```--correlation-data``` i ```--label```
- ```--output-asset-names``` zmieniono nazwę na ```--output-assets```. Teraz akceptuje ona rozdzieloną spacjami listę elementów zawartości w formacie "assetname = Label". Element zawartości bez etykiety może być wysyłany w następujący sposób: "assetname =".

#### <a name="streaming-locator-commands"></a>Polecenia lokalizatora przesyłania strumieniowego

- ```az ams streaming locator``` polecenie podstawowe zamienione z ```az ams streaming-locator```.
- dodano argumenty ```--streaming-locator-id``` i ```--alternative-media-id support```.
- ```--content-keys argument``` argumentem.
- ```--content-policy-name``` zmieniono nazwę na ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Polecenia zasad przesyłania strumieniowego

- ```az ams streaming policy``` polecenie podstawowe zamienione z ```az ams streaming-policy```.
- Obsługa parametrów szyfrowania w ```az ams streaming-policy create``` dodaniu.

#### <a name="transform-commands"></a>Przekształcenia — polecenia

- argument ```--preset-names``` został zastąpiony ```--preset```. Teraz można ustawić tylko 1 wynik/ustawienie wstępne (aby dodać więcej, co trzeba wykonać ```az ams transform output add```). Ponadto można ustawić niestandardowe StandardEncoderPreset przez przekazanie ścieżki do niestandardowego kodu JSON.
- ```az ams transform output remove``` można wykonać, przekazując indeks danych wyjściowych do usunięcia.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumenty dodane w poleceniach ```az ams transform create``` i ```az ams transform output add```.

## <a name="october-2018---ga"></a>Październik 2018 — GA

W tej sekcji opisano aktualizacje Azure Media Services (AMS) dla października.

### <a name="rest-v3-ga-release"></a>Wersja V3

[Wersja V3](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) (wersja 3) zawiera więcej interfejsów API dla filtrów metadanych na żywo, konta/zasobu i obsługa technologii DRM.

#### <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure 

Obsługa zarządzania zasobami platformy Azure umożliwia ujednolicone zarządzanie i interfejs API operacji (teraz wszystko w jednym miejscu).

Począwszy od tej wersji, za pomocą szablonów Menedżer zasobów można tworzyć zdarzenia na żywo.

#### <a name="improvement-of-asset-operations"></a>Poprawa operacji zasobów 

Wprowadzono następujące ulepszenia:

- Pozyskiwanie adresów URL (HTTP) lub adresów URL usługi Azure Blob Storage SAS.
- Określ nazwy kontenerów dla zasobów. 
- Łatwiejsza obsługa danych wyjściowych w celu tworzenia niestandardowych przepływów pracy przy użyciu Azure Functions.

#### <a name="new-transform-object"></a>Nowy obiekt transformacji

Nowy obiekt **transformacji** upraszcza model kodowania. Nowy obiekt ułatwia tworzenie i udostępnianie kodowań Menedżer zasobów szablonów i ustawień wstępnych. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Uwierzytelnianie Azure Active Directory i kontrola RBAC

Uwierzytelnianie usługi Azure AD i Access Control oparte na rolach (RBAC) umożliwiają bezpieczne transformacje, LiveEvents, Zasady kluczy zawartości lub zasoby według roli lub użytkowników w usłudze Azure AD.

#### <a name="client-sdks"></a>Zestawy SDK klienta  

Języki obsługiwane w Media Services v3: .NET Core, Java, Node. js, Ruby, TypeScript, Python, go.

#### <a name="live-encoding-updates"></a>Aktualizacje kodowania na żywo

Wprowadzono następujące aktualizacje kodowania na żywo:

- Nowy tryb niskiego opóźnienia dla na żywo (kompleksowy 10 sekund).
- Ulepszona obsługa protokołu RTMP (Zwiększona stabilność i więcej obsługa kodera-źródła).
- Bezpieczne pozyskiwanie RTMP.

    Gdy tworzysz wydarzenie na żywo, uzyskasz teraz 4 adresy URL pozyskiwania. 4 adresy URL pozyskiwania są prawie identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numeru portu. Dwa z adresów URL to podstawowy i zapasowy dla RTMP. 
- Obsługa transkodowania 24-godzinnego. 
- Ulepszona obsługa sygnałów AD w usłudze RTMP za pośrednictwem SCTE35.

#### <a name="improved-event-grid-support"></a>Ulepszona obsługa Event Grid

Można wyświetlić następujące ulepszenia Event Grid wsparcia:

- Azure Event Grid integrację ułatwiającą programowanie z Logic Apps i Azure Functions. 
- Subskrybuj zdarzenia dotyczące kodowania, kanałów na żywo i nie tylko.

### <a name="cmaf-support"></a>Obsługa CMAF

Obsługa szyfrowania CMAF i "cbcs" dla odtwarzaczy Apple HLS (iOS 11 +) i MPEG-PAUZ, które obsługują CMAF.

### <a name="video-indexer"></a>Indeksator wideo

Video Indexer wersja GA została ogłoszona w sierpniu. Aby uzyskać nowe informacje o aktualnie obsługiwanych funkcjach, zobacz [co to jest Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Plany dotyczące zmian

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
 
Dostępny jest moduł interfejsu wiersza polecenia platformy Azure 2,0 zawierający operacje na wszystkich funkcjach (w tym na żywo, Zasady kluczy zawartości, filtry kont/zasobów, zasady przesyłania strumieniowego). 

### <a name="known-issues"></a>Znane problemy

Następujący problem ma wpływ tylko na klientów, którzy korzystali z interfejsu API wersji zapoznawczej dla zasobu lub AccountFilters.

Jeśli utworzono zasoby lub filtry kont z zakresu od 09/28 do 10/12 przy użyciu interfejsu wiersza polecenia Media Services v3 lub interfejsów API, należy usunąć wszystkie elementy zawartości i AccountFilters, a następnie utworzyć je ponownie z powodu konfliktu wersji. 

## <a name="may-2018---preview"></a>Maj 2018 — wersja zapoznawcza

### <a name="net-sdk"></a>Zestaw SDK .NET

Zestaw SDK platformy .NET zawiera następujące funkcje:

* **Transformacje** i **zadania** do kodowania lub analizowania zawartości multimedialnej. Aby zapoznać się z przykładami, zobacz [przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md) i [Analizowanie](analyze-videos-tutorial-with-api.md).
* **Lokalizatory przesyłania strumieniowego** do publikowania i przesyłania strumieniowego zawartości na urządzenia użytkowników końcowych
* **Zasady przesyłania strumieniowego** i **Zasady kluczy zawartości** umożliwiające konfigurowanie dostarczania kluczy i ochrony zawartości (DRM) podczas dostarczania zawartości.
* **Zdarzenia na żywo** i **wyjście na żywo** umożliwiają skonfigurowanie pozyskiwania i archiwizowania zawartości na żywo przesyłanych strumieniowo.
* **Zasoby** do przechowywania i publikowania zawartości multimedialnej w usłudze Azure Storage. 
* **Punkty końcowe przesyłania strumieniowego** służące do konfigurowania i skalowania dynamicznego tworzenia pakietów, szyfrowania i przesyłania strumieniowego na żywo i na żądanie zawartości multimedialnej.

### <a name="known-issues"></a>Znane problemy

* Podczas przesyłania zadania możesz określić, aby pozyskać źródło wideo przy użyciu adresów URL protokołu HTTPS, adresów URL SAS lub ścieżek do plików znajdujących się w usłudze Azure Blob Storage. Obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu przez adresy URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

- [Omówienie](media-services-overview.md)
- [Informacje o wersji Media Services V2](../previous/media-services-release-notes.md)
