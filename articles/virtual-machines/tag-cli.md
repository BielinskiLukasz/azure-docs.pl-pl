---
title: Jak oznaczyć maszynę wirtualną platformy Azure przy użyciu interfejsu wiersza polecenia
description: Dowiedz się więcej na temat tagowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595024"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Jak oznaczyć maszynę wirtualną przy użyciu interfejsu wiersza polecenia

W tym artykule opisano, jak oznaczyć maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure. Tagi to zdefiniowane przez użytkownika pary klucz/wartość, które mogą być umieszczone bezpośrednio w ramach zasobu lub grupy zasobów. Platforma Azure obsługuje obecnie do 50 tagów na zasób i grupę zasobów. Tagi mogą być umieszczane na zasobie w momencie tworzenia lub dodawane do istniejącego zasobu. Można także oznaczyć maszynę wirtualną za pomocą [programu Azure PowerShell](tag-powershell.md).


Można wyświetlić wszystkie właściwości danej maszyny wirtualnej, w tym Tagi, przy użyciu `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Aby dodać nowy tag maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure, można użyć `azure vm update` poleceniu wraz z parametrem tagu `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Aby usunąć Tagi, można użyć `--remove` parametru w `azure vm update` poleceniu.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Teraz, po zastosowaniu tagów do naszych zasobów interfejsu wiersza polecenia platformy Azure i portalu, zapoznaj się z informacjami na temat użycia, aby zobaczyć Tagi w portalu rozliczeń.


**Następne kroki**

- Aby dowiedzieć się więcej o znakowaniu zasobów platformy Azure, zobacz [Azure Resource Manager omówienie](../azure-resource-manager/management/overview.md) i [Używanie tagów do organizowania zasobów platformy Azure](../azure-resource-manager/management/tag-resources.md).
- Aby dowiedzieć się, jak tagi mogą ułatwić zarządzanie użyciem zasobów platformy Azure, zobacz [Opis rachunku na korzystanie z platformy Azure](../cost-management-billing/understand/review-individual-bill.md) i [Uzyskiwanie szczegółowych informacji o zużyciu zasobów Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
