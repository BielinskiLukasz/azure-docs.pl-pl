---
title: Konfigurowanie zarządzanych tożsamości na maszynie wirtualnej platformy Azure przy użyciu usługi REST — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przez system i użytkownika na maszynie wirtualnej platformy Azure przy użyciu zawieszania do wykonywania wywołań interfejsu API REST.
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
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f975595e935a5c0254450168aa295e6e7366a94
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375589"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością systemu w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule, za pomocą zapełniania w celu wykonywania wywołań do punktu końcowego Azure Resource Manager REST, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie wirtualnej platformy Azure:

- Włączanie i wyłączanie zarządzanej tożsamości przypisanej do systemu na maszynie wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Jeśli używasz systemu Windows, zainstaluj [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub Użyj [Azure Cloud Shell](../../cloud-shell/overview.md) w Azure Portal.
- [Zainstaluj konsolę lokalną interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), jeśli używasz [podsystemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub [systemu operacyjnego dystrybucji Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Jeśli używasz lokalnej konsoli interfejsu wiersza polecenia platformy Azure, zaloguj się do platformy Azure `az login` przy użyciu konta, które jest skojarzone z subskrypcją platformy Azure, aby zarządzać tożsamościami zarządzanymi przez system lub użytkownika.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej platformy Azure przy użyciu zwinięcia, aby nawiązywać wywołania do punktu końcowego REST Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia maszyny wirtualnej platformy Azure

Aby można było utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną przez system, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla maszyny wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utwórz maszynę wirtualną przy użyciu ZWINIĘCIEa, aby wywołać punkt końcowy usługi REST Azure Resource Manager. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z tożsamością zarządzaną przypisaną przez system, jak określono w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}`. Zastąp `<ACCESS TOKEN>` wartością otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz wartości `<SUBSCRIPTION ID>`, jak jest to odpowiednie dla danego środowiska.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**
   
   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 
   
   **Treść żądania**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości przypisanej do systemu na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, która została pierwotnie zainicjowana bez niej, konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia, aby wywołać punkt końcowy Azure Resource Manager REST w celu włączenia zarządzanej tożsamości przypisanej do systemu na maszynie wirtualnej zgodnie z opisem w treści żądania przez `{"identity":{"type":"SystemAssigned"}` wartości dla maszyny wirtualnej o nazwie *myVM*.  Zastąp `<ACCESS TOKEN>` wartością otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz wartości `<SUBSCRIPTION ID>`, jak jest to odpowiednie dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do maszyny wirtualnej, należy listę zarządzanych tożsamości przypisanych przez użytkownika za pomocą tego polecenia ZWINIĘCIE: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Jeśli do maszyny wirtualnej są przypisane skojarzone tożsamości przypisane przez użytkownika, zgodnie z opisem w `identity` wartość w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamość zarządzaną przez użytkownika podczas włączania zarządzanej tożsamości przypisanej do systemu na maszynie wirtualnej.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 
   
   **Treść żądania**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej z istniejącymi tożsamościami zarządzanymi przypisanymi przez użytkownika, należy dodać `SystemAssigned` do wartości `type`.  
   
   Na przykład jeśli maszyna wirtualna ma zarządzane tożsamości przypisane przez użytkownika `ID1` i `ID2` przypisane do niej, a chcesz dodać tożsamość zarządzaną przypisaną przez system do maszyny wirtualnej, użyj następującego Wywołaj metodę. Zamień `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` na wartości odpowiednie dla danego środowiska.

   W wersji interfejsu API `2018-06-01` są przechowywane tożsamości zarządzane przypisane przez użytkownika w `userAssignedIdentities` wartość w formacie słownika, w przeciwieństwie do wartości `identityIds` w formacie tablicy używanym w wersji interfejsu API `2017-12-01`.
   
   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Zaktualizuj maszynę wirtualną za pomocą zwinięcia, aby wywołać punkt końcowy Azure Resource Manager REST w celu wyłączenia zarządzanej tożsamości przypisanej do systemu.  W poniższym przykładzie jest wyłączona tożsamość zarządzana przypisana przez system zgodnie z opisem w treści żądania przez wartość `{"identity":{"type":"None"}}` z maszyny wirtualnej o nazwie *myVM*.  Zastąp `<ACCESS TOKEN>` wartością otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz wartości `<SUBSCRIPTION ID>`, jak jest to odpowiednie dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do maszyny wirtualnej, należy listę zarządzanych tożsamości przypisanych przez użytkownika za pomocą tego polecenia ZWINIĘCIE: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Jeśli do maszyny wirtualnej są przypisane skojarzone tożsamości przypisane przez użytkownika, zgodnie z opisem w `identity` wartość w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamość zarządzaną przez użytkownika podczas wyłączania tożsamości zarządzanej przypisanej przez system na maszynie wirtualnej.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Aby usunąć tożsamość zarządzaną przypisaną przez system z maszyny wirtualnej, która ma tożsamości zarządzane przypisane przez użytkownika, Usuń `SystemAssigned` z `{"identity":{"type:" "}}` wartości, zachowując wartość `UserAssigned` oraz wartości słownika `userAssignedIdentities`, jeśli używasz **interfejsu API w wersji 2018-06-01**. Jeśli używasz **interfejsu API w wersji 2017-12-01** lub starszej, Zachowaj `identityIds` tablicę.

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika na maszynie wirtualnej platformy Azure przy użyciu zwinięcia, aby wykonać wywołania do punktu końcowego REST Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) [maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla maszyny wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu instrukcji dostępnych tutaj: [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Utwórz maszynę wirtualną przy użyciu ZWINIĘCIEa, aby wywołać punkt końcowy usługi REST Azure Resource Manager. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* *w grupie zasobów grupa zasobu z* `ID1`tożsamości zarządzanej przypisanej przez użytkownika, zgodnie z opisem w treści żądania przez `"identity":{"type":"UserAssigned"}`wartość. Zastąp `<ACCESS TOKEN>` wartością otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz wartości `<SUBSCRIPTION ID>`, jak jest to odpowiednie dla danego środowiska.
 
   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) [maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu instrukcji znalezionych w tym miejscu, [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Aby nie usuwać istniejących tożsamości zarządzanych użytkownika lub systemu przypisanych do maszyny wirtualnej, należy wyświetlić listę typów tożsamości przypisanych do maszyny wirtualnej za pomocą następującego polecenia ZWINIĘCIE. Jeśli zarządzane tożsamości są przypisane do zestawu skalowania maszyn wirtualnych, są one wymienione w obszarze w wartości `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.

    Jeśli masz przypisane tożsamości zarządzane przez użytkownika lub system do maszyny wirtualnej, zgodnie z opisem w `identity` wartość w odpowiedzi, przejdź do kroku 5, w którym pokazano, jak zachować tożsamość zarządzaną przypisaną przez system podczas dodawania tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej.

4. Jeśli nie masz żadnych tożsamości zarządzanych przypisanych przez użytkownika do maszyny wirtualnej, użyj następującego polecenia ZWINIĘCIE, aby wywołać punkt końcowy Azure Resource Manager REST w celu przypisania do maszyny wirtualnej pierwszej tożsamości zarządzanej przypisanej przez użytkownika.

   W poniższych przykładach przypisano tożsamość zarządzaną przypisaną przez użytkownika, `ID1` do maszyny wirtualnej o nazwie *myVM* *w grupie zasobów*.  Zastąp `<ACCESS TOKEN>` wartością otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu okaziciela oraz wartości `<SUBSCRIPTION ID>`, jak jest to odpowiednie dla danego środowiska.

   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        |
 
   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Jeśli masz istniejącą tożsamość zarządzaną przez użytkownika lub przypisanej do systemu przypisanej do maszyny wirtualnej:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj tożsamość zarządzaną przypisaną przez użytkownika do wartości słownika `userAssignedIdentities`.
    
   Jeśli na przykład masz tożsamość zarządzaną przypisaną przez system i tożsamość zarządzaną przez użytkownika `ID1` aktualnie przypisana do maszyny wirtualnej i chcesz dodać do niej tożsamość zarządzaną przez użytkownika, `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   Zachowaj zarządzane tożsamości przypisane przez użytkownika, które chcesz zachować w `identityIds` wartość tablicy podczas dodawania nowej tożsamości zarządzanej przypisanej przez użytkownika.

   Jeśli na przykład masz tożsamość zarządzaną przypisaną przez system i tożsamość zarządzaną przez użytkownika `ID1` aktualnie przypisana do maszyny wirtualnej i chcesz dodać do niej tożsamość zarządzaną przez użytkownika, `ID2`: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .

1. Pobierz token dostępu okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną przy użyciu zarządzanej tożsamości przypisanej do systemu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby upewnić się, że nie usunięto żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które mają być przypisane do maszyny wirtualnej lub nie można usunąć tożsamości zarządzanej przypisanej przez system, należy wyświetlić listę zarządzanych tożsamości za pomocą następującego polecenia ZWINIĘCIE: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.
 
   Jeśli masz zarządzane tożsamości przypisane do maszyny wirtualnej, są one wyświetlane w odpowiedzi w wartości `identity`.

   Na przykład, jeśli masz przypisane przez użytkownika tożsamości zarządzane `ID1` i `ID2` przypisane do maszyny wirtualnej i chcesz tylko nadal `ID1` przypisane i zachować tożsamość przypisaną przez system:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj `null` do tożsamości zarządzanej przypisanej przez użytkownika, którą chcesz usunąć:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   Przechowuj tylko tożsamości zarządzane przypisane przez użytkownika, które chcesz przechowywać w tablicy `identityIds`:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Jeśli maszyna wirtualna ma zarządzane tożsamości przypisane do systemu i przypisane przez użytkownika, możesz usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika, przełączając do używania tylko tożsamości zarządzanej przypisanej do systemu przy użyciu następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`. | 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Jeśli maszyna wirtualna ma tylko tożsamości zarządzane przypisane przez użytkownika, a chcesz je usunąć, użyj następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacja*     | Wymagany. Ustaw prawidłowy token dostępu `Bearer`.| 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat tworzenia, wyświetlania lub usuwania tożsamości zarządzanych przez użytkownika przy użyciu usługi REST, zobacz:

- [Utwórz, Wyświetl lub Usuń zarządzane tożsamości przypisane przez użytkownika przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)
