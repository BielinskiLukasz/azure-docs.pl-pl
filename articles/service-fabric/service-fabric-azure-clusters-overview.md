---
title: Tworzenie klastrów Service Fabric platformy Azure w systemach Windows Server i Linux | Microsoft Docs
description: Klastry Service Fabric są uruchamiane w systemach Windows Server i Linux, co oznacza, że będzie można wdrażać i hostować aplikacje Service Fabric wszędzie tam, gdzie będzie można uruchamiać system Windows Server lub Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: edb6a84762ce65e65ff33492f3a7bcebbce60777
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390377"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Omówienie klastrów Service Fabric na platformie Azure
Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem klastra. Klastry mogą być skalowane do tysięcy węzłów. Jeśli dodasz nowe węzły do klastra, Service Fabric ponownie zrównoważą repliki partycji usługi i wystąpienia w większej liczbie węzłów. Ogólna wydajność aplikacji zwiększa się i rywalizacja o zmniejszenie ilości pamięci. Jeśli węzły w klastrze nie są efektywnie używane, można zmniejszyć liczbę węzłów w klastrze. Service Fabric ponownie zrównoważy repliki partycji i wystąpienia na zmniejszonej liczbie węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

Typ węzła definiuje rozmiar, liczbę i właściwości zestawu węzłów (maszyn wirtualnych) w klastrze. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”. Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych). [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu. 

## <a name="cluster-components-and-resources"></a>Składniki i zasoby klastra
Klaster Service Fabric na platformie Azure to zasób platformy Azure, który używa i współdziała z innymi zasobami platformy Azure:
* Maszyny wirtualne i karty sieci wirtualnych
* zestawy skalowania maszyn wirtualnych
* sieci wirtualne
* moduły równoważenia obciążenia
* konta magazynu
* publiczne adresy IP

![Klaster usługi Service Fabric][Image]

### <a name="virtual-machine"></a>Maszyna wirtualna
[Maszyna wirtualna](/azure/virtual-machines/) będąca częścią klastra nazywa się węzłem, ale technicznie, węzeł klastra to proces środowiska uruchomieniowego Service Fabric. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają właściwości, takie jak [Właściwości umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Każda maszyna lub maszyna wirtualna ma uruchomioną funkcję Autostart, *elemencie fabrichost określono. exe*, która uruchamia się w czasie rozruchu, a następnie uruchamia dwa pliki wykonywalne, *Fabric. exe* i *FabricGateway. exe*, które tworzą ten węzeł. Wdrożenie produkcyjne to jeden węzeł na maszynę fizyczną lub wirtualną. Scenariusze testowania umożliwiają hostowanie wielu węzłów na jednej maszynie lub maszynie wirtualnej przez uruchomienie wielu wystąpień programu *Fabric. exe* i *FabricGateway. exe*.

Każda maszyna wirtualna jest skojarzona z kartą sieci wirtualnej (NIC), a każda karta sieciowa ma przypisany prywatny adres IP.  Maszyna wirtualna jest przypisana do sieci wirtualnej i lokalnego modułu równoważenia obciążenia za pomocą karty sieciowej.

Wszystkie maszyny wirtualne w klastrze są umieszczane w sieci wirtualnej.  Wszystkie węzły w tym samym typie węzła/zestawie skalowania są umieszczane w tej samej podsieci w sieci wirtualnej.  Te węzły mają tylko prywatne adresy IP i nie są adresowane bezpośrednio poza siecią wirtualną.  Klienci mogą uzyskiwać dostęp do usług w węzłach za pomocą modułu równoważenia obciążenia platformy Azure.

### <a name="scale-setnode-type"></a>Zestaw skalowania/typ węzła
Podczas tworzenia klastra należy zdefiniować jeden lub więcej typów węzłów.  Węzły lub maszyny wirtualne w typie węzła mają taki sam rozmiar i cechy, jak liczba procesorów CPU, pamięć, liczba dysków i we/wy dysku.  Na przykład jeden typ węzła może być dla małych maszyn wirtualnych frontonu z portami otwartymi w Internecie, podczas gdy inny typ węzła może być dla dużych maszyn wirtualnych zaplecza, które przetwarzają dane. W klastrach platformy Azure każdy typ węzła jest mapowany na [zestaw skalowania maszyn wirtualnych](/azure/virtual-machine-scale-sets/).

Zestawy skalowania umożliwiają wdrażanie kolekcji maszyn wirtualnych jako zestawu i zarządzanie nimi. Każdy typ węzła zdefiniowany w klastrze Service Fabric platformy Azure konfiguruje oddzielny zestaw skalowania. Środowisko uruchomieniowe Service Fabric jest ładowane do każdej maszyny wirtualnej w zestawie skalowania przy użyciu rozszerzeń maszyny wirtualnej platformy Azure. Można niezależnie skalować każdy typ węzła w górę lub w dół, zmieniać jednostkę SKU systemu operacyjnego działającą w każdym węźle klastra, mieć otwarte różne zestawy portów i korzystać z różnych metryk pojemności. Zestaw skalowania ma pięć [domen uaktualnienia](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) i pięć [domen błędów](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) i może mieć do 100 maszyn wirtualnych.  Tworzenie klastrów o więcej niż 100 węzłów przez utworzenie wielu zestawów skalowania/typów węzłów.

> [!IMPORTANT]
> Wybór liczby typów węzłów dla klastra i właściwości każdego typu węzła (rozmiar, podstawowe, połączenie z Internetem, liczba maszyn wirtualnych itp.) jest ważnym zadaniem.  Aby uzyskać więcej informacji, Przeczytaj [temat zagadnienia dotyczące planowania pojemności klastra](service-fabric-cluster-capacity.md).

Aby uzyskać więcej informacji, Przeczytaj [Service Fabric typy węzłów i zestawy skalowania maszyn wirtualnych](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Wystąpienia maszyn wirtualnych są przyłączone za [modułem równoważenia obciążenia platformy Azure](/azure/load-balancer/load-balancer-overview), który jest skojarzony z [publicznym adresem IP](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) i etykietą DNS.  W przypadku inicjowania obsługi administracyjnej klastra z  *&lt;&gt;klastrem*nazwa DNS,  *&lt;ClusterName.&lt; &gt; Location&gt;. cloudapp.Azure.com* to etykieta DNS skojarzona z modułem równoważenia obciążenia przed zestawem skalowania.

Maszyny wirtualne w klastrze mają tylko [prywatne adresy IP](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Ruch związany z zarządzaniem i ruch usługi są kierowane przez publiczny moduł równoważenia obciążenia.  Ruch sieciowy jest kierowany do tych maszyn za pośrednictwem reguł translatora adresów sieciowych (połączenia klientów z określonymi węzłami/wystąpieniami) lub reguł równoważenia obciążenia (ruch przechodzi do działania okrężnego maszyn wirtualnych).  Moduł równoważenia obciążenia ma skojarzony publiczny adres IP z nazwą DNS w formacie:  *&lt;ClusterName.&lt; &gt; Location&gt;. cloudapp.Azure.com*.  Publiczny adres IP jest innym zasobem platformy Azure w grupie zasobów.  W przypadku zdefiniowania wielu typów węzłów w klastrze dla każdego typu węzła/zestawu skalowania zostanie utworzony moduł równoważenia obciążenia. Lub można skonfigurować pojedynczy moduł równoważenia obciążenia dla wielu typów węzłów.  Typ węzła podstawowego ma etykietę  *&lt;DNS ClusterName.&lt; &gt; Location&gt;. cloudapp.Azure.com*, inne typy węzłów mają  *&lt;&gt;&gt;etykietęDNS&lt; ClusterName-NodeType.&lt; Location&gt;. cloudapp.Azure.com*.

### <a name="storage-accounts"></a>Konta magazynu
Każdy typ węzła klastra jest obsługiwany przez [konto usługi Azure Storage](/azure/storage/common/storage-introduction) i dyski zarządzane.

## <a name="cluster-security"></a>Zabezpieczenia klastra
Klaster Service Fabric jest posiadanym zasobem.  Użytkownik jest odpowiedzialny za zabezpieczanie klastrów w celu zapobiegania łączeniu się z nimi nieautoryzowanych użytkowników. Bezpieczny klaster jest szczególnie ważny w przypadku uruchamiania obciążeń produkcyjnych w klastrze. 

### <a name="node-to-node-security"></a>Zabezpieczenia między węzłami
Zabezpieczenia między węzłami zabezpieczają komunikację między maszynami wirtualnymi lub komputerami w klastrze. Ten scenariusz zabezpieczeń zapewnia, że tylko komputery autoryzowane do dołączenia do klastra mogą uczestniczyć w aplikacjach i usługach obsługujących klaster. Service Fabric używa certyfikatów X. 509 w celu zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji.  Certyfikat klastra jest wymagany do zabezpieczenia ruchu klastra i zapewnienia uwierzytelniania klastra i serwera.  Do klastrów testowych można używać certyfikatów z podpisem własnym, ale do zabezpieczania klastrów produkcyjnych należy używać certyfikatu z zaufanego urzędu certyfikacji.

Aby uzyskać więcej informacji, zapoznaj się z tematem [zabezpieczenia między węzłami](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Zabezpieczenia między klientem a węzłem
Zabezpieczenia klienta w węźle uwierzytelniają klientów i pomagają w zabezpieczaniu komunikacji między klientem a poszczególnymi węzłami w klastrze. Ten typ zabezpieczeń pomaga upewnić się, że tylko autoryzowani użytkownicy mają dostęp do klastra i aplikacji wdrożonych w klastrze. Klienci są jednoznacznie identyfikowani przy użyciu swoich poświadczeń zabezpieczeń certyfikatu X. 509. Dowolna liczba opcjonalnych certyfikatów klienta może służyć do uwierzytelniania klientów administratorów lub użytkowników z klastrem.

Oprócz certyfikatów klienta Azure Active Directory można również skonfigurować tak, aby uwierzytelniać klientów w klastrze.

Aby uzyskać więcej informacji, zapoznaj się z artykułem [Zabezpieczenia klienta-węzła](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Access Control oparte na rolach (RBAC) umożliwia przypisywanie szczegółowych kontroli dostępu do zasobów platformy Azure.  Można przypisać różne reguły dostępu do subskrypcji, grup zasobów i zasobów.  Reguły RBAC są dziedziczone wzdłuż hierarchii zasobów, chyba że zostaną zastąpione na niższym poziomie.  Można przypisać dowolnych grup użytkowników lub użytkowników w usłudze AAD za pomocą reguł RBAC, aby wyznaczeni Użytkownicy i grupy mogli modyfikować klaster.  Aby uzyskać więcej informacji, zapoznaj się z [omówieniem usługi Azure RBAC](/azure/role-based-access-control/overview).

Service Fabric obsługuje również kontrolę dostępu, aby ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników. Dzięki temu klaster jest bezpieczniejszy. Dla klientów, którzy łączą się z klastrem, są obsługiwane dwa typy kontroli dostępu: Rola administratora i rola użytkownika.  

Aby uzyskać więcej informacji, Przeczytaj [Service Fabric Access Control opartych na rolach (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci 
Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) kontrolują ruch przychodzący i wychodzący podsieci, maszyny wirtualnej lub konkretnej karty sieciowej.  Domyślnie, gdy wiele maszyn wirtualnych jest umieszczanych w tej samej sieci wirtualnej, mogą komunikować się ze sobą za pośrednictwem dowolnego portu.  Jeśli chcesz ograniczyć komunikację między maszynami, możesz zdefiniować sieciowych grup zabezpieczeń do segmentacji sieci lub izolowania maszyn wirtualnych od siebie.  Jeśli w klastrze istnieje wiele typów węzłów, można zastosować sieciowych grup zabezpieczeń do podsieci, aby uniemożliwić komunikację między maszynami należącymi do różnych typów węzłów.  

Aby uzyskać więcej informacji, Przeczytaj o [grupach zabezpieczeń](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Skalowanie

Wymagania aplikacji zmieniają się w miarę upływu czasu. Może być konieczne zwiększenie zasobów klastra w celu spełnienia zwiększonych obciążeń aplikacji lub ruchu sieciowego albo zmniejszenie zasobów klastra po spadku popytu. Po utworzeniu klastra Service Fabric można skalować klaster w poziomie (zmienić liczbę węzłów) lub w pionie (zmienić zasoby węzłów). Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze. W miarę skalowania klastra aplikacje są automatycznie skalowane.

Aby uzyskać więcej informacji, zobacz [skalowanie klastrów platformy Azure](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Uaktualnianie
Klaster Service Fabric platformy Azure to zasób, którego jesteś członkiem, ale jest częścią zarządzaną przez firmę Microsoft. Firma Microsoft jest odpowiedzialna za stosowanie poprawek podstawowego systemu operacyjnego i wykonywanie Service Fabric uaktualnień środowiska uruchomieniowego w klastrze. Można ustawić, aby klaster otrzymywał aktualizacje automatycznego środowiska uruchomieniowego, gdy firma Microsoft wyprowadzi nową wersję, lub wybrać wybraną obsługiwaną wersję środowiska uruchomieniowego. Oprócz uaktualnień w czasie wykonywania można także aktualizować konfigurację klastra, na przykład certyfikaty lub porty aplikacji.

Aby uzyskać więcej informacji, przeczytaj temat [uaktualnianie klastrów](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Możesz tworzyć klastry na maszynach wirtualnych z następującymi systemami operacyjnymi:

| System operacyjny | Najwcześniejsza obsługiwana wersja Service Fabric |
| --- | --- |
| Windows Server 2012 R2 | Wszystkie wersje |
| Windows Server 2016 | Wszystkie wersje |
| Windows Server 1709 | 6.0 |
| System Windows Server w wersji 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

Aby uzyskać dodatkowe informacje, zobacz [obsługiwane wersje klastra na platformie Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-operating-systems)

> [!NOTE]
> Jeśli zdecydujesz się na wdrożenie Service Fabric w systemie Windows Server 1709, należy pamiętać, że (1) nie jest to długotrwała gałąź obsługi, więc może być konieczne przeniesienie wersji w przyszłości i (2) w przypadku wdrażania kontenerów kontenery utworzone w systemie Windows Server 2016 nie działają w systemie Windows Server  1709 i na odwrót (trzeba będzie ponownie skompilować je, aby je wdrożyć).
>


## <a name="next-steps"></a>Następne kroki
Przeczytaj więcej na temat [zabezpieczania](service-fabric-cluster-security.md), [skalowania](service-fabric-cluster-scaling.md)i [uaktualniania](service-fabric-cluster-upgrade.md) klastrów platformy Azure.

Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
