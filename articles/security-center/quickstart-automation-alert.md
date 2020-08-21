---
title: Tworzenie automatyzacji zabezpieczeń dla konkretnych alertów zabezpieczeń przy użyciu szablonu Azure Resource Manager (ARM)
description: Dowiedz się, jak utworzyć automatyzację Azure Security Center, aby wyzwolić aplikację logiki, która będzie wyzwalana przez określone alerty Security Center przy użyciu szablonu Azure Resource Manager (ARM)
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: ecef318978194ac3773c54e2d9c960781086de65
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691811"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-azure-resource-manager-template-arm-template"></a>Szybki Start: Tworzenie automatycznej odpowiedzi na określony alert zabezpieczeń przy użyciu szablonu Azure Resource Manager (szablon ARM)

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia automatyzacji przepływu pracy, który wyzwala aplikację logiki, gdy określone alerty zabezpieczeń są odbierane przez Azure Security Center.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Aby uzyskać listę ról i uprawnień wymaganych do pracy z funkcją automatyzacji przepływu pracy Azure Security Center, zobacz [Automatyzacja przepływu pracy](workflow-automation.md).


## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Odpowiednie zasoby

- [**Microsoft. Security/automations**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftsecurity-resource-provider): Automatyzacja, która wywoła aplikację logiki, po odebraniu alertu Azure Security Center zawierającego określony ciąg.
- [**Microsoft. logiki/przepływy pracy**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftlogic-resource-provider): pusta aplikacja logiki z wyzwalaczem.

Inne Security Center Szablony szybkiego startu można znaleźć w [szablonach](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security)utworzonych przez społeczność.

## <a name="deploy-the-template"></a>Wdrażanie szablonu

  - Program **PowerShell**:
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **Interfejs wiersza polecenia**:
    ```azurecli-interactive
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **Portal**:

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

    Aby uzyskać więcej informacji na temat tej opcji wdrożenia, zobacz [Użyj przycisku wdrożenia, aby wdrożyć szablony z repozytorium GitHub](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Użyj Azure Portal, aby sprawdzić, czy Automatyzacja przepływu pracy został wdrożony. 

1. W [Azure Portal](https://portal.azure.com)Otwórz **Security Center**.
1. Na górnym pasku menu wybierz ikonę filtru, a następnie wybierz konkretną subskrypcję, w której wdrożono nową automatyzację przepływu pracy.
1. Na pasku bocznym Security Center Otwórz **automatyzację przepływu pracy** i Sprawdź nową automatyzację.

    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Tekst alternatywny obrazu." lightbox="./media/quickstart-automation-alert/validating-template-run.png":::

    >[!TIP]
    > Jeśli masz wiele automatyzacji przepływu pracy w subskrypcji, użyj opcji **Filtruj według nazwy** . 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, Usuń automatyzację przepływu pracy przy użyciu Azure Portal.

1. W [Azure Portal](https://portal.azure.com)Otwórz **Security Center**.
1. Na górnym pasku menu wybierz ikonę filtru, a następnie wybierz konkretną subskrypcję, w której wdrożono nową automatyzację przepływu pracy.
1. Na pasku bocznym Security Center Otwórz **automatyzację przepływu pracy** i Znajdź automatyzację do usunięcia.

    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Tekst alternatywny obrazu." lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::

1. Zaznacz pole wyboru dla elementu, który ma zostać usunięty.
1. Na pasku narzędzi wybierz pozycję Usuń.


## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)