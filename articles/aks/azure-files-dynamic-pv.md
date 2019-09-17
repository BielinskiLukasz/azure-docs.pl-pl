---
title: Dynamiczne tworzenie woluminu plików dla wielu zasobników w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak dynamicznie tworzyć wolumin trwały z Azure Files do użycia z wieloma współbieżnymi zasobnikami w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 09/12/2019
ms.author: mlearned
ms.openlocfilehash: 045fcb3286c89097459a4a8405d22ee70e44c205
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018837"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamiczne tworzenie i używanie woluminu trwałego z Azure Files w usłudze Azure Kubernetes Service (AKS)

Wolumin trwały reprezentuje część magazynu, która została zainicjowana do użycia z Kubernetes. Wolumin trwały może być używany przez jeden lub wiele zasobników i może być dynamicznie lub statycznie inicjowany. Jeśli wiele z nich potrzebuje współbieżnego dostępu do tego samego woluminu magazynu, można użyć Azure Files, aby nawiązać połączenie przy użyciu [protokołu SMB (Server Message Block)][smb-overview]. W tym artykule pokazano, jak dynamicznie utworzyć udział Azure Files do użytku przez wiele zasobników w klastrze usługi Azure Kubernetes Service (AKS).

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz [Opcje magazynu dla aplikacji w AKS][concepts-storage].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Tworzenie klasy magazynu

Klasa magazynu służy do definiowania sposobu tworzenia udziału plików platformy Azure. Konto magazynu jest tworzone automatycznie w [grupie zasobów węzła][node-resource-group] do użytku z klasą magazynu w celu przechowywania udziałów plików platformy Azure. Wybierz następującą [nadmiarowość usługi Azure Storage][storage-skus] dla *skuName*:

* *Standard_LRS* — standardowa Magazyn lokalnie nadmiarowy (LRS)
* *Standard_GRS* — magazyn Geograficznie nadmiarowy (GRS)
* *Standard_RAGRS* — standardowy magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)
* Magazyn lokalnie nadmiarowy *Premium_LRS* -Premium (LRS)

> [!NOTE]
> Azure Files obsługiwać usługę Premium Storage w klastrach AKS z systemem Kubernetes 1,13 lub nowszym.

Aby uzyskać więcej informacji na temat klas magazynu Kubernetes dla Azure Files, zobacz [Kubernetes Storage Classes][kubernetes-storage-classes].

Utwórz plik o nazwie `azure-file-sc.yaml` i skopiuj w poniższym przykładowym manifeście. Aby uzyskać więcej informacji na temat *mountOptions*, zobacz sekcję [Opcje instalacji][mount-options] .

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Utwórz klasę magazynu za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Tworzenie roli klastra i powiązania

Klastry AKS używają kontroli dostępu opartej na rolach (RBAC) Kubernetes do ograniczania akcji, które mogą być wykonywane. *Role* definiują uprawnienia do udzielenia, a *powiązania* stosują je do wybranych użytkowników. Te przypisania można zastosować do danej przestrzeni nazw lub całego klastra. Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][kubernetes-rbac].

Aby zezwolić platformie Azure na tworzenie wymaganych zasobów magazynu, Utwórz *ClusterRole* i *ClusterRoleBinding*. Utwórz plik o nazwie `azure-pvc-roles.yaml` i skopiuj w następującym YAML:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Przypisz uprawnienia za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Tworzenie trwałego żądania woluminu

Trwałego żądania woluminu (PVC) używa obiektu klasy Storage do dynamicznego inicjowania obsługi udziału plików platformy Azure. Poniższe YAML można użyć do utworzenia trwałego zastrzeżenia woluminu o rozmiarze *5 GB* z dostępem do *ReadWriteMany* . Aby uzyskać więcej informacji o trybach dostępu, zobacz dokumentację [Kubernetes trwałych woluminów][access-modes] .

Teraz Utwórz plik o nazwie `azure-file-pvc.yaml` i skopiuj w następującym YAML. Upewnij się, że *storageClassName* jest zgodna z klasą magazynu utworzoną w ostatnim kroku:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> W przypadku używania jednostki SKU *Premium_LRS* dla klasy magazynu minimalna wartość *magazynu* musi być równa *100Gi*.

Utwórz wartość trwałego zastrzeżenia przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

Po zakończeniu zostanie utworzony udział plików. Tworzony jest również wpis tajny Kubernetes, który zawiera informacje o połączeniu i poświadczenia. Aby wyświetlić stan obwodu PVC, można użyć polecenia [polecenia kubectl Get][kubectl-get] :

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Użyj woluminu trwałego

Poniższy YAML tworzy element, który używa trwałego zastrzeżenia woluminu *azurefile* do zainstalowania udziału plików platformy Azure w ścieżce */mnt/Azure* . W przypadku kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w AKS) Określ *mountPath* przy użyciu konwencji ścieżki systemu Windows, takiej jak *'d: '* .

Utwórz plik o nazwie `azure-pvc-files.yaml`i skopiuj go do poniższego YAML. Upewnij się, że wartość claimname pasuje do obwodu PVC utworzonego w ostatnim kroku.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

Masz teraz działający udział w udziale Azure Files zainstalowanym w katalogu */mnt/Azure* . Ta konfiguracja może być widoczna podczas sprawdzania pod kątem użytkownika za `kubectl describe pod mypod`pośrednictwem. Następujące wąskie przykładowe dane wyjściowe pokazują wolumin zainstalowany w kontenerze:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Opcje instalacji

Domyślna wartość *parametru FileMode* i *dirMode* to *0755* dla Kubernetes w wersji 1.9.1 lub nowszej. W przypadku używania klastra z Kuberetes w wersji 1.8.5 lub nowszej i dynamicznego tworzenia woluminu trwałego za pomocą klasy magazynu opcje instalacji można określić w obiekcie klasy magazynu. W poniższym przykładzie są ustawiane *0777*:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

W przypadku korzystania z klastra w wersji 1.8.0-1.8.4 kontekstu zabezpieczeń można określić z wartością *runAsUser* ustawioną na *0*. Aby uzyskać więcej informacji na temat kontekstu zabezpieczeń pod, zobacz [Konfigurowanie kontekstu zabezpieczeń][kubernetes-security-context].

## <a name="next-steps"></a>Następne kroki

W przypadku skojarzonych najlepszych rozwiązań zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w AKS][operator-best-practices-storage].

Dowiedz się więcej na temat Kubernetes woluminów trwałych przy użyciu Azure Files.

> [!div class="nextstepaction"]
> [Wtyczka Kubernetes dla Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks