---
title: Tworzenie klastra usługi Azure Service Fabric przy użyciu nazwy pospolitej certyfikatu | Microsoft Docs
description: Dowiedz się, jak utworzyć klaster Service Fabric przy użyciu nazwy pospolitej certyfikatu z szablonu.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: atsenthi
ms.openlocfilehash: 49c733c475f401b0e8c9329e2e5d7b463175f81a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599745"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Wdróż klaster Service Fabric, który używa nazwy pospolitej certyfikatu zamiast odcisku palca
Dwa certyfikaty nie mogą mieć tego samego odcisku palca, co utrudnia zarzucanie certyfikatów klastra lub zarządzanie nimi. Jednak wiele certyfikatów może mieć taką samą nazwę pospolitą lub podmiot.  Zarządzanie certyfikatami w klastrze przy użyciu nazw pospolitych certyfikatów znacznie prostsze. W tym artykule opisano, jak wdrożyć klaster Service Fabric, aby używał nazwy pospolitej certyfikatu zamiast odcisku palca certyfikatu.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Pobierz certyfikat
Najpierw Pobierz certyfikat z [urzędu certyfikacji (CA)](https://wikipedia.org/wiki/Certificate_authority).  Nazwa pospolita certyfikatu powinna być dla domeny niestandardowej, która jest posiadana, i zakupiona z rejestratora domen. Na przykład "azureservicefabricbestpractices.com"; osoby, które nie są pracownikami firmy Microsoft, nie mogą udostępniać certyfikatów dla domen MS, dlatego nie można używać nazw DNS LB ani Traffic Manager jako wspólnych nazw dla certyfikatu, [Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) a jeśli domena niestandardowa będzie resolvabl e na platformie Azure. Należy również zadeklarować domenę niestandardową jako "element ManagementEndpoint", jeśli chcesz, aby Portal odzwierciedlał niestandardowy alias domeny dla klastra.

Do celów testowych można uzyskać certyfikat podpisany przez urząd certyfikacji z bezpłatnego lub otwartego urzędu certyfikacji.

> [!NOTE]
> Certyfikaty z podpisem własnym, w tym wygenerowane podczas wdrażania klastra Service Fabric w Azure Portal, nie są obsługiwane. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Przekaż certyfikat do magazynu kluczy
Na platformie Azure klaster Service Fabric jest wdrażany w zestawie skalowania maszyn wirtualnych.  Przekaż certyfikat do magazynu kluczy.  Po wdrożeniu klastra jest on instalowany na zestawie skalowania maszyn wirtualnych, na którym działa klaster.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Pobieranie i aktualizowanie przykładowego szablonu
W tym artykule jest stosowany przykładowy szablon i szablon szablonu [bezpiecznego klastra z 5 węzłami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) . Pobierz pliki *azuredeploy. JSON* i *azuredeploy. Parameters. JSON* na komputer.

### <a name="update-parameters-file"></a>Aktualizuj plik parametrów
Najpierw Otwórz plik *azuredeploy. Parameters. JSON* w edytorze tekstów i Dodaj następującą wartość parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Następnie ustaw wartości parametrów *certificateCommonName*, *sourceVaultValue*i *certificateUrlValue* na zwracane przez poprzedni skrypt:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Aktualizowanie pliku szablonu
Następnie otwórz plik *azuredeploy. JSON* w edytorze tekstów i wprowadź trzy aktualizacje, aby obsługiwać wspólną nazwę certyfikatu.

1. W sekcji **Parametry** Dodaj parametr *certificateCommonName* :
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Rozważ również usunięcie *certificateThumbprint*, może to nie być już potrzebne.

2. Ustaw wartość zmiennej *sfrpApiVersion* na "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. W zasobie **Microsoft. COMPUTE/virtualMachineScaleSets** zaktualizuj rozszerzenie maszyny wirtualnej tak, aby używało nazwy pospolitej w ustawieniach certyfikatu zamiast odcisku palca.  We->->**właściwościach**rozszerzeń virtualMachineProfileextensionProfilesCertificates,Add->->-> 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    i Usuń `"thumbprint": "[parameters('certificateThumbprint')]",`.

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

4. W zasobów **Microsoft. servicefabric/klastrów** zaktualizuj wersję interfejsu API do wersji "2018-02-01".  Dodaj również ustawienie **certificateCommonNames** z właściwością **commonNames** i Usuń ustawienie **certyfikatu** (z właściwością odcisku palca), jak w poniższym przykładzie:
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
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > Pole "certificateIssuerThumbprint" pozwala określić oczekiwanych wystawców certyfikatów z daną wspólną nazwą podmiotu. To pole akceptuje Wyliczenie oddzielone przecinkami odcisków palców SHA1. Należy zauważyć, że jest to wzmocnienie weryfikacji certyfikatu — w przypadku, gdy wystawcy nie określono lub nie jest pusty, certyfikat zostanie zaakceptowany do uwierzytelnienia, jeśli jego łańcuch może zostać skompilowany, a następnie zakończony przez moduł walidacji jako zaufany. Jeśli wystawcy zostanie określony, certyfikat zostanie zaakceptowany, jeśli odcisk palca jego wystawcy bezpośredniego pasuje do którejkolwiek z wartości określonych w tym polu — niezależnie od tego, czy katalog główny jest zaufany, czy nie. Należy pamiętać, że infrastruktura PKI może używać różnych urzędów certyfikacji do wystawiania certyfikatów dla tego samego podmiotu i dlatego ważne jest, aby określić wszystkie oczekiwane odciski palców wystawcy dla danego tematu.
   >
   > Określenie wystawcy jest uznawane za najlepsze rozwiązanie; Pomijanie będzie nadal działać — w przypadku certyfikatów w łańcuchu do zaufanego katalogu głównego — to zachowanie ma ograniczenia i może zostać rozwiązane w najbliższej przyszłości. Należy również zauważyć, że klastry wdrożone na platformie Azure i zabezpieczone certyfikatami x509 wydanymi przez prywatną infrastrukturę PKI, które zostały zgłoszone przez podmiot, mogą nie być w stanie sprawdzić poprawności przez usługę Service Fabric platformy Azure (w przypadku komunikacji między klastrami), jeśli zasady certyfikatu infrastruktury kluczy publicznych nie jest wykrywalny, dostępny i dostępny. 

## <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon
Wdróż ponownie zaktualizowany szablon po wprowadzeniu zmian.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* Dowiedz się [, jak przerzucać certyfikat klastra](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizowanie certyfikatów klastra i zarządzanie nimi](service-fabric-cluster-security-update-certs-azure.md)
* Uproszczenie zarządzania certyfikatami przez [zmianę klastra z odcisku palca certyfikatu na nazwę pospolitą](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
