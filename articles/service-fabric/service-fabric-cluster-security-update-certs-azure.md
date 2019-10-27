---
title: Zarządzanie certyfikatami w klastrze usługi Azure Service Fabric | Microsoft Docs
description: Opisuje, jak dodać nowe certyfikaty, certyfikat przerzucania i usunąć certyfikat do lub z klastra Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 9c14afb22d95493deaf3552cb8c7392c3fc5a679
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934021"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Dodawanie lub usuwanie certyfikatów klastra Service Fabric na platformie Azure
Zaleca się zapoznanie się ze sposobem, w jaki Service Fabric używa certyfikatów X. 509, i zna [scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md). Przed kontynuowaniem należy zrozumieć, czym jest certyfikat klastra i co jest używane w programie.

Domyślne zachowanie ładowania certyfikatu zestawu SDK usługi Azure Service Fabric polega na wdrożeniu i użyciu zdefiniowanego certyfikatu z datą wygaśnięcia, która jest najdalej w przyszłości. niezależnie od ich podstawowej lub pomocniczej definicji konfiguracji. Powracanie do klasycznego zachowania jest niezalecaną zaawansowaną akcją i wymaga ustawienia wartości parametru ustawienia "UseSecondaryIfNewer" na wartość false w konfiguracji sieci szkieletowej. kod.

Usługa Service Fabric pozwala określić dwa certyfikaty klastra, podstawowe i pomocnicze, podczas konfigurowania zabezpieczeń certyfikatów podczas tworzenia klastra, oprócz certyfikatów klienta. Zapoznaj się z tematem [Tworzenie klastra platformy Azure za pośrednictwem portalu](service-fabric-cluster-creation-via-portal.md) lub [Tworzenie klastra platformy Azure za pośrednictwem Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) , aby uzyskać szczegółowe informacje na temat konfigurowania ich w czasie tworzenia. W przypadku określenia tylko jednego certyfikatu klastra podczas tworzenia, który jest używany jako certyfikat podstawowy. Po utworzeniu klastra można dodać nowy certyfikat jako pomocniczy.

> [!NOTE]
> W przypadku bezpiecznego klastra będzie zawsze potrzebny co najmniej jeden prawidłowy (nieodwołany i niewygasły) wdrożony certyfikat klastra (podstawowy lub pomocniczy) (jeśli nie, klaster przestanie działać). 90 dni, zanim wszystkie prawidłowe certyfikaty osiągną wygaśnięcie, system generuje śledzenie ostrzeżeń oraz zdarzenie kondycji ostrzeżenia w węźle. W tym artykule nie ma obecnie poczty e-mail ani żadnych innych powiadomień, które Service Fabric wysyłali. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Dodawanie pomocniczego certyfikatu klastra przy użyciu portalu
Nie można dodać pomocniczego certyfikatu klastra za pomocą Azure Portal przy użyciu programu Azure PowerShell. Ten proces jest opisany w dalszej części tego dokumentu.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Usuwanie certyfikatu klastra przy użyciu portalu
W przypadku bezpiecznego klastra będzie zawsze potrzebny co najmniej jeden ważny (nieodwołany i niewygasły) certyfikat. Certyfikat wdrożony z najpóźniejszym terminem wygaśnięcia w przyszłości będzie używany, a usunięcie go spowoduje, że klaster przestanie działać. Upewnij się, że usunięto certyfikat, którego ważność wygasła, lub nieużywany certyfikat, który wkrótce wygaśnie.

Aby usunąć nieużywany certyfikat zabezpieczeń klastra, przejdź do sekcji Zabezpieczenia i wybierz opcję "Usuń" z menu kontekstowego w nieużywanym certyfikacie.

Jeśli zamiarem jest usunięcie certyfikatu oznaczonego jako podstawowa, należy wdrożyć certyfikat pomocniczy z datą wygaśnięcia dalszą w przyszłości niż certyfikat podstawowy, włączając zachowanie autorollover. Usuń certyfikat podstawowy po zakończeniu przerzucania.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Dodawanie certyfikatu pomocniczego przy użyciu programu Menedżer zasobów PowerShell
> [!TIP]
> Teraz lepszym i łatwiejszym sposobem dodawania certyfikatu pomocniczego jest użycie polecenia cmdlet [Add-AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) . Nie musisz wykonywać pozostałej części kroków opisanych w tej sekcji.  Ponadto nie jest potrzebny szablon używany do tworzenia i wdrażania klastra przy użyciu polecenia cmdlet [Add-AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) .

W tych krokach przyjęto założenie, że wiesz już, jak działa Menedżer zasobów i wdrożono co najmniej jeden klaster Service Fabric przy użyciu szablonu Menedżer zasobów oraz szablon użyty do skonfigurowania klastra. Założono również, że masz doświadczenie w korzystaniu z formatu JSON.

> [!NOTE]
> Jeśli szukasz przykładowego szablonu i parametrów, których można użyć do wykonywania kolejnych czynności lub jako punktu początkowego, Pobierz go z [repozytorium git](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Edytowanie szablonu Menedżer zasobów

Na przykład w przykład 5-VM-1-elementów NodeType-Secure_Step2. JSON zawiera wszystkie zmiany, które będziemy wprowadzać. przykład jest dostępny w [repozytorium git](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Upewnij się, że wykonaj wszystkie kroki**

1. Otwórz szablon Menedżer zasobów użyty do wdrożenia klastra. (Jeśli pobrano próbkę z poprzedniego repozytorium, użyj 5-VM-1-elementów NodeType-Secure_Step1. JSON, aby wdrożyć bezpieczny klaster, a następnie otwórz ten szablon).

2. Dodaj **dwa nowe parametry** "secCertificateThumbprint" i "secCertificateUrlValue" typu "String" do sekcji parametrów szablonu. Można skopiować poniższy fragment kodu i dodać go do szablonu. W zależności od źródła szablonu można już zdefiniować te, jeśli tak, przejdź do następnego kroku. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Wprowadź zmiany w zasobów **Microsoft. servicefabric/klastrów** — zlokalizuj definicję zasobu "Microsoft. servicefabric/Clusters" w szablonie. W obszarze właściwości tej definicji znajdziesz tag JSON "Certificate", który powinien wyglądać podobnie jak w poniższym fragmencie kodu JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Dodaj nowy tag "thumbprintSecondary" i nadaj mu wartość "[Parameters (" secCertificateThumbprint ")]".  

    Teraz definicja zasobu powinna wyglądać podobnie do następującej (w zależności od źródła szablonu), ale może nie być dokładnie tak jak w poniższym fragmencie kodu. 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Jeśli chcesz **wycofać certyfikat**, określ nowy certyfikat jako podstawowy i Przenieś bieżący podstawowy jako pomocniczy. Powoduje to Przerzucanie bieżącego certyfikatu podstawowego do nowego certyfikatu w jednym kroku wdrożenia.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Wprowadź zmiany **we wszystkich** definicjach zasobów **Microsoft. COMPUTE/virtualMachineScaleSets** — zlokalizuj definicję zasobu Microsoft. COMPUTE/virtualMachineScaleSets. Przewiń do "Wydawca": "Microsoft. Azure. servicefabric" w obszarze "virtualMachineProfile".

    W ustawieniach wydawcy Service Fabric powinna zostać wyświetlona coś podobnego do tego.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Dodawanie do niego nowych wpisów certyfikatów
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Właściwości powinny teraz wyglądać następująco
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Jeśli chcesz **wycofać certyfikat**, określ nowy certyfikat jako podstawowy i Przenieś bieżący podstawowy jako pomocniczy. Powoduje to Przerzucanie bieżącego certyfikatu do nowego certyfikatu w jednym kroku wdrożenia.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    Właściwości powinny teraz wyglądać następująco    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Wprowadź zmiany **we wszystkich** definicjach zasobów **Microsoft. COMPUTE/virtualMachineScaleSets** — zlokalizuj definicję zasobu Microsoft. COMPUTE/virtualMachineScaleSets. Przewiń do "vaultCertificates":, w obszarze "OSProfile". powinien on wyglądać podobnie do tego.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Dodaj do niego secCertificateUrlValue. Skorzystaj z następującego fragmentu kodu:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Teraz otrzymany kod JSON powinien wyglądać podobnie do tego.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Upewnij się, że zostały powtórzone kroki 4 i 5 dla wszystkich definicji zasobów elementów NodeType/Microsoft. COMPUTE/virtualMachineScaleSets w szablonie. Jeśli pominięto jeden z nich, certyfikat nie zostanie zainstalowany w tym zestawie skalowania maszyn wirtualnych, a w klastrze będą dostępne nieprzewidywalne wyniki, w tym klaster przechodzący (w przypadku braku prawidłowych certyfikatów, których klaster może używać na potrzeby zabezpieczeń). Więc sprawdź podwójne, zanim przejdziesz dalej.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edytuj plik szablonu, aby odzwierciedlić nowe parametry, które zostały dodane powyżej
Jeśli używasz przykładu z [repozytorium git](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) , aby wykonać następujące czynności, możesz zacząć wprowadzać zmiany w przykładzie 5-VM-1-elementów NodeType-Secure. PARAMETERS_STEP2. JSON 

Edytuj plik parametrów szablonu Menedżer zasobów, Dodaj dwa nowe parametry dla secCertificateThumbprint i secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Wdrażanie szablonu na platformie Azure

- Teraz możesz przystąpić do wdrażania szablonu na platformie Azure. Otwórz wiersz polecenia platformy Azure PS w wersji 1 +.
- Zaloguj się do konta platformy Azure i wybierz określoną subskrypcję platformy Azure. Jest to ważny krok dla osób, kto ma dostęp do więcej niż jednej subskrypcji platformy Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Przetestuj szablon przed jego wdrożeniem. Użyj tej samej grupy zasobów, do której jest obecnie wdrożony klaster.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Wdróż szablon w grupie zasobów. Użyj tej samej grupy zasobów, do której jest obecnie wdrożony klaster. Uruchom polecenie New-AzResourceGroupDeployment. Nie trzeba określać trybu, ponieważ wartością domyślną jest **przyrostowy**.

> [!NOTE]
> Jeśli ustawisz tryb na zakończony, możesz przypadkowo usunąć zasoby, które nie znajdują się w szablonie. Dlatego nie należy używać go w tym scenariuszu.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Oto przykład wypełnienia tego samego programu PowerShell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Po zakończeniu wdrażania Połącz się z klastrem przy użyciu nowego certyfikatu i wykonaj pewne zapytania. Jeśli masz możliwość wykonania. Następnie można usunąć stary certyfikat. 

Jeśli używasz certyfikatu z podpisem własnym, nie zapomnij zaimportować ich do lokalnego magazynu certyfikatów TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Aby uzyskać skrócone informacje na ten temat, można połączyć się z bezpiecznym klastrem 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Aby uzyskać szczegółowe informacje w tym miejscu, można uzyskać kondycję klastra

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Wdrażanie certyfikatów klienta w klastrze.

Możesz użyć tych samych kroków, które opisano w poprzednich krokach 5, aby certyfikaty zostały wdrożone z magazynu kluczy do węzłów. Wystarczy zdefiniować i użyć innych parametrów.


## <a name="adding-or-removing-client-certificates"></a>Dodawanie lub usuwanie certyfikatów klienta

Oprócz certyfikatów klastra można dodać certyfikaty klienta, aby wykonywać operacje zarządzania w klastrze Service Fabric.

Można dodać dwa rodzaje certyfikatów klienta — administrator lub tylko do odczytu. Służą one do kontrolowania dostępu do operacji administracyjnych i operacji zapytań w klastrze. Domyślnie certyfikaty klastra są dodawane do listy dozwolonych certyfikatów administracyjnych.

można określić dowolną liczbę certyfikatów klienta. Każde Dodawanie/usuwanie powoduje aktualizację konfiguracji klastra Service Fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Dodawanie certyfikatów klienta — administrator lub tylko do odczytu za pośrednictwem portalu

1. Przejdź do sekcji Zabezpieczenia i wybierz przycisk "+ Uwierzytelnianie" znajdujący się u góry sekcji Zabezpieczenia.
2. W sekcji "Dodawanie uwierzytelniania" Wybierz pozycję "typ uwierzytelniania" — "klient tylko do odczytu" lub "klient administracyjny".
3. Teraz wybierz metodę autoryzacji. Wskazuje to na Service Fabric, czy powinien on wyszukiwać ten certyfikat przy użyciu nazwy podmiotu, czy odcisku palca. Ogólnie rzecz biorąc, nie jest dobrym sposobem na korzystanie z metody autoryzacji nazwy podmiotu. 

![Dodaj certyfikat klienta][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Usuwanie certyfikatów klienta — administrator lub tylko do odczytu przy użyciu portalu

Aby usunąć certyfikat pomocniczy z używania na potrzeby zabezpieczeń klastra, przejdź do sekcji Zabezpieczenia i wybierz opcję "Usuń" z menu kontekstowego dla określonego certyfikatu.

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły, aby uzyskać więcej informacji na temat zarządzania klastrem:

* [Service Fabric proces uaktualniania klastra i oczekiwania z niego](service-fabric-cluster-upgrade.md)
* [Konfigurowanie dostępu opartego na rolach dla klientów](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


