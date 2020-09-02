---
title: 'Szybki Start: Tworzenie Traffic Manager przy użyciu szablonu Azure Resource Manager (szablon ARM)'
description: W tym artykule szybki start opisano sposób tworzenia profilu usługi Azure Traffic Manager przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: dbdb6a255fdf0214103a0011f25b0a6d25014e69
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299154"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Szybki Start: Tworzenie profilu Traffic Manager przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia profilu Traffic Manager z zewnętrznymi punktami końcowymi przy użyciu metody routingu wydajności.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

Jeden zasób platformy Azure jest zdefiniowany w szablonie:

* [**Microsoft. Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Aby znaleźć więcej szablonów związanych z usługą Azure Traffic Manager, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrażanie szablonu

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Poczekaj, aż zobaczysz monit z konsoli programu.

1. Wybierz pozycję **Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz polecenie **Wklej**.

1. Wprowadź wartości.

    Wdrożenie szablonu tworzy profil z dwoma zewnętrznymi punktami końcowymi. **Endpoint1** używa docelowego punktu końcowego *w in<span>TT. Microsoft</span>. com* z lokalizacją w **Europie Północnej**. **Endpoint2** używa docelowego punktu końcowego *d<span>OCS. Microsoft</span>. com* z lokalizacją w regionie **Południowo-środkowe stany USA**. 

    Nazwa grupy zasobów jest nazwą projektu z dołączoną **RG** .

    > [!NOTE]
    > **uniqueDNSname** musi być globalnie unikatową nazwą, aby szablon został pomyślnie wdrożony. Jeśli wdrożenie nie powiedzie się, Zacznij od początku, korzystając z kroku 1.

    Wdrożenie szablonu trwa kilka minut. Po zakończeniu dane wyjściowe są podobne do:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Szablon platformy Azure Traffic Manager Menedżer zasobów dane wyjściowe wdrożenia programu PowerShell":::

Azure PowerShell służy do wdrożenia szablonu. Oprócz Azure PowerShell można również użyć Azure Portal, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Określ nazwę DNS profilu Traffic Manager przy użyciu polecenia [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Skopiuj wartość **RelativeDnsName** . Nazwa DNS profilu Traffic Manager to *<* relativednsname *>. trafficmanager.NET*. 

1. Z poziomu lokalnego programu PowerShell uruchom następujące polecenie, zastępując zmienną **{relativeDNSname}** atrybutem *<* relativeDNSname *>. trafficmanager.NET*.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```
    Powinna zostać wyświetlona wartość NameHost *w wartości in<span>TT. Microsoft</span>. com* lub *d<span>OCS. Microsoft</span>. com* w zależności od regionu.

1. Aby sprawdzić, czy można rozwiązać ten problem, należy wyłączyć punkt końcowy dla elementu docelowego, który został uzyskany w ostatnim kroku. Zastąp element **{EndpointName}** wartością **endpoint1** lub **endpoint2** , aby wyłączyć obiekt docelowy dla odpowiednio wartości *<span>TT. Microsoft</span>. com* lub *d<span>OCS. Microsoft</span>. com* .

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```
1. Uruchom ponownie polecenie z kroku 2 w lokalnym programie PowerShell. Tym razem należy uzyskać inne NameHost dla innego punktu końcowego. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli profil Traffic Manager nie jest już potrzebny, Usuń grupę zasobów. Spowoduje to usunięcie profilu Traffic Manager i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:
* Profil usługi Traffic Manager

Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
