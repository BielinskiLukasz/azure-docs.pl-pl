---
title: Pojęcia — Storage w usłudze Azure Kubernetes Services (AKS)
description: Informacje o magazynie w usłudze Azure Kubernetes Service (AKS), w tym woluminy, woluminy trwałe, klasy magazynu i oświadczenia
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 601f89c4510899dbb1f5d8a238961d9a4e5864e0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913720"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opcje magazynu dla aplikacji w usłudze Azure Kubernetes Service (AKS)

Aplikacje działające w usłudze Azure Kubernetes Service (AKS) mogą wymagać przechowywania i pobierania danych. W przypadku niektórych obciążeń aplikacji ten magazyn danych może korzystać z lokalnego, szybkiego magazynu w węźle, który nie jest już wymagany po usunięciu z nich. Inne obciążenia aplikacji mogą wymagać magazynu, który utrzymuje więcej zwykłych woluminów danych w ramach platformy Azure. Może być konieczne udostępnienie wielu zasobników z tymi samymi woluminami danych lub ponowne dołączenie woluminów danych, jeśli na innym węźle zostanie ponownie zaplanowana. Na koniec może zajść potrzeba dodania poufnych danych lub informacji konfiguracyjnych aplikacji do zasobników.

![Opcje magazynu dla aplikacji w klastrze usługi Azure Kubernetes Services (AKS)](media/concepts-storage/aks-storage-options.png)

W tym artykule przedstawiono podstawowe koncepcje zapewniające magazyn dla aplikacji w AKS:

- [Objętości](#volumes)
- [Trwałe woluminy](#persistent-volumes)
- [Klasy magazynu](#storage-classes)
- [Trwałe woluminy — oświadczenia](#persistent-volume-claims)

## <a name="volumes"></a>Woluminy

Aplikacje często muszą mieć możliwość przechowywania i pobierania danych. Ponieważ Kubernetes zazwyczaj traktuje poszczególne z nich w postaci tymczasowych, jednorazowych zasobów, różne podejścia są dostępne dla aplikacji, które będą w razie potrzeby używać i utrwalać dane. *Wolumin* reprezentuje sposób przechowywania, pobierania i utrwalania danych w różnych magazynach i za pośrednictwem cyklu życia aplikacji.

Tradycyjne woluminy do przechowywania i pobierania danych są tworzone jako zasoby Kubernetes, które są obsługiwane przez usługę Azure Storage. Można ręcznie tworzyć te woluminy danych do przypisania do zasobników bezpośrednio lub Kubernetes je automatycznie. Te woluminy danych mogą korzystać z usługi Azure disks lub Azure Files:

- Za pomocą *usługi Azure disks* można utworzyć zasób Kubernetes *Datadisk* . Dyski mogą korzystać z usługi Azure Premium Storage, obsługiwanej przez dysków SSD o wysokiej wydajności lub magazynu w warstwie Standardowa platformy Azure, z obsługą regularnego HDD. W przypadku większości obciążeń produkcyjnych i programistycznych należy użyć usługi Premium Storage. Dyski platformy Azure są instalowane jako *ReadWriteOnce*, więc są dostępne tylko dla jednego pod. W przypadku woluminów magazynu, do których można uzyskać dostęp jednocześnie przez wiele zasobników, użyj Azure Files.
- *Azure Files* można użyć do zainstalowania udziału SMB 3,0 obsługiwanego przez konto usługi Azure Storage w ramach platformy. Pliki umożliwiają udostępnianie danych między wieloma węzłami i zasobnikami. Pliki mogą korzystać z usługi Azure Storage w warstwie Standardowa w ramach regularnego HDD lub Azure Premium Storage, które są obsługiwane przez dysków SSD o wysokiej wydajności.
> [!NOTE] 
> Azure Files obsługiwać usługę Premium Storage w klastrach AKS z systemem Kubernetes 1,13 lub nowszym.

W Kubernetes woluminy mogą reprezentować więcej niż tradycyjny dysk, na którym można przechowywać informacje i je pobierać. Woluminy Kubernetes mogą być również używane jako sposób wstrzykiwania danych do użycia przez kontenery. Wspólne dodatkowe typy woluminów w Kubernetes obejmują:

- *emptyDir* — ten wolumin jest często używany jako miejsce tymczasowe dla pod. Wszystkie kontenery w ramach programu mogą uzyskiwać dostęp do danych w woluminie. Dane zapisywane do tego typu woluminu są utrwalane tylko dla cykl życia w miejscu — gdy zostanie usunięty, wolumin zostanie usunięty. Ten wolumin zazwyczaj używa bazowego magazynu dysku lokalnego, chociaż może również istnieć tylko w pamięci węzła.
- *Secret* — ten wolumin służy do iniekcji danych poufnych do zasobników, takich jak hasła. Najpierw utwórz wpis tajny przy użyciu interfejsu API Kubernetes. Podczas definiowania użytkownika lub wdrożenia można żądać określonego klucza tajnego. Wpisy tajne są dostarczane tylko do węzłów, które mają zaplanowaną w tym, że wymagają, a wpis tajny jest przechowywany w *tmpfs*, a nie zapisywany na dysku. Po usunięciu ostatniego elementu na węźle, który wymaga wpisu tajnego, wpis tajny jest usuwany z tmpfs węzła. Wpisy tajne są przechowywane w danym obszarze nazw i można do nich uzyskiwać dostęp tylko w obrębie tego samego obszaru nazw.
- *configMap* — ten typ woluminu służy do iniekcji właściwości pary klucz-wartość do zasobników, takich jak informacje o konfiguracji aplikacji. Zamiast definiować informacje o konfiguracji aplikacji w ramach obrazu kontenera, można zdefiniować go jako zasób Kubernetes, który można łatwo aktualizować i stosować do nowych wystąpień w miarę ich wdrażania. Podobnie jak przy użyciu klucza tajnego, należy najpierw utworzyć ConfigMap za pomocą interfejsu API Kubernetes. Tego ConfigMap można następnie żądać podczas definiowania wdrożenia pod lub. ConfigMaps są przechowywane w obrębie danego obszaru nazw i można do nich uzyskać dostęp tylko w obrębie tego samego obszaru nazw.

## <a name="persistent-volumes"></a>Woluminy trwałe

Woluminy, które są zdefiniowane i tworzone w ramach cyklu życia, są dostępne tylko do momentu usunięcia elementu. W przypadku, gdy na innym hoście w ramach zdarzeń konserwacyjnych, w szczególności w StatefulSets. *Wolumin trwały* (PV) jest zasobem magazynu utworzonym i zarządzanym przez interfejs API Kubernetes, który może istnieć poza okresem istnienia poszczególnych elementów.

Do udostępnienia PersistentVolume są używane dyski lub pliki platformy Azure. Jak wskazano w poprzedniej sekcji woluminów, wybór dysków lub plików jest często określany na podstawie potrzeby jednoczesnego dostępu do danych lub warstwy wydajności.

![Woluminy trwałe w klastrze usługi Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volumes.png)

PersistentVolume może być *statycznie* tworzony przez administratora klastra lub tworzony *dynamicznie* przez serwer interfejsu API Kubernetes. W przypadku zaplanowania i żądania magazynu, który nie jest obecnie dostępny, Kubernetes może utworzyć podstawowy magazyn dyskowy lub plików platformy Azure i dołączyć go do usługi pod. Dynamiczna obsługa administracyjna używa *StorageClass* , aby określić, jakiego typu usługi Azure Storage należy utworzyć.

## <a name="storage-classes"></a>Klasy magazynu

Aby zdefiniować różne warstwy magazynu, takie jak Premium i Standard, można utworzyć *StorageClass*. StorageClass również definiuje *reclaimPolicy*. Ten reclaimPolicy steruje zachowaniem bazowego zasobu usługi Azure storage, gdy Zasobnik zostanie usunięty i trwały wolumin przestaną być potrzebne. Bazowego zasobu magazynu można usunąć lub przechowywane do użytku z programem przyszłych zasobników.

W programie AKS są tworzone dwa początkowe StorageClasses:

- *default* — używa usługi Azure Standard Storage do utworzenia dysku zarządzanego. Zasady odzyskiwania wskazują, że podstawowy dysk platformy Azure jest usuwany, gdy wolumin trwały, który go używał, został usunięty.
- *Managed-Premium* — używa usługi Azure Premium Storage do tworzenia dysku zarządzanego. Zasady odzyskiwania ponownie wskazują, że podstawowy dysk platformy Azure jest usuwany, gdy wolumin trwały, który go używał, zostanie usunięty.

Jeśli dla trwałego woluminu nie określono StorageClass, zostanie użyta wartość domyślna StorageClass. Należy zachować ostrożność podczas żądania woluminów trwałych, aby korzystały z odpowiedniego magazynu, którego potrzebujesz. Można utworzyć StorageClass do dodatkowych potrzeb przy użyciu `kubectl`. Poniższy przykład używa Managed Disks Premium i określa, że podstawowy dysk platformy Azure powinien zostać *zachowany* po usunięciu:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>Trwałe oświadczenia woluminu

PersistentVolumeClaim żąda dysku lub magazynu plików dla konkretnej StorageClass, trybu dostępu i rozmiaru. Serwer interfejsu API Kubernetes umożliwia dynamiczne Inicjowanie obsługi bazowego zasobu magazynu na platformie Azure, jeśli nie ma istniejącego zasobu do spełnienia tego żądania na podstawie zdefiniowanego StorageClassu. Definicja pod obejmuje instalację woluminu, gdy wolumin został połączony z pod.

![Trwały wolumin oświadczeń w klastrze usługi Kubernetes usługi Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

PersistentVolume jest *powiązany* z PersistentVolumeClaim po przypisaniu dostępnego zasobu magazynu do żądania. Istnieje 1:1 mapowanie woluminów trwałych na oświadczenia.

Poniższy przykład manifestu YAML przedstawia trwałe żądanie woluminu, które korzysta z StorageClass *Managed-Premium* i żąda *5Gi* dysku:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

W przypadku tworzenia definicji na podstawie trwałego żądania woluminu jest określona w celu zażądanie żądanego magazynu. Następnie należy określić *volumeMount* dla aplikacji, aby odczytywać i zapisywać dane. Poniższy przykład manifestu YAML pokazuje, jak poprzednie trwałego żądania woluminu można użyć do zainstalowania woluminu w */mnt/Azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Następne kroki

W przypadku skojarzonych najlepszych rozwiązań zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w AKS][operator-best-practices-storage].

Aby dowiedzieć się, jak tworzyć dynamiczne i statyczne woluminy korzystające z dysków lub Azure Files platformy Azure, zobacz następujące artykuły:

- [Tworzenie woluminu statycznego przy użyciu dysków platformy Azure][aks-static-disks]
- [Tworzenie woluminu statycznego przy użyciu Azure Files][aks-static-files]
- [Tworzenie woluminu dynamicznego przy użyciu dysków platformy Azure][aks-dynamic-disks]
- [Tworzenie woluminu dynamicznego przy użyciu Azure Files][aks-dynamic-files]

Aby uzyskać dodatkowe informacje na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

- [Kubernetes/AKS klastrów i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes/AKS tożsamość][aks-concepts-identity]
- [Zabezpieczenia Kubernetes/AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes/AKS][aks-concepts-network]
- [Skala Kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
