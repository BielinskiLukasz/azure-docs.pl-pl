---
title: Zablokuj zasoby, aby uniemożliwić zmiany
description: Zablokuj użytkownikom możliwość aktualizowania lub usuwania krytycznych zasobów platformy Azure, stosując blokadę dla wszystkich użytkowników i ról.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384548"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Zablokuj zasoby, aby zapobiec nieoczekiwanym zmianom

Jako administrator możesz zablokować subskrypcję, grupę zasobów lub zasób, aby zapobiec przypadkowemu usunięciu lub zmodyfikowaniu kluczowych zasobów przez innych użytkowników w organizacji. Poziom blokady można ustawić na wartość **CanNotDelete** lub **ReadOnly**. W portalu blokady są nazywane odpowiednio **usuwaniem** i **tylko do odczytu** .

* **CanNotDelete** oznacza, że autoryzowani użytkownicy nadal mogą odczytywać i modyfikować zasób, ale nie mogą usunąć tego zasobu. 
* **ReadOnly** oznacza, że autoryzowani użytkownicy mogą odczytywać zasoby, ale nie mogą usuwać ani aktualizować zasobu. Zastosowanie tej blokady jest podobne do ograniczenia wszystkich autoryzowanych użytkowników do uprawnień udzielonych przez rolę **czytelnika** .

## <a name="how-locks-are-applied"></a>Jak są stosowane blokady

W przypadku zastosowania blokady w zakresie nadrzędnym wszystkie zasoby w tym zakresie dziedziczą tę samą blokadę. Nawet zasoby dodawane później dziedziczą blokadę z elementu nadrzędnego. Pierwszeństwo ma najbardziej restrykcyjną blokadę dziedziczenia.

W przeciwieństwie do kontroli dostępu opartej na rolach, można użyć blokad zarządzania do zastosowania ograniczeń dla wszystkich użytkowników i ról. Aby dowiedzieć się więcej o ustawianiu uprawnień dla użytkowników i ról, zobacz [Access Control oparte na rolach platformy Azure](../../role-based-access-control/role-assignments-portal.md).

Blokady Menedżer zasobów dotyczą tylko operacji wykonywanych w płaszczyźnie zarządzania, która składa się z operacji wysyłanych do `https://management.azure.com`. Blokady nie ograniczają sposobu wykonywania własnych funkcji przez zasoby. Zmiany zasobów są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład blokada tylko do odczytu na SQL Database uniemożliwia usunięcie lub zmodyfikowanie bazy danych. Nie uniemożliwia to tworzenia, aktualizowania ani usuwania danych w bazie danych. Transakcje danych są dozwolone, ponieważ te operacje nie są wysyłane do `https://management.azure.com`.

Zastosowanie **tylko do odczytu** może prowadzić do nieoczekiwanych wyników, ponieważ niektóre operacje, które nie wydają się do modyfikacji zasobu, rzeczywiście wymagają akcji blokowanych przez blokadę. Blokadę **ReadOnly** można zastosować do zasobu lub do grupy zasobów zawierającej zasób. Niektóre typowe przykłady operacji blokowanych przez blokadę tylko do **odczytu** są następujące:

* Blokada **tylko do odczytu** na koncie magazynu uniemożliwia wszystkim użytkownikom wyświetlanie kluczy. Operacja Lista kluczy jest obsługiwana za pomocą żądania POST, ponieważ zwracane klucze są dostępne dla operacji zapisu.

* Blokada **tylko do odczytu** w zasobie App Service uniemożliwia programowi Visual Studio Eksplorator serwera wyświetlanie plików dla zasobu, ponieważ ta interakcja wymaga dostępu do zapisu.

* Blokada **tylko do odczytu** w grupie zasobów zawierającej maszynę wirtualną uniemożliwia wszystkim użytkownikom uruchamianie lub ponowne uruchamianie maszyny wirtualnej. Te operacje wymagają żądania POST.

## <a name="who-can-create-or-delete-locks"></a>Kto może tworzyć lub usuwać blokady

Aby utworzyć lub usunąć blokady zarządzania, musisz mieć dostęp do akcji `Microsoft.Authorization/*` lub `Microsoft.Authorization/locks/*`. Spośród wbudowanych ról tylko **Właściciel** i **Administrator dostępu użytkowników** mają dostęp do tych akcji.

## <a name="managed-applications-and-locks"></a>Zarządzane aplikacje i blokady

Niektóre usługi platformy Azure, takie jak Azure Databricks, używają [aplikacji zarządzanych](../managed-applications/overview.md) do implementowania usługi. W takim przypadku usługa tworzy dwie grupy zasobów. Jedna grupa zasobów zawiera przegląd usługi i nie jest zablokowana. Inna grupa zasobów zawiera infrastrukturę usługi i jest zablokowana.

Jeśli spróbujesz usunąć grupę zasobów infrastruktury, zostanie wyświetlony komunikat o błędzie informujący, że grupa zasobów jest zablokowana. Jeśli spróbujesz usunąć blokadę dla grupy zasobów infrastruktury, zostanie wyświetlony komunikat o błędzie informujący, że nie można usunąć blokady, ponieważ należy ona do aplikacji systemowej.

Zamiast tego należy usunąć usługę, która również usuwa grupę zasobów infrastruktury.

W przypadku aplikacji zarządzanych wybierz wdrożoną usługę.

![Wybieranie usługi](./media/lock-resources/select-service.png)

Zwróć uwagę, że usługa zawiera link do **zarządzanej grupy zasobów**. Ta grupa zasobów zawiera infrastrukturę i jest zablokowana. Nie można go bezpośrednio usunąć.

![Pokaż grupę zarządzaną](./media/lock-resources/show-managed-group.png)

Aby usunąć wszystkie elementy usługi, w tym zablokowaną grupę zasobów infrastruktury, wybierz pozycję **Usuń** dla usługi.

![Usuń usługę](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Kopie zapasowe i blokady platformy Azure

Jeśli zablokujesz grupę zasobów utworzoną przez usługę Azure Backup, kopie zapasowe będą kończyć się niepowodzeniem. Usługa obsługuje maksymalnie 18 punktów przywracania. W przypadku blokady **CanNotDelete** usługa Backup nie może oczyścić punktów przywracania. Aby uzyskać więcej informacji, zobacz [często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](../../backup/backup-azure-vm-backup-faq.md).

## <a name="portal"></a>wielodostępowy

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Szablon

W przypadku użycia szablonu Menedżer zasobów do wdrożenia blokady należy użyć różnych wartości dla nazwy i typu w zależności od zakresu blokady.

W przypadku zastosowania blokady do **zasobu**należy użyć następujących formatów:

* Nazwa — `{resourceName}/Microsoft.Authorization/{lockName}`
* Typ — `{resourceProviderNamespace}/{resourceType}/providers/locks`

W przypadku stosowania blokady do **grupy zasobów** lub **subskrypcji**należy użyć następujących formatów:

* Nazwa — `{lockName}`
* Typ — `Microsoft.Authorization/locks`

Poniższy przykład przedstawia szablon, który tworzy plan usługi App Service, witrynę sieci Web i blokadę w witrynie sieci Web. Typ zasobu blokady to typ zasobu zasobu do zablokowania i **/providers/Locks**. Nazwa blokady jest tworzona przez połączenie nazwy zasobu z **/Microsoft.Authorization/** i nazwę blokady.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Aby zapoznać się z przykładem ustawienia blokady grupy zasobów, zobacz [Tworzenie grupy zasobów i blokowanie jej](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>Program PowerShell
Wdrożone zasoby można blokować za pomocą Azure PowerShell przy użyciu polecenia [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Aby zablokować zasób, podaj nazwę zasobu, jego typ zasobu i nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Aby uzyskać informacje na temat blokady, użyj polecenie [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Aby uzyskać wszystkie blokady w ramach subskrypcji, użyj:

```azurepowershell-interactive
Get-AzResourceLock
```

Aby uzyskać wszystkie blokady dla zasobu, użyj:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Aby usunąć blokadę, użyj:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Wdrożone zasoby można zablokować za pomocą polecenia [AZ Lock Create](/cli/azure/lock#az-lock-create) .

Aby zablokować zasób, podaj nazwę zasobu, jego typ zasobu i nazwę grupy zasobów.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Aby zablokować grupę zasobów, podaj nazwę grupy zasobów.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Aby uzyskać informacje na temat blokady, użyj [AZ Lock list](/cli/azure/lock#az-lock-list). Aby uzyskać wszystkie blokady w ramach subskrypcji, użyj:

```azurecli
az lock list
```

Aby uzyskać wszystkie blokady dla zasobu, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Aby uzyskać wszystkie blokady dla grupy zasobów, użyj:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Aby usunąć blokadę, użyj:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>Interfejs API REST
Wdrożone zasoby można zablokować za pomocą [interfejsu API REST dla blokad zarządzania](https://docs.microsoft.com/rest/api/resources/managementlocks). Interfejs API REST umożliwia tworzenie i usuwanie blokad oraz pobieranie informacji o istniejących blokadach.

Aby utworzyć blokadę, uruchom polecenie:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Zakresem może być subskrypcja, Grupa zasobów lub zasób. Nazwa blokady jest taka, którą chcesz wywołać blokadę. W przypadku interfejsu API-Version należy użyć **2016-09-01**.

W żądaniu Dołącz obiekt JSON, który określa właściwości blokady.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się, jak logicznie organizować zasoby, zobacz [Używanie tagów do organizowania zasobów](tag-resources.md)
* Ograniczenia i konwencje w ramach subskrypcji można stosować przy użyciu zasad niestandardowych. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure Policy?](../../governance/policy/overview.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

