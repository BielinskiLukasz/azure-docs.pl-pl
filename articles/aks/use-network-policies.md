---
title: Zabezpieczanie z użyciem zasad sieciowych w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć ruch przepływający do i z zasobników przy użyciu zasad sieciowych Kubernetes w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 1339fe66a4925104d459c0491caccdd7db5998a7
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114461"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Zabezpieczanie ruchu między różnymi sieciami przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)

W przypadku uruchamiania nowoczesnych aplikacji opartych na mikrousługach w programie Kubernetes często trzeba kontrolować, które składniki mogą komunikować się ze sobą. Zasada najniższych uprawnień powinna być stosowana do sposobu przepływu ruchu między jednostkami w klastrze usługi Azure Kubernetes Service (AKS). Załóżmy, że wiesz, że chcesz blokować ruch bezpośrednio do aplikacji zaplecza. Funkcja *zasad sieciowych* w programie Kubernetes umożliwia definiowanie reguł ruchu przychodzącego i wychodzącego między zasobnikami w klastrze.

W tym artykule opisano sposób instalowania aparatu zasad sieciowych i tworzenia zasad sieciowych Kubernetes w celu kontrolowania przepływu ruchu między nimi w AKS. Zasady sieciowe powinny być używane tylko dla węzłów i zasobników opartych na systemie Linux w AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

> [!TIP]
> W przypadku użycia funkcji zasad sieciowych w trakcie korzystania z wersji zapoznawczej zalecamy [utworzenie nowego klastra](#create-an-aks-cluster-and-enable-network-policy).
> 
> Jeśli chcesz kontynuować korzystanie z istniejących klastrów testowych, które używały zasad sieciowych podczas korzystania z wersji zapoznawczej, Uaktualnij klaster do nowej wersji usługi Kubernetes w najnowszej wersji, a następnie wdróż następujący manifest YAML, aby naprawić awarię serwera metryk i Kubernetes cyfrowego. Ta poprawka jest wymagana tylko w przypadku klastrów, które używały aparatu zasad sieciowych Calico.
>
> Zgodnie z najlepszymi rozwiązaniami w zakresie bezpieczeństwa, zapoznaj się z [zawartością tego manifestu YAML][calico-aks-cleanup] , aby zrozumieć, co jest wdrażane w klastrze AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Omówienie zasad sieciowych

Wszystkie zasobniki w klastrze AKS mogą domyślnie wysyłać i odbierać ruch bez ograniczeń. Aby zwiększyć bezpieczeństwo, można zdefiniować reguły sterujące przepływem ruchu. Aplikacje zaplecza często są widoczne tylko dla wymaganych usług frontonu, na przykład. Lub składniki bazy danych są dostępne tylko dla warstw aplikacji, które łączą się z nimi.

Zasady sieciowe to specyfikacja Kubernetes, która definiuje zasady dostępu do komunikacji między serwerami. Korzystając z zasad sieciowych, należy zdefiniować uporządkowany zestaw reguł do wysyłania i odbierania ruchu, a następnie zastosować je do kolekcji zestawów, które pasują do jednego lub kilku selektorów etykiet.

Te reguły zasad sieciowych są zdefiniowane jako manifesty YAML. Zasady sieciowe mogą być dołączane jako część szerszego manifestu, który tworzy również wdrożenie lub usługę.

### <a name="network-policy-options-in-aks"></a>Opcje zasad sieciowych w AKS

Platforma Azure zapewnia dwa sposoby implementacji zasad sieciowych. Podczas tworzenia klastra AKS wybierz opcję zasad sieciowych. Nie można zmienić opcji zasad po utworzeniu klastra:

* Własna implementacja platformy Azure, nazywana *zasadami sieci platformy Azure*.
* *Calico zasady sieci*, rozwiązanie zabezpieczeń sieci i sieci "open source" oparte na [Tiger][tigera]a.

Obie implementacje używają systemu Linux *dołączenie iptables* , aby wymusić określone zasady. Zasady są tłumaczone na zestawy dozwolonych i niedozwolonych par adresów IP. Te pary są następnie zaprogramowane jako reguły filtru IPTable.

Zasada sieciowa działa tylko z opcją Azure CNI (Advanced). Implementacja jest inna dla dwóch opcji:

* *Zasady sieci platformy Azure* — usługa Azure CNI konfiguruje mostek na hoście maszyny wirtualnej dla sieci wewnątrz węzła. Reguły filtrowania są stosowane, gdy pakiety są przekazywane przez mostek.
* *Zasady sieci Calico* — usługa Azure CNI konfiguruje lokalne trasy jądra dla ruchu wewnątrz węzła. Zasady są stosowane w interfejsie sieciowym pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Różnice między zasadami platformy Azure i Calico oraz ich możliwościami

| Możliwość                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Obsługiwane platformy                      | Linux                      | Linux                       |
| Obsługiwane opcje sieci             | Azure CNI                  | Azure CNI                   |
| Zgodność ze specyfikacją Kubernetes | Wszystkie typy zasad obsługiwane |  Wszystkie typy zasad obsługiwane |
| Dodatkowe funkcje                      | Brak                       | Rozszerzony model zasad składający się z globalnych zasad sieciowych, globalnego zestawu sieci i punktu końcowego hosta. Aby uzyskać więcej informacji na temat `calicoctl` korzystania z interfejsu wiersza polecenia do zarządzania tymi rozszerzonymi funkcjami, zobacz [calicoctl User Reference][calicoctl]. |
| Pomoc techniczna                                  | Obsługiwane przez zespół pomocy technicznej i inżynierów platformy Azure | Wsparcie społeczności Calico. Aby uzyskać więcej informacji na temat dodatkowej płatnej pomocy technicznej, zobacz [Opcje pomocy technicznej dla programu Project Calico][calico-support]. |
| Rejestrowanie                                  | Reguły dodane/usunięte w dołączenie iptables są rejestrowane na każdym hoście w obszarze */var/log/Azure-npm.log* | Aby uzyskać więcej informacji, zobacz [dzienniki składników Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Tworzenie klastra AKS i Włączanie zasad sieciowych

Aby wyświetlić zasady sieciowe w działaniu, Utwórz i rozwiń zasady, które definiują przepływ ruchu:

* Odrzuć cały ruch do pod.
* Zezwalaj na ruch na podstawie etykiet pod.
* Zezwalaj na ruch na podstawie przestrzeni nazw.

Najpierw Utwórzmy klaster AKS, który obsługuje zasady sieciowe. Funkcja zasad sieciowych można włączyć tylko podczas tworzenia klastra. Nie można włączyć zasad sieciowych w istniejącym klastrze AKS.

Aby używać zasad sieciowych z klastrem AKS, należy użyć [wtyczki Azure CNI][azure-cni] i zdefiniować własną sieć wirtualną i podsieci. Aby uzyskać szczegółowe informacje na temat planowania wymaganych zakresów podsieci, zobacz [Konfigurowanie zaawansowanej sieci][use-advanced-networking].

Następujący przykładowy skrypt:

* Tworzy sieć wirtualną i podsieć.
* Tworzy jednostkę usługi Azure Active Directory (Azure AD) do użycia z klastrem AKS.
* Przypisuje uprawnienia współautora dla jednostki usługi klastra AKS w sieci wirtualnej.
* Tworzy klaster AKS w zdefiniowanej sieci wirtualnej i włącza zasady sieciowe.
    * Opcja zasad sieciowych *platformy Azure* jest używana. Aby zamiast tego użyć Calico jako opcji zasad sieciowych, należy użyć `--network-policy calico` parametru.

Podaj własne bezpieczne *SP_PASSWORD*. Można zastąpić zmienne *RESOURCE_GROUP_NAME* i *CLUSTER_NAME* :

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Utworzenie klastra trwa kilka minut. Gdy klaster jest gotowy, skonfiguruj `kubectl` , aby nawiązać połączenie z klastrem Kubernetes za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] . To polecenie umożliwia pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes do ich użycia:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odrzuć cały ruch przychodzący do pod

Przed zdefiniowaniem reguł zezwalających na określony ruch sieciowy należy najpierw utworzyć zasady sieciowe, aby odmówić całego ruchu. Te zasady umożliwiają rozpoczęcie dozwolonych tylko żądanego ruchu sieciowego. Możesz również jasno zobaczyć, że ruch zostanie porzucony podczas stosowania zasad sieciowych.

W przypadku przykładowych zasad dotyczących środowiska aplikacji i ruchu Utwórzmy najpierw przestrzeń nazw o nazwie *programowanie* , aby uruchomić przykładowe zasobniki:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Utwórz przykładowy przykład zaplecza z systemem NGINX. Tego zaplecza można użyć, aby symulować przykładową aplikację opartą na sieci Web zaplecza. Utwórz ten obszar w przestrzeni nazw *Development* i otwórz port *80* w celu obsługi ruchu w sieci Web. Oznacz element pod za pomocą *aplikacji App = webapp, role = zaplecza* , tak aby można było określić go przy użyciu zasad sieciowych w następnej sekcji:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Utwórz kolejną stronę pod i Dołącz sesję terminalową, aby sprawdzić, czy można pomyślnie uzyskać dostęp do domyślnej strony sieci Web NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki Użyj `wget` , aby potwierdzić, że możesz uzyskać dostęp do domyślnej strony sieci Web Nginx:

```console
wget -qO- http://backend
```

Następujące przykładowe dane wyjściowe pokazują, że zwracana jest domyślna strona sieci Web NGINX:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Tworzenie i stosowanie zasad sieciowych

Po potwierdzeniu, że możesz użyć podstawowej strony sieci Web NGINX na przykładowej zaplecza, Utwórz zasady sieciowe, aby odmówić całego ruchu. Utwórz plik o nazwie `backend-policy.yaml` i wklej następujący manifest YAML. Ten manifest używa *podSelector* do dołączania zasad do zasobników, które mają etykietę *App: webapp, role: zaplecza* , taką jak przykład Nginx. W ramach ruchu przychodzącegonie są zdefiniowane żadne reguły, dlatego cały ruch przychodzący do niego jest odrzucany:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Zastosuj zasady sieci przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testowanie zasad sieciowych


Sprawdźmy, czy można ponownie użyć strony sieci Web NGINX na zapleczu. Utwórz inny test pod i Dołącz sesję terminala:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki Użyj `wget` , aby zobaczyć, czy można uzyskać dostęp do domyślnej strony sieci Web Nginx. Tym razem ustaw wartość limitu czasu na *2* sekundy. Zasady sieciowe blokują teraz cały ruch przychodzący, więc nie można załadować strony, jak pokazano w następującym przykładzie:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Zezwalaj na ruch przychodzący na podstawie etykiety pod

W poprzedniej sekcji zaplanowano NGINX zaplecza, a zasady sieciowe zostały utworzone w celu odblokowania całego ruchu. Utwórzmy fronton pod i zaktualizuj zasady sieciowe, aby umożliwić ruch z zasobników frontonu.

Aktualizowanie zasad sieciowych w celu zezwalania na ruch z aplikacji w postaci etykiet *: webapp, role: fronton* i w dowolnej przestrzeni nazw. Edytuj poprzedni plik *zaplecza-Policy. YAML* i Dodaj reguły transferu *matchLabels* , aby manifest wyglądał następująco:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Ta zasada sieciowa używa elementu *namespaceSelector* i *podSelector* dla reguły transferu danych przychodzących. Składnia YAML jest ważna dla reguł transferu danych przychodzących. W tym przykładzie oba elementy muszą być zgodne dla reguły transferu danych przychodzących. Wersje Kubernetes wcześniejsze niż *1,12* mogą nie interpretować tych elementów poprawnie i ograniczać ruch sieciowy zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [zachowanie elementów i selektorów][policy-rules].

Zastosuj zaktualizowane zasady sieci przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Zaplanuj pod nazwą *App = webapp, role = fronton* i Dołącz sesję terminalową:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki Użyj `wget` , aby zobaczyć, czy można uzyskać dostęp do domyślnej strony sieci Web Nginx:

```console
wget -qO- http://backend
```

Ponieważ reguła ruchu przychodzącego zezwala na ruch z punktami końcowymi, które mają aplikację Labels *: webapp, role: fronton*, ruch z frontonu poniżej jest dozwolony. Następujące przykładowe dane wyjściowe przedstawiają domyślną stronę sieci Web NGINX:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminala. Wartość pod jest automatycznie usuwana.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Przetestuj element pod bez pasującej etykiety

Zasady sieciowe zezwalają na ruch z aplikacji z nazwami oznaczonymi etykietą *App: webapp, role: fronton*, ale powinny odrzucać cały ruch. Przetestujmy, aby sprawdzić, czy inny pod bez etykiet nie może uzyskać dostępu do NGINX zaplecza pod. Utwórz inny test pod i Dołącz sesję terminala:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki Użyj `wget` , aby zobaczyć, czy można uzyskać dostęp do domyślnej strony sieci Web Nginx. Zasady sieciowe blokują ruch przychodzący, więc nie można załadować strony, jak pokazano w następującym przykładzie:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Zezwalaj na ruch tylko z wewnątrz zdefiniowanej przestrzeni nazw

W poprzednich przykładach zostały utworzone zasady sieci, które odrzuciły cały ruch, a następnie Zaktualizowano zasady w celu zezwolenia na ruch z programu z określoną etykietą. Innym często spotykanym zapotrzebowaniem jest ograniczanie ruchu tylko w obrębie danego obszaru nazw. Jeśli poprzednie przykłady dotyczyły ruchu w przestrzeni nazw *deweloperskiej* , Utwórz zasady sieci, które uniemożliwiają ruch z innej przestrzeni nazw, takiej jak *produkcja*, od osiągnięcia do nich zasobników.

Najpierw utwórz nową przestrzeń nazw, aby symulować produkcyjną przestrzeń nazw:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Zaplanuj test pod w przestrzeni nazw produkcyjny, który jest oznaczony jako *App = webapp, role = fronton*. Dołącz sesję terminalową:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

W wierszu polecenia powłoki Użyj `wget` , aby potwierdzić, że możesz uzyskać dostęp do domyślnej strony sieci Web Nginx:

```console
wget -qO- http://backend.development
```

Ponieważ etykiety pod pod kątem zgodności są obecnie dozwolone w zasadach sieciowych, ruch jest dozwolony. Zasady sieciowe nie sprawdzają się w przestrzeniach nazw, tylko w etykietach pod. Następujące przykładowe dane wyjściowe przedstawiają domyślną stronę sieci Web NGINX:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizowanie zasad sieciowych

Zaktualizujmy sekcję *namespaceSelector* reguły transferu danych przychodzących, aby zezwalać tylko na ruch z przestrzeni nazw deweloperskich. Edytuj plik manifestu *zaplecza-Policy. YAML* , jak pokazano w następującym przykładzie:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Bardziej skomplikowane przykłady umożliwiają zdefiniowanie wielu reguł dotyczących ruchu przychodzącego, takich jak *namespaceSelector* , a następnie *podSelector*.

Zastosuj zaktualizowane zasady sieci przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testowanie zaktualizowanych zasad sieciowych

Zaplanuj inny element pod w przestrzeni nazw produkcyjnej i Dołącz sesję terminala:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

W wierszu polecenia powłoki Użyj `wget` , aby zobaczyć, że zasady sieciowe odrzucają teraz ruch:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Wyjdź z testu pod:

```console
exit
```

Mając odmowę ruchu z przestrzeni nazw produkcyjnej, Zaplanuj test na odwrocie w przestrzeni nazw *deweloperskiej* i Dołącz sesję terminala:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

W wierszu polecenia powłoki Użyj `wget` , aby zobaczyć, że zasady sieciowe zezwalają na ruch:

```console
wget -qO- http://backend
```

Ruch jest dozwolony, ponieważ jest zaplanowany w przestrzeni nazw, który jest zgodny z dozwolonymi zasadami sieciowymi. Następujące przykładowe dane wyjściowe przedstawiają domyślną stronę sieci Web NGINX:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Wyjdź z dołączonej sesji terminala. Test pod zostanie automatycznie usunięty.

```console
exit
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule utworzyliśmy dwie przestrzenie nazw i zastosowano zasady sieci. Aby wyczyścić te zasoby, użyj polecenia [polecenia kubectl Delete][kubectl-delete] i określ nazwy zasobów:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o zasobach sieciowych, zobacz [pojęcia dotyczące sieci dla aplikacji w usłudze Azure Kubernetes Service (AKS)][concepts-network].

Aby dowiedzieć się więcej na temat zasad, zobacz [Kubernetes Network policies][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.6/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support
[calico-logs]: https://docs.projectcalico.org/v3.6/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
