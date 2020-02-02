---
title: Wprowadzenie do usługi Azure Kubernetes Service
description: Poznaj funkcje i korzyści usługi Azure Kubernetes Service ułatwiające wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi na platformie Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: c98e67d174c3c8b7fc426e1dd2c5ebafd6760969
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932605"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Usługa Azure Service Kubernetes (AKS) ułatwia wdrażanie zarządzanego klastra Kubernetes na platformie Azure. Usługa AKS zmniejsza złożoność i nakłady operacyjne związane z zarządzaniem rozwiązaniem Kubernetes, przenosząc znaczną część tej odpowiedzialności na platformę Azure. Jako hostowana usługa Kubernetes, platforma Azure obsługuje krytyczne zadania, takie jak monitorowanie kondycji i konserwacja. Wzorce rozwiązania Kubernetes są zarządzane przez platformę Azure. Zarządzasz tylko węzłami agentów i obsługujesz je. Jako zarządzana usługa Kubernetes usługa AKS jest bezpłatna — płacisz tylko za węzły agentów w swoich klastrach, a nie za wzorce.

Klaster usługi AKS możesz utworzyć w witrynie Azure Portal, przy użyciu wiersza polecenia platformy Azure lub opcji wdrożenia opartych na szablonach, takich jak szablony usługi Resource Manager i programu Terraform. Podczas wdrażania klastra usługi AKS wzorzec Kubernetes i wszystkie węzły są wdrażane i konfigurowane automatycznie. Dodatkowe funkcje, takie jak zaawansowana łączność sieciowa, integracja z usługą Azure Active Directory i monitorowanie, także można skonfigurować podczas procesu wdrażania. Obsługa kontenerów systemu Windows Server jest obecnie dostępna w wersji zapoznawczej w AKS.

Aby uzyskać więcej informacji na temat Kubernetes, zobacz [podstawowe pojęcia dotyczące Kubernetes dla AKS][concepts-clusters-workloads].

Aby rozpocząć pracę, Wypełnij AKS przewodnika Szybki Start [w Azure Portal][aks-portal] lub [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Dostęp, zabezpieczenia i monitorowanie

Aby zapewnić lepsze zabezpieczenia i zarządzanie, usługa AKS umożliwia integrację z usługą Azure Active Directory i korzystanie z kontroli dostępu opartej na rolach platformy Kubernetes. Możesz także monitorować kondycję klastra i zasobów.

### <a name="identity-and-security-management"></a>Zarządzanie tożsamościami i zabezpieczeniami

Aby ograniczyć dostęp do zasobów klastra, AKS obsługuje funkcję [kontroli dostępu opartej na rolach (RBAC) Kubernetes][kubernetes-rbac]. Kontrola dostępu oparta na rolach umożliwia kontrolowanie dostępu do zasobów i przestrzeni nazw platformy Kubernetes, a także uprawnień do tych zasobów. Możesz też skonfigurować integrację klastra usługi AKS z usługą Azure Active Directory. Dzięki integracji z usługą Azure AD dostęp do platformy Kubernetes można skonfigurować w oparciu o istniejącą tożsamość i członkostwo w grupie. Istniejącym użytkownikom i grupom usługi Azure AD można zapewnić dostęp do zasobów usługi AKS i zintegrowane środowisko logowania.

Aby uzyskać więcej informacji na temat tożsamości, zobacz [Opcje dostępu i tożsamości dla AKS][concepts-identity].

Aby zabezpieczyć klastry AKS, zobacz [integrowanie Azure Active Directory z AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Zintegrowane rejestrowanie i monitorowanie

Aby dowiedzieć się, jak wydajnie działa Twój klaster usługi AKS i wdrożone aplikacje, usługa Azure Monitor dla kondycji kontenera zbiera metryki pamięci i procesora z kontenerów, węzłów i kontrolerów. Dzienniki kontenerów są dostępne i można również [przejrzeć dzienniki wzorców Kubernetes][aks-master-logs]. Te dane monitorowania są przechowywane w obszarze roboczym usługi Azure Log Analytics i są dostępne za pośrednictwem witryny Azure Portal, interfejsu wiersza polecenia platformy Azure lub punktu końcowego REST.

Aby uzyskać więcej informacji, zobacz [monitorowanie kondycji kontenera usługi Azure Kubernetes Service][container-health].

## <a name="clusters-and-nodes"></a>Klastry i węzły

Węzły usługi AKS są uruchamiane na maszynach wirtualnych platformy Azure. Możesz połączyć magazyn z węzłami i zasobnikami, uaktualnić składniki klastra oraz używać procesorów GPU. AKS obsługuje klastry Kubernetes z wieloma pulami węzłów w celu obsługi mieszanych systemów operacyjnych i kontenerów systemu Windows Server (obecnie w wersji zapoznawczej). W węzłach systemu Linux jest uruchamiany dostosowany obraz systemu operacyjnego Ubuntu, a w węzłach z systemem Windows Server jest uruchamiany dostosowany obraz systemu operacyjnego Windows Server 2019.

### <a name="cluster-node-and-pod-scaling"></a>Skalowanie węzłów klastra i zasobników

W miarę zmian zapotrzebowania na zasoby liczba węzłów klastra lub zasobników używanych do uruchamiania usług może być automatycznie skalowana w górę i w dół. Można używać funkcji Horizontal Pod Autoscaler i funkcji Cluster Autoscaler. To podejście do skalowania umożliwia automatyczne dostosowywanie klastra usługi AKS do zapotrzebowania i uruchamianie tylko potrzebnych zasobów.

Aby uzyskać więcej informacji, zobacz [skalowanie klastra usługi Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Uaktualnienia węzłów klastra

Usługa Azure Kubernetes Service oferuje wiele wersji platformy Kubernetes. Po udostępnieniu nowych wersji w usłudze AKS klaster można uaktualnić przy użyciu witryny Azure Portal lub interfejsu wiersza polecenia platformy Azure. Podczas procesu uaktualniania węzły są dokładnie odizolowywane i opróżniane, aby zminimalizować zakłócenia dla działających aplikacji.

Aby dowiedzieć się więcej o wersjach cykl życia, zobacz [obsługiwane wersje Kubernetes w AKS][aks-supported versions]. Aby zapoznać się z instrukcjami dotyczącymi sposobu uaktualniania, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Węzły obsługujące procesory GPU

Usługa AKS obsługuje tworzenie pul węzłów z obsługą procesorów GPU. Obecnie platforma Azure udostępnia maszyny wirtualne obsługujące jeden lub wielu procesorów GPU. Maszyny wirtualne obsługujące procesory GPU są zaprojektowane do przetwarzania obciążeń z dużą ilością obliczeń, dużą ilością grafiki i wizualizacji.

Aby uzyskać więcej informacji, zobacz [Korzystanie z procesorów GPU w systemie AKS][aks-gpu].

### <a name="storage-volume-support"></a>Obsługa woluminów magazynu

Na potrzeby obsługi obciążeń aplikacji możesz zainstalować woluminy magazynu do trwałego magazynowania danych. Mogą być używane woluminy statyczne i dynamiczne. W zależności od liczby połączonych zasobników współużytkujących magazyn możesz używać magazynu opartego na usłudze Azure Disks na potrzeby dostępu pojedynczego zasobnika lub usługi Azure Files na potrzeby dostępu wielu współbieżnych zasobników.

Aby uzyskać więcej informacji, zobacz [Opcje magazynu dla aplikacji w AKS][concepts-storage].

Rozpocznij pracę z dynamicznymi woluminami trwałymi przy użyciu [usługi Azure disks][azure-disk] lub [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Sieci wirtualne i ruch przychodzący

Klaster usługi AKS można wdrożyć w istniejącej sieci wirtualnej. W takiej konfiguracji każdy zasobnik w klastrze ma przypisany adres IP w sieci wirtualnej i może bezpośrednio komunikować się z innymi zasobnikami w klastrze oraz innymi węzłami w sieci wirtualnej. Zasobniki mogą też łączyć się z innymi usługami w równorzędnych sieciach wirtualnych i z sieciami lokalnymi za pośrednictwem usługi ExpressRoute lub połączeń sieci VPN typu lokacja-lokacja (S2S).

Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące sieci dla aplikacji w AKS][aks-networking].

Aby rozpocząć pracę z ruchem przychodzącym, zobacz [Routing aplikacji protokołu HTTP][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Ruch przychodzący z routingiem aplikacji protokołu HTTP

Dodatek Routing aplikacji protokołu HTTP ułatwia dostęp do aplikacji wdrożonych w klastrze usługi AKS. Po włączeniu konfiguruje ono kontroler ruchu przychodzącego w klastrze usługi AKS. Przy wdrażaniu aplikacji publicznie dostępne nazwy DNS są konfigurowane automatycznie. Routing aplikacji protokołu HTTP konfiguruje strefę DNS i integruje ją z klastrem usługi AKS. Następnie możesz wdrożyć zasoby ruchu przychodzącego platformy Kubernetes w zwykły sposób.

Aby rozpocząć pracę z ruchem przychodzącym, zobacz [Routing aplikacji protokołu HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integracja narzędzi deweloperskich

Platforma Kubernetes ma rozbudowany ekosystem narzędzi do programowania i zarządzania, takich jak Helm, Draft i rozszerzenie Kubernetes dla programu Visual Studio Code. Te narzędzia współpracują bezproblemowo z usługą AKS.

Dodatkowo usługa Azure Dev Spaces udostępnia środowisko do szybkiego, iteracyjnego programowania na platformie Kubernetes dla zespołów. Wykonując minimum czynności konfiguracyjnych, możesz uruchamiać i debugować kontenery bezpośrednio w usłudze AKS. Aby rozpocząć, zobacz [Azure dev Spaces][azure-dev-spaces].

Projekt DevOps platformy Azure udostępnia proste rozwiązanie do przeniesienia istniejącego kodu i repozytorium Git na platformę Azure. Projekt DevOps automatycznie tworzy zasoby platformy Azure, takie jak usługa AKS i potok wydania w usłudze Azure DevOps Services zawierający potok kompilacji dla ciągłej integracji, konfiguruje potok wydania dla ciągłego dostarczania, a następnie tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

Aby uzyskać więcej informacji, zobacz [projekt Azure DevOps][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Obsługa obrazów platformy Docker i prywatny rejestr kontenerów

Usługa AKS obsługuje format obrazu platformy Docker. Integracja usługi AKS z usługą Azure Container Registry (ACR) pozwala uzyskać prywatny magazyn obrazów platformy Docker.

Aby utworzyć prywatny magazyn obrazów, zobacz [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Certyfikat platformy Kubernetes

Usługa Azure Kubernetes Service (AKS) ma certyfikat CNCF zgodności z platformą Kubernetes.

## <a name="regulatory-compliance"></a>Zgodność z przepisami

Usługa Azure Kubernetes Service (AKS) jest zgodna z regulacjami SOC, ISO, PCI DSS i HIPAA. Aby uzyskać więcej informacji, zobacz [Omówienie zgodności Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wdrażania usługi AKS i zarządzania nią z przewodnika Szybki start dotyczącego interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [AKS — Szybki Start][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
