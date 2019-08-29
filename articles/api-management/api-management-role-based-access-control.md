---
title: Jak używać Access Control opartych na rolach na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak używać wbudowanych ról i tworzyć role niestandardowe na platformie Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: e33d981429f0e79accbe47ea0edea5f3c7a2157b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072209"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Jak używać Access Control opartych na rolach na platformie Azure API Management

Usługa Azure API Management opiera się na Access Control opartej na rolach (RBAC) na platformie Azure w celu umożliwienia szczegółowego zarządzania dostępem dla usług i jednostek API Management (na przykład interfejsów API i zasad). Ten artykuł zawiera omówienie wbudowanych i niestandardowych ról w programie API Management. Aby uzyskać więcej informacji na temat zarządzania dostępem w Azure Portal, zobacz [wprowadzenie do zarządzania dostępem w Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Wbudowane role

API Management obecnie udostępnia trzy wbudowane role i doda dwie kolejne role w najbliższej przyszłości. Role te można przypisywać w różnych zakresach, w tym w ramach subskrypcji, grupy zasobów i wystąpienia poszczególnych API Management. Jeśli na przykład przypiszesz rolę "czytnik usługi Azure API Management Service" do użytkownika na poziomie grupy zasobów, użytkownik ma dostęp do odczytu do wszystkich wystąpień API Management wewnątrz grupy zasobów. 

W poniższej tabeli przedstawiono krótkie opisy wbudowanych ról. Role te można przypisywać przy użyciu Azure Portal lub innych narzędzi, w tym [programu Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), interfejsu [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)i [interfejsów API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Aby uzyskać szczegółowe informacje o sposobie przypisywania ról wbudowanych, zobacz Korzystanie z przypisań [ról w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Role          | Dostęp do odczytu<sup>[1]</sup> | Dostęp do zapisu<sup>[2]</sup> | Tworzenie, usuwanie, skalowanie, Sieć VPN i konfiguracja domeny niestandardowej usługi | Dostęp do starszego portalu wydawców | Opis
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Współautor usługi API Management platformy Azure | ✓ | ✓ | ✓ | ✓ | Administrator. Ma pełny dostęp CRUD do usług i jednostek API Management (na przykład interfejsów API i zasad). Ma dostęp do starszego portalu wydawców. |
| Czytnik usługi API Management platformy Azure | ✓ | | || Ma dostęp tylko do odczytu do usług API Management i jednostek. |
| Operator usługi API Management platformy Azure | ✓ | | ✓ | | Może zarządzać usługami API Management, ale nie do jednostek.|
| Edytor usługi API Management platformy Azure<sup>*</sup> | ✓ | ✓ | |  | Może zarządzać jednostkami API Management, ale nie usługami.|
| Usługa Azure API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Może zarządzać portalem dla deweloperów. Dostęp tylko do odczytu do usług i jednostek.|

<sup>[1] dostęp do odczytu do usług API Management i jednostek (na przykład interfejsów API i zasad).</sup>

<sup>[2] dostęp do zapisu do usług API Management i jednostek z wyjątkiem następujących operacji: Tworzenie, usuwanie i skalowanie wystąpienia; Konfiguracja sieci VPN; i konfiguracja domeny niestandardowej.</sup>

<sup>\*Rola Edytor usługi będzie dostępna po przeprowadzeniu migracji wszystkich interfejsów użytkownika administratora z istniejącego portalu wydawców do Azure Portal. Rola Menedżera zawartości będzie dostępna po rozpoczęciu portalu wydawców tylko w celu uwzględnienia funkcji związanych z zarządzaniem portalem dla deweloperów.</sup>  

## <a name="custom-roles"></a>Role niestandardowe

Jeśli żadna z wbudowanych ról nie spełnia określonych wymagań, można utworzyć role niestandardowe, aby zapewnić bardziej szczegółowy sposób zarządzania dostępem dla API Management jednostek. Można na przykład utworzyć rolę niestandardową, która ma dostęp tylko do odczytu do usługi API Management, ale ma dostęp tylko do zapisu do jednego określonego interfejsu API. Aby dowiedzieć się więcej na temat ról niestandardowych, zobacz [role niestandardowe w usłudze Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> Aby można było zobaczyć wystąpienie API Management w Azure Portal, rola niestandardowa musi zawierać ```Microsoft.ApiManagement/service/read``` akcję.

Gdy tworzysz rolę niestandardową, łatwiej jest zacząć od jednej z wbudowanych ról. Edytuj atrybuty, aby dodać **Akcje**,nochangess lub **AssignableScopes**, a następnie Zapisz zmiany jako nową rolę. Poniższy przykład rozpoczyna się od roli "czytnik usługi Azure API Management Service" i tworzy rolę niestandardową o nazwie "Edytor interfejsu API kalkulatora". Rolę niestandardową można przypisać do określonego interfejsu API. W związku z tym ta rola ma dostęp tylko do tego interfejsu API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Artykuł [działania dostawcy zasobów Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) zawiera listę uprawnień, które mogą być udzielane na poziomie API Management.

## <a name="video"></a>Połączenia wideo


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Access Control opartych na rolach na platformie Azure, zobacz następujące artykuły:
  * [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
  * [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md)
  * [Role niestandardowe w usłudze Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Operacje dostawcy zasobów Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)