---
title: 'Samouczek: używanie sesji debugowania do diagnozowania, naprawiania i zatwierdzania zmian w zestawu umiejętności'
titleSuffix: Azure Cognitive Search
description: Sesje debugowania (wersja zapoznawcza) zapewniają interfejs oparty na portalu do oceny i naprawy problemów/błędów w umiejętności
author: tchristiani
ms.author: terrychr
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 2f25cab211d24facea7863c15b6d7671a9657ae9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290682"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Samouczek: diagnozowanie, naprawianie i zatwierdzanie zmian w zestawu umiejętności

Ten artykuł zawiera Azure Portal do uzyskiwania dostępu do sesji debugowania w celu naprawy problemów z podaną zestawu umiejętności. Zestawu umiejętności zawiera błędy, które należy rozwiązać. Ten samouczek przeprowadzi Cię przez sesję debugowania, aby identyfikować i rozwiązywać problemy związane z danymi wejściowymi i wyjściowymi.

> [!Important]
> Sesje debugowania to funkcja w wersji zapoznawczej, która jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) lub Użyj bieżącej subskrypcji
> * Wystąpienie usługi Azure Wyszukiwanie poznawcze
> * Konto usługi Azure Storage
> * [Aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Tworzenie usług i ładowanie danych

W tym samouczku jest stosowana usługa Azure Wyszukiwanie poznawcze i usługi Azure Storage.

* [Pobierz przykładowe dane](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) składające się z 19 plików.

* [Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) lub [Znajdź istniejące konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Wybierz ten sam region co usługa Azure Wyszukiwanie poznawcze, aby uniknąć naliczania opłat za przepustowość.
   
   Wybierz typ konta StorageV2 (ogólnego przeznaczenia w wersji 2).

* Otwórz strony usługi Storage i Utwórz kontener. Najlepszym rozwiązaniem jest określenie poziomu dostępu "Private". Nazwij kontener `clinicaltrialdataset` .

* W kontenerze kliknij pozycję **Przekaż** , aby przekazać pliki przykładowe pobrane i rozpakowane w pierwszym kroku.

* [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="create-data-source-skillset-index-and-indexer"></a>Tworzenie źródła danych, zestawu umiejętności, indeksu i indeksatora

W tej sekcji, program ogłaszający i poświadczona kolekcja są używane do tworzenia źródła danych, zestawu umiejętności, indeksu i indeksatora usługi wyszukiwania.

1. Jeśli nie masz narzędzia do księgowania, możesz [pobrać aplikację klasyczną](https://www.getpostman.com/).
1. [Pobierz kolekcję sesji debugowania.](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Uruchom notkę
1. W obszarze **pliki**  >  **nowe**wybierz kolekcję do zaimportowania.
1. Po zaimportowaniu kolekcji rozwiń listę akcje (...).
1. Kliknij pozycję **Edytuj**.
1. Wprowadź nazwę searchService (na przykład, jeśli punkt końcowy to `https://mydemo.search.windows.net` , nazwa usługi to " `mydemo` ").
1. Wprowadź apiKey z kluczem podstawowym lub pomocniczym usługi wyszukiwania.
1. Wprowadź storageConnectionString na stronie klucze konta usługi Azure Storage.
1. Wprowadź ContainerName dla kontenera utworzonego na koncie magazynu.

> [!div class="mx-imgBorder"]
> ![Edytuj zmienne w programie Poster](media/cognitive-search-debug/postman-enter-variables.png)

Kolekcja zawiera cztery różne wywołania REST, które są używane do ukończenia tej sekcji.

Pierwsze wywołanie tworzy źródło danych. `clinical-trials-ds`. Drugie wywołanie tworzy zestawu umiejętności, `clinical-trials-ss` . Trzecie wywołanie tworzy indeks, `clinical-trials` . Połączenie czwarte i końcowe tworzy indeksator, `clinical-trials-idxr` . Po zakończeniu wszystkich wywołań w kolekcji Zamknij program Poster i wróć do Azure Portal.

> [!div class="mx-imgBorder"]
> ![Tworzenie źródła danych przy użyciu programu Poster](media/cognitive-search-debug/postman-create-data-source.png)

## <a name="check-the-results"></a>Sprawdź wyniki

Zestawu umiejętności zawiera kilka typowych błędów. W tej sekcji uruchomienie pustego zapytania w celu zwrócenia wszystkich dokumentów spowoduje wyświetlenie wielu błędów. W kolejnych krokach problemy zostaną rozwiązane przy użyciu sesji debugowania.

1. Przejdź do usługi wyszukiwania w Azure Portal. 
1. Wybierz kartę **indeksy** . 
1. Wybierz `clinical-trials` indeks
1. Kliknij przycisk **Wyszukaj** , aby uruchomić puste zapytanie. 

Po zakończeniu wyszukiwania dwa pola bez danych na liście; "organizacje" i "lokalizacje" są wymienione w oknie. Postępuj zgodnie z instrukcjami, aby odnaleźć wszystkie problemy wygenerowane przez zestawu umiejętności.

1. Wróć do strony Przegląd usługi wyszukiwania.
1. Wybierz kartę **indeksatory** . 
1. Kliknij `clinical-trials-idxr` i wybierz powiadomienie z ostrzeżeniami. 

Istnieje wiele problemów z mapowaniami pól danych wyjściowych projekcji i na stronie trzy istnieją ostrzeżenia, ponieważ co najmniej jedno dane wejściowe dotyczące umiejętności są nieprawidłowe.

Wróć do ekranu przegląd usługi wyszukiwania.

## <a name="start-your-debug-session"></a>Rozpocznij sesję debugowania

> [!div class="mx-imgBorder"]
> ![Rozpocznij nową sesję debugowania](media/cognitive-search-debug/new-debug-session-screen-required.png)

1. Kliknij kartę sesje debugowania (wersja zapoznawcza).
1. Wybierz pozycję + NewDebugSession
1. Nadaj nazwę sesji. 
1. Połącz sesję z kontem magazynu. 
1. Podaj nazwę indeksatora. Indeksator zawiera odwołania do źródła danych, zestawu umiejętności i index.
1. Zaakceptuj wybór dokumentu domyślnego dla pierwszego dokumentu w kolekcji. 
1. **Zapisz** sesję. Zapisanie sesji spowoduje uruchomienie potoku wzbogacenia AI zgodnie z definicją zestawu umiejętności.

> [!Important]
> Sesja debugowania działa tylko z jednym dokumentem. Określony dokument w zestawie danych może zostać wybrany > lub sesja zostanie domyślnie ustawiona na pierwszy dokument.

> [!div class="mx-imgBorder"]
> ![Rozpoczęto nową sesję debugowania](media/cognitive-search-debug/debug-execution-complete1.png)

Po zakończeniu wykonywania sesji debugowania wartość domyślna sesji na karcie wzbogacanie AI jest wyświetlona.

+ Wykres umiejętności zawiera wizualną hierarchię zestawu umiejętności i jej kolejność wykonywania od góry do dołu. Umiejętności, które są obok siebie w grafie, są wykonywane równolegle. Kolorowe kodowanie umiejętności na grafie wskazuje typy umiejętności, które są wykonywane w zestawu umiejętności. W tym przykładzie Zielona umiejętność to tekst, a czerwona umiejętność to wizja. Kliknięcie poszczególnych umiejętności na grafie spowoduje wyświetlenie szczegółów tego wystąpienia umiejętności w prawym okienku okna sesji. Ustawienia umiejętności, Edytor JSON, szczegóły wykonania oraz błędy/ostrzeżenia są dostępne do przeglądu i edycji.
+ Ulepszona struktura danych zawiera szczegóły węzłów w drzewie wzbogacania wygenerowanych przez umiejętności z zawartości dokumentu źródłowego.

Karta błędy/ostrzeżenia dostarczy znacznie mniejszej listy niż ta, która jest wyświetlana wcześniej, ponieważ ta lista zawiera tylko szczegóły błędów dla pojedynczego dokumentu. Podobnie jak lista wyświetlana przez indeksator, można kliknąć komunikat ostrzegawczy i wyświetlić szczegóły tego ostrzeżenia.

## <a name="fix-missing-skill-input-value"></a>Popraw brakującą wartość wejściową kwalifikacji

Na karcie błędy/ostrzeżenia występuje błąd dla operacji oznaczonej etykietą `Enrichment.NerSkillV2.#1` . Szczegóły tego błędu wyjaśniają, że występuje problem z wartością wejściową "/document/languageCode". 

1. Wróć do karty wzbogacania AI.
1. Kliknij **Wykres umiejętności**.
1. Kliknij umiejętność z etykietą #1, aby wyświetlić jej szczegóły w okienku po prawej stronie.
1. Znajdź dane wejściowe dla "languageCode".
1. Wybierz **</>** symbol na początku wiersza i Otwórz ewaluatora wyrażeń.
1. Kliknij przycisk **Oceń** , aby potwierdzić, że to wyrażenie powoduje wystąpienie błędu. Upewnij się, że właściwość "languageCode" nie jest prawidłowym danymi wejściowymi.

> [!div class="mx-imgBorder"]
> ![Ewaluator wyrażeń](media/cognitive-search-debug/expression-evaluator-language.png)

Istnieją dwa sposoby badania tego błędu w sesji. Pierwszy polega na tym, że dane wejściowe pochodzą z-jakie umiejętności w hierarchii powinny zostać wygenerowane? Na karcie wykonania w okienku Szczegóły umiejętności powinna zostać wyświetlona wartość źródła danych wejściowych. Jeśli nie ma źródła, oznacza to błąd mapowania pola.

1. Kliknij kartę **wykonania** .
1. Spójrz na dane wejściowe i Znajdź "languageCode". Brak źródła dla tego danych wejściowych wymienionych. 
1. Przełącz okienko po lewej stronie, aby wyświetlić ulepszoną strukturę danych. Nie ma zmapowanej ścieżki odpowiadającej elementowi "languageCode".

> [!div class="mx-imgBorder"]
> ![Ulepszona struktura danych](media/cognitive-search-debug/enriched-data-structure-language.png)

Istnieje zmapowana ścieżka dla "języka". W związku z tym w ustawieniach umiejętności występuje literówka. Aby rozwiązać ten problem, należy zaktualizować wyrażenie w #1 kwalifikacje z wyrażeniem "/Document/Language".

1. Otwórz ewaluatora wyrażeń **</>** dla ścieżki "Language" (język).
1. Skopiuj wyrażenie. Zamknij okno.
1. Przejdź do ustawień umiejętności #1 i Otwórz ewaluatora wyrażeń **</>** dla danych wejściowych "languageCode".
1. Wklej nową wartość "/Document/Language" w polu wyrażenie i kliknij pozycję **Oceń**.
1. Powinien on zawierać poprawne dane wejściowe "pl". Kliknij przycisk Zastosuj, aby zaktualizować wyrażenie.
1. Kliknij przycisk **Zapisz** w prawym okienku szczegółów umiejętności.
1. W menu okna sesji kliknij polecenie **Uruchom** . Spowoduje to uruchomienie innego wykonywania zestawu umiejętności przy użyciu dokumentu. 

Po zakończeniu wykonywania sesji debugowania kliknij kartę błędy/ostrzeżenia, aby zobaczyć, że wystąpił błąd z etykietą "wzbogacanie. NerSkillV2. #1". Jednak nadal istnieją dwa ostrzeżenia, które usługa nie może zmapować pól wyjściowych dla organizacji i lokalizacji do indeksu wyszukiwania. Brak wartości: "/Document/merged_content/Organizations" i "/Document/merged_content/Locations".

## <a name="fix-missing-skill-output-values"></a>Popraw brakujące wartości wyjściowe kwalifikacji

> [!div class="mx-imgBorder"]
> ![Błędy i ostrzeżenia](media/cognitive-search-debug/warnings-missing-value-locations-organizations.png)

Brak wartości wyjściowych z umiejętności. Aby zidentyfikować umiejętność z błędem, przejdź do wzbogaconej struktury danych, Znajdź nazwę wartości i sprawdź jej pierwotne źródło. W przypadku brakujących wartości organizacji i lokalizacji są one wyprowadzane z #1 umiejętności. Otwarcie < ewaluatora wyrażeń/> dla każdej ścieżki spowoduje wyświetlenie odpowiednio wyrażeń wymienionych jako "/Document/Content/Organizations" i "/Document/Content/Locations".

> [!div class="mx-imgBorder"]
> ![Jednostka organizacji ewaluatora wyrażeń](media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png)

Dane wyjściowe dla tych jednostek są puste i nie powinny być puste. Jakie są dane wejściowe tego wyniku?

1. Przejdź do **wykresu umiejętności** i wybierz pozycję kwalifikacje #1.
1. Wybierz kartę **wykonania** w prawym okienku szczegółów umiejętności.
1. Otwórz ewaluatora wyrażeń **</>** dla danych wejściowych "text".

> [!div class="mx-imgBorder"]
> ![Dane wejściowe dotyczące kwalifikacji tekstu](media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png)

Wyświetlany wynik dla tego danych wejściowych nie wygląda jak tekst wejściowy. Wygląda podobnie do obrazu, który jest ujęty w nowe wiersze. Brak tekstu oznacza, że nie można zidentyfikować żadnych jednostek. Spojrzenie na hierarchię zestawu umiejętności wyświetla zawartość, która jest najpierw przetwarzana przez umiejętność #6 (OCR), a następnie przenoszona do umiejętności #5 (merge). 

1. Wybierz umiejętność #5 (merge) na **grafie umiejętności**.
1. Wybierz kartę **wykonania** w prawym okienku szczegółów umiejętności i Otwórz ewaluatora wyrażeń **</>** dla danych wyjściowych "mergedText".

> [!div class="mx-imgBorder"]
> ![Dane wyjściowe dla umiejętności scalania](media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png)

W tym miejscu tekst jest sparowany z obrazem. Spojrzenie na wyrażenie "/Document/merged_content" powoduje błąd w ścieżkach "Organizations" i "Locations" dla #1 umiejętności. Zamiast używać elementu "/Document/Content", powinien on używać elementu "/Document/merged_content" dla danych wejściowych "text".

1. Skopiuj wyrażenie dla danych wyjściowych "mergedText" i Zamknij okno ewaluatora wyrażeń.
1. Wybierz pozycję umiejętność #1 na **grafie umiejętności**.
1. Wybierz kartę **Ustawienia umiejętności** w prawym okienku szczegółów umiejętności.
1. Otwórz ewaluatora wyrażeń **</>** dla danych wejściowych "text".
1. Wklej nowe wyrażenie do pola. Kliknij pozycję **Oceń**.
1. Należy wyświetlić poprawne dane wejściowe z dodanym tekstem. Kliknij przycisk **Zastosuj** , aby zaktualizować ustawienia umiejętności.
1. Kliknij przycisk **Zapisz** w prawym okienku szczegółów umiejętności.
1. W menu okna sesje kliknij polecenie **Uruchom** . Spowoduje to uruchomienie innego wykonywania zestawu umiejętności przy użyciu dokumentu.

Po zakończeniu działania indeksatora te błędy nadal są dostępne. Wróć do umiejętności #1 i zbadaj. Dane wejściowe dotyczące umiejętności zostały poprawione na "merged_content" z "Content". Jakie są dane wyjściowe tych jednostek w ramach umiejętności?

1. Wybierz kartę **wzbogacanie AI** .
1. Wybierz **Wykres umiejętności** i kliknij #1 umiejętności.
1. Przejdź do **ustawień umiejętności** , aby znaleźć "dane wyjściowe".
1. Otwórz ewaluatora wyrażeń **</>** dla jednostki "organizacje".

> [!div class="mx-imgBorder"]
> ![Dane wyjściowe dla jednostki organizacji](media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png)

Obliczenie wyniku wyrażenia daje prawidłowy wynik. Umiejętność działania umożliwia zidentyfikowanie poprawnej wartości dla jednostki "organizacje". Jednak mapowanie danych wyjściowych w ścieżce jednostki nadal zgłasza błąd. W porównaniu ze ścieżką wyjściową w polu umiejętność do ścieżki wyjściowej błędu, umiejętność nadrzędna danych wyjściowych, organizacji i lokalizacji w węźle/Document/Content. Chociaż mapowanie pola wyjściowego oczekuje, że wyniki mają być nadrzędne w węźle merged_content/Document/. W poprzednim kroku dane wejściowe zmieniły się z "/Document/Content" na "/Document/merged_content". Kontekst w ustawieniach umiejętności należy zmienić, aby upewnić się, że dane wyjściowe są generowane z odpowiednim kontekstem.

1. Wybierz kartę **wzbogacanie AI** .
1. Wybierz **Wykres umiejętności** i kliknij #1 umiejętności.
1. Przejdź do **ustawień umiejętności** , aby znaleźć "kontekst".
1. Kliknij dwukrotnie ustawienie "context" i edytuj je, aby odczytać "/Document/merged_content".
1. Kliknij przycisk **Zapisz** w prawym okienku szczegółów umiejętności.
1. W menu okna sesje kliknij polecenie **Uruchom** . Spowoduje to uruchomienie innego wykonywania zestawu umiejętności przy użyciu dokumentu.

> [!div class="mx-imgBorder"]
> ![Korekta kontekstu w ustawieniu umiejętności](media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png)

Wszystkie błędy zostały rozwiązane.

## <a name="commit-changes-to-the-skillset"></a>Zatwierdź zmiany w zestawu umiejętności

Po zainicjowaniu sesji debugowania usługa wyszukiwania utworzyła kopię zestawu umiejętności. Zostało to zrobione, dlatego wprowadzone zmiany nie wpłyną na system produkcyjny. Po zakończeniu debugowania zestawu umiejętności można zatwierdzić poprawki (zastąpić oryginalny zestawu umiejętności) w systemie produkcyjnym. Jeśli chcesz kontynuować wprowadzanie zmian do zestawu umiejętności bez wpływu na system produkcyjny, sesja debugowania może zostać zapisana i ponownie otwarta.

1. Kliknij pozycję **Zatwierdź zmiany** w głównym menu sesji debugowania.
1. Kliknij przycisk **OK** , aby potwierdzić, że chcesz zaktualizować zestawu umiejętności.
1. Zamknij sesję debugowania i wybierz kartę **indeksatory** .
1. Otwórz "badania kliniczne-idxr".
1. Kliknij przycisk **Resetuj**.
1. Kliknij przycisk **Uruchom**. Kliknij przycisk **OK** , aby potwierdzić.

Gdy indeksator zakończył działanie, powinien istnieć zielony znacznik wyboru, a słowo "powodzenie" obok sygnatury czasowej dla ostatniego uruchomienia na karcie Historia wykonywania. Aby upewnić się, że zmiany zostały zastosowane:

1. Zamknij **indeksator** i wybierz kartę **indeks** .
1. Otwórz indeks "badania kliniczne", a następnie na karcie Eksplorator wyszukiwania kliknij pozycję **Wyszukaj**.
1. W oknie wyników należy pokazać, że organizacje i lokalizacje obiektów są teraz wypełniane oczekiwanymi wartościami.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub jako grupę zasobów, usuwając cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o umiejętności](https://docs.microsoft.com/azure/search/cognitive-search-working-with-skillsets) 
>  [Dowiedz się więcej o zwiększeniu liczby przyrostowej i buforowaniu](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)