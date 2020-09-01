---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7b98d3c1febd68a7ee73cf3064f4d8e108ea81fa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084888"
---
Przed wdrożeniem maszyn wirtualnych na urządzeniu z systemem Azure Stack Edge musisz skonfigurować klienta programu tak, aby łączył się z urządzeniem za pośrednictwem Azure Resource Manager przez Azure PowerShell. Aby uzyskać szczegółowe instrukcje, przejdź do pozycji [Połącz z Azure Resource Manager na urządzeniu brzegowym Azure Stack](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Upewnij się, że w celu uzyskania dostępu do urządzenia z poziomu klienta można użyć następujących kroków (Ta konfiguracja została wykonana podczas nawiązywania połączenia z usługą Azure Resource Manager, sprawdzasz, czy konfiguracja zakończyła się pomyślnie). 

1. Sprawdź, czy Azure Resource Manager komunikacja działa. Wpisz:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>:30005/
    ```

1. Wywoływanie interfejsów API urządzeń lokalnych w celu uwierzytelnienia. Wpisz: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Podaj nazwę użytkownika- *EdgeARMuser* i hasło, aby połączyć się za pośrednictwem Azure Resource Manager.

1. W przypadku skonfigurowania **obliczeń** dla Kubernetes można pominąć ten krok. Upewnij się, że włączono interfejs sieciowy dla obliczeń. W lokalnym interfejsie użytkownika przejdź do pozycji ustawienia **obliczeń** . Wybierz interfejs sieciowy, który zostanie użyty do utworzenia przełącznika wirtualnego. Utworzone maszyny wirtualne zostaną podłączone do przełącznika wirtualnego dołączonego do tego portu i skojarzonej sieci. Upewnij się, że wybrano sieć zgodną ze statycznym adresem IP, który będzie używany przez maszynę wirtualną.  

    ![Włącz ustawienia obliczeń 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Włącz obliczenia w interfejsie sieciowym. Azure Stack Edge spowoduje utworzenie przełącznika wirtualnego i zarządzanie nim za pomocą tego interfejsu sieciowego. W tej chwili nie należy wprowadzać określonych adresów IP dla usługi Kubernetes. Włączenie obliczeń może potrwać kilka minut.

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

