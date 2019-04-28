---
title: Tworzenie funkcji integrującej się z usługą Azure Logic Apps
description: Utwórz funkcję integrującą się z usługą Azure Logic Apps i usługami Azure Cognitive Services, aby kategoryzować tonację w tweetach i wysyłać powiadomienia, gdy poziom nastrojów (tonacji) będzie niski.
services: functions, logic-apps, cognitive-services
keywords: workflow, cloud apps, cloud services, business processes, system integration, enterprise application integration, EAI
author: craigshoemaker
manager: jeconnoc
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: cshoe
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 5e0ef8287b7ce257cd551a1ace043ccbed72b50b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037134"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Tworzenie funkcji integrującej się z usługą Azure Logic Apps

Usługa Azure Functions integruje się z usługą Azure Logic Apps w Projektancie aplikacji usługi Logic Apps. Ta integracja umożliwia używanie mocy obliczeniowej usługi Functions w aranżacjach z innymi usługami platformy Azure oraz innych dostawców. 

W tym samouczku pokazano, jak za pomocą usługi Functions z usługami Logic Apps i Microsoft Cognitive Services na platformie Azure uruchamiać analizę tonacji we wpisach w usłudze Twitter. Funkcja wyzwalana przez protokół HTTP kategoryzuje tweety jako zielone, żółte lub czerwone na podstawie wyniku tonacji. W razie wykrycia niskiego poziomu tonacji wysyłana jest wiadomość e-mail. 

![ilustracja: pierwsze dwa kroki aplikacji w Projektancie aplikacji usługi Logic Apps](media/functions-twitter-email/00-logic-app-overview.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu interfejsu API usług Cognitive Services.
> * Tworzenie funkcji kategoryzującej tonację w tweetach.
> * Tworzenie aplikacji logiki łączącej się z usługą Twitter.
> * Dodawanie wykrywania tonacji do aplikacji logiki. 
> * Łączenie aplikacji logiki z funkcją.
> * Wysyłanie wiadomości e-mail na podstawie odpowiedzi z funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

+ Aktywne konto w usłudze [Twitter](https://twitter.com/). 
+ Konto usługi [Outlook.com](https://outlook.com/) (do wysyłania powiadomień).
+ Do wykonania czynności przedstawionych w tym artykule są wymagane zasoby utworzone w temacie [Tworzenie pierwszej funkcji w witrynie Azure Portal](functions-create-first-azure-function.md).  
Jeśli jeszcze tego nie zrobiono, wykonaj teraz te kroki, aby utworzyć aplikację funkcji.

## <a name="create-a-cognitive-services-resource"></a>Tworzenie zasobu usług Cognitive Services

Interfejsy API usług Cognitive Services są dostępne na platformie Azure jako pojedyncze zasoby. Użyj interfejsu API analizy tekstu do wykrywania tonacji monitorowanych tweetów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

3. Kliknij kolejno pozycje **SI i uczenie maszynowe** > **Analiza tekstu**. Następnie użyj ustawień określonych w tabeli, aby utworzyć zasób.

    ![Tworzenie strony zasobu usług Cognitive](media/functions-twitter-email/01-create-text-analytics.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | --- |
    | **Nazwa** | MyCognitiveServicesAccnt | Wybierz unikatową nazwę konta. |
    | **Lokalizacja** | Zachodnie stany USA | Użyj najbliższej lokalizacji. |
    | **Warstwa cenowa** | F0 | Rozpocznij od najniższej warstwy. Gdy wyczerpią się wywołania, przeskaluj do wyższego poziomu.|
    | **Grupa zasobów** | myResourceGroup | Użyj tej samej grupy zasobów dla wszystkich usług w tym samouczku.|

4. Kliknij przycisk **Utwórz**, aby utworzyć zasób. 

5. Kliknij pozycję **Przegląd** i skopiuj wartość **Punkt końcowy** do edytora tekstów. Ta wartość jest używana podczas tworzenia połączenia z interfejsem API usług Cognitive Services.

    ![Ustawienia usług Cognitive Services](media/functions-twitter-email/02-cognitive-services.png)

6. W kolumnie nawigacji po lewej stronie kliknij pozycję **Klucze**, a następnie skopiuj wartość pozycji **Klucz 1** i zapisz ją w edytorze tekstów. Ten klucz umożliwia łączenie aplikacji logiki z interfejsem API usług Cognitive Services. 
 
    ![Klucze usług Cognitive Services](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>Tworzenie aplikacji funkcji

Usługa Functions zapewnia doskonały sposób na odciążanie przetwarzania zadań w przepływie pracy aplikacji logiki. W tym samouczku funkcja wyzwalana przez protokół HTTP używana jest do przetwarzania wyników tonacji tweetów z usług Cognitive Services i zwracania wartości kategorii.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP  

1. Rozwiń aplikację funkcji i kliknij przycisk **+** obok pozycji **Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **W portalu**.

    ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](media/functions-twitter-email/05-function-app-create-portal.png)

2. Następnie wybierz pozycję **Element webhook i interfejs API** i kliknij pozycję **Utwórz**. 

    ![Wybieranie wyzwalacza HTTP](./media/functions-twitter-email/06-function-webhook.png)

3. Zastąp zawartość pliku `run.csx` poniższym kodem, a następnie kliknij przycisk **Zapisz**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```
    Ten kod funkcji zwraca kategorię koloru na podstawie wyniku tonacji otrzymanego w żądaniu. 

4. Aby przetestować tę funkcję, kliknij przycisk **Testuj** przy prawej krawędzi, co spowoduje rozwinięcie karty testu. Wpisz wartość `0.2` w polu **Treść żądania**, a następnie kliknij przycisk **Uruchom**. W treści odpowiedzi jest zwracana wartość **RED** (Czerwony). 

    ![Testowanie funkcji w witrynie Azure Portal](./media/functions-twitter-email/07-function-test.png)

Została utworzona funkcja kategoryzująca wyniki tonacji. Następnie należy utworzyć aplikację logiki, która zintegruje funkcję z usługą Twitter i interfejsem API usług Cognitive Services. 

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki   

1. W witrynie Azure Portal kliknij przycisk **Nowy** znajdujący się w lewym górnym rogu tej witryny.

2. Kliknij kolejno pozycje **Sieć Web** > **Aplikacja logiki**.
 
3. Następnie wpisz wartość **Nazwa**, taką jak `TweetSentiment`, i użyj ustawień określonych w tabeli.

    ![Tworzenie aplikacji logiki w witrynie Azure Portal](./media/functions-twitter-email/08-logic-app-create.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | ----------------- | ------------ | ------------- |
    | **Nazwa** | TweetSentiment | Wybierz odpowiednią nazwę dla aplikacji. |
    | **Grupa zasobów** | myResourceGroup | Wybierz tę samą istniejącą grupę zasobów co wcześniej. |
    | **Lokalizacja** | Wschodnie stany USA | Wybierz bliską lokalizację. |    

4. Po wprowadzeniu odpowiednich wartości ustawień kliknij pozycję **Utwórz** w celu utworzenia aplikacji logiki. 

5. Po utworzeniu aplikacji kliknij nową aplikację logiki przypiętą do pulpitu nawigacyjnego. Następnie w Projektancie aplikacji usługi Logic Apps przewiń w dół i kliknij szablon **Pusta aplikacja logiki**. 

    ![Szablon pustej aplikacji usługi Logic Apps](media/functions-twitter-email/09-logic-app-create-blank.png)

Teraz za pomocą Projektanta aplikacji usługi Logic Apps możesz dodać do aplikacji usługi i wyzwalacze.

## <a name="connect-to-twitter"></a>Łączenie z usługą Twitter

Najpierw utwórz połączenie z kontem w usłudze Twitter. Aplikacja logiki sonduje pod kątem tweetów, które wyzwalają uruchomienie aplikacji.

1. W projektancie kliknij usługę **Twitter**, a następnie kliknij wyzwalacz **Po wysłaniu nowego tweetu**. Zaloguj się na koncie w usłudze Twitter i zezwól aplikacji logiki na korzystanie z tego konta.

2. Użyj ustawień wyzwalacza usługi Twitter określonych w tabeli. 

    ![Ustawienia łącznika usługi Twitter](media/functions-twitter-email/10-tweet-settings.png)

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | ----------------- | ------------ | ------------- |
    | **Wyszukiwany tekst** | #Azure | Użyj hasztagu dostatecznie popularnego, aby wygenerować nowe tweety w wybranym interwale. Jeśli używasz warstwy bezpłatnej, a hasztag jest zbyt popularny, możesz szybko zużyć przydział transakcji w interfejsie API usług Cognitive Services. |
    | **Interwał** | 15 | Czas między żądaniami usługi Twitter w jednostkach częstotliwości. |
    | **Częstotliwość** | Minuta | Jednostka częstotliwości używana do sondowania usługi Twitter.  |

3.  Kliknij przycisk **Zapisz** w celu połączenia się z kontem usługi Twitter. 

Teraz aplikacja jest połączona z usługą Twitter. Następnie należy połączyć się z analizą tekstu w celu wykrywania tonacji zebranych tweetów.

## <a name="add-sentiment-detection"></a>Dodawanie wykrywania tonacji

1. Kliknij pozycję **Nowy krok**, a następnie pozycję **Dodaj akcję**.

2. W obszarze **Wybierz akcję** wpisz **Analiza tekstu**, a następnie kliknij akcję **Wykryj tonację**.
    
    ![Nowy krok, a następnie pozycja Dodaj akcję](media/functions-twitter-email/11-detect-sentiment.png)

3. Wpisz nazwę połączenia, taką jak `MyCognitiveServicesConnection`, wklej klucz interfejsu API usług Cognitive Services i punkt końcowy usług Cognitive Services zapisane w edytorze tekstów, a następnie kliknij pozycję **Utwórz**.

    ![Nowy krok, a następnie pozycja Dodaj akcję](media/functions-twitter-email/12-connection-settings.png)

4. Następnie wprowadź **Tekst tweetu** w polu tekstowym i kliknij pozycję **Nowy krok**.

    ![Definiowanie tekstu do przeanalizowania](media/functions-twitter-email/13-analyze-tweet-text.png)

Skonfigurowano już wykrywanie tonacji, więc można dodać połączenie z funkcją wykorzystującą dane wyjściowe wyniku tonacji.

## <a name="connect-sentiment-output-to-your-function"></a>Łączenie danych wyjściowych tonacji z funkcją

1. W projektancie usługi Logic Apps kliknij kolejno pozycje **Nowy krok** > **Dodaj akcję**, zastosuj filtr **Azure Functions** i kliknij pozycję **Wybierz funkcję platformy Azure**.

    ![Wykrywanie tonacji](media/functions-twitter-email/14-azure-functions.png)
  
4. Wybierz aplikację funkcji, która została utworzona wcześniej.

    ![Wybieranie funkcji](media/functions-twitter-email/15-select-function.png)

5. Wybierz funkcję, która została utworzona na potrzeby tego samouczka.

    ![Wybieranie funkcji](media/functions-twitter-email/16-select-function.png)

4. W polu **Treść żądania** kliknij pozycję **Score** (Wynik), a następnie pozycję **Zapisz**.

    ![Wynik](media/functions-twitter-email/17-function-input-score.png)

Teraz funkcja jest wyzwalana po wysłaniu wyniku tonacji z aplikacji logiki. Funkcja zwraca aplikacji logiki kategorię kodowaną kolorem. Następnie zostanie dodane powiadomienie e-mail wysyłane w razie zwrócenia przez funkcję wartości tonacji **RED** (Czerwony). 

## <a name="add-email-notifications"></a>Dodawanie powiadomień w wiadomościach e-mail

Ostatnia część przepływu pracy polega na wyzwoleniu wiadomości e-mail, gdy wynik tonacji skategoryzowano jako _RED_ (Czerwony). W tym temacie używany jest łącznik usługi Outlook.com. Wykonując podobne kroki, można użyć łącznika usługi Gmail lub programu Outlook w usłudze Office 365.   

1. W Projektancie aplikacji usługi Logic Apps kliknij pozycję **Nowy krok** > **Dodaj połączenie**. 

    ![Dodaj warunek do aplikacji logiki.](media/functions-twitter-email/18-add-condition.png)

2. Kliknij pozycję **Wybierz wartość**, a następnie pozycję **Treść**. Wybierz pozycję **jest równe**, kliknij pozycję **Wybierz wartość** i wpisz `RED`, a następnie kliknij przycisk **Zapisz**. 

    ![Wybierz akcję dla warunku.](media/functions-twitter-email/19-condition-settings.png)    

3. W obszarze **Jeśli prawda** kliknij pozycję **Dodaj akcję**, wyszukaj `outlook.com`, kliknij pozycję **Wyślij wiadomość e-mail** i zaloguj się na koncie w usłudze Outlook.com.

    ![Skonfiguruj wiadomość e-mail do wysłania oraz akcję jej wysyłania.](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Jeśli nie masz konta usługi Outlook.com, możesz wybrać inny łącznik, taki jak usługi Gmail lub programu Outlook w usłudze Office 365.

4. W akcji **Wyślij wiadomość e-mail** użyj ustawień poczty e-mail określonych w tabeli. 

    ![Skonfiguruj wiadomość e-mail do wysłania oraz akcję jej wysyłania.](media/functions-twitter-email/21-configure-email.png)
    
| Ustawienie      |  Sugerowana wartość   | Opis  |
| ----------------- | ------------ | ------------- |
| **Do** | Wpisz adres e-mail | Adres e-mail, na który będą wysyłane powiadomienia. |
| **Temat** | Wykryto negatywną tonację tweetów  | Wiersz tematu powiadomienia w wiadomości e-mail.  |
| **Treść** | Tekst tweetu, lokalizacja | Kliknij parametry **Tekst tweetu** i **Lokalizacja**. |

1. Kliknij pozycję **Zapisz**.

Przepływ pracy jest gotowy, można więc włączyć aplikację logiki i przyjrzeć się funkcji w działaniu.

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

1. W Projektancie aplikacji usługi Logic Apps kliknij pozycję **Uruchom** w celu uruchomienia aplikacji.

2. W lewej kolumnie kliknij pozycję **Przegląd**, aby zobaczyć stan aplikacji logiki. 
 
    ![Stan wykonania aplikacji logiki](media/functions-twitter-email/22-execution-history.png)

3. Opcjonalnie: kliknij jeden z przebiegów, aby wyświetlić szczegóły wykonania.

4. Przejdź do funkcji, przejrzyj dzienniki i potwierdź, że wartości tonacji zostały odebrane i przetworzone.
 
    ![Wyświetlanie dzienników funkcji](media/functions-twitter-email/sent.png)

5. Po wykryciu potencjalnie negatywnej tonacji otrzymasz wiadomość e-mail. Jeśli wiadomość e-mail nie nadeszła, możesz tak zmienić kod funkcji, aby wartość RED (Czerwony) była zwracana zawsze:

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    Po zweryfikowaniu powiadomień e-mail przywróć pierwotną postać kodu:

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > Po ukończeniu tego samouczka najlepiej jest wyłączyć aplikację logiki. Wyłączenie aplikacji pozwala uniknąć naliczania opłat za wykonania i zużywania transakcji w interfejsie API usług Cognitive Services.

Teraz wiesz już, jak łatwe jest integrowanie usługi Functions w przepływie pracy usługi Logic Apps.

## <a name="disable-the-logic-app"></a>Wyłączanie aplikacji logiki

Aby wyłączyć aplikację logiki, kliknij pozycję **Przegląd**, a następnie pozycję **Wyłącz** w górnej części ekranu. Wyłączenie aplikacji spowoduje jej zatrzymanie oraz przerwanie naliczania opłat bez usuwania aplikacji.

![Dzienniki funkcji](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu interfejsu API usług Cognitive Services.
> * Tworzenie funkcji kategoryzującej tonację w tweetach.
> * Tworzenie aplikacji logiki łączącej się z usługą Twitter.
> * Dodawanie wykrywania tonacji do aplikacji logiki. 
> * Łączenie aplikacji logiki z funkcją.
> * Wysyłanie wiadomości e-mail na podstawie odpowiedzi z funkcji.

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć dla funkcji bezserwerowy interfejs API.

> [!div class="nextstepaction"] 
> [Tworzenie bezserwerowego interfejsu API za pomocą usługi Azure Functions](functions-create-serverless-api.md)

Aby dowiedzieć się więcej na temat usługi Logic Apps, zobacz [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

