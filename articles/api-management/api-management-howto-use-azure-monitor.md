---
title: Monitorowanie opublikowanych interfejsów API w usłudze Azure API Management | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak monitorować interfejs API w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 3501284be2430941863bbae90f044df8ba5e7a2a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454808"
---
# <a name="monitor-published-apis"></a>Monitorowanie opublikowanych interfejsów API

Dzięki usłudze Azure Monitor możesz wykonywać wizualizacje i zapytania, ustalać trasy, archiwizować i podejmować działania dotyczące metryk lub dzienników pochodzących z zasobów platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie dzienników aktywności
> * Wyświetlanie dzienników diagnostycznych
> * Wyświetlanie metryk interfejsu API 
> * Konfigurowanie reguły alertu, gdy interfejs API odbiera nieautoryzowane wywołania

W poniższym filmie wideo pokazano, jak monitorować usługę API Management przy użyciu usługi Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ukończ również następujący samouczek: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Wyświetlanie metryk interfejsów API

Usługa API Management emituje metryki co minutę, oferując wgląd w stan i kondycję interfejsów API w czasie zbliżonym do rzeczywistego. Poniżej znajduje się podsumowanie niektórych dostępnych metryk:

* Wydajność (wersja zapoznawcza): ułatwia podejmowanie decyzji dotyczących uaktualniania usług APIM lub ich zmiany na starszą wersję. Metryka jest emitowana co minutę i odzwierciedla pojemność bramy w momencie raportowania. Wartość metryki należy do zakresu od 0 do 100 i jest obliczana w oparciu o zasoby bramy, takie jak wykorzystanie procesora i pamięci.
* Całkowita liczba żądań bramy: liczba żądań interfejsu API w danym okresie. 
* Żądania bramy zakończone powodzeniem: liczba żądań interfejsu API, które otrzymały kody odpowiedzi HTTP informujące o powodzeniu, w tym 304, 307 i mniejsze niż 301 (na przykład 200).
* Żądania bramy zakończone niepowodzeniem: liczba żądań interfejsu API, które otrzymały kody odpowiedzi HTTP informujące o błędzie, w tym 400 i większe niż 500.
* Nieautoryzowane żądania bramy: liczba żądań interfejsu API, które otrzymały kody odpowiedzi HTTP, w tym 401, 403 i 429.
* Inne żądania bramy: liczba żądań interfejsu API, które otrzymały kody odpowiedzi HTTP nienależące do żadnej z powyższych kategorii (na przykład 418).

![wykres metryk](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Aby uzyskać dostęp do metryk:

1. Wybierz pozycję **Metryki** w menu w dolnej części strony.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. Z listy rozwijanej wybierz interesujące Cię metryki. Na przykład wybierz metrykę **Żądania bramy zakończone powodzeniem**. Możesz również dodać więcej metryk do wykresu.
3. Wykres przedstawia łączną liczbę wywołań interfejsu API zakończonych powodzeniem.

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Konfigurowanie reguły alertu na potrzeby nieautoryzowanego żądania

Można skonfigurować odbieranie alertów w oparciu o metryki i dzienniki aktywności. Usługa Azure Monitor umożliwia skonfigurowanie alertu powodującego wykonywanie następujących czynności po jego wyzwoleniu:

* Wysłanie powiadomienia e-mail
* Wywołanie elementu webhook
* Wywołanie aplikacji logiki platformy Azure

Aby skonfigurować alerty:

1. Wybierz pozycję **Alerty (klasyczne)** na pasku menu w dolnej części strony.

    ![alerts](./media/api-management-azure-monitor/api-management-alert-rules-blade.png)

2. Wybierz pozycję **Dodaj alert dotyczący metryki**.
3. Wprowadź **nazwę** tego alertu.
4. Wybierz pozycję **Nieautoryzowane żądania bramy** jako metrykę do monitorowania.
5. Wybierz pozycję **Właściciele, współautorzy i czytelnicy poczty e-mail**.
6. Naciśnij przycisk **OK**.
7. Spróbuj wywołać interfejs API konferencji bez klucza API. Jako właściciel tej usługi API Management otrzymasz alert e-mail. 

    > [!TIP]
    > Wyzwolenie reguły alertu może również spowodować wywołanie elementu webhook lub aplikacji logiki platformy Azure.

    ![set-up-alert](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="activity-logs"></a>Dzienniki aktywności

Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do usług API Management. Korzystając z tych dzienników, można określić rodzaj, użytkownika i czas każdej operacji zapisu (PUT, POST, DELETE) wykonanej względem usług API Management.

> [!NOTE]
> Dzienniki aktywności nie obejmują operacji odczytu (GET) ani operacji wykonywanych w witrynie Azure Portal albo przy użyciu oryginalnych interfejsów API zarządzania.

Dostęp do dzienników aktywności można uzyskać w usłudze API Management, a dostęp do wszystkich zasobów platformy Azure — w usłudze Azure Monitor. 

![dzienniki aktywności](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie usługi APIM.
2. Kliknij pozycję **Dziennik aktywności**.

    ![dziennik aktywności](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Wybierz żądany zakres filtrowania i kliknij przycisk **Zastosuj**.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Dzienniki diagnostyczne zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji, a także pomagają rozwiązywać problemy. Dzienniki diagnostyczne różnią się od dzienników aktywności. Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do zasobów platformy Azure. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez zasób.

Aby skonfigurować dzienniki diagnostyczne:

1. Wybierz wystąpienie usługi APIM.
2. Kliknij pozycję **Ustawienia diagnostyczne**.

    ![dzienniki diagnostyczne](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Kliknij pozycję **Włącz diagnostykę**. Dzienniki diagnostyczne wraz z metrykami można zarchiwizować na koncie magazynu, przesyłać strumieniowo do usługi Event Hub lub wysłać do dziennika usługi Azure Monitor. 

Usługa API Management udostępnia obecnie dzienniki diagnostyczne (przetwarzane w trybie wsadowym co godzinę) dotyczące poszczególnych żądań interfejsu AP. Każdy wpis jest zgodny z następującym schematem:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Właściwość  | Typ | Opis |
| ------------- | ------------- | ------------- |
| isRequestSuccess | wartość logiczna | Wartość true, jeśli żądanie HTTP zostało zakończone z kodem stanu odpowiedzi z zakresu 2xx lub 3xx |
| time | data i godzina | Znacznik czasu odbierania żądania HTTP przez bramę |
| operationName | ciąg | Wartość stała „Microsoft.ApiManagement/GatewayLogs” |
| category | ciąg | Wartość stała „GatewayLogs” |
| durationMs | liczba całkowita | Liczba milisekund od momentu odebrania żądania w bramie do momentu pełnego wysłania odpowiedzi |
| callerIpAddress | ciąg | Adres IP bezpośredniego modułu wywołującego bramy (może być pośrednik) |
| correlationId | ciąg | Unikatowy identyfikator żądania HTTP przypisany przez usługę API Management |
| location | ciąg | Nazwa regionu platformy Azure, w którym znajdowała się brama przetwarzająca żądanie |
| httpStatusCodeCategory | ciąg | Kategoria kodu stanu odpowiedzi HTTP: Powodzenie (301 lub mniej albo 304 lub 307), Bez autoryzacji (401, 403, 429), Błąd (400, od 500 do 600), Inne |
| resourceId | ciąg | Identyfikator zasobu usługi API Management /SUBSCRIPTIONS/<subscription>/RESOURCEGROUPS/<grupa_zasobów>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/<name> |
| properties | obiekt | Właściwości bieżącego żądania |
| method | ciąg | Metoda HTTP żądania przychodzącego |
| url | ciąg | Adres URL żądania przychodzącego |
| clientProtocol | ciąg | Wersja protokołu HTTP żądania przychodzącego |
| responseCode | liczba całkowita | Kod stanu odpowiedzi HTTP wysłanej do klienta |
| backendMethod | ciąg | Metoda HTTP żądania wysłanego do zaplecza |
| backendUrl | ciąg | Adres URL żądania wysłanego do zaplecza |
| backendResponseCode | liczba całkowita | Kod odpowiedzi HTTP odebranej z zaplecza |
| backendProtocol | ciąg | Wersja protokołu HTTP żądania wysłanego do zaplecza | 
| requestSize | liczba całkowita | Liczba bajtów odebranych od klienta podczas przetwarzania żądania | 
| responseSize | liczba całkowita | Liczba bajtów wysłanych do klienta podczas przetwarzania żądania | 
| cache | ciąg | Stan zaangażowania pamięci podręcznej usługi API Management w przetwarzanie żądania (tj. trafienie, chybienie, brak) | 
| cacheTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy pamięci podręcznej w usłudze API Management (łączenie, wysyłanie i odbieranie bajtów) | 
| backendTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy zaplecza (łączenie, wysyłanie i odbieranie bajtów) | 
| clientTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy klienta (łączenie, wysyłanie i odbieranie bajtów) | 
| apiId | ciąg | Identyfikator jednostki interfejsu API dla bieżącego żądania | 
| operationId | ciąg | Identyfikator jednostki operacji dla bieżącego żądania | 
| productId | ciąg | Identyfikator jednostki produktu dla bieżącego żądania | 
| userId | ciąg | Identyfikator jednostki użytkownika dla bieżącego żądania | 
| apimSubscriptionId | ciąg | Identyfikator jednostki subskrypcji dla bieżącego żądania | 
| backendId | ciąg | Identyfikator jednostki zaplecza dla bieżącego żądania | 
| LastError | obiekt | Ostatni błąd przetwarzania żądania | 
| elapsed | liczba całkowita | Liczba milisekund, które upłynęły od momentu odebrania żądania przez bramę do momentu wystąpienia błędu | 
| source | ciąg | Nazwa wewnętrznej procedury obsługi przetwarzania lub zasad, które spowodowały błąd | 
| scope | ciąg | Zakres dokumentu zasad zawierający zasady, które spowodowały błąd | 
| section | ciąg | Sekcja dokumentu zasad zawierająca zasady, które spowodowały błąd | 
| reason | ciąg | Przyczyna błędu | 
| message | ciąg | Komunikat o błędzie | 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie dzienników aktywności
> * Wyświetlanie dzienników diagnostycznych
> * Wyświetlanie metryk interfejsu API
> * Konfigurowanie reguły alertu, gdy interfejs API odbiera nieautoryzowane wywołania

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Śledzenie wywołań](api-management-howto-api-inspector.md)
