---
title: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: d3ee6db382f848a029f3e0355abdfe5eecc80656
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035835"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Zarządzanie kontami bazy danych w usłudze Azure Cosmos DB

W tym artykule opisano zarządzanie kontem usługi Azure Cosmos DB. Dowiesz się, jak skonfigurować obsługę wielu regionów, dodawać i usuwać region, konfigurować wiele regionów zapisu oraz konfigurować priorytety trybu failover. 

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Konfigurowanie klientów pod kątem multihostingu

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK

```csharp
// Create a new connection policy.
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults.
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to preferred locations.
// The name of the location will match what you see in the portal, etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Sync SDK

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Set up the connection policy with your preferred regions.
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client.
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Dodawanie/usuwanie regionów z poziomu konta bazy danych

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos DB i otwórz menu **Replikuj dane globalnie**.

2. Aby dodać regiony, zaznacz na mapie sześciokąty z etykietą **+**, które odpowiadają danym regionom. Aby dodać region, wybierz opcję **+ Dodaj region** i wybierz region z menu rozwijanego.

3. Aby usunąć regiony, usuń co najmniej jeden region z mapy, wybierając niebieskie sześciokąty ze znacznikami wyboru. Możesz też wybrać ikonę „kosza na śmieci” (🗑) z prawej strony regionu.

4. Aby zapisać zmiany, wybierz pozycję **OK**.

   ![Menu dodawania lub usuwania regionów](./media/how-to-manage-database-account/add-region.png)

W trybie zapisu w jednym regionie nie można usunąć regionu zapisu. Aby można było usunąć bieżący region zapisu, należy przełączyć do trybu failover w innym regionie.

W trybie zapisu w wielu regionach można dodawać lub usuwać dowolne regiony, o ile pozostaje co najmniej jeden region.

### <a id="add-remove-regions-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Konfigurowanie wielu regionów zapisu

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Podczas tworzenia konta bazy danych upewnij się, że jest włączone ustawienie **Moduły zapisujące obsługujące wiele regionów**.

![Zrzut ekranu tworzenia konta usługi Azure Cosmos](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>Szablon usługi Resource Manager

Poniższy kod JSON jest przykładowym szablonem usługi Azure Resource Manager. Możesz go użyć do wdrożenia konta usługi Azure Cosmos DB z zasadami spójności „powiązana nieaktualność”. Maksymalny interwał nieaktualności jest ustawiany na 5 sekund. Maksymalna tolerowana liczba starych żądań jest ustawiana na 100. Aby dowiedzieć się więcej o formacie szablonu usługi Resource Manager oraz o składni, zobacz [Usługa Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Włączanie ręcznego przejścia do trybu failover dla konta usługi Azure Cosmos DB

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos DB i otwórz menu **Replikuj dane globalnie**.

2. W górnej części menu wybierz pozycję **Ręczne przejście do trybu failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W menu **Ręczne przejście do trybu failover** wybierz nowy region zapisu. Zaznacz pole wyboru, aby wskazać, że rozumiesz, iż ta opcja zmienia region zapisu.

4. Aby wyzwolić tryb failover, wybierz pozycję **OK**.

   ![Menu Ręczne przejście do trybu failover w portalu](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a id="automatic-failover"></a>Włączanie automatycznego przejścia do trybu failover dla konta usługi Azure Cosmos DB

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Z poziomu konta usługi Azure Cosmos DB otwórz okienko **Replikuj dane globalnie**. 

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**. 

4. Wybierz pozycję **Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

W tym menu można również ustawić priorytety trybu failover.

### <a id="enable-automatic-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-db-account"></a>Ustawianie priorytetów trybu failover dla konta usługi Azure Cosmos DB

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Z poziomu konta usługi Azure Cosmos DB otwórz okienko **Replikuj dane globalnie**. 

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**. 

4. Aby zmodyfikować priorytety trybu failover, przeciągnij regiony odczytu za pośrednictwem trzech kropek znajdujących się po lewej stronie wiersza, który jest wyświetlany po umieszczeniu na nich wskaźnika myszy. 

5. Wybierz pozycję **Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

W tym menu nie można modyfikować regionu zapisu. Aby ręcznie zmienić region zapisu, należy wykonać ręczne przejście do trybu failover.

### <a id="set-failover-priorities-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać poziomami spójności i konfliktami danych w usłudze Azure Cosmos DB. Zobacz następujące artykuły:

* [Zarządzanie spójnością](how-to-manage-consistency.md)
* [Zarządzanie konfliktami między regionami](how-to-manage-conflicts.md)

