---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/02/2019
ms.author: mlearned
ms.openlocfilehash: 4d736556147797bcd007bdab1b5328deeadea712
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827353"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)

W tym artykule opisano często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Które regiony platformy Azure obecnie udostępniają AKS?

Aby uzyskać pełną listę dostępnych regionów, zobacz [AKS regiony i dostępność][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Czy AKS obsługuje automatyczne skalowanie węzła?

Tak, możliwość automatycznego skalowania węzłów agentów w poziomie w AKS jest obecnie dostępna w wersji zapoznawczej. Aby uzyskać instrukcje [, zobacz Automatyczne skalowanie klastra w celu spełnienia wymagań aplikacji w AKS][aks-cluster-autoscaler] . Skalowanie automatyczne AKS opiera się na [automatycznym skalowaniu Kubernetes][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Czy mogę wdrożyć AKS w istniejącej sieci wirtualnej?

Tak, klaster AKS można wdrożyć w istniejącej sieci wirtualnej przy użyciu [funkcji zaawansowanej sieci][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Czy mogę ograniczyć dostęp do serwera interfejsu API Kubernetes?

Tak, możesz ograniczyć dostęp do serwera interfejsu API Kubernetes przy użyciu [zakresów adresów IP autoryzowanych przez serwer interfejsu API][api-server-authorized-ip-ranges], które są obecnie dostępne w wersji zapoznawczej.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Czy serwer interfejsu API Kubernetes może być dostępny tylko w ramach mojej sieci wirtualnej?

W tej chwili nie jest to planowane. Postęp można śledzić w [repozytorium GitHub AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Czy mogę mieć różne rozmiary maszyn wirtualnych w jednym klastrze?

Tak, możesz użyć różnych rozmiarów maszyn wirtualnych w klastrze AKS, tworząc [wiele pul węzłów][multi-node-pools], które są obecnie dostępne w wersji zapoznawczej.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Czy aktualizacje zabezpieczeń są stosowane do węzłów agenta AKS?

Platforma Azure automatycznie stosuje poprawki zabezpieczeń do węzłów systemu Linux w klastrze w porze nocnego harmonogramu. Jednak użytkownik jest odpowiedzialny za upewnienie się, że te węzły systemu Linux są ponownie uruchamiane zgodnie z potrzebami. Istnieje kilka opcji ponownego uruchamiania węzłów:

- Ręcznie za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.
- Uaktualniając klaster AKS. Klaster uaktualnia [węzły Cordon i opróżniania][cordon-drain] automatycznie, a następnie przeniesie nowy węzeł w tryb online przy użyciu najnowszego obrazu Ubuntu i nowej wersji poprawki lub pomocniczej wersji Kubernetes. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra AKS][aks-upgrade].
- Korzystając z [Kured](https://github.com/weaveworks/kured), demona ponownego uruchomienia Open Source dla Kubernetes. Kured działa jako [elementu daemonset](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) i monitoruje każdy węzeł pod kątem obecności pliku, który wskazuje, że wymagane jest ponowne uruchomienie. W klastrach ponowne uruchomienia systemu operacyjnego są zarządzane przez ten sam [proces Cordon i opróżnianie][cordon-drain] w ramach uaktualnienia klastra.

Aby uzyskać więcej informacji na temat korzystania z programu kured, zobacz temat [stosowanie aktualizacji zabezpieczeń i jądra do węzłów w AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Węzły systemu Windows Server

W przypadku węzłów systemu Windows Server (obecnie dostępnych w wersji zapoznawczej w AKS) Windows Update nie zostanie automatycznie uruchomiona i zastosuje najnowsze aktualizacje. Zgodnie z regularnym harmonogramem Windows Update cyklu wydania i procesu weryfikacji należy przeprowadzić uaktualnienie do klastra i pul węzłów systemu Windows Server w klastrze AKS. Ten proces uaktualniania tworzy węzły z zainstalowanym najnowszym obrazem systemu Windows Server i poprawkami, a następnie usuwa starsze węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego są dwie grupy zasobów utworzone za pomocą AKS?

AKS kompiluje się z wielu zasobów infrastruktury platformy Azure, w tym zestawów skalowania maszyn wirtualnych, sieci wirtualnych i dysków zarządzanych. Dzięki temu można korzystać z wielu podstawowych możliwości platformy Azure w ramach zarządzanego środowiska Kubernetes zapewnianego przez AKS. Na przykład większość typów maszyn wirtualnych platformy Azure może być używana bezpośrednio z AKS i Azure Reservations może być używana do automatycznego otrzymywania rabatów dla tych zasobów.

Aby włączyć tę architekturę, każde wdrożenie AKS obejmuje dwie grupy zasobów:

1. Należy utworzyć pierwszą grupę zasobów. Ta grupa zawiera tylko zasób usługi Kubernetes. Dostawca zasobów AKS automatycznie tworzy drugą grupę zasobów podczas wdrażania. Przykładem drugiej grupy zasobów jest *MC_myResourceGroup_myAKSCluster_eastus*. Aby uzyskać informacje na temat sposobu określania nazwy tej drugiej grupy zasobów, zobacz następną sekcję.
1. Druga grupa zasobów, znana jako *Grupa zasobów węzła*, zawiera wszystkie zasoby infrastruktury skojarzone z klastrem. Te zasoby obejmują maszyny wirtualne węzła Kubernetes, wirtualne sieci i magazyn. Domyślnie grupa zasobów węzła ma nazwę, taką jak *MC_myResourceGroup_myAKSCluster_eastus*. AKS automatycznie usuwa zasób węzła przy każdym usunięciu klastra, dlatego powinien być używany tylko w przypadku zasobów, które współdzielą cykl życia klastra.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Czy mogę podać moją własną nazwę grupy zasobów węzła AKS?

Tak. Domyślnie AKS będzie nazwać węzeł grupy zasobów *MC_resourcegroupname_clustername_location*, ale można również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie [AKS-Preview][aks-preview-cli] interfejsu wiersza polecenia platformy Azure w wersji *0.3.2* lub nowszej. Podczas tworzenia klastra AKS za pomocą polecenia [AZ AKS Create][az-aks-create] należy użyć parametru *--Node-Resource-Group* i określić nazwę grupy zasobów. W przypadku wdrażania klastra AKS za pomocą [szablonu Azure Resource Manager][aks-rm-template] można zdefiniować nazwę grupy zasobów za pomocą właściwości *nodeResourceGroup* .

* Dodatkowa grupa zasobów jest automatycznie tworzona przez dostawcę zasobów platformy Azure we własnej subskrypcji.
* Możesz określić niestandardową nazwę grupy zasobów tylko podczas tworzenia klastra.

Podczas pracy z grupą zasobów węzła należy pamiętać, że nie można:

* Określ istniejącą grupę zasobów dla grupy zasobów węzła.
* Określ inną subskrypcję dla grupy zasobów węzła.
* Zmień nazwę grupy zasobów węzła po utworzeniu klastra.
* Określ nazwy zarządzanych zasobów w grupie zasobów węzła.
* Modyfikuj lub Usuń Tagi zarządzanych zasobów w grupie zasobów węzła. (Zobacz dodatkowe informacje w następnej sekcji).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Czy mogę modyfikować Tagi i inne właściwości zasobów AKS w grupie zasobów węzła?

W przypadku modyfikowania lub usuwania tagów utworzonych przez platformę Azure i innych właściwości zasobów w grupie zasobów węzła można uzyskać nieoczekiwane wyniki, takie jak skalowanie i uaktualnianie błędów. AKS umożliwia tworzenie i modyfikowanie tagów niestandardowych. Możesz chcieć utworzyć lub zmodyfikować niestandardowe znaczniki, na przykład, aby przypisać jednostkę biznesową lub centrum kosztów. Przez zmodyfikowanie zasobów w grupie zasobów węzła w klastrze AKS należy przerwać cel poziomu usługi (SLO). Aby uzyskać więcej informacji, zobacz [czy AKS oferuje umowę dotyczącą poziomu usług?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Jakie kontrolery Kubernetes Admission są obsługiwane przez AKS? Czy można dodać lub usunąć kontrolery do przyjmowania?

AKS obsługuje następujące [Kontrolery przyjęcia][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Obecnie nie można modyfikować listy kontrolerów przyjmowania w AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Czy Azure Key Vault jest zintegrowany z AKS?

AKS nie jest obecnie natywnie zintegrowana z Azure Key Vault. Jednak [Azure Key Vault FlexVolume for Kubernetes Project][keyvault-flexvolume] umożliwia bezpośrednią integrację z Kubernetesowych zasobników z Key Vaultymi kluczami tajnymi.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Czy można uruchamiać kontenery systemu Windows Server na AKS?

Tak, kontenery systemu Windows Server są dostępne w wersji zapoznawczej. Aby uruchamiać kontenery systemu Windows Server w programie AKS, należy utworzyć pulę węzłów z systemem Windows Server jako system operacyjny gościa. Kontenery systemu Windows Server mogą korzystać tylko z systemu Windows Server 2019. Aby rozpocząć, zobacz [Tworzenie klastra AKS z pulą węzłów systemu Windows Server][aks-windows-cli].

Obsługa systemu Windows Server dla puli węzłów obejmuje pewne ograniczenia, które są częścią nadrzędnego serwera systemu Windows w projekcie Kubernetes. Aby uzyskać więcej informacji o tych ograniczeniach, zobacz [kontenery systemu Windows Server w ograniczeniach AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Czy AKS oferuje umowę dotyczącą poziomu usług?

W umowie dotyczącej poziomu usług (SLA) dostawca zgadza się zwrócić klienta koszt usługi, jeśli nie jest spełniony opublikowany poziom usług. Ponieważ AKS jest bezpłatny, żaden koszt nie jest dostępny do zwrotu, więc AKS nie ma formalnej umowy SLA. AKS ma jednak na celu zachowanie dostępności co najmniej 99,5% dla serwera interfejsu API Kubernetes.

Ważne jest, aby rozpoznać rozróżnienie między dostępnością usługi AKS, która odnosi się do czasu przestoju płaszczyzny kontroli Kubernetes i dostępności konkretnego obciążenia działającego w usłudze Azure Virtual Machines. Mimo że płaszczyzna kontroli może być niedostępna, jeśli płaszczyzna kontroli nie jest gotowa, obciążenia klastra uruchomione na maszynach wirtualnych platformy Azure mogą nadal działać. Maszyny wirtualne platformy Azure są płatnymi zasobami, które są objęte umową SLA. Przeczytaj [tutaj, aby uzyskać więcej informacji](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/) na temat umowy SLA dotyczącej maszyn wirtualnych platformy Azure i jak zwiększyć dostępność przy użyciu funkcji, takich jak [strefy dostępności][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Dlaczego nie mogę ustawić maxPods poniżej 30?

W AKS można ustawić wartość `maxPods` podczas tworzenia klastra przy użyciu interfejsu wiersza polecenia platformy Azure i szablonów Azure Resource Manager. Jednak zarówno korzystającą wtyczki kubenet, jak i Azure CNI wymagają *wartości minimalnej* (zweryfikowany podczas tworzenia):

| Obsługa sieci | Minimalnie | Maksymalnie |
| -- | :--: | :--: |
| Azure CNI | 0,30 | 250 |
| Korzystającą wtyczki kubenet | 0,30 | 110 |

Ponieważ AKS jest usługą zarządzaną, wdrażamy Dodatki i zasobniki oraz zarządzają nimi w ramach klastra. W przeszłości użytkownicy mogą definiować wartość `maxPods` niższą niż wartość, którą muszą używać zarządzane zasobniki (na przykład 30). AKS teraz oblicza minimalną liczbę zasobników przy użyciu tej formuły: (((maxPods lub (maxPods * vm_count)) >.

Użytkownicy nie mogą zastąpić minimalnej `maxPods` weryfikacji.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Czy mogę zastosować rabaty rezerwacji platformy Azure do węzłów agenta AKS?

Węzły agenta AKS są rozliczane jako standardowe maszyny wirtualne platformy Azure, więc w przypadku zakupu [rezerwacji platformy Azure][reservation-discounts] dla rozmiaru maszyny wirtualnej, który jest używany w programie AKS, te rabaty są automatycznie stosowane.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Czy mogę przenieść lub migrować mój klaster między dzierżawami platformy Azure?

Za pomocą polecenia `az aks update-credentials` można przenieść klaster AKS między dzierżawami platformy Azure. Postępuj zgodnie z instrukcjami w temacie [Wybierz, aby zaktualizować lub utworzyć nazwę główną usługi](https://docs.microsoft.com/azure/aks/update-credentials) , a następnie [zaktualizuj klaster AKS przy użyciu nowych poświadczeń](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Czy mogę przenieść klaster między subskrypcjami?

Przenoszenie klastrów między subskrypcjami nie jest obecnie obsługiwane.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Czy mogę przenieść klastry AKS z bieżącej subskrypcji platformy Azure do innej? 

Przeniesienie klastra AKS i skojarzonych z nim zasobów między subskrypcjami platformy Azure nie jest obsługiwane.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Dlaczego mój klaster usuwanie trwa długo? 

Większość klastrów jest usuwana na żądanie użytkownika; w niektórych przypadkach, zwłaszcza w przypadku, gdy klienci korzystają z własnej grupy zasobów lub że usuwanie zadań RG może zająć trochę czasu lub niepowodzeniem. Jeśli wystąpi problem z usuwaniem, należy sprawdzić, czy nie masz blokad w RG, że wszystkie zasoby spoza RG są nieskojarzone z RG itp.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Jeśli mam element/wdrożenia w stanie "NodeLost" lub "nieznany" Czy mogę uaktualnić klaster?

Można, ale AKS nie jest to zalecane. Uaktualnienia powinny być wykonywane, gdy stan klastra jest znany i w dobrej kondycji.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Czy w przypadku klastra z co najmniej jednym węzłem w stanie złej kondycji można przeprowadzić uaktualnienie?

Nie, Usuń/Usuń wszystkie węzły w stanie awarii lub w inny sposób usunięte z klastra przed uaktualnieniem.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Klaster został usunięty, ale zapoznaj się z błędem `[Errno 11001] getaddrinfo failed` 

Najczęściej jest to spowodowane tym, że użytkownicy, którzy mają co najmniej jedną sieciową grupę zabezpieczeń (sieciowych grup zabezpieczeń), są nadal używani i skojarzeni z klastrem.  Usuń je i spróbuj ponownie wykonać operację usuwania.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Uruchomiono uaktualnianie, ale teraz moje zasobniki są w pętli awarii, a sondy gotowości nie powiodły się?

Upewnij się, że nazwa główna usługi nie wygasła.  Zapoznaj się z tematem: [AKS nazwa główna usługi](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) i [AKS Aktualizuj poświadczenia](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Mój klaster działał, ale nagle nie może zainicjować obsługi LoadBalancers, instalacji obwodów PVC itp.? 

Upewnij się, że nazwa główna usługi nie wygasła.  Zapoznaj się z tematem: [AKS nazwa główna usługi](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) i [AKS Aktualizuj poświadczenia](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Czy można używać interfejsów API zestawu skalowania maszyn wirtualnych do skalowania ręcznie?

Nie, operacje skalowania przy użyciu interfejsów API zestawu skalowania maszyn wirtualnych nie są obsługiwane. Użyj interfejsów API AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Czy można używać zestawów skalowania maszyn wirtualnych do ręcznego skalowania do 0 węzłów?

Nie, operacje skalowania przy użyciu interfejsów API zestawu skalowania maszyn wirtualnych nie są obsługiwane.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Czy mogę zatrzymać lub cofnąć przydzielenie wszystkich moich maszyn wirtualnych?

Chociaż AKS ma mechanizmy odporności, aby wytrzymać takie konfiguracje i odzyskania z niego, nie jest to zalecana konfiguracja.

## <a name="can-i-use-custom-vm-extensions"></a>Czy mogę używać niestandardowych rozszerzeń maszyn wirtualnych?

Żaden AKS nie jest usługą zarządzaną i manipulowanie zasobami IaaS nie jest obsługiwane. Aby zainstalować składniki niestandardowe itd. Skorzystaj z interfejsów API i mechanizmów Kubernetes. Na przykład aby zainstalować wymagane składniki, Skorzystaj z DaemonSets.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
