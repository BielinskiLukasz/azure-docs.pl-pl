---
title: Co to jest transkrypcja partii — usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja usługi Batch jest idealnym rozwiązaniem, jeśli chcesz transkrypcja dużą ilość dźwięku w magazynie, na przykład obiekty blob platformy Azure. Za pomocą dedykowanego interfejsu API REST można wskazać pliki audio z identyfikatorem URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać transkrypcje.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 3e7f310f37bd016a73c589db3c9a23e197465427
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053920"
---
# <a name="what-is-batch-transcription"></a>Co to jest transkrypcja partii?

Transkrypcja usługi Batch to zestaw operacji interfejsu API REST, które umożliwiają transkrypcja dużej ilości danych audio w magazynie. Możesz wskazać pliki audio z identyfikatorem URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać wyniki transkrypcji. Dzięki nowemu interfejsowi API programu v 3.0 możesz jego przepisywania jeden lub więcej plików audio lub przetwarzać cały kontener magazynu.

Asynchroniczne transkrypcja zamiany mowy na tekst to tylko jedna z funkcji. Korzystając z interfejsów API REST transkrypcji wsadowej, można wywołać następujące metody:



|    Operacja transkrypcji partii                                             |    Metoda    |    Wywołanie interfejsu API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Tworzy nową transkrypcję.                                              |    POST      |    speechtotext/v 3.0/transkrypcje            |
|    Pobiera listę transkrypcji dla uwierzytelnionej subskrypcji.    |    GET       |    speechtotext/v 3.0/transkrypcje            |
|    Pobiera listę obsługiwanych ustawień regionalnych dla transkrypcji w trybie offline.              |    GET       |    speechtotext/v 3.0/transkrypcje/ustawienia regionalne    |
|    Aktualizuje modyfikowalne szczegóły transkrypcji identyfikowanej przez jego identyfikator.    |    WYSŁANA     |    speechtotext/v 3.0/transkrypcje/{ID}       |
|    Usuwa określone zadanie transkrypcji.                                 |    DELETE    |    speechtotext/v 3.0/transkrypcje/{ID}       |
|    Pobiera transkrypcję identyfikowaną przez podany identyfikator.                        |    GET       |    speechtotext/v 3.0/transkrypcje/{ID}       |
|    Pobiera pliki wynikowe transkrypcji identyfikowane przez podany identyfikator.    |    GET       |    speechtotext/v 3.0/transkrypcje/{ID}/pliki |




Możesz przejrzeć i przetestować szczegółowy interfejs API, który jest dostępny jako [dokument struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Zadania transkrypcji partii są planowane na podstawie najlepszego nakładu pracy. Obecnie nie ma oszacowania, kiedy zadanie zmienia się w stan uruchomienia. W przypadku normalnego ładowania systemu powinno to nastąpić w ciągu kilku minut. Po uruchomieniu rzeczywista transkrypcja jest przetwarzana szybciej niż w czasie rzeczywistym dźwięku.

Obok łatwego w użyciu interfejsu API nie musisz wdrażać niestandardowych punktów końcowych i nie masz żadnych wymagań współbieżności do obserwowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscription-key"></a>Klucz subskrypcji

Podobnie jak w przypadku wszystkich funkcji usługi Speech, można utworzyć klucz subskrypcji z [Azure Portal](https://portal.azure.com) , postępując zgodnie z [przewodnikiem](get-started.md)wprowadzenie.

>[!NOTE]
> Aby można było użyć transkrypcji partii, wymagana jest Standardowa subskrypcja (S0) dla usługi rozpoznawania mowy. Bezpłatne klucze subskrypcji (F0) nie działają. Aby uzyskać więcej informacji, zobacz [Cennik i limity](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modele niestandardowe

Jeśli planujesz dostosowanie modeli, postępuj zgodnie z instrukcjami w temacie [dostosowanie akustyczne](how-to-customize-acoustic-models.md) i [Dostosowywanie języka](how-to-customize-language-model.md). Aby można było używać utworzonych modeli w transkrypcji partii, potrzebna jest ich lokalizacja modelu. Lokalizację modelu można pobrać podczas inspekcji szczegółów modelu ( `self` Właściwości). Wdrożony niestandardowy punkt końcowy *nie jest wymagany* w przypadku usługi transkrypcji partii.

## <a name="the-batch-transcription-api"></a>Interfejs API transkrypcji usługi Batch

### <a name="supported-formats"></a>Obsługiwane formaty

Interfejs API transkrypcji usługi Batch obsługuje następujące formaty:

| Format | Wymaga | Multimedia | Częstotliwość próbkowania                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |
| FORMATU    | PCM   | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |
| OGG    | OPUS  | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |

W przypadku strumieni stereofonicznych audio lewy i prawy są dzielone podczas transkrypcji. Dla każdego kanału tworzony jest plik wynikowy JSON. Sygnatury czasowe wygenerowane na wypowiedź umożliwiają deweloperowi utworzenie uporządkowanej końcowej transkrypcji.

### <a name="configuration"></a>Konfiguracja

Parametry konfiguracji są podane jako dane JSON (co najmniej jeden z pojedynczych plików):

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Parametry konfiguracji są podane jako dane JSON (przetwarzanie całego kontenera magazynu):

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

Aby używać niestandardowych modeli szkolonych w transkrypcjach wsadowych, można się do nich odwoływać, jak pokazano poniżej:

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Właściwości konfiguracji

Użyj tych opcjonalnych właściwości, aby skonfigurować transkrypcję:

:::row:::
   :::column span="1":::
      **Parametr**
   :::column-end:::
   :::column span="2":::
      **Opis**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Określa sposób obsługi niezbyt wulgarności w wynikach rozpoznawania. Akceptowane wartości to `None` wyłączenie filtrowania wulgarności, `Masked` zastępowanie nieseksu gwiazdką, `Removed` Aby usunąć z wyniku wszystkie niezbyt wulgarne lub `Tags` dodać tagi "wulgarne". Ustawienie domyślne to `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Określa, jak obsłużyć interpunkcję w wynikach rozpoznawania. Akceptowane wartości to `None` wyłączenie interpunkcji, `Dictated` w celu oznaczania znaków jawnych (mówionych), `Automatic` w celu poinformowania dekodera z interpunkcją lub `DictatedAndAutomatic` użycia podyktowanych i automatycznych interpunkcji. Ustawienie domyślne to `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Określa, czy sygnatury czasowe poziomu słowa mają być dodawane do danych wyjściowych. Akceptowane wartości to `true` włączenie znaczników czasu poziomu słowa i `false` (wartość domyślna), aby je wyłączyć.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Określa, że analiza diarization powinna zostać przeprowadzona na wejściu, który powinien być kanałem mono zawierającym dwa głosy. Akceptowane wartości `true` włączają diarization i `false` (wartość domyślna), aby je wyłączyć. Wymagane jest również `wordLevelTimestampsEnabled` ustawienie wartości true.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Opcjonalna tablica numerów kanałów do przetworzenia. W tym miejscu podzbiór dostępnych kanałów w pliku audio może być określony do przetworzenia (np. `0` tylko). Jeśli nie zostanie określony, kanały `0` i `1` są uzyskanego jako domyślne.
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Opcjonalny czas trwania usuwania transkrypcji po zakończeniu transkrypcji. `timeToLive`Jest przydatny do masowego tworzenia transkrypcji, aby upewnić się, że zostaną ostatecznie usunięte (np. `PT12H` ). Jeśli nie zostanie określony lub ustawiony na `PT0H` , transkrypcja nie zostanie usunięta automatycznie.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Opcjonalny adres URL z [sygnaturą dostępu współdzielonego usługi](../../storage/common/storage-sas-overview.md) do zapisywalnego kontenera na platformie Azure. Wynik jest przechowywany w tym kontenerze. Jeśli nie zostanie określony, firma Microsoft zapisuje wyniki w kontenerze magazynu zarządzanym przez firmę Microsoft. Gdy transkrypcja zostanie usunięta przez wywołanie [usuwania transkrypcji](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription), dane wynikowe również zostaną usunięte.
:::row-end:::

### <a name="storage"></a>Magazyn

Transkrypcja usługi Batch obsługuje [usługę Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) do odczytu i zapisywania transkrypcji w magazynie.

## <a name="the-batch-transcription-result"></a>Wynik transkrypcji partii

Dla każdego wejściowego audio jest tworzony jeden plik wynikowy transkrypcji. Możesz uzyskać listę plików wynikowych, wywołując [pliki Get transkrypcji](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles). Ta metoda zwraca listę plików wynikowych dla tego transkrypcji. Aby znaleźć plik transkrypcji dla określonego pliku wejściowego, należy odfiltrować wszystkie zwrócone pliki z `kind`  ==  `Transcription` i `name`  ==  `{originalInputName.suffix}.json` .

Każdy plik wynikowy transkrypcji jego format:

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]    
    }
  ]
}
```

Wynik zawiera następujące formularze:

:::row:::
   :::column span="1":::
      **Formularz**
   :::column-end:::
   :::column span="2":::
      **Zawartość**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      Rzeczywiste wyrazy zostały rozpoznane.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Odwrócony tekst — znormalizowana forma rozpoznanego tekstu. Skróty ("Lekarz Kowalski" do "Dr Kowalski"), numery telefonów i inne przekształcenia są stosowane.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      Formularz ITN z zastosowanym maską wulgarności.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      Formularz wyświetlania rozpoznanego tekstu. Dodano znaki interpunkcyjne i wielkie litery.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separacja głośników (Diarization)

Diarization to proces oddzielania głośników w części audio. Nasz potok wsadowy obsługuje diarization i jest w stanie rozpoznawać dwa głośniki w nagraniach kanału mono. Funkcja nie jest dostępna w nagraniach stereo.

Dane wyjściowe transkrypcji z włączonym diarization zawiera `Speaker` wpis dla każdej frazy uzyskanego. Jeśli diarization nie jest używana, właściwość `Speaker` nie jest obecna w danych wyjściowych JSON. W przypadku diarization obsługujemy dwie głosy, więc głośniki są identyfikowane jako `1` lub `2` .

Aby zażądać diarization, wystarczy dodać odpowiedni parametr w żądaniu HTTP, jak pokazano poniżej.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Znaczniki czasu na poziomie słowa muszą być włączone, ponieważ parametry w powyższym żądaniu wskazują.

## <a name="best-practices"></a>Najlepsze rozwiązania

Usługa transkrypcji może obsłużyć dużą liczbę przesłanych transkrypcji. Można wysyłać zapytania o stan transkrypcji za pomocą `GET` przy [pobieraniu transkrypcji](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions). Wywołaj regularne [usuwanie transkrypcji](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) z usługi po pobraniu wyników. Alternatywnie Ustaw `timeToLive` Właściwość na rozsądną wartość, aby zapewnić ostateczne usuwanie wyników.

## <a name="sample-code"></a>Przykładowy kod

Kompletne przykłady są dostępne w [repozytorium przykładowym GitHub](https://aka.ms/csspeech/samples) w `samples/batch` podkatalogu.

Zaktualizuj przykładowy kod przy użyciu informacji o subskrypcji, regionu usługi, identyfikatora URI sygnatury dostępu współdzielonego wskazującego plik audio do transkrypcja i lokalizację modelu w przypadku, gdy chcesz użyć modelu niestandardowego.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Przykładowy kod konfiguruje klienta i przesyła żądanie transkrypcji. Następnie sonduje informacje o stanie i drukuje szczegóły dotyczące postępu transkrypcji.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Aby uzyskać szczegółowe informacje o poprzednich wywołaniach, zobacz [dokument struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Aby wyświetlić pełny przykład, przejdź do witryny [GitHub](https://aka.ms/csspeech/samples) w `samples/batch` podkatalogu.

Zanotuj konfigurację asynchroniczną dotyczącą ogłaszania audio i uzyskiwania stanu transkrypcji. Tworzony klient jest klientem HTTP platformy .NET. Istnieje `PostTranscriptions` metoda wysyłania szczegółów pliku audio i `GetTranscriptions` metody uzyskiwania stanu. `PostTranscriptions`zwraca dojście i `GetTranscriptions` używa go do utworzenia dojścia w celu uzyskania stanu transkrypcji.

Bieżący przykładowy kod nie określa modelu niestandardowego. Usługa używa modelu linii bazowej do jego przepisywania pliku lub plików. Aby określić model, można przekazać do tej samej metody odwołanie modelu dla modelu niestandardowego.

> [!NOTE]
> W przypadku transkrypcji linii bazowej nie trzeba deklarować identyfikatora modelu linii bazowej.

## <a name="download-the-sample"></a>Pobieranie przykładu

Przykład można znaleźć w `samples/batch` katalogu w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja interfejsu API zamiany mowy na tekst v3](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
