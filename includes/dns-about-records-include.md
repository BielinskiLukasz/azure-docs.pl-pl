---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 261ae22348cd82b129727261c619727917e19c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73832050"
---
### <a name="record-names"></a>Nazwy rekordów

W usłudze DNS platformy Azure rekordy są określane przy użyciu nazw względnych. *W pełni kwalifikowana* nazwa domeny (FQDN) zawiera nazwę strefy, której nie zawiera nazwa *względna*. Na przykład względna `www` nazwa rekordu `contoso.com` w strefie podaje `www.contoso.com`w pełni kwalifikowaną nazwę rekordu .

Rekord *wierzchołka* to rekord DNS w katalogu głównym (*wierzchołku*) strefy DNS. Na przykład w strefie `contoso.com`DNS rekord wierzchołka `contoso.com` ma również w pełni kwalifikowaną nazwę (czasami nazywana jest *domeną nagą).*  Zgodnie z konwencją\@nazwa względna ' ' jest używana do reprezentowania rekordów wierzchołka.

### <a name="record-types"></a>Typy rekordów

Każdy rekord DNS ma nazwę i typ. Rekordy są pogrupowane w różne typy według danych, które zawierają. Najczęściej spotykanym typem jest rekord „A”, który mapuje nazwę na adres IPv4. Innym często spotykanym typem jest rekord „MX”, który mapuje nazwę na serwer poczty e-mail.

Usługa Azure DNS obsługuje wszystkie typowe typy rekordów DNS: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV i TXT. Należy pamiętać, że [rekordy SPF są reprezentowane przy użyciu rekordu TXT](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Zestawy rekordów

Czasami trzeba utworzyć więcej niż jeden rekord DNS określonego typu o danej nazwie. Na przykład załóżmy, że witryna sieci Web „www.contoso.com” jest hostowana pod dwoma różnymi adresami IP. Witryna sieci Web wymaga dwóch różnych rekordów A, po jednym dla każdego adresu IP. Oto przykład zestawu rekordów:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

System DNS platformy Azure zarządza wszystkimi rekordami DNS za pomocą *zestawów rekordów*. Zestaw rekordów (określany także jako zestaw rekordów *zasobów*) jest kolekcją rekordów DNS w strefie, które mają taką samą nazwę i są tego samego typu. Większość zestawów rekordów zawiera jeden rekord. Sytuacje taka jak powyższa, w których zestaw rekordów zawiera więcej niż jeden rekord, również mają często miejsce.

Na przykład załóżmy, że utworzono wcześniej rekord A „www” w strefie „contoso.com” wskazujący na adres IP „134.170.185.46” (pierwszy rekord powyżej).  W celu utworzenia drugiego rekordu ten rekord zostanie dodany do istniejącego zestawu rekordów zamiast tworzenia dodatkowego zestawu rekordów.

Typy rekordów SOA i CNAME stanowią wyjątki. Standardy systemu DNS nie zezwalają na występowanie wielu rekordów tych typów o takiej samej nazwie, w związku z czym te zestawy rekordów mogą zawierać tylko jeden rekord.