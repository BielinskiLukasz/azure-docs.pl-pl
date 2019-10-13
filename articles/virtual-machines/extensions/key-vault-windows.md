---
title: Azure Key Vault rozszerzenie maszyny wirtualnej dla systemu Windows | Microsoft Docs
description: Wdróż agenta wykonującego automatyczne odświeżanie Key Vault wpisów tajnych na maszynach wirtualnych przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
author: msmbaldwin
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 09/23/2018
ms.author: mbaldwin
ms.openlocfilehash: 53daa634374c10d48c42f5985459db7e068f293d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301895"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Key Vault rozszerzenie maszyny wirtualnej dla systemu Windows

Rozszerzenie maszyny wirtualnej Key Vault umożliwia automatyczne odświeżanie certyfikatów przechowywanych w magazynie kluczy platformy Azure. W związku z tym rozszerzenie monitoruje listę obserwowanych certyfikatów przechowywanych w magazynach kluczy, a po wykryciu zmiany pobiera i instaluje odpowiednie certyfikaty. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej Key Vault dla systemu Windows. 

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny wirtualnej Key Vault obsługuje poniższe wersje systemu Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia maszyny wirtualnej Key Vault. Rozszerzenie nie wymaga ustawień chronionych — wszystkie jego ustawienia są uznawane za informacje publiczne. Rozszerzenie wymaga listy monitorowanych certyfikatów, częstotliwości sondowania i docelowego magazynu certyfikatów. W szczególności:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "requireInitialSync": <initial synchronization of certificates e..g: true>,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```

> [!NOTE]
> Adresy URL obserwowanych certyfikatów powinny mieć postać `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Wynika to z faktu, że ścieżka `/secrets` zwraca pełny certyfikat, w tym klucz prywatny, podczas gdy ścieżka `/certificates` nie jest. Więcej informacji o certyfikatach można znaleźć tutaj: [Key Vault Certificates](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| dawc | Microsoft. Azure. z magazynem kluczy. EDP | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | string |
| linkOnRenewal | false | wartość logiczna |
| certificateStoreLocation  | LocalMachine | string |
| requiredInitialSync | true | wartość logiczna |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | Tablica ciągów


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Szablony są idealne do wdrożenia co najmniej jednej maszyny wirtualnej, która wymaga odświeżenia certyfikatów po wdrożeniu. Rozszerzenie można wdrożyć na poszczególnych maszynach wirtualnych lub w zestawach skalowania maszyn wirtualnych. Schemat i konfiguracja są wspólne dla obu typów szablonów. 

Konfiguracja JSON rozszerzenia maszyny wirtualnej musi być zagnieżdżona w ramach fragmentu zasobów maszyny wirtualnej szablonu, w odniesieniu do `"resources": []` obiektu szablonu maszyny wirtualnej i w przypadku zestawu skalowania maszyn wirtualnych w ramach obiektu `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Wdrożenie Azure PowerShell

Azure PowerShell można użyć do wdrożenia rozszerzenia maszyny wirtualnej Key Vault do istniejącej maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. 

* Aby wdrożyć rozszerzenie na maszynie wirtualnej:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyn wirtualnych:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure można użyć do wdrożenia rozszerzenia maszyny wirtualnej Key Vault na istniejącej maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych. 
 
* Aby wdrożyć rozszerzenie na maszynie wirtualnej:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyn wirtualnych:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Należy pamiętać o następujących ograniczeniach/wymaganiach:
- Ograniczenia Key Vault:
    - Musi istnieć w czasie wdrożenia 
    - Zasady dostępu Key Vault są ustawione dla tożsamości VM/VMSS przy użyciu pliku MSI


## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z Azure Portal i przy użyciu Azure PowerShell. Aby wyświetlić stan wdrożenia dla danej maszyny wirtualnej, uruchom następujące polecenie przy użyciu Azure PowerShell.

## <a name="azure-powershell"></a>Program Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).
