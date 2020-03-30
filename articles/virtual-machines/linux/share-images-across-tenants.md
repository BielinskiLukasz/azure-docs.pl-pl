---
title: Udostępnianie obrazów galerii wśród dzierżawców platformy Azure
description: Dowiedz się, jak udostępniać obrazy maszyn wirtualnych w dzierżawach platformy Azure przy użyciu galerii obrazów udostępnionych.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034970"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Udostępnianie obrazów maszyn wirtualnych galerii w dzierżawach platformy Azure

Udostępnione galerie obrazów umożliwiają udostępnianie obrazów za pomocą funkcji RBAC. Za pomocą funkcji RBAC można udostępniać obrazy w obrębie dzierżawy, a nawet osobom spoza dzierżawy. Aby uzyskać więcej informacji na temat tej prostej opcji udostępniania, zobacz [udostępnianie galerii](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Nie można użyć portalu do wdrożenia maszyny Wirtualnej z obrazu w innej dzierżawie platformy Azure. Aby utworzyć maszynę wirtualną na podstawie obrazu udostępnionego przez dzierżawców, należy użyć interfejsu wiersza polecenia platformy Azure lub [programu Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Tworzenie maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Zaloguj się do jednostki usługi dla dzierżawy 1 przy użyciu identyfikatora aplikacji, klucza aplikacji i identyfikatora dzierżawy 1. Można użyć, `az account show --query "tenantId"` aby uzyskać identyfikatory dzierżawy w razie potrzeby.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Zaloguj się jednostki usługi dla dzierżawy 2 przy użyciu appID, klucz aplikacji i identyfikator dzierżawy 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Utwórz maszynę wirtualną. Zastąp informacje w przykładzie własnymi.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Następne kroki

Jeśli napotkasz jakiekolwiek problemy, możesz [rozwiązać problem z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).