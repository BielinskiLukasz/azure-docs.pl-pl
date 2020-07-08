---
title: Integracja usługi Azure API Management z usługą Azure Application Insights
titleSuffix: Azure API Management
description: Dowiedz się, jak rejestrować i wyświetlać zdarzenia z usługi Azure API Management w usłudze Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 48a83fad3395f6ecf06fb1f1ba95aa1b06a53431
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81259140"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Jak zintegrować usługę Azure API Management z usługą Azure Application Insights

Usługa Azure API Management pozwala na łatwą integrację z usługą Azure Application Insights — rozszerzalną usługę dla deweloperów sieci Web tworzących aplikacje i zarządzające nimi na wielu platformach. Ten przewodnik przeprowadzi Cię przez każdy krok integracji i opisuje strategie zmniejszania wpływu na wydajność wystąpienia usługi API Management.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego przewodnika, musisz mieć wystąpienie usługi Azure API Management. Jeśli go nie masz, najpierw Wypełnij [samouczek](get-started-create-service-instance.md) .

## <a name="create-an-azure-application-insights-instance"></a>Tworzenie wystąpienia usługi Azure Application Insights

Aby móc korzystać z usługi Azure Application Insights, musisz najpierw utworzyć wystąpienie usługi.

1. Otwórz **Azure Portal** i przejdź do **Application Insights**.  
    ![Tworzenie usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Kliknij pozycję **+ Dodaj**.  
    ![Tworzenie usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Wypełnij formularz. Wybierz pozycję **Ogólne** jako **Typ aplikacji**.
4. Kliknij pozycję **Utwórz**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Utwórz połączenie między usługą Azure Application Insights i wystąpieniem usługi Azure API Management

1. Przejdź do **wystąpienia usługi Azure API Management** w **Azure Portal**.
2. Wybierz pozycję **Application Insights** z menu po lewej stronie.
3. Kliknij pozycję **+ Dodaj**.  
    ![Rejestrator usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Wybierz wcześniej utworzone wystąpienie **Application Insights** i podaj Krótki opis.
5. Kliknij pozycję **Utwórz**.
6. Właśnie utworzono Rejestrator Application Insights platformy Azure z kluczem Instrumentacji. Powinien teraz pojawić się na liście.  
    ![Rejestrator usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Za sceną jest tworzona jednostka [rejestratora](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger/createorupdate) w wystąpieniu API Management, zawierająca klucz Instrumentacji wystąpienia Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Włączanie rejestrowania Application Insights dla interfejsu API

1. Przejdź do **wystąpienia usługi Azure API Management** w **Azure Portal**.
2. Wybierz pozycję **Interfejsy API** z menu po lewej stronie.
3. Kliknij swój interfejs API, w tym przypadku **interfejsu API konferencji demonstracyjnej**.
4. Przejdź do karty **Ustawienia** na górnym pasku.
5. Przewiń w dół do sekcji **dzienniki diagnostyczne** .  
    ![Rejestrator usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Zaznacz pole **Włącz** .
7. Wybierz dołączony rejestrator w polu rozwijanym **miejsce docelowe** .
8. Wprowadź **100** jako **próbkowanie (%)** i zaznacz pole wyboru **zawsze Rejestruj błędy** .
9. Kliknij pozycję **Zapisz**.

> [!WARNING]
> Zastąpienie wartości domyślnej **0** w **pierwszych bajtach pola Body** może znacząco zmniejszyć wydajność interfejsów API.

> [!NOTE]
> Za sceną jest tworzona jednostka [diagnostyczna](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) o nazwie "ApplicationInsights" na poziomie interfejsu API.

| Nazwa ustawienia                        | Typ wartości                        | Opis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Włącz                              | wartość logiczna                           | Określa, czy jest włączone rejestrowanie tego interfejsu API.                                                                                                                                                                                                                                                                                                |
| Element docelowy                         | Rejestrator Application Insights platformy Azure | Określa Rejestrator Application Insights platformy Azure do użycia                                                                                                                                                                                                                                                                                           |
| Próbkowanie (%)                        | decimal                           | Wartości od 0 do 100 (procent). <br/> Określa procent żądań, które będą rejestrowane w usłudze Azure Application Insights. 0% próbkowanie oznacza, że zarejestrowano zero żądań, a próbkowanie 100% oznacza, że wszystkie żądania zostały zarejestrowane. <br/> To ustawienie służy do zmniejszania skutków wydajności żądań rejestrowania do usługi Azure Application Insights (zobacz sekcję poniżej). |
| Zawsze Rejestruj błędy                   | wartość logiczna                           | W przypadku wybrania tego ustawienia wszystkie błędy zostaną zarejestrowane w usłudze Azure Application Insights niezależnie od ustawienia **próbkowania** .                                                                                                                                                                                                                  |
| Opcje podstawowe: nagłówki              | list                              | Określa nagłówki, które będą rejestrowane w usłudze Azure Application Insights na potrzeby żądań i odpowiedzi.  Wartość domyślna: żadne nagłówki nie są rejestrowane.                                                                                                                                                                                                             |
| Opcje podstawowe: pierwsze bajty treści  | liczba całkowita                           | Określa, ile pierwszych bajtów treści jest rejestrowanych na platformie Azure Application Insights w przypadku żądań i odpowiedzi.  Wartość domyślna: treść nie jest zarejestrowana.                                                                                                                                                                                                    |
| Opcje zaawansowane: poziom szczegółowości         |                                   | Określa poziom szczegółowości. Rejestrowane będą tylko niestandardowe dane śledzenia o wyższym poziomie ważności. Wartość domyślna: informacje.                                                                                                                                                                                                                               |
| Opcje zaawansowane: żądanie frontonu  |                                   | Określa, czy i w jaki sposób *żądania frontonu* będą rejestrowane w usłudze Azure Application Insights. *Żądanie frontonu* to żądanie przychodzące do usługi Azure API Management.                                                                                                                                                                        |
| Opcje zaawansowane: odpowiedź frontonu |                                   | Określa, czy i w jaki sposób *odpowiedzi frontonu* będą rejestrowane w usłudze Azure Application Insights. *Odpowiedź frontonu* to odpowiedź wychodząca z usługi Azure API Management.                                                                                                                                                                   |
| Opcje zaawansowane: żądanie wewnętrznej bazy danych   |                                   | Określa, czy w usłudze Azure Application Insights mają być rejestrowane *żądania zaplecza* . *Żądanie zaplecza* to żądanie wychodzące z usługi Azure API Management.                                                                                                                                                                        |
| Opcje zaawansowane: odpowiedź zaplecza  |                                   | Określa, czy i w jaki sposób *odpowiedzi zaplecza* będą rejestrowane w usłudze Azure Application Insights. *Odpowiedź zaplecza* to odpowiedź przychodząca do usługi Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Można określić rejestratory na różnych poziomach — pojedynczy Rejestrator interfejsu API lub Rejestrator dla wszystkich interfejsów API.
>  
> W przypadku określenia obu:
> + Jeśli są to różne rejestratory, zostaną użyte oba typy (dzienniki multipleksowania),
> + Jeśli są to te same rejestratory, ale mają różne ustawienia, to jeden dla jednego interfejsu API (bardziej szczegółowy) przesłoni jeden dla wszystkich interfejsów API.

## <a name="what-data-is-added-to-azure-application-insights"></a>Jakie dane są dodawane do usługi Azure Application Insights

Usługa Azure Application Insights otrzymuje następujące:

+ *Żądaj* elementu telemetrii dla każdego żądania przychodzącego (*żądanie frontonu*, *odpowiedź frontonu*),
+ Element telemetrii *zależności* dla każdego żądania przekazanego do usługi zaplecza (*żądanie wewnętrznej bazy*danych, *odpowiedź zaplecza*),
+ Element telemetrii *wyjątku* dla każdego żądania zakończonego niepowodzeniem.

Żądanie zakończone niepowodzeniem to żądanie, które:

+ Niepowodzenie z powodu zamkniętego połączenia z klientem lub
+ wyzwolono sekcję *on-Error* zasad interfejsu API lub
+ ma odpowiedź zgodną z kodem stanu HTTP 4xx lub 5xx.

## <a name="performance-implications-and-log-sampling"></a>Wpływ na wydajność i próbkowanie dzienników

> [!WARNING]
> Rejestrowanie wszystkich zdarzeń może mieć poważne konsekwencje dla wydajności, w zależności od szybkości żądań przychodzących.

Na podstawie wewnętrznych testów obciążenia włączenie tej funkcji spowodowało spadek przepływności o 40% – 50%, gdy liczba żądań przekroczyła 1 000 żądań na sekundę. Usługa Azure Application Insights została zaprojektowana tak, aby używać analizy statystycznej do oceny wydajności aplikacji. Nie jest to system audytu i nie jest przystosowany do rejestrowania każdego pojedynczego żądania dla interfejsów API.

Liczbę rejestrowanych żądań można manipulować przez dostosowanie ustawienia **próbkowania** (patrz powyższe kroki). Wartość 100% oznacza, że wszystkie żądania są rejestrowane, natomiast 0% nie uwzględnia żadnego rejestrowania. **Próbkowanie** pomaga zmniejszyć ilość danych telemetrycznych, co skutecznie uniemożliwia obniżenie wydajności, zachowując jednocześnie zalety rejestrowania.

Pomijanie rejestrowania nagłówków i treści żądań i odpowiedzi spowoduje również pozytywny wpływ na rozwiązywanie problemów z wydajnością.

## <a name="video"></a>Wideo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [usłudze Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Rozważ [zarejestrowanie się w usłudze Azure Event Hubs](api-management-howto-log-event-hubs.md).
