---
title: Zabezpieczenia sieciowe Azure Event Grid zasobów
description: W tym artykule opisano sposób konfigurowania dostępu z prywatnych punktów końcowych
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300156"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Zabezpieczenia sieciowe Azure Event Grid zasobów
W tym artykule opisano sposób korzystania z następujących funkcji zabezpieczeń w Azure Event Grid: 

- Tagi usług dla ruchu wychodzącego (wersja zapoznawcza)
- Reguły zapory IP dla ruchu przychodzącego (wersja zapoznawcza)
- Prywatne punkty końcowe dla ruchu przychodzącego (wersja zapoznawcza)


## <a name="service-tags"></a>Tagi usługi
Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Aby uzyskać więcej informacji na temat tagów usługi, zobacz [Omówienie tagów usług](../virtual-network/service-tags-overview.md).

Możesz użyć tagów usługi do definiowania kontroli dostępu do sieci w [sieciowych grupach zabezpieczeń](../virtual-network/security-overview.md#security-rules) lub [Zapora platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń należy używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **AzureEventGrid**) w odpowiednim polu *źródłowym* lub *miejscu docelowym* reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić.

| Tag usługi | Przeznaczenie | Może korzystać z ruchu przychodzącego lub wychodzącego? | Może być regionalna? | Czy można używać z zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. <br/><br/>*Uwaga:* Ten tag obejmuje Azure Event Grid punkty końcowe w regionach Południowo-środkowe stany USA, Wschodnie stany USA 2, zachodnie stany USA 2 i stany USA. | Oba | Nie | Nie |


## <a name="ip-firewall"></a>Zapora IP 
Azure Event Grid obsługuje kontrolę dostępu opartą na protokole IP do publikowania w tematach i domenach. Za pomocą kontrolek opartych na protokole IP można ograniczyć wydawców do tematu lub domeny tylko do zestawu zatwierdzonego zestawu maszyn i usług w chmurze. Ta funkcja uzupełnia [mechanizmy uwierzytelniania](security-authentication.md) obsługiwane przez Event Grid.

Domyślnie temat i domena są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją do tylko zestawu adresów IP lub zakresów adresów IP w notacji [CIDR (bez klas routingu między domenami)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Wydawcy pochodzące z dowolnego innego adresu IP będą odrzucani i otrzymają odpowiedź 403 (zabroniony).


## <a name="private-endpoints"></a>Prywatne punkty końcowe
Możesz użyć [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) , aby umożliwić bezpieczne wykonywanie zdarzeń bezpośrednio z sieci wirtualnej do Twoich tematów i domen za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md) bez pośrednictwa publicznego Internetu. Prywatny punkt końcowy jest specjalnym interfejsem sieciowym dla usługi platformy Azure w sieci wirtualnej. Gdy tworzysz prywatny punkt końcowy dla tematu lub domeny, zapewnia on bezpieczną łączność między klientami w sieci wirtualnej i zasobem Event Grid. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą Event Grid używa bezpiecznego linku prywatnego.

![Diagram architektury](./media/network-security/architecture-diagram.png)

Używanie prywatnych punktów końcowych dla zasobu Event Grid pozwala:

- Bezpieczny dostęp do tematu lub domeny z sieci wirtualnej przez sieć szkieletową firmy Microsoft w przeciwieństwie do publicznego Internetu.
- Bezpiecznie łącz się z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu sieci VPN lub usługa expressroutes z prywatną komunikację równorzędną.

W przypadku tworzenia prywatnego punktu końcowego tematu lub domeny w sieci wirtualnej do zatwierdzenia Właściciel zasobu jest wysyłana prośba o zgodę. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem zasobu, to żądanie zgody jest automatycznie zatwierdzane. W przeciwnym razie połączenie jest w stanie **oczekiwania** , dopóki nie zostanie zatwierdzone. Aplikacje w sieci wirtualnej mogą bezproblemowo łączyć się z usługą Event Grid za pośrednictwem prywatnego punktu końcowego, używając tych samych parametrów połączenia i mechanizmów autoryzacji, które mogą być używane w inny sposób. Właściciele zasobów mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi za pomocą karty **prywatne punkty końcowe** dla zasobu w Azure Portal.

### <a name="connect-to-private-endpoints"></a>Nawiązywanie połączenia z prywatnymi punktami końcowymi
Wydawcy w sieci wirtualnej korzystającej z prywatnego punktu końcowego powinni używać tych samych parametrów połączenia dla tematu lub domeny, co w przypadku klientów nawiązujących połączenie z publicznym punktem końcowym. Funkcja rozpoznawania nazw DNS automatycznie kieruje połączenia z sieci wirtualnej do tematu lub domeny za pośrednictwem prywatnego linku. Event Grid domyślnie tworzy [prywatną strefę DNS](../dns/private-dns-overview.md) dołączoną do sieci wirtualnej z niezbędną aktualizacją prywatnych punktów końcowych. Jeśli jednak używasz własnego serwera DNS, może być konieczne wprowadzenie dodatkowych zmian w konfiguracji DNS.

### <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych
Podczas tworzenia prywatnego punktu końcowego rekord CNAME DNS dla zasobu zostanie zaktualizowany do aliasu w poddomenie z prefiksem `privatelink`. Domyślnie zostanie utworzona prywatna strefa DNS odpowiadająca poddomenie linku prywatnego. 

W przypadku rozwiązywania tematu lub adresu URL punktu końcowego domeny spoza sieci wirtualnej z prywatnym punktem końcowym jest on rozpoznawany jako publiczny punkt końcowy usługi. Rekordy zasobów DNS dla "tematu", po rozwiązaniu spoza sieci **wirtualnej** obsługującej prywatny punkt końcowy, będą:

| Name (Nazwa)                                          | Typ      | Wartość                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<\> profilu usługi Azure Traffic Manager

Możesz odmówić dostępu do klienta lub kontrolować go poza siecią wirtualną za pośrednictwem publicznego punktu końcowego za pomocą [zapory IP](#ip-firewall). 

Po rozwiązaniu problemu z siecią wirtualną, w której jest przechowywany prywatny punkt końcowy, temat lub adres URL punktu końcowego domeny jest rozpoznawany jako adres IP prywatnego punktu końcowego. Rekordy zasobów DNS dla tematu "temat", po rozwiązaniu z wewnątrz sieci **wirtualnej** obsługującej prywatny punkt końcowy, będą:

| Name (Nazwa)                                          | Typ      | Wartość                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Takie podejście umożliwia dostęp do tematu lub domeny przy użyciu tych samych parametrów połączenia dla klientów w sieci wirtualnej obsługujących prywatne punkty końcowe oraz klientów spoza sieci wirtualnej.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci mogą rozpoznać nazwę FQDN tematu lub domeny punktu końcowego jako adres IP prywatnego punktu końcowego. Skonfiguruj serwer DNS w celu delegowania poddomeny prywatnego linku do prywatnej strefy DNS dla sieci wirtualnej lub skonfiguruj rekordy A dla `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` przy użyciu prywatnego adresu IP punktu końcowego.

Zalecaną nazwą strefy DNS jest `privatelink.eventgrid.azure.net`.

### <a name="private-endpoints-and-publishing"></a>Prywatne punkty końcowe i publikowanie

W poniższej tabeli opisano różne stany połączenia prywatnego punktu końcowego oraz efekty publikowania:

| Stan połączenia   |  Pomyślne opublikowanie (tak/nie) |
| ------------------ | -------------------------------|
| Zatwierdzono           | Tak                            |
| Odrzucono           | Nie                             |
| Oczekiwanie            | Nie                             |
| Odłączony       | Nie                             |

Aby publikowanie zakończyło się pomyślnie, stan połączenia prywatnego punktu końcowego powinien zostać **zatwierdzony**. Jeśli połączenie zostanie odrzucone, nie można go zatwierdzić przy użyciu Azure Portal. Jedyną możliwością jest usunięcie połączenia i utworzenie nowego.

## <a name="pricing-and-quotas"></a>Cennik i przydziały
**Prywatne punkty końcowe** są dostępne tylko w przypadku tematów i domen w warstwie Premium. Event Grid umożliwia tworzenie do 64 połączeń prywatnych punktów końcowych na temat lub domenę. Aby przeprowadzić uaktualnienie z warstwy Podstawowa do warstwy Premium, zobacz artykuł dotyczący [aktualizacji warstwy cenowej](update-tier.md) .

Funkcja **Zapora IP** jest dostępna w warstwach Podstawowa i Premium Event Grid. Zezwalamy na tworzenie do 16 reguł zapory IP dla tematu lub domeny.


## <a name="next-steps"></a>Następne kroki
Można skonfigurować zaporę IP dla zasobu Event Grid, aby ograniczyć dostęp za pośrednictwem publicznej sieci Internet tylko z poziomu wybranych adresów IP lub zakresów adresów IP. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie zapory IP](configure-firewall.md).

Można skonfigurować prywatne punkty końcowe, aby ograniczyć dostęp tylko z wybranych sieci wirtualnych. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md).
