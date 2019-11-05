---
title: Dodatek programu Excel dla usług sieci Web
titleSuffix: Azure Machine Learning Studio (classic)
description: Jak używać Azure Machine Learning usług sieci Web bezpośrednio w programie Excel bez konieczności pisania kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 18e7e2c2c57bd44cb974e5de668045d6322bc4ce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492976"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Dodatek programu Excel dla usług sieci Web Azure Machine Learning Studio (klasyczny)
Program Excel ułatwia wywoływanie usług sieci Web bezpośrednio bez konieczności pisania kodu.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Procedura używania istniejącej usługi sieci Web w skoroszycie

1. Otwórz [przykładowy plik programu Excel](https://aka.ms/amlexcel-sample-2), który zawiera dodatek programu Excel i dane dotyczące pasażerów w Titanic. 
 
    > [!NOTE]
    > Zostanie wyświetlona lista usług sieci Web związanych z plikiem i u dołu pola wyboru "autoprzewidywania". Jeśli włączysz funkcję autoprzewidywania, prognozy **wszystkich** usług zostaną zaktualizowane po każdym wprowadzeniu zmiany w danych wejściowych. W przypadku usunięcia zaznaczenia należy kliknąć pozycję "przewidywanie wszystkich" do odświeżenia. Aby włączyć funkcję autoprzewidywania na poziomie usługi, przejdź do kroku 6.

2. Wybierz usługę sieci Web, klikając ją — w tym przykładzie zostanie wyTitanic predykcyjny (przykład dodatku w programie Excel) [score] ".
   
    ![Wybierz usługę sieci Web](./media/excel-add-in-for-web-services/image1.png)
3. Spowoduje to przejście do sekcji **przewidywania** .  Ten skoroszyt zawiera już przykładowe dane, ale dla pustego skoroszytu możesz wybrać komórkę w programie Excel i kliknąć pozycję **Użyj przykładowych danych**.
4. Wybierz dane z nagłówkami i kliknij ikonę zakres danych wejściowych.  Upewnij się, że pole "moje dane ma nagłówki" jest zaznaczone.
5. W obszarze **dane wyjściowe**wprowadź numer komórki, w której mają być wyświetlane dane wyjściowe, na przykład "H1" w tym miejscu.
6. Kliknij pozycję **przewidywanie**. W przypadku wybrania pola wyboru "automatycznie przewidywalna" zmiana dotycząca wybranych obszarów (określonych jako dane wejściowe) spowoduje wyzwolenie żądania i zaktualizowanie komórek wyjściowych bez konieczności naciskania przycisku przewidywania.
   
    ![Sekcja przewidywania](./media/excel-add-in-for-web-services/image1.png)

Wdróż usługę sieci Web lub Użyj istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci Web, zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).

Pobierz klucz interfejsu API dla usługi sieci Web. Miejsce, w którym wykonujesz tę akcję, zależy od tego, czy została opublikowana klasyczna usługa sieci Web Machine Learning nowej usługi sieci Web Machine Learning.

**Korzystanie z klasycznej usługi sieci Web** 

1. W Machine Learning Studio (klasyczny) kliknij sekcję **usługi sieci Web** w okienku po lewej stronie, a następnie wybierz usługę sieci Web.
   
    ![Studio — Wybieranie usługi sieci Web](./media/excel-add-in-for-web-services/image4.png)
2. Skopiuj klucz interfejsu API dla usługi sieci Web.
   
    ![Klucz interfejsu API Studio](./media/excel-add-in-for-web-services/image5.png)
3. Na karcie **pulpit nawigacyjny** usługi sieci Web kliknij link **żądanie/odpowiedź** .
4. Poszukaj sekcji **Żądaj identyfikatora URI** .  Skopiuj i Zapisz adres URL.

> [!NOTE]
> Teraz można zalogować się do portalu [usług sieci web Azure Machine Learning](https://services.azureml.net) , aby uzyskać klucz interfejsu API dla klasycznej usługi sieci Web Machine Learning.
> 
> 

**Użyj nowej usługi sieci Web**

1. W portalu [usług sieci web Azure Machine Learning](https://services.azureml.net) kliknij pozycję **usługi sieci Web**, a następnie wybierz usługę sieci Web. 
2. Kliknij **pozycję**Użyj.
3. Poszukaj sekcji **podstawowe informacje o zużyciu** . Skopiuj i Zapisz **klucz podstawowy** i adres URL **odpowiedzi żądania** .

## <a name="steps-to-add-a-new-web-service"></a>Procedura dodawania nowej usługi sieci Web

1. Wdróż usługę sieci Web lub Użyj istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci Web, zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).
2. Kliknij **pozycję**Użyj.
3. Poszukaj sekcji **podstawowe informacje o zużyciu** . Skopiuj i Zapisz **klucz podstawowy** i adres URL **odpowiedzi żądania** .
4. W programie Excel przejdź do sekcji **usługi sieci Web** (Jeśli jesteś w sekcji **przewidywania** , kliknij strzałkę wstecz, aby przejść do listy usług sieci Web).
   
    ![Przejdź do wyboru usługi sieci Web](./media/excel-add-in-for-web-services/image3.png)
5. Kliknij pozycję **Dodaj usługę sieci Web**.
6. Wklej adres URL do pola tekstowego dodatku programu Excel o nazwie **URL**.
7. Wklej interfejs API/klucz podstawowy do pola tekstowego oznaczonego **kluczem interfejsu API**.
8. Kliknij pozycję **Dodaj**.
   
    ![Adres URL i klucz interfejsu API klasycznej usługi sieci Web.](./media/excel-add-in-for-web-services/image6.png)
9. Aby skorzystać z usługi sieci Web, postępuj zgodnie z powyższymi wskazówkami "procedura używania istniejącej usługi sieci Web".

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Po zapisaniu skoroszytu zostanie również zapisany interfejs API/klucz podstawowy dla dodanych usług sieci Web. Oznacza to, że skoroszyt powinien być współużytkowany tylko z zaufanymi osobami.

Zadawaj pytania w poniższej sekcji komentarza lub na naszym [forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
