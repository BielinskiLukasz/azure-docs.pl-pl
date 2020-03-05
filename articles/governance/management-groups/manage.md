---
title: Jak korzystać z grup zarządzania — Zarządzanie platformą Azure
description: Dowiedz się, jak wyświetlać, obsługiwać, aktualizować i usuwać hierarchię grup zarządzania.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 94df67888c0ed0ea532844a92a362a181621d3d3
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267938"
---
# <a name="manage-your-resources-with-management-groups"></a>Zarządzanie zasobami za pomocą grup zarządzania

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania.

Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.  Aby dowiedzieć się więcej na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

>[!IMPORTANT]
>Azure Resource Manager tokenów użytkowników i pamięci podręcznej grup zarządzania trwa 30 minut, zanim zostanie wymuszone odświeżenie.  Po wykonaniu akcji takich jak przeniesienie grupy zarządzania lub subskrypcji może potrwać do 30 minut.  
>Aby wyświetlić aktualizacje wcześniej, musisz zaktualizować token przez odświeżenie przeglądarki, zalogowanie się i wyjście lub żądanie nowego tokenu.  



## <a name="change-the-name-of-a-management-group"></a>Zmień nazwę grupy zarządzania

Nazwę grupy zarządzania można zmienić przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="change-the-name-in-the-portal"></a>Zmień nazwę w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi** > **grupy zarządzania**.

1. Wybierz grupę zarządzania, której nazwę chcesz zmienić.

1. Wybierz pozycję **szczegóły**.

1. Wybierz opcję **Zmień nazwę grupy** w górnej części strony.

   ![Zmień nazwę opcji grupy na stronie grupy zarządzania](./media/detail_action_small.png)

1. Po otwarciu menu Wprowadź nową nazwę, którą chcesz wyświetlić.

   ![Zmień nazwę okienka grupy, aby zmienić nazwę grupy zarządzania](./media/rename_context.png)

1. Wybierz pozycję **Zapisz**.

### <a name="change-the-name-in-powershell"></a>Zmiana nazwy w programie PowerShell

Aby zaktualizować nazwę wyświetlaną, użyj **Update-AzManagementGroup**. Aby na przykład zmienić nazwę wyświetlaną grup zarządzania z "contoso IT" na "Group contoso", uruchom następujące polecenie:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Zmień nazwę w interfejsie wiersza polecenia platformy Azure

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenie Update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Usuwanie grupy zarządzania

Aby można było usunąć grupę zarządzania, muszą zostać spełnione następujące wymagania:

1. W grupie zarządzania nie ma podrzędnych grup ani subskrypcji zarządzania.

   - Aby przenieść subskrypcję lub grupę zarządzania do innej grupy zarządzania [, zobacz Przenoszenie grup zarządzania i subskrypcji w hierarchii](#moving-management-groups-and-subscriptions).

1. Potrzebujesz uprawnień do zapisu w grupie zarządzania ("właściciel", "Współautor" lub "Współautor grupy zarządzania"). Aby zobaczyć, jakie masz uprawnienia, wybierz grupę zarządzania, a następnie wybierz pozycję **IAM**. Aby dowiedzieć się więcej na temat ról RBAC, zobacz [Zarządzanie dostępem i uprawnieniami za pomocą RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Usuwanie w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi** > **grupy zarządzania**.

1. Wybierz grupę zarządzania, którą chcesz usunąć.

1. Wybierz pozycję **szczegóły**.

1. Wybierz pozycję **Usuń**

    > [!TIP]
    > Jeśli ikona jest wyłączona, umieszczenie selektora myszy na ikonie pokazuje przyczynę.

   ![Usuń opcję grupy](./media/delete.png)

1. Istnieje okno z potwierdzeniem, że chcesz usunąć grupę zarządzania.

   ![Okno potwierdzania usuwania grupy](./media/delete_confirm.png)

1. Wybierz pozycję **Tak**.

### <a name="delete-in-powershell"></a>Usuwanie w programie PowerShell

Użyj polecenia **Remove-AzManagementGroup** w programie PowerShell, aby usunąć grupy zarządzania.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Usuwanie w interfejsie wiersza polecenia platformy Azure

Korzystając z interfejsu wiersza polecenia platformy Azure, użyj polecenie AZ Account Management-Group Delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Wyświetlanie grup zarządzania

Można wyświetlić dowolną grupę zarządzania, w której jest włączona bezpośrednia lub dziedziczona rola RBAC.  

### <a name="view-in-the-portal"></a>Wyświetl w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi** > **grupy zarządzania**.

1. Zostanie załadowana strona hierarchii grupy zarządzania. Na tej stronie można eksplorować wszystkie grupy zarządzania i subskrypcje, do których masz dostęp. Wybranie nazwy grupy powoduje przejście do poziomu hierarchii. Nawigacja działa tak samo, jak Eksplorator plików.

1. Aby wyświetlić szczegóły grupy zarządzania, wybierz łącze **(szczegóły)** obok tytułu grupy zarządzania. Jeśli ten link nie jest dostępny, nie masz uprawnień do wyświetlania tej grupy zarządzania.

   ![Główną](./media/main.png)

### <a name="view-in-powershell"></a>Wyświetl w programie PowerShell

Aby pobrać wszystkie grupy, należy użyć polecenia Get-AzManagementGroup.  Zobacz [AZ. resources](/powershell/module/az.resources/Get-AzManagementGroup) , aby uzyskać pełną listę grup zarządzania, Pobierz polecenia programu PowerShell.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Aby uzyskać informacje o pojedynczej grupie zarządzania, użyj parametru-GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Aby zwrócić konkretną grupę zarządzania i wszystkie poziomy hierarchii w niej, użyj parametrów **-expand** i **-rekursywnie** .  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Wyświetl w interfejsie wiersza polecenia platformy Azure

Do pobrania wszystkich grup służy polecenie list.  

```azurecli-interactive
az account management-group list
```

Aby uzyskać informacje o pojedynczej grupie zarządzania, użyj polecenia show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Aby zwrócić konkretną grupę zarządzania i wszystkie poziomy hierarchii w niej, użyj parametrów **-expand** i **-rekursywnie** .

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Przeniesienie grup zarządzania i subskrypcji   

Jednym z powodów tworzenia grupy zarządzania jest łączenie subskrypcji. Tylko grupy zarządzania i subskrypcje mogą być elementami podrzędnymi innej grupy zarządzania. Subskrypcja przenoszona do grupy zarządzania odziedziczy wszystkie uprawnienia dostępu użytkowników i zasad z nadrzędnej grupy zarządzania

Podczas przeniesienia grupy zarządzania lub subskrypcji jako elementu podrzędnego innej grupy zarządzania należy ocenić trzy reguły jako prawdziwe.

Jeśli wykonujesz akcję Przenieś, potrzebujesz: 

-  Uprawnienia Zapis grup zarządzania i przypisywanie ról w podrzędnej subskrypcji lub grupie zarządzania.
    - **Właściciel** przykładu wbudowanej roli
- Dostęp do zapisu grupy zarządzania w docelowej nadrzędnej grupie zarządzania.
    - Wbudowana rola — przykład: **właściciel**, **współautor**, **współautor grupy zarządzania**
- Dostęp do zapisu grupy zarządzania w istniejącej nadrzędnej grupie zarządzania.
    - Wbudowana rola — przykład: **właściciel**, **współautor**, **współautor grupy zarządzania**

**Wyjątek**: Jeśli obiekt docelowy lub istniejąca nadrzędna grupa zarządzania jest główną grupą zarządzania, wymagania dotyczące uprawnień nie są stosowane. Ponieważ główną grupą zarządzania jest domyślny punkt załadunkowy dla wszystkich nowych grup zarządzania i subskrypcji, nie musisz mieć uprawnień do przenoszenia elementu.

Jeśli rola właściciela w subskrypcji jest dziedziczona z bieżącej grupy zarządzania, cele przenoszenia są ograniczone. Subskrypcję można przenieść tylko do innej grupy zarządzania, w której masz rolę właściciela. Nie można przenieść go do grupy zarządzania, w której jesteś współautorem, ponieważ utracisz własność subskrypcji. Jeśli masz bezpośrednio przypisaną rolę właściciela subskrypcji (niedziedziczonej z grupy zarządzania), możesz przenieść ją do dowolnej grupy zarządzania, w której jesteś współautorem. 

Aby sprawdzić, jakie uprawnienia znajdują się w Azure Portal, wybierz grupę zarządzania, a następnie wybierz pozycję **IAM**. Aby dowiedzieć się więcej na temat ról RBAC, zobacz [Zarządzanie dostępem i uprawnieniami za pomocą RBAC](../../role-based-access-control/overview.md).


## <a name="move-subscriptions"></a>Przenoszenie subskrypcji 

#### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Dodawanie istniejącej subskrypcji do grupy zarządzania w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi** > **grupy zarządzania**.

1. Wybierz grupę zarządzania, która ma być elementem nadrzędnym.

1. W górnej części strony wybierz pozycję **Dodaj subskrypcję**.

1. Wybierz subskrypcję z listy z poprawnym IDENTYFIKATORem.

   ![Dostępne subskrypcje do dodania do grupy zarządzania](./media/add_context_sub.png)

1. Wybierz pozycję "Zapisz".

#### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Usuwanie subskrypcji z grupy zarządzania w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi** > **grupy zarządzania**.

1. Wybierz zaplanowaną grupę zarządzania, która jest bieżącym elementem nadrzędnym.  

1. Wybierz wielokropek na końcu wiersza dla subskrypcji na liście, która ma zostać przeniesiona.

   ![Opcja przenoszenia w grupie zarządzania](./media/move_small.png)

1. Wybierz pozycję **Przenieś**.

1. W wyświetlonym menu wybierz **nadrzędną grupę zarządzania**.

   ![Przenieś okienko, aby zmienić grupę nadrzędną](./media/move_small_context.png)

1. Wybierz pozycję **Zapisz**.

### <a name="move-subscriptions-in-powershell"></a>Przenoszenie subskrypcji w programie PowerShell

Aby przenieść subskrypcję programu PowerShell, użyj polecenia New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Aby usunąć łącze między programem a subskrypcją i grupą zarządzania, użyj polecenia Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Przenoszenie subskrypcji w interfejsie wiersza polecenia platformy Azure

Aby przenieść subskrypcję w interfejsie wiersza polecenia, należy użyć polecenie Dodaj.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Aby usunąć subskrypcję z grupy zarządzania, użyj polecenia Usuń subskrypcję.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>Przenoszenie grup zarządzania 

### <a name="move-management-groups-in-the-portal"></a>Przenoszenie grup zarządzania w portalu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **wszystkie usługi** > **grupy zarządzania**.

1. Wybierz grupę zarządzania, która ma być elementem nadrzędnym.

1. W górnej części strony wybierz pozycję **Dodaj grupę zarządzania**.

1. W wyświetlonym menu wybierz, czy chcesz utworzyć nową, czy użyć istniejącej grupy zarządzania.

   - Wybranie opcji Nowy spowoduje utworzenie nowej grupy zarządzania.
   - Wybranie istniejącej opcji spowoduje wyświetlenie listy rozwijanej wszystkich grup zarządzania, które można przenieść do tej grupy zarządzania.  

   ![Przenoszenie grupy zarządzania do nowej lub istniejącej grupy](./media/add_context_MG.png)

1. Wybierz pozycję **Zapisz**.

### <a name="move-management-groups-in-powershell"></a>Przenoszenie grup zarządzania w programie PowerShell

Użyj polecenia Update-AzManagementGroup w programie PowerShell, aby przenieść grupę zarządzania pod inną grupę.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Przenoszenie grup zarządzania w interfejsie wiersza polecenia platformy Azure

Użyj polecenia Aktualizuj, aby przenieść grupę zarządzania przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Inspekcja grup zarządzania przy użyciu dzienników aktywności

Grupy zarządzania są obsługiwane w [dzienniku aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md). Możesz badać wszystkie zdarzenia, które wystąpiły do grupy zarządzania w tej samej centralnej lokalizacji co inne zasoby platformy Azure.  Na przykład widoczne są wszystkie przypisania ról i zmiany przypisań zasad w określonej grupie zarządzania.

![Dzienniki aktywności z grupami zarządzania](media/al-mg.png)

Jeśli chcesz wykonać zapytanie dotyczące grup zarządzania spoza witryny Azure Portal, zakres docelowy grup zarządzania wygląda tak: **„/providers/Microsoft.Management/managementGroups/{identyfikator_grupy_zarządzania}”** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Odwoływanie się do grup zarządzania od innych dostawców zasobów

Podczas odwoływania się do grup zarządzania z innych akcji dostawcy zasobów należy użyć następującej ścieżki jako zakresu. Ta ścieżka jest używana podczas korzystania z programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsów API REST.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Przykładem użycia tej ścieżki jest przypisanie nowego przypisania roli do grupy zarządzania w programie PowerShell

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Ta sama ścieżka zakresu jest używana podczas pobierania definicji zasad w grupie zarządzania.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)