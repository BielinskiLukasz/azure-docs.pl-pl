---
title: Przeprowadzić migrację obszaru roboczego i projektów centrum usługi Microsoft Translator? — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak przeprowadzić migrację obszaru roboczego i projektów centrum do usługi Azure Cognitive Services Custom translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 7ea7b48e1fc36399a0ca173f9068faf8b88849d5
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836580"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrowanie obszaru roboczego i projektów centrum do translatora niestandardowego

Można z łatwością migrować obszar roboczy i projekty [centrum usługi Microsoft Translator](https://hub.microsofttranslator.com/) do translatora niestandardowego. Migracja jest inicjowana z centrum Microsoft Hub przez wybranie obszaru roboczego lub projektu, a następnie wybranie obszaru roboczego w usłudze translator niestandardowy, a następnie wybranie szkoleń, które chcesz przenieść. Po rozpoczęciu migracji wybrane ustawienia szkoleniowe zostaną przeniesione ze wszystkimi odpowiednimi dokumentami. Wdrożone modele są przeszkolone i można je wdrożyć ponownie po zakończeniu.

Te akcje są wykonywane podczas migracji:
* Wszystkie dokumenty i definicje projektu będą przenoszone wraz z dodaniem prefiksu "hub_" poprzedzonego nazwą. Automatycznie generowane testy i dane dostrajania będą mieć nazwę hub_systemtune_\<ModelId > lub hub_systemtest_\<ModelId >.
* Wszystkie szkolenia, które były w stanie wdrożenia podczas migracji, zostaną automatycznie przeszkolone przy użyciu dokumentów szkoleń centrum. W przypadku tego szkolenia nie zostanie naliczona opłata za subskrypcję. Jeśli wybrano opcję automatycznego wdrażania dla migracji, model przeszkolony zostanie wdrożony po zakończeniu. Będą stosowane zwykłe opłaty za hosting.
* Wszystkie zmigrowane szkolenia, które nie były w stanie wdrożonym, zostaną wprowadzone do migrowanej wersji roboczej. W tym stanie będziesz mieć możliwość szkolenia modelu z zmigrowanym definicją, ale będą stosowane zwykłe opłaty szkoleniowe.
* W dowolnym momencie Ocena BLEU migrowana z szkoleń centrum znajduje się na stronie TrainingDetails modelu w nagłówku "Bleu score in MT Hub".

> [!Note] 
> Aby szkolenie zakończyło się pomyślnie, translator niestandardowy wymaga co najmniej 10 000 unikatowych rozpakowanych zdań. Translator niestandardowy nie może przeprowadzić szkolenia z mniejszą ilością niż [Sugerowana wartość minimalna](sentence-alignment.md#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Znajdowanie identyfikatora obszaru roboczego usługi tłumaczenia niestandardowego

Aby przeprowadzić migrację obszaru roboczego [centrum usługi Microsoft Translator](https://hub.microsofttranslator.com/) , wymagany jest identyfikator docelowego obszaru roboczego w usłudze translator niestandardowym. Docelowy obszar roboczy w usłudze translator niestandardowy to miejsce, w którym zostaną zmigrowane wszystkie obszary robocze i projekty centrum.

Identyfikator docelowego obszaru roboczego znajdziesz na stronie niestandardowe ustawienia translatora:

1. Przejdź do strony "ustawienie" w portalu translatora niestandardowego.

2. Identyfikator obszaru roboczego znajduje się w sekcji podstawowe informacje.

    ![Jak znaleźć identyfikator docelowego obszaru roboczego](media/how-to/how-to-find-destination-ws-id.png)

3. Zachowaj identyfikator docelowego obszaru roboczego do odwoływania się podczas procesu migracji.

## <a name="migrate-a-project"></a>Migrowanie projektu

Jeśli chcesz przeprowadzić migrację projektów selektywnie, centrum usługi Microsoft Translator zapewnia tę możliwość.

Aby przeprowadzić migrację projektu:

1. Zaloguj się do usługi Microsoft Translator Hub.

2. Przejdź do strony "projects" (projekty).

3. Kliknij link "Migruj" dla odpowiedniego projektu.

    ![Jak przeprowadzić migrację z centrum](media/how-to/how-to-migrate-from-hub.png)

4. Po naciśnięciu linku migracji zostanie wyświetlony formularz umożliwiający:
   * Określ obszar roboczy, który chcesz przenieść do translatora niestandardowego
   * Wskaż, czy chcesz przenieść wszystkie szkolenia z pomyślnymi szkoleniami, czy tylko wdrożone szkolenia. Domyślnie wszystkie pomyślne szkolenia zostaną przeniesione.
   * Wskaż, czy chcesz, aby Twoje szkolenie zostało wdrożone w przypadku zakończenia szkolenia. Domyślnie szkolenie nie zostanie wdrożone po zakończeniu.

5. Kliknij pozycję "Prześlij żądanie".

## <a name="migrate-a-workspace"></a>Migrowanie obszaru roboczego

Oprócz migracji pojedynczego projektu można także migrować wszystkie projekty z pomyślnymi szkoleniami w obszarze roboczym. Spowoduje to, że każdy projekt w obszarze roboczym będzie oceniany tak, jakby został naciśnięty link Migrowanie. Ta funkcja jest odpowiednia dla użytkowników z wieloma projektami, którzy chcą migrować wszystkie z nich do translatora niestandardowego z tymi samymi ustawieniami. Migrację obszaru roboczego można zainicjować ze strony Ustawienia w centrum usługi Translator.

Aby przeprowadzić migrację obszaru roboczego:

1. Zaloguj się do usługi Microsoft Translator Hub.

2. Przejdź do strony "Ustawienia".

3. Na stronie "Ustawienia" kliknij pozycję "Migruj dane obszaru roboczego do translatora niestandardowego".

    ![Jak przeprowadzić migrację z centrum](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Na następnej stronie wybierz jedną z następujących opcji:

    a. Wdrożone tylko szkolenia: wybranie tej opcji spowoduje Migrowanie tylko wdrożonych systemów i powiązanych dokumentów.

    b. Wszystkie pomyślne szkolenia: wybranie tej opcji spowoduje migrację wszystkich pomyślnych szkoleń i powiązanych dokumentów.

    d. Wprowadź identyfikator docelowego obszaru roboczego w usłudze translator niestandardowym.

    ![Jak przeprowadzić migrację z centrum](media/how-to/how-to-migrate-from-hub-screen.png)

5. Kliknij pozycję Prześlij żądanie.

## <a name="migration-history"></a>Historia migracji

Po zażądaniu migracji obszaru roboczego/projektu z centrum można znaleźć historię migracji na stronie ustawień translatora niestandardowego.

Aby wyświetlić historię migracji, wykonaj następujące kroki:

1. Przejdź do strony "ustawienie" w portalu translatora niestandardowego.

2. W sekcji Historia migracji na stronie Ustawienia kliknij pozycję Historia migracji.

    ![Historia migracji](media/how-to/how-to-migration-history.png)

Na stronie Historia migracji są wyświetlane następujące informacje jako podsumowania dla każdej żądanej migracji.

1. Zmigrowane przez: Nazwa i adres e-mail użytkownika przesłanego tego żądania migracji

2. Data migracji: Sygnatura daty i godziny migracji

3. Projekty: liczba projektów zleconych do migracji liczba projektów zakończonych powodzeniem.

4. Szkolenia: liczba pożądanych szkoleń w zakresie migracji w przypadku pomyślnego przeskanowania liczby przeszkolonych szkoleń.

5. Dokumenty: liczba dokumentów zażądanych do migracji Liczba dokumentów, które zostały pomyślnie zmigrowane.

    ![Szczegóły historii migracji](media/how-to/how-to-migration-history-details.png)

Jeśli chcesz uzyskać bardziej szczegółowy raport migracji o projektach, szkoleniach i dokumentach, możesz wybrać opcję Eksportuj szczegóły jako plik CSV.

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji
* Systemy z parami języka, które nie są jeszcze dostępne w usłudze translatora niestandardowego, będą dostępne tylko w celu uzyskania dostępu do danych lub ich rozmieszczenia za pomocą translatora niestandardowego. Te projekty zostaną oznaczone jako "niedostępne" na stronie projekty. Po włączeniu nowych par językowych przy użyciu translatora niestandardowego projekty staną się aktywne do uczenia i wdrożenia. 
* Migrowanie projektu z centrum do translatora niestandardowego nie będzie miało żadnego wpływu na szkolenia lub projekty w centrum. Nie są usuwane projekty ani dokumenty z centrum podczas migracji i nie są dewdrażane modele.
* Migracja jest dozwolona tylko raz dla każdego projektu. Jeśli musisz powtórzyć migrację projektu, skontaktuj się z nami.
* Translator niestandardowy obsługuje pary językowe NMT do i z języka angielskiego. [Zapoznaj się z pełną listą obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Centrum nie wymaga modeli bazowych i w związku z tym obsługuje kilka tysięcy języków. Można migrować nieobsługiwaną parę języka, ale tylko przeprowadzimy migrację dokumentów i definicji projektu. Nie będziemy mogli nauczyć się nowego modelu. Ponadto te dokumenty i projekty będą wyświetlane jako nieaktywne w celu wskazania, że nie mogą być używane w tym momencie. Jeśli dla tych projektów i/lub dokumentów zostanie dodana obsługa, staną się one aktywne i przeszkolene.
* Usługa Custom Translator nie obsługuje obecnie danych szkoleniowych dotyczących wielojęzycznych. Podobnie jak w przypadku nieobsługiwanych par językowych, można migrować dokumenty w języku, ale są one wyświetlane jako nieaktywne do momentu, w którym jest obsługiwane dane.
* W celu uczenia się niestandardowym translatorem wymagane są 10 równoległych zdań. Centrum firmy Microsoft może nauczyć się na mniejszym zestawie danych. Jeśli przeprowadzono migrację szkoleń, która nie spełnia tego wymagania, nie zostanie ona przeszkolony.

## <a name="custom-translator-versus-hub"></a>Translator niestandardowy a centrum

Ta tabela zawiera porównanie funkcji usługi Microsoft Translator Hub i translatora niestandardowego.

|   | Centralny | Custom Translator |
|:-----|:----:|:----:|
|Stan funkcji dostosowywania   | Ogólna dostępność  | Ogólna dostępność |
| Wersja interfejsu API tekstu  | Wersja 2    | Czytanie  |
| Dostosowanie SMT | Tak   | Nie |
| Dostosowanie NMT | Nie    | Tak |
| Nowe ujednolicone usługi rozpoznawania mowy | Nie    | Tak |
| Bez śledzenia | Tak | Tak |

## <a name="new-languages"></a>Nowe języki

Jeśli jesteś społecznością lub organizacją, nad tworzeniem nowego systemu językowego dla usługi Microsoft translator, skontaktuj się z [custommt@microsoft.com](mailto:custommt@microsoft.com) , aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](how-to-train-model.md).
- Zacznij korzystać ze wdrożonego niestandardowego modelu tłumaczenia za pośrednictwem [programu Microsoft interfejs API tłumaczenia tekstu w usłudze translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
