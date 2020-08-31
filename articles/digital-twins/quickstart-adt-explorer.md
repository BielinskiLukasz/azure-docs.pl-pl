---
title: Zapoznawanie się z przykładowym scenariuszem
titleSuffix: Azure Digital Twins
description: Użyj przykładu ADT Explorer, aby wizualizować i eksplorować wstępnie skompilowany scenariusz.
author: baanders
ms.author: baanders
ms.date: 8/12/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: bbb1378419c68db07be5ca14ca6834810f2fc9f5
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055468"
---
# <a name="explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Eksplorowanie przykładowego scenariusza usługi Azure Digital bliźniaczych reprezentacji za pomocą Eksploratora ADT

Za pomocą usługi Azure Digital bliźniaczych reprezentacji można tworzyć i korzystać z modeli na żywo w rzeczywistych środowiskach. W tym celu należy modelować poszczególne elementy jako **bliźniaczych reprezentacji cyfrowe**, a następnie połączyć je z **grafem** wiedzy, który może reagować na zdarzenia na żywo i uzyskać zapytanie o informacje.

W tym przewodniku szybki start zobaczysz wstępnie utworzony Graf usługi Azure Digital bliźniaczych reprezentacji z pomocą przykładowej aplikacji o nazwie [**Eksplorator usługi Azure Digital bliźniaczych reprezentacji (ADT)**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Eksplorator ADT umożliwia przekazywanie scenariusza, wyświetlanie wizualnych reprezentacji bliźniaczych reprezentacji i grafów oraz wykonywanie innych działań związanych z zarządzaniem za pośrednictwem przeglądarki i środowiska wizualnego.

Przewodnik Szybki Start zawiera następujące główne kroki:

1. Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji i Eksploratora ADT
1. Przekazywanie wstępnie utworzonych modeli i danych grafu w celu skonstruowania przykładowego scenariusza
1. Eksplorowanie grafu scenariusza, który został utworzony
1. Wprowadzanie zmian do grafu

Przykładowy Graf, z którym będziesz pracować, reprezentuje Kompilowanie z dwiema podłogami i dwoma pomieszczeń. Wykres będzie wyglądać następująco:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Widok grafu przedstawiający 4 węzły cykliczne połączone za pomocą strzałek. Okrąg oznaczony etykietą "Floor1" jest połączony przez strzałkę o nazwie "Contains" z kółkiem o nazwie "Room1"; okrąg oznaczony etykietą "Floor0" jest połączony przez strzałkę o nazwie "Contains" z kółkiem o nazwie "Room0". "Floor1" i "Floor0" nie są połączone.":::

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz mieć subskrypcję platformy Azure. Jeśli jeszcze tego nie zrobiono, **[Utwórz go bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

Przed rozpoczęciem szybkiego startu należy również pobrać dwie próbki:
* Przykładowa aplikacja **Eksploratora ADT** . Ten przykład zawiera główną aplikację używaną w szybkim samouczku do ładowania i eksplorowania scenariusza bliźniaczych reprezentacji Digital na platformie Azure. Aby uzyskać dostęp do aplikacji, przejdź tutaj: [Eksplorator usługi Azure Digital bliźniaczych reprezentacji (ADT)](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Naciśnij przycisk *Pobierz zip* , aby pobrać plik *. Plik ZIP* tego przykładowego kodu na maszynę jako _**ADT_Explorer.zip**_.
* **Przykładowy scenariusz usługi Azure Digital bliźniaczych reprezentacji**. Obejmuje to wstępnie utworzony Graf usługi Azure Digital bliźniaczych reprezentacji, który zostanie załadowany do Eksploratora ADT w celu pracy z programem. Aby uzyskać ten scenariusz, przejdź tutaj: [bliźniaczych reprezentacji cyfrowych platformy Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Naciśnij przycisk *Pobierz zip* , aby pobrać plik *. Plik ZIP* tego przykładowego kodu na maszynę jako _**Azure_Digital_Twins_samples.zip**_.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Konfigurowanie usługi Azure Digital bliźniaczych reprezentacji i Eksploratora ADT

Pierwszym krokiem w pracy z usługą Azure Digital bliźniaczych reprezentacji jest skonfigurowanie **wystąpienia usługi Azure Digital bliźniaczych reprezentacji**. Po utworzeniu wystąpienia usługi będzie można je wypełnić za pomocą przykładowych danych w dalszej części przewodnika Szybki Start.

Należy również skonfigurować uprawnienia dla Eksploratora ADT do uruchamiania na komputerze i uzyskać dostęp do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Pozwoli to na korzystanie z przykładowej aplikacji do eksplorowania Twojego wystąpienia i jego danych.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

Najprostszym sposobem skonfigurowania wystąpienia i wymaganego uwierzytelnienia jest uruchomienie przykładowego skryptu wdrażania automatycznego. Postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Konfiguracja wystąpienia i uwierzytelniania (skrypty)*](how-to-set-up-instance-scripted.md). Instrukcje zawierają również kroki umożliwiające zweryfikowanie, czy każdy krok został pomyślnie ukończony i jest gotowy do przejścia do korzystania z nowego wystąpienia.

W tym przewodniku szybki start będziesz potrzebować następujących wartości z usługi podczas konfigurowania wystąpienia. Aby ponownie zebrać te wartości, Skorzystaj z poniższych linków, aby znaleźć je w [Azure Portal](https://portal.azure.com).
* **_Nazwa hosta_** wystąpienia usługi Azure Digital bliźniaczych reprezentacji ([Znajdź w portalu](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Identyfikator aplikacji rejestracji aplikacji usługi Azure AD **_(klient)_** ([Znajdź w portalu](how-to-set-up-instance-portal.md#collect-important-values))
* Identyfikator rejestracji w usłudze Azure AD ( **_dzierżawa)_** ([Znajdź w portalu](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="set-adt-explorer-permissions"></a>Ustawianie uprawnień programu ADT Explorer

Następnie przygotuj utworzone wystąpienie usługi Azure Digital bliźniaczych reprezentacji do pracy z Eksploratorem ADT, który jest aplikacją sieci Web hostowaną lokalnie. Odwiedź stronę [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) w Azure Portal i wybierz nazwę rejestracji aplikacji z listy.

Wybierz pozycję *uwierzytelnianie* z menu Rejestracja i naciśnij pozycję *+ Dodaj platformę*.

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="Azure Portal stronie szczegółów uwierzytelniania dla rejestracji aplikacji. Zostanie wyróżniony przycisk "Dodaj platformę"" lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

Na stronie *Konfigurowanie platform* poniżej wybierz pozycję *Sieć Web*.
Wypełnij szczegóły konfiguracji w następujący sposób:
* **Identyfikatory URI przekierowania**: Dodaj identyfikator URI przekierowania *http://localhost:3000* .
* **Niejawne udzielenie**: zaznacz pole wyboru *tokeny dostępu*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="Na stronie Konfigurowanie platform Wyróżnij informacje opisane powyżej na ekranie":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Naciśnij pozycję *Konfiguruj* , aby zakończyć.

Teraz masz skonfigurowaną konfigurację sieci Web, która będzie używana w programie ADT Explorer. Należy uwzględnić kartę Uwierzytelnianie w Azure Portal.

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="Azure Portal stronie szczegółów uwierzytelniania dla rejestracji aplikacji. Istnieją wyróżnione sekcje dotyczące platformy sieci Web z identyfikatorem URI przekierowania http://localhost:3000 i niejawne przyznawanie tokenów dostępu":::

### <a name="run-and-configure-adt-explorer"></a>Uruchom i skonfiguruj Eksploratora ADT

Następnie uruchom aplikację ADT Explorer i skonfiguruj ją dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Przejdź do pobranego folderu _**ADT_Explorer.zip**_ i rozpakuj go. Otwórz wiersz polecenia w lokalizacji folderu *ADT_explorer/Client/src*.

Uruchom `npm install` , aby pobrać wszystkie wymagane zależności.

Następnie uruchom aplikację, uruchamiając polecenie `npm run start` .

Po kilku sekundach zostanie otwarte okno przeglądarki, a aplikacja zostanie wyświetlona w przeglądarce.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Okno przeglądarki pokazujące aplikację uruchomioną na hoście lokalnym: 3000. Aplikacja jest nazywana Eksploratorem ADT i zawiera pola Eksploratora zapytań, modelu, widoku wykresu i Eksploratora właściwości. Nie ma jeszcze danych na ekranie." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Naciśnij przycisk *Zaloguj* w górnej części okna, aby skonfigurować Eksploratora ADT do pracy z skonfigurowanym wystąpieniem. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="W Eksploratorze ADT Wyróżnij ikonę logowania w górnej części okna. Ikona pokazuje prostą Silhouette osoby nałożonej przy użyciu Silhouette klucza." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Wprowadź ważne informacje zebrane wcześniej w sekcji [wymagania wstępne](#prerequisites) :
* Identyfikator aplikacji (klienta)
* Identyfikator katalogu (dzierżawcy)
* ADT adres URL w formacie *https://{Nazwa hosta wystąpienia}*

>[!NOTE]
> Możesz odwiedzać/edytować te informacje w dowolnym momencie, wybierając tę samą ikonę, aby ponownie pobrać pole logowania. Dane zostaną zachowane.

Jeśli zobaczysz *wymagane uprawnienia* do okna podręcznego od firmy Microsoft, przyznaj zgodę na tę aplikację i zaakceptuj ją, aby kontynuować.

## <a name="add-the-sample-data"></a>Dodawanie przykładowych danych

Następnie zaimportujemy przykładowy scenariusz i wykres do Eksploratora ADT.

Przykładowy scenariusz znajduje się w folderze pobranym  _**Azure_Digital_Twins_samples.zip**_ , więc należy przejść do folderu i rozpakować go teraz.

### <a name="models"></a>Modele

Pierwszym krokiem w rozwiązaniu Digital bliźniaczych reprezentacji na platformie Azure jest definiowanie słownika dla danego środowiska. Jest to realizowane przez tworzenie [**modeli**](concepts-models.md)niestandardowych, które opisują typy obiektów istniejących w danym środowisku. 

Każdy model jest zapisywana w języku JSON-LD podobnym do **DTDL (Digital bliźniaczy Definition Language)** i opisuje pojedynczy typ jednostki pod względem jego *Właściwości*, *telemetrii*, *relacji*i *składników*. Później te modele będą używane jako podstawy bliźniaczych reprezentacji cyfrowych, które reprezentują określone wystąpienia tych typów.

Zwykle podczas tworzenia modelu należy wykonać trzy czynności:
1. Napisz definicję modelu (w ramach przewodnika Szybki Start, która została już wykonana jako część przykładowego rozwiązania)
2. Sprawdź poprawność, aby upewnić się, że składnia jest dokładna (w przewodniku Szybki Start, który jest już częścią przykładowego rozwiązania)
3. Przekaż do swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji
 
W tym przewodniku szybki start pliki modelu zostały już zapisaną i zweryfikowane i są dołączone do pobranego rozwiązania. W tej sekcji przekazano dwa wstępnie zapisaną modele do wystąpienia, aby zdefiniować te składniki środowiska budowania:
* Floor
* Zmieścić

#### <a name="upload-models"></a>Przekazywanie modeli

W polu *Widok modelu* kliknij ikonę *Przekaż model* .

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="W polu widok modelu zostanie wyróżniona środkowa ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. W wyświetlonym oknie dialogowym Wybieranie pliku przejdź do folderu *Azure_Digital_Twins_samples/adtsampleapp/sampleclientapp/models* w pobranym repozytorium.
2. Wybierz pozycję *Room.js* i *Floor.jsna*, a następnie kliknij przycisk OK. (Możesz przekazać inne modele, jeśli chcesz, ale nie będą używane w tym przewodniku Szybki Start).
3. Postępuj zgodnie z wyskakującym oknem dialogowym z prośbą o zalogowanie się do konta platformy Azure.

>[!NOTE]
>Jeśli zostanie wyświetlony następujący komunikat o błędzie: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Odczyt podręczny "błąd: błąd podczas pobierania modeli: ClientAuthError: błąd otwierania okna podręcznego. Taka sytuacja może wystąpić, jeśli używasz programu IE lub w przeglądarce są blokowane okienka wyskakujące." border="false"::: 
> Spróbuj wyłączyć blokowanie wyskakujących okienek lub użyć innej przeglądarki.

Program ADT Explorer przekaże teraz te pliki modelu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Powinny one być wyświetlane w polu *Widok modelu* , wyświetlając ich przyjazne nazwy i pełne identyfikatory modeli. Możesz kliknąć bąbelki informacje o *modelu* , aby zobaczyć kod DTDL za nimi.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Widok pola "model widoku" z dwoma definicjami modelu wymienionymi w obszarze (dtmi: example; 1) i pokoju (dtmi: przykład: Pokój; 1). Ikona "widok modelu" przedstawiająca literę "i" w okręgu jest wyróżniona dla każdego modelu." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Bliźniaczych reprezentacji i wykres Wieloosiowy

Teraz, gdy niektóre modele zostały przekazane do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, można dodać [**bliźniaczych reprezentacji cyfrowe**](concepts-twins-graph.md) , które są zgodne z definicjami modelu. 

Bliźniaczych reprezentacji Digital reprezentuje rzeczywiste jednostki w środowisku biznesowym: elementy takie jak czujniki w farmie, lampki w kabinie lub — w tym przewodniku szybki start — pokoje na piętro budynku. Można utworzyć wiele bliźniaczych reprezentacji dowolnego typu modelu (na przykład wiele pokojów, które używają modelu *Room* ) i połączyć je z relacjami do **grafu bliźniaczyego** , który reprezentuje pełne środowisko.

W tej sekcji zostaną przekazane wstępnie utworzone bliźniaczych reprezentacji, które są połączone ze wstępnie utworzonym wykresem. Wykres zawiera dwie piętra i dwa pokoje, połączone w następującym układzie:
* *Floor0*
    - zawiera *Room0*
* *Floor1*
    - zawiera *Room1*

#### <a name="import-the-graph"></a>Importowanie wykresu

W polu *Widok wykresu* kliknij ikonę *Importuj wykres* .

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="W polu widok wykresu zostanie wyróżniona ikona. Pokazuje strzałkę wskazującą chmurę." lightbox="media/quickstart-adt-explorer/import-graph.png":::

W polu Selektor plików przejdź do folderu *Azure_Digital_Twins_samples/adtsampleapp/sampleclientapp* i wybierz _**buildingScenario.xlsx**_ plik arkusza kalkulacyjnego. Ten plik zawiera opis przykładowego wykresu. Naciśnij przycisk OK.

Po kilku sekundach Eksplorator ADT otworzy widok *importu* z podglądem wykresu, który zostanie załadowany.

Aby potwierdzić przekazywanie wykresu, naciśnij ikonę *Zapisz* w prawym górnym rogu *widoku wykresu*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Wyróżnianie ikony Zapisz w okienku podglądu wykresu" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Eksplorator ADT będzie teraz używał przekazanego pliku do utworzenia żądanego bliźniaczych reprezentacji i relacji między nimi. Zostanie wyświetlone okno dialogowe pokazujące, że zostało zakończone. Naciśnij przycisk *Zamknij*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Okno dialogowe informujące o powodzeniu importowania grafu. Plik "Import" został pomyślnie odczytany. 49 bliźniaczych reprezentacji zaimportowano. 50 relacji importowanych. '" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Wykres został teraz przekazany do Eksploratora ADT. Aby wyświetlić wykres, należy kliknąć przycisk *Uruchom zapytanie* w oknie *Eksplorator wykresu* w górnej części okna Eksploratora ADT. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Zostanie wyróżniony przycisk odczytu "uruchom zapytanie" w górnej części okna" lightbox="media/quickstart-adt-explorer/run-query.png":::

Spowoduje to uruchomienie zapytania domyślnego w celu wybrania i wyświetlenia wszystkich bliźniaczych reprezentacji cyfrowych. Eksplorator ADT pobiera wszystkie bliźniaczych reprezentacji i relacje z usługi i rysuje wykres zdefiniowany przez nich w polu *Widok wykresu* .

## <a name="explore-the-graph"></a>Eksplorowanie grafu

Teraz można zobaczyć przekazany Graf przykładowego scenariusza:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Widok "widok wykresu" z wykresem bliźniaczym wewnątrz. Okrąg oznaczony etykietą "floor1" jest połączony przez strzałkę o nazwie "Contains" z kółkiem o nazwie "room1"; okrąg oznaczony etykietą "floor0" jest połączony przez strzałkę o nazwie "Contains" z kółkiem o nazwie "room0".":::

Okręgi ("węzły grafu") reprezentują bliźniaczych reprezentacjiy cyfrowe, a wiersze reprezentują relacje. Zobaczysz, że *Floor0* bliźniaczy zawiera *Room0*, a *Floor1a* zawiera *Room1*.

Jeśli używasz myszy, możesz kliknąć i przeciągnąć fragmenty wykresu, aby przenieść je wokół siebie.

### <a name="view-twin-properties"></a>Wyświetl właściwości przędzy 

Możesz wybrać sznurek, aby wyświetlić listę jego właściwości i ich wartości w oknie *Eksplorator właściwości* . 

Oto właściwości *Room0*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Zaznacz wokół pola "Eksplorator właściwości" właściwości Room0, w tym (między innymi) pole $dtId "Room0", pole temperatury 70 i pole wilgotności 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Należy pamiętać, że *Room0* ma temperaturę **70**.

Oto właściwości *Room1*:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Zaznacz wokół pola "Eksplorator właściwości" właściwości Room1, w tym (między innymi) pole $dtId "Room1", pole temperatury 80 i pole wilgotności 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Należy pamiętać, że *Room1* ma temperaturę **80**.

### <a name="query-the-graph"></a>Tworzenie zapytań względem grafu

Główną funkcją usługi Azure Digital bliźniaczych reprezentacji jest możliwość łatwego i wydajnego [wykonywania zapytań](concepts-query-language.md) na pytania dotyczące środowiska. 

Jednym ze sposobów wykonywania zapytań dotyczących bliźniaczych reprezentacji w grafie jest ich *Właściwości*. Zapytania oparte na właściwościach mogą pomóc w udzieleniu odpowiedzi na różne pytania, w tym Znajdowanie wartości odstających w środowisku, które mogą wymagać uwagi.

W tej sekcji zostanie uruchomione zapytanie z odpowiedzią na następujące pytanie: _ **jakie są wszystkie bliźniaczych reprezentacji w moim środowisku z temperaturą powyżej 75?**_

Aby wyświetlić odpowiedź, uruchom następujące zapytanie w polu *Eksplorator zapytań* :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Wycofaj z wyświetlania właściwości przędzy wcześniej, że *Room0* ma temperaturę **70** , a *Room1* ma temperaturę **80**. W związku z tym w tym miejscu tylko _**Room1**_ są wyświetlane w wynikach.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Wyniki zapytania właściwości, zawierające tylko Room1" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Inne operatory porównania ( *<* , *>* ,, *=* lub *! =*) są również obsługiwane w powyższym zapytaniu. Aby wypróbować Twoje pytania, możesz spróbować podłączyć te, różne wartości lub różne właściwości sznurka do zapytania.

## <a name="edit-data-in-the-graph"></a>Edytowanie danych na grafie

Za pomocą Eksploratora ADT można edytować właściwości bliźniaczych reprezentacji reprezentowanych w grafie. W tej sekcji będziemy ** _podnieść temperaturę_ Room0 do 76**.

W tym celu wybierz pozycję *Room0*, aby wyświetlić jej listę właściwości w polu *Eksplorator właściwości* .

Właściwości na tej liście są edytowalne. Wybierz wartość temperatury **70** , aby włączyć wprowadzanie nowej wartości. Wprowadź **76**i naciśnij ikonę *Zapisz* , aby zaktualizować temperaturę do **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Pole "Eksplorator właściwości" wyświetla właściwości dla Room0. Wartość temperatury jest polem edytowalnym pokazującym 76 i wyróżnieniem wokół ikony zapisywania." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Po pomyślnym zapisaniu zobaczysz okno *informacji o poprawek* zawierające kod poprawki, który został użyty w tle za pomocą [interfejsu API](how-to-use-apis-sdks.md) Digital bliźniaczych reprezentacji platformy Azure, aby przeprowadzić aktualizację. Naciśnij przycisk *Zamknij*.

### <a name="query-to-see-the-result"></a>Zapytanie w celu wyświetlenia wyniku

Aby sprawdzić, czy wykres pomyślnie zarejestrował aktualizację do temperatury *Room0*, uruchom ponownie zapytanie ze starszej wersji, aby **uzyskać wszystkie bliźniaczych reprezentacji w środowisku z temperaturą powyżej 75**:

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Teraz, gdy temperatura *Room0* została zmieniona z **70** na **76**, oba bliźniaczych reprezentacji powinny być widoczne w wyniku.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Wyniki zapytania właściwości, pokazujące zarówno Room0, jak i Room1" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Przegląd i contextualize nauk

W tym przewodniku szybki start utworzono wystąpienie usługi Azure Digital bliźniaczych reprezentacji, połączono je z Eksploratorem ADT i wypełniono z przykładowym scenariuszem. 

Następnie zbadasz wykres, przez...
1. Korzystanie z zapytania, aby odpowiedzieć na pytanie dotyczące scenariusza.
2. Edytowanie właściwości w dwucyfrowej formie dwuosiowej.
    * Ponownie uruchomić zapytanie, aby zobaczyć, jak zmieniono odpowiedź w wyniku aktualizacji.

Celem tego ćwiczenia jest zademonstrowanie, jak można użyć grafu Digital bliźniaczych reprezentacji na potrzeby odpowiedzi na pytania dotyczące środowiska, nawet w przypadku zmiany środowiska. 

Mimo że w tym przewodniku szybki start została wprowadzona aktualizacja temperatury ręcznie, jest ona często oparta na usłudze Azure Digital bliźniaczych reprezentacji, która umożliwia łączenie cyfrowych bliźniaczych reprezentacji z rzeczywistymi urządzeniami IoT w taki sposób, aby automatycznie otrzymywały aktualizacje na podstawie danych telemetrycznych. Pozwala to na utworzenie grafu na żywo, który zawsze odzwierciedla rzeczywisty stan środowiska, i używa zapytań, aby uzyskać informacje na temat tego, co dzieje się w środowisku w czasie rzeczywistym.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby zagnieździć prace dla tego przewodnika Szybki Start, należy najpierw zakończyć uruchomioną aplikację konsolową. Spowoduje to wyłączenie połączenia z aplikacją ADT Explorer w przeglądarce, a użytkownik nie będzie mógł wyświetlać danych na żywo w przeglądarce. Możesz zamknąć kartę przeglądarka.

Jeśli planujesz kontynuować korzystanie z samouczków Digital bliźniaczych reprezentacji systemu Azure, wystąpienie używane w tym przewodniku Szybki Start może być ponownie używane na potrzeby tych artykułów i nie trzeba go usunąć.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Na koniec Usuń przykładowe foldery projektu pobrane na maszynę lokalną (_**ADT_Explorer.zip**_ i _**Azure_Digital_Twins_samples.zip**_).

## <a name="next-steps"></a>Następne kroki 

Następnie przejdź do samouczków Digital bliźniaczych reprezentacji systemu Azure, aby utworzyć własny scenariusz i narzędzia interakcji dotyczące platformy Azure Digital bliźniaczych reprezentacji.

> [!div class="nextstepaction"]
> [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)