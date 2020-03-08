---
title: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać zasobami Azure Cosmos DB przy użyciu szablonów Azure Portal, PowerShell, interfejsu wiersza polecenia i Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356548"
---
# <a name="manage-an-azure-cosmos-account"></a>Zarządzanie kontem usługi Azure Cosmos

W poniższym artykule opisano sposób zarządzania różnymi zadaniami w ramach konta usługi Azure Cosmos przy użyciu witryny Azure Portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i szablonów usługi Azure Resource Manager.

## <a name="create-an-account"></a>Tworzenie konta

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Tworzenie konta Azure Cosmos dB przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Zobacz [Tworzenie konta Azure Cosmos dB przy użyciu programu PowerShell](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Szablon Azure Resource Manager

Ten szablon Azure Resource Manager utworzy konto usługi Azure Cosmos dla interfejsu API SQL skonfigurowany z dwoma regionami i opcjami w celu wybrania poziomu spójności, automatycznej pracy awaryjnej i wielu wzorców. Aby wdrożyć ten szablon, kliknij pozycję Wdróż na platformie Azure na stronie Readme, [Utwórz konto usługi Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Dodawanie/usuwanie regionów z poziomu konta bazy danych

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Zaloguj się w [portalu Azure](https://portal.azure.com).

1. Przejdź do konta usługi Azure Cosmos i otwórz menu **Replikacja danych globalnie** .

1. Aby dodać regiony, zaznacz sześciokąty na mapie z etykietą **+** odpowiadającą żądanym regionom. Alternatywnie, aby dodać region, wybierz opcję **+ Dodaj region** i wybierz region z menu rozwijanego.

1. Aby usunąć regiony, usuń co najmniej jeden region z mapy, wybierając niebieskie sześciokąty ze znacznikami wyboru. Możesz też wybrać ikonę „kosza na śmieci” (🗑) z prawej strony regionu.

1. Aby zapisać zmiany, wybierz pozycję **OK**.

   ![Menu dodawania lub usuwania regionów](./media/how-to-manage-database-account/add-region.png)

W trybie zapisu w jednym regionie nie można usunąć regionu zapisu. Aby można było usunąć bieżący region zapisu, należy przejść do trybu failover w innym regionie.

W trybie zapisu w wielu regionach można dodawać lub usuwać dowolne regiony, o ile pozostaje co najmniej jeden region.

### <a id="add-remove-regions-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Dodawanie lub usuwanie regionów przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

Zobacz [Dodawanie lub usuwanie regionów przy użyciu programu PowerShell](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Konfigurowanie wielu regionów zapisu

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Otwórz kartę **Replikuj dane globalnie** i wybierz pozycję **Włącz** , aby włączyć zapisywanie w ramach wieloregionu. Po włączeniu zapisów obejmujących wiele regionów wszystkie regiony odczytu znajdujące się obecnie na koncie staną się regionami odczytu i zapisu.

![Konto usługi Azure Cosmos służy do konfigurowania wieloskładnikowego zrzutu ekranu](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Włączanie regionów wielu zapisów za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Zobacz [Włączanie regionów wielu zapisów przy użyciu programu PowerShell](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>Szablon usługi Resource Manager

Konto można migrować z jednego serwera głównego do wielu wzorców, wdrażając szablon Menedżer zasobów używany do tworzenia konta i ustawienia `enableMultipleWriteLocations: true`. Następujący szablon Azure Resource Manager jest nieminimalnym szablonem, który spowoduje wdrożenie konta Azure Cosmos dla interfejsu API SQL z dwoma regionami i włączenie wielu lokalizacji zapisu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Włączanie automatycznego przejścia do trybu failover dla konta usługi Azure Cosmos

Opcja automatycznej pracy awaryjnej umożliwia Azure Cosmos DB przełączenia w tryb failover do regionu z najwyższym priorytetem trybu failover bez akcji użytkownika, jeśli region staje się niedostępny. Gdy włączona jest automatyczna tryb failover, priorytet regionu można modyfikować. Aby można było włączyć automatyczne przełączanie do trybu failover, konto musi mieć co najmniej dwa regiony.

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Na koncie usługi Azure Cosmos Otwórz okienko **Replikuj dane globalnie** .

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**. 

4. Wybierz pozycję **Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Włączanie automatycznego przełączania do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Zobacz [Włączanie automatycznego przełączania do trybu failover przy użyciu programu PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ustawianie priorytetów trybu failover dla konta usługi Azure Cosmos

Po skonfigurowaniu konta usługi Cosmos do automatycznego przełączania w tryb failover priorytet trybu failover dla regionów można zmienić.

> [!IMPORTANT]
> Nie można zmodyfikować regionu zapisu (priorytet trybu failover równy zero), jeśli konto jest skonfigurowane do automatycznego przełączania do trybu failover. Aby zmienić region zapisu, należy wyłączyć automatyczne przełączanie do trybu failover i przeprowadzić ręczną pracę awaryjną.

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Na koncie usługi Azure Cosmos Otwórz okienko **Replikuj dane globalnie** .

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**.

4. Aby zmodyfikować priorytety trybu failover, przeciągnij regiony odczytu za pośrednictwem trzech kropek znajdujących się po lewej stronie wiersza, który jest wyświetlany po umieszczeniu na nich wskaźnika myszy.

5. Wybierz pozycję **Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Ustawianie priorytetu trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Zobacz [Ustawianie priorytetu trybu failover przy użyciu programu PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Wykonaj ręczną pracę awaryjną na koncie usługi Azure Cosmos

> [!IMPORTANT]
> Aby ta operacja zakończyła się pomyślnie, należy skonfigurować konto usługi Azure Cosmos do ręcznego przełączania do trybu failover.

Proces przeprowadzania ręcznej pracy awaryjnej polega na zmianie regionu zapisu konta (priorytetu trybu failover = 0) w innym regionie skonfigurowanym dla tego konta.

> [!NOTE]
> Kont wielu wzorców nie można przełączyć ręcznie do trybu failover. W przypadku aplikacji korzystających z zestawu SDK usługi Azure Cosmos zestaw SDK wykrywa, kiedy region stanie się niedostępny, a następnie przekierowuje automatycznie do następnego najbliższego regionu, jeśli używany jest interfejs API multihostingu w zestawie SDK.

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos i otwórz menu **Replikacja danych globalnie** .

2. W górnej części menu wybierz pozycję **Ręczne przejście do trybu failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W menu **Ręczne przejście do trybu failover** wybierz nowy region zapisu. Zaznacz pole wyboru, aby wskazać, że rozumiesz, iż ta opcja zmienia region zapisu.

4. Aby wyzwolić tryb failover, wybierz pozycję **OK**.

   ![Menu Ręczne przejście do trybu failover w portalu](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [wyzwalanie ręcznego przełączania do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Zobacz [wyzwalanie ręcznego przełączania w tryb failover przy użyciu programu PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i zapoznać się z przykładami dotyczącymi zarządzania kontem usługi Azure Cosmos oraz bazą danych i kontenerami, przeczytaj następujące artykuły:

* [Zarządzanie Azure Cosmos DB przy użyciu Azure PowerShell](manage-with-powershell.md)
* [Zarządzanie usługą Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md)
