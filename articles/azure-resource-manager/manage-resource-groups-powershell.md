---
title: Zarządzanie grupami zasobów — Azure PowerShell
description: Użyj Azure PowerShell do zarządzania grupami zasobów przy użyciu Azure Resource Manager. Pokazuje, jak tworzyć, wyświetlać i usuwać grupy zasobów.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5d0d7394de2763c785640b0981de9017cf0a7ae7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149794"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Zarządzanie grupami zasobów Azure Resource Manager przy użyciu Azure PowerShell

Dowiedz się, jak używać Azure PowerShell z [Azure Resource Manager](resource-group-overview.md) do zarządzania grupami zasobów platformy Azure. Aby zarządzać zasobami platformy Azure, zobacz [Zarządzanie zasobami platformy Azure przy użyciu Azure PowerShell](./manage-resources-powershell.md).

Inne artykuły dotyczące zarządzania grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu Azure Portal](./manage-resources-portal.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Co to jest Grupa zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc, Dodaj zasoby, które mają ten sam cykl życia do tej samej grupy zasobów, dzięki czemu możesz łatwo wdrażać, aktualizować i usuwać je jako grupę.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. W przypadku określania lokalizacji grupy zasobów należy określić miejsce przechowywania metadanych.

## <a name="create-resource-groups"></a>Tworzenie grup zasobów

Poniższy skrypt programu PowerShell tworzy grupę zasobów, a następnie wyświetla grupę zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Wyświetl listę grup zasobów

Poniższy skrypt programu PowerShell zawiera listę grup zasobów w ramach subskrypcji.

```azurepowershell-interactive
Get-AzResourceGroup
```

Aby uzyskać jedną grupę zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Usuń grupy zasobów

Następujący skrypt programu PowerShell usuwa grupę zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Resource Manager kolejności usuwania zasobów, zobacz [Azure Resource Manager Usuwanie grupy zasobów](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów w istniejącej grupie zasobów

Zobacz [wdrażanie zasobów w istniejącej grupie zasobów](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Aby sprawdzić poprawność wdrożenia grupy zasobów, zobacz [test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrożyć zasoby w grupie przy użyciu szablonu Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie w przypadku niepowodzenia wdrożenia

Ta funkcja jest również znana jako *Rollback w przypadku błędu*. Aby uzyskać więcej informacji, zobacz [ponowne wdrażanie w przypadku niepowodzenia wdrożenia](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Przenieś do innej grupy zasobów lub subskrypcji

Zasoby w grupie można przenieść do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](./resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resource-groups"></a>Zablokuj grupy zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usuwanie lub modyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, Grupa zasobów lub zasób. 

Poniższy skrypt blokuje grupę zasobów, aby nie można było usunąć grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Następujący skrypt pobiera wszystkie blokady dla grupy zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resource-groups"></a>Dodawanie tagów do grup zasobów

Możesz zastosować znaczniki do grup zasobów i zasobów, aby logicznie organizować zasoby. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grup zasobów do szablonów

Po skonfigurowaniu grupy zasobów można wyświetlić szablon Menedżer zasobów dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

- Automatyzuj przyszłe wdrożenia rozwiązania, ponieważ szablon zawiera kompletną infrastrukturę.
- Poznaj składnię szablonu, przeglądając JavaScript Object Notation (JSON), który reprezentuje Twoje rozwiązanie.

Aby wyeksportować wszystkie zasoby w grupie zasobów, należy użyć polecenia cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) i podać nazwę grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Zapisuje szablon jako plik lokalny.

Zamiast eksportować wszystkie zasoby w grupie zasobów, możesz wybrać zasoby do wyeksportowania.

Aby wyeksportować jeden zasób, Przekaż ten identyfikator zasobu.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Aby wyeksportować więcej niż jeden zasób, Przekaż identyfikatory zasobów w tablicy.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Podczas eksportowania szablonu można określić, czy parametry są używane w szablonie. Domyślnie parametry nazw zasobów są uwzględniane, ale nie mają wartości domyślnej. Wartość parametru należy przekazać podczas wdrażania.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

W zasobie parametr jest używany jako nazwa.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Jeśli podczas eksportowania szablonu używasz parametru `-IncludeParameterDefaultValue`, parametr Template zawiera wartość domyślną ustawioną na bieżącą wartość. Możesz użyć tej wartości domyślnej lub zastąpić wartość domyślną, przekazując inną wartość.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Jeśli używasz `-SkipResourceNameParameterization` parametru podczas eksportowania szablonu, parametry nazw zasobów nie są uwzględnione w szablonie. Zamiast tego nazwa zasobu jest ustawiana bezpośrednio w ramach zasobu na jego bieżącą wartość. Nie można dostosować nazwy podczas wdrażania.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Aby uzyskać więcej informacji, zobacz [Eksportowanie jednego i wielu zasobów do szablonu w Azure Portal](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu RBAC i Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).