---
title: Zaktualizować klastra usługi Azure Service Fabric, aby użyć nazwy pospolitej certyfikatu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przełączyć klaster usługi Service Fabric z za pomocą odcisków palca certyfikatu do przy użyciu nazwy pospolitej certyfikatu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/01/2019
ms.author: ryanwi
ms.openlocfilehash: d42f586b066d17487a17baddeec3a551bfd65617
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55493632"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Zmień klaster z odcisk palca certyfikatu na nazwę pospolitą
Nie dwóch certyfikatów może mieć ten sam odcisk palca, który sprawia, że Przerzucanie certyfikatów klastra lub zarządzania trudne. Wiele certyfikatów, mogą jednak mieć tę samą nazwę pospolitą lub temat.  Przełączanie wdrożonego klastra z za pomocą odcisków palca certyfikatu za pomocą nazw pospolitych certyfikatów sprawia, że zarządzanie certyfikatami znacznie prostsze. W tym artykule opisano sposób aktualizacji działającego klastra usługi Service Fabric do użycia nazwy pospolitej certyfikatu zamiast odcisk palca certyfikatu.

>[!NOTE]
> Jeśli masz dwa odcisk palca zadeklarowany w szablonie, należy wykonać dwa wdrożenia.  Pierwsze wdrożenie odbywa się przed wykonaniem kroków opisanych w tym artykule.  Ustawia pierwszym wdrożeniu usługi **odcisk palca** właściwości w szablonie, aby certyfikat jest używany i usuwa **thumbprintSecondary** właściwości.  Dla drugiego wdrożenia postępuj zgodnie z instrukcjami w tym artykule.
 
## <a name="get-a-certificate"></a>Uzyskaj certyfikat
Najpierw należy uzyskać certyfikat z [certyfikatu urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority).  Nazwa pospolita certyfikatu powinna być nazwą hosta klastra.  Na przykład "myclustername.southcentralus.cloudapp.azure.com".  

Do celów testowych można pobrać certyfikatu podpisanego przez urząd certyfikacji od urzędu certyfikacji bezpłatnej lub otwarte.

> [!NOTE]
> Certyfikaty z podpisem własnym, włącznie z wygenerowanymi w przypadku wdrażania klastra usługi Service Fabric w witrynie Azure portal nie są obsługiwane.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Przekaż certyfikat i zainstaluj go w zestawie skalowania
Na platformie Azure klaster usługi Service Fabric jest wdrażany w zestawie skalowania maszyn wirtualnych.  Przekaż certyfikat do magazynu kluczy, a następnie zainstaluj go w zestawie skalowania maszyn wirtualnych, działającego na klastrze.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Oblicza hasła zestawu skali maszyny wirtualnej nie obsługują ten sam identyfikator zasobu dla dwóch osobnych wpisów tajnych oraz ich każdego wpisu tajnego jest wersjonowany unikatowy zasób. 

## <a name="download-and-update-the-template-from-the-portal"></a>Pobieranie i aktualizowanie szablonu z portalu
Certyfikat został zainstalowany w podstawowym zestawie skalowania, ale należy również zaktualizować klastra usługi Service Fabric, aby użyć tego certyfikatu i nazwy pospolitej.  Pobierz szablon do wdrożenia klastra.  Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do grupy zasobów klastra hostingu.  W **ustawienia**, wybierz opcję **wdrożeń**.  Wybierz najbardziej aktualne wdrożenie, a następnie kliknij przycisk **Wyświetl szablon**.

![Wyświetlanie szablonów][image1]

Pobierz szablon i parametry pliki w formacie JSON na komputerze lokalnym.

Najpierw otwórz plik parametrów w edytorze tekstów i dodaj następującą wartość parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Następnie otwórz plik szablonu w edytorze tekstów i trzy aktualizacje do obsługi nazwy pospolitej certyfikatu.

1. W **parametry** Dodaj *certificateCommonName* parametru:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Należy również rozważyć usunięcie *certificateThumbprint*, może nie będą już potrzebne.

2. W **Microsoft.Compute/virtualMachineScaleSets** zasób, zaktualizuj rozszerzenie maszyny wirtualnej do użycia nazwy pospolitej w ustawieniach certyfikatu zamiast odcisku palca.  W **virtualMachineProfile**->**extenstionProfile**->**rozszerzenia**->**właściwości** -> **ustawienia**->**certyfikatu**, Dodaj `"commonNames": ["[parameters('certificateCommonName')]"],` i Usuń `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  W **Microsoft.ServiceFabric/clusters** zasobu wersji aktualizacji interfejsu API "2018-02-01".  Również dodać **certificateCommonNames** ustawienie z **commonNames** właściwości i Usuń **certyfikatu** ustawienie (za pomocą właściwości odcisk palca), w następującej przykład:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Wdrożyć zaktualizowany szablon
Po wprowadzeniu zmian, należy ponownie wdrożyć zaktualizowany szablon.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* Dowiedz się, jak [Przerzucanie certyfikatów klastra](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizowanie i zarządzanie certyfikatami klastra](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
