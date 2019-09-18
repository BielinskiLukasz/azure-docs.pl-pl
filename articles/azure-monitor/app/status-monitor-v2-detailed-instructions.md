---
title: Szczegółowe instrukcje dotyczące platformy Azure monitor stanu v2 | Microsoft Docs
description: Szczegółowe instrukcje dotyczące rozpoczynania pracy z monitor stanu v2. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 791db3de897231667d184f08ee152705c59a1e35
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057851"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Monitor stanu v2: Szczegółowe instrukcje

W tym artykule opisano, jak dołączyć do Galeria programu PowerShell i pobrać moduł ApplicationMonitor.
Uwzględniono najbardziej typowe parametry, które należy rozpocząć.
Instrukcje pobierania ręcznego są również udostępniane w przypadku braku dostępu do Internetu.

## <a name="get-an-instrumentation-key"></a>Pobieranie klucza Instrumentacji

Aby rozpocząć, musisz dysponować kluczem Instrumentacji. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Uruchom program PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu

### <a name="run-as-admin"></a>Uruchom jako administrator

Program PowerShell wymaga uprawnień na poziomie administratora, aby wprowadzać zmiany na komputerze.
### <a name="execution-policy"></a>Zasady wykonywania
- Opis: Domyślnie uruchamianie skryptów programu PowerShell jest wyłączone. Zalecamy Zezwalanie na skrypty RemoteSigned tylko dla bieżącego zakresu.
- Informacje ogólne: [Informacje o zasadach wykonywania](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) i [Set-](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)ExecutionPolicy.
- Polecenie: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Opcjonalny parametr:
    - `-Force`. Pomija monit o potwierdzenie.

**Przykłady błędów**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Wymagania wstępne dotyczące programu PowerShell

Inspekcja wystąpienia programu PowerShell przez uruchomienie `$PSVersionTable` polecenia.
To polecenie tworzy następujące dane wyjściowe:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Te instrukcje zostały zanotowane i przetestowane na komputerze z systemem Windows 10 i wymienionych powyżej wersjach.

## <a name="prerequisites-for-powershell-gallery"></a>Wymagania wstępne dotyczące Galeria programu PowerShell

Te kroki spowodują przygotowanie serwera do pobierania modułów z Galeria programu PowerShell.

> [!NOTE] 
> Galeria programu PowerShell jest obsługiwana w systemach Windows 10, Windows Server 2016 i PowerShell 6.
> Aby uzyskać informacje na temat wcześniejszych wersji, zobacz [Installing PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Uruchom program PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu uprawnień.
2. Zainstaluj dostawcę pakietów NuGet.
    - Opis: Ten dostawca jest potrzebny do współpracy z repozytoriami opartymi na NuGet, takimi jak Galeria programu PowerShell.
    - Informacje ogólne: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Polecenie: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy żądania.
        - `-Force`. Pomija monit o potwierdzenie.
    
    Ten monit zostanie wyświetlony, jeśli nie skonfigurowano narzędzia NuGet:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Skonfiguruj Galeria programu PowerShell jako zaufane repozytorium.
    - Opis: Domyślnie Galeria programu PowerShell jest niezaufanym repozytorium.
    - Informacje ogólne: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Polecenie: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Opcjonalny parametr:
        - `-Proxy`. Określa serwer proxy żądania.

    Ten monit zostanie wyświetlony, jeśli Galeria programu PowerShell nie jest zaufany:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Możesz potwierdzić tę zmianę i Przeprowadź inspekcję wszystkich PSRepositories, `Get-PSRepository` uruchamiając polecenie.

4. Zainstaluj najnowszą wersję programu PowerShellGet.
    - Opis: Ten moduł zawiera narzędzia używane do uzyskiwania innych modułów z Galeria programu PowerShell. Wersja 1.0.0.1 jest dostarczana z systemami Windows 10 i Windows Server. Wymagana jest wersja 1.6.0 lub nowsza. Aby określić, która wersja jest zainstalowana, uruchom `Get-Command -Module PowerShellGet` polecenie.
    - Informacje ogólne: [Instalowanie PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Polecenie: `Install-Module -Name PowerShellGet`.
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy żądania.
        - `-Force`. Pomija ostrzeżenie "już zainstalowane" i instaluje najnowszą wersję.

    Ten błąd zostanie wyświetlony, jeśli nie używasz najnowszej wersji programu PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Uruchom ponownie program PowerShell. Nie można załadować nowej wersji w bieżącej sesji. Nowe sesje programu PowerShell będą ładować najnowszą wersję programu PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Pobierz i zainstaluj moduł za pośrednictwem Galeria programu PowerShell

Te kroki spowodują pobranie modułu AZ. ApplicationMonitor z Galeria programu PowerShell.

1. Upewnij się, że spełniono wszystkie wymagania wstępne dotyczące Galeria programu PowerShell.
2. Uruchom program PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu uprawnień.
3. Zainstaluj moduł AZ. ApplicationMonitor.
    - Informacje ogólne: [Install-module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Polecenie: `Install-Module -Name Az.ApplicationMonitor`.
    - Parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy żądania.
        - `-AllowPrerelease`. Umożliwia instalację wersji Alpha i beta.
        - `-AcceptLicense`. Pomija monit "Akceptuj licencję"
        - `-Force`. Pomija ostrzeżenie "niezaufane repozytorium".

## <a name="download-and-install-the-module-manually-offline-option"></a>Pobierz i zainstaluj moduł ręcznie (opcja offline)

Jeśli z jakiegoś powodu nie można nawiązać połączenia z modułem programu PowerShell, można ręcznie pobrać i zainstalować moduł AZ. ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Ręcznie Pobierz najnowszy plik NUPKG

1. Przejdź do pozycji https://www.powershellgallery.com/packages/Az.ApplicationMonitor (Plik > Nowy > Inny).
2. Wybierz najnowszą wersję pliku w tabeli **historii wersji** .
3. W obszarze **Opcje instalacji**wybierz pozycję **Pobieranie ręczne**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Option 1: Zainstaluj do katalogu modułów programu PowerShell
Zainstaluj ręcznie pobrany moduł programu PowerShell w katalogu programu PowerShell, aby można było go odnajdywać za pomocą sesji programu PowerShell.
Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Rozpakuj NUPKG jako plik zip przy użyciu polecenia expand-Archive (v 1.0.1.0)

- Opis: Podstawowa wersja programu Microsoft. PowerShell. Archive (v 1.0.1.0) nie może rozpakować plików NUPKG. Zmień nazwę pliku na rozszerzenie zip.
- Informacje ogólne: [Rozwiń gałąź "archiwalne"](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Dotyczące

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Rozpakuj NUPKG za pomocą polecenia expand-Archive (v 1.1.0.0)

- Opis: Użyj bieżącej wersji rozszerzenia expand-Archive, aby rozpakować pliki NUPKG bez zmiany rozszerzenia.
- Informacje ogólne: [Rozwiń pozycję archiwalne](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) i [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Dotyczące

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opcja 2: Ręcznie Rozpakuj i zaimportuj NUPKG
Zainstaluj ręcznie pobrany moduł programu PowerShell w katalogu programu PowerShell, aby można było go odnajdywać za pomocą sesji programu PowerShell.
Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).

Jeśli instalujesz moduł w innym katalogu, ręcznie zaimportuj moduł za pomocą polecenia [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Biblioteki DLL zostaną zainstalowane za pośrednictwem ścieżek względnych.
> Przechowuj zawartość pakietu w Twoim zamierzonym katalogu środowiska uruchomieniowego i upewnij się, że uprawnienia dostępu Zezwalaj na odczyt, ale nie do zapisu.

1. Zmień rozszerzenie na "zip" i Wyodrębnij zawartość pakietu w żądanym katalogu instalacyjnym.
2. Znajdź ścieżkę pliku AZ. ApplicationMonitor. psd1.
3. Uruchom program PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu uprawnień.
4. Załaduj moduł przy użyciu `Import-Module Az.ApplicationMonitor.psd1` polecenia.
    

## <a name="route-traffic-through-a-proxy"></a>Kierowanie ruchu przez serwer proxy

W przypadku monitorowania komputera w prywatnym intranecie należy kierować ruchem HTTP za pośrednictwem serwera proxy.

Polecenia programu PowerShell do pobrania i zainstalowania AZ. ApplicationMonitor z Galeria programu PowerShell obsługują `-Proxy` parametr.
Zapoznaj się z powyższymi instrukcjami podczas pisania skryptów instalacji.

Zestaw Application Insights SDK będzie musiał wysłać dane telemetryczne aplikacji do firmy Microsoft. Zalecamy skonfigurowanie ustawień serwera proxy dla aplikacji w pliku Web. config. Aby uzyskać więcej informacji, [Zobacz Application Insights często zadawane pytania: Przekazywanie](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)serwera proxy.


## <a name="enable-monitoring"></a>Włącz monitorowanie

Użyj polecenia `Enable-ApplicationInsightsMonitoring` , aby włączyć monitorowanie.

Szczegółowy opis sposobu korzystania z tego polecenia cmdlet można znaleźć w [dokumentacji interfejsu API](status-monitor-v2-api-enable-monitoring.md) .



## <a name="next-steps"></a>Następne kroki

 Wyświetlanie telemetrii:

- [Poznaj metryki](../../azure-monitor/app/metrics-explorer.md) , aby monitorować wydajność i użycie.
- [Wyszukaj zdarzenia i dzienniki](../../azure-monitor/app/diagnostic-search.md) , aby zdiagnozować problemy.
- [Użyj analizy](../../azure-monitor/app/analytics.md) , aby uzyskać bardziej zaawansowane zapytania.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

 Dodawanie kolejnych funkcji telemetrii:

- [Utwórz testy sieci Web](monitor-web-app-availability.md) , aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj telemetrię klienta sieci Web](../../azure-monitor/app/javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
- [Dodaj do kodu zestaw SDK Application Insights](../../azure-monitor/app/asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.

Więcej informacji o monitor stanu v2:

- Skorzystaj z naszego przewodnika, aby [rozwiązywać problemy z](status-monitor-v2-troubleshoot.md) Monitor stanu v2.
