---
title: Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Cosmos DB
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: daveba
ms.openlocfilehash: ff41bbe796b561234ce5d09abfc8eb5da0e0a6b5
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220320"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Cosmos DB 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


W tym samouczku przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system dla maszyny wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Azure Cosmos DB. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie konta usługi Cosmos DB
> * Tworzenie kolekcji w ramach konta usługi Cosmos DB
> * Udzielanie dostępu przypisanej przez system tożsamości zarządzanej do wystąpienia usługi Azure Cosmos DB
> * Pobieranie elementu `principalID` przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux
> * Uzyskiwanie tokenu dostępu i używanie go do wywoływania usługi Azure Resource Manager
> * Uzyskiwanie kluczy dostępu z usługi Azure Resource Manager w celu wykonywania wywołań usługi Cosmos DB

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Zalogowanie się w witrynie Azure Portal](https://portal.azure.com)

- [Utworzenie maszyny wirtualnej z systemem Linux](/azure/virtual-machines/linux/quick-create-portal)

- [Włączenie tożsamości zarządzanej przypisanej przez system na maszynie wirtualnej](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia w tym samouczku, masz dwie opcje:

- Użyj usługi [Azure Cloud Shell](~/articles/cloud-shell/overview.md) w witrynie Azure Portal lub za pośrednictwem przycisku **Wypróbuj** znajdującego się w prawym górnym rogu każdego bloku kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia.

## <a name="create-a-cosmos-db-account"></a>Tworzenie konta usługi Cosmos DB 

Jeśli jeszcze nie masz konta usługi Cosmos DB, utwórz je. Możesz pominąć ten krok i użyć istniejącego konta usługi Cosmos DB. 

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij kolejno pozycje **Bazy danych** i **Azure Cosmos DB**. Zostanie wyświetlony panel „Nowe konto”.
3. Wprowadź **identyfikator** konta usługi Cosmos DB do późniejszego użycia.  
4. **Interfejs API** należy ustawić na „SQL”. Podejście opisane w tym samouczku można stosować w przypadku innych dostępnych typów interfejsu API, ale kroki opisane w tym samouczku dotyczą interfejsu API SQL.
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.  Wybierz **lokalizację**, w której jest dostępna usługa Cosmos DB.
6. Kliknij pozycję **Utwórz**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Tworzenie kolekcji w ramach konta usługi Cosmos DB

Następnie na koncie usługi Cosmos DB dodaj kolekcję danych, dla której możesz utworzyć zapytania w kolejnych krokach.

1. Przejdź do nowo utworzonego konta usługi Cosmos DB.
2. Na karcie **Omówienie** kliknij przycisk **+/Dodaj kolekcję**. Zostanie wysunięty panel „Dodawanie kolekcji”.
3. Nadaj kolekcji identyfikator bazy danych i identyfikator kolekcji, wybierz pojemność magazynu, wprowadź klucz partycji, wprowadź wartość przepływności, a następnie kliknij przycisk **OK**.  W tym samouczku wystarczy użyć identyfikatora bazy danych i identyfikatora kolekcji „Test”, a następnie wybrać stałą pojemność magazynu i najniższą przepływność (400 RU/s).  

## <a name="retrieve-the-principalid-of-the-linux-vms-system-assigned-managed-identity"></a>Pobieranie elementu `principalID` przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux

Aby uzyskać dostęp do kluczy dostępu do konta usługi Cosmos DB z poziomu usługi Resource Manager w poniższej sekcji, musisz pobrać element `principalID` przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux.  Upewnij się, że wartości parametrów `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (grupa zasobów, do której należy maszyna wirtualna) i `<VM NAME>` zostały zastąpione własnymi wartościami.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
Odpowiedź zawiera szczegółowe informacje o przypisanej przez system tożsamości zarządzanej (zanotuj identyfikator principalID, ponieważ będzie używany w następnej sekcji):

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>Udzielanie dostępu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux do kluczy dostępu do konta usługi Cosmos DB

Usługa Cosmos DB nie zapewnia natywnej obsługi uwierzytelniania usługi Azure AD. Możesz jednak użyć tożsamości zarządzanej, aby pobrać klucz dostępu do konta usługi Cosmos DB z usługi Resource Manager, a następnie użyć klucza do uzyskania dostępu do usługi Cosmos DB. W tym kroku udzielasz przypisanej przez system tożsamości zarządzanej dostępu do kluczy do konta usługi Cosmos DB.

Aby udzielić przypisanej przez system tożsamości zarządzanej dostępu do konta usługi Cosmos DB w usłudze Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure, zaktualizuj wartości `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` i `<COSMOS DB ACCOUNT NAME>` dla swojego środowiska. Zastąp element `<MI PRINCIPALID>` właściwością `principalId` zwróconą przez polecenie `az resource show` w części [Pobieranie identyfikatora principalID przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux](#retrieve-the-principalID-of-the-linux-VM's-system-assigned-identity).  W przypadku korzystania z kluczy dostępu usługa Cosmos DB obsługuje dwa poziomy szczegółowości: dostęp do odczytu/zapisu na koncie i dostęp tylko do odczytu do konta.  Przypisz rolę `DocumentDB Account Contributor`, jeśli chcesz uzyskać klucze odczytu/zapisu dla konta, lub przypisz rolę `Cosmos DB Account Reader Role`, jeśli chcesz uzyskać klucze tylko do odczytu dla konta:

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Odpowiedź zawiera szczegóły utworzonego przypisania roli:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-linux-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Linux i używanie jej do wywołania usługi Azure Resource Manager

W pozostałej części tego samouczka będziesz pracować z poziomu wcześniej utworzonej maszyny wirtualnej.

Aby wykonać te kroki, potrzebujesz klienta SSH. Jeśli używasz systemu Windows, możesz użyć klienta SSH w pozycji [Podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Jeżeli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md) lub [Jak utworzyć i użyć parę publicznego i prywatnego klucza SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W witrynie Azure Portal przejdź do pozycji **Maszyny wirtualne**, przejdź do maszyny wirtualnej z systemem Linux, a następnie na stronie **Przegląd** kliknij opcję **Połącz** u góry. Skopiuj ciąg, aby nawiązać połączenie z maszyną wirtualną. 
2. Połącz się z maszyną wirtualną przy użyciu klienta SSH.  
3. Następnie zobaczysz monit o wprowadzenie **hasła** dodanego podczas tworzenia **maszyny wirtualnej z systemem Linux**. Logowanie powinno wtedy zostać pomyślnie wykonane.  
4. Użyj programu CURL, aby uzyskać token dostępu dla usługi Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > W poprzednim żądaniu wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Azure Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI.
    > W poniższej odpowiedzi element access_token został skrócony, aby zapewnić zwięzłość informacji.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Uzyskiwanie kluczy dostępu z usługi Azure Resource Manager w celu wykonywania wywołań usługi Cosmos DB  

Teraz użyj programu CURL, aby wywołać usługę Resource Manager przy użyciu tokenu dostępu pozyskanego w poprzedniej sekcji, aby pobrać klucz dostępu do usługi Cosmos DB. Gdy będziemy już mieć klucz dostępu, będzie można wykonać zapytanie dotyczące usługi Cosmos DB. Pamiętaj, aby zastąpić parametry `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` i `<COSMOS DB ACCOUNT NAME>` własnymi wartościami. Zastąp wartość `<ACCESS TOKEN>` tokenem dostępu pobranym wcześniej.  Jeśli chcesz pobrać klucze odczytu/zapisu, użyj typu operacji klucza `listKeys`.  Jeśli chcesz pobrać klucze tylko do odczytu, użyj typu operacji klucza `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> W tekście w poprzednim adresie URL rozróżniana jest wielkość liter, więc upewnij się, że stosujesz odpowiednie małe i wielkie litery dla grup zasobów, aby odpowiednio je przedstawić. Ponadto ważna informacja jest taka, że jest to żądanie POST, a nie GET. Należy upewnić się, że zostaje przekazana wartość do przechwytywania limitu długości z parametrem -d, który może mieć wartość NULL.  

Odpowiedź programu CURL zapewnia listę kluczy.  Jeśli pobierasz klucze tylko do odczytu:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Teraz, gdy masz klucz dostępu do konta usługi Cosmos DB, możesz przekazać go do zestawu SDK usługi Cosmos DB i wykonywać wywołania w celu uzyskania dostępu do konta.  Szybki przykład to przekazanie klucza dostępu do wiersza polecenia platformy Azure.  Element <COSMOS DB CONNECTION URL> można uzyskać na karcie **Omówienie** karty w bloku konta usługi Cosmos DB w witrynie Azure Portal.  Zastąp element wartością <ACCESS KEY> uzyskaną powyżej:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

To polecenie interfejsu wiersza polecenia zwraca szczegółowe informacje o kolekcji:

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Linux w celu uzyskania dostępu do usługi Cosmos DB.  Aby dowiedzieć się więcej o usłudze Cosmos DB, zobacz:

> [!div class="nextstepaction"]
>[Omówienie usługi Azure Cosmos DB](/azure/cosmos-db/introduction)

