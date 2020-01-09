---
title: Konfigurowanie tożsamości zarządzanych w zestawie skalowania maszyn wirtualnych — interfejs wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przez system i użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
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
ms.openlocfilehash: 2832a8c584c0fbe707f22501809d772c6ffb970b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430087"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure:
- Włączanie i wyłączanie zarządzanej tożsamości przypisanej przez system w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, Twoje konto musi mieć następujące przypisania kontroli dostępu oparte na rolach na platformie Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) w celu utworzenia zestawu skalowania maszyn wirtualnych oraz włączenia i usunięcia tożsamości zarządzanej system i/lub przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych.
    - Rola [współautor tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - Rola [operatora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowsza), jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia. 
      
      > [!NOTE]
      > Polecenia zostały zaktualizowane w celu odzwierciedlenia najnowszej wersji [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przez system dla zestawu skalowania maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Aby utworzyć zestaw skalowania maszyn wirtualnych z włączoną tożsamością zarządzaną przypisaną przez system:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć zestaw skalowania maszyn wirtualnych:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) na potrzeby zawierania i wdrażania zestawu skalowania maszyn wirtualnych i powiązanych z nim zasobów przy użyciu polecenia [AZ Group Create](/cli/azure/group/#az-group-create). Możesz pominąć ten krok, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz zestaw skalowania maszyn wirtualnych za pomocą polecenia [AZ VMSS Create](/cli/azure/vmss/#az-vmss-create) . Poniższy przykład tworzy zestaw skalowania maszyn wirtualnych o nazwie *myVMSS* z tożsamością zarządzaną przypisaną przez system, zgodnie z żądaniem `--assign-identity` parametru. Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure

Jeśli konieczne jest włączenie tożsamości zarządzanej przypisanej przez system w istniejącym zestawie skalowania maszyn wirtualnych platformy Azure:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.

   ```azurecli-interactive
   az login
   ```

2. Użyj polecenia [AZ VMSS Identity Assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) , aby umożliwić zarządzanej tożsamości przypisanej przez system do istniejącej maszyny wirtualnej:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłącz tożsamość zarządzaną przypisaną przez system z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie potrzebuje już tożsamości zarządzanej przypisanej do systemu, ale nadal musisz mieć zarządzane tożsamości przypisane przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości zarządzanej przypisanej do systemu i nie ma żadnych tożsamości zarządzanych przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W `none` wartość jest uwzględniana wielkość liter. Musi być małymi literami. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>tożsamość zarządzana przypisana przez użytkownika

Ta sekcja zawiera informacje na temat włączania i usuwania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych

W tej sekcji omówiono tworzenie zestawu skalowania maszyn wirtualnych i przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyn wirtualnych. Jeśli masz już zestaw skalowania maszyn wirtualnych, którego chcesz użyć, Pomiń tę sekcję i przejdź do następnej.

1. Możesz pominąć ten krok, jeśli masz już grupę zasobów, której chcesz użyć. Utwórz [grupę zasobów](~/articles/azure-resource-manager/management/overview.md#terminology) na potrzeby zawierania i wdrażania tożsamości zarządzanej przypisanej przez użytkownika przy użyciu polecenia [AZ Group Create](/cli/azure/group/#az-group-create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Odpowiedź zawiera szczegółowe informacje o utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących. Wartość `id` zasobu przypisana do tożsamości zarządzanej przypisanej przez użytkownika jest używana w następnym kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Utwórz zestaw skalowania maszyn wirtualnych za pomocą polecenia [AZ VMSS Create](/cli/azure/vmss/#az-vmss-create). Poniższy przykład tworzy zestaw skalowania maszyn wirtualnych skojarzony z nową tożsamością zarządzaną przez użytkownika określoną przez parametr `--assign-identity`. Upewnij się, że parametry `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY>` zostały zastąpione własnymi wartościami. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych

1. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpowiedź zawiera szczegółowe informacje o utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Przypisz tożsamość zarządzaną przypisaną przez użytkownika do zestawu skalowania maszyn wirtualnych za pomocą polecenia [AZ VMSS Identity Assign](/cli/azure/vmss/identity). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY>` to właściwość `name` zasobu tożsamości przypisanej przez użytkownika, jak została utworzona w poprzednim kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika z zestawu skalowania maszyn wirtualnych, użyj [AZ VMSS Identity Remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Jeśli jest to jedyna tożsamość zarządzana przypisana przez użytkownika przypisana do zestawu skalowania maszyn wirtualnych, `UserAssigned` zostanie usunięta z wartości typu tożsamości.  Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY>` będzie mieć Właściwość `name` tożsamości zarządzanej przypisanej przez użytkownika, którą można znaleźć w sekcji tożsamość zestawu skalowania maszyn wirtualnych przy użyciu `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Jeśli zestaw skalowania maszyn wirtualnych nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W `none` wartość jest uwzględniana wielkość liter. Musi być małymi literami.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Jeśli zestaw skalowania maszyn wirtualnych ma zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika, można usunąć wszystkie tożsamości przypisane przez użytkownika, przełączając do korzystania tylko z tożsamości zarządzanej przypisanej do systemu. Użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanych tożsamości dla zasobów platformy Azure — omówienie](overview.md)
- Aby zapoznać się z pełnym przewodnikiem Szybki Start tworzenia zestawu skalowania maszyn wirtualnych platformy Azure, zobacz: 

  - [Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















