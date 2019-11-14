---
title: Profilowanie aplikacji sieci Web działających na maszynie wirtualnej platformy Azure przy użyciu Application Insights Profiler | Microsoft Docs
description: Profilowanie aplikacji sieci Web na maszynie wirtualnej platformy Azure przy użyciu Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fa1179e55a4826450d30351be0a905efb059780b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031010"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilowanie aplikacji sieci Web działających na maszynie wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych przy użyciu Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Możesz również wdrożyć usługę Azure Application Insights Profiler w ramach następujących usług:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Wdróż Profiler na maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych
W tym artykule pokazano, jak uzyskać Application Insights Profiler uruchomione na maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych platformy Azure. Profiler jest instalowany z rozszerzeniem Diagnostyka Azure dla maszyn wirtualnych. Skonfiguruj rozszerzenie w celu uruchomienia profilera i skompiluj zestaw Application Insights SDK w aplikacji.

1. Dodaj zestaw Application Insights SDK do [aplikacji ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

   Aby wyświetlić profile dla swoich żądań, należy wysłać dane telemetryczne żądania do Application Insights.

1. Zainstaluj rozszerzenie Diagnostyka Azure na maszynie wirtualnej. Aby zapoznać się z pełnymi przykładami szablonów Menedżer zasobów, zobacz:  
   * [Maszyna wirtualna](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Zestaw skalowania maszyn wirtualnych](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Kluczową częścią jest ApplicationInsightsProfilerSink w WadCfg. Aby Diagnostyka Azure włączyć program Profiler do wysyłania danych do iKey, Dodaj kolejny ujścia do tej sekcji.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Wdróż definicję wdrożenia zmodyfikowanego środowiska.  

   Zastosowanie modyfikacji zwykle obejmuje pełne wdrożenie szablonu lub publikowanie oparte na usłudze w chmurze za pomocą poleceń cmdlet programu PowerShell lub programu Visual Studio.  

   Następujące polecenia programu PowerShell są alternatywnym rozwiązaniem dla istniejących maszyn wirtualnych, które dotykają tylko rozszerzenia Diagnostyka Azure. Dodaj wcześniej wymienione ProfilerSink do konfiguracji zwracanej przez polecenie Get-AzVMDiagnosticsExtension. Następnie Przekaż zaktualizowaną konfigurację do polecenia Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Jeśli zamierzona aplikacja jest uruchomiona za pomocą [usług IIS](https://www.microsoft.com/web/downloads/platform.aspx), Włącz funkcję systemu `IIS Http Tracing`.

   a. Ustanów dostęp zdalny do środowiska, a następnie użyj okna [Dodawanie funkcji systemu Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) . Lub uruchom następujące polecenie w programie PowerShell (jako administrator):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Jeśli jest to problem, można użyć [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) , aby uruchomić następujące polecenie:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Wdróż aplikację.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Ustawianie ujścia profilera przy użyciu Azure Resource Explorer
Nie mamy jeszcze sposobu na ustawienie ujścia Application Insights Profiler w portalu. Zamiast korzystać z programu PowerShell, jak opisano powyżej, można użyć Azure Resource Explorer do ustawienia ujścia. Należy jednak pamiętać, że po ponownym wdrożeniu maszyny wirtualnej ujścia zostanie utracona. Należy zaktualizować konfigurację używaną podczas wdrażania maszyny wirtualnej w celu zachowania tego ustawienia.

1. Sprawdź, czy rozszerzenie Diagnostyka Azure systemu Windows jest zainstalowane, wyświetlając rozszerzenia zainstalowane dla maszyny wirtualnej.  

    ![Sprawdź, czy rozszerzenie funkcji wad jest zainstalowane][wadextension]

2. Znajdź rozszerzenie diagnostyki maszyn wirtualnych dla maszyny wirtualnej. Przejdź do obszaru [https://resources.azure.com](https://resources.azure.com) (Ustawienia — Integracje i usługi). Rozwiń grupę zasobów, Microsoft. COMPUTE virtualMachines, nazwę maszyny wirtualnej i rozszerzenia.  

    ![Przejdź do funkcji wad config w Azure Resource Explorer][azureresourceexplorer]

3. Dodaj ujścia Application Insights Profiler do węzła SinksConfig w obszarze WadCfg. Jeśli nie masz jeszcze sekcji SinksConfig, może być konieczne dodanie jednej z nich. Upewnij się, że określisz odpowiednie Application Insights iKey w ustawieniach. Musisz przełączyć tryb eksplorators na odczyt/zapis w prawym górnym rogu i naciśnij niebieski przycisk "Edytuj".

    ![Dodawanie Application Insights Profiler ujścia][resourceexplorersinksconfig]

4. Po zakończeniu edycji konfiguracji kliknij przycisk "Umieść". Jeśli umieszczenie zakończy się pomyślnie, w środku ekranu pojawi się zielony znacznik.

    ![Wyślij żądanie Put, aby zastosować zmiany][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Czy profiler może działać na serwerach lokalnych?
Nie ma planu obsługi Application Insights Profiler dla serwerów lokalnych.

## <a name="next-steps"></a>Następne kroki

- Generuj ruch do aplikacji (na przykład uruchom [Test dostępności](monitor-web-app-availability.md)). Następnie poczekaj od 10 do 15 minut, aż ślady rozpoczną wysyłanie do wystąpienia Application Insights.
- Zobacz [ślady profilera](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w Azure Portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów z profilerem, zobacz [Rozwiązywanie problemów z narzędziem Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
