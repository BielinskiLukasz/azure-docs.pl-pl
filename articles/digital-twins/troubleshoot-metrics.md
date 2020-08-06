---
title: Wyświetl metryki z Azure Monitor
titleSuffix: Azure Digital Twins
description: Zobacz jak wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji w Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: cc270ebb12b27c6461b00a4f7042bc3c829d02ef
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812303"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Rozwiązywanie problemów z usługą Azure Digital bliźniaczych reprezentacji: metryki

Metryki opisane w tym artykule zawierają informacje o stanie zasobów usługi Azure Digital bliźniaczych reprezentacji w ramach subskrypcji platformy Azure. Metryki Digital bliźniaczych reprezentacji na platformie Azure ułatwiają ocenę ogólnej kondycji usługi Azure Digital bliźniaczych reprezentacji i podłączonych do niej zasobów. Te dane statystyczne związane z użytkownikiem pomagają zobaczyć, co się dzieje z usługą Azure Digital bliźniaczych reprezentacji, oraz jak przeprowadzić analizę głównych przyczyn problemów bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Metryki są domyślnie włączone. Możesz wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji z poziomu [Azure Portal](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Jak wyświetlić metryki usługi Azure Digital bliźniaczych reprezentacji

1. Utwórz wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Instrukcje dotyczące sposobu konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji można znaleźć w temacie [*jak to zrobić: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-scripted.md).

2. Znajdź wystąpienie usługi Azure Digital bliźniaczych reprezentacji w [Azure Portal](https:/portal.azure.com) (możesz otworzyć stronę, wpisując jej nazwę na pasku wyszukiwania portalu). 

    Z menu wystąpienie wybierz pozycję **metryki**.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Zrzut ekranu przedstawiający stronę metryki usługi Azure Digital bliźniaczych reprezentacji":::

    Na tej stronie są wyświetlane metryki wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Widoki niestandardowe metryk można również utworzyć, wybierając te, które mają zostać wyświetlone na liście.
    
3. Możesz wysłać dane metryk do punktu końcowego Event Hubs lub konta usługi Azure Storage, klikając pozycję **Ustawienia diagnostyczne** z menu, a następnie **Dodaj ustawienie diagnostyczne**.

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="Zrzut ekranu przedstawiający stronę ustawień diagnostycznych i przycisk do dodania":::

    Aby uzyskać więcej informacji o tym procesie, zobacz [*Rozwiązywanie problemów: Konfigurowanie diagnostyki*](troubleshoot-diagnostics.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Metryki Digital bliźniaczych reprezentacji na platformie Azure i sposoby ich używania

Usługa Azure Digital bliźniaczych reprezentacji zawiera kilka metryk, które umożliwiają przedstawienie informacji o kondycji wystąpienia i skojarzonych z nim zasobów. Możesz również połączyć informacje z wielu metryk, aby malować większy obraz stanu wystąpienia. 

W poniższych tabelach opisano metryki śledzone przez poszczególne wystąpienia usługi Azure Digital bliźniaczych reprezentacji oraz sposób, w jaki każda Metryka odnosi się do ogólnego stanu wystąpienia.

#### <a name="api-request-metrics"></a>Metryki żądania interfejsu API

Metryki mające na celu wykonywanie żądań interfejsu API:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Żądania interfejsu API (wersja zapoznawcza) | Liczba | Łącznie | Liczba żądań interfejsu API dla operacji odczytu, zapisu, usuwania i zapytań cyfrowych bliźniaczych reprezentacji. |  Ponowne <br>Operacje <br>Protokol <br>Kod stanu, <br>Klasa kodu stanu, <br>Tekst stanu |
| ApiRequestsFailureRate | Współczynnik błędów żądań interfejsu API (wersja zapoznawcza) | Procent | Średnia | Procent żądań interfejsu API odbieranych przez usługę dla wystąpienia, które dają wewnętrzny błąd (500) kod odpowiedzi dla operacji odczytu, zapisu, usuwania i zapytań cyfrowych bliźniaczych reprezentacji. | Ponowne <br>Operacje <br>Protokol <br>Kod stanu, <br>Klasa kodu stanu, <br>Tekst stanu
| ApiRequestsLatency | Opóźnienie żądań interfejsu API (wersja zapoznawcza) | ) | Średnia | Czas odpowiedzi dla żądań interfejsu API. Odnosi się to do czasu od momentu odebrania żądania przez usługę Azure Digital bliźniaczych reprezentacji, dopóki usługa nie wyśle wyniku sukcesu/niepowodzenia dla operacji odczytu, zapisu, usuwania i wykonywania zapytań w funkcji Digital bliźniaczych reprezentacji. | Ponowne <br>Operacje <br>Protokół |

#### <a name="billing-metrics"></a>Metryki rozliczeń

Metryki mające na celu rozliczanie:

>[!NOTE]
> W okresie zapoznawczym **opłaty nie są**naliczane. Mimo że te metryki nadal pojawiają się na liście możliwej do wyboru, nie mają zastosowania w wersji zapoznawczej i pozostaną na zero, dopóki usługa nie przejdzie poza wersję zapoznawczą.

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Operacje interfejsu API rozliczeń (wersja zapoznawcza) | Liczba | Łącznie | Metryka rozliczeń dla wszystkich żądań interfejsu API w usłudze Azure Digital bliźniaczych reprezentacji. | Identyfikator miernika |
| BillingMessagesProcessed | Przetworzone komunikaty rozliczeń (wersja zapoznawcza) | Liczba | Łącznie | Metryka rozliczania dla liczby komunikatów wysyłanych z usługi Azure Digital bliźniaczych reprezentacji do zewnętrznych punktów końcowych. | Identyfikator miernika |
| BillingQueryUnits | Jednostki zapytań rozliczeń (wersja zapoznawcza) | Liczba | Łącznie | Liczba jednostek zapytania, wewnętrznie obliczona miara użycia zasobów usługi używana do wykonywania zapytań. Dostępny jest również pomocnik interfejsu API do mierzenia jednostek zapytania: [Klasa QueryChargeHelper](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | Identyfikator miernika |

#### <a name="ingress-metrics"></a>Metryki transferu danych przychodzących

Metryki mające na celu wykonywanie danych wejściowych:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Zdarzenia związane z transferem danych przychodzących (wersja zapoznawcza) | Liczba | Łącznie | Liczba przychodzących zdarzeń telemetrii do usługi Azure Digital bliźniaczych reprezentacji. | Wynik |
| IngressEventsFailureRate | Współczynnik błędów zdarzeń związanych z transferem danych przychodzących (wersja zapoznawcza) | Procent | Średnia | Procent przychodzących zdarzeń telemetrii, dla których usługa zwraca kod odpowiedzi z błędu wewnętrznego (500). | Wynik |
| IngressEventsLatency | Opóźnienie zdarzeń związanych z transferem danych przychodzących (wersja zapoznawcza) | ) | Średnia | Czas od momentu nadejścia zdarzenia do momentu, gdy jest on gotowy do egressed przez usługę Azure Digital bliźniaczych reprezentacji, w którym usługa wysyła wynik sukces/niepowodzenie. | Wynik |

#### <a name="routing-metrics"></a>Metryki routingu

Metryki, które mają wykonywać Routing:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji| Opis | Wymiary |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Komunikaty kierowane (wersja zapoznawcza) | Liczba | Łącznie | Liczba komunikatów kierowanych do usługi platformy Azure dla punktów końcowych, takich jak centrum zdarzeń, Service Bus lub Event Grid. | Operacje <br>Wynik |
| RoutingFailureRate | Współczynnik błędów routingu (wersja zapoznawcza) | Procent | Średnia | Wartość procentowa zdarzeń spowodowanych błędem, ponieważ są one kierowane z usługi Azure Digital bliźniaczych reprezentacji do usługi platformy Azure w punkcie końcowym, takiej jak centrum zdarzeń, Service Bus lub Event Grid. | Operacje <br>Wynik |
| RoutingLatency | Opóźnienie routingu (wersja zapoznawcza) | ) | Średnia | Czas między zdarzeniem, które ma być przesyłane z usługi Azure Digital bliźniaczych reprezentacji do momentu opublikowania w usłudze Azure Endpoint Service, takiej jak centrum zdarzeń, Service Bus lub Event Grid. | Operacje <br>Wynik |

## <a name="dimensions"></a>Wymiary

Wymiary ułatwiają znalezienie dodatkowych informacji o metrykach. Niektóre metryki routingu zawierają informacje na punkt końcowy. W poniższej tabeli wymieniono możliwe wartości dla tych wymiarów.

| Wymiar | Wartości |
| --- | --- |
| Uwierzytelnianie | OAuth |
| Operacja (dla żądań interfejsu API) | Microsoft. DigitalTwins/DigitalTwins/Delete, <br>Microsoft. DigitalTwins/DigitalTwins/Write, <br>Microsoft. DigitalTwins/DigitalTwins/odczyt, <br>Microsoft. DigitalTwins/eventroutes/odczyt, <br>Microsoft. DigitalTwins/eventroutes/Write, <br>Microsoft. DigitalTwins/eventroutes/Delete, <br>Microsoft. DigitalTwins/modele/odczyt, <br>Microsoft. DigitalTwins/modele/zapis, <br>Microsoft. DigitalTwins/modele/usuwanie, <br>Microsoft. DigitalTwins/kwerenda/akcja |
| Operacja (dla routingu) | Event Grid <br>Centrum zdarzeń, <br>Service Bus |
| Protokół | HTTPS |
| Wynik | Prawnego <br>Niepowodzenie |
| Kod stanu | 200, 404, 500 i tak dalej. |
| Klasa kodu stanu | 2xx, 4xx, 5xx itd. |
| Tekst stanu | Wewnętrzny błąd serwera, nie znaleziono i tak dalej. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zarządzaniu zarejestrowanymi metrykami dla usługi Azure Digital bliźniaczych reprezentacji, zobacz [*Rozwiązywanie problemów: Konfigurowanie diagnostyki*](troubleshoot-diagnostics.md).