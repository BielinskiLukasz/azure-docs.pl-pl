---
title: Ograniczenia dotyczące pul węzłów systemu Windows Server w usłudze Azure Kubernetes Service (AKS)
description: Informacje o znanych ograniczeniach w przypadku uruchamiania pul węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 3a57fbb010f8a04352d09d4b6d57cf465e3e6988
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279155"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Bieżące ograniczenia dotyczące pul węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS)

W usłudze Azure Kubernetes Service (AKS) można utworzyć pulę węzłów, w której działa system Windows Server jako system operacyjny gościa na węzłach. Te węzły mogą uruchamiać natywne aplikacje kontenera systemu Windows, takie jak te utworzone na .NET Framework. W związku z tym, że system operacyjny Linux i Windows zapewnia obsługę kontenera, niektóre typowe funkcje Kubernetes i powiązane z usługą nie są obecnie dostępne dla pul węzłów systemu Windows.

W tym artykule opisano niektóre ograniczenia i koncepcje systemu operacyjnego dla węzłów systemu Windows Server w AKS. Pule węzłów dla systemu Windows Server są obecnie w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Dodatkowe informacje można znaleźć w następujących artykułach pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Które systemy operacyjne Windows są obsługiwane?

AKS używa systemu Windows Server 2019 jako wersji systemu operacyjnego hosta i obsługuje tylko izolację procesu. Obrazy kontenerów skompilowane przy użyciu innych wersji systemu Windows Server nie są obsługiwane. [Zgodność wersji kontenera systemu Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Czy Kubernetes różnią się w systemach Windows i Linux?

Obsługa puli węzłów serwera okna zawiera pewne ograniczenia, które są częścią nadrzędnego serwera systemu Windows w projekcie Kubernetes. Te ograniczenia nie są specyficzne dla AKS. Aby uzyskać więcej informacji o tej obsłudze nadrzędnej systemu Windows Server w programie Kubernetes, zobacz sekcję [obsługiwane funkcje i ograniczenia][upstream-limitations] w artykule [wprowadzenie do obsługi systemu Windows w dokumencie Kubernetes][intro-windows] , z projektu Kubernetes.

Kubernetes jest historycznym systemem Linux. Wiele przykładów używanych w nadrzędnej witrynie sieci Web [Kubernetes.IO][kubernetes] jest przeznaczonych do użycia w węzłach systemu Linux. Podczas tworzenia wdrożeń korzystających z kontenerów systemu Windows Server następujące zagadnienia są stosowane na poziomie systemu operacyjnego:

- **Tożsamość** — system Linux identyfikuje użytkownika przez liczbę całkowitą użytkownika (UID). Użytkownik ma także alfanumeryczną nazwę użytkownika na potrzeby logowania, którą system Linux tłumaczy na UID użytkownika. Podobnie system Linux identyfikuje grupę użytkowników według identyfikatora grupy całkowitej (GID) i tłumaczy nazwę grupy na odpowiadającą jej wartość GID.
    - System Windows Server używa większego identyfikatora zabezpieczeń (SID), który jest przechowywany w bazie danych Menedżera dostępu zabezpieczeń (SAM) systemu Windows. Ta baza danych nie jest udostępniana między hostem i kontenerami ani między kontenerami.
- **Uprawnienia do plików** — system Windows Server używa listy kontroli dostępu opartej na identyfikatorach SID, a nie maski bitów uprawnień i UID + GID
- **Ścieżki plików** — Konwencja w systemie Windows Server jest używana \ zamiast/.
    - W obszarze specyfikacje instalujące woluminy określ ścieżkę dla kontenerów systemu Windows Server. Na przykład zamiast punktu instalacji */mnt/Volume* w kontenerze systemu Linux należy określić literę dysku i lokalizację, taką jak */K/Volume* , do zainstalowania jako dysk *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Jakiego rodzaju dyski są obsługiwane w systemie Windows?

Dyski platformy Azure i Azure Files są obsługiwanymi typami woluminów dostępnymi jako woluminy NTFS w kontenerze systemu Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Czy można uruchamiać tylko klastry systemu Windows w AKS?

Węzły główne (płaszczyzna kontroli) w klastrze AKS są hostowane przez AKS tej usługi, nie będą widoczne w systemie operacyjnym węzłów obsługujących składniki główne. Wszystkie klastry AKS są tworzone z domyślną pulą pierwszego węzła, który jest oparty na systemie Linux. Ta Pula węzłów zawiera usługi systemowe, które są konieczne do funkcjonowania klastra. Zaleca się uruchomienie co najmniej dwóch węzłów w pierwszej puli węzłów w celu zapewnienia niezawodności klastra i możliwości wykonywania operacji klastra. Nie można usunąć pierwszej puli węzłów opartej na systemie Linux, chyba że zostanie usunięty sam klaster AKS.

## <a name="what-network-plug-ins-are-supported"></a>Jakie wtyczki sieciowe są obsługiwane?

Klastry AKS z pulami węzłów systemu Windows muszą używać modelu sieci usługi Azure CNI (Advanced). Sieć korzystającą wtyczki kubenet (podstawowa) nie jest obsługiwana. Aby uzyskać więcej informacji na temat różnic między modelami sieci, zobacz [pojęcia dotyczące sieci dla aplikacji w AKS][azure-network-models]. — Model sieci usługi Azure CNI wymaga dodatkowego planowania i zagadnień związanych z zarządzaniem adresami IP. Aby uzyskać więcej informacji na temat planowania i implementowania usługi Azure CNI, zobacz [Konfigurowanie sieci Azure CNI w programie AKS][configure-azure-cni].

## <a name="can-i-change-the-min--of-pods-per-node"></a>Czy mogę zmienić minimalną liczbę zasobników na węzeł?

Obecnie jest wymagane ustawienie co najmniej 30 zasobników, aby zapewnić niezawodność klastrów.

## <a name="how-do-patch-my-windows-nodes"></a>Jak zastosować poprawki do węzłów systemu Windows?

Węzły systemu Windows Server w AKS muszą zostać *uaktualnione* w celu pobrania najnowszych poprawek poprawek i aktualizacji. Aktualizacje systemu Windows nie są włączone na węzłach w AKS. AKS zwalnia nowe obrazy puli węzłów, gdy tylko poprawki są dostępne, klienci są odpowiedzialni za uaktualnienie pul węzłów, aby zachować bieżące informacje na temat poprawek i poprawek. Dotyczy to również używanej wersji Kubernetes. Informacje o wersji AKS będą wskazywać, kiedy są dostępne nowe wersje. Aby uzyskać więcej informacji na temat uaktualniania puli węzłów systemu Windows Server, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

> [!NOTE]
> Zaktualizowany obraz systemu Windows Server będzie używany tylko wtedy, gdy uaktualnienie klastra (uaktualnienie płaszczyzny kontroli) zostało wykonane przed uaktualnieniem puli węzłów
>

## <a name="how-many-node-pools-can-i-create"></a>Ile pul węzłów można utworzyć?

Klaster AKS może mieć maksymalnie osiem pul węzłów (8). W ramach pul węzłów można mieć maksymalnie 400 węzłów. [Ograniczenia puli węzłów][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Jak Nazywam się pulami węzłów systemu Windows?

Należy zachować nazwę maksymalnie 6 (sześć znaków). Jest to bieżące ograniczenie AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Czy wszystkie funkcje są obsługiwane z węzłami systemu Windows?

Zasady sieciowe i korzystającą wtyczki kubenet nie są obecnie obsługiwane w węzłach systemu Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Czy można uruchamiać kontrolery transferu danych przychodzących w węzłach systemu Windows?

Tak, kontroler transferu danych przychodzących obsługujący kontenery systemu Windows Server można uruchamiać w węzłach systemu Windows w AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Czy mogę używać Azure Dev Spaces z węzłami systemu Windows?

Azure Dev Spaces jest obecnie dostępna tylko dla pul węzłów opartych na systemie Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Czy moje kontenery systemu Windows Server używają gMSA?

Obsługa kont usług zarządzanych przez grupę (gMSA) nie jest obecnie dostępna w AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Czy mogę używać Azure Monitor do kontenerów z węzłami i kontenerami systemu Windows?

Tak, jednak Azure Monitor nie zbiera dzienników (stdout) z kontenerów systemu Windows. Nadal możesz dołączyć strumień strumieni strumienia stdout z kontenera systemu Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Co zrobić, jeśli potrzebuję funkcji, która nie jest obsługiwana?

Pracujemy nad udostępnieniem wszystkich funkcji potrzebnych do systemu Windows w AKS, ale jeśli wystąpią przerwy, projekt typu "open source", a [AKS-Engine][aks-engine] umożliwia łatwą i w pełni dostosowywalny sposób uruchamiania Kubernetes na platformie Azure, w tym pomocy technicznej systemu Windows. Zapoznaj się z naszym planem funkcji [AKS plan][aks-roadmap].

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z kontenerami systemu Windows Server w programie AKS, należy [utworzyć pulę węzłów, w której działa system Windows Server w AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
