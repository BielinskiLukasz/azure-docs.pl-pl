---
title: Frontony i pule zaplecza w usłudze Azure predrzwiczk | Microsoft Docs
description: W tym artykule opisano, jakie zaplecze i pule zaplecza są zgodne z konfiguracją drzwi.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: b764799d3f40cef24a0412ac950026af650d4ec7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229031"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Frontony i pule zaplecza w usłudze Azure front-drzwi
W tym artykule opisano koncepcje dotyczące mapowania wdrożenia aplikacji za pomocą usługi Azure front-drzwi. Objaśniono w nim również różne terminy związane z konfiguracją drzwi przed końcami aplikacji.

## <a name="backends"></a>Zaplecza
Zaplecze jest równe wystąpieniu wdrożenia aplikacji w regionie. Usługa front-drzwi obsługuje zarówno platformę Azure, jak i frontony spoza platformy Azure, więc region nie jest ograniczony do regionów świadczenia usługi Azure. Ponadto może to być lokalne centrum danych lub wystąpienie aplikacji w innej chmurze.

Punkty końcowe usługi frontu drzwi odnoszą się do nazwy hosta lub publicznego adresu IP aplikacji, która może obsługiwać żądania klientów. Zastąp nie należy mylić z warstwą bazy danych, warstwą magazynowania i tak dalej. Zaplecze należy wyświetlić jako publiczny punkt końcowy zaplecza aplikacji. Po dodaniu zaplecza w puli zaplecza z drzwiami przednimi, należy również dodać następujące elementy:

- **Typ hosta zaplecza**. Typ zasobu, który ma zostać dodany. Usługa front-drzwi obsługuje Autowykrywanie Twoich zasobie aplikacji z usługi App Service, usługi w chmurze lub magazynu. Jeśli chcesz użyć innego zasobu na platformie Azure lub nawet z zapleczem innym niż Azure, wybierz opcję **host niestandardowy**.

    >[!IMPORTANT]
    >Podczas konfiguracji interfejsy API nie sprawdzają, czy zaplecze nie są dostępne w środowiskach z przodu. Upewnij się, że tylne drzwi mogą dotrzeć do zaplecza.

- **Nazwa subskrypcji i hosta zaplecza**. Jeśli nie wybrano **niestandardowego hosta** dla typu hosta zaplecza, wybierz zaplecze, wybierając odpowiednią subskrypcję i odpowiednią nazwę hosta zaplecza w interfejsie użytkownika.

- **Nagłówek hosta zaplecza**. Wartość nagłówka hosta wysyłana do zaplecza dla każdego żądania. Aby uzyskać więcej informacji, zobacz [nagłówek hosta zaplecza](#hostheader).

- **Priorytet**. Przypisywanie priorytetów do różnych zaplecza, gdy chcesz użyć podstawowego zaplecze usługi dla całego ruchu. Ponadto należy podać kopie zapasowe, jeśli podstawowy lub zapasowy frontonu są niedostępne. Aby uzyskać więcej informacji, zobacz [priorytet](front-door-routing-methods.md#priority).

- **Waga**. Przypisz wagi do różnych założonych punktów końcowych, aby dystrybuować ruch między zestawami założonymi (nawet lub zgodnie z współczynnikim wagi). Aby uzyskać więcej informacji, zobacz [wagi](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Nagłówek hosta zaplecza

Żądania przekazywane przez tylne drzwi do zaplecza zawierają pole nagłówka hosta używane przez zaplecze do pobrania zasobu dostosowanego. Wartość tego pola zazwyczaj pochodzi z identyfikatora URI zaplecza i ma hosta i port.

Na przykład żądanie wysłane dla sieci Web\.contoso.com będzie miało nagłówek hosta www\.contoso.com. Jeśli do skonfigurowania zaplecza używasz Azure Portal, wartością domyślną tego pola jest nazwa hosta zaplecza. Jeśli zaplecze to contoso-westus.azurewebsites.net, w Azure Portal wartość autowypełniana dla nagłówka hosta zaplecza będzie contoso-westus.azurewebsites.net. Jeśli jednak używasz szablonów Azure Resource Manager lub innej metody bez jawnego ustawienia tego pola, usługa front-drzwi wyśle nazwę hosta przychodzącego jako wartość dla nagłówka hosta. Jeśli żądanie zostało wykonane dla sieci Web\.contoso.com, a zaplecze to contoso-westus.azurewebsites.net z pustym polem nagłówka, usługa front-drzwi ustawi nagłówek hosta jako www\.contoso.com.

Większość zapleców aplikacji (Azure Web Apps, BLOB Storage i Cloud Services) wymaga, aby nagłówek hosta był zgodny z domeną zaplecza. Jednak Host frontonu, który jest przesyłany do zaplecza, będzie używać innej nazwy hosta, takiej jak www\.contoso.azurefd.net.

Jeśli zaplecze wymaga, aby nagłówek hosta był zgodny z nazwą hosta zaplecza, upewnij się, że nagłówek hosta zaplecza zawiera zaplecze nazwy hosta.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurowanie nagłówka hosta zaplecza dla zaplecza

Aby skonfigurować pole **nagłówka hosta zaplecza** dla zaplecza w sekcji Pula zaplecza:

1. Otwórz zasób z drzwiami wstępnymi i wybierz pulę zaplecza z zapleczem do skonfigurowania.

2. Dodaj zaplecze, jeśli nie zostało to zrobione, lub Edytuj istniejący.

3. W polu Nagłówek hosta wewnętrznej bazy danych ustaw wartość niestandardową lub pozostaw to pole puste. Nazwa hosta dla żądania przychodzącego będzie używana jako wartość nagłówka hosta.

## <a name="backend-pools"></a>Pule zaplecza
Pula zaplecza w usłudze front-drzwi odwołuje się do zestawu punktów końcowych, które odbierają podobny ruch dla swojej aplikacji. Innymi słowy, jest to logiczna Grupa wystąpień aplikacji na świecie, która odbiera ten sam ruch i reaguje z oczekiwanym zachowaniem. Te punkty końcowe są wdrażane w różnych regionach lub w tym samym regionie. Wszystkie aukcje załączone mogą być w trybie wdrożenia aktywny/aktywny lub co jest zdefiniowane jako Konfiguracja aktywna/pasywna.

Pula zaplecza definiuje, w jaki sposób różne zaplecza powinny być oceniane przez sondy kondycji. Definiuje także sposób równoważenia obciążenia między nimi.

### <a name="health-probes"></a>Sondy kondycji
Usługa front-drzwi wysyła okresowe żądania sondowania HTTP/HTTPS do każdego ze skonfigurowanych frontonów. Żądania sondowania określają bliskość i kondycję każdego zaplecza do równoważenia obciążenia żądaniami użytkowników końcowych. Ustawienia sondowania kondycji dla puli zaplecza definiują, w jaki sposób sondować stan kondycji zapleczy aplikacji. Dla konfiguracji równoważenia obciążenia dostępne są następujące ustawienia:

- **Ścieżka**. Adres URL służący do żądania sondowania dla wszystkich zapleczy w puli zaplecza. Na przykład jeśli jeden z zacontoso-westus.azurewebsites.netów jest ustawiony na wartość/Probe/test.aspx, a następnie w środowiskach usługi front-drzwi, przy założeniu, że protokół jest ustawiony na HTTP, wyśle żądania sondowania kondycji do\:http//contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokół**. Określa, czy wysyłać żądania sondowania kondycji z usługi front-drzwi do frontonu przy użyciu protokołu HTTP lub HTTPS.

- **Interwał (w sekundach)** . Definiuje częstotliwość sond kondycji do zakończenia lub interwałów, w których każde ze środowisk przed drzwiami wysyła sondę.

    >[!NOTE]
    >Aby przyspieszyć przełączanie w tryb failover, ustaw wartość interwał na niższą. Im niższa wartość, tym wyższego natężenia głośności sondy kondycji. Na przykład jeśli interwał jest ustawiony na 30 sekund z 90 środowiskami z przodu lub punktów obecności na całym świecie, każde zaplecze otrzyma około 3-5 żądań sondy na sekundę.

Aby uzyskać więcej informacji, zobacz [sondy kondycji](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Ustawienia równoważenia obciążenia
Ustawienia równoważenia obciążenia dla puli zaplecza definiują, jak oceniane są sondy kondycji. Te ustawienia określają, czy zaplecze jest w dobrej kondycji. Sprawdzają także, jak równoważyć obciążenie ruchu między różnymi zadziałami w puli zaplecza. Dla konfiguracji równoważenia obciążenia dostępne są następujące ustawienia:

- **Rozmiar próbki**. Identyfikuje liczbę próbek sond kondycji, które należy wziąć pod uwagę w przypadku oceny kondycji zaplecza.

- **Pomyślny rozmiar próbki**. Definiuje rozmiar próbki, jak wspomniano powyżej, liczbę pomyślnych próbek wymaganych do wywołania zaplecza w dobrej kondycji. Załóżmy na przykład, że interwał sondy kondycji z przodu wynosi 30 sekund, rozmiar próbki wynosi 5, a rozmiar próbki powiódł się 3. Za każdym razem, gdy analizujemy sondy kondycji dla zaplecza, analizujemy pięć ostatnich próbek powyżej 150 sekund (5 x 30). Co najmniej trzy pomyślne sondy są wymagane do zadeklarować zaplecze jako dobra kondycja.

- **Czułość opóźnienia (dodatkowe opóźnienie)** . Określa, czy chcesz, aby drzwi frontonu wysyłali żądanie do zaplecza w ramach zakresu czułości pomiaru opóźnienia, czy też przesłać żądanie do najbliższej wewnętrznej bazy danych.

Aby uzyskać więcej informacji, zobacz [najmniej metody routingu opartego na opóźnieniu](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie profilu frontu drzwi](quickstart-create-front-door.md)
- [Jak działają tylne drzwi](front-door-routing-architecture.md)