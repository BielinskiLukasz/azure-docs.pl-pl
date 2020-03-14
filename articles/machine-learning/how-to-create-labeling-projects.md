---
title: Tworzenie projektu etykietowania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć i uruchamiać projekty etykietowania w celu tagowania danych na potrzeby uczenia maszynowego.  Narzędzia te zawierają etykietki z asystą lub przez człowieka w pętli etykietowania, aby pomóc w zadaniu.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/01/2020
ms.openlocfilehash: d39cf8745c6f53cb11bb12561fd452325fe52ac6
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79296952"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Utwórz projekt etykietowania danych i Eksportuj etykiety 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Voluminous danych etykietowania w projektach uczenia maszynowego jest często kłopotliwej. Projekty, które mają składnik programu obsługi komputera, takie jak Klasyfikacja obrazu lub wykrywanie obiektów, zwykle wymagają etykiet dla tysięcy obrazów.
 
[Azure Machine Learning](https://ml.azure.com/) oferuje centralne miejsce do tworzenia i monitorowania projektów etykietowania oraz zarządzania nimi. Służy do koordynowania danych, etykiet i członków zespołu w celu wydajnego zarządzania zadaniami etykietowania. Machine Learning obsługuje klasyfikację obrazów, wiele etykiet lub wiele klas oraz identyfikację obiektów z ograniczonymi polami.

Machine Learning śledzi postęp i utrzymuje kolejkę niekompletnych zadań etykietowania. Etykiety nie potrzebują konta platformy Azure do wzięcia udziału. Po uwierzytelnieniu za pomocą konto Microsoft lub [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), może to zrobić tak długo, jak ich czas zezwala.

Rozpocznij i Zatrzymaj projekt, Dodaj i Usuń etykiety i zespoły oraz monitoruj postęp etykietowania. Dane z etykietami można eksportować w formacie COCO lub jako zestaw danych Azure Machine Learning.

> [!Important]
> Obecnie obsługiwane są tylko projekty klasyfikacji obrazów i identyfikacji obiektów. Ponadto obrazy danych muszą być dostępne w magazynie datastorage obiektów blob platformy Azure. (Jeśli nie masz istniejącego magazynu danych, możesz przekazać obrazy podczas tworzenia projektu). 

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie projektu
> * Określ dane i strukturę projektu
> * Zarządzanie zespołami i osobami, które pracują nad projektem
> * Uruchamianie i monitorowanie projektu
> * Eksportowanie etykiet


## <a name="prerequisites"></a>Wymagania wstępne

* Dane, które chcesz oznaczyć, w plikach lokalnych lub w usłudze Azure Blob Storage.
* Zestaw etykiet, które mają zostać zastosowane.
* Instrukcje dotyczące etykietowania.
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Tworzenie projektu etykietowania

Projekty etykiet są administrowane przy użyciu Azure Machine Learning. Strona **projekty etykiet** służy do zarządzania projektami i osobami. Do projektu jest przypisany co najmniej jeden zespół, a zespół ma przypisaną co najmniej jedną osobę.

Jeśli dane są już w usłudze Azure Blob Storage, należy udostępnić ją jako magazyn danych przed utworzeniem projektu etykietowania. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i rejestrowanie magazynów](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)danych.

Aby utworzyć projekt, wybierz pozycję **Dodaj projekt**. Nadaj projektowi odpowiednią nazwę i wybierz pozycję **etykieta typ zadania**.

![Kreator tworzenia etykiet dla projektu](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Wybierz **wiele klas klasyfikacji obrazów** dla projektów, gdy chcesz zastosować tylko *jedną klasę* z zestawu klas do obrazu.
* Wybierz opcję **Klasyfikacja obrazu wieloetykietowego** dla projektów, gdy chcesz zastosować *jedną lub więcej* etykiet z zestawu klas do obrazu. Na przykład zdjęcie psa może mieć etykietę z obu *pies* i *Daytime*.
* Wybierz pozycję **Identyfikacja obiektu (pole ograniczenia)** dla projektów, gdy chcesz przypisać klasę i pole ograniczenia do każdego obiektu w obrazie.

Wybierz pozycję **dalej** , gdy wszystko będzie gotowe do kontynuowania.

## <a name="specify-the-data-to-label"></a>Określ dane do etykiet

Jeśli utworzono już zestaw danych, który zawiera dane, wybierz go z listy rozwijanej **Wybierz istniejący zestaw danych** . Lub wybierz pozycję **Utwórz zestaw danych** , aby użyć istniejącego magazynu usługi Azure datastore lub do przekazywania plików lokalnych.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Tworzenie zestawu danych na podstawie usługi Azure datastore

W wielu przypadkach warto tylko przekazać pliki lokalne. Jednak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) zapewnia szybszy i bardziej niezawodny sposób transferu dużej ilości danych. Zalecamy Eksplorator usługi Storage jako domyślny sposób przenoszenia plików.

Aby utworzyć zestaw danych na podstawie danych, które zostały już zapisane w usłudze Azure Blob Storage:

1. Wybierz pozycję **Utwórz zestaw danych** > **ze sklepu datastore**.
1. Przypisz **nazwę** do zestawu danych.
1. Wybierz pozycję **plik** jako **Typ zestawu danych**.  
1. Wybierz magazyn danych.
1. Jeśli dane są w podfolderze w magazynie obiektów blob, wybierz pozycję **Przeglądaj** , aby wybrać ścieżkę.
    * Dołącz "/* *" do ścieżki, aby uwzględnić wszystkie pliki w podfolderach wybranej ścieżki.
    * Dołącz "* */* . *", aby uwzględnić wszystkie dane w bieżącym kontenerze i jego podfolderach.
1. Podaj opis zestawu danych.
1. Wybierz opcję **Dalej**.
1. Potwierdź szczegóły. Wybierz pozycję **Wstecz** , aby zmodyfikować ustawienia, lub **Utwórz** , aby utworzyć zestaw danych.

> [!NOTE]
> Wybrane dane są ładowane do projektu.  Dodawanie większej ilości danych do magazynu danych nie zostanie wyświetlone w projekcie po utworzeniu projektu.  

### <a name="create-a-dataset-from-uploaded-data"></a>Tworzenie zestawu danych na podstawie przekazanych danych

Aby bezpośrednio przekazać dane:

1. Wybierz pozycję **Utwórz zestaw danych** > **z plików lokalnych**.
1. Przypisz **nazwę** do zestawu danych.
1. Wybierz opcję "plik" jako **Typ zestawu danych**.
1. *Opcjonalne:* Wybierz pozycję **Ustawienia zaawansowane** , aby dostosowywać magazyn danych, kontener i ścieżkę do swoich potrzeb.
1. Wybierz pozycję **Przeglądaj** , aby wybrać pliki lokalne do przekazania.
1. Podaj opis zestawu danych.
1. Wybierz opcję **Dalej**.
1. Potwierdź szczegóły. Wybierz pozycję **Wstecz** , aby zmodyfikować ustawienia, lub **Utwórz** , aby utworzyć zestaw danych.

Dane są przekazywane do domyślnego magazynu obiektów BLOB ("workspaceblobstore") obszaru roboczego Machine Learning.

## <a name="specify-label-classes"></a>Określanie klas etykiet

Na stronie **klasy etykiet** Określ zestaw klas do kategoryzacji danych. Należy to uważnie robić, ponieważ ich dokładność i szybkość są zależne od ich możliwości wyboru między klasami. Na przykład zamiast wyszukać pełną rodzaj i gatunek dla roślin lub zwierząt, użyj kodu pola lub skrócić rodzaj.

Wprowadź jedną etykietę na wiersz. Użyj przycisku **+** , aby dodać nowy wiersz. Jeśli masz więcej niż 3 lub 4 etykiety, ale mniej niż 10, możesz chcieć utworzyć prefiks nazw z cyframi ("1:", "2:"), aby etykietki mogły używać klawiszy numerycznych do przyspieszenia ich pracy.

## <a name="describe-the-labeling-task"></a>Opisywanie zadania etykietowania

Ważne jest jasne wyjaśnienie zadania etykietowania. Na stronie **instrukcje etykietowania** można dodać link do zewnętrznej witryny w celu etykietowania instrukcji lub podać instrukcje w polu edycji na stronie. Należy zachować zorientowane na zadania instrukcje i odpowiednie dla odbiorców. Weź pod uwagę następujące pytania:

* Co to są etykiety, które zobaczysz i w jaki sposób będą wybierać między nimi? Czy istnieje tekst referencyjny, do którego się odwołuje?
* Co należy zrobić, jeśli etykieta nie wydaje się odpowiednia?
* Co należy zrobić, jeśli wiele etykiet wydaje się stosowne?
* Jaki próg ufności ma mieć zastosowanie do etykiety? Czy chcesz, aby "Najlepsza wartość", jeśli nie są one określone?
* Co należy zrobić przy użyciu częściowo zamknięte lub nakładających się obiektów?
* Co należy zrobić, jeśli obiekt zainteresowania jest przycinany przez brzeg obrazu?
* Co należy zrobić po przesłaniu etykiety, jeśli uważasz, że popełniono błąd?

W przypadku pól ograniczenia ważne pytania obejmują:

* Jak jest zdefiniowane pole ograniczenia dla tego zadania? Czy powinien znajdować się w całości na wewnętrznej stronie obiektu, czy powinien znajdować się na zewnątrz? Czy powinien być przycięty jak najlepiej jak to możliwe?
* Jakiego poziomu opieka i spójność ma oczekiwać, że etykiety mają być stosowane przy definiowaniu powiązanych pól?
* Jak dodać etykietę do obiektu, który jest częściowo pokazywany w obrazie? 
* Jak dodać etykietę do obiektu, który częściowo został objęty przez inny obiekt?

>[!NOTE]
> Pamiętaj, że etykiety będą mogły wybrać pierwszych 9 etykiet przy użyciu klawiszy Number 1-9.

## <a name="use-ml-assisted-labeling"></a>Użyj etykiety z asystą

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Strona **etykietowania** z pomocąą ml umożliwia wyzwalanie automatycznych modeli uczenia maszynowego w celu przyspieszenia zadania etykietowania. Na początku projektu etykietowania obrazy są ustawiane losowo w kolejności losowej, aby zmniejszyć liczbę potencjalnych odchyleń. Jednak wszelkie bias, które znajdują się w zestawie danych, zostaną odzwierciedlone w modelu przeszkolonym. Na przykład jeśli 80% obrazów ma jedną klasę, wówczas około 80% danych używanych do uczenia modelu będzie tej klasy. To szkolenie nie obejmuje aktywnego uczenia się.

Ta funkcja jest dostępna dla zadań klasyfikacji obrazów (wiele klas lub wieloetykietowe).  

Wybierz opcję *Włącz oznaczenie ml z etykietami* i określ procesor GPU, aby włączyć obsługę etykietowania, która składa się z dwóch faz:
* Obsługa klastrów
* Etykietowanie

Dokładna liczba obrazów z etykietami, które są niezbędne do rozpoczęcia etykietowania pomocy, nie jest ustalona.  Może się to różnić w zależności od jednego projektu etykietowego do innego. W przypadku niektórych projektów czasami możliwe jest wyświetlenie przednich lub zadań klastra po ręcznym etykietowaniu obrazów 300. Przy pomocy zatytułowanej do uczenia maszynowego jest stosowana technika o nazwie *nauka przekazu*, która korzysta ze wstępnie przeszkolonego modelu, aby przeskoczyć proces szkolenia. Jeśli klasy zestawu danych są podobne do tych w modelu wstępnie szkolonym, etykietki wstępne mogą być dostępne po tylko kilku setkach obrazów oznaczonych ręcznie. Jeśli zestaw danych znacznie różni się od danych używanych do wstępnego uczenia modelu, może to zająć dużo czasu.

Ponieważ końcowe etykiety nadal opierają się na danych wejściowych z Labeler, Technologia ta jest czasami wywoływana *przez człowieka w pętli* etykiet.

### <a name="clustering"></a>Obsługa klastrów

Po przesłaniu pewnej liczby etykiet model uczenia maszynowego zacznie grupować podobne obrazy.  Te podobne obrazy są prezentowane na etykietach na tym samym ekranie w celu przyspieszenia ręcznego tagowania. Klastrowanie jest szczególnie przydatne, gdy Labeler przegląda siatkę obrazów 4, 6 lub 9. 

Gdy model uczenia maszynowego został przeszkolony na ręcznie oznaczonych danych, model zostanie obcięty do jego ostatniej w pełni połączonej warstwy. Obrazy bez etykiet są następnie przenoszone przez obcięty model w procesie powszechnie znanym jako "osadzanie" lub "cechowania". Spowoduje to osadzenie każdego obrazu w miejscu o dużej wymiarze zdefiniowanym przez tę warstwę modelu. Obrazy, które są najbliższe sąsiadów w miejscu, są używane na potrzeby zadań związanych z klastrowaniem. 

### <a name="prelabeling"></a>Etykietowanie

Po przesłaniu większej liczby etykiet obrazu model klasyfikacji jest używany do przewidywania tagów obrazu.  Labeler teraz widzi strony, które zawierają przewidywane etykiety, które znajdują się już w każdym obrazie.  Następnie zadanie to przegląda te etykiety i poprawia wszystkie nieoznaczone obrazy przed przesłaniem strony.  

Po przeszkoleniu modelu uczenia maszynowego na ręcznie etykietowanych danych model jest oceniany na zestawie testów z ręcznie oznaczonymi obrazami, aby określić jego dokładność na różnych różnych progach ufności. Ten proces oceny jest używany do określenia progu pewności, powyżej którego model jest wystarczająco dokładny, aby pokazać etykietki wstępne. Model jest następnie oceniany pod kątem braku etykietowania danych. Obrazy z przewidywaniami bardziej wątpliwości niż ten próg są używane do wstępnego etykietowania.

> [!NOTE]
> Etykieta zatytułowana "ML" jest dostępna **tylko** w obszarze roboczym wersji Enterprise Edition.

## <a name="initialize-the-labeling-project"></a>Inicjowanie projektu etykietowania

Po zainicjowaniu projektu etykietowania niektóre aspekty projektu są niezmienne. Nie można zmienić typu zadania lub zestawu danych. *Możesz* zmodyfikować etykiety i adres URL opisu zadania. Uważnie przejrzyj ustawienia przed utworzeniem projektu. Po przesłaniu projektu nastąpi powrót do strony głównej **etykietowania danych** , co spowoduje wyświetlenie projektu jako **inicjującego**. Ta strona nie jest automatycznie odświeżana. Dlatego po wstrzymaniu ręcznie Odśwież stronę, aby zobaczyć stan projektu jako **utworzony**.

## <a name="manage-teams-and-people"></a>Zarządzanie zespołami i osobami

Domyślnie każdy utworzony projekt etykietowania otrzymuje nowego zespołu, który jest członkiem. Ale zespoły mogą być współużytkowane między projektami. I projekty mogą mieć więcej niż jeden zespół. Aby utworzyć zespół, wybierz pozycję **Dodaj zespół** na stronie **zespoły** .

Osobami zarządzanymi na stronie **osoby** . Dodaj i Usuń osoby według adresu e-mail. Każdy Labeler musi uwierzytelniać się za pomocą konto Microsoft lub Azure Active Directory, jeśli jest używany.  

Po dodaniu osoby można ją przypisać do jednego lub kilku zespołów: Przejdź do strony **zespoły** , wybierz zespół, a następnie wybierz pozycję **Przypisz osoby** lub **Usuń osoby**.

Aby wysłać wiadomość e-mail do zespołu, wybierz zespół, aby wyświetlić stronę **szczegółów zespołu** . Na tej stronie wybierz pozycję **zespół poczty e-mail** , aby otworzyć roboczą wiadomość e-mail z adresami wszystkich członków zespołu.

## <a name="run-and-monitor-the-project"></a>Uruchamianie i monitorowanie projektu

Po zainicjowaniu projektu platforma Azure rozpocznie działanie. Wybierz projekt na stronie **etykietowanie danych** głównych, aby przejść do **szczegółów projektu**. Karta **pulpit nawigacyjny** pokazuje postęp zadania etykietowania.

Na karcie **dane** można zobaczyć zestaw danych i przejrzeć dane z etykietami. Jeśli zobaczysz nieprawidłowe etykiety danych, zaznacz ją i wybierz polecenie **Odrzuć**, co spowoduje usunięcie etykiet i umieszczenie danych z powrotem w kolejce bez etykiety.

Za pomocą karty **zespół** Przypisz lub Cofnij przypisanie zespołów do projektu.

Aby wstrzymać lub ponownie uruchomić projekt, wybierz przycisk **wstrzymaj**/**Rozpocznij** . Dane można etykietować tylko wtedy, gdy projekt jest uruchomiony.

Dane można oznaczyć bezpośrednio na stronie **szczegółów projektu** , wybierając pozycję **dane etykiet**.

## <a name="add-new-label-class-to-a-project"></a>Dodaj nową klasę etykiet do projektu

Podczas tworzenia etykiet może się okazać, że do klasyfikowania obrazów są potrzebne dodatkowe etykiety.  Na przykład możesz chcieć dodać etykietę "nieznany" lub "inne", aby wskazać mylące obrazy.

Wykonaj następujące kroki, aby dodać jedną lub więcej etykiet do projektu:

1. Wybierz projekt na stronie **etykietowanie danych** głównych.
1. W górnej części strony wybierz pozycję **Wstrzymaj** , aby zatrzymać etykietki w swoich działaniach.
1. Wybierz kartę **szczegóły** .
1. Na liście po lewej stronie wybierz pozycję **klasy etykiet**.
1. W górnej części listy wybierz pozycję **+ Dodaj etykiety** ![dodaj etykietę](media/how-to-create-labeling-projects/add-label.png)
1. W formularzu Dodaj nową etykietę i wybierz sposób wykonywania czynności.  Ze względu na to, że zmieniono etykiety dostępne dla obrazu, wybierz sposób traktowania danych, które zostały oznaczone etykietą:
    * Zacznij od początku, usuwając wszystkie istniejące etykiety.  Wybierz tę opcję, jeśli chcesz zacząć od początku do nowego pełnego zestawu etykiet. 
    * Zacznij od początku, zachowując wszystkie istniejące etykiety.  Wybierz tę opcję, aby oznaczyć wszystkie dane jako bez etykiet, ale zachować istniejące etykiety jako tag domyślny dla obrazów, które zostały wcześniej oznaczone etykietami.
    * Kontynuuj, zachowując wszystkie istniejące etykiety. Wybierz tę opcję, aby zapewnić, że wszystkie dane mają już etykietę, i Zacznij używać nowej etykiety dla danych, które nie zostały jeszcze oznaczone etykietami.
1. Zmodyfikuj stronę z instrukcjami w razie potrzeby dla nowych etykiet.
1. Po dodaniu wszystkich nowych etykiet w górnej części strony wybierz pozycję **Rozpocznij** , aby ponownie uruchomić projekt.  

## <a name="export-the-labels"></a>Eksportowanie etykiet

W dowolnym momencie możesz wyeksportować dane etykiet dla Machine Learning eksperymentowania. Etykiety obrazów można eksportować w [formacie Coco](http://cocodataset.org/#format-data) lub jako zestaw danych Azure Machine Learning. Użyj przycisku **Eksportuj** na stronie **szczegóły projektu** w projekcie etykietowania.

Plik COCO jest tworzony w domyślnym magazynie obiektów BLOB obszaru roboczego Azure Machine Learning w folderze w obszarze *Export/Coco*. Możesz uzyskać dostęp do wyeksportowanego zestawu danych Azure Machine Learning w sekcji **zestawy** danych w Machine Learning. Strona szczegóły zestawu danych zawiera również przykładowy kod umożliwiający uzyskanie dostępu do etykiet z poziomu języka Python.

![Wyeksportowany zestaw danych](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Następne kroki

* Obrazy etykiet do [wykrywania klasyfikacji obrazów lub obiektów](how-to-label-images.md)
* Dowiedz się więcej o [Azure Machine Learning i Machine Learning Studio (klasyczne)](compare-azure-ml-to-studio-classic.md)
