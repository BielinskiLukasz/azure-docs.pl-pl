---
title: Wdrażanie szablonu Azure Resource Manager w elemencie Runbook Azure Automation
description: Jak wdrożyć szablon Azure Resource Manager przechowywany w usłudze Azure Storage z elementu Runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: PowerShell, Runbook, JSON, Automatyzacja Azure
ms.openlocfilehash: 922a4e8d98405de9e2b8420da4abf0e157011546
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850928"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Wdrażanie szablonu usługi Azure Resource Manager w elemencie runbook programu PowerShell usługi Azure Automation

Można napisać [Azure Automation element Runbook programu PowerShell](automation-first-runbook-textual-powershell.md) , który wdraża zasób platformy Azure za pomocą [szablonu usługi Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Dzięki temu można zautomatyzować wdrażanie zasobów platformy Azure. Szablony Menedżer zasobów można obsługiwać w centralnej, bezpiecznej lokalizacji, takiej jak usługa Azure Storage.

W tym artykule opisano Tworzenie elementu Runbook programu PowerShell, który używa szablonu Menedżer zasobów przechowywanego w [usłudze Azure Storage](../storage/common/storage-introduction.md) w celu wdrożenia nowego konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli jeszcze jej nie masz, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub [utworzyć bezpłatne konto](https://azure.microsoft.com/free/).
* [Konto usługi Automation](automation-sec-configure-azure-runas-account.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure.  To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* [Konto magazynu platformy Azure](../storage/common/storage-create-storage-account.md) , w którym ma być przechowywany szablon Menedżer zasobów
* Program Azure PowerShell został zainstalowany na komputerze lokalnym. Aby uzyskać informacje na temat pobierania Azure PowerShell [, zobacz Instalowanie i Konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) .

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym przykładzie używamy szablonu Menedżer zasobów, który wdraża nowe konto usługi Azure Storage.

W edytorze tekstów Skopiuj następujący tekst:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Zapisz plik lokalnie jako `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Zapisz szablon Menedżer zasobów w usłudze Azure Storage

Teraz używamy programu PowerShell do utworzenia udziału plików usługi Azure Storage i przekazania pliku `TemplateTest.json`.
Aby uzyskać instrukcje dotyczące sposobu tworzenia udziału plików i przekazywania pliku w Azure Portal, zobacz Rozpoczynanie [pracy z usługą Azure File Storage w systemie Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Uruchom program PowerShell na maszynie lokalnej i uruchom następujące polecenia, aby utworzyć udział plików i przekazać szablon Menedżer zasobów do tego udziału plików.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Utwórz skrypt elementu Runbook programu PowerShell

Teraz tworzymy skrypt programu PowerShell, który pobiera plik `TemplateTest.json` z usługi Azure Storage i wdraża szablon w celu utworzenia nowego konta usługi Azure Storage.

W edytorze tekstów wklej następujący tekst:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Zapisz plik lokalnie jako `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importowanie i publikowanie elementu Runbook na koncie Azure Automation

Teraz używamy programu PowerShell do zaimportowania elementu Runbook do konta Azure Automation, a następnie opublikowania elementu Runbook.
Aby uzyskać informacje o sposobach importowania i publikowania elementu Runbook w Azure Portal, zobacz [Zarządzanie elementami Runbook w programie Azure Automation](manage-runbooks.md).

Aby zaimportować `DeployTemplate.ps1` do konta usługi Automation jako element Runbook programu PowerShell, uruchom następujące polecenia programu PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Teraz uruchamiamy element Runbook przez wywołanie polecenia cmdlet [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) .

Aby uzyskać informacje o sposobach uruchamiania elementu Runbook w Azure Portal, zobacz [Uruchamianie elementu Runbook w programie Azure Automation](automation-starting-a-runbook.md).

Uruchom następujące polecenia w konsoli programu PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Działa element Runbook, a jego stan można sprawdzić, uruchamiając `$job.Status`.

Element Runbook pobierze szablon Menedżer zasobów i użyje go do wdrożenia nowego konta usługi Azure Storage.
Aby zobaczyć, że nowe konto magazynu zostało utworzone, należy uruchomić następujące polecenie:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Podsumowanie

Gotowe. Teraz możesz używać Azure Automation i usługi Azure Storage oraz szablonów Menedżer zasobów do wdrażania wszystkich zasobów platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat szablonów Menedżer zasobów, zobacz [omówienie Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* Aby rozpocząć pracę z usługą Azure Storage, zobacz [wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md).
* Aby znaleźć inne przydatne Azure Automation elementy Runbook, zobacz [elementy Runbook i galerie modułów dla Azure Automation](automation-runbook-gallery.md).
* Aby znaleźć inne przydatne Menedżer zasobów szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/)


