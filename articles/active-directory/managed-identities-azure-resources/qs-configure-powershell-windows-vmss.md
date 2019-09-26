---
title: Jak skonfigurować tożsamości zarządzane dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu programu PowerShell
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przez system i użytkownika w zestawie skalowania maszyn wirtualnych przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa3100cae9b1a2c9ca320776cc357f3720b3473
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309996"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure w zestawach skalowania maszyn wirtualnych przy użyciu programu PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule, korzystając z programu PowerShell, dowiesz się, jak wykonywać zarządzane tożsamości dla operacji zasobów platformy Azure na zestawie skalowania maszyn wirtualnych:
- Włączanie i wyłączanie zarządzanej tożsamości przypisanej przez system w zestawie skalowania maszyn wirtualnych
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i zarządzaną tożsamością użytkownika](overview.md#how-does-it-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, Twoje konto musi mieć następujące przypisania kontroli dostępu oparte na rolach na platformie Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) w celu utworzenia zestawu skalowania maszyn wirtualnych oraz włączenia i usunięcia zarządzanej tożsamości przypisanej przez system i/lub użytkownika z zestawu skalowania maszyn wirtualnych.
    - Rola [współautor tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - Rola [operatora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.
- Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) Jeśli jeszcze go. 

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i usunąć tożsamość zarządzaną przypisaną przez system przy użyciu Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Aby utworzyć zestaw skalowania maszyn wirtualnych z włączoną tożsamością zarządzaną przypisaną przez system:

1. Zapoznaj się z *przykładem 1* w artykule dotyczącym polecenia cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) , aby utworzyć zestaw skalowania maszyn wirtualnych z tożsamością zarządzaną przypisaną przez system.  Dodaj parametr `-IdentityType SystemAssigned` `New-AzVmssConfig` do polecenia cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure

Jeśli musisz włączyć tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure `Connect-AzAccount`przy użyciu. Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych. Upewnij się również, że konto należy do roli zapewniającej uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, na przykład "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzAccount
   ```

2. Najpierw pobierz właściwości zestawu skalowania maszyn wirtualnych przy użyciu [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) polecenia cmdlet. Następnie w celu włączenia zarządzanej tożsamości przypisanej do systemu Użyj `-IdentityType` przełącznika w poleceniu cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) :

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłącz tożsamość zarządzaną przypisaną przez system z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie potrzebuje już tożsamości zarządzanej przypisanej do systemu, ale nadal wymaga tożsamości zarządzanej przez użytkownika, użyj następującego polecenia cmdlet:

1. Zaloguj się do platformy Azure `Connect-AzAccount`przy użyciu. Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną. Upewnij się również, że konto należy do roli zapewniającej uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, na przykład "Współautor maszyny wirtualnej":

2. Uruchom następujące polecenie cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga już tożsamości zarządzanej przypisanej do systemu i nie ma tożsamości zarządzanych przypisanych przez użytkownika, użyj następujących poleceń:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika z zestawu skalowania maszyn wirtualnych przy użyciu Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Tworzenie nowego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisanej przez użytkownika nie jest obecnie obsługiwane przez program PowerShell. Zapoznaj się z następną sekcją dodawania tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych. Wracaj tutaj, aby zapoznać się z aktualizacjami.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Zaloguj się do platformy Azure `Connect-AzAccount`przy użyciu. Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych. Upewnij się również, że konto należy do roli zapewniającej uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych, na przykład "Współautor maszyny wirtualnej":

   ```powershell
   Connect-AzAccount
   ```

2. Najpierw pobierz właściwości zestawu skalowania maszyn wirtualnych przy użyciu `Get-AzVM` polecenia cmdlet. Następnie do przypisywania tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyn wirtualnych `-IdentityType` Użyj `-IdentityID` przełącznika i w poleceniu cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) . Zamień `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` ,,zwłasnymiwartościami.`USER ASSIGNED ID2` `<USER ASSIGNED ID1>`

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli zestaw skalowania maszyn wirtualnych ma wiele tożsamości zarządzanych przez użytkownika, można usunąć wszystkie oprócz ostatniego z nich przy użyciu następujących poleceń. Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>` Jest właściwością nazwa tożsamości zarządzanej przypisanej przez użytkownika, która powinna pozostać w zestawie skalowania maszyn wirtualnych. Te informacje można znaleźć w sekcji tożsamość zestawu skalowania maszyn wirtualnych przy użyciu `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Jeśli zestaw skalowania maszyn wirtualnych nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Jeśli zestaw skalowania maszyn wirtualnych ma zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika, można usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika, przełączając do korzystania tylko z tożsamości zarządzanej przypisanej do systemu.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanych tożsamości dla zasobów platformy Azure — omówienie](overview.md)
- Aby zapoznać się z pełnymi przewodnikami szybki start tworzenia maszyn wirtualnych platformy Azure, zobacz:
  
  - [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu programu PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















