---
title: 'Samouczek 1: przewidywanie ryzyka kredytowego'
titleSuffix: Azure Machine Learning Studio (classic)
description: Szczegółowy Samouczek przedstawiający sposób tworzenia rozwiązania do analizy predykcyjnej w celu oceny ryzyka kredytowego w klasycznej wersji Azure Machine Learning Studio. Ten samouczek jest pierwszą częścią trzyczęściowej serii.  Pokazano w nim, jak utworzyć obszar roboczy, przekazać dane i utworzyć eksperyment.
keywords: ryzyko kredytowe, rozwiązanie analizy predykcyjnej, ocena ryzyka
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: b861fbca1f8b4fd6b313e770b1636674767e406a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492624"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>Samouczek 1: przewidywanie ryzyka kredytowego — Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

W tym samouczku szczegółowo przedstawiono proces opracowywania rozwiązania analizy predykcyjnej. Tworzysz prosty model w Machine Learning Studio (klasyczny).  Następnie wdrożysz model jako usługę internetową w ramach usługi Azure Machine Learning.  Wdrożony model może tworzyć przewidywania przy użyciu nowych danych. Ten samouczek jest **pierwszą częścią trzyczęściowej serii**.

Załóżmy, że chcesz przewidzieć ryzyko kredytowe osoby na podstawie informacji przekazanych we wniosku kredytowym.  

Ocena ryzyka kredytowego to złożony problem, ale w tym samouczku zostanie on nieco uproszczony. Będziesz jej używać jako przykładu tworzenia rozwiązania do analizy predykcyjnej przy użyciu Microsoft Azure Machine Learning Studio (klasyczne). Będziesz używać klasycznej wersji Azure Machine Learning Studio i usługi sieci Web Machine Learning dla tego rozwiązania.  

W tym trzyczęściowym samouczku zaczniesz od publicznie dostępnych danych ryzyka kredytowego.  Następnie wdrożysz i wytrenujesz model predykcyjny.  Na koniec wdrożysz model jako usługę internetową.

W tej części samouczka zostaną wykonane następujące czynności: 
 
> [!div class="checklist"]
> * Tworzenie obszaru roboczego Machine Learning Studio (klasyczny)
> * Przekazywanie istniejących danych
> * Tworzenie eksperymentu

Następnie możesz użyć tego eksperymentu, aby [wytrenować modele w części 2](tutorial-part2-credit-risk-train.md) i [wdrożyć je w części 3](tutorial-part3-credit-risk-deploy.md).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że wcześniej korzystano Machine Learning Studio (klasyczny) co najmniej raz i że masz pewne zrozumienie koncepcji uczenia maszynowego. Nie zakładamy jednak, że jesteś ekspertem w którejkolwiek z tych dziedzin.

Jeśli wcześniej nie korzystasz z **Azure Machine Learning Studio (klasyczny)** , możesz zacząć od przewodnika Szybki Start, [utworzyć pierwszy eksperyment nauki o danych w Azure Machine Learning Studio (klasyczny)](create-experiment.md). Przewodnik Szybki Start przeprowadzi Cię przez Machine Learning Studio (klasyczny) po raz pierwszy. Podstawowe informacje przedstawione w tym samouczku obejmują przeciąganie modułów i upuszczanie ich w obszarze eksperymentu, łączenie modułów ze sobą, uruchamianie eksperymentu oraz przeglądanie wyników.


> [!TIP] 
> Funkcjonalną kopię eksperymentu opracowywanego w tym samouczku można znaleźć w witrynie [Azure AI Gallery](https://gallery.azure.ai). Przejdź do **[samouczka — przewidywanie ryzyka kredytowego](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** , a następnie kliknij pozycję **Otwórz w programie Studio** , aby pobrać kopię eksperymentu do obszaru roboczego Machine Learning Studio (klasycznego).
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Tworzenie obszaru roboczego Machine Learning Studio (klasyczny)

Aby użyć Machine Learning Studio (klasyczny), musisz mieć obszar roboczy Microsoft Azure Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.  

Aby utworzyć obszar roboczy, zobacz temat [Tworzenie i udostępnianie obszaru roboczego Azure Machine Learning Studio (klasycznego)](create-workspace.md).

Po utworzeniu obszaru roboczego Otwórz Machine Learning Studio (klasyczny) ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Jeśli masz więcej niż jeden obszar roboczy, możesz wybrać odpowiedni obszar roboczy na pasku narzędzi w prawym górnym rogu okna.

![Wybieranie obszaru roboczego w programie Studio (klasyczny)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Jeśli jesteś właścicielem obszaru roboczego, możesz udostępniać eksperymenty, nad którymi pracujesz, zapraszając inne osoby do obszaru roboczego. Można to zrobić w Machine Learning Studio (klasyczny) na stronie **Ustawienia** . Wystarczy, że każdy użytkownik będzie miał konto Microsoft lub konto organizacji.
> 
> Na stronie **SETTINGS** (USTAWIENIA) kliknij pozycję **USERS** (UŻYTKOWNICY), a następnie kliknij przycisk **INVITE MORE USERS** (ZAPROŚ UŻYTKOWNIKÓW) w dolnej części okna.
> 

## <a name="upload"></a>Przekazywanie istniejących danych

Aby opracować model predykcyjny na potrzeby oceny ryzyka kredytowego, potrzebujesz danych, których można użyć do wytrenowania, a następnie przetestowania modelu. Na potrzeby tego samouczka użyjesz zestawu danych „UCI Statlog (German Credit Data) Data Set” z repozytorium UC Irvine Machine Learning. Znajdziesz go tutaj:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Użyjesz pliku o nazwie **german.data**. Pobierz ten plik na lokalny dysk twardy.  

Zestaw danych **german.data** zawiera wiersze 20 zmiennych z 1000 złożonych w przeszłości wniosków kredytowych. Tych 20 zmiennych reprezentuje zestaw cech tego zestawu danych (*wektor cech*), który dostarcza charakterystyki identyfikujące każdego wnioskodawcę kredytowego. Dodatkowa kolumna w każdym wierszu reprezentuje obliczone ryzyko kredytowe wnioskodawcy, przy czym dla 700 kandydatów ryzyko kredytowe oceniono jako niskie, a dla 300 kandydatów jako wysokie.

Witryna internetowa UCI zawiera opis atrybutów wektora cech dla tych danych. Te dane obejmują informacje finansowe, historię kredytową, stan zatrudnienia i dane osobowe. Każdy wnioskodawca został poddany klasyfikacji binarnej, która wskazuje, czy jego ryzyko kredytowe jest wysokie, czy niskie. 

Użyjesz tych danych, aby wytrenować model analizy predykcyjnej. Gdy wszystko będzie gotowe, model powinien mieć możliwość akceptowania wektora funkcji dla nowej osoby i przewidywania, czy są to niskie czy wysokie ryzyko kredytowe.  

Jest jeszcze pewien interesujący zwrot.

Opis zestawu danych w witrynie internetowej UCI zawiera informację, jakie są koszty, jeśli ryzyko kredytowe osoby zostanie ocenione błędnie.
Jeśli model przewidzi wysokie ryzyko kredytowe dla osoby, której faktyczne ryzyko jest niskie, oznacza to, że model przeprowadził błędną klasyfikację.

Jednak odwrotna błędna klasyfikacja jest pięć razy bardziej kosztowna dla instytucji finansowej: jeśli model przewidzi niskie ryzyko kredytowe dla osoby, której faktyczne ryzyko jest wysokie.

Dlatego warto wytrenować model tak, aby koszt tego drugiego typu błędnej klasyfikacji był pięć razy wyższy, niż odwrotnej błędnej klasyfikacji.

Prostym sposobem, aby to zrobić podczas trenowania modelu w eksperymencie, jest zduplikowanie (pięć razy) tych wpisów, które reprezentują wnioskodawcę z wysokim ryzykiem kredytowym. 

W takim przypadku, jeśli model błędnie sklasyfikuje niskie ryzyko kredytowe dla kogoś, kogo rzeczywiste ryzyko kredytowe jest wysokie, zrobi ten sam błąd pięć razy — po razie dla każdego duplikatu. Takie rozwiązanie zwiększy koszt tego błędu w wynikach trenowania.


### <a name="convert-the-dataset-format"></a>Konwertowanie formatu zestawu danych

Oryginalny zestaw danych ma format wartości rozdzielanych spacją. Klasyczna wersja Machine Learning Studio działa lepiej przy użyciu pliku wartości rozdzielanych przecinkami (CSV), dzięki czemu można przekonwertować zestaw danych, zastępując spacje przecinkami.  

Istnieje wiele sposobów na przekonwertowanie danych. Jednym z nich jest użycie następującego polecenia programu Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Innym sposobem jest użycie polecenia sed systemu Unix:  

    sed 's/ /,/g' german.data > german.csv  

W obu przypadkach zostanie utworzona rozdzielana przecinkami wersja danych w pliku o nazwie **german.csv**, którego możesz użyć w eksperymencie.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Przekaż zestaw danych do Machine Learning Studio (klasyczny)

Gdy dane zostaną przekonwertowane do formatu CSV, należy przekazać je do klasycznej wersji Machine Learning Studio. 

1. Otwórz stronę główną Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Kliknij menu ![Menu](./media/tutorial-part1-credit-risk/menu.png) w lewym górnym rogu okna, kliknij pozycję **Azure Machine Learning**, wybierz pozycję **Studio** i zaloguj się.

3. Kliknij pozycję **+ NEW** (+NOWY) w dolnej części okna.

4. Wybierz pozycję **DATASET** (ZESTAW DANYCH).

5. Wybierz pozycję **FROM LOCAL FILE** (Z PLIKU LOKALNEGO).

    ![Dodawanie zestawu danych z pliku lokalnego](./media/tutorial-part1-credit-risk/add-dataset.png)

6. W oknie dialogowym **Upload a new dataset** (Przekazywanie nowego zestawu danych) kliknij przycisk Browse (Przeglądaj) i znajdź utworzony plik **german.csv**.

7. Wprowadź nazwę dla zestawu danych. Na potrzeby tego samouczka nadaj mu nazwę „UCI German Credit Card Data”.

8. Jako typ danych wybierz opcję **Generic CSV File With no header (.nh.csv)** (Ogólny plik CSV bez nagłówka).

9. Jeśli chcesz, dodaj opis.

10. Kliknij znacznik wyboru **OK**.  

    ![Przekazywanie zestawu danych](./media/tutorial-part1-credit-risk/upload-dataset.png)

Spowoduje to przekazanie danych do modułu zestawu danych, którego możesz użyć w eksperymencie.

Można zarządzać zestawami danych, które zostały przekazane do Studio (klasyczne), klikając kartę **zestawy danych** z lewej strony okna Studio (klasyczne).

![Zarządzanie zestawami danych](./media/tutorial-part1-credit-risk/dataset-list.png)

Aby uzyskać więcej informacji na temat importowania innych typów danych do eksperymentu, zobacz [Importowanie danych szkoleniowych do Azure Machine Learning Studio (klasyczne)](import-data.md).

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

Następnym krokiem w tym samouczku jest utworzenie eksperymentu w klasycznej wersji Machine Learning Studio, która używa przekazanego zestawu danych.  

1. W programie Studio (klasyczny) kliknij pozycję **+ Nowy** w dolnej części okna.
1. Wybierz pozycję **EXPERIMENT** (EKSPERYMENT), a następnie wybierz pozycję „Blank Experiment” (Pusty eksperyment). 

    ![Tworzenie nowego eksperymentu](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Wybierz domyślną nazwę eksperymentu w górnej części kanwy i zmień ją, wpisując tekst opisowy.

    ![Zmienianie nazwy eksperymentu](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Dobrym pomysłem jest wypełnienie pól **Summary** (Podsumowanie) i **Description** (Opis) dla eksperymentu w okienku **Properties** (Właściwości). Te właściwości dają możliwość udokumentowania eksperymentu, aby każdy, kto będzie go później przeglądał, mógł zrozumieć jego cele i metodologię.
   > 
   > ![Właściwości eksperymentu](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. W palecie modułów z lewej strony kanwy eksperymentu rozwiń pozycję **Saved Datasets** (Zapisane zestawy danych).
1. W obszarze **My Datasets** (Moje zestawy danych) znajdź utworzony przez siebie zestaw danych i przeciągnij go na kanwę. Zestaw danych możesz również znaleźć, wprowadzając jego nazwę w polu **wyszukiwania** nad paletą.  

    ![Dodawanie zestawu danych do eksperymentu](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Przygotowywanie danych

Można wyświetlić pierwsze 100 wierszy danych i niektóre informacje statystyczne dla całego zestawu danych: kliknij port wyjściowy zestawu danych (mały okrąg u dołu) i wybierz opcję **Wizualizuj**.  

Ponieważ plik danych nie zawiera nagłówków kolumn, w klasycznej wersji programu Studio udostępniono nagłówki ogólne (Kol1, Col2 *itp.* ). Dobre nagłówki nie są niezbędne do utworzenia modelu, ale ułatwiają pracę z danymi w eksperymencie. Ponadto po opublikowaniu tego modelu w usłudze internetowej nagłówki pomagają w identyfikowaniu kolumn użytkownikowi usługi.  

Nagłówki kolumn można dodawać przy użyciu modułu [Edytowanie metadanych][edit-metadata] .

Aby zmienić metadane skojarzone z zestawem danych, należy użyć modułu [Edytowanie metadanych][edit-metadata] . W tym przypadku użyjesz go do nadania bardziej przyjaznych nazw nagłówkom kolumn. 

Aby użyć [edycji metadanych][edit-metadata], należy najpierw określić kolumny do zmodyfikowania (w tym przypadku wszystkie). Następnie należy określić akcję do wykonania dla tych kolumn (w tym przypadku zmiana nagłówków kolumn).

1. W palecie modułów wpisz wyraz „metadata” (metadane) w polu **Search** (Wyszukaj). [Edytowanie metadanych][edit-metadata] pojawia się na liście modułów.

1. Kliknij i przeciągnij moduł [Edytuj metadane][edit-metadata] na kanwę i upuść go poniżej zestawu danych, który został dodany wcześniej.

1. Połącz zestaw danych z [edycją metadanych][edit-metadata]: kliknij port wyjściowy zestawu danych (mały okrąg w dolnej części zestawu danych), przeciągnij do portu wejściowego [edytowania metadanych][edit-metadata] (mały okrąg w górnej części modułu), a następnie zwolnij przycisk myszy. Zestaw danych i moduł pozostaną połączone, nawet jeśli będziesz przenosić je w obrębie kanwy.
 
    Eksperyment powinien wyglądać teraz mniej więcej tak:  

    ![Dodawanie modułu Edytowanie metadanych](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    Czerwony wykrzyknik wskazuje, że dla tego modułu nie ustawiono jeszcze właściwości. Zrobisz to w następnym kroku.

    > [!TIP]
    > Aby dodać komentarz do modułu, kliknij dwukrotnie moduł i wpisz tekst. Pozwoli to od razu sprawdzić rolę modułu w eksperymencie. W takim przypadku kliknij dwukrotnie moduł [Edytuj metadane][edit-metadata] i wpisz komentarz "Dodaj nagłówki kolumn". Kliknij dowolne miejsce na kanwie, aby zamknąć pole tekstowe. Aby wyświetlić komentarz, kliknij strzałkę w dół na module.
    > 
    > ![Moduł Edytowanie metadanych z dodanym komentarzem](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Wybierz pozycję [Edytuj metadane][edit-metadata], a następnie w okienku **Właściwości** z prawej strony kanwy kliknij pozycję **Uruchom selektor kolumny**.

1. W oknie dialogowym **Select columns** (Wybieranie kolumn) wybierz wszystkie wiersze w obszarze **Available Columns** (Dostępne kolumny) i kliknij pozycję > w celu przeniesienia ich do obszaru **Selected Columns** (Wybrane kolumny).
   Okno dialogowe powinno wyglądać następująco:

   ![Selektor kolumn z zaznaczonymi wszystkimi kolumnami](./media/tutorial-part1-credit-risk/select-columns.png)


1. Kliknij znacznik wyboru **OK**.

1. Z powrotem w okienku **Properties** (Właściwości) poszukaj parametru **New column names** (Nowe nazwy kolumn). W tym polu wprowadź listę nazw dla 21 kolumn w zestawie danych, rozdzielane przecinkami i w kolejności kolumn. Nazwy kolumn możesz uzyskać z dokumentacji zestawu danych w witrynie UCI lub, dla wygody, możesz skopiować i wkleić poniższą listę:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   Okienko Properties (Właściwości) wygląda następująco:

   ![Właściwości modułu Edytowanie metadanych](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Jeśli chcesz zweryfikować nagłówki kolumn, uruchom eksperyment (kliknij przycisk **RUN** (URUCHOM) poniżej kanwy eksperymentu). Po zakończeniu działania (zielony znacznik wyboru pojawia się w obszarze [Edytowanie metadanych][edit-metadata]) kliknij port wyjściowy modułu [Edytowanie metadanych][edit-metadata] i wybierz polecenie **Wizualizuj**. W ten sam sposób możesz wyświetlić dane wyjściowe każdego modułu, aby sprawdzić postęp danych w eksperymencie.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Tworzenie szkoleniowych i testowych zestawów danych

Potrzebujesz danych, aby wytrenować model i aby go przetestować.
Z tego względu w następnym kroku eksperymentu podzielisz zestaw danych na dwa oddzielne zestawy danych: jeden na potrzeby trenowania naszego modelu, a drugi na potrzeby testowania.

W tym celu należy użyć modułu [Split Data][split] .  

1. Znajdź moduł [Split Data][split] , przeciągnij go na kanwę, a następnie połącz go z modułem [Edytowanie metadanych][edit-metadata] .

1. Domyślnie współczynnik podziału wynosi 0,5 i jest ustawiony parametr **Randomized split** (Podział losowy). Oznacza to, że Losowa połowa danych jest wyprowadzana przez jeden port modułu [Split Data][split] i połowa do drugiego. Możesz dostosować te parametry, jak również parametr **Random seed** (Podział losowy), aby zmienić podział między danymi szkoleniowymi i testowymi. W tym przykładzie pozostaw je tak, jak są.
   
   > [!TIP]
   > Właściwość **Fraction of rows in the first output dataset** (Odsetek wierszy w pierwszym zestawie danych wyjściowych) określa, ile danych przechodzi przez *lewy* port wyjściowy. Jeśli na przykład ustawisz współczynnik o wartości 0,7, to 70% danych będzie przechodzić przez lewy port wyjściowy, a 30% przez prawy port.  
   > 
   > 

1. Kliknij dwukrotnie moduł [Split Data][split] i wprowadź komentarz "szkolenia/dane testowe podzielone na 50%". 

Możesz korzystać z danych wyjściowych modułu [Split Data][split] , ale zamiast tego wybieramy użycie z lewej strony jako dane szkoleniowe i właściwych danych wyjściowych jako danych testowych.  

Jak wspomnieliśmy w [poprzednim kroku](tutorial-part1-credit-risk.md#upload), koszt błędnego sklasyfikowania wysokiego ryzyka kredytowego jako niskiego jest pięć razy wyższy, niż koszt błędnego sklasyfikowania niskiego ryzyka kredytowego jako wysokiego. Aby to uwzględnić, musisz wygenerować nowy zestaw danych, który odzwierciedla tę cechę kosztu. W nowym zestawie danych każdy przykład wysokiego ryzyka jest replikowany pięć razy, podczas gdy przykłady niskiego ryzyka nie są replikowane.   

Tę replikację możesz przeprowadzić przy użyciu kodu języka R:  

1. Znajdź i przeciągnij moduł [Wykonaj skrypt języka R][execute-r-script] na kanwę eksperymentu. 

1. Podłącz lewy port wyjściowy modułu [Split Data][split] do pierwszego portu wejściowego ("pozycję DataSet1") modułu [skryptu wykonywania języka R][execute-r-script] .

1. Kliknij dwukrotnie moduł [wykonywania skryptu języka R][execute-r-script] i wprowadź komentarz "Ustaw korektę kosztów".

1. W okienku **Properties** (Właściwości) usuń domyślny tekst dla parametru **R Script** (Skrypt R) i wprowadź ten skrypt:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Skrypt języka R w module Wykonywanie skryptu R](./media/tutorial-part1-credit-risk/execute-r-script.png)

Tę samą operację replikacji należy wykonać dla każdego danych wyjściowych modułu [Split Data][split] , aby dane dotyczące szkolenia i testowania miały takie samo dostosowanie kosztów. Najprostszym sposobem na to jest zduplikowanie wykonanego modułu [skryptu języka R][execute-r-script] i połączenie go z innym portem wyjściowym modułu [Split Data][split] .

1. Kliknij prawym przyciskiem myszy moduł [wykonywania skryptu R][execute-r-script] i wybierz polecenie **Kopiuj**.

1. Kliknij prawym przyciskiem myszy kanwę eksperymentu, a następnie wybierz pozycję **Paste** (Wklej).

1. Przeciągnij nowy moduł do pozycji, a następnie podłącz odpowiedni port wyjściowy modułu [Split Data][split] do pierwszego portu wejściowego tego nowego modułu [skryptu języka R][execute-r-script] . 

1. W dolnej części kanwy kliknij przycisk **Run** (Uruchom). 

> [!TIP]
> Kopia modułu wykonywania skryptu R zawiera ten sam skrypt, co oryginalny moduł. Podczas kopiowania i wklejania modułu na kanwie kopia zachowuje wszystkie właściwości oryginału.  
> 
>

Nasz eksperyment wygląda teraz mniej więcej tak:

![Dodawanie modułu podziału i skryptów języka R](./media/tutorial-part1-credit-risk/experiment.png)

Aby uzyskać więcej informacji na temat używania skryptów języka R w eksperymentach, zobacz [Rozszerzanie eksperymentu przy użyciu języka R](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące kroki: 
 
> [!div class="checklist"]
> * Tworzenie obszaru roboczego Machine Learning Studio (klasyczny)
> * Przekazywanie istniejących danych do obszaru roboczego
> * Tworzenie eksperymentu

Teraz możesz przystąpić do trenowania i ewaluowania modeli dla tych danych.

> [!div class="nextstepaction"]
> [Samouczek 2 — Trenowanie i ewaluowanie modeli](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
