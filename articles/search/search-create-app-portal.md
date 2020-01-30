---
title: Tworzenie aplikacji wyszukiwania w Azure Portal
titleSuffix: Azure Cognitive Search
description: Uruchom Kreatora tworzenia aplikacji (wersja zapoznawcza), aby wygenerować strony HTML i skrypt dla działającej aplikacji sieci Web. Strona zawiera pasek wyszukiwania, obszar wyników, pasek boczny i pomoc techniczną typeahead.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 9ed62ea6fdfc0732128ed340cc0bc48481eb73ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841084"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Szybki Start: Tworzenie aplikacji wyszukiwania w portalu (Azure Wyszukiwanie poznawcze)

Użyj kreatora **tworzenia aplikacji wyszukiwania** w portalu, aby wygenerować aplikację sieci Web typu "localhost", która jest uruchamiana w przeglądarce. W zależności od konfiguracji wygenerowana aplikacja działa przy pierwszym użyciu z połączeniem na żywo z zdalnym indeksem. Aplikacja domyślna może obejmować pasek wyszukiwania, obszar wyników, filtry paska bocznego i obsługę typeahead.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne

Na potrzeby tego przewodnika Szybki Start uaktualnisz do [najnowszej wersji przeglądarki Microsoft Edge](https://www.microsoft.com/edge) lub w tym momencie korzystasz z przeglądarek Chrome firmy Google.

[Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

[Utwórz indeks](search-create-index-portal.md) , który ma być używany jako podstawa aplikacji. 

Ten przewodnik Szybki Start używa wbudowanego przykładowych danych i indeksu nieruchomości, ponieważ zawiera obrazy miniatur. Aby utworzyć indeks używany w tym ćwiczeniu, uruchom kreatora **importowania danych** , wybierając źródło danych *realestate-US-Sample* .

![Strona źródła danych dla przykładowych danych](media/search-create-app-portal/import-data-realestate.png)

Gdy indeks jest gotowy do użycia, przejdź do następnego kroku.

## <a name="start-the-wizard"></a>Uruchom Kreatora

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i [Znajdź usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Na stronie Przegląd w obszarze linki w środku strony wybierz pozycję **indeksy**. 

1. Wybierz pozycję *realestate-US-Sample-index* z listy istniejących indeksów.

1. Na stronie indeks w górnej części wybierz pozycję **Utwórz aplikację wyszukiwania (wersja zapoznawcza)** , aby uruchomić kreatora.

1. Na pierwszej stronie kreatora wybierz pozycję **Włącz udostępnianie zasobów między źródłami (CORS)** , aby dodać obsługę mechanizmu CORS do definicji indeksu. Ten krok jest opcjonalny, ale Lokalna aplikacja internetowa nie będzie łączyć się ze zdalnym indeksem bez tego.

## <a name="configure-search-results"></a>Konfiguruj wyniki wyszukiwania

Kreator udostępnia podstawowy układ dla renderowanych wyników wyszukiwania, które obejmują miejsce dla obrazu miniatury, tytuł i opis. Tworzenie kopii zapasowych każdego z tych elementów jest polem w indeksie dostarczającym dane. 

1. W obszarze miniatury wybierz pole *miniatury* w indeksie *realestate-US-Sample* . Ten przykład ma na celu uwzględnienie miniatur obrazu w postaci obrazów rozłożonych na adresy URL przechowywanych w polu o nazwie *miniatura*. Jeśli indeks nie zawiera obrazów, pozostaw to pole puste.

1. W obszarze tytuł wybierz pole, które przekazuje unikatowość każdego dokumentu. W tym przykładzie identyfikator listy jest rozsądnie wybrany.

1. W polu Opis wybierz pole, które zawiera szczegółowe informacje, które mogą pomóc komuś zdecydować, czy klikać ten dokument.

![Strona źródła danych dla przykładowych danych](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Dodaj pasek boczny

Usługa Search obsługuje nawigację aspektową, która jest często renderowana jako pasek boczny. Zestawy reguł są oparte na polach z możliwością filtrowania i z możliwością ich wypełniania, jak wyrażone w schemacie indeksu.

Na platformie Azure Wyszukiwanie poznawcze Nawigacja aspektowa to skumulowane środowisko filtrowania. W ramach kategorii wybranie wielu filtrów rozszerza wyniki (na przykład wybierając Seattle i Bellevue w mieście). W różnych kategoriach, wybranie wielu filtrów powoduje zawężenie wyników.

> [!TIP]
> Możesz wyświetlić pełny schemat indeksu w portalu. Poszukaj linku **definicji indeksu (JSON)** na stronie przeglądu każdego indeksu. Pola, które kwalifikują się do nawigacji aspektowej, mają atrybuty "z możliwością filtrowania: true" i "kroju: true".

Zaakceptuj bieżące zaznaczenie aspektów i przejdź do następnej strony.


## <a name="add-typeahead"></a>Dodaj typeahead

Funkcja typeahead jest dostępna w formie funkcji Autouzupełnianie i sugestii dotyczących zapytań. Kreator obsługuje sugestie dotyczące zapytań. Na podstawie danych wejściowych naciśnięć klawiszy dostarczonych przez użytkownika usługa wyszukiwania zwraca listę "ukończonych" ciągów zapytań, które można wybrać jako dane wejściowe.

Sugestie są włączone dla określonych definicji pól. Kreator udostępnia opcje konfigurowania ilości informacji uwzględnionych w sugestii. 

Poniższy zrzut ekranu przedstawia opcje w kreatorze, juxtaposed z renderowaną stroną w aplikacji. Możesz zobaczyć, jak są używane wybory pól i jak "Pokaż nazwę pola" służy do dołączania lub wykluczania etykiet w ramach sugestii.

![Konfiguracja sugestii dotyczących zapytań](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Tworzenie, pobieranie i wykonywanie

1. Wybierz pozycję **Utwórz aplikację wyszukiwania** , aby wygenerować plik HTML.

1. Po wyświetleniu monitu wybierz pozycję **Pobierz aplikację** , aby pobrać plik.

1. Otwórz ten plik. Powinna zostać wyświetlona strona podobna do poniższego zrzutu ekranu. Wprowadź termin i Użyj filtrów, aby zawęzić wyniki. 

Indeks podstawowy składa się z fikcyjnych, wygenerowanych danych, które zostały zduplikowane w dokumentach, a opisy czasami nie pasują do obrazu. Podczas tworzenia aplikacji na podstawie własnych indeksów można spodziewać się bardziej spójnego środowiska.

![Uruchomienie aplikacji](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy pracujesz nad własną subskrypcją, dobrym pomysłem jest zakończenie projektu w celu ustalenia, czy nadal potrzebujesz utworzonych zasobów. Zasoby po lewej stronie mogą być kosztowne. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Gdy aplikacja domyślna jest przydatna do początkowej eksploracji i małych zadań, przeglądanie interfejsów API na początku pomoże Ci zrozumieć koncepcje i przepływ pracy na poziomie bardziej szczegółowym:

> [!div class="nextstepaction"]
> [Tworzenie indeksu przy użyciu zestawu .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)