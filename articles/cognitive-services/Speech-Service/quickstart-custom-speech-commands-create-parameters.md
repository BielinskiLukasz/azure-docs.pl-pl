---
title: 'Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano Dodawanie parametrów do aplikacji poleceń niestandardowych.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 994ac88f78dfe5a5b0ee6fef3fa97d66d53c911b
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156696"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)

W [poprzednim artykule](./quickstart-custom-speech-commands-create-new.md)został utworzony nowy projekt poleceń niestandardowych w celu reagowania na polecenia bez parametrów.

W tym artykule zostanie rozbudowana ta aplikacja z parametrami, aby umożliwić obsługę włączania i wyłączania wielu urządzeń.

## <a name="create-parameters"></a>Utwórz parametry

1. Otwórz projekt, który został [wcześniej utworzony](./quickstart-custom-speech-commands-create-new.md)
1. Ponieważ polecenie zostanie teraz obsłużone i wyłączone, Zmień nazwę polecenia na "TurnOnOff"
   - Umieść kursor nad nazwą polecenia i wybierz ikonę Edytuj, aby zmienić nazwę
1. Utwórz nowy parametr, aby określić, czy użytkownik chce włączyć lub wyłączyć urządzenie
   - Wybierz ikonę `+` obok sekcji Parametry

   > [!div class="mx-imgBorder"]
   > ![Utwórz parametr](media/custom-speech-commands/create-on-off-parameter.png)

   | Ustawienie            | Sugerowana wartość     | Opis                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nazwa               | OnOff               | Opisowa nazwa parametru                                                                     |
   | Jest globalny          | unchecked           | Pole wyboru wskazujące, czy wartość tego parametru jest stosowana globalnie do wszystkich poleceń w projekcie |
   | Wymagane           | checked             | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru          |
   | Szablon odpowiedzi  | "— Włączone lub wyłączone?"      | Monit o podanie wartości tego parametru, gdy nie jest on znany                                       |
   | Typ               | Ciąg              | Typ parametru, taki jak Number, String lub Data Time                                               |
   | Konfigurowanie      | Lista ciągów         | W przypadku ciągów lista ciągów ogranicza dane wejściowe do zestawu możliwych wartości                                      |
   | Wartości listy ciągów | włączone, wyłączone             | Dla parametru lista ciągów, zestaw możliwych wartości i ich synonimów                                |

   - Następnie wybierz ponownie ikonę `+`, aby dodać drugi parametr reprezentujący nazwę urządzeń. Na potrzeby tego przykładu telewizora i wentylatoru

   | Ustawienie            | Sugerowana wartość       | Opis                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nazwa               | SubjectDevice         | Opisowa nazwa parametru                                                                     |
   | Jest globalny          | unchecked             | Pole wyboru wskazujące, czy wartość tego parametru jest stosowana globalnie do wszystkich poleceń w projekcie |
   | Wymagane           | checked               | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru          |
   | Szablon odpowiedzi  | "-Które urządzenie?"     | Monit o podanie wartości tego parametru, gdy nie jest on znany                                       |
   | Typ               | Ciąg                | Typ parametru, taki jak Number, String lub Data Time                                               |
   | Konfigurowanie      | Lista ciągów           | W przypadku ciągów lista ciągów ogranicza dane wejściowe do zestawu możliwych wartości                                      |
   | Wartości listy ciągów | Telewizja, wentylator               | Dla parametru lista ciągów, zestaw możliwych wartości i ich synonimów                                |
   | Synonimy (telewizja)      | Telewizja, informowanie     | Opcjonalne synonimy dla każdej możliwej wartości parametru listy ciągów                                      |

## <a name="add-sample-sentences"></a>Dodaj przykładowe zdania

Za pomocą parametrów warto dodać przykładowe zdania, które obejmują wszystkie możliwe kombinacje. Przykład:

1. Pełne informacje o parametrach — `"turn {OnOff} the {SubjectDevice}"`
1. Informacje o części parametru-`"turn it {OnOff}"`
1. Brak informacji o parametrach-`"turn something"`

Przykładowe zdania z różnymi ilościami informacji umożliwiają aplikacji poleceń niestandardowych rozpoznawanie zarówno rozdzielczości jednego zrzutu, jak i rozwiązywanie wielu przełączania przy użyciu częściowych informacji.

Z tego względu należy edytować przykładowe zdania, aby użyć parametrów zgodnie z sugerowaną poniżej.

> [!TIP]
> W edytorze przykładowe zdania Użyj nawiasów klamrowych, aby odwołać się do parametrów. - `turn {OnOff} the {SubjectDevice}` użyć uzupełniania karty, aby odwołać się do poprzednio utworzonych parametrów.

> [!div class="mx-imgBorder"]
> ![przykładowe zdania z parametrami](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Dodaj parametry do reguły ukończenia

Zmodyfikuj regułę ukończenia utworzoną w [poprzednim przewodniku szybki start](./quickstart-custom-speech-commands-create-new.md):

1. Dodaj nowy warunek i wybierz wymagany parametr. Zaznacz obie `OnOff` i `SubjectDevice`
1. Edytuj akcję odpowiedzi na mowę, aby użyć `OnOff` i `SubjectDevice`:

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Wypróbuj

Otwórz panel rozmowy testowej i spróbuj wykonać kilka interakcji.

- Dane wejściowe: wyłączanie telewizora
- Wynik: OK, wyłączanie telewizora

- Wejście: wyłączanie telewizji
- Wynik: OK, wyłączanie telewizora

- Wejście: wyłączanie
- Dane wyjściowe: które urządzenie?
- Dane wejściowe: telewizja
- Wynik: OK, wyłączanie telewizora

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Nawiązywanie połączenia z aplikacją niestandardową za pomocą zestawu Speech SDK (wersja zapoznawcza)](./quickstart-custom-speech-commands-speech-sdk.md)
