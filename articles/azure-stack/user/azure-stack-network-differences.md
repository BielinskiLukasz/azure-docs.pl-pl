---
title: "Sieci Azure stosu: Różnice i zagadnienia dotyczące"
description: "Więcej informacji na temat różnic i zagadnienia dotyczące podczas pracy z sieci w programie Azure stosu."
services: azure-stack
keywords: 
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 02/28/2018
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 4c881a5f5e64ddc9fc67060208f3bef6ae0f5028
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
---
# <a name="considerations-for-azure-stack-networking"></a>Zagadnienia dotyczące sieci Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Sieć w stosie Azure udostępnia wiele funkcji, których możesz znaleźć na platformie Azure z pewne różnice, które należy zrozumieć przed rozpoczęciem wdrażania.


Ten artykuł zawiera omówienie unikatowy zagadnienia dotyczące sieci i jej funkcji w stosie Azure. Aby uzyskać informacje ogólne różnice między stosu Azure i usługi Azure, zobacz [kluczowe zagadnienia dotyczące](azure-stack-considerations.md) tematu.


## <a name="cheat-sheet-networking-differences"></a>Ściągawka: różnice sieci

|Usługa | Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- | --- |
| DNS | Wielodostępne DNS | Obsługiwane| Nie jest jeszcze obsługiwane|
| |Rekordów AAAA usługi DNS|Obsługiwane|Nieobsługiwane|
| |Strefy DNS na subskrypcję|100 (ustawienie domyślne)<br>Można zwiększyć na żądanie.|100|
| |Zestawy rekordów DNS dla strefy|5000 (ustawienie domyślne)<br>Można zwiększyć na żądanie.|5000|
||Serwery nazw dla delegowania strefy|Azure Podaj cztery serwery nazw dla każdej strefy użytkownika (dzierżawcy), która jest tworzona.|Stos Azure udostępnia dwa serwery nazw dla każdej strefy użytkownika (dzierżawcy), która jest tworzona.|
| Sieć wirtualna|Wirtualne sieci równorzędne|Połącz dwie sieci wirtualne w tym samym regionie za pośrednictwem sieci Azure sieci szkieletowej.|Nie jest jeszcze obsługiwane|
| |Adresy IPv6|Można przypisać adres IPv6 w ramach [Konfiguracja interfejsu sieciowego](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Obsługiwany jest tylko protokół IPv4.|
|Bramy sieci VPN|Brama sieci VPN typu punkt lokacja|Obsługiwane|Nie jest jeszcze obsługiwane|
| |Brama sieci wirtualnej do sieci wirtualnej|Obsługiwane|Nie jest jeszcze obsługiwane|
| |Jednostki SKU bramy sieci VPN|Obsługa Basic, GW1, GW2, GW3, standardowe wysokiej wydajności, niezwykle wydajny. |Obsługa podstawowa, standardowa i jednostki SKU wysokiej wydajności.|
|Moduł równoważenia obciążenia|Publiczne adresy IP protokołu IPv6|Obsługa przypisywanie IPv6 publicznego adresu IP do modułu równoważenia obciążenia.|Obsługiwany jest tylko protokół IPv4.|
|Network Watcher|Monitor sieci dzierżawcy możliwości monitorowania sieci|Obsługiwane|Nie jest jeszcze obsługiwane|
|CDN|Profile sieci dostarczania zawartości|Obsługiwane|Nie jest jeszcze obsługiwane|
|Brama aplikacji|Równoważenia obciążenia warstwy 7|Obsługiwane|Nie jest jeszcze obsługiwane|
|Traffic Manager|Kierującą ruch przychodzący do aplikacji optymalnej wydajności i niezawodności.|Obsługiwane|Nie jest jeszcze obsługiwane|
|ExpressRoute|Skonfigurowanie istnieje w przypadku szybkiego połączenia do usług chmurowych firmy Microsoft z zakładzie infrastruktury lub wspólnej lokalizacji lokalnej.|Obsługiwane|Obsługa nawiązywania stosu Azure obwodzie usługi Express Route.|

## <a name="next-steps"></a>Kolejne kroki

[Usługa DNS w usłudze Azure Stack](azure-stack-dns.md)
