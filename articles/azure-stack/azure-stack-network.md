---
title: "Sieci integracji zagadnienia dotyczące stosu Azure zintegrowanych systemów | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak zaplanować integracji sieci centrum danych z wieloma węzłami Azure stosu."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 43d79a8c14751ee25eaca7a3b50649702d159d76
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
---
## <a name="network-connectivity"></a>Połączenie sieciowe
Ten artykuł zawiera informacje infrastruktury sieci stosu Azure, aby ułatwić wybór najlepiej integrowanie stosu Azure z istniejącym środowiskiem sieci. 

> [!NOTE]
> Do rozpoznawania nazw DNS zewnętrznych ze stosu Azure (na przykład www.bing.com), musisz podać serwerów DNS, aby przekazywała żądania DNS. Aby uzyskać więcej informacji o wymaganiach usługi Azure DNS stosu, zobacz [integracja centrum danych Azure stosu - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Projekt sieci fizycznej
Rozwiązanie stosu Azure wymaga odporne i wysokiej dostępności infrastruktury fizycznej do obsługi swoich operacji i usług. Poniższy diagram przedstawia naszych zalecany projekt:

![Zalecany projekt sieci Azure stosu](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Sieci logiczne
Sieci logiczne stanowią abstrakcję powiązanej z nimi infrastruktury sieci fizycznej. Są one używane do organizacji i uproszczenia przypisań sieci hostów, maszyn wirtualnych i usług. W ramach tworzenia sieci logicznej Lokacje sieciowe są tworzone na definiowanie wirtualnych sieci lokalnych (VLAN), podsieci IP i IP pary podsieci/sieci VLAN, które są skojarzone z sieci logicznej w każdej lokalizacji fizycznej.

W poniższej tabeli przedstawiono sieci logiczne i skojarzone zakresy podsieci IPv4, które należy zaplanować:

| Sieć logiczna | Opis | Rozmiar | 
| -------- | ------------- | ------------ | 
| Publiczny adres VIP | Publiczne adresy IP niewielki zestaw usług Azure stosu, z resztą używanych przez maszyny wirtualne dzierżawcy. Infrastruktura stosu Azure używa 32 adresów z tą siecią. Jeśli zamierzasz korzystać z usługi aplikacji i dostawców zasobów SQL, używane są adresy więcej 7. | / 26 (62 hostów) - /22 (1022 hostów)<br><br>Zalecane = prefiksie/24 (254 hostów) | 
| Infrastruktura przełączników | Point-to-Point adresy IP do celów routingu dedykowanego przełącznika interfejsów zarządzania i adresy sprzężenia zwrotnego przypisane do tego przełącznika. | /26 | 
| Infrastruktura | Używane składniki wewnętrzne stosu Azure do komunikacji. | /24 |
| Prywatne | Używane dla sieci magazynu i prywatne adresy VIP. | /24 | 
| BMC | Używany do komunikacji ze składnikami bmc na hostach fizycznych. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Infrastruktura sieciowa
Infrastruktura sieci Azure stosu składa się z kilku sieci logiczne, które są skonfigurowane na przełączników. Na poniższym diagramie przedstawiono te sieci logiczne oraz sposób ich integracji z top-of-rack (TOR), kontrolera zarządzania płytą główną (BMC) i obramowania przełączników (sieci klienta).

![Połączenia diagram i przełącznik sieci logicznej](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Sieci BMC
Ta sieć jest dedykowany do łączenia wszystkich kontrolerów zarządzania płytą główną (znanej także jako service procesory, na przykład iDRAC iLO, iBMC, itp.) do sieci zarządzania. Jeśli jest obecny, host cyklu życia sprzętu (HLH) znajduje się w tej sieci i może udostępnić OEM określone oprogramowanie do obsługi sprzętu i/lub monitorowania. 

### <a name="private-network"></a>Sieć prywatna
Ta prefiksie/24 254 hosta w sieci IP jest prywatna w regionie Azure stosu (nie zwiększa poza urządzeń przełącznika obramowania regionu Azure stosu) i jest podzielony na dwie podsieci:

- **Sieć magazynowania**. Migracja na żywo /25 126 hosta w sieci IP używane do obsługi ruchu sieciowego magazynu Direct spacje i bloku komunikatów serwera (SMB) i maszyny wirtualnej. 
- **Wewnętrzna sieć wirtualna IP**. A/25 sieciowych dedykowanych do wewnętrznych adresów VIP usługi równoważenia obciążenia oprogramowania.

### <a name="azure-stack-infrastructure-network"></a>Sieć platformy Azure infrastruktury stosu
To/24 sieć jest przeznaczona do wewnętrznych składników stosu Azure, dzięki czemu mogą komunikować się i wymieniać dane między sobą. Ta podsieć wymaga rutowalne adresy IP, ale polega ochrona poufności do rozwiązania przy użyciu list kontroli dostępu (ACL), nie jest mogą być kierowane poza przełączniki obramowania, z wyjątkiem zakresu bardzo mały rozmiar odpowiednikiem /27 sieci wykorzystana przez niektóre z nich usługi, gdy potrzebują dostępu do zasobów zewnętrznych i/lub Internetu. 

### <a name="public-infrastructure-network"></a>Sieć publicznych infrastruktury
To/27 sieć jest bardzo małych zakresu podsieci infrastruktury Azure stosu wspomniano wcześniej, nie wymaga publiczne adresy IP, ale wymagają dostępu do Internetu za pośrednictwem NAT lub przezroczystego obiektu pośredniczącego. Ta sieć zostanie przydzielone dla systemu konsoli odzyskiwania awaryjnego (ERCS), ERCS maszyna wirtualna wymaga dostępu do Internetu podczas rejestracji na platformie Azure i powinny obsługiwać routing do sieci zarządzania na potrzeby rozwiązywania problemów.

### <a name="public-vip-network"></a>Sieć publiczna VIP
Sieć publiczna adresu VIP jest przypisany do kontrolera sieci w stosie Azure. Nie jest sieć logiczna na przełączniku. Programowego używa puli adresów i przypisuje/32 sieci dla obciążeń dzierżawców. W tabeli routingu przełącznika tych 32 adresów IP są rozgłaszane jako dostępny za pośrednictwem protokołu BGP. Ta sieć zawiera zewnętrzne dostępny lub publicznych adresów IP. Infrastruktury Azure stosu używa co najmniej 8 adresów tego publicznej sieci wirtualne adresy IP, podczas gdy pozostała jest używany przez dzierżawione maszyny wirtualne. Rozmiar sieci w tej podsieci może należeć do zakresu od co najmniej /26 (64 hostów) do maksymalnie /22 (1022 hostów), zaleca się zaplanowanie prefiksie/24 sieci.

### <a name="switch-infrastructure-network"></a>Przełącznik sieci infrastruktury
To 26 sieć jest podsieci, która zawiera podsieci /30 IP routingu point-to-point (2 IP hosta w) i sprzężenia zwrotne, które są przeznaczone wyłącznie/32 podsieci dla wewnątrzpasmowe przełącznik zarządzania i identyfikator routera BGP Ten zakres adresów IP musi obsługiwać Routing zewnętrznie z rozwiązania Azure stosu centrum danych, mogą być prywatnych lub publicznych adresów IP.

### <a name="switch-management-network"></a>Sieć zarządzania przełącznika
Ta /29 (host 6 adresów IP) sieć jest przeznaczona do łączenia porty zarządzania przełączników. Umożliwia dostęp poza pasmem dla wdrażania, zarządzania i rozwiązywania problemów. Jest on obliczany od przełącznika sieci infrastruktury wymienionych powyżej.

## <a name="publish-azure-stack-services"></a>Publikowanie usług Azure stosu
Należy udostępnić usług Azure stosu użytkowników z zewnętrznego stosu Azure. Stos Azure konfiguruje różnych punktów końcowych dla jego role infrastruktury. Te punkty końcowe są przypisywane wirtualne adresy IP z puli publicznych adresów IP. Wpis DNS jest tworzony dla każdego punktu końcowego w zewnętrznych strefę DNS, który określono w czasie wdrażania. Na przykład aplikacji portal użytkowników jest przypisany wpis hosta DNS portalu.  *&lt;region >.&lt; Nazwa FQDN >*.

### <a name="ports-and-urls"></a>Porty i adresy URL
Aby usługi Azure stosu (takich jak portali usługi Azure Resource Manager, DNS, itp) dostępny dla zewnętrznych sieci, musisz zezwolić na ruch przychodzący z tymi punktami końcowymi dla określonych adresów URL, porty i protokoły.
 
W przypadku wdrożenia w przypadku, gdy pasma przezroczystego obiektu pośredniczącego, aby serwer proxy tradycyjnych należy zezwolić na określone porty i adresy URL dla komunikacji wychodzącej. Obejmują one porty i adresy URL dla tożsamości, zespolonego marketplace poprawek i aktualizacji, rejestracji i dane użycia.

Aby uzyskać więcej informacji, zobacz:
- [Przychodzący portów i protokołów](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [Adresy URL i portów wychodzących](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)


## <a name="next-steps"></a>Kolejne kroki
[Azure łączności obramowania stosu](azure-stack-border-connectivity.md)