---
title: Odporność i odzyskiwanie po awarii w usłudze Azure SignalR Service
description: Omówienie sposobu konfigurowania wielu wystąpień usługi SignalR w celu uzyskania odporności i udostępnienia możliwości odzyskiwania po awarii
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: cf0f345b0fbf9fea2512f72c1996c9a1597cc0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747646"
---
# <a name="resiliency-and-disaster-recovery"></a>Odporność i odzyskiwanie po awarii

Odporność i odzyskiwanie po awarii to typowe potrzeby systemów online. Sama usługa Azure SignalR Service gwarantuje dostępność na poziomie 99,9%, ale nadal jest to usługa regionalna.
Wystąpienie usługi jest zawsze uruchomione w jednym regionie i nie będzie awaryjnie do innego regionu, gdy występuje awaria całego regionu.

Zamiast tego zestaw SDK usługi udostępnia obsługę wielu wystąpień usługi SignalR i automatycznego przełączania do innych wystąpień, gdy niektóre z nich staną się niedostępne.
Dzięki tej funkcji będziesz mógł odzyskać, gdy ma miejsce awaria, ale musisz samodzielnie skonfigurować odpowiednią topologię systemu. W tym dokumencie dowiesz się, jak to zrobić.

## <a name="high-available-architecture-for-signalr-service"></a>Architektura wysokiej dostępności usługi SignalR

Aby zapewnić odporność obejmującą wiele regionów dla usługi SignalR, musisz skonfigurować wiele wystąpień usługi w różnych regionach. Wtedy gdy jeden region nie działa, można użyć innych jako zapasowych.
W przypadku łączenia wielu wystąpień usługi z serwerem aplikacji można określić dla wystąpienia rolę określającą je jako podstawowe lub pomocnicze.
Wystąpienie podstawowe to wystąpienie, które obsługuje ruch online, a wystąpienie pomocnicze to wystąpienie w pełni funkcjonalne, lecz stanowiące zapas dla wystąpienia podstawowego.
W implementacji zestawu SDK negocjacja zwraca tylko podstawowe punkty końcowe, dzięki czemu w standardowych przypadkach klienci łączą się tylko z podstawowymi punktami końcowymi.
Ale jeśli podstawowe wystąpienie nie działa, negocjacja zwróci pomocnicze punkty końcowe, dzięki czemu klient będzie mógł nawiązać połączenie.
Podstawowe wystąpienie i serwer aplikacji są połączone za pośrednictwem normalnych połączeń z serwerem, ale pomocnicze wystąpienie i serwer aplikacji są połączone za pośrednictwem specjalnego typu połączenia o nazwie słabe połączenie.
Główną różnicą słabe połączenie jest to, że nie akceptuje routingu połączenia klienta, ponieważ wystąpienie pomocnicze znajduje się w innym regionie. Routing klienta do innego regionu nie jest optymalnym wyborem (zwiększa opóźnienie).

Jedno wystąpienie usługi może mieć różne role, jeśli łączy się z wieloma serwerami aplikacji.
Jedną z typowych konfiguracji dla scenariusza z wieloma regionami jest skonfigurowanie dwóch (lub więcej) par wystąpień usługi SignalR i serwerów aplikacji.
W każdej parze serwer aplikacji i usługa SignalR znajdują się w tym samym regionie, a usługa SignalR jest połączona z serwerem aplikacji za pomocą roli podstawowej.
Serwer aplikacji i usługa SignalR w różnych parach są także połączone, lecz usługa SignalR łączy się z serwerem w innym regionie jako pomocnicza.

W takiej topologii komunikat z jednego serwera może być w dalszym ciągu dostarczony do wszystkich klientów, ponieważ wszystkie serwery aplikacji i wystąpienia usługi SignalR są wzajemnie połączone.
Lecz kiedy klient nawiązuje połączenie, jest on zawsze kierowany do serwera aplikacji w tym samym regionie w celu osiągnięcia optymalnego opóźnienia sieci.

Poniżej przedstawiono diagram, który ilustruje taką topologię:

![topology](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Konfigurowanie serwerów aplikacji od kątem wielu wystąpień usługi SignalR

Po utworzeniu usługi SignalR i serwerów aplikacji w każdym regionie można skonfigurować serwery aplikacji pod kątem łączenia ze wszystkimi wystąpieniami usługi SignalR.

Można to zrobić na dwa sposoby:

### <a name="through-config"></a>Za pomocą konfiguracji

Powinieneś już wiedzieć, jak ustawić ciąg połączenia usługi SignalR za pomocą zmiennych środowiskowych/ustawień `Azure:SignalR:ConnectionString`aplikacji/web.cofig, we wpisie konfigura o nazwie .
Jeśli masz wiele punktów końcowych, możesz je umieścić w wielu wpisach konfiguracji, z których każdy jest w następującym formacie:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

W tym miejscu parametr `<name>` to nazwa punktu końcowego, a parametr `<role>` to jego rola (podstawowy lub pomocniczy).
Nazwa jest opcjonalna, lecz będzie przydatna, jeśli chcesz dostosować zachowanie routingu między wieloma punktami końcowymi.

### <a name="through-code"></a>Za pomocą kodu

Jeśli wolisz przechowywać parametry połączenia w innym miejscu, możesz również odczytać je `AddAzureSignalR()` w kodzie i używać `MapAzureSignalR()` ich jako parametrów podczas wywoływania (w ASP.NET Core) lub (w ASP.NET).

Oto przykładowy kod:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

Można skonfigurować wiele wystąpień podstawowych lub pomocniczych. Jeśli istnieje wiele wystąpień podstawowych i/lub pomocniczych, negotiate zwróci punkt końcowy w następującej kolejności:

1. Jeśli istnieje co najmniej jedno wystąpienie podstawowe w trybie online, zwróć losowe podstawowe wystąpienie online.
2. Jeśli wszystkie wystąpienia podstawowe są w dół, zwraca losowe pomocnicze wystąpienie online.

## <a name="failover-sequence-and-best-practice"></a>Sekwencja trybu failover i najlepsze rozwiązania

Teraz masz skonfigurowaną właściwą topologię systemu. Po każdorazowym wyłączeniu wystąpienia usługi SignalR ruch online będzie kierowany do innych wystąpień.
Oto, co dzieje się w przypadku wyłączenia wystąpienia podstawowego (i jego odzyskania po pewnym czasie):

1. Gdy wystąpienie podstawowe usługi zostanie wyłączone, wszystkie połączenia serwera z tym wystąpieniem zostaną przerwane.
2. Wszystkie serwery połączone z tym wystąpieniem oznaczą ją jako będące w trybie offline, a negocjacje przestaną zwracać ten punkt końcowy i zaczną zwracać pomocniczy punkt końcowy.
3. Wszystkie połączenia klientów z tym wystąpieniem zostaną także zamknięte, a klienci nawiążą połączenie ponownie. Ponieważ serwery aplikacji teraz zwracają pomocniczy punkt końcowy, klienci łączą się z wystąpieniem pomocniczym.
4. Teraz wystąpienie pomocnicze obsługuje cały ruch w trybie online. Wszystkie komunikaty wysyłane przez serwer do klientów nadal mogą być dostarczane, ponieważ wystąpienie pomocnicze jest połączone ze wszystkimi serwerami aplikacji. Jednak komunikaty wysyłane przez klienta do serwera są kierowane tylko do serwera aplikacji w tym samym regionie.
5. Po odzyskaniu wystąpienia podstawowego i jego przejściu z powrotem w tryb online serwer aplikacji nawiąże z nim połączenie ponownie i oznaczy je jako będące w trybie online. Negocjacje będą teraz zwracać podstawowy punkt końcowy i nowi klienci będą z powrotem łączyć się z nim. Istniejący klienci nie zostaną rozłączeni i będą w dalszym ciągu kierowani do wystąpienia dodatkowego, dopóki sami się nie rozłączą.

Poniższe diagramy przedstawiają przechodzenie w tryb failover przez usługę SignalR:

Rys. 1 Przed przejściem w tryb failover ![Przed przejściem w tryb failover](media/signalr-concept-disaster-recovery/before-failover.png)

Rys. 2 Po przejściu w tryb failover ![Po przejściu w tryb failover](media/signalr-concept-disaster-recovery/after-failover.png)

Rys. 3 Krótko po odzyskaniu wystąpienia podstawowego ![Krótko po odzyskaniu wystąpienia podstawowego](media/signalr-concept-disaster-recovery/after-recover.png)

Jak widać, w normalnym przypadku tylko podstawowy serwer aplikacji i podstawowa usługa SignalR obsługują ruch online (w kolorze niebieskim).
Po przejściu w tryb failover pomocniczy serwer aplikacji i pomocnicza usługa SignalR także stają się aktywne.
Gdy podstawowa usługa SignalR wróci do trybu online, nowi klienci będą łączyć się z podstawową usługą SignalR. Lecz istniejący klienci nadal będą łączyć się z wystąpieniem pomocniczym, więc oba wystąpienia będą obsługiwać ruch.
Gdy wszyscy istniejący klienci rozłączą się, system wróci do normalnego stanu (Rys. 1).

Architekturę wysokiej dostępności z wieloma regionami można zaimplementować za pomocą dwóch głównych wzorców:

1. Pierwszy to para serwer aplikacji i wystąpienie usługi SignalR obsługujące cały ruch online z inną parą jako zapasem (wzorzec aktywny/pasywny przedstawiony na rysunku 1). 
2. Drugi to dwie (lub więcej) pary serwerów aplikacji i wystąpień usługi SignalR, przy czym wszystkie obsługują ruch online i służą jako zapas dla innych par (wzorzec aktywny/aktywny podobny do przedstawionego na rysunku 3).

Usługa SignalR może obsługiwać oba wzorce, a główna różnica zależy od sposobu implementacji serwerów aplikacji.
Jeśli serwery aplikacji są zgodne ze wzorcem aktywny/pasywny, usługa SignalR będzie także zgodna z tym wzorcem (ponieważ podstawowy serwer aplikacji zwraca tylko swoje podstawowe wystąpienie usługi SignalR).
Jeśli serwery aplikacji są zgodne z wzorcem aktywny/aktywny, usługa SignalR będzie także zgodna z tym wzorcem (ponieważ wszystkie serwery aplikacji zwracają własne podstawowe wystąpienia usługi SignalR, to wszystkie wystąpienia mogą obsługiwać ruch).

Należy zauważyć, niezależnie od wzorców, które chcesz użyć, należy połączyć każde wystąpienie usługi SignalR do serwera aplikacji jako podstawowy.

Ze względu na charakter połączenia usługi SignalR (jest to połączenie długie) połączenia klientów zostaną przerwane w przypadku wystąpienia awarii i przejścia do trybu failover.
Takie przypadki należy obsługiwać po stronie klienta, aby były przezroczyste dla użytkowników końcowych. Na przykład należy nawiązać ponownie połączenie po zamknięciu połączenia.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania aplikacji pod kątem odporności ze względu na usługę SignalR. Aby dowiedzieć się więcej na temat połączenia serwera/klienta i routingu połączenia w usłudze SignalR, możesz przeczytać [ten artykuł](signalr-concept-internals.md) przedstawiający elementy wewnętrzne usługi SignalR.

W przypadku skalowania scenariuszy, takich jak dzielenie na fragmenty, które używają wielu wystąpień razem do obsługi dużej liczby połączeń, przeczytaj [sposób skalowania wielu wystąpień](signalr-howto-scale-multi-instances.md).
