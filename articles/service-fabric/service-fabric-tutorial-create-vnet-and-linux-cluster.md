---
title: Tworzenie klastra usługi Service Fabric systemu Linux na platformie Azure | Microsoft Docs
description: Dowiedz się, jak wdrożyć klaster usługi Service Fabric systemu Linux w istniejącej sieci wirtualnej platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: bef2e5da1a151fd6178298f3b993337fd07bd294
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313335"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>wdrażanie klastra usługi Service Fabric systemu Linux w sieci wirtualnej platformy Azure

W tym artykule dowiesz się, jak wdrożyć klaster z systemem Linux usługi Service Fabric w [sieci wirtualnej platformy Azure (VNET)](../virtual-network/virtual-networks-overview.md) przy użyciu wiersza polecenia platformy Azure i szablonu. Po wykonaniu tych czynności powstanie działający w chmurze klaster, w którym można będzie wdrażać aplikacje. Aby utworzyć klaster systemu Windows za pomocą programu PowerShell, zobacz [Tworzenie bezpiecznego klastra systemu Windows na platformie Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj [interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md).
* Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* Aby uzyskać kluczowe pojęcia związane z klastrów, przeczytaj [klastrów omówienie platformy Azure](service-fabric-azure-clusters-overview.md)

Poniższe procedury tworzenia klastra usługi Service Fabric siedem. Aby obliczyć koszt działania klastra usługi Service Fabric na platformie Azure, skorzystaj z [Kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Pobieranie i eksplorowanie szablonu

Pobierz poniższe pliki szablonu usługi Resource Manager:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Ten szablon umożliwia wdrożenie bezpiecznego klastra z siedmiu maszyn wirtualnych i trzy typy węzłów w sieci wirtualnej.  Inne przykładowe szablony można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Plik [AzureDeploy.json][template] umożliwia wdrożenie szeregu zasobów, w tym następujących.

### <a name="service-fabric-cluster"></a>Klaster usługi Service Fabric

W zasobie **Microsoft.ServiceFabric/clusters** został wdrożony klaster systemu Linux o następujących właściwościach:

* trzy typy węzłów
* pięć węzłów typu węzła podstawowego (z możliwością konfiguracji za pomocą parametrów szablonu), jednego węzła w każdego typu węzła
* System operacyjny: Ubuntu 16.04 LTS (z możliwością konfiguracji w parametrach szablonu)
* Zabezpieczenie przy użyciu certyfikatu (z możliwością konfiguracji za pomocą parametrów szablonu)
* [Usługa DNS](service-fabric-dnsservice.md) jest włączona
* [Poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster): Brązowy (z możliwością konfiguracji za pomocą parametrów szablonu)
* [Poziom niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster): Srebrny (z możliwością konfiguracji za pomocą parametrów szablonu)
* Punkt końcowy połączenia klienta: 19000 (z możliwością konfiguracji w parametrach szablonu)
* Punkt końcowy bramy protokołu HTTP: 19080 (z możliwością konfiguracji w parametrach szablonu)

### <a name="azure-load-balancer"></a>Moduł równoważenia obciążenia platformy Azure

W zasobie **Microsoft.Network/loadBalancers** skonfigurowano moduł równoważenia obciążenia, a sondy i reguły skonfigurowano dla następujących portów:

* Punkt końcowy połączenia klienta: 19000
* Punkt końcowy bramy protokołu HTTP: 19080
* Port aplikacji: 80
* Port aplikacji: 443

### <a name="virtual-network-and-subnet"></a>Sieć wirtualna i podsieć

Nazwy sieci wirtualnej i podsieci są deklarowane w parametrach szablonu.  Przestrzenie adresowe sieci wirtualnej i podsieci również są deklarowane w parametrach szablonu i skonfigurowane w zasobie **Microsoft.Network/virtualNetworks**:

* Przestrzeń adresowa sieci wirtualnej: 10.0.0.0/16
* Przestrzeń adresowa podsieci usługi Service Fabric: 10.0.2.0/24

Jeśli będą potrzebne dowolne inne porty aplikacji, będzie trzeba dostosować zasób Microsoft.Network/loadBalancers, aby zezwolić na ruch przychodzący.

## <a name="set-template-parameters"></a>Ustawianie parametrów szablonu

Plik parametrów [AzureDeploy.Parameters][parameters] deklaruje wiele wartości służących do wdrażania klastra i skojarzonych zasobów. Niektóre parametry, które być może będzie trzeba zmodyfikować na potrzeby danego wdrożenia:

|Parametr|Przykładowa wartość|Uwagi|
|---|---||
|adminUserName|vmadmin| Nazwa użytkownika będącego administratorem maszyn wirtualnych klastra. |
|adminPassword|Haslo#1234| Hasło administratora maszyn wirtualnych klastra.|
|clusterName|mojklastersf123| Nazwa klastra. |
|location|southcentralus| Lokalizacja klastra. |
|certificateThumbprint|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość odcisku palca SHA1 certyfikatu. Na przykład „6190390162C988701DB5676EB81083EA608DCCF3”. </p>|
|certificateUrlValue|| <p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź adres URL certyfikatu. Na przykład „https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”.</p>|
|sourceVaultValue||<p>W przypadku tworzenia certyfikatu z podpisem własnym lub podania pliku certyfikatu ta wartość powinna być pusta.</p><p>Aby użyć istniejącego certyfikatu, który został wcześniej przekazany do magazynu kluczy, wprowadź wartość magazynu źródłowego. Na przykład „/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Wdrażanie sieci wirtualnej i klastra

Następnym etapem jest skonfigurowanie topologii sieci i wdrożenie klastra usługi Service Fabric. Plik [AzureDeploy.json][template] szablonu usługi Resource Manager tworzy sieć wirtualną (VNET) i podsieć dla usługi Service Fabric. Szablon pozwala również wdrożyć klaster z włączonymi zabezpieczeniami opartymi na certyfikacie.  W przypadku klastrów produkcyjnych jako certyfikatu klastra należy używać certyfikatu z urzędu certyfikacji. Do zabezpieczenia klastrów testowych może służyć certyfikat z podpisem własnym.

Szablon w tym artykule wdrażanie klastra, który używa odcisk palca certyfikatu do identyfikowania certyfikatu klastra.  Nie dwóch certyfikatów może mieć ten sam odcisk palca, co sprawia, że certyfikat zarządzania jest trudniejsze. Przełączanie wdrożonego klastra z za pomocą odcisków palca certyfikatu za pomocą nazw pospolitych certyfikatów sprawia, że zarządzanie certyfikatami znacznie prostsze.  Aby dowiedzieć się, jak zaktualizować klaster pod kątem używania nazwy pospolite certyfikatów dla certyfikatu zarządzania, przeczytaj [zmienić klastra do wspólnego zarządzania nazwę certyfikatu](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Tworzenie klastra przy użyciu istniejącego certyfikatu

W poniższym skrypcie wdrożenie nowego klastra zabezpieczonego przy użyciu istniejącego certyfikatu odbywa się za pomocą polecenia [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) oraz szablonu. Polecenie to powoduje także utworzenie nowego magazynu kluczy na platformie Azure i przekazanie danego certyfikatu.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Tworzenie klastra przy użyciu nowego certyfikatu z podpisem własnym

Poniższy skrypt używa polecenia [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) i szablonu, aby wdrożyć nowy klaster na platformie Azure. Polecenie tworzy również nowy magazyn kluczy na platformie Azure, dodaje nowy certyfikat z podpisem własnym do magazynu kluczy i pobiera plik certyfikatu do środowiska lokalnego.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem

Połącz się z klastrem przy użyciu polecenia `sfctl cluster select` interfejsu wiersza polecenia usługi Service Fabric oraz swojego klucza.  Użyj opcji **--no-verify** tylko w przypadku certyfikatu z podpisem własnym.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Za pomocą polecenia `sfctl cluster health` sprawdź poprawność połączenia i upewnij się, że klaster jest w dobrej kondycji.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie przechodzisz od razu do następnego artykułu, rozważ [usunięcie klastra](service-fabric-cluster-delete.md), aby uniknąć naliczania opłat.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [skalowanie klastra](service-fabric-tutorial-scale-cluster.md).

Szablon w tym artykule wdrażanie klastra, który używa odcisk palca certyfikatu do identyfikowania certyfikatu klastra.  Nie dwóch certyfikatów może mieć ten sam odcisk palca, co sprawia, że certyfikat zarządzania jest trudniejsze. Przełączanie wdrożonego klastra z za pomocą odcisków palca certyfikatu za pomocą nazw pospolitych certyfikatów sprawia, że zarządzanie certyfikatami znacznie prostsze.  Aby dowiedzieć się, jak zaktualizować klaster pod kątem używania nazwy pospolite certyfikatów dla certyfikatu zarządzania, przeczytaj [zmienić klastra do wspólnego zarządzania nazwę certyfikatu](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
