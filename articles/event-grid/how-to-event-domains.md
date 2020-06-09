---
title: Publikowanie zdarzeń przy użyciu domen zdarzeń z Azure Event Grid
description: Pokazuje, jak zarządzać dużymi zestawami tematów w Azure Event Grid i publikować zdarzenia do nich za pomocą domen zdarzeń.
services: event-grid
author: femila
ms.service: event-grid
ms.author: femila
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 10a0b138c702d4e4d09608c42959c68e97790f81
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560988"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Zarządzanie tematami i publikowanie zdarzeń przy użyciu domen zdarzeń

W tym artykule pokazano, jak:

* Tworzenie domeny Event Grid
* Subskrybowanie do tematów usługi Event Grid
* Klucze list
* Publikowanie zdarzeń w domenie

Aby dowiedzieć się więcej o domenach zdarzeń, zobacz [Opis domen zdarzeń do zarządzania Event Grid tematami](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Tworzenie domeny zdarzeń

Aby zarządzać dużymi zestawami tematów, Utwórz domenę zdarzeń.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

Pomyślne utworzenie zwraca następujące wartości:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Należy pamiętać, że `endpoint` `id` są one wymagane do zarządzania domeną i publikowania zdarzeń.

## <a name="manage-access-to-topics"></a>Zarządzanie dostępem do tematów

Zarządzanie dostępem do tematów odbywa się za pomocą [przypisywania ról](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Przypisanie roli korzysta z kontroli dostępu opartej na rolach w celu ograniczenia operacji na zasobach platformy Azure do autoryzowanych użytkowników w określonym zakresie.

Event Grid ma dwie wbudowane role, których można użyć do przypisywania określonych użytkowników w różnych tematach w domenie. Role te to `EventGrid EventSubscription Contributor (Preview)` , które umożliwiają tworzenie i usuwanie subskrypcji oraz `EventGrid EventSubscription Reader (Preview)` , które pozwalają na wyświetlanie listy subskrypcji zdarzeń.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)
Poniższe polecenie interfejsu wiersza polecenia platformy Azure `alice@contoso.com` umożliwia tworzenie i usuwanie subskrypcji zdarzeń tylko w temacie `demotopic1` :

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)
Poniższe polecenia programu PowerShell są ograniczone `alice@contoso.com` do tworzenia i usuwania subskrypcji zdarzeń tylko w temacie `demotopic1` :

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Aby uzyskać więcej informacji na temat zarządzania dostępem do Event Grid operacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Tworzenie tematów i subskrypcji

Usługa Event Grid automatycznie tworzy odpowiedni temat w domenie i zarządza nim w oparciu o wywołanie tworzenia subskrypcji zdarzeń dla tematu domeny. Nie ma oddzielnego kroku do utworzenia tematu w domenie. Podobnie po usunięciu ostatniej subskrypcji zdarzeń dla tematu również temat zostaje usunięty.

Subskrybowanie tematu w domenie jest takie samo jak subskrybowanie innych zasobów platformy Azure. W polu Identyfikator zasobu źródłowego Określ identyfikator domeny zdarzenia zwracany podczas tworzenia domeny wcześniej. Aby określić temat, który chcesz subskrybować, Dodaj `/topics/<my-topic>` na końcu identyfikatora zasobu źródłowego. Aby utworzyć subskrypcję zdarzeń zakresu domeny, która odbiera wszystkie zdarzenia w domenie, określ identyfikator domeny zdarzeń bez określania żadnych tematów.

Zwykle użytkownik, do którego udzielono dostępu w poprzedniej sekcji, utworzy subskrypcję. Aby uprościć ten artykuł, Utwórz subskrypcję. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Jeśli potrzebujesz testowego punktu końcowego, aby subskrybować Twoje zdarzenia, możesz zawsze wdrożyć wstępnie utworzoną [aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer) , która wyświetla zdarzenia przychodzące. Zdarzenia można wysyłać do witryny testowej w witrynie `https://<your-site-name>.azurewebsites.net/api/updates` .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Uprawnienia ustawione dla tematu są przechowywane w Azure Active Directory i muszą zostać usunięte jawnie. Usunięcie subskrypcji zdarzeń nie spowoduje odwołania użytkowników, którzy mają dostęp do zapisu w temacie.


## <a name="publish-events-to-an-event-grid-domain"></a>Publikowanie zdarzeń w domenie Event Grid

Publikowanie zdarzeń w domenie jest takie samo jak [Publikowanie w temacie niestandardowym](./post-to-custom-topic.md). Jednak zamiast publikować w temacie niestandardowym, należy opublikować wszystkie zdarzenia w punkcie końcowym domeny. W danych zdarzenia JSON należy określić temat, do którego mają być przekazywane zdarzenia. Następująca tablica zdarzeń spowodowałaby zaistnienie zdarzenia w `"id": "1111"` temacie w `demotopic1` czasie, gdy zdarzenie `"id": "2222"` zostanie wysłane do tematu `demotopic2` :

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)
Aby uzyskać punkt końcowy domeny za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Aby uzyskać klucze dla domeny, należy użyć:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)
Aby uzyskać punkt końcowy domeny za pomocą programu PowerShell, użyj polecenia

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Aby uzyskać klucze dla domeny, należy użyć:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Następnie użyj ulubionej metody tworzenia żądania HTTP POST, aby opublikować zdarzenia w domenie Event Grid.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o pojęciach dotyczących wysokiego poziomu w domenach zdarzeń oraz o tym, dlaczego są one przydatne, zobacz [Omówienie pojęć dotyczących domen zdarzeń](event-domains.md).
