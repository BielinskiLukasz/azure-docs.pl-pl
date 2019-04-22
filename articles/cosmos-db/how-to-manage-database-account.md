---
title: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: b2b5e58ca480aa3abaa0766319977b8d1160ebeb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59283005"
---
# <a name="manage-an-azure-cosmos-account"></a>Zarządzanie kontem usługi Azure Cosmos

W tym artykule opisano sposób zarządzania kontem usługi Azure Cosmos. Dowiesz się jak skonfigurować z obsługą wielu regionów, Dodaj lub Usuń region, konfigurowanie wielu regionów zapisu i konfigurowanie priorytetów trybu failover. 

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Konfigurowanie klientów pod kątem multihostingu

### <a id="configure-clients-multi-homing-dotnet">Zestaw .NET SDK w wersji 2</a>

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>Zestaw .NET SDK w wersji 3 (wersja zapoznawcza)

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>Dodawanie/usuwanie regionów z poziomu konta bazy danych

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos, a następnie otwórz **Replikuj dane globalnie** menu.

2. Aby dodać regionów, wybierz Sześciokąty na mapie za pomocą **+** etykietę, która odpowiada na regiony, które żądaną. Aby dodać region, wybierz opcję **+ Dodaj region** opcji, a następnie wybierz region, z menu rozwijanego.

3. Aby usunąć regiony, usuń co najmniej jeden region z mapy, wybierając niebieskie sześciokąty ze znacznikami wyboru. Możesz też wybrać ikonę „kosza na śmieci” (🗑) z prawej strony regionu.

4. Aby zapisać zmiany, wybierz pozycję **OK**.

   ![Menu dodawania lub usuwania regionów](./media/how-to-manage-database-account/add-region.png)

W jednym regionie tryb zapisu, nie można usunąć regionu zapisu. Użytkownik musi pracy awaryjnej w innym regionie przed usunięciem bieżącego regionu zapisu.

W wielu regionach zapisu trybu, można dodać lub usunąć dowolny region, w przypadku, jeśli masz co najmniej jednego regionu.

### <a id="add-remove-regions-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Create an account with 1 region
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
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

Poniższy kod JSON jest przykładem [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) szablonu. Służy do wdrażania przy użyciu konta usługi Azure Cosmos [powiązana nieaktualność, poziomu spójności](consistency-levels.md). Interwał maksymalna nieaktualność jest ustawiony na 5 sekund. Maksymalna liczba żądań stare, który jest tolerowane jest ustawiony na 100. Aby dowiedzieć się więcej o formacie szablonu usługi Resource Manager oraz o składni, zobacz [Usługa Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

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


## <a id="manual-failover"></a>Włączanie ręcznego przejścia do trybu failover dla konta usługi Azure Cosmos

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos, a następnie otwórz **Replikuj dane globalnie** menu.

2. W górnej części menu wybierz pozycję **Ręczne przejście do trybu failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W menu **Ręczne przejście do trybu failover** wybierz nowy region zapisu. Zaznacz pole wyboru, aby wskazać, że rozumiesz, iż ta opcja zmienia region zapisu.

4. Aby wyzwolić tryb failover, wybierz pozycję **OK**.

   ![Menu Ręczne przejście do trybu failover w portalu](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Given your account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
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

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ustawianie priorytetów trybu failover dla konta usługi Azure Cosmos

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Z konta usługi Azure Cosmos Otwórz **Replikuj dane globalnie** okienka. 

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**. 

4. Aby zmodyfikować priorytety trybu failover, przeciągnij regiony odczytu za pośrednictwem trzech kropek znajdujących się po lewej stronie wiersza, który jest wyświetlany po umieszczeniu na nich wskaźnika myszy. 

5. Wybierz pozycję **Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

W tym menu nie można modyfikować regionu zapisu. Aby ręcznie zmienić region zapisu, należy wykonać ręczne przejście do trybu failover.

### <a id="set-failover-priorities-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następujące artykuły:

* [Zarządzanie spójnością](how-to-manage-consistency.md)
* [Zarządzanie konfliktami między regionami](how-to-manage-conflicts.md)
* [Dystrybucja globalna - kulisy](global-dist-under-the-hood.md)
* [Jak skonfigurować Multi-Master w swoich aplikacjach](how-to-multi-master.md)
* [Konfigurowanie klientów w ramach wieloadresowości](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Dodać lub usunąć regiony z Twojego konta usługi Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Utwórz zasady rozwiązywania konfliktów niestandardowe](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

