---
title: 'Instrukcje: Tworzenie aplikacji za pomocą prostych poleceń — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak tworzyć i testować hostowaną aplikację poleceń niestandardowych przy użyciu prostych poleceń.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 7f98b2c94659c083f5dbd2c43fd9015f8b60a9db
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307653"
---
# <a name="create-application-with-simple-commands"></a>Tworzenie aplikacji przy użyciu prostych poleceń

W tym artykule omówiono sposób wykonywania następujących zadań:
 - Utwórz pustą aplikację
 - Aktualizowanie zasobów LUIS
 - Dodaj podstawowe polecenia do aplikacji poleceń niestandardowych

## <a name="create-empty-application"></a>Utwórz pustą aplikację
Utwórz pustą aplikację poleceń niestandardowych. Aby uzyskać szczegółowe informacje, zapoznaj się z [przewodnikiem Szybki Start](quickstart-custom-commands-application.md). Tym razem zamiast importowania projektu, należy utworzyć pusty projekt.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie projektu](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>Aktualizowanie zasobów LUIS (opcjonalnie)

Możesz zaktualizować zasób tworzenia wybrany w oknie **Nowy projekt** , a następnie ustawić zasób przewidywania. Zasób predykcyjny jest używany do rozpoznawania podczas publikowania aplikacji poleceń niestandardowych. Nie potrzebujesz zasobów predykcyjnych w fazach tworzenia i testowania.

## <a name="add-turnon-command"></a>Dodaj polecenie wlaczania

W aplikacji polecenia niestandardowe o **pokoju inteligentnym** Dodaj proste polecenie, które przetwarza wypowiedź, `turn on the tv` i odpowiadaj na komunikat `Ok, turning the tv on` .

1. Utwórz nowe polecenie, wybierając pozycję **nowe** w górnej części okienka po lewej stronie. Zostanie otwarte **nowe okno polecenia** .
1. Podaj wartość dla pola **Nazwa** jako **wlaczac**.
1. Wybierz przycisk **Utwórz**.

W środkowym okienku wyświetlane są różne właściwości polecenia. Należy skonfigurować następujące właściwości polecenia. Aby uzyskać wyjaśnienie wszystkich właściwości konfiguracji polecenia, przejdź do pozycji [odwołania](./custom-commands-references.md).

| Konfiguracja            | Opis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Przykładowe zdania** | Przykład wyrażenia długości, że użytkownik może powiedzieć, aby wyzwolić to polecenie                                                                 |
| **Parametry**       | Informacje wymagane do wykonania polecenia                                                                                |
| **Reguły uzupełniania** | Akcje, które należy wykonać, aby zrealizować polecenie. Na przykład, aby odpowiedzieć użytkownikowi lub komunikować się z inną usługą sieci Web. |
| **Reguły interakcji**   | Dodatkowe reguły do obsługi bardziej szczegółowych lub złożonych sytuacji                                                              |


> [!div class="mx-imgBorder"]
> ![Utwórz polecenie](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Dodaj przykładowe zdania

Zacznijmy od sekcji **przykładowe zdania** i podaj przykład, co użytkownik może powiedzieć.

1. Wybierz sekcję **przykładowe zdania** w środkowym okienku.
1. W najbardziej prawym okienku Dodaj przykłady:

    ```
    turn on the tv
    ```

1.  Wybierz pozycję **Zapisz** w górnej części okienka.

Na razie nie mamy parametrów, więc można przejść do sekcji **reguł ukończenia** .

### <a name="add-a-completion-rule"></a>Dodawanie reguły uzupełniania

Następnie polecenie musi mieć regułę uzupełniania. Ta reguła instruuje użytkownika, że jest wykonywana akcja realizacji. Aby dowiedzieć się więcej na temat reguł i reguł ukończenia, przejdź do pozycji [odwołania](./custom-commands-references.md).

1. Wybierz opcję **domyślna reguła** uzupełniania i edytuj ją w następujący sposób: 

    
    | Ustawienie    | Sugerowana wartość                          | Opis                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nazwa**       | ConfirmationResponse                  | Nazwa opisująca przeznaczenie reguły          |
    | **Warunki** | Brak                                     | Warunki określające, kiedy można uruchomić regułę    |
    | **Akcje**    | Wysyłaj odpowiedzi na mowę > prosty edytor > pierwszej wariacji >`Ok, turning the tv on` | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |
    

1. Alternatywnie możesz usunąć istniejącą domyślną regułę uzupełniania i utworzyć nową, wybierając pozycję **Dodaj** w górnej części środkowego okienka.
1. Podaj wartość w sekcji **Nazwa** .
1. Dodaj akcję.
   1. Utwórz akcję, wybierając pozycję **Dodaj akcję** w sekcji **Akcje** .
   1. W oknie **Nowa akcja** na liście **Typ** wybierz pozycję **Wyślij odpowiedź na mowę**.
   1. W obszarze **odpowiedź**wybierz pozycję **prosty edytor**.
   1. W **pierwszym polu wariacja** podaj wartość odpowiedzi jako `Ok, turning the tv on` .

   > [!div class="mx-imgBorder"]
   > ![Tworzenie odpowiedzi na mowę](media/custom-commands/create-speech-response-action.png)

1. Wybierz pozycję **Zapisz** , aby zapisać akcję.
1. Wróć do sekcji **reguły uzupełniania** , wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany. 


### <a name="try-it-out"></a>Wypróbowywanie działania

Testowanie zachowania przy użyciu panelu rozmowy testowej
1. Wybierz ikonę **szkolenia** znajdującą się u góry okienka po prawej stronie.
1. Po zakończeniu szkolenia wybierz pozycję **test**. Wypróbuj następujący wypowiedź za pośrednictwem głosu/tekstu:
    - Wejście: Włączanie telewizora
    - Wynik: OK, Włączanie telewizora


> [!div class="mx-imgBorder"]
> ![Testowanie za pomocą rozmowy w sieci Web](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> W panelu test można wybrać pozycję **Zmień szczegóły** , aby uzyskać informacje dotyczące sposobu przetwarzania danych wejściowych dźwięku/tekstu.  

## <a name="add-settemperature-command"></a>Dodaj polecenie settemperaturę

Teraz Dodaj jeszcze jedno polecenie **Settemperaturę** , które będzie miało pojedynczy wypowiedź `set the temperature to 40 degrees` i odpowiedzieć na komunikat `Ok, setting temperature to 40 degrees` .

Postępuj zgodnie z instrukcjami dla polecenia **wlaczaj** , aby utworzyć nowe polecenie za pomocą przykładowego zdania "**Ustaw temperaturę na 40 stopni**".

Następnie edytuj istniejące **gotowe** reguły ukończenia w następujący sposób:

| Ustawienie    | Sugerowana wartość                          |
| ---------- | ---------------------------------------- |
| Nazwa  | ConfirmationResponse                  |
| Warunki | Brak                                     |
| Akcje    | Wysyłaj odpowiedzi na mowę > prosty edytor > pierwszej wariacji >`Ok, setting temperature to 40 degrees` |

Wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany w poleceniu.

## <a name="add-setalarm-command"></a>Dodaj polecenie setAlarm
Utwórz nowe polecenie **setAlarm** przy użyciu przykładowego zdania "**Ustaw alarm dla 9 am**". Następnie edytuj istniejące **gotowe** reguły ukończenia w następujący sposób:

| Ustawienie    | Sugerowana wartość                          |
| ---------- | ---------------------------------------- |
| Nazwa reguły  | ConfirmationResponse                  |
| Warunki | Brak                                     |
| Akcje    | Wysyłaj odpowiedzi na mowę > prosty edytor > pierwszej wariacji >`Ok, setting an alarm for 9 am tomorrow` |

Wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany w poleceniu.

## <a name="try-it-out"></a>Wypróbowywanie działania

Testowanie zachowania przy użyciu panelu rozmowy testowej
1. Wybierz pozycję **uczenie**. Po pomyślnym zakończeniu szkolenia wybierz pozycję **Testuj** i wypróbować:
    - Wpisz: Ustaw temperaturę na 40 stopni
    - Oczekiwana odpowiedź: OK, ustawianie temperatury na 40 stopni
    - Wpisywanie: Włączanie telewizora
    - Oczekiwana odpowiedź: OK, Włączanie telewizora
    - Wpisz: ustaw alarm dla 9 am/jutro
    - Oczekiwana odpowiedź: OK, Ustawianie alarmu dla 9 am/jutro

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie parametrów do poleceń](./how-to-custom-commands-add-parameters-to-commands.md)
