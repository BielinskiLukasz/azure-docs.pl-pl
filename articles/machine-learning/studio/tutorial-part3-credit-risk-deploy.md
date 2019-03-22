---
title: Samouczek 3. Wdrażanie modelu ryzyka kredytowego
titleSuffix: Azure Machine Learning Studio
description: Szczegółowy samouczek pokazujący sposób tworzenia rozwiązania analizy predykcyjnej w celu oceny ryzyka kredytowego w usłudze Azure Machine Learning Studio. Niniejszy samouczek jest trzecią częścią trzyczęściowej serii. Przedstawia on sposób wdrażania modelu w postaci usługi internetowej.
keywords: ryzyko kredytowe, rozwiązanie analizy predykcyjnej, ocena ryzyka, wdrażanie, usługa internetowa
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 7132c2073223008cd82d0ffd54aaf266d0a630ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839021"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio"></a>Samouczek 3. Wdrażanie modelu ryzyka kredytowego — usługa Azure Machine Learning Studio

W tym samouczku szczegółowo przedstawiono proces opracowywania rozwiązania analizy predykcyjnej. W jego ramach opracujesz prosty model w usłudze Machine Learning Studio.  Następnie wdrożysz model jako usługę internetową w ramach usługi Azure Machine Learning.  Wdrożony model może tworzyć przewidywania przy użyciu nowych danych. Niniejszy samouczek jest **trzecią częścią trzyczęściowej serii**.

Załóżmy, że chcesz przewidzieć ryzyko kredytowe osoby na podstawie informacji przekazanych we wniosku kredytowym.  

Ocena ryzyka kredytowego to złożony problem, ale w tym samouczku zostanie on nieco uproszczony. Użyjesz go jako przykładu na potrzeby przedstawienia sposobu tworzenia rozwiązania analizy predykcyjnej przy użyciu usługi Microsoft Azure Machine Learning Studio. W tym rozwiązaniu skorzystasz z usługi Azure Machine Learning Studio i usługi internetowej Machine Learning. 

W tym trzyczęściowym samouczku zaczniesz od publicznie dostępnych danych ryzyka kredytowego.  Następnie wdrożysz i wytrenujesz model predykcyjny.  Na koniec wdrożysz model jako usługę internetową.

W [pierwszej części samouczka](tutorial-part1-credit-risk.md) został utworzony obszar roboczy usługi Machine Learning Studio, przekazano dane i utworzono eksperyment.

W [drugiej części samouczka](tutorial-part2-credit-risk-train.md) modele zostały przeszkolone i ocenione.

W tej części samouczka zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Przygotowanie do wdrożenia
> * Wdrażanie usługi sieci Web
> * Testowanie usługi internetowej
> * Zarządzanie usługą internetową
> * Uzyskiwanie dostępu do usługi sieci Web

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ukończona [druga część samouczka](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia
Aby zapewnić innym osobom możliwość użycia modelu predykcyjnego, który został opracowany w ramach tego samouczka, możesz go wdrożyć jako usługę internetową na platformie Azure.

Do tej pory były przeprowadzane eksperymenty z trenowaniem naszego modelu. Ale wdrożona usługa nie będzie już trenowana — będzie generowała nowe przewidywania, generując wyniki dla danych wejściowych użytkownika na podstawie naszego modelu. Zamierzamy więc przeprowadzić pewne przygotowania, aby przekonwertować ten eksperyment z eksperymentu ***szkoleniowego*** w eksperyment ***predykcyjny***. 

Proces przygotowania do wdrożenia składa się z trzech kroków:  

1. Usunięcie jednego z modeli
1. Przekonwertowanie utworzonego *eksperymentu trenowania* w *eksperyment predykcyjny*
1. Wdrożenie eksperymentu predykcyjnego jako usługi internetowej

### <a name="remove-one-of-the-models"></a>Usunięcie jednego z modeli

Najpierw musisz nieco ograniczyć ten eksperyment. Obecnie masz dwa różne modele w eksperymencie, ale chcesz użyć tylko jednego modelu podczas wdrażania go jako usługi internetowej.  

Załóżmy, że zdecydujesz, że model wzmocnionego drzewa spisywał się lepiej niż model SVM. Dlatego najpierw musisz usunąć moduł [Two-Class Support Vector Machine (Dwuklasowa maszyna wektorów nośnych)][two-class-support-vector-machine] i moduły, które zostały użyte do jego szkolenia. Warto najpierw utworzyć kopię tego eksperymentu, klikając przycisk **Zapisz jako** w dolnej części kanwy eksperymentu.

Musisz usunąć następujące moduły:  

* [Two-Class Support Vector Machine (Dwuklasowa maszyna wektorów nośnych)][two-class-support-vector-machine]
* Moduły [Train Model (Trenuj model)][train-model] i [Score Model (Generuj wyniki dla modelu)][score-model], które zostały do niego podłączone
* [Normalize Data (Normalizuj dane)][normalize-data] (obydwa)
* [Evaluate Model (Ewaluuj model)][evaluate-model] (ponieważ zakończyliśmy ewaluację modeli)

Wybierz każdy moduł i naciśnij klawisz Delete lub kliknij prawym przyciskiem myszy moduł i wybierz pozycję **Delete (Usuń)**. 

![Wyróżnia które moduły można usunąć, aby usunąć model pomocy technicznej Vector Machine](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Nasz model powinien teraz wyglądać mniej więcej tak:

![Wynikowy eksperymentu po usunięciu model pomocy technicznej wektor maszyny](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Teraz jesteśmy gotowi do wdrożenia tego modelu przy użyciu [dwuklasowego wzmocnionego drzewa decyzyjnego][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konwertowanie eksperymentu trenowania w eksperyment predykcyjny

Aby przygotować ten model do wdrożenia, musisz przekonwertować ten eksperyment trenowania w eksperyment predykcyjny. Ten proces obejmuje trzy kroki:

1. Zapisanie wytrenowanego modelu, a następnie zastąpienie naszych modułów trenowania
1. Dostosowanie eksperymentu do usunięcia modułów, które były potrzebne tylko do szkolenia
1. Zdefiniowanie, gdzie usługa internetowa będzie akceptować dane wejściowe i gdzie będzie generować dane wyjściowe

Możesz to zrobić ręcznie, ale na szczęście wszystkie trzy kroki można wykonać, klikając pozycję **Set Up Web Service (Skonfiguruj usługę internetową)** w dolnej części kanwy eksperymentu (i wybierając opcję **Predictive Web Service [Predykcyjna usługa internetowa]**).

> [!TIP]
> Jeśli chcesz uzyskać więcej informacji o tym, co się stanie, gdy konwertujesz eksperyment trenowania w eksperyment predykcyjny, zobacz [Jak przygotować model do wdrożenia w usłudze Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Po kliknięciu pozycji **Set Up Web Service (Skonfiguruj usługę internetową)** ma miejsce kilka zdarzeń:

* Wytrenowany model jest konwertowany na pojedynczy moduł **Trained model (Wytrenowany model)** i zapisywany w palecie modułów z lewej strony kanwy eksperymentu (można go odnaleźć w obszarze **Trained Models [Wytrenowane modele]**)
* Moduły, które zostały użyte do szkolenia, zostaną usunięte, a w szczególności:
  * [Two-Class Boosted Decision Tree (Dwuklasowe wzmocnione drzewo decyzyjne)][two-class-boosted-decision-tree]
  * [Train Model (Trenuj model)][train-model]
  * [Spil Data (Podziel dane)][split]
  * Drugi moduł [Execute R Script (Wykonaj skrypt języka R)][execute-r-script], który był używany dla danych testowych
* Zapisany wytrenowany model jest ponownie dodawany do eksperymentu
* Zostaną dodane moduły **Web service input (Dane wejściowe usługi internetowej)** i **Web service output (Dane wyjściowe usługi internetowej)** (określają one to, czy dane użytkownika wejdą do modelu danych użytkownika i jakie dane są zwracane podczas uzyskiwania dostępu do usługi internetowej)

> [!NOTE]
> Możesz zobaczyć, że eksperyment został zapisany w dwóch częściach na kartach, które zostały dodane w górnej części kanwy eksperymentu. Oryginalny eksperyment trenowania znajduje się na karcie **Training experiment (Eksperyment trenowania)**, a nowo utworzony eksperyment predykcyjny znajduje się w obszarze **Predictive experiment (Eksperyment predykcyjny)**. Eksperyment predykcyjny jest tym, który wdrożysz jako usługę internetową.

Musisz wykonać jeden dodatkowy krok dla tego konkretnego eksperymentu.
Dodano dwa moduły [Execute R Script (Wykonaj skrypt języka R)][execute-r-script], aby zapewnić funkcję określania wagi danych. To było po prostu obejście potrzebne do trenowania i testowania, więc możesz usunąć te moduły w ostatecznym modelu.
Usługa Machine Learning Studio usunęła jeden moduł [Execute R Script (Wykonaj skrypt języka R)][execute-r-script] po usunięciu modułu [Split (Podziel)][split]. Teraz możesz usunąć drugi i połączyć element [Metadata Editor (Edytor metadanych)][metadata-editor] bezpośrednio z modułem [Score Model (Wygeneruj wyniki dla modelu)][score-model].    

Nasz eksperyment powinien teraz wyglądać następująco:  

![Generowanie wyników dla wytrenowanego modelu](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Możesz się zastanawiać, dlaczego w eksperymencie predykcyjnym został pozostawiony zestaw danych dla danych niemieckiej karty kredytowej UCI. Usługa będzie generować wyniki dla danych użytkownika, a nie oryginalnego zestawu danych, więc po co zostawiać oryginalny zestaw danych w modelu?
> 
> Prawdą jest, że usługa nie potrzebuje oryginalnych danych karty kredytowej. Ale potrzebuje ona schematu dla danych, który zawiera takie informacje, jak liczba istniejących kolumn i które kolumny są liczbowe. Te informacje schematu są niezbędne do interpretacji danych użytkownika. Możesz pozostawić te składniki połączone tak, aby moduł generowania wyników miał schemat zestawu danych, gdy usługa jest uruchomiona. Dane nie są używane, a tylko schemat.  
> 
>Warto zwrócić uwagę na jedną ważną sprawę, a mianowicie, że jeśli oryginalny zestaw danych zawierał etykietę, wówczas oczekiwany schemat z internetowych danych wejściowych będzie również oczekiwał kolumny z etykietą. Obejściem tego problemu jest usunięcie etykiety i wszelkich innych danych, które znajdowały się w treningowym zestawie danych, ale nie będą się znajdowały w internetowych danych wejściowych przed nawiązaniem połączenia internetowych danych wejściowych i zestawu danych szkoleniowych ze wspólnym modułem. 
> 

Uruchom eksperyment po raz ostatni (kliknij pozycję **Run [Uruchom]**). Jeśli chcesz sprawdzić, czy model nadal działa, kliknij wyjście modułu [Score Model (Wygeneruj wyniki dla modelu)][score-model], a następnie wybierz pozycję **View Results (Wyświetl wyniki)**. Zobaczysz, że są wyświetlane oryginalne dane wraz z wartością ryzyka kredytowego („Scored Labels”, Wyliczone wyniki dla etykiet) i wartością generowania wyniku dla prawdopodobieństwa („Scored Probabilities”, Wyliczone wyniki dla prawdopodobieństw). 

## <a name="deploy-the-web-service"></a>Wdrażanie usługi sieci Web
Możesz wdrożyć eksperyment jako klasyczną usługę internetową albo jako nową usługę internetową, która jest oparta na usłudze Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Wdrażanie w postaci klasycznej usługi internetowej
Aby wdrożyć klasyczną usługę internetową pochodzącą z naszego eksperymentu, kliknij pozycję **Deploy Web Service (Wdróż usługę internetową)** poniżej kanwy, a następnie wybierz pozycję **Deploy Web Service [Classic] (Wdróż usługę internetową [klasyczną])**. Usługa Machine Learning Studio wdraża eksperyment jako usługę internetową i przechodzi do pulpitu nawigacyjnego dla tej usługi internetowej. Na tej stronie możesz wrócić do eksperymentu (**View snapshot (Wyświetl migawkę)** lub **View latest (Wyświetl najnowsze)**) i uruchomić prosty test usługi internetowej (zobacz **Testowanie usługi internetowej** poniżej). Istnieją również tutaj informacje dotyczące tworzenia aplikacji mogących uzyskiwać dostęp do usługi internetowej (więcej informacji na ten temat znajdziesz w następnym kroku tego samouczka).

![Pulpit nawigacyjny usługi internetowej](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Usługę możesz skonfigurować, klikając kartę **CONFIGURATION (KONFIGURACJA)**. Tutaj możesz zmodyfikować nazwę usługi (została jej domyślnie nadana nazwa eksperymentu) i podać jej opis. Możesz również określić bardziej przyjazne etykiety dla danych wejściowych i wyjściowych.  

![Konfigurowanie usługi internetowej](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Wdrażanie w postaci nowej usługi internetowej

> [!NOTE] 
> Aby wdrożyć nową usługę internetową, musisz mieć wystarczające uprawnienia w ramach subskrypcji, w której wdrażasz tę usługę. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą internetową przy użyciu portalu usług internetowych usługi Azure Machine Learning](manage-new-webservice.md). 

Aby wdrożyć nową usługę internetową pochodzącą z naszego eksperymentu:

1. Kliknij pozycję **Deploy Web Service (Wdróż usługę internetową)** poniżej kanwy, a następnie wybierz pozycję **Deploy Web Service [New] (Wdróż usługę internetową [nową])**. Usługa Machine Learning Studio przeniesie Cię na stronę **Deploy Experiment (Wdrażanie eksperymentu)** usług internetowych usługi Azure Machine Learning.

1. Wprowadź nazwę usługi internetowej. 

1. Aby uzyskać **plan cenowy**, możesz wybrać istniejący plan cenowy lub wybrać pozycję „Create new” (Utwórz nową) oraz nadać nowemu planowi nazwę i wybrać opcję planu miesięcznego. Plan domyślnie jest dzielony na warstwy planów dla domyślnego regionu, a usługi internetowe są wdrażane dla tego regionu.

1. Kliknij przycisk **Deploy (Wdróż)**.

Po kilku minutach zostanie otwarta strona **Quickstart (Szybki start)** Twojej usługi internetowej.

Usługę możesz skonfigurować, klikając kartę **Configure (Konfiguracja)**. Tutaj możesz zmodyfikować tytuł usługi i określić jej opis. 

Aby przetestować usługę internetową, kliknij kartę **Test** (zobacz **Testowanie usługi internetowej** poniżej). Aby uzyskać informacje na temat tworzenia aplikacji, które mogą uzyskać dostęp do usługi internetowej, kliknij kartę **Consume (Zużywanie)** (następny krok w tym samouczku będzie zawierał więcej szczegółów).

> [!TIP]
> Po jej wdrożeniu możesz zaktualizować usługę internetową. Na przykład jeśli chcesz zmienić model, wówczas możesz edytować eksperyment szkoleniowy, dostosować parametry modelu, a następnie kliknij pozycję **Deploy Web Service (Wdróż usługę internetową)**, wybierając pozycję **Deploy Web Service [Classic ]\(Wdróż usługę internetową [klasyczną])** lub **Deploy Web Service [New] \(Wdróż usługę internetową [nową])**. Gdy ponownie wdrożysz eksperyment, zastąpi on usługę internetową, używając teraz zaktualizowanego modelu.  
> 
> 

## <a name="test-the-web-service"></a>Testowanie usługi internetowej

Podczas uzyskiwania dostępu do usługi internetowej dane użytkownika są wprowadzane za pośrednictwem modułu **Web service input (Dane wejściowe usługi internetowej)**, w którym są przekazywane do modułu [Score Model (Wygeneruj wyniki dla modelu)][score-model] i oceniane. W oparciu o skonfigurowanie eksperymentu predykcyjnego model oczekuje danych w tym samym formacie, co oryginalny zestaw danych ryzyka kredytowego.
Wyniki są zwracane użytkownikowi z usługi internetowej za pośrednictwem modułu **Web service output (Dane wyjściowe usługi internetowej)**.

> [!TIP]
> W oparciu o skonfigurowanie eksperymentu predykcyjnego są zwracane wyniki z modułu [Score Model (Wygeneruj wyniki dla modelu)][score-model]. Obejmuje to wszystkie dane wejściowe oraz wartość ryzyka kredytowego i generowania wyniku dla prawdopodobieństwa. Ale jeśli chcesz, możesz zwrócić coś innego — na przykład możesz zwrócić tylko wartość ryzyka kredytowego. Aby to zrobić, wstaw moduł [Project Columns (Kolumny projektu)][project-columns] między moduły [Score Model (Wygeneruj wyniki dla modelu)][score-model] i **Web service output (Dane wyjściowe usługi internetowej)**, aby wyeliminować kolumny, które mają nie być zwracane przez usługę internetową. 
> 
> 

Możesz przetestować klasyczną usługę internetową w usłudze **Machine Learning Studio** albo w portalu **usług internetowych usługi Azure Machine Learning**.
Nową usługę internetową możesz przetestować tylko w portalu **usług internetowych usługi Machine Learning**.

> [!TIP]
> Podczas testowania w portalu usług internetowych Azure Machine Learning portal może utworzyć przykładowe dane, których możesz użyć do testowania usługi żądanie-odpowiedź. Na stronie **Configure (Konfiguracja)** wybierz opcję „Yes” (Tak) dla pozycji **Sample Data Enabled? (Włączono przykładowe dane?)**. Gdy otworzysz kartę żądanie-odpowiedź na stronie **Test**, portal wypełni przykładowe dane pobierane z oryginalnego zestawu danych ryzyka kredytowego.

### <a name="test-a-classic-web-service"></a>Testowanie klasycznej usługi internetowej

Możesz przetestować klasyczną usługę internetową w usłudze Machine Learning Studio albo w portalu usług internetowych usługi Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Testowanie usługi Machine Learning Studio

1. Na stronie **DASHBOARD (PULPIT NAWIGACYJNY)** usługi internetowej kliknij przycisk **Test** w obszarze **Default Endpoint (Domyślny punkt końcowy)**. Pojawi się okno dialogowe i poprosi o dane wejściowe dla usługi. Są to te same kolumny, które znajdowały się w oryginalnym zestawie danych ryzyka kredytowego.  

1. Wprowadź zestaw danych, a następnie kliknij przycisk **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testowanie w portalu usług internetowych usługi Machine Learning

1. Na stronie **DASHBOARD (PULPIT NAWIGACYJNY)** usługi internetowej kliknij link **Test preview (Podgląd testu)** w obszarze **Default Endpoint (Domyślny punkt końcowy)**. Zostanie otwarta strona testowa w portalu usług internetowych usługi Azure Machine Learning dla punktu końcowego usługi internetowej i poprosi o dane wejściowe dla usługi. Są to te same kolumny, które znajdowały się w oryginalnym zestawie danych ryzyka kredytowego.

2. Kliknij pozycję **Test Request-Response (Testowanie usługi żądanie-odpowiedź)**. 

### <a name="test-a-new-web-service"></a>Testowanie nowej usługi internetowej

Nową usługę internetową możesz przetestować tylko w portalu usług internetowych usługi Machine Learning.

1. W portalu [usług internetowych usługi Azure Machine Learning](https://services.azureml.net/quickstart) kliknij przycisk **Test** w górnej części strony. Zostanie otwarta strona **Test**, gdzie możesz wprowadzić dane dla usługi. Wyświetlane pola wejściowe odpowiadają kolumnom, które były wyświetlane w oryginalnym zestawie danych ryzyka kredytowego. 

1. Wprowadź zestaw danych, a następnie kliknij pozycję **Test Request-Response (Testowanie usługi żądanie-odpowiedź)**.

Wyniki testu są wyświetlane w prawej części strony w kolumnie wyników. 


## <a name="manage-the-web-service"></a>Zarządzanie usługą internetową

Po wdrożeniu swojej usługi internetowej, czy to klasycznej, czy też nowej, możesz zarządzać nią z portalu [usług internetowych usługi Microsoft Azure Machine Learning](https://services.azureml.net/quickstart).

Aby monitorować wydajność Twojej usługi internetowej:

1. Zaloguj się do portalu [usług internetowych usługi Microsoft Azure Machine Learning](https://services.azureml.net/quickstart)
1. Kliknij pozycję **Web services (Usługi internetowe)**
1. Kliknij swoją usługę internetową
1. Kliknij pozycję **Dashboard (Pulpit nawigacyjny)**

## <a name="access-the-web-service"></a>Uzyskiwanie dostępu do usługi sieci Web

W poprzednim kroku tego samouczka została wdrożona usługa internetowa, która używa Twojego modelu przewidywania ryzyka kredytowego. Teraz użytkownicy będą mogli wysyłać do niej dane i otrzymywać wyniki. 

Usługa internetowa to usługa internetowa platformy Azure, która może odbierać i zwracać dane przy użyciu interfejsów API REST na jeden z dwóch sposobów:  

* **Żądanie/odpowiedź** — użytkownik wysyła co najmniej jeden wiersz danych kredytowych do usługi przy użyciu protokołu HTTP, a usługa odpowiada za pomocą co najmniej jednego zestawu wyników.
* **Wykonywanie wsadowe** — użytkownik przechowuje jeden lub więcej wierszy danych kredytowych w obiekcie blob platformy Azure, a następnie wysyła lokalizację obiektu blob do usługi. Usługa generuje wyniki dla wszystkich wierszy danych w wejściowym obiekcie blob, zapisuje wyniki w innym obiekcie blob, a następnie zwraca adres URL tego kontenera.  

Jest to najszybszy i najłatwiejszy sposób uzyskania dostępu do klasycznej usługi internetowej za pośrednictwem [aplikacji internetowej usługi Azure ML Request-Response](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) lub [szablonu aplikacji internetowej usługi Azure ML Batch Execution](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Te szablony aplikacji internetowej mogą tworzyć niestandardową aplikację internetową, która zna Twoje dane wejściowe usługi internetowej i to, co zwróci. Wystarczy, że zapewnisz dostęp do swojej usługi internetowej i danych, a szablon zajmie się resztą.

Aby uzyskać więcej informacji na temat korzystania z szablonów aplikacji internetowych, zobacz [Korzystanie z usługi internetowej Azure Machine Learning za pomocą szablonu aplikacji internetowej](/azure/machine-learning/studio/consume-web-services).



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku zostały wykonane następujące kroki:

> [!div class="checklist"]
> * Przygotowanie do wdrożenia
> * Wdrażanie usługi sieci Web
> * Testowanie usługi internetowej
> * Zarządzanie usługą internetową
> * Uzyskiwanie dostępu do usługi sieci Web

Możesz również opracować niestandardową aplikację do uzyskiwania dostępu do usługi internetowej przy użyciu kodu startowego dostarczanego w językach programowania R, C# i Python.

> [!div class="nextstepaction"]
> [Korzystanie z usługi internetowej Azure Machine Learning](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/