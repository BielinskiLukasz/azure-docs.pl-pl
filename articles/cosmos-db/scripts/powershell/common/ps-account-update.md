---
title: Skrypt programu PowerShell w celu zaktualizowania domyślnego poziomu spójności na koncie usługi Azure Cosmos
description: Przykład skryptu programu Azure PowerShell — aktualizowanie domyślnego poziomu spójności na koncie usługi Azure Cosmos DB przy użyciu programu PowerShell
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: d3df2e91624f9b5d82d534a1d525fa6866f1a489
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366075"
---
# <a name="update-the-regions-on-an-azure-cosmos-db-account-using-powershell"></a>Aktualizowanie regionów na koncie usługi Azure Cosmos DB przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> Nie można modyfikować regionów i zmieniać innych właściwości konta usługi Cosmos w tej samej operacji. Należy to wykonać jako dwie oddzielne operacje.
> [!NOTE]
> W tym przykładzie pokazano przy użyciu konta interfejsu API SQL. Aby użyć tego przykładu dla innych interfejsów API, skopiuj powiązane właściwości i zastosuj do skryptu specyficznego dla interfejsu API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Update an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|**Azure Cosmos DB**| |
| [Konto Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Wyświetla listę kont usługi Cosmos DB lub pobiera określone konto usługi Cosmos DB. |
| [Aktualizacja-AzCosmosDBKonta](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Zaktualizuj konto usługi Cosmos DB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).
