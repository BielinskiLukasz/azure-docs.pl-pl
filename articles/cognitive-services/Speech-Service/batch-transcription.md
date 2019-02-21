---
title: Jak używać transkrypcji usługi Batch — usługi mowy
titlesuffix: Azure Cognitive Services
description: Transkrypcja partii jest idealnym rozwiązaniem, jeśli chcesz także dużej ilości dźwięk w magazynie, takim jak obiektów blob platformy Azure. Za pomocą dedykowanego interfejsu API REST, można wskazać pliki audio, przy użyciu sygnatury dostępu współdzielonego (SAS) identyfikator URI i asynchronicznie otrzymywać transkrypcji.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 3b403eb80bae01efe730b69b7e6a5ddaea81355a
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447654"
---
# <a name="why-use-batch-transcription"></a>Dlaczego warto używać usługi Batch transkrypcji?

Transkrypcja partii jest idealnym rozwiązaniem, jeśli chcesz także dużej ilości dźwięk w magazynie, takim jak obiektów blob platformy Azure. Za pomocą dedykowanego interfejsu API REST, można wskazać pliki audio, przy użyciu sygnatury dostępu współdzielonego (SAS) identyfikator URI i asynchronicznie otrzymywać transkrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscription-key"></a>Klucz subskrypcji

Jak z wszystkich funkcji usługi rozpoznawania mowy, tworzenie klucz subskrypcji z [witryny Azure portal](https://portal.azure.com) postępując zgodnie z naszym [Wprowadzenie — przewodnik](get-started.md). Jeśli planujesz uzyskiwanie transkrypcje modeli podstawowych w naszym Tworzenie klucza jest wszystko, co należy zrobić.

>[!NOTE]
> Standardowa subskrypcja (S0) dla usług przetwarzania mowy wymagane jest wprowadzenie transkrypcji usługi batch. Bezpłatna subskrypcja kluczy (F0) nie będzie działać. Aby uzyskać więcej informacji, zobacz [ceny i limity](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modele niestandardowe

Jeśli zamierzasz dostosować modele akustyczne lub języka, wykonaj kroki opisane w [dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md) i [dostosowywanie modeli językowych](how-to-customize-language-model.md). Używanie modeli utworzonych w transkrypcji usługi batch należy ich identyfikatory modelu. Ten identyfikator nie jest identyfikator punktu końcowego, który można znaleźć w widoku Szczegóły punktu końcowego, jest identyfikator modelu, który można pobrać po wybraniu szczegółów modeli.

## <a name="the-batch-transcription-api"></a>Transkrypcji interfejsu API usługi Batch

Interfejs API transkrypcji usługi Batch oferuje asynchroniczne transkrypcja mowy na tekst, oraz dodatkowe funkcje. To interfejs API REST, który udostępnia metody:

1. Tworzenie żądań przetwarzania wsadowego
1. Stan zapytania
1. Pobieranie transkrypcji

> [!NOTE]
> Interfejs API transkrypcji usługi Batch jest idealny dla wywołania roboczych, które zazwyczaj są gromadzone tysiące godzin audio. Ułatwia on także bardzo dużych ilości nagrania audio.

### <a name="supported-formats"></a>Obsługiwane formaty

Interfejs API transkrypcji usługi Batch obsługuje następujące formaty:

| Format | Koder-dekoder | Szybkość transmisji bitów | Częstotliwość próbkowania |
|--------|-------|---------|-------------|
| WAV | MODUŁU PCM | 16-bitowych | stereo mono, kHz, 8 lub 16 |
| MP3 | MODUŁU PCM | 16-bitowych | stereo mono, kHz, 8 lub 16 |
| OGG | DZIELE | 16-bitowych | stereo mono, kHz, 8 lub 16 |

Dla strumieni audio stereo transkrypcji interfejsu API usługi Batch dzieli kanału lewy i prawy podczas transkrypcji. Każdy dwa pliki JSON z wynikiem są tworzone z pojedynczy kanał. Sygnatury czasowe na wypowiedź Włącz dla deweloperów utworzyć uporządkowany końcowego transkrypcji. To przykładowe żądanie zawiera właściwości filtrowania wulgaryzmów, znaki interpunkcyjne i sygnatury czasowe z poziomu programu word. 

### <a name="configuration"></a>Konfigurowanie

Parametry konfiguracji są dostarczane jako dane JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": ["<optional acoustic model ID>, <optional language model ID>"],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  }
}
```

> [!NOTE]
> Interfejs API transkrypcji usługi Batch korzysta z usługi REST, do żądania transkrypcje, stanu i skojarzonych wyników. Można użyć interfejsu API z dowolnego języka. W następnej sekcji opisano sposób użycia interfejsu API.

### <a name="configuration-properties"></a>Właściwości konfiguracji

| Parametr | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Określa sposób obsługi wulgaryzmów w wyniki rozpoznawania. Akceptowane wartości to `none` która wyłącza filtrowanie wulgaryzmów `masked` gwiazdek, która zastępuje wulgaryzmów `removed` z wyników, które powoduje usunięcie wszystkich wulgaryzmów lub `tags` dodaje tagi "wulgaryzmów". Ustawieniem domyślnym jest `masked`. | Optional (Opcjonalność) |
| `PunctuationMode` | Określa sposób obsługi znaków interpunkcyjnych w wyniki rozpoznawania. Akceptowane wartości to `none` która wyłącza znak interpunkcyjny, `dictated` co oznacza jawne znak interpunkcyjny, `automatic` umożliwiającą dekodera przeciwdziałania znak interpunkcyjny, lub `dictatedandautomatic` co oznacza definiowane znaków interpunkcyjnych lub automatyczny. | Optional (Opcjonalność) |
 | `AddWordLevelTimestamps` | Określa, jeśli sygnatury czasowe z poziomu programu word powinna być dodana do danych wyjściowych. Akceptowane wartości to `true` umożliwiająca sygnatury czasowe z poziomu programu word i `false` (wartość domyślna) można ją wyłączyć. | Optional (Opcjonalność) |

## <a name="sample-code"></a>Przykładowy kod

Pełny przykład jest dostępny w [repozytorium przykładów GitHub](https://aka.ms/csspeech/samples) wewnątrz `samples/batch` podkatalogu.

Trzeba dostosować przykładowego kodu, informacje o subskrypcji, usługa region sygnatury dostępu Współdzielonego identyfikator URI wskazujący na plik dźwiękowy transkrypcja i identyfikatory modelu, w przypadku, gdy chcesz użyć niestandardowego modelu akustycznego lub języka. 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Przykładowy kod będzie instalacji klienta i przesłać żądanie transkrypcji. Zostanie następnie sondowania pod kątem informacji o stanie i Drukuj szczegóły o postępie transkrypcji.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Aby uzyskać szczegółowe informacje dotyczące poprzedniego wywołania, zobacz nasze [dokument struktury Swagger](https://westus.cris.ai/swagger/ui/index). Aby uzyskać pełny przykład pokazano poniżej, przejdź do [GitHub](https://aka.ms/csspeech/samples) w `samples/batch` podkatalogu.

Zwróć uwagę na asynchroniczne Instalatora w celu opublikowanie audio i odbieranie stanu transkrypcji. Klient, który tworzysz to klient HTTP platformy .NET. Brak `PostTranscriptions` Metoda przesyłania plików audio i `GetTranscriptions` metody do odbierania wyników. `PostTranscriptions` Zwraca uchwyt, a `GetTranscriptions` używa jej do utworzenia dojście można pobrać stanu transkrypcji.

Bieżący kod przykładowy nie określono modelu niestandardowego. Usługa używa modele planu bazowego dla przepisywania pliku lub plików. Aby określić te modele, można przekazać w tej samej metody identyfikatory modelu akustycznego i modelu języka.

> [!NOTE]
> W transkrypcji punktu odniesienia nie jest wymagana do deklarowania Identyfikatora dla modeli linii bazowej. Jeśli określisz tylko język, w identyfikator modelu (a nie Identyfikatora model akustyczny), pasujące model akustyczny wybierana jest. Jeśli określono tylko identyfikator model akustyczny, dopasowania modelu języka jest wybierana.

### <a name="supported-storage"></a>Obsługiwane

Aktualnie obsługiwana jest tylko usługi Azure Blob storage.

## <a name="download-the-sample"></a>Pobierz przykład

Można znaleźć przykład w `samples/batch` katalogu w [repozytorium przykładów GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> Batch transkrypcji zadania zaplanowane na optymalne, nie ma żadnych szacowany czas podczas zadania zmieni się w stanie uruchomienia. Jeden raz w stanie uruchomienia, rzeczywiste transkrypcji są przetwarzane szybciej niż audio czasu rzeczywistego.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
