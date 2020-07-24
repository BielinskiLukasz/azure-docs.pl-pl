---
title: Agent Application Insights platformy Azure — wprowadzenie | Microsoft Docs
description: Przewodnik Szybki Start dotyczący Application Insights agenta. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 05527ff456c70290a925befc9d30fb1eaf177199
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008238"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Wprowadzenie do usługi Azure Monitor Application Insights Agent dla serwerów lokalnych

Ten artykuł zawiera polecenia szybkiego startu, które powinny być wykonywane dla większości środowisk.
Instrukcje są zależne od Galeria programu PowerShell do dystrybucji aktualizacji.
Te polecenia obsługują parametr programu PowerShell `-Proxy` .

Aby uzyskać wyjaśnienie tych poleceń, instrukcje dotyczące dostosowywania i informacje dotyczące rozwiązywania problemów, zobacz [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="download-and-install-via-powershell-gallery"></a>Pobierz i zainstaluj za pomocą Galeria programu PowerShell

### <a name="install-prerequisites"></a>Instalacja wymagań wstępnych
Uruchom program PowerShell jako administrator.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zamknij program PowerShell.

### <a name="install-application-insights-agent"></a>Zainstaluj agenta Application Insights
Uruchom program PowerShell jako administrator.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Włączanie monitorowania
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Pobierz i zainstaluj ręcznie (opcja offline)
### <a name="download-the-module"></a>Pobierz moduł
Ręcznie Pobierz najnowszą wersję modułu z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Rozpakuj i Zainstaluj agenta Application Insights
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Włączanie monitorowania
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Następne kroki

 Wyświetlanie telemetrii:

- [Poznaj metryki](../../azure-monitor/platform/metrics-charts.md) , aby monitorować wydajność i użycie.
- [Wyszukaj zdarzenia i dzienniki](../../azure-monitor/app/diagnostic-search.md) , aby zdiagnozować problemy.
- [Użyj analizy](../log-query/log-query-overview.md) , aby uzyskać bardziej zaawansowane zapytania.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

 Dodawanie kolejnych funkcji telemetrii:

- [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj telemetrię klienta sieci Web](../../azure-monitor/app/javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
- [Dodaj do kodu zestaw SDK Application Insights](../../azure-monitor/app/asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.

Zrób więcej dzięki Application Insights agentowi:

- Zapoznaj się z [szczegółowymi instrukcjami](status-monitor-v2-detailed-instructions.md) dla wyjaśnienia poleceń znalezionych w tym miejscu.
- Skorzystaj z naszego przewodnika, aby [rozwiązać problemy z](status-monitor-v2-troubleshoot.md) agentem Application Insights.
