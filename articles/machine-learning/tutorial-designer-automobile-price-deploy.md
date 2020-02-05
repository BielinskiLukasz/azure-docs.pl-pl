---
title: 'Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą narzędzia Projektant'
titleSuffix: Azure Machine Learning
description: W tym samouczku przedstawiono sposób tworzenia rozwiązania do analizy predykcyjnej w programie Azure Machine Learning Designer (wersja zapoznawcza). Uczenie, ocenę i wdrożenie modelu uczenia maszynowego przy użyciu modułów przeciągania i upuszczania.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6fc6479ccda77bff84319d3244b2bd4a0599dc47
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933994"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą narzędzia Projektant (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Można wdrożyć model predykcyjny opracowany w pierwszej części [samouczka](tutorial-designer-automobile-price-train-score.md) , aby dać innym osobom szansę korzystania z niego. W części pierwszej został przeszkolony model. Teraz można generować nowe prognozy na podstawie danych wejściowych użytkownika. W tej części samouczka zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz potok wnioskowania w czasie rzeczywistym.
> * Utwórz klaster inferencing.
> * Wdróż punkt końcowy w czasie rzeczywistym.
> * Przetestuj punkt końcowy w czasie rzeczywistym.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj [jedną z części samouczka](tutorial-designer-automobile-price-train-score.md) , aby dowiedzieć się, jak szkolić i oceny model uczenia maszynowego w projektancie.

## <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku w czasie rzeczywistym

Aby wdrożyć potok, należy najpierw skonwertować potok szkoleniowy do potoku w czasie rzeczywistym. Ten proces powoduje usunięcie modułów szkoleniowych i dodanie danych wejściowych i wyjściowych usługi sieci Web do obsługi żądań.

### <a name="create-a-real-time-inference-pipeline"></a>Tworzenie potoku w czasie rzeczywistym

1. Nad kanwą potoku wybierz pozycję **Utwórz potok wnioskowania** > **potoku w czasie rzeczywistym**.

    ![Zrzut ekranu przedstawiający miejsce znalezienia przycisku Utwórz potok](./media/tutorial-designer-automobile-price-deploy/create-inference-pipeline.png)

    Potok powinien teraz wyglądać następująco: 

   ![Zrzut ekranu przedstawiający oczekiwaną konfigurację potoku po przygotowaniu go do wdrożenia](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Po wybraniu opcji **Utwórz potok wnioskowania**kilka rzeczy zostanie wykonanych:
    
    * Szkolony model jest przechowywany jako moduł **DataSet** w palecie modułów. Można go znaleźć w obszarze **Moje zestawy danych**.
    * Moduły szkoleniowe, takie jak **model uczenia** i **dane podzielone** , są usuwane.
    * Zapisany model przeszkolony zostanie dodany z powrotem do potoku.
    * Dodawane są moduły danych **wejściowych** i **usług** sieci Web. Te moduły pokazują, gdzie dane użytkownika są wprowadzane do potoku i gdzie są zwracane dane.

    > [!NOTE]
    > Domyślnie dane **wejściowe usługi sieci Web** będą oczekiwać tego samego schematu danych co dane szkoleniowe, które są używane do tworzenia potoku predykcyjnego. W tym scenariuszu cena jest uwzględniona w schemacie. Cena nie jest jednak używana jako współczynnik podczas przewidywania.
    >

1. Wybierz pozycję **Uruchom**, a następnie użyj tego samego elementu docelowego obliczeń i eksperymentu, który został użyty w części pierwszej.

1. Wybierz pozycję **Wdróż**.

## <a name="create-an-inferencing-cluster"></a>Tworzenie klastra inferencing

W wyświetlonym oknie dialogowym możesz wybrać dowolny z istniejących klastrów usługi Azure Kubernetes Service (AKS), aby wdrożyć model. Jeśli nie masz klastra AKS, wykonaj następujące kroki, aby go utworzyć.

1. Wybierz pozycję **obliczenia** w wyświetlonym oknie dialogowym, aby przejść do strony **obliczenia** .

1. Na Wstążce Nawigacja wybierz pozycję **klastry wnioskowania** >  **+ Nowy**.

    ![Zrzut ekranu przedstawiający sposób uzyskiwania do okienka nowy klaster wnioskowania](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. W okienku klaster wnioskowania Skonfiguruj nową usługę Kubernetes.

1. Wprowadź *AKS-COMPUTE* dla **nazwy obliczeniowej**.
    
1. Wybierz region znajdujący się w pobliżu, który jest dostępny dla **regionu**.

1. Wybierz pozycję **Utwórz**.

    > [!NOTE]
    > Utworzenie nowej usługi AKS trwa około 15 minut. Stan aprowizacji można sprawdzić na stronie **klastry wnioskowania** .
    >

## <a name="deploy-the-real-time-endpoint"></a>Wdrażanie punktu końcowego w czasie rzeczywistym

Po zakończeniu aprowizacji usługi AKS Wróć do potoku inferencing w czasie rzeczywistym, aby zakończyć wdrażanie.

1. Wybierz pozycję **Wdróż** powyżej kanwy.

1. Wybierz pozycję **wdróż nowy punkt końcowy**w czasie rzeczywistym. 

1. Wybierz utworzony klaster AKS.

1. Wybierz pozycję **Wdróż**.

    ![Zrzut ekranu przedstawiający sposób konfigurowania nowego punktu końcowego w czasie rzeczywistym](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Powiadomienie o powodzeniu powyżej kanwy pojawia się po zakończeniu wdrażania. Może to potrwać kilka minut.

## <a name="test-the-real-time-endpoint"></a>Testowanie punktu końcowego w czasie rzeczywistym

Po zakończeniu wdrażania można testować punkt końcowy w czasie rzeczywistym, przechodząc do strony **punkty końcowe** .

1. Na stronie **punkty końcowe** Wybierz wdrożony punkt końcowy.

    ![Zrzut ekranu przedstawiający kartę punkty końcowe czasu rzeczywistego z wyróżnionym ostatnio utworzonym punktem końcowym](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Wybierz pozycję **Testuj**.

1. Możesz ręcznie wprowadzić dane testowe lub użyć autowypełnianych danych przykładowych, a następnie wybrać opcję **Testuj**.

    Portal przesyła żądanie testowe do punktu końcowego i wyświetla wyniki. Mimo że dla danych wejściowych jest generowana wartość ceny, nie jest ona używana do generowania wartości przewidywania.

    ![Zrzut ekranu przedstawiający sposób testowania punktu końcowego w czasie rzeczywistym z etykietą z oceną dla wyróżnionej ceny](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono najważniejsze kroki w temacie Tworzenie, wdrażanie i Używanie modelu uczenia maszynowego w projektancie. Aby dowiedzieć się więcej o sposobach rozwiązywania innych rodzajów problemów za pomocą projektanta, zobacz nasze inne przykładowe potoki.

> [!div class="nextstepaction"]
> [Przykład klasyfikacji ryzyka kredytowego](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
