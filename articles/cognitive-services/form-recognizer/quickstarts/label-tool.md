---
title: 'Szybki Start: formularze etykiet, uczenie modelu i analizowanie formularza przy użyciu narzędzia do etykietowania przykładowego'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start będziesz używać narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy do ręcznego etykietowania dokumentów. Następnie nauczysz model niestandardowy z oznaczonymi dokumentami i użyjesz modelu, aby wyodrębnić pary klucz/wartość.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 301b68d0dfaeef6d5cfdd4d7a5a504794ac877f4
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205830"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Uczenie modelu aparatu rozpoznawania formularzy z etykietami przy użyciu narzędzia do etykietowania przykładowego

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy z przykładowym narzędziem do etykietowania do uczenia modelu niestandardowego z ręcznie oznaczonymi danymi. Aby dowiedzieć się więcej na temat tej funkcji, zobacz sekcję [uczenie z etykietami](../overview.md#train-with-labels) .

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:

- Zestaw składający się z co najmniej sześciu formularzy tego samego typu. Te dane będą używane do uczenia modelu i testowania formularza. Możesz użyć [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki Start. Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów BLOB na koncie usługi Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Konfigurowanie przykładowego narzędzia do etykietowania

Użyjesz aparatu platformy Docker, aby uruchomić przykładowe narzędzie do etykietowania. Wykonaj następujące kroki, aby skonfigurować kontener platformy Docker. Podstawowe informacje dotyczące platformy Docker i kontenera można znaleźć w temacie [Omówienie platformy Docker](https://docs.docker.com/engine/docker-overview/).
1. Najpierw zainstaluj platformę Docker na komputerze-hoście. W tym przewodniku pokazano, jak używać komputera lokalnego jako hosta. Jeśli chcesz używać usługi hostingu platformy Docker na platformie Azure, zobacz Przewodnik dotyczący [wdrażania przykładowego narzędzia do etykietowania](../deploy-label-tool.md) . 

   Komputer hosta musi spełniać następujące wymagania sprzętowe:

    | Kontener | Minimalne | Zalecane|
    |:--|:--|:--|
    |Przykładowe narzędzie do etykietowania|2 rdzeń, 4 GB pamięci|4 rdzenie, 8 GB pamięci|

   Zainstaluj platformę Docker na maszynie, postępując zgodnie z odpowiednimi instrukcjami dla danego systemu operacyjnego: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * System [Linux](https://docs.docker.com/install/).

1. Pobierz kontener narzędzia do etykietowania przykładowego za pomocą polecenia `docker pull`.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Teraz możesz przystąpić do uruchamiania kontenera z `docker run`.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   To polecenie spowoduje udostępnienie przykładowego narzędzia do etykietowania za pomocą przeglądarki sieci Web. Przejdź do obszaru [http://localhost:3000](http://localhost:3000) (Ustawienia — Integracje i usługi).

> [!NOTE]
> Możesz także oznaczyć dokumenty i pouczenie modeli przy użyciu interfejsu API REST aparatu rozpoznawania formularzy. Aby nauczyć się i analizować za pomocą interfejsu API REST, zobacz [uczenie się z etykietami przy użyciu interfejsu API REST i języka Python](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Konfigurowanie danych wejściowych

Najpierw upewnij się, że wszystkie dokumenty szkoleniowe mają ten sam format. Jeśli masz formularze w wielu formatach, zorganizuj je w podfolderach w oparciu o wspólny format. Podczas uczenia należy skierować interfejs API do podfolderu.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurowanie udostępniania zasobów między domenami (CORS)

Włącz funkcję CORS na koncie magazynu. Wybierz konto magazynu w Azure Portal a następnie kliknij kartę **CORS** w okienku po lewej stronie. W dolnej linii Wypełnij poniższe wartości. Następnie kliknij przycisk **Zapisz** u góry.

* Dozwolone źródła = * 
* Dozwolone metody = \[Zaznacz wszystko\]
* Dozwolone nagłówki = *
* Uwidocznione nagłówki = * 
* Maksymalny wiek = 200

> [!div class="mx-imgBorder"]
> ![konfigurację mechanizmu CORS w Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Połącz z przykładowym narzędziem do etykietowania

Przykładowe narzędzie do etykietowania łączy się ze źródłem (gdzie oryginalne formularze są) i obiektem docelowym (gdzie eksportuje utworzone etykiety i dane wyjściowe).

Połączenia można skonfigurować i udostępnić między projektami. Korzystają one z rozszerzalnego modelu dostawcy, dzięki czemu można łatwo dodawać nowych dostawców źródła/obiektu docelowego.

Aby utworzyć nowe połączenie, kliknij ikonę **nowe połączenia** (plug) na pasku nawigacyjnym po lewej stronie.

Wypełnij pola następującymi wartościami:

* **Nazwa wyświetlana** — nazwa wyświetlana połączenia.
* **Opis** — opis projektu.
* **Adres URL** sygnatury dostępu współdzielonego (SAS) dla kontenera BLOB Storage platformy Azure. Aby pobrać adres URL SAS, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**. Ustaw czas wygaśnięcia na jakiś czas po użyciu usługi. Upewnij się, że uprawnienia **Odczyt**, **zapis**, **usuwanie**i **Wyświetlanie listy** są zaznaczone, a następnie kliknij pozycję **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

![Ustawienia połączenia przykładowego narzędzia do etykietowania](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przykładowym narzędziu do etykietowania projekty przechowują konfiguracje i ustawienia. Utwórz nowy projekt i wypełnij pola następującymi wartościami:

* **Nazwa wyświetlana** — nazwa wyświetlana projektu
* **Token zabezpieczający** — niektóre ustawienia projektu mogą zawierać wartości poufne, takie jak klucze interfejsu API lub inne wspólne klucze tajne. Każdy projekt generuje token zabezpieczający, który może służyć do szyfrowania/odszyfrowywania poufnych ustawień projektu. Tokeny zabezpieczające w ustawieniach aplikacji można znaleźć, klikając ikonę koła zębatego w dolnym rogu lewego paska nawigacyjnego.
* **Połączenie źródłowe** — połączenie z usługą Azure Blob Storage utworzone w poprzednim kroku, którego chcesz użyć dla tego projektu.
* **Ścieżka folderu** — opcjonalne — Jeśli Twoje formularze źródłowe znajdują się w folderze kontenera obiektów blob, określ tutaj nazwę folderu
* **Identyfikator URI usługi rozpoznawania formularza** — adres URL punktu końcowego aparatu rozpoznawania formularza.
* **Klucz interfejsu API** — klucz subskrypcji aparatu rozpoznawania formularza.
* **Opis** — opcjonalnie — opis projektu

![Nowa strona projektu na przykładowym narzędziu do etykietowania](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Etykiety formularzy

Po utworzeniu lub otwarciu projektu zostanie otwarte okno edytora tagów głównych. Edytor tagów składa się z trzech części:

* Okienko podglądu o zmiennym rozmiarze, które zawiera przewijaną listę formularzy ze źródła połączenia.
* Główne okienko edytora, które pozwala na stosowanie tagów.
* Okienko edytora tagów, które pozwala użytkownikom modyfikować, blokować, zmieniać kolejność i usuwać Tagi. 

### <a name="identify-text-elements"></a>Zidentyfikuj elementy tekstowe

Kliknij przycisk **Uruchom OCR dla wszystkich plików** w okienku po lewej stronie, aby uzyskać informacje o układzie tekstu dla każdego dokumentu. Narzędzie do etykietowania rysuje ramki ograniczenia wokół każdego elementu tekstowego.

### <a name="apply-labels-to-text"></a>Zastosuj etykiety do tekstu

Następnie utworzysz Tagi (etykiety) i zastosujemy je do elementów tekstowych, które mają być rozpoznawane przez model.

1. Najpierw użyj okienka edytora tagów, aby utworzyć Tagi, które chcesz zidentyfikować.
  1. Kliknij przycisk **+** , aby utworzyć nowy tag.
  1. Wprowadź nazwę tagu.
  1. Naciśnij klawisz ENTER, aby zapisać tag.
1. W edytorze głównym kliknij i przeciągnij, aby wybrać jedno lub wiele wyrazów z wyróżnionych elementów tekstowych.
1. Kliknij tag, który chcesz zastosować, lub naciśnij odpowiedni klawisz klawiatury. Klucze liczb są przypisywane jako klawisze dostępu dla pierwszych 10 tagów. Można zmienić kolejność tagów przy użyciu ikon strzałek w górę i w dół w okienku Edytora tagów.
    > [!Tip]
    > Podczas etykietowania formularzy należy pamiętać o następujących wskazówkach.
    > * Do każdego zaznaczonego elementu tekstowego można zastosować tylko jeden tag.
    > * Każdy tag można zastosować tylko raz na stronę. Jeśli wartość pojawia się wiele razy w tym samym formularzu, Utwórz różne Tagi dla każdego wystąpienia. Na przykład: "Invoice nr 1", "Invoice nr 2" i tak dalej.
    > * Znaczniki nie mogą obejmować między stronami.
    > * Etykiety wartości w postaci, w jakiej są wyświetlane w formularzu; nie próbuj podzielić wartości na dwie części z dwoma różnymi tagami. Na przykład pole adresu powinno mieć etykietę z pojedynczym tagiem nawet wtedy, gdy obejmuje wiele wierszy.
    > * Nie dołączaj kluczy do pól otagowanych,&mdash;tylko wartości.
    > * Dane tabeli powinny być wykrywane automatycznie i będą dostępne w końcowym wyjściowym pliku JSON. Jeśli jednak model nie wykryje wszystkich danych tabeli, możesz również ręcznie oznaczyć te pola. Oznacz każdą komórkę w tabeli inną etykietą. Jeśli formularze zawierają tabele o różnej liczbie wierszy, upewnij się, że tag zawiera co najmniej jeden formularz o największej możliwej tabeli.


Wykonaj powyższe kroki, aby oznaczyć pięć formularzy, a następnie przejść do następnego kroku.

![Główne okno edytora przykładowego narzędzia do etykietowania](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

Kliknij ikonę szkolenia (samochód pociąg) w okienku po lewej stronie, aby otworzyć stronę szkolenia. Następnie kliknij przycisk **uczenie** , aby rozpocząć uczenie modelu. Po zakończeniu procesu szkolenia zostaną wyświetlone następujące informacje:

* **Identyfikator modelu** — Identyfikator modelu, który został utworzony i przeszkolony. Każde wywołanie szkoleniowe tworzy nowy model z własnym IDENTYFIKATORem. Skopiuj ten ciąg do bezpiecznej lokalizacji; będzie ona potrzebna, jeśli chcesz wykonywać wywołania prognoz za pomocą interfejsu API REST.
* **Średnia dokładność** — średnia dokładność modelu. Możesz poprawić dokładność modelu przez etykietowanie dodatkowych formularzy i szkoleń, aby utworzyć nowy model. Zalecamy rozpoczęcie od etykietowania pięciu formularzy i dodanie większej liczby formularzy zgodnie z wymaganiami.
* Lista tagów i Szacowana dokładność na tag.

![Widok szkoleń](../media/label-tool/train-screen.png)

Po zakończeniu szkolenia Przejrzyj wartość **średnia dokładność** . Jeśli jest to niska, należy dodać więcej dokumentów wejściowych i powtórz powyższe kroki. Dokumenty, które zostały już oznaczone etykietami, pozostaną w indeksie projektu.

> [!TIP]
> Możesz również uruchomić proces szkolenia przy użyciu wywołania interfejsu API REST. Aby dowiedzieć się, jak to zrobić, zobacz [uczenie się z etykietami przy użyciu języka Python](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Analizowanie formularza

Kliknij ikonę przewidywania (prostokąty) po lewej stronie, aby przetestować model. Przekaż dokument formularza, który nie był używany w procesie szkoleniowym. Następnie kliknij przycisk **predykcyjny** po prawej stronie, aby uzyskać prognozy klucza/wartości dla formularza. Narzędzie zastosuje znaczniki w obwiedniach i zgłosi zaufanie każdego tagu.

> [!TIP]
> Można również uruchomić analizowanie interfejsu API z wywołaniem REST. Aby dowiedzieć się, jak to zrobić, zobacz [uczenie się z etykietami przy użyciu języka Python](./python-labeled-data.md).

## <a name="improve-results"></a>Popraw wyniki

W zależności od raportowanej dokładności możesz chcieć przeprowadzić dalsze szkolenia, aby usprawnić model. Po zakończeniu przewidywania przejrzyj wartości zaufania dla każdego z zastosowanych tagów. Jeśli średnia wartość szkoleniowa jest wysoka, ale wyniki pewności są niskie (lub wyniki są niedokładne), należy dodać plik używany do przewidywania do zestawu szkoleniowego, oznaczyć go etykietą i ponownie przeprowadzić uczenie.

Raportowane średnia dokładność, wyniki pewności i rzeczywista dokładność mogą być niespójne, gdy analizowane dokumenty różnią się od tych używanych w szkoleniu. Należy pamiętać, że niektóre dokumenty wyglądają podobnie, gdy są wyświetlane przez osoby, ale mogą odróżnić się od modelu AI. Na przykład można pouczenie się typu formularza, który ma dwie odmiany, gdzie zestaw szkoleniowy składa się z 20% zmian A i 80% wariacji B. W czasie przewidywania Wyniki pewności dotyczące dokumentów odmiany A mogą być niższe.

## <a name="save-a-project-and-resume-later"></a>Zapisz projekt i Wznów później

Aby wznowić projekt w innym czasie lub w innej przeglądarce, musisz zapisać token zabezpieczający projektu i ponownie wprowadzić go później. 

### <a name="get-project-credentials"></a>Pobierz poświadczenia projektu
Przejdź do strony ustawień projektu (ikona suwaka) i zanotuj nazwę tokenu zabezpieczającego. Następnie przejdź do ustawień aplikacji (ikony koła zębatego), które pokazują wszystkie tokeny zabezpieczające w bieżącym wystąpieniu przeglądarki. Znajdź token zabezpieczający projektu i skopiuj jego nazwę i wartość klucza do bezpiecznej lokalizacji.

### <a name="restore-project-credentials"></a>Przywróć poświadczenia projektu
Gdy chcesz wznowić projekt, musisz najpierw utworzyć połączenie z tym samym kontenerem usługi BLOB Storage. Powtórz powyższe kroki, aby to zrobić. Następnie przejdź do strony ustawień aplikacji (ikony koła zębatego) i sprawdź, czy jest tam używany token zabezpieczający projektu. Jeśli tak nie jest, Dodaj nowy token zabezpieczający i skopiuj go przy użyciu nazwy i klucza tokenu z poprzedniego kroku. Następnie kliknij przycisk Zapisz ustawienia. 

### <a name="resume-a-project"></a>Wznów projekt
Na koniec przejdź do strony głównej (ikona domu), a następnie kliknij pozycję Otwórz projekt w chmurze. Następnie wybierz połączenie usługi BLOB Storage i wybierz plik *. vott* projektu. Aplikacja będzie ładować wszystkie ustawienia projektu, ponieważ ma token zabezpieczający.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób użycia narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy do uczenia modelu z ręcznymi etykietami danych. Jeśli chcesz zintegrować narzędzie do etykietowania z własną aplikacją, Użyj interfejsów API REST, które zajmują się szkoleniem dotyczącym danych z etykietami.

> [!div class="nextstepaction"]
> [Uczenie z etykietami przy użyciu języka Python](./python-labeled-data.md)
