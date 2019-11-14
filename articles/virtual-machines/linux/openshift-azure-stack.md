---
title: Wdróż OpenShift w Azure Stack
description: Wdróż OpenShift w Azure Stack.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: d6c73b8cd33aa85793a2ce839410065e03b97be7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035544"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Wdrażanie platformy kontenera OpenShift lub OKD w Azure Stack

OpenShift można wdrożyć w Azure Stack. Istnieją pewne kluczowe różnice między platformą Azure a Azure Stack, więc wdrożenie będzie się różnić nieco, a możliwości również różnią się nieco.

Obecnie dostawca chmury platformy Azure nie działa w Azure Stack. Z tego powodu nie będzie można korzystać z dołączania dysku do magazynu trwałego w Azure Stack. Zamiast tego można skonfigurować inne opcje magazynu, takie jak NFS, iSCSI, GlusterFS itp. Alternatywnie można włączyć CNS i używać GlusterFS na potrzeby trwałego magazynu. Jeśli jest włączona funkcja CNS, trzy dodatkowe węzły zostaną wdrożone z dodatkowym magazynem na potrzeby użycia GlusterFS.

Można użyć jednej z kilku metod wdrażania OpenShift kontenera platform lub OKD w Azure Stack:

- Niezbędne składniki infrastruktury platformy Azure można wdrożyć ręcznie, a następnie skorzystać z [dokumentacji platformy kontenera OpenShift](https://docs.openshift.com/container-platform) lub [dokumentacji OKD](https://docs.okd.io).
- Można również użyć istniejącego [szablonu Menedżer zasobów](https://github.com/Microsoft/openshift-container-platform/) , który upraszcza wdrażanie klastra platformy kontenerów OpenShift.
- Można również użyć istniejącego [szablonu Menedżer zasobów](https://github.com/Microsoft/openshift-origin) , który upraszcza Wdrożenie klastra OKD.

Jeśli używasz szablonu Menedżer zasobów, wybierz odpowiednią gałąź (azurestack-Release-3. x). Szablony platformy Azure nie będą działały, ponieważ wersje interfejsu API różnią się między platformą Azure i Azure Stack. Odwołanie do obrazu RHEL jest obecnie trwale kodowane jako zmienna w pliku azuredeploy. JSON i należy je zmienić w celu dopasowania do obrazu.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

W przypadku wszystkich opcji wymagana jest subskrypcja Red Hat. Podczas wdrażania wystąpienie Red Hat Enterprise Linux jest zarejestrowane w ramach subskrypcji Red Hat i dołączone do identyfikatora puli zawierającego uprawnienia dla platformy kontenera OpenShift.
Upewnij się, że masz prawidłową nazwę użytkownika, hasło i Identyfikator puli usługi Red Hat Subscription Manager (RHSM). Alternatywnie możesz użyć klucza aktywacji, identyfikatora organizacji i identyfikatora puli.  Możesz sprawdzić te informacje, logując się do https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Wymagania wstępne Azure Stack

Do środowiska Azure Stack należy dodać obraz RHEL (OpenShift Container platform) lub CentOS (OKD), aby wdrożyć klaster OpenShift. Aby dodać te obrazy, skontaktuj się z administratorem Azure Stack. Instrukcje można znaleźć tutaj:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Wdrażanie przy użyciu OpenShift kontenera lub szablonu Menedżer zasobów OKD

Aby wdrożyć program przy użyciu szablonu Menedżer zasobów, należy użyć pliku parametrów, aby podać parametry wejściowe. Aby dodatkowo dostosować wdrożenie, rozwidlenie repozytorium GitHub i zmiana odpowiednich elementów.

Niektóre typowe opcje dostosowania obejmują, ale nie są ograniczone do:

- Rozmiar maszyny wirtualnej bastionu (zmienna w pliku azuredeploy. JSON)
- Konwencje nazewnictwa (zmienne w azuredeploy. JSON)
- OpenShift specyficzne dla klastra, zmodyfikowane za pomocą pliku hosts (deployOpenShift.sh)
- Odwołanie do obrazu RHEL (zmienna w azuredeploy. JSON)

Aby zapoznać się z instrukcjami wdrażania przy użyciu interfejsu wiersza polecenia platformy Azure, postępuj zgodnie z odpowiednią sekcją w sekcji [OpenShift Container platform](./openshift-container-platform-3x.md) lub [OKD](./openshift-okd.md) .

## <a name="next-steps"></a>Następne kroki

- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift na platformie Azure](./openshift-container-platform-3x-troubleshooting.md)