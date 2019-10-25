---
title: Konfigurowanie usługi Application Insights na platformie Azure przy użyciu programu PowerShell | Microsoft Docs
description: Automatyzowanie konfigurowania Diagnostyka Azure do danych potoku do Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/06/2019
ms.openlocfilehash: 0e1fd048b855473c1d7978e2a908c45360a1ed2a
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819017"
---
# <a name="using-powershell-to-set-up-application-insights-for-azure-cloud-services"></a>Konfigurowanie Application Insights na platformie Azure za pomocą programu PowerShell Cloud Services

Platformę [Microsoft Azure](https://azure.com) można [skonfigurować do wysyłania danych usługi Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) do usługi [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Dane diagnostyczne są związane z usługami Azure Cloud Services i maszynami wirtualnymi platformy Azure. Uzupełniają one dane telemetryczne wysyłane z poziomu aplikacji za pomocą zestawu SDK usługi Application Insights. W ramach automatyzowania procesu tworzenia nowych zasobów platformy Azure można skonfigurować diagnostykę przy użyciu programu PowerShell.

## <a name="azure-template"></a>Szablon Azure
Jeśli aplikacja internetowa działa na platformie Azure, a zasoby zostały utworzone przy użyciu szablonu usługi Azure Resource Manager, można skonfigurować usługę Application Insights, dodając następujący kod do węzła zasobów:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` — nazwa zasobu usługi Application Insights
* `myWebAppName` — identyfikator aplikacji sieci Web

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Włączanie rozszerzenia diagnostyki w ramach wdrażania usługi Cloud Service
Polecenie cmdlet `New-AzureDeployment` ma parametr `ExtensionConfiguration`, który przyjmuje tablicę konfiguracji diagnostycznych. Można ją utworzyć za pomocą polecenia cmdlet `New-AzureServiceDiagnosticsExtensionConfig`. Na przykład:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Włączanie rozszerzenia diagnostyki w istniejącej usłudze Cloud Service
W istniejącej usłudze użyj polecenia cmdlet `Set-AzureServiceDiagnosticsExtension`.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Pobieranie bieżącej konfiguracji rozszerzenia diagnostyki
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Usuwanie rozszerzenia diagnostyki
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Jeśli włączono rozszerzenie diagnostyki za pomocą polecenia cmdlet `Set-AzureServiceDiagnosticsExtension` lub `New-AzureServiceDiagnosticsExtensionConfig` bez parametru Role, można usunąć to rozszerzenie przy użyciu polecenia `Remove-AzureServiceDiagnosticsExtension` bez parametru Role. Jeśli podczas włączania rozszerzenia użyto parametru Role, trzeba go użyć również podczas usuwania rozszerzenia.

Aby usunąć rozszerzenie diagnostyki z pojedynczej roli:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Zobacz także
* [Monitorowanie aplikacji usług Azure Cloud Services za pomocą usługi Application Insights](../../azure-monitor/app/cloudservices.md)
* [Wysyłanie Diagnostyki Azure do usługi Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automatyzowanie konfigurowania alertów](powershell-alerts.md)

