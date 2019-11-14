---
title: Rozwiązania Stackify retrace rozszerzenie agenta systemu Linux platformy Azure
description: Wdróż agenta ponownego śledzenia rozwiązania Stackify na maszynie wirtualnej z systemem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 5914947bd994ee405f253e34c3dd919dd6561898
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073638"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Rozwiązania Stackify retrace rozszerzenie agenta systemu Linux

## <a name="overview"></a>Omówienie

Rozwiązania Stackify zawiera produkty, które śledzą szczegółowe informacje o aplikacji, aby ułatwić szybkie znajdowanie i rozwiązywanie problemów. W przypadku zespołów deweloperów, retrace to w pełni zintegrowana, wielośrodowiskowa, niewydajna wydajność aplikacji. Łączy on kilka narzędzi, których potrzebuje każdy zespół programistyczny.

Retrace to JEDYNe narzędzie, które zapewnia wszystkie poniższe możliwości we wszystkich środowiskach na jednej platformie.

* Zarządzanie wydajnością aplikacji (APM)
* Rejestrowanie aplikacji i serwera
* Śledzenie i monitorowanie błędów
* Serwer, aplikacja i metryki niestandardowe

**Informacje o rozszerzeniu agenta rozwiązania Stackify Linux**

To rozszerzenie zapewnia ścieżkę instalacji agenta systemu Linux do ponownego śledzenia. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny 

Agenta ponownego śledzenia można uruchomić w odniesieniu do tych dystrybucji systemu Linux.

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS, 16,10 i 17,04 |
| Debian | 7,9 + i 8.2 +, 9 |
| Red Hat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie agenta rozwiązania Stackify dla systemu Linux wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

Może być konieczne dostosowanie konfiguracji sieci w celu umożliwienia połączeń z usługą rozwiązania Stackify, zobacz https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Schemat rozszerzenia

---

Poniższy kod JSON przedstawia schemat rozszerzenia agenta retrace rozwiązania Stackify. Rozszerzenie wymaga `environment` i `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu 

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie rozwiązania Stackify retrace agenta systemu Linux podczas wdrażania szablonu Azure Resource Manager.  

KOD JSON rozszerzenia maszyny wirtualnej może być zagnieżdżony w obrębie zasobu maszyny wirtualnej lub umieszczony na głównym lub najwyższym poziomie szablonu JSON Menedżer zasobów. Położenie pliku JSON wpływa na wartość nazwy zasobu i typu. Aby uzyskać więcej informacji, zobacz Ustawianie nazwy i typu dla zasobów podrzędnych.

W poniższym przykładzie przyjęto założenie, że rozszerzenie rozwiązania Stackify retrace systemu Linux jest zagnieżdżone w ramach zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia kod JSON jest umieszczany w obiekcie "Resources": [] maszyny wirtualnej.

Rozszerzenie wymaga `environment` i `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Podczas umieszczania rozszerzenia JSON w katalogu głównym szablonu, nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla zagnieżdżonych.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>Wdrażanie programu PowerShell

Za pomocą polecenia `Set-AzVMExtension` można wdrożyć rozszerzenie maszyny wirtualnej agenta systemu Linux rozwiązania Stackify retrace do istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia należy zapisać konfigurację publiczną i prywatną w tabeli skrótów programu PowerShell.

Rozszerzenie wymaga `environment` i `activationKey`.

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure 

Narzędzia interfejsu wiersza polecenia platformy Azure można użyć do wdrożenia rozszerzenia maszyny wirtualnej rozwiązania Stackify retrace agenta systemu Linux na istniejącej maszynie wirtualnej.  

Rozszerzenie wymaga `environment` i `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="error-codes"></a>Kody błędów

| Kod błędu | Znaczenie | Możliwa akcja |
| :---: | --- | --- |
| 10 | Błąd instalacji | wget jest wymagany |
| 20 | Błąd instalacji | wymagany jest język Python |
| 30 | Błąd instalacji | sudo jest wymagany |
| 40 | Błąd instalacji | activationKey jest wymagany |
| 51 | Błąd instalacji | Dystrybucji systemu operacyjnego nie jest obsługiwana |
| 60 | Błąd instalacji | środowisko jest wymagane |
| 70 | Błąd instalacji | Nieznane |
| 80 | Włącz błąd | Instalacja usługi nie powiodła się |
| 90 | Włącz błąd | Uruchamianie usługi nie powiodło się |
| 100 | Wyłącz błąd | Zatrzymanie usługi nie powiodło się |
| 110 | Wyłącz błąd | Usuwanie usługi nie powiodło się |
| 120 | Błąd dezinstalacji | Zatrzymanie usługi nie powiodło się |

Jeśli potrzebujesz więcej pomocy, możesz skontaktować się z pomocą techniczną rozwiązania Stackify w https://support.stackify.com.