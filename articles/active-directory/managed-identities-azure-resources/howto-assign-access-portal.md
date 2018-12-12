---
title: Jak przypisać z tożsamości zarządzanej dostępu do zasobów platformy Azure przy użyciu witryny Azure portal
description: Szczegółowe instrukcje dotyczące przypisywania tożsamości zarządzanej na jednym dostępu do zasobów do innego zasobu za pomocą witryny Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: e50a7b0aa80bff36a67ea52514d6b85099bfdf8c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081442"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Przypisać z tożsamości zarządzanej dostępu do tego zasobu za pomocą witryny Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure za pomocą tożsamości zarządzanej, można zapewnić dostęp tożsamość zarządzaną, do innego zasobu, podobnie jak podmiot zabezpieczeń. W tym artykule pokazano, jak przyznać maszynie wirtualnej platformy Azure lub dostęp tożsamości zarządzanej zestaw skalowania maszyn wirtualnych do konta usługi Azure storage przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu tożsamości zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości zarządzanej w obrębie zasobu platformy Azure, takich jak [maszyny Wirtualnej platformy Azure](qs-configure-portal-windows-vm.md) lub [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, w którym skonfigurowano tożsamość zarządzaną.

2. Przejdź do żądanego zasobu, na którym chcesz zmodyfikować kontroli dostępu. W tym przykładzie udostępniamy możliwość dostępu do maszyny wirtualnej platformy Azure na koncie magazynu, dzięki czemu możemy przejdź do konta magazynu.

3. Wybierz **kontrola dostępu (IAM)** strony zasobu i wybierz **+ Dodaj przypisanie roli**. Następnie określ **roli**, **Przypisz dostęp do**, a następnie określ odpowiedni **subskrypcji**. W obszarze kryteria wyszukiwania powinna być widoczna zasobu. Wybierz zasób, a następnie wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu (IAM) kontroli dostępu](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Kolejne kroki

- [Tożsamość zarządzaną dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć tożsamość zarządzana na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md).
- Aby włączyć tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej zestawu skalowania przy użyciu witryny Azure portal](qs-configure-portal-windows-vmss.md).


