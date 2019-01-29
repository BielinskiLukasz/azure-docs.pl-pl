---
title: Jak zarządzać przypisanych przez użytkownika tożsamości zarządzanej przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące sposobu tworzenia, wyświetlania i usuwania, użytkownik przypisany zarządzanych tożsamości przy użyciu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: priyamo
ms.openlocfilehash: 47ad46bfb89efd11ea19386c2f2e26fc11a229c5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175506"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Utwórz listę lub usunąć przypisanych przez użytkownika tożsamości zarządzanej przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure za pomocą tożsamości zarządzanej w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności używania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak tworzyć, listy i usuwać przypisane przez użytkownika tożsamości zarządzanej przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. Zaloguj się do platformy Azure za pomocą `az login`, przy użyciu konta, który jest skojarzony z subskrypcją platformy Azure w ramach której chcesz wdrożyć, użytkownik przypisany tożsamości zarządzanej.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Aby utworzyć przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

Użyj [Utwórz tożsamość az](/cli/azure/identity#az-identity-create) tożsamości zarządzanej polecenie, aby utworzyć przypisanych przez użytkownika. `-g` Parametr określa grupę zasobów, gdzie trzeba utworzyć przypisanych przez użytkownika tożsamości zarządzanej, a `-n` parametr określa jej nazwę. Zastąp `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów własnymi wartościami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista zarządzanych tożsamości przypisanych przez użytkownika

Na liście odczytu przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

Aby wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika, użyj [listę tożsamości az](/cli/azure/identity#az-identity-list) polecenia. Zastąp `<RESOURCE GROUP>` swoją własną wartością:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
W odpowiedzi json zarządzanych tożsamości przypisanych przez użytkownika mają `"Microsoft.ManagedIdentity/userAssignedIdentities"` wartość zwracana dla klucza `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej

Aby usunąć przypisanych przez użytkownika tożsamości zarządzanej, Twoje konto musi [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) przypisania roli.

Aby usunąć tożsamości zarządzanej przypisanych przez użytkownika, użyj [usuwanie tożsamości az](/cli/azure/identity#az-identity-delete) polecenia.  -N parametr określa jej nazwę, a parametr -g Określa grupę zasobów, w której utworzono zarządzanych tożsamości przypisanych przez użytkownika. Zastąp `<USER ASSIGNED IDENTITY NAME>` i `<RESOURCE GROUP>` wartości parametrów własnymi wartościami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Usuwanie tożsamości zarządzanej przypisanych przez użytkownika nie spowoduje usunięcia odwołania, z dowolnego zasobu, który został przypisany do. Usuń te z VM/VMSS za pomocą `az vm/vmss identity remove` polecenia

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać pełną listę poleceń tożsamość wiersza polecenia platformy Azure, zobacz [tożsamości az](/cli/azure/identity).

Aby uzyskać informacje dotyczące sposobu przypisywania użytkownik przypisany tożsamości do maszyny Wirtualnej platformy Azure, zobacz temat zarządzanej [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
