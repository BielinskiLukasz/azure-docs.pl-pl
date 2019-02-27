---
title: Samouczek 1. Przewidywanie ryzyka kredytowego
titleSuffix: Azure Machine Learning Studio
description: Szczegółowy samouczek pokazujący sposób tworzenia rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning Studio. Ten samouczek jest pierwszą częścią trzyczęściowej serii.  Pokazano w nim, jak utworzyć obszar roboczy, przekazać dane i utworzyć eksperyment.
keywords: ryzyko kredytowe, rozwiązanie analizy predykcyjnej, ocena ryzyka
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: c067b0e6a85e0b5c4bd4cbb582de13bb1bc87774
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453617"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio"></a>Samouczek 1. Przewidywanie ryzyka kredytowego — Azure Machine Learning Studio

W tym samouczku szczegółowo przedstawiono proces opracowywania rozwiązania analizy predykcyjnej. W jego ramach opracujesz prosty model w usłudze Machine Learning Studio.  Następnie wdrożysz model jako usługę internetową w ramach usługi Azure Machine Learning.  Wdrożony model może tworzyć przewidywania przy użyciu nowych danych. Ten samouczek jest **pierwszą częścią trzyczęściowej serii**.

Załóżmy, że chcesz przewidzieć ryzyko kredytowe osoby na podstawie informacji przekazanych we wniosku kredytowym.  

Ocena ryzyka kredytowego to złożony problem, ale w tym samouczku zostanie on nieco uproszczony. Użyjesz go jako przykładu na potrzeby przedstawienia sposobu tworzenia rozwiązania analizy predykcyjnej przy użyciu usługi Microsoft Azure Machine Learning Studio. W tym rozwiązaniu skorzystasz z usługi Azure Machine Learning Studio i usługi internetowej Machine Learning.  

W tym trzyczęściowym samouczku zaczniesz od publicznie dostępnych danych ryzyka kredytowego.  Następnie wdrożysz i wytrenujesz model predykcyjny.  Na koniec wdrożysz model jako usługę internetową.

W tej części samouczka zostaną wykonane następujące czynności: 
 
> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Machine Learning Studio
> * Przekazywanie istniejących danych
> * Tworzenie eksperymentu

Następnie możesz użyć tego eksperymentu, aby [wytrenować modele w części 2](tutorial-part2-credit-risk-train.md) i [wdrożyć je w części 3](tutorial-part3-credit-risk-deploy.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że usługa Machine Learning Studio była już przez Ciebie używana przynajmniej raz i że znasz niektóre pojęcia związane z uczeniem maszynowym. Nie zakładamy jednak, że jesteś ekspertem w którejkolwiek z tych dziedzin.

Jeśli usługa **Azure Machine Learning Studio** nie była nigdy wcześniej przez Ciebie używana, możesz najpierw skorzystać z przewodnika Szybki start [Tworzenie pierwszego eksperymentu dotyczącego przetwarzania danych w usłudze Azure Machine Learning Studio](create-experiment.md). Ten przewodnik Szybki start przedstawia pracę z usługą Machine Learning Studio po raz pierwszy. Podstawowe informacje przedstawione w tym samouczku obejmują przeciąganie modułów i upuszczanie ich w obszarze eksperymentu, łączenie modułów ze sobą, uruchamianie eksperymentu oraz przeglądanie wyników.


> [!TIP] 
> Funkcjonalną kopię eksperymentu opracowywanego w tym samouczku można znaleźć w witrynie [Azure AI Gallery](https://gallery.azure.ai). Otwórz stronę **[Tutorial — Predict credit risk](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** (Samouczek — przewidywanie ryzyka kredytowego) i kliknij przycisk **Open in Studio** (Otwórz w usłudze Studio), aby pobrać kopię eksperymentu do obszaru roboczego usługi Machine Learning Studio.
> 


## <a name="create-a-machine-learning-studio-workspace"></a>Tworzenie obszaru roboczego usługi Machine Learning Studio

Aby używać usługi Machine Learning Studio, musisz mieć obszar roboczy usługi Microsoft Azure Machine Learning Studio. Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.  

Aby utworzyć obszar roboczy, zobacz [Tworzenie i udostępnianie obszaru roboczego usługi Azure Machine Learning Studio](create-workspace.md).

Po utworzeniu obszaru roboczego otwórz usługę Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Jeśli masz więcej niż jeden obszar roboczy, możesz wybrać odpowiedni obszar roboczy na pasku narzędzi w prawym górnym rogu okna.

![Wybieranie obszaru roboczego w usłudze Studio](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Jeśli jesteś właścicielem obszaru roboczego, możesz udostępniać eksperymenty, nad którymi pracujesz, zapraszając inne osoby do obszaru roboczego. Możesz to zrobić w usłudze Machine Learning Studio na stronie **SETTINGS** (USTAWIENIA). Wystarczy, że każdy użytkownik będzie miał konto Microsoft lub konto organizacji.
> 
> Na stronie **SETTINGS** (USTAWIENIA) kliknij pozycję **USERS** (UŻYTKOWNICY), a następnie kliknij przycisk **INVITE MORE USERS** (ZAPROŚ UŻYTKOWNIKÓW) w dolnej części okna.
> 

## <a name="upload"></a>Przekazywanie istniejących danych

Aby opracować model predykcyjny na potrzeby oceny ryzyka kredytowego, potrzebujesz danych, których można użyć do wytrenowania, a następnie przetestowania modelu. Na potrzeby tego samouczka użyjesz zestawu danych „UCI Statlog (German Credit Data) Data Set” z repozytorium UC Irvine Machine Learning. Znajdziesz go tutaj:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Użyjesz pliku o nazwie **german.data**. Pobierz ten plik na lokalny dysk twardy.  

Zestaw danych **german.data** zawiera wiersze 20 zmiennych z 1000 złożonych w przeszłości wniosków kredytowych. Tych 20 zmiennych reprezentuje zestaw cech tego zestawu danych (*wektor cech*), który dostarcza charakterystyki identyfikujące każdego wnioskodawcę kredytowego. Dodatkowa kolumna w każdym wierszu reprezentuje obliczone ryzyko kredytowe wnioskodawcy, przy czym dla 700 kandydatów ryzyko kredytowe oceniono jako niskie, a dla 300 kandydatów jako wysokie.

Witryna internetowa UCI zawiera opis atrybutów wektora cech dla tych danych. Te dane obejmują informacje finansowe, historię kredytową, stan zatrudnienia i dane osobowe. Każdy wnioskodawca został poddany klasyfikacji binarnej, która wskazuje, czy jego ryzyko kredytowe jest wysokie, czy niskie. 

Użyjesz tych danych, aby wytrenować model analizy predykcyjnej. Gdy wszystko będzie gotowe, model powinien być w stanie zaakceptować wektor cech dla nowej osoby i przewidzieć, czy jej ryzyko kredytowe jest niskie, czy wysokie.  

Jest jeszcze pewien interesujący zwrot.

Opis zestawu danych w witrynie internetowej UCI zawiera informację, jakie są koszty, jeśli ryzyko kredytowe osoby zostanie ocenione błędnie.
Jeśli model przewidzi wysokie ryzyko kredytowe dla osoby, której faktyczne ryzyko jest niskie, oznacza to, że model przeprowadził błędną klasyfikację.

Jednak odwrotna błędna klasyfikacja jest pięć razy bardziej kosztowna dla instytucji finansowej: jeśli model przewidzi niskie ryzyko kredytowe dla osoby, której faktyczne ryzyko jest wysokie.

Dlatego warto wytrenować model tak, aby koszt tego drugiego typu błędnej klasyfikacji był pięć razy wyższy, niż odwrotnej błędnej klasyfikacji.

Prostym sposobem, aby to zrobić podczas trenowania modelu w eksperymencie, jest zduplikowanie (pięć razy) tych wpisów, które reprezentują wnioskodawcę z wysokim ryzykiem kredytowym. 

W takim przypadku, jeśli model błędnie sklasyfikuje niskie ryzyko kredytowe dla kogoś, kogo rzeczywiste ryzyko kredytowe jest wysokie, zrobi ten sam błąd pięć razy — po razie dla każdego duplikatu. Takie rozwiązanie zwiększy koszt tego błędu w wynikach trenowania.


### <a name="convert-the-dataset-format"></a>Konwertowanie formatu zestawu danych

Oryginalny zestaw danych ma format wartości rozdzielanych spacją. Usługa Machine Learning Studio działa lepiej z plikiem wartości rozdzielanych przecinkami (CSV), dlatego warto przekonwertować zestaw danych, zastępując spacje za pomocą przecinków.  

Istnieje wiele sposobów na przekonwertowanie danych. Jednym z nich jest użycie następującego polecenia programu Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Innym sposobem jest użycie polecenia sed systemu Unix:  

    sed 's/ /,/g' german.data > german.csv  

W obu przypadkach zostanie utworzona rozdzielana przecinkami wersja danych w pliku o nazwie **german.csv**, którego możesz użyć w eksperymencie.

### <a name="upload-the-dataset-to-machine-learning-studio"></a>Przekazywanie zestawu danych do usługi Machine Learning Studio

Po przekonwertowaniu danych na format CSV należy je przekazać do usługi Machine Learning Studio. 

1. Otwórz stronę główną usługi Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

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

Aby zarządzać zestawami danych przekazanymi do usługi Studio, kliknij kartę **DATASETS** (ZESTAWY DANYCH) po lewej stronie okna usługi Studio.

![Zarządzanie zestawami danych](./media/tutorial-part1-credit-risk/dataset-list.png)

Aby uzyskać więcej informacji na temat importowania innych typów danych do eksperymentu, zobacz [Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio](import-data.md).

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

Następnym krokiem w tym samouczku jest utworzenie eksperymentu w usłudze Machine Learning Studio, który używa przekazanego zestawu danych.  

1. W usłudze Studio kliknij pozycję **+NEW** (+NOWY) w dolnej części okna.
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

Możesz wyświetlić 100 pierwszych wierszy danych i pewne informacje statystyczne dla całego zestawu danych: Kliknij port wyjściowy zestawu danych (mały okrąg w dolnej części), a następnie wybierz pozycję **Visualize** (Wizualizuj).  

Ponieważ plik danych nie zawiera nagłówków kolumn, usługa Studio wstawiła nagłówki ogólne (Col1, Col2, *itd.*). Dobre nagłówki nie są niezbędne do utworzenia modelu, ale ułatwiają pracę z danymi w eksperymencie. Ponadto po opublikowaniu tego modelu w usłudze internetowej nagłówki pomagają w identyfikowaniu kolumn użytkownikowi usługi.  

Nagłówki kolumn możesz dodać za pomocą modułu [Edit Metadata][edit-metadata] (Edytowanie metadanych).

Modułu [Edit Metadata][edit-metadata] (Edytowanie metadanych) możesz użyć, aby zmienić metadane skojarzone z zestawem danych. W tym przypadku użyjesz go do nadania bardziej przyjaznych nazw nagłówkom kolumn. 

Aby użyć modułu [Edit Metadata][edit-metadata] (Edytowanie metadanych), najpierw należy określić kolumny, które będą modyfikowane (w tym przypadku wszystkie). Następnie określ akcję, która ma zostać wykonana na tych kolumnach (w tym przypadku — zmiana nagłówków kolumn).

1. W palecie modułów wpisz wyraz „metadata” (metadane) w polu **Search** (Wyszukaj). Moduł [Edit Metadata][edit-metadata] (Edytowanie metadanych) zostanie wyświetlony na liście modułów.

1. Kliknij i przeciągnij moduł [Edit Metadata][edit-metadata] (Edytowanie metadanych) na kanwę i upuść go poniżej dodanego wcześniej zestawu danych.

1. Połącz zestaw danych z modułem [Edit Metadata][edit-metadata] (Edytowanie metadanych): kliknij port wyjściowy zestawu danych (mały okrąg w dolnej części zestawu danych), przeciągnij go do portu wejściowego modułu [Edit Metadata][edit-metadata] (Edytowanie metadanych) (mały okrąg w górnej części modułu), a następnie zwolnij przycisk myszy. Zestaw danych i moduł pozostaną połączone, nawet jeśli będziesz przenosić je w obrębie kanwy.
 
    Eksperyment powinien wyglądać teraz mniej więcej tak:  

    ![Dodawanie modułu Edytowanie metadanych](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    Czerwony wykrzyknik wskazuje, że dla tego modułu nie ustawiono jeszcze właściwości. Zrobisz to w następnym kroku.

    > [!TIP]
    > Aby dodać komentarz do modułu, kliknij dwukrotnie moduł i wpisz tekst. Pozwoli to od razu sprawdzić rolę modułu w eksperymencie. W tym przypadku kliknij dwukrotnie moduł [Edit Metadata][edit-metadata] (Edytowanie metadanych) i wpisz komentarz „Dodawanie nagłówków kolumn”. Kliknij dowolne miejsce na kanwie, aby zamknąć pole tekstowe. Aby wyświetlić komentarz, kliknij strzałkę w dół na module.
    > 
    > ![Moduł Edytowanie metadanych z dodanym komentarzem](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Wybierz moduł [Edit Metadata][edit-metadata] (Edytowanie metadanych), a następnie w okienku **Properties** (Właściwości) po prawej stronie kanwy kliknij pozycję **Launch column selector** (Uruchom selektor kolumn).

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
  > Jeśli chcesz zweryfikować nagłówki kolumn, uruchom eksperyment (kliknij przycisk **RUN** (URUCHOM) poniżej kanwy eksperymentu). Po zakończeniu eksperymentu (na module [Edit Metadata][edit-metadata] (Edytowanie metadanych) zostanie wyświetlony zielony znacznik wyboru), kliknij port wyjściowy modułu [Edit Metadata][edit-metadata] (Edytowanie metadanych) i wybierz pozycję **Visualize** (Wizualizuj). W ten sam sposób możesz wyświetlić dane wyjściowe każdego modułu, aby sprawdzić postęp danych w eksperymencie.
  > 
  > 

### <a name="create-training-and-test-datasets"></a>Tworzenie szkoleniowych i testowych zestawów danych

Potrzebujesz danych, aby wytrenować model i aby go przetestować.
Z tego względu w następnym kroku eksperymentu podzielisz zestaw danych na dwa oddzielne zestawy danych: jeden na potrzeby trenowania naszego modelu, a drugi na potrzeby testowania.

Aby to zrobić, należy użyć modułu [Split Data][split] (Podział danych).  

1. Znajdź moduł [Split Data][split] (Podział danych), przeciągnij go na kanwę, a następnie połącz go z modułem [Edit Metadata][edit-metadata] (Edytowanie metadanych).

1. Domyślnie współczynnik podziału wynosi 0,5 i jest ustawiony parametr **Randomized split** (Podział losowy). Oznacza to, że losowa połowa danych przechodzi przez jeden port wyjściowy modułu [Split Data][split] (Podział danych), a pozostała połowa przez drugi. Możesz dostosować te parametry, jak również parametr **Random seed** (Podział losowy), aby zmienić podział między danymi szkoleniowymi i testowymi. W tym przykładzie pozostaw je tak, jak są.
   
   > [!TIP]
   > Właściwość **Fraction of rows in the first output dataset** (Odsetek wierszy w pierwszym zestawie danych wyjściowych) określa, ile danych przechodzi przez *lewy* port wyjściowy. Jeśli na przykład ustawisz współczynnik o wartości 0,7, to 70% danych będzie przechodzić przez lewy port wyjściowy, a 30% przez prawy port.  
   > 
   > 

1. Kliknij dwukrotnie moduł [Split Data][split] (Podział danych) i wprowadź komentarz „Podział na dane szkoleniowe/testowe 50%”. 

Danych wyjściowych modułu [Split Data][split] (Podział danych) możesz używać, jak chcesz, ustalmy jednak, że lewych danych wyjściowych użyjemy do trenowania, a prawych danych wyjściowych do testowania.  

Jak wspomnieliśmy w [poprzednim kroku](tutorial-part1-credit-risk.md#upload), koszt błędnego sklasyfikowania wysokiego ryzyka kredytowego jako niskiego jest pięć razy wyższy, niż koszt błędnego sklasyfikowania niskiego ryzyka kredytowego jako wysokiego. Aby to uwzględnić, musisz wygenerować nowy zestaw danych, który odzwierciedla tę cechę kosztu. W nowym zestawie danych każdy przykład wysokiego ryzyka jest replikowany pięć razy, podczas gdy przykłady niskiego ryzyka nie są replikowane.   

Tę replikację możesz przeprowadzić przy użyciu kodu języka R:  

1. Znajdź moduł [Execute R Script][execute-r-script] (Wykonywanie skryptu R) i przeciągnij go na kanwę eksperymentu. 

1. Połącz lewy port wyjściowy modułu [Split Data][split] (Podział danych) z pierwszy portem wejściowym („Dataset1”) modułu [Execute R Script][execute-r-script] (Wykonywanie skryptu R).

1. Kliknij dwukrotnie moduł [Execute R Script][execute-r-script] (Wykonywanie skryptu R) i wprowadź komentarz „Ustawianie korekty kosztu”.

1. W okienku **Properties** (Właściwości) usuń domyślny tekst dla parametru **R Script** (Skrypt R) i wprowadź ten skrypt:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Skrypt języka R w module Wykonywanie skryptu R](./media/tutorial-part1-credit-risk/execute-r-script.png)

Musisz wykonać tę samą operację replikacji dla wszystkich danych wyjściowych modułu [Split Data][split] (Podział danych), aby dane szkoleniowe i testowe miały taką samą korektę kosztów. Najłatwiejszym sposobem na zrobienie tego jest zduplikowanie właśnie utworzonego modułu [Execute R Script][execute-r-script] (Wykonywanie skryptu R) i połączenie go z drugim portem wyjściowym modułu [Split Data][split] (Podział danych).

1. Kliknij prawym przyciskiem myszy moduł [Execute R Script][execute-r-script] (Wykonywanie skryptu R), a następnie wybierz pozycję **Copy** (Kopiuj).

1. Kliknij prawym przyciskiem myszy kanwę eksperymentu, a następnie wybierz pozycję **Paste** (Wklej).

1. Przeciągnij nowy moduł w odpowiednie miejsce, a następnie połącz prawy port wyjściowy modułu [Split Data][split] (Podział danych) z pierwszym portem wejściowym tego nowego modułu [Execute R Script][execute-r-script] (Wykonywanie skryptu R). 

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
> * Tworzenie obszaru roboczego usługi Machine Learning Studio
> * Przekazywanie istniejących danych do obszaru roboczego
> * Tworzenie eksperymentu

Teraz możesz przystąpić do trenowania i ewaluowania modeli dla tych danych.

> [!div class="nextstepaction"]
> [Samouczek 2 — Trenowanie i ewaluowanie modeli](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/