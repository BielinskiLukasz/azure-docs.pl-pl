---
title: Dane osobowe
description: Dowiedz się, jak zarządzać danymi osobowymi skojarzonymi z Azure Resource Manager operacjami.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 2b1f97fa4f831235fe04c18b1ed33e729ed2326b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149508"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Zarządzanie danymi osobowymi skojarzonymi z Azure Resource Manager

Aby uniknąć ujawniania poufnych informacji, Usuń wszelkie dane osobowe, które mogą być podane we wdrożeniach, grupach zasobów lub tagach. Azure Resource Manager zawiera operacje umożliwiające zarządzanie danymi osobowymi, które mogą być udostępniane w ramach wdrożeń, grup zasobów lub tagów.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Usuwanie danych osobowych w historii wdrożenia

W przypadku wdrożeń Menedżer zasobów zachowuje wartości parametrów i komunikatów o stanie w historii wdrażania. Te wartości są zachowywane do momentu usunięcia wdrożenia z historii. Aby sprawdzić, czy w tych wartości zostały podane dane osobowe, Wyświetl listę wdrożeń. Jeśli znajdziesz dane osobowe, Usuń wdrożenia z historii.

Aby wyświetlić listę **wdrożeń** w historii, użyj:

* [Lista według grupy zasobów](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [AZ Group Deployment list](/cli/azure/group/deployment#az-group-deployment-list)

Aby usunąć **wdrożenia** z historii, użyj:

* [Usuwanie](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [AZ Group Deployment Delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Usuń dane osobowe z nazw grup zasobów

Nazwa grupy zasobów będzie trwała do momentu usunięcia grupy zasobów. Aby sprawdzić, czy w nazwach zostały podane dane osobowe, Wyświetl listę grup zasobów. Jeśli znajdziesz dane osobowe, [Przenieś zasoby](resource-group-move-resources.md) do nowej grupy zasobów, a następnie Usuń grupę zasobów z danymi osobowymi w nazwie.

Aby wyświetlić listę **grup zasobów**, użyj:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [AZ Group list](/cli/azure/group#az-group-list)

Aby usunąć **grupy zasobów**, użyj:

* [Usuwanie](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Usuwanie danych osobowych w tagach

Tagi i nazwy są zachowywane do momentu usunięcia lub zmodyfikowania znacznika. Aby sprawdzić, czy w tagach zostały podane dane osobowe, Wyświetl Tagi. Jeśli znajdziesz dane osobowe, Usuń Tagi.

Aby wyświetlić listę **tagów**, użyj:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [AZ tag list](/cli/azure/tag#az-tag-list)

Aby usunąć **Tagi**, użyj:

* [Usuwanie](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [Wyczyść tag Delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z omówieniem Azure Resource Manager, zobacz [co to jest Menedżer zasobów?](resource-group-overview.md)