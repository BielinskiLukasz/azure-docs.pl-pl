---
title: Publikowanie aplikacji zarządzanej katalogu usług
description: Przedstawia sposób tworzenia aplikacji zarządzanej platformy Azure przeznaczonej dla członków Twojej organizacji.
author: tfitzmac
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: 48aaca64949aafecff27c76ad7572b3c2fa44732
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391508"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Szybki start: tworzenie i publikowanie definicji aplikacji zarządzanej

Ten przewodnik Szybki start zawiera wprowadzenie do pracy z [aplikacjami zarządzanymi platformy Azure.](overview.md) Można utworzyć i opublikować aplikację zarządzaną, która jest przeznaczona dla członków instytucji.

Aby opublikować zarządzaną aplikację w katalogu usług, należy:

* Utwórz szablon określający zasoby wdrażane wraz z aplikacją zarządzaną.
* Zdefiniuj elementy interfejsu użytkownika portalu, stosowane podczas wdrażania aplikacji zarządzanej.
* Utwórz pakiet zip zawierający wymagane pliki szablonu.
* Wybierz użytkowników, grupy lub aplikacje, dla których jest wymagany dostęp do grupy zasobów w ramach subskrypcji użytkownika.
* Utwórz definicję aplikacji zarządzanej, wskazującą pakiet zip i zawierającą żądanie dostępu dla określonej tożsamości.

## <a name="create-the-arm-template"></a>Tworzenie szablonu ARM

Każda definicja aplikacji zarządzanej zawiera plik o nazwie **mainTemplate.json**. W tym pliku należy zdefiniować zasoby platformy Azure, które zostaną wdrożone. Szablon nie różni się od zwykłego szablonu usługi Azure Resource Manager (ARM).

Utwórz plik o nazwie **mainTemplate.json**. W nazwie jest rozróżniana wielkość liter.

Dodaj do tego pliku następujący kod JSON. Definiuje on parametry wymagane podczas tworzenia konta magazynu oraz określa właściwości tego konta magazynu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
        }
    }
}
```

Zapisz plik mainTemplate.json.

## <a name="define-your-create-experience"></a>Zdefiniuj środowisko tworzenia

Jako wydawca definiujesz środowisko portalu do tworzenia aplikacji zarządzanej. Plik **createUiDefinition.json** generuje interfejs portalu. Można zdefiniować sposób, w jaki użytkownicy dostarczają dane wejściowe dla każdego parametru przy użyciu [elementów sterujących,](create-uidefinition-elements.md) w tym rozwijanych, pól tekstowych i pól haseł.

Tworzenie pliku o nazwie **createUiDefinition.json** (Ta nazwa jest rozróżniana wielkość liter)

Dodaj następujący rozrusznik JSON do pliku i zapisz go.

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
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Aby dowiedzieć się więcej, zobacz [Wprowadzenie do tworzeniadefinicji nawij.](create-uidefinition-overview.md)

## <a name="package-the-files"></a>Pakowanie plików

Dodaj oba pliki do pliku zip o nazwie app.zip. Oba pliki muszą znajdować się na poziomie głównym pliku zip. Jeśli zostaną umieszczone w folderze, podczas tworzenia definicji aplikacji zarządzanej otrzymasz komunikat o błędzie, informujący o braku wymaganych plików.

Przekaż pakiet do dostępnej lokalizacji, w której może być używany. Musisz podać unikatową nazwę konta magazynu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Tworzenie grupy użytkowników aplikacji usługi Azure Active Directory

Następnym krokiem jest wybranie grupy użytkowników, użytkownika lub aplikacji do zarządzania zasobami dla klienta. Ta tożsamość ma uprawnienia w zarządzanej grupie zasobów zgodne z przypisaną rolą. Może to być dowolna wbudowana rola w ramach kontroli dostępu opartej na rolach (RBAC), na przykład Właściciel lub Współautor. Jeśli chcesz utworzyć nową grupę użytkowników usługi Active Directory, zobacz [Create a group and add members in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) (Tworzenie grupy i dodawanie do niej członków w usłudze Azure Active Directory).

Aby umożliwić zarządzanie zasobami, potrzebujesz identyfikatora obiektu grupy użytkowników. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Uzyskiwanie identyfikatora definicji roli

Następnie potrzebny będzie identyfikator definicji roli wbudowanej RBAC, do której chcesz udzielić dostępu użytkownikowi, grupie użytkowników lub aplikacji. Najczęściej używana jest rola Właściciel, Współautor lub Czytelnik. W następującym poleceniu przedstawiono sposób uzyskiwania identyfikatora definicji dla roli Właściciel:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

Jeśli nie istnieje jeszcze grupa zasobów do przechowywania definicji aplikacji zarządzanej, utwórz ją:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Teraz utwórz zasób definicji aplikacji zarządzanej.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

Gdy wykonywanie polecenia zakończy się, będziesz mieć w swojej grupie zasobów definicję aplikacji zarządzanej.

Niektóre z parametrów użytych w powyższym przykładzie to:

* **grupa zasobów**: Nazwa grupy zasobów, w której tworzona jest definicja aplikacji zarządzanej.
* **poziom blokady:** Typ blokady umieszczony w zarządzanej grupie zasobów. Uniemożliwia klientowi wykonywanie niepożądanych operacji na tej grupie zasobów. Obecnie jedynym obsługiwanym poziomem blokady jest ReadOnly. Gdy zostanie określony poziom ReadOnly, klient może jedynie odczytywać zasoby obecne w zarządzanej grupie zasobów. Tożsamości wydawcy, które mają dostęp do zarządzanej grupy zasobów, są wykluczone z blokady.
* **authorizations**: opisuje identyfikator podmiotu zabezpieczeń i identyfikator definicji roli, przy użyciu których udzielany jest dostęp do zarządzanej grupy zasobów. Określa się go w formacie `<principalId>:<roleDefinitionId>`. Jeśli wymagana jest więcej niż jedna wartość, należy podać wartości w formacie `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Wartości rozdziela się spacją.
* **pakiet pliku URI**: Lokalizacja pakietu .zip, który zawiera wymagane pliki.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Przynieś własny magazyn dla definicji aplikacji zarządzanej

Możesz zapisać definicję aplikacji zarządzanej na koncie magazynu dostarczonym przez użytkownika podczas tworzenia, aby jej lokalizacja i dostęp mogły być w pełni zarządzane przez użytkownika zgodnie z twoimi potrzebami regulacyjnymi.

> [!NOTE]
> Przynieś własny magazyn jest obsługiwany tylko za pomocą wdrożeń interfejsu API ARM Template lub REST definicji aplikacji zarządzanych.

### <a name="select-your-storage-account"></a>Wybierz swoje konto magazynu

Należy [utworzyć konto magazynu,](../../storage/common/storage-account-create.md) aby zawierało definicję aplikacji zarządzanej do użycia z wykazem usług.

Skopiuj identyfikator zasobu konta magazynu. Będzie on używany później podczas wdrażania definicji.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Ustawianie przypisania roli dla "Dostawcy zasobów urządzenia" na koncie magazynu

Aby definicja aplikacji zarządzanej można wdrożyć na koncie magazynu, należy przyznać uprawnienia współautora do **dostawcy zasobów urządzenia** roli dostawcy zasobów, aby można było zapisać pliki definicji do kontenera konta magazynu.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu.
1. Wybierz **formant dostępu (IAM),** aby wyświetlić ustawienia kontroli dostępu dla konta magazynu. Wybierz kartę **Przypisania ról,** aby wyświetlić listę przypisań ról.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **Współautor.** 
1. Z pola **Przypisz dostęp do** wybierz **pozycję Użytkownik, grupa lub podmiot usługi Azure AD**.
1. W obszarze **Wybierz**wyszukaj rolę **Dostawcy zasobów urządzenia** i wybierz ją.
1. Zapisz przypisanie roli.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Wdrażanie definicji aplikacji zarządzanej za pomocą szablonu ARM 

Użyj następującego szablonu ARM, aby wdrożyć spakowaną aplikację zarządzaną jako nową definicję aplikacji zarządzanej w wykazie usług, których pliki definicji są przechowywane i przechowywane na własnym koncie magazynu:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
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
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

Dodaliśmy nową właściwość o nazwie **storageAccountId** do właściwości aplikacjiDefincja i podać identyfikator konta magazynu, który chcesz przechowywać definicję jako jej wartość:

Można sprawdzić, czy pliki definicji aplikacji są zapisywane na podanym koncie magazynu w kontenerze zatytułowanym **applicationdefinitions**.

> [!NOTE]
> Aby zwiększyć bezpieczeństwo, można utworzyć definicję zarządzanych aplikacji przechowywać ją w [obiekcie blob konta magazynu platformy Azure, gdzie szyfrowanie jest włączone](../../storage/common/storage-service-encryption.md). Zawartość definicji jest szyfrowana za pomocą opcji szyfrowania konta magazynu. Tylko użytkownicy z uprawnieniami do pliku mogą zobaczyć definicję w wykazie usług.

## <a name="make-sure-users-can-see-your-definition"></a>Upewnij się, że użytkownicy będą mogli zobaczyć definicję

Masz dostęp do definicji aplikacji zarządzanej, ale chcesz mieć pewność, że inni użytkownicy w Twojej organizacji również mają do niej dostęp. Z definicji przyznaj im co najmniej rolę czytelnika. Użytkownicy mogą odziedziczyć ten poziom dostępu z subskrypcji lub grupy zasobów. Aby sprawdzić, kto ma dostęp do definicji, i dodać użytkowników lub grupy, zobacz [Używanie kontroli dostępu opartej na rolach do zarządzania dostępem do zasobów subskrypcji platformy Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

Opublikowano definicję aplikacji zarządzanej. Teraz możesz dowiedzieć się, jak wdrożyć wystąpienie tej definicji.

> [!div class="nextstepaction"]
> [Quickstart: Deploy service catalog app (Szybki start: wdrażanie aplikacji katalogu usług)](deploy-service-catalog-quickstart.md)
