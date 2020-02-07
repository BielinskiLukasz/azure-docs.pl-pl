---
title: Migrowanie lokalnego notesu Jupyter do wersji zapoznawczej programu Azure Notebooks
description: Szybko Przenieś Notes Jupyter Azure Notebooks do wersji zapoznawczej z komputera lokalnego lub adresu URL sieci Web, a następnie Udostępnij go do współpracy.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064329"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Szybki Start: Migrowanie lokalnego notesu Jupyter w wersji zapoznawczej programu Azure Notebooks

W tym przewodniku szybki start przeprowadzisz migrację notesu Jupyter z komputera lokalnego lub innego dostępnego adresu URL pliku do Azure Notebooks. 

Notesy Jupyter na własnym komputerze są dostępne tylko dla Ciebie. Pliki można udostępniać, ale odbiorcy mogą korzystać z własnych lokalnych kopii notesu i trudno jest wprowadzić zmiany. Nawet jeśli przechowujesz notesy w udostępnionym repozytorium online, takim jak GitHub, każdy współpracownik musi mieć skonfigurowaną lokalną instalację Jupyter, jak ty.

Migrowanie notesów lokalnych lub opartych na repozytorium do Azure Notebooks umożliwia natychmiastowe udostępnianie ich współpracownikom, którzy potrzebują tylko przeglądarki do wyświetlania i uruchamiania Twoich notesów. W przypadku zalogowania się do Azure Notebooks mogą także wprowadzać zmiany.

## <a name="prerequisites"></a>Wymagania wstępne

- [Notes Jupyter](https://jupyter-notebook.readthedocs.io) na komputerze lokalnym lub pod innym dostępnym adresem URL pliku. 

## <a name="create-a-project-on-azure-notebooks"></a>Utwórz projekt w notesach platformy Azure

Ten przewodnik Szybki Start przedstawia migrację notesu z komputera lokalnego lub innego dostępnego adresu URL pliku. Aby przeprowadzić migrację notesów z repozytorium GitHub, zobacz [Szybki Start: klonowanie notesu](quickstart-clone-jupyter-notebook.md).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. (Aby uzyskać szczegółowe informacje, zobacz [Szybki Start — logowanie do Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony:

    ![Moje łącza projektów u góry okna przeglądarki](media/quickstarts/my-projects-link.png)

1. Na stronie **Moje projekty** wybierz pozycję **Nowy projekt** (skrót klawiaturowy: n). Przycisk może być wyświetlany tylko jako **+** , jeśli okno przeglądarki jest wąskie:

    ![Nowe polecenie projektu na stronie Moje projekty](media/quickstarts/new-project-command.png)

1. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** wprowadź odpowiednie wartości dla notesu migrowanego w polach **Nazwa projektu** i **Identyfikator projektu** , usuń zaznaczenie opcji **projekt publiczny** i **Utwórz Readme.MD**, a następnie wybierz pozycję **Utwórz**.

## <a name="upload-the-local-notebook"></a>Przekazywanie lokalnego notesu

1. Na stronie projekt wybierz pozycję **Przekaż** (która może pojawić się jako strzałka w górę tylko wtedy, gdy okno przeglądarki jest małe), a następnie wybierz pozycję 1. W wyświetlonym oknie podręcznym wybierz pozycję **z komputera** , jeśli Notes znajduje się w lokalnym systemie plików lub **z adresu URL** , jeśli Notes znajduje się w trybie online:

    ![Polecenie, aby przekazać Notes z adresem URL lub na komputerze lokalnym](media/quickstarts/upload-from-computer-url-command.png)

   Jeśli Notes znajduje się w repozytorium GitHub, wykonaj kroki opisane w sekcji [Szybki Start: klonowanie notesu](quickstart-clone-jupyter-notebook.md) .

   - Jeśli używasz **z komputera**, przeciągnij i upuść pliki *. ipynb* w menu podręcznym lub wybierz pozycję **Wybierz pliki**, a następnie wyszukaj i wybierz pliki, które chcesz zaimportować. Następnie wybierz pozycję **Przekaż**. Przekazane pliki znajdują się taką samą nazwę jak pliki lokalne. Nie musisz przekazywać zawartości żadnych folderów *. ipynb_checkpoints* .

     ![Przekaż z komputera okna podręcznego](media/quickstarts/upload-from-computer-popup.png)

   - W przypadku używania **z adresu URL**wprowadź adres źródłowy w polu **adres URL pliku** i nazwę pliku do przypisania do notesu w projekcie w polu **Nazwa pliku** . Następnie wybierz pozycję **Przekaż**. Jeśli masz wiele plików z oddzielnymi adresami URL, użyj polecenia **Dodaj plik** do sprawdzenia pierwszego WPROWADZONEGO adresu URL, po którym okno podręczne zawiera nowe pola dla innego pliku.

     ![Przekaż z adresu URL okna podręcznego](media/quickstarts/upload-from-url-popup.png)

1. Otwórz i uruchom notesu nowo przesłanym, aby sprawdzić jej zawartość i operacji. Gdy skończysz, wybierz pozycję **plik** > **zatrzymać i Zamknij** , aby zamknąć Notes.

1. Aby udostępnić link do przekazanego notesu, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Kopiuj link** (skrót klawiaturowy: y), a następnie wklej ten link do odpowiedniego komunikatu. Alternatywnie można udostępnić projekt jako całość przy użyciu kontrolki **Udostępnij** na stronie projektu.

1. Aby edytować pliki inne niż notesy, kliknij prawym przyciskiem myszy plik w projekcie i wybierz polecenie **Edytuj plik** (skrót klawiaturowy: i). Akcja domyślna, **Uruchom** (skrót klawiaturowy: r), wyświetla tylko zawartość pliku i nie zezwala na edytowanie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i uruchamianie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
