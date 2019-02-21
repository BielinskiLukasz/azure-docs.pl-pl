---
title: Pojęcia — zabezpieczeń w usługach Azure Kubernetes (AKS)
description: Dowiedz się więcej na temat zabezpieczeń w usłudze Azure Kubernetes Service (AKS), tym komunikacji głównego i języka node, zasad sieciowych i wpisy tajne usługi Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: df95329128c93f326b6f2c75fb7faef1a46029cc
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456507"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Pojęcia dotyczące zabezpieczeń dla aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)

Aby chronić Twoje dane klienta, jak uruchamiać obciążenia aplikacji w usłudze Azure Kubernetes Service (AKS), zabezpieczenie klastra jest klucza. Kubernetes obejmuje składniki zabezpieczeń, takie jak *zasad sieciowych* i *wpisów tajnych*. Platforma Azure następnie dodaje składniki, takie jak sieciowe grupy zabezpieczeń i zorganizowanych uaktualnienia klastra. Te składniki zabezpieczeń są łączone w celu zachowania klaster AKS najnowsze aktualizacje zabezpieczeń systemu operacyjnego i wersji platformy Kubernetes i za pomocą zabezpieczonej zasobnika ruchu i dostęp do poufnych poświadczeń.

W tym artykule przedstawiono podstawowe pojęcia, z których zabezpieczania aplikacji w usłudze AKS:

- [Podstawowe składniki zabezpieczeń](#master-security)
- [Węzeł zabezpieczeń](#node-security)
- [Uaktualnianie klastra](#cluster-upgrades)
- [Bezpieczeństwo sieci](#network-security)
- [Wpisy tajne usługi Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Zabezpieczenia głównego

W usłudze AKS składniki rozwiązania Kubernetes są częścią usług zarządzanych przez firmę Microsoft. Każdy klaster AKS jest własne wzorzec Kubernetes gośćmi jednego, przeznaczonego do zapewnienia serwera interfejsu API, harmonogram itp. Ten wzorzec jest zarządzana i obsługiwana przez firmę Microsoft

Domyślnie serwer interfejsu API rozwiązania Kubernetes używa publicznego adresu IP i za pomocą w pełni kwalifikowaną nazwę (FQDN). Można kontrolować dostęp do serwera interfejsu API przy użyciu kontroli dostępu opartej na rolach platformy Kubernetes i usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure AD za pomocą usługi AKS][aks-aad].

## <a name="node-security"></a>Węzeł zabezpieczeń

Węzłów AKS są maszyn wirtualnych platformy Azure, które umożliwia zarządzanie i obsługa. Na węzłach jest uruchamiana zoptymalizowane dystrybucji systemu Ubuntu Linux przy użyciu rozwiązania Docker kontener środowiska uruchomieniowego. Gdy klaster AKS zostanie utworzony lub skalowany w górę, węzły zostają automatycznie wdrożone najnowsze aktualizacje zabezpieczeń systemu operacyjnego i konfiguracjach.

Platforma Azure automatycznie stosuje poprawki zabezpieczeń systemu operacyjnego węzłów w nocy. Jeśli aktualizacja zabezpieczeń systemu operacyjnego wymaga ponownego uruchomienia komputera hosta, że ponowne uruchomienie komputera nie jest wykonywana automatycznie. Ręcznie uruchom ponownie węzły lub typowym podejściem jest użycie [Kured][kured], demon ponowny rozruch typu open source dla platformy Kubernetes. Kured działa jako [DaemonSet] [ aks-daemonsets] i monitoruje każdy węzeł na obecność pliku wskazujący, że wymagane jest ponowne uruchomienie komputera. Ponowne uruchamianie odbywa się w klastrze, korzystając z tych samych [odizolowywanie i opróżnianie procesu](#cordon-and-drain) jako uaktualniania klastra.

Węzły są wdrażane w podsieci prywatnej sieci wirtualnej przy użyciu nie publiczne adresy IP, które są przypisane. Do celów zarządzania i rozwiązywania problemów protokół SSH jest domyślnie włączona. Ten dostęp SSH jest dostępna tylko przy użyciu wewnętrznego adresu IP.

Aby udostępnić magazyn, węzły używają usługi Azure Managed Disks. Dla większości rozmiarów maszyn wirtualnych węzła są obsługiwane przez dyski SSD o wysokiej wydajności dysków w warstwie Premium. Dane przechowywane na dyskach zarządzanych są automatycznie szyfrowane w stanie spoczynku na platformie Azure. W celu zapewnienia nadmiarowości te dyski jednocześnie możliwość bezpiecznego są replikowane w obrębie centrum danych platformy Azure.

Środowisk Kubernetes w usłudze AKS, lub w innych miejscach, obecnie nie są całkowicie bezpieczne dla szkodliwy użycie wielu obcych dzierżaw. Funkcje dodatkowe zabezpieczenia, takie jak *zasad zabezpieczeń zasobnika* lub więcej kontroli dostępu w zakresie opartej na rolach (RBAC) dla węzłów utrudniają luki w zabezpieczeniach. Wartość true, zabezpieczeń przy uruchamianiu obciążeń liczonych w szkodliwy wielodostępne, funkcja hypervisor to tylko poziom zabezpieczeń, które należy ufać. Domeny zabezpieczeń dla rozwiązania Kubernetes staje się całego klastra, a nie oddzielnego węzła. Dla tych typów szkodliwy obciążenia z wieloma dzierżawami należy użyć fizycznie izolowane klastrów. Aby uzyskać więcej informacji na temat sposobów izolowania obciążeń, zobacz [najlepsze rozwiązania dotyczące izolacji klastra w usłudze AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Uaktualnianie klastra

Zabezpieczenia i zgodność z przepisami lub korzystać z najnowszych funkcji platforma Azure udostępnia narzędzia do organizowania Uaktualnianie klastra usługi AKS i składników. Takie ograniczenia uaktualniania zawiera zarówno Kubernetes głównego i agenta składniki. Możesz wyświetlić [listę dostępnych wersji rozwiązania Kubernetes](supported-kubernetes-versions.md) dla klastra usługi AKS. Aby uruchomić proces uaktualniania, należy określić jedną z tych dostępnych wersji. Platforma Azure, a następnie bezpiecznie cordons i opróżnia każdy węzeł usługi AKS i przeprowadza uaktualnienie.

### <a name="cordon-and-drain"></a>Cordon i opróżniania

Podczas procesu uaktualniania węzłów AKS są indywidualnie odizolowywane z klastra, dzięki czemu nowych zasobników nie jest zaplanowane na nich. Węzły są następnie opróżniane i uaktualnione w następujący sposób:

- Istniejące zasobników są bezpiecznie zakończone i zaplanowane na pozostałych węzłach.
- Węzeł został ponownie uruchomiony, proces uaktualniania został zakończony, a następnie sprzężeń z powrotem do klastra usługi AKS.
- Zasobniki są planowane do uruchomienia na je ponownie.
- Kolejnego węzła w klastrze jest odizolowywane i opróżniane przy użyciu tego samego procesu, aż wszystkie węzły są pomyślnie uaktualniony.

Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Bezpieczeństwo sieci

Dla łączności i zabezpieczeń w sieciach lokalnych można wdrożyć klaster AKS do istniejących podsieci sieci wirtualnej platformy Azure. Te sieci wirtualne mogą mieć Azure połączenia sieci VPN typu lokacja-lokacja lub usługi Express Route do sieci lokalnej. Kontrolery transferu danych przychodzących rozwiązania Kubernetes można zdefiniować przy użyciu prywatnych, wewnętrznych adresów IP tak usługi są dostępne tylko za pośrednictwem tego połączenia sieci wewnętrznej.

### <a name="azure-network-security-groups"></a>Grupy zabezpieczeń sieci platformy Azure

Aby filtrować przepływu ruchu w sieciach wirtualnych, platforma Azure stosuje reguły sieciowej grupy zabezpieczeń. Te reguły określają źródło i docelowe zakresy adresów IP, portów i protokołów, które są dozwolone lub odmowa dostępu do zasobów. Aby zezwolić na ruch protokołu TLS do serwera interfejsu API platformy Kubernetes oraz dostęp SSH do węzłów tworzone są reguły domyślnej. Podczas tworzenia usługi przy użyciu usługi równoważenia obciążenia, mapowania portów lub ruch przychodzący trasy, AKS automatycznie modyfikuje sieciowej grupy zabezpieczeń dla ruchu odpowiedni przepływ.

## <a name="kubernetes-secrets"></a>Kubernetes Secrets

Kubernetes *klucz tajny* służy do wstrzyknąć poufne dane do zasobników, takich jak dostęp do poświadczeń ani kluczy. Należy najpierw utworzyć wpis tajny przy użyciu interfejsu API rozwiązania Kubernetes. Podczas definiowania wdrożenia lub zasobników, można żądać określonego klucza tajnego. Klucze tajne są udostępniane tylko węzły, które mają zaplanowane zasobnik, który go wymaga i klucz tajny jest przechowywany w *tmpfs*robaków napisanych nie na dysku. Po usunięciu ostatniego zasobnik w węźle, który wymaga klucza tajnego klucza tajnego został usunięty z tmpfs węzła. Klucze tajne są przechowywane w ramach danego obszaru nazw i może zostać oceniony jedynie przez zasobników w tej samej przestrzeni nazw.

Korzystanie z kluczy tajnych zmniejsza poufne informacje, które jest zdefiniowana w zasobniku lub YAML manifestu usługi. Zamiast tego możesz poprosić hasła przechowywane w serwera interfejsu API rozwiązania Kubernetes jako część manifeście YAML. To podejście zapewnia tylko dostęp pod określonym do klucza tajnego.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę z zabezpieczaniem klastry usługi AKS, zobacz [Uaktualnianie klastra usługi AKS][aks-upgrade-cluster].

Dodatkowe informacje na temat podstawowej platformy Kubernetes oraz pojęcia zostały przedstawione z usługi AKS zobacz następujące artykuły:

- [Kubernetes / klastrów usługi AKS i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes / tożsamości AKS][aks-concepts-identity]
- [Kubernetes / sieci wirtualne w usłudze AKS][aks-concepts-network]
- [Kubernetes / magazynu usługi AKS][aks-concepts-storage]
- [Kubernetes / skalowanie usługi AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
