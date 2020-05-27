---
title: Podstawowe informacje o interfejsie wiersza polecenia mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać narzędzia poleceń interfejsu wiersza polecenia mowy do pracy z usługą mowy bez kodu i minimalnej konfiguracji.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 2e75e177c1a5af13c1907b3a1abc9218096e8d45
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800699"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Poznaj podstawowe informacje o interfejsie wiersza polecenia mowy

Ten artykuł zawiera informacje o podstawowych wzorcach użycia interfejsu wiersza polecenia mowy, który umożliwia korzystanie z usługi mowy bez pisania kodu. Możesz szybko przetestować główne funkcje usługi mowy, bez tworzenia środowisk programistycznych lub pisania kodu, aby sprawdzić, czy przypadki użycia mogą być odpowiednio spełnione. Ponadto interfejs wiersza polecenia mowy jest gotowy do produkcji i może służyć do automatyzowania prostych przepływów pracy w usłudze mowy przy użyciu `.bat` skryptów powłoki lub.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Podstawowy sposób użycia

W tej sekcji przedstawiono kilka podstawowych poleceń SPX, które często są przydatne podczas pierwszego testowania i eksperymentowania. Zacznij od przeprowadzenia rozpoznawania mowy przy użyciu domyślnego mikrofonu, uruchamiając następujące polecenie.

```shell
spx recognize --microphone
```

Po wprowadzeniu polecenia protokół SPX rozpocznie nasłuchiwanie dźwięku na bieżącym aktywnym urządzeniu wejściowym i zatrzymuje się po naciśnięciu klawisza `ENTER` . Zarejestrowane mowę są następnie rozpoznawane i konwertowane na tekst w danych wyjściowych konsoli. Synteza zamiany tekstu na mowę jest również łatwa w użyciu interfejsu wiersza polecenia. 

Uruchomienie następującego polecenia spowoduje wprowadzenie tekstu jako dane wejściowe i wyjście z wydanej zamiany mowy na bieżące aktywne urządzenie wyjściowe.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Oprócz rozpoznawania mowy i syntezy, można także przeprowadzić Tłumaczenie mowy przy użyciu interfejsu wiersza polecenia mowy. Podobnie jak powyżej polecenie rozpoznawania mowy, uruchom następujące polecenie, aby przechwycić dźwięk z domyślnego mikrofonu i wykonać translację do tekstu w języku docelowym.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

W tym poleceniu należy określić zarówno źródło (język do **przetłumaczenia**), jak i obiekt docelowy (język do tłumaczenia **na**). Użycie `--microphone` argumentu spowoduje nasłuchiwanie dźwięku na bieżącym aktywnym urządzeniu wejściowym i zatrzymanie po naciśnięciu klawisza `ENTER` . Dane wyjściowe to tłumaczenie tekstu w języku docelowym, zapisywane w pliku tekstowym.

> [!NOTE]
> Zapoznaj się z listą wszystkich obsługiwanych języków z odpowiednimi kodami ustawień regionalnych w [artykule język i ustawienia regionalne](language-support.md) .

## <a name="batch-operations"></a>Operacje wsadowe

Polecenia w poprzedniej sekcji doskonale sprawdzają się, jak działa usługa Speech. Jednak podczas oceniania, czy mogą być spełnione przypadki użycia, może być konieczne wykonanie operacji wsadowych względem zakresu danych wejściowych, które już masz, aby zobaczyć, w jaki sposób usługa obsługuje wiele scenariuszy. W tej sekcji pokazano, jak:

* Uruchamianie rozpoznawania mowy usługi Batch w katalogu plików audio
* Wykonaj iterację `.tsv` pliku i uruchom syntezę zamiany tekstu na mowę

## <a name="batch-speech-recognition"></a>Rozpoznawanie mowy w usłudze Batch

Jeśli masz katalog plików audio, możesz łatwo uruchomić interfejs wiersza polecenia mowy, aby szybko uruchamiać rozpoznawanie mowy w usłudze Batch. Po prostu uruchom następujące polecenie, wskazując katalog za pomocą `--files` polecenia. W tym przykładzie dołączysz `\*.wav` do katalogu, aby rozpoznać wszystkie pliki znajdujące się `.wav` w katalogu. Dodatkowo należy określić `--threads` argument, aby uruchomić rozpoznawanie w przypadku 10 wątków równoległych.

> [!NOTE]
> `--threads`Argument może być również używany w następnej sekcji dla `spx synthesize` poleceń, a dostępne wątki będą zależeć od procesora i bieżącego procentu obciążenia.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Rozpoznane dane wyjściowe mowy są zapisywane `speech_output.tsv` przy użyciu `--output file` argumentu. Poniżej znajduje się przykład struktury pliku wyjściowego.

    audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
    sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
    sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.

## <a name="batch-text-to-speech-synthesis"></a>Synteza zamiany tekstu na mowę

Najprostszym sposobem uruchomienia funkcji zamiany tekstu na mowę jest utworzenie nowego `.tsv` pliku z wartościami rozdzielanymi znakami tabulacji `--foreach` . Weź pod uwagę następujący plik `text_synthesis.tsv` :

    audio.output    text
    C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
    C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
    C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.

 Następnie należy uruchomić polecenie, aby wskazać `text_synthesis.tsv` , wykonać syntezę dla każdego `text` pola i napisać wynik do odpowiedniej `audio.output` ścieżki jako `.wav` plik. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

To polecenie jest odpowiednikiem uruchomienia `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **dla każdego** rekordu w `.tsv` pliku. Kilka rzeczy do zanotowania:

* Nagłówki kolumn `audio.output` i `text` , odpowiadają odpowiednio do argumentów wiersza polecenia `--audio output` i `--text` . Wieloczęściowe argumenty wiersza polecenia, takie jak `--audio output` powinny być sformatowane w pliku bez spacji, brak wiodących kresek i kropki oddzielające ciągi, np. `audio.output` . Wszystkie inne istniejące argumenty wiersza polecenia można dodać do pliku jako dodatkowe kolumny przy użyciu tego wzorca.
* Gdy plik jest sformatowany w ten sposób, żadne dodatkowe argumenty nie są wymagane do przesłania do `--foreach` .
* Upewnij się, że poszczególne wartości są oddzielane `.tsv` za pomocą **karty**.

Jednak jeśli masz `.tsv` plik podobny do poniższego przykładu, z nagłówkami kolumn, które **nie pasują** do argumentów wiersza polecenia:

    wav_path    str_text
    C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
    C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
    C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.

Można zastąpić te nazwy pól prawidłowymi argumentami, używając następującej składni w `--foreach` wywołaniu. Jest to takie samo wywołanie jak powyżej.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Następne kroki

* Ukończ [rozpoznawanie mowy](./quickstarts/speech-to-text-from-microphone.md) lub Przewodniki Szybki Start dotyczące funkcji rozpoznawania [mowy przy](./quickstarts/text-to-speech.md) użyciu zestawu SDK.
