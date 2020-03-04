---
title: Często zadawane pytania dotyczące Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: 285fdb7892d2da40dd50e025cb1dd7644ec17ae0
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255730"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Często zadawane pytania dotyczące Azure Dev Spaces

Dotyczy to często zadawanych pytań dotyczących Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Które regiony platformy Azure są obecnie dostępne Azure Dev Spaces?

Aby uzyskać pełną listę dostępnych regionów, zobacz sekcję [Obsługiwane regiony][supported-regions] .

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Czy mogę używać Azure Dev Spaces z istniejącymi wykresami wieloetapowe dockerfile lub Helm?

Tak, jeśli projekt zawiera już wykres pliku dockerfile lub Helm, można użyć tych plików z Azure Dev Spaces. Po uruchomieniu `azds prep`należy użyć parametru `--chart` i określić lokalizację wykresu. Azure Dev Spaces nadal generuje plik *azds. YAML* i *pliku dockerfile. opracowywać* , ale nie zastąpi ani nie zmodyfikuje istniejącego wykresu pliku dockerfile lub Helm. Może być konieczne zmodyfikowanie plików *azds. YAML* i *pliku dockerfile. opracowywać* , aby wszystkie elementy działały prawidłowo z istniejącą aplikacją podczas uruchamiania `azds up`.

Korzystając z własnego wykresu pliku dockerfile lub Helm, istnieją następujące ograniczenia:
* Jeśli używany jest tylko jeden pliku dockerfile, musi on zawierać wszystko, co jest potrzebne do włączenia scenariuszy programistycznych, takich jak zestaw SDK języka nie tylko środowisko uruchomieniowe. W przypadku używania oddzielnego pliku dockerfile dla Azure Dev Spaces, takich jak pliku dockerfile. Development, wszystko, czego potrzebujesz do włączenia scenariuszy deweloperskich, musi być zawarte w tym pliku dockerfile.
* Wykres Helm musi obsługiwać przekazywanie części lub całego znacznika obrazu jako wartość z *wartości. YAML*.
* Jeśli modyfikujesz wszystko za pomocą transferu danych przychodzących, możesz również zaktualizować wykres Helm, aby korzystał z rozwiązania transferu danych przychodzących dostarczonego przez Azure Dev Spaces.
* Jeśli chcesz użyć [możliwości routingu zapewnianych przez Azure dev Spaces][dev-spaces-routing], wszystkie usługi dla poszczególnych projektów muszą pasować do jednej przestrzeni nazw Kubernetes i muszą zostać wdrożone przy użyciu prostego nazewnictwa, na przykład *Service-a*. W standardowych wykresach Helm tę aktualizację nazewnictwa można wykonać, określając wartość właściwości *fullnameOverride* .

Aby porównać własny wykres pliku dockerfile lub Helm z istniejącą wersją, która działa z Azure Dev Spaces, Przejrzyj pliki wygenerowane w [przewodniku szybki start][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Czy mogę modyfikować pliki wygenerowane przez Azure Dev Spaces?

Tak, można zmodyfikować *azds. YAML* plik, pliku dockerfile i Helm [wygenerowany przez Azure dev Spaces podczas przygotowywania projektu][dev-spaces-prep]. Modyfikowanie tych plików zmienia sposób kompilowania i uruchamiania projektu.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Czy mogę używać Azure Dev Spaces bez publicznego adresu IP?

Nie, nie można zainicjować obsługi administracyjnej Azure Dev Spaces w klastrze AKS bez publicznego adresu IP. Publiczny adres IP jest [wymagany przez Azure dev Spaces do routingu][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Czy mogę użyć własnych danych przychodzących z Azure Dev Spaces?

Tak, możesz skonfigurować własne przychodzące po stronie przychodzące Azure Dev Spaces tworzenia. Na przykład można użyć [traefik][ingress-traefik] lub [Nginx][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Czy mogę używać protokołu HTTPS z Azure Dev Spaces?

Tak, możesz skonfigurować własny ruch przychodzący przy użyciu protokołu HTTPS za pomocą [traefik][ingress-https-traefik] lub [Nginx][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Czy mogę używać Azure Dev Spaces w klastrze korzystającym z CNI zamiast korzystającą wtyczki kubenet? 

Tak, można użyć Azure Dev Spaces w klastrze AKS, który używa CNI do obsługi sieci. Na przykład można użyć Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows][windows-containers], które używają CNI do obsługi sieci. Więcej informacji na temat korzystania z CNI dla sieci z Azure Dev Spaces jest dostępna [tutaj](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Czy mogę używać Azure Dev Spaces z kontenerami systemu Windows?

Obecnie Azure Dev Spaces ma być uruchamiany tylko w przypadku systemów Linux i w węzłach, ale można uruchomić Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Czy można używać Azure Dev Spaces w klastrach AKS z włączonymi zakresami adresów IP autoryzowanych serwerów interfejsu API?

Tak, można użyć Azure Dev Spaces w klastrach AKS z włączonymi [zakresami adresów IP autoryzowanych przez serwer interfejsu API][aks-auth-range] . Więcej informacji na temat korzystania z klastrów AKS z dozwolonymi zakresami adresów IP serwera interfejsu API w Azure Dev Spaces jest dostępny [tutaj](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Czy można używać Azure Dev Spaces w klastrach AKS z ograniczonym ruchem wychodzącym dla węzłów klastra?

Tak, można użyć Azure Dev Spaces w klastrach AKS z [ograniczonym ruchem wychodzącym dla węzłów klastra][aks-restrict-egress-traffic] włączonych po dodaniu prawidłowych nazw FQDN. Więcej informacji na temat używania klastrów AKS z ograniczonym ruchem wychodzącym dla węzłów klastra z włączonym Azure Dev Spaces jest dostępny [tutaj](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Czy mogę używać Azure Dev Spaces w przypadku klastrów AKS z włączoną funkcją RBAC?

Tak, można użyć Azure Dev Spaces w klastrach AKS z włączoną funkcją RBAC lub bez niej.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Co się stanie po włączeniu ruchu przychodzącego dla projektu w programie Visual Studio?

W przypadku korzystania z programu Visual Studio w celu przygotowania projektu można włączyć obsługę ruchu przychodzącego dla usługi. Włączenie transferu danych przychodzących powoduje utworzenie publicznego punktu końcowego w celu uzyskania dostępu do usługi w ramach klastra AKS, który jest opcjonalny. Jeśli nie włączysz transferu danych przychodzących, usługa będzie dostępna tylko z poziomu klastra AKS.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md