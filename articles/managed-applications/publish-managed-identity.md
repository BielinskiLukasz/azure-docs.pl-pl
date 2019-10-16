---
title: Aplikacja zarządzana przez platformę Azure z zarządzaną tożsamością
description: Skonfiguruj zarządzaną aplikację przy użyciu tożsamości zarządzanej do łączenia z istniejącymi zasobami, zarządzania zasobami platformy Azure i zapewniania tożsamości operacyjnej dziennika aktywności.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 9e1f5072921104c749a0acef95b7da09f1cbb662
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330227"
---
# <a name="azure-managed-application-with-managed-identity"></a>Aplikacja zarządzana przez platformę Azure z zarządzaną tożsamością

> [!NOTE]
> Obsługa tożsamości zarządzanych dla zarządzanych aplikacji jest obecnie w wersji zapoznawczej. Użyj wersji interfejsu API 2018-09-01-Preview, aby użyć tożsamości zarządzanej.

Dowiedz się, jak skonfigurować zarządzaną aplikację w taki sposób, aby zawierała zarządzaną tożsamość. Tożsamości zarządzanej można użyć, aby umożliwić klientowi udzielenie zarządzanej aplikacji dostępu do dodatkowych istniejących zasobów. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowizacji ani rotacji żadnych wpisów tajnych. Aby uzyskać więcej informacji o tożsamościach zarządzanych w usłudze Azure Active Directory (AAD), zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

Aplikacja może mieć przyznane dwa typy tożsamości:

- **Tożsamość przypisana do systemu** jest powiązana z aplikacją i jest usuwana, jeśli aplikacja zostanie usunięta. Aplikacja może mieć tylko jedną tożsamość przypisaną do systemu.
- **Tożsamość przypisana przez użytkownika** to autonomiczny zasób platformy Azure, który można przypisać do aplikacji. Aplikacja może mieć wiele tożsamości przypisanych do użytkownika.

## <a name="how-to-use-managed-identity"></a>Jak używać tożsamości zarządzanej

Tożsamość zarządzana umożliwia wykonywanie wielu scenariuszy dla zarządzanych aplikacji. Niektóre typowe scenariusze, które mogą zostać rozwiązane, to:

- Wdrażanie zarządzanej aplikacji połączonej z istniejącymi zasobami platformy Azure. Przykładem jest Wdrażanie maszyny wirtualnej platformy Azure w ramach aplikacji zarządzanej, która jest dołączona do [istniejącego interfejsu sieciowego](../virtual-network/virtual-network-network-interface-vm.md).
- Przyznanie aplikacji zarządzanej oraz dostęp wydawcy do zasobów platformy Azure poza **zarządzaną grupą zasobów**.
- Zapewnianie operacyjnej tożsamości zarządzanych aplikacji dla dziennika aktywności i innych usług na platformie Azure.

## <a name="adding-managed-identity"></a>Dodawanie tożsamości zarządzanej

Tworzenie aplikacji zarządzanej z zarządzaną tożsamością wymaga ustawienia dodatkowej właściwości dla zasobu platformy Azure. W poniższym przykładzie przedstawiono przykładową Właściwość **tożsamości** :

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Istnieją dwa typowe sposoby tworzenia aplikacji zarządzanej przy użyciu **tożsamości**: [CreateUIDefinition. json](./create-uidefinition-overview.md) i [szablonów Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). W przypadku prostych scenariuszy pojedynczego tworzenia CreateUIDefinition należy używać do włączania tożsamości zarządzanej, ponieważ oferuje ona bogatsze środowisko. Jednak w przypadku systemów Advanced lub Complex, które wymagają zautomatyzowanego lub wielu wdrożeń aplikacji zarządzanych, można używać szablonów.

### <a name="using-createuidefinition"></a>Korzystanie z CreateUIDefinition

Zarządzaną aplikację można skonfigurować za pomocą tożsamości zarządzanej za pomocą pliku [CreateUIDefinition. JSON](./create-uidefinition-overview.md). W [sekcji dane wyjściowe](./create-uidefinition-overview.md#outputs)klucz `managedIdentity` może służyć do przesłonięcia właściwości Identity szablonu zarządzanej aplikacji. Przykładowy w postaci, w której zostanie włączona tożsamość **przypisana przez system** w zarządzanej aplikacji. Bardziej złożone obiekty tożsamości można utworzyć za pomocą elementów CreateUIDefinition, aby poprosił odbiorcę o dane wejściowe. Te dane wejściowe mogą służyć do konstruowania zarządzanych aplikacji przy użyciu **tożsamości przypisanej do użytkownika**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Kiedy używać CreateUIDefinition do zarządzanej tożsamości

Poniżej przedstawiono kilka zaleceń dotyczących używania CreateUIDefinition do włączania tożsamości zarządzanej w zarządzanych aplikacjach.

- Tworzenie aplikacji zarządzanej odbywa się za pomocą Azure Portal lub witryny Marketplace.
- Tożsamość zarządzana wymaga złożonych danych wejściowych konsumenta.
- Tożsamość zarządzana jest wymagana podczas tworzenia aplikacji zarządzanej.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Podstawowa CreateUIDefinition, która umożliwia SystemAssigned tożsamość aplikacji zarządzanej.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Podstawowa CreateUIDefinition, która pobiera zasób **tożsamości przypisany przez użytkownika** jako dane wejściowe i włącza tożsamość UserAssigned dla aplikacji zarządzanej.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

Plik CreateUIDefinition. JSON powyżej generuje środowisko użytkownika, które ma pole tekstowe odbiorcy, aby wprowadzić identyfikator zasobu platformy Azure **przypisany przez użytkownika** . Wygenerowane środowisko będzie wyglądać następująco:

![Przykładowa tożsamość przypisana przez użytkownika CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Korzystanie z szablonów Azure Resource Manager

> [!NOTE]
> Szablony aplikacji zarządzane w portalu Marketplace są generowane automatycznie dla klientów przechodzących przez Azure Portal tworzenia środowiska.
> W tych scenariuszach klucz wyjściowy `managedIdentity` w CreateUIDefinition musi być używany do włączenia tożsamości.

Tożsamość zarządzaną można również włączyć za pomocą szablonów Azure Resource Manager. Przykładowy w postaci, w której zostanie włączona tożsamość **przypisana przez system** w zarządzanej aplikacji. Bardziej złożone obiekty tożsamości można utworzyć przy użyciu parametrów szablonu Azure Resource Manager, aby zapewnić dane wejściowe. Te dane wejściowe mogą służyć do konstruowania zarządzanych aplikacji przy użyciu **tożsamości przypisanej do użytkownika**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Kiedy używać szablonów Azure Resource Manager dla tożsamości zarządzanej

Poniżej przedstawiono kilka zaleceń dotyczących używania Azure Resource Manager szablonów do włączania tożsamości zarządzanej w zarządzanych aplikacjach.

- Zarządzane aplikacje można programistycznie wdrażać na podstawie szablonu.
- Niestandardowe przypisania ról dla tożsamości zarządzanej są konieczne do aprowizacji zarządzanej aplikacji.
- Aplikacja zarządzana nie potrzebuje Azure Portal i przepływu tworzenia witryny Marketplace.

#### <a name="systemassigned-template"></a>Szablon SystemAssigned

Podstawowy szablon Azure Resource Manager, który wdraża zarządzaną aplikację przy użyciu tożsamości **przypisanej do systemu** .

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Szablon UserAssigned

Podstawowy szablon Azure Resource Manager, który wdraża zarządzaną aplikację przy użyciu **tożsamości przypisanej do użytkownika**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Udzielanie dostępu do zasobów platformy Azure

Gdy zarządzana aplikacja otrzymuje tożsamość, może uzyskać dostęp do istniejących zasobów platformy Azure. Ten proces można przeprowadzić za pomocą interfejsu kontroli dostępu (IAM) w Azure Portal. Nazwę zarządzanej aplikacji lub **tożsamości przypisanej do użytkownika** można wyszukać w celu dodania przypisania roli.

![Dodawanie przypisania roli dla aplikacji zarządzanej](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Łączenie istniejących zasobów platformy Azure

> [!NOTE]
> Przed wdrożeniem zarządzanej aplikacji należy [skonfigurować](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) **tożsamość przypisaną przez użytkownika** . Ponadto wdrożenie połączonego zasobu aplikacji zarządzanych jest obsługiwane tylko dla danego rodzaju **witryny Marketplace** .

Tożsamości zarządzanej można także użyć do wdrożenia aplikacji zarządzanej, która wymaga dostępu do istniejących zasobów podczas jej wdrażania. Gdy zarządzana aplikacja jest obsługiwana przez klienta, można dodać **tożsamości przypisane do użytkownika** , aby zapewnić dodatkowe autoryzacje wdrożenia **mainTemplate** .

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Tworzenie CreateUIDefinition z połączonym zasobem

Podczas łączenia wdrożenia aplikacji zarządzanej z istniejącymi zasobami należy dostarczyć zarówno istniejący zasób platformy Azure, jak i **tożsamość przypisaną przez użytkownika** z odpowiednim przypisaniem roli dla tego zasobu.

 Przykładowa CreateUIDefinition, która wymaga dwóch danych wejściowych: identyfikatora zasobu interfejsu sieciowego i identyfikatora zasobu tożsamości przypisanego przez użytkownika.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Ten plik CreateUIDefinition. JSON generuje środowisko użytkownika, które ma dwa pola. Pierwsze pole umożliwia użytkownikowi wprowadzanie identyfikatora zasobu platformy Azure dla zasobu połączonego z wdrożeniem aplikacji zarządzanej. Druga dotyczy konsumenta, który wprowadza identyfikator zasobu platformy Azure **przypisany przez użytkownika** , który ma dostęp do połączonego zasobu platformy Azure. Wygenerowane środowisko będzie wyglądać następująco:

![Przykład CreateUIDefinition z dwoma danymi wejściowymi: Identyfikator zasobu interfejsu sieciowego i identyfikator zasobu tożsamości przypisanego przez użytkownika](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Tworzenie mainTemplate z połączonym zasobem

Oprócz aktualizowania CreateUIDefinition należy również zaktualizować główny szablon, aby akceptował pozostały identyfikator połączonego zasobu. Szablon główny można zaktualizować, aby akceptował nowe dane wyjściowe przez dodanie nowego parametru. Ponieważ dane wyjściowe `managedIdentity` przesłaniają wartość w generowanym szablonie aplikacji zarządzanej, nie są przekazywane do szablonu głównego i nie powinny być zawarte w sekcji Parameters.

Przykładowy szablon główny, który ustawia profil sieci na istniejący interfejs sieciowy udostępniony przez CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Używanie aplikacji zarządzanej z połączonym zasobem

Po utworzeniu pakietu aplikacji zarządzanej aplikacja zarządzana może być używana przez Azure Portal. Aby można było korzystać z programu, należy wykonać kilka czynności w ramach wymagań wstępnych.

- Należy utworzyć wystąpienie wymaganego połączonego zasobu platformy Azure.
- **Tożsamość przypisana przez użytkownika** musi być [utworzona i ma nadane przypisania roli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) do połączonego zasobu.
- Istniejący identyfikator połączonego zasobu i identyfikator **tożsamości przypisany przez użytkownika** są udostępniane CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Uzyskiwanie dostępu do tokenu zarządzanej tożsamości

Do tokenu aplikacji zarządzanej można teraz uzyskać dostęp za pośrednictwem interfejsu API `listTokens` z dzierżawy wydawcy. Przykładowe żądanie może wyglądać następująco:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Parametry treści żądania:

Parametr | Wymagane | Opis
---|---|---
authorizationAudience | *znaleziono* | Identyfikator URI identyfikatora aplikacji dla zasobu docelowego. Jest to także zbiór `aud` (grupy odbiorców) wystawionego tokenu. Wartość domyślna to "https://management.azure.com/"
Resourceidentity | *znaleziono* | Lista zarządzanych tożsamości przypisanych przez użytkownika w celu pobrania tokenu dla programu. Jeśli nie zostanie określony, `listTokens` zwróci token dla tożsamości zarządzanej przypisanej do systemu.


Przykładowa odpowiedź może wyglądać następująco:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

Odpowiedź będzie zawierać tablicę tokenów pod właściwością `value`:

Parametr | Opis
---|---
access_token | Żądany token dostępu.
expires_in | Liczba sekund, przez jaką token dostępu będzie prawidłowy.
expires_on | Wartość TimeSpan w przypadku wygaśnięcia tokenu dostępu. Jest to reprezentowane przez liczbę sekund od epoki.
not_before | Przedział czasu, gdy obowiązuje token dostępu. Jest to reprezentowane przez liczbę sekund od epoki.
authorizationAudience | @No__t-0 (odbiorcy) żądanie tokenu dostępu. Jest to takie samo, jak podane w żądaniu `listTokens`.
resourceId | Identyfikator zasobu platformy Azure dla wystawionego tokenu. Jest to identyfikator aplikacji zarządzanej lub identyfikator tożsamości przypisany przez użytkownika.
token_type | Typ tokenu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak skonfigurować zarządzaną aplikację przy użyciu dostawcy niestandardowego](./custom-providers-overview.md)
