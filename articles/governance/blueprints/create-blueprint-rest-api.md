---
title: Tworzenie strategii platformy Azure przy użyciu interfejsu API REST
description: Usługa Azure Blueprints umożliwia tworzenie, definiowanie i wdrażanie artefaktów.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/07/2018
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: b600eeff0482944a8b9b18ad39c23ee6ea4700ce
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283550"
---
# <a name="define-and-assign-an-azure-blueprint-with-rest-api"></a>Definiowanie i przypisywanie strategii platformy Azure przy użyciu interfejsu API REST

Znajomość sposobu tworzenia i przypisywania strategii umożliwia definiowanie typowych wzorców tworzenia konfiguracji wielokrotnego użytku, które można szybko wdrażać, w oparciu o szablony usługi Resource Manager, zasady, zabezpieczenia itd. Z tego samouczka dowiesz się, jak za pomocą usługi Azure Blueprints wykonywać niektóre typowe zadania związane z tworzeniem, publikowaniem i przypisywaniem strategii w organizacji, takie jak:

> [!div class="checklist"]
> - Tworzenie nowej strategii i dodawanie różnych obsługiwanych artefaktów
> - Wprowadzanie zmian do istniejącej strategii, która wciąż znajduje się w stanie **Wersja robocza**
> - Oznaczanie strategii jako gotowej do przypisania za pomocą stanu **Opublikowano**
> - Przypisywanie strategii do istniejącej subskrypcji
> - Sprawdzanie stanu i postępu przypisanej strategii
> - Usuwanie strategii, która została przypisana do subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="getting-started-with-rest-api"></a>Wprowadzenie do interfejsu API REST

Jeśli jeszcze nie znasz interfejsu API REST, zacznij od przejrzenia [dokumentacji interfejsu API REST platformy Azure](/rest/api/azure/), aby uzyskać ogólny opis interfejsu API REST, a w szczególności identyfikatora URI żądania i treści żądania. W tym artykule te pojęcia służą do podawania wskazówek dotyczących pracy z usługą Azure Blueprints, dlatego praktyczna wiedza na ich temat jest niezbędna. Narzędzia, między innymi takie jak [ARMClient](https://github.com/projectkudu/ARMClient), mogą automatycznie obsługiwać autoryzację i są zalecane dla początkujących.

Aby uzyskać informacje o specyfikacjach strategii, zobacz [Interfejs API REST usługi Azure Blueprints](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>Interfejs API REST i program PowerShell

Jeśli jeszcze nie masz narzędzia do wykonywania wywołań interfejsu API REST, rozważ wykonywanie tych instrukcji w programie PowerShell. Poniżej przedstawiono przykładowy nagłówek umożliwiający uwierzytelnianie za pomocą platformy Azure. Wygeneruj nagłówek uwierzytelniania, czasami nazywany **tokenem elementu nośnego**, i podaj identyfikator URI interfejsu API REST, z którym chcesz nawiązać połączenie, za pomocą dowolnych parametrów lub **treści żądania**:

```powershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2016-06-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Zastąp parametr `{subscriptionId}` w zmiennej **$restUri** powyżej, aby uzyskać informacje na temat Twojej subskrypcji. Zmienna $response zawiera wynik działania polecenia cmdlet `Invoke-RestMethod`, który można przeanalizować za pomocą poleceń cmdlet, takich jak [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Jeśli punkt końcowy usługi interfejsu API REST oczekuje **treści żądania**, podaj zmienną w formacie JSON dla parametru `-Body` polecenia `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. Utworzymy strategię o nazwie „MyBlueprint” służącą do konfigurowania przypisań ról i zasad dla subskrypcji. Następnie dodamy grupę zasobów, szablon usługi Resource Manager i przypisanie roli w grupie zasobów.

> [!NOTE]
> Gdy używasz interfejsu API REST, w pierwszej kolejności jest tworzony obiekt _strategii_. Dla każdego _artefaktu_ zawierającego parametry, który ma zostać dodany, parametry _strategii_ początkowej muszą zostać zdefiniowane wcześniej.

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourMG}` — zastąp nazwą swojej grupy zarządzania
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

1. Utwórz obiekt _strategii_ początkowej. **Treść żądania** zawiera właściwości strategii, wszystkie grupy zasobów, które mają zostać utworzone, oraz wszystkie parametry poziomu strategii. Parametry są określane podczas przypisywania i używane przez artefakty dodane w kolejnych krokach.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
     ```

   - Treść żądania

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

1. Dodaj przypisanie roli w subskrypcji. **Treść żądania** definiuje _rodzaj_ artefaktu, właściwości dostosowują się do identyfikatora definicji roli, a tożsamości podmiotu zabezpieczeń są przekazywane w postaci tablicy wartości. W poniższym przykładzie tożsamości podmiotu zabezpieczeń, którym przyznano określoną rolę, są konfigurowane za pomocą parametru określonego podczas przypisywania strategii.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2017-11-11-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Dodaj przypisanie zasad w subskrypcji. **Treść żądania** definiuje _rodzaj_ artefaktu, właściwości, które dostosowują się do definicji inicjatywy lub zasad, oraz konfiguruje przypisanie zasad tak, aby używało zdefiniowanych parametrów strategii, które zostaną skonfigurowane podczas przypisywania strategii.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2017-11-11-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Dodaj kolejne przypisanie zasad dla tagu magazynu (używając ponownie parametru _storageAccountType_) w subskrypcji. Ten dodatkowy artefakt przypisania zasad pokazuje, że parametr zdefiniowany w strategii może być używany przez więcej niż jeden artefakt. W tym przykładzie parametr **storageAccountType** służy do określania tagu w grupie zasobów. Ta wartość zawiera informacje o koncie magazynu, które zostanie tworzone w następnym kroku.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

1. Dodaj szablon w grupie zasobów. **Treść żądania** dla szablonu usługi Resource Manager zawiera normalny składnik JSON szablonu i definiuje docelową grupę zasobów za pomocą wartości **properties.resourceGroup**. Szablon używa również wielokrotnie parametrów strategii **storageAccountType**, **tagName** i **tagValue**, przekazując każdy z nich do szablonu. Parametry strategii są udostępniane w szablonie dzięki zdefiniowaniu wartości **properties.parameters**, a w pliku JSON szablonu ta para klucz-wartość służy do iniekcji wartości. Nazwy parametrów strategii i szablonu mogą być takie same, ale wprowadziliśmy inne, aby lepiej zilustrować sposób ich przekazywania ze strategii do artefaktu szablonu.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2017-11-11-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
                         "metadata": {
                             "description": "Storage Account type"
                         }
                     },
                     "tagNameFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag name from blueprint"
                         }
                     },
                     "tagValueFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag value from blueprint"
                         }
                     }
                 },
                 "variables": {
                     "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
                 },
                 "resources": [{
                     "type": "Microsoft.Storage/storageAccounts",
                     "name": "[variables('storageAccountName')]",
                     "apiVersion": "2016-01-01",
                     "tags": {
                        "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
                     },
                     "location": "[resourceGroup().location]",
                     "sku": {
                         "name": "[parameters('storageAccountTypeFromBP')]"
                     },
                     "kind": "Storage",
                     "properties": {}
                 }],
                 "outputs": {
                     "storageAccountSku": {
                         "type": "string",
                         "value": "[variables('storageAccountName')]"
                     }
                 }
             },
             "resourceGroup": "storageRG",
             "parameters": {
                 "storageAccountTypeFromBP": {
                     "value": "[parameters('storageAccountType')]"
                 },
                 "tagNameFromBP": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValueFromBP": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Dodaj przypisanie roli w grupie zasobów. Podobnie jak w poprzednim wpisie przypisania roli, w poniższym przykładzie użyto identyfikatora definicji dla roli **Właściciel** i podano mu inny parametr ze strategii.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu artefaktów do strategii czas na jej opublikowanie. Opublikowanie strategii umożliwia przypisanie jej do subskrypcji.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2017-11-11-preview
  ```

Wartość zmiennej `{BlueprintVersion}` jest ciągiem liter, cyfr i łączników (bez spacji czy innych znaków specjalnych) o maksymalnej długości 20 znaków. Używaj wartości, które są unikatowe i informacyjne, na przykład **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Po opublikowaniu strategii przy użyciu interfejsu API REST można przypisać ją do subskrypcji. Przypisz utworzoną przez siebie strategię do jednej z subskrypcji w Twojej hierarchii grup zarządzania. **Treść żądania** określa strategię, która ma zostać przypisana, dostarcza nazwę i lokalizację do wszystkich grup zasobów w definicji strategii oraz podaje wszystkie parametry zdefiniowane w strategii i używane przez co najmniej jeden dołączony artefakt.

1. Podaj jednostce usługi Azure Blueprint rolę **Właściciel** w subskrypcji docelowej. Identyfikator aplikacji jest statyczny (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`), ale identyfikator jednostki usługi różni się w zależności od dzierżawy. Szczegółowych informacji na temat dzierżawy można żądać, używając poniższego interfejsu API REST. Korzysta on z [interfejsu API programu Graph usługi Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md), który ma inną autoryzację.

   - Identyfikator URI interfejsu API REST

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Uruchom wdrażanie strategii, przypisując ją do subskrypcji. Ponieważ parametry **contributors** i **owners** wymagają, aby tablica identyfikatorów obiektów podmiotów zabezpieczeń miała przypisaną rolę, skorzystaj z [interfejsu API programu Grpah usługi Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md) do zbierania identyfikatorów obiektów, które będą używane w **treści żądania** na potrzeby Twoich własnych użytkowników, grup lub jednostek usługi.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
     ```

   - Treść żądania

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

## <a name="unassign-a-blueprint"></a>Cofanie przypisania strategii

Strategię można usunąć z subskrypcji. Usunięcie często przeprowadza się, gdy zasoby artefaktu przestają być potrzebne. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. Aby usunąć przypisanie strategii, wykonaj następującą operację interfejsu API REST:

- Identyfikator URI interfejsu API REST

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
  ```

## <a name="delete-a-blueprint"></a>Usuwanie strategii

Aby usunąć samą strategię, wykonaj następującą operację interfejsu API REST:

- Identyfikator URI interfejsu API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
  ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [cyklu życia strategii](./concepts/lifecycle.md)
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](./concepts/parameters.md)
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](./concepts/sequencing-order.md)
- Dowiedz się, jak używać [blokowania zasobów strategii](./concepts/resource-locking.md)
- Dowiedz się, jak [zaktualizować istniejące przypisania](./how-to/update-existing-assignments.md)
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](./troubleshoot/general.md)