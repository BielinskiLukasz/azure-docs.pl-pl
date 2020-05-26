---
title: Przegląd konfiguracji stanu Azure Automation
description: Ten artykuł zawiera omówienie konfiguracji stanu Azure Automation.
keywords: PowerShell DSC, Konfiguracja żądanego stanu, środowisko PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9880915061c0639aebe30bdb33258d7c79e155d7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836893"
---
# <a name="azure-automation-state-configuration-overview"></a>Przegląd konfiguracji stanu Azure Automation

Konfiguracja stanu Azure Automation to usługa zarządzania konfiguracją platformy Azure, która umożliwia pisanie, zarządzanie i kompilowanie konfiguracyjnych [konfiguracji stanu (DSC) dla](/powershell/scripting/dsc/configurations/configurations) węzłów w chmurze lub lokalnych centrach danych. Usługa importuje również [zasoby DSC](/powershell/scripting/dsc/resources/resources)i przypisuje konfiguracje do węzłów docelowych, a wszystko to w chmurze. Aby uzyskać dostęp do konfiguracji stanu Azure Automation w Azure Portal, wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**. 

Azure Automation konfiguracji stanu można użyć do zarządzania różnymi maszynami:

- Maszyny wirtualne platformy Azure
- Azure Virtual Machines (klasyczny)
- Fizyczne/wirtualne maszyny z systemem Windows w środowisku lokalnym lub w chmurze innej niż Azure (w tym wystąpienia AWS EC2)
- Fizyczne/wirtualne maszyny z systemem Linux lokalnie, na platformie Azure lub w chmurze innej niż Azure

Jeśli nie masz gotowości do zarządzania konfiguracją maszyny z chmury, możesz użyć konfiguracji stanu Azure Automation jako punktu końcowego tylko do raportowania. Ta funkcja umożliwia ustawianie (wypychania) konfiguracji za poorednictwem DSC i wyświetlanie szczegółów raportowania w Azure Automation.

> [!NOTE]
> Zarządzanie maszynami wirtualnymi platformy Azure za pomocą konfiguracji stanu Azure Automation jest bezpłatne, jeśli zainstalowana wersja rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure jest większa niż 2,70. Aby uzyskać więcej informacji, zobacz [**stronę z cennikiem usługi Automation**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Dlaczego warto używać konfiguracji stanu Azure Automation

Azure Automation konfiguracja stanu zapewnia kilka korzyści w porównaniu z użyciem DSC poza platformą Azure. Ta usługa umożliwia szybkie i łatwe skalowanie w tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji. Można łatwo włączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.

Usługa konfiguracji stanu Azure Automation to DSC, co Azure Automation elementów Runbook do obsługi skryptów programu PowerShell. Innymi słowy, w taki sam sposób, jaki Azure Automation ułatwia zarządzanie skryptami programu PowerShell, ułatwia również zarządzanie konfiguracjami DSC. 

### <a name="built-in-pull-server"></a>Wbudowany serwer ściągania

Azure Automation konfiguracja stanu udostępnia serwer ściągania DSC podobny do [usługi Windows Feature DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). Węzły docelowe mogą automatycznie odbierać konfiguracje, odpowiadać na żądanym stanie i zgłaszać ich zgodność. Wbudowany serwer ściągania w programie Azure Automation eliminuje konieczność skonfigurowania i utrzymania własnego serwera ściągania. Azure Automation mogą kierować wirtualne lub fizyczne maszyny z systemem Windows lub Linux, w chmurze lub w środowisku lokalnym.

### <a name="management-of-all-your-dsc-artifacts"></a>Zarządzanie wszystkimi artefaktami DSC

Azure Automation konfiguracja stanu umożliwia korzystanie z tej samej warstwy zarządzania do [konfiguracji żądanego stanu programu PowerShell](/powershell/scripting/dsc/overview/overview) , ponieważ oferuje ona obsługę skryptów programu PowerShell. Z poziomu Azure Portal lub z programu PowerShell można zarządzać wszystkimi konfiguracjami DSC, zasobami i węzłami docelowymi.

![Zrzut ekranu przedstawiający stronę Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importowanie danych raportowania do dzienników Azure Monitor

Węzły, które są zarządzane za pomocą Azure Automation stanu Configuration, wysyłają szczegółowe dane stanu raportowania do wbudowanego serwera ściągania. Konfigurację stanu Azure Automation można skonfigurować w celu wysłania tych danych do Log Analytics obszaru roboczego. Zapoznaj [się z raportem Azure Monitor Azure Automation przekazywania danych dotyczących konfiguracji stanu](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Wymagania wstępne dotyczące używania konfiguracji stanu Azure Automation

W przypadku korzystania z konfiguracji stanu Azure Automation należy wziąć pod uwagę wymagania zawarte w tej sekcji.

### <a name="operating-system-requirements"></a>Wymagania dotyczące systemu operacyjnego

W przypadku węzłów z systemem Windows obsługiwane są następujące wersje:

- Windows Server 2019
- Windows Server 2016
- 2012R2 systemu Windows Server
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Autonomiczna jednostka SKU produktu [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) nie zawiera implementacji DSC. Dlatego nie może być zarządzany przez program PowerShell DSC lub Azure Automation konfigurację stanu.

W przypadku węzłów z systemem Linux rozszerzenie DSC Linux obsługuje wszystkie dystrybucje systemu Linux wymienione w obszarze [obsługiwane dystrybucje systemu Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Wymagania DSC

W przypadku wszystkich węzłów systemu Windows działających na platformie Azure program [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) jest instalowany po włączeniu maszyn. W przypadku węzłów z systemami Windows Server 2012 i Windows 7 [usługa WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) jest włączona.

W przypadku wszystkich węzłów systemu Linux działających na platformie Azure program [POWERSHELL DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest instalowany po włączeniu maszyn.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfiguracja sieci prywatnych

Jeśli węzły znajdują się w sieci prywatnej, wymagane są następujące porty i adresy URL. Te zasoby zapewniają łączność sieciową z zarządzanym węzłem i umożliwiają komunikację DSC z Azure Automation.

* Port: tylko protokół TCP 443 wymagany do wychodzącego dostępu do Internetu
* Globalny adres URL: ***. Azure-Automation.NET**
* Globalny adres URL US Gov Wirginia: ***. Azure-Automation.us**
* Usługa agenta: **https:// \< identyfikator obszaru roboczego \> . agentsvc.Azure-Automation.NET**

Jeśli używasz zasobów DSC komunikujących się między węzłami, takimi jak [WAITFOR * Resources](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), musisz również zezwolić na ruch między węzłami. Zapoznaj się z dokumentacją poszczególnych zasobów DSC, aby poznać te wymagania sieciowe.

#### <a name="proxy-support"></a>Obsługa serwera proxy

Obsługa serwera proxy dla agenta DSC jest dostępna w systemie Windows w wersji 1809 i nowszych. Ta opcja jest włączona przez ustawienie wartości `ProxyURL` i `ProxyCredential` właściwości w [skrypcie konfiguracji](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) , używanym do rejestrowania węzłów. 

>[!NOTE]
>Konfiguracja stanu Azure Automation nie zapewnia obsługi serwera proxy DSC dla wcześniejszych wersji systemu Windows.

W przypadku węzłów systemu Linux Agent DSC obsługuje serwer proxy i używa `http_proxy` zmiennej do określenia adresu URL. Aby dowiedzieć się więcej na temat obsługi serwera proxy, zobacz [generowanie konfiguracji DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Zakresy sieci Azure Automation konfiguracji stanu i przestrzeń nazw

Zaleca się użycie adresów wymienionych poniżej podczas definiowania wyjątków. W przypadku adresów IP można pobrać [zakresy adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowany co tydzień i ma aktualnie wdrożone zakresy oraz wszystkie nadchodzące zmiany w zakresach adresów IP.

Jeśli masz konto usługi Automation zdefiniowane dla określonego regionu, możesz ograniczyć komunikację z tym regionalnym centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu:

| **Okolicy** | **Rekord DNS** |
| --- | --- |
| Zachodnio-środkowe stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Południowo-środkowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Aby uzyskać listę adresów IP regionów zamiast nazw regionów, Pobierz plik XML [adresu IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) z centrum pobierania Microsoft.

> [!NOTE]
> Plik XML adresu IP centrum danych platformy Azure zawiera zakresy adresów IP, które są używane w centrach danych Microsoft Azure. Plik zawiera zakresy obliczeń, SQL i magazynu.
>
>Zaktualizowany plik jest publikowany co tydzień. Plik odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień. Dobrym pomysłem jest pobranie nowego pliku XML co tydzień. Następnie zaktualizuj swoją witrynę, aby prawidłowo identyfikować usługi działające na platformie Azure. 

Użytkownicy usługi Azure ExpressRoute powinni pamiętać, że ten plik jest używany do aktualizacji anonsu usługi Border Gateway Protocol (BGP) w pierwszym tygodniu każdego miesiąca.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz Wprowadzenie do [konfiguracji stanu Azure Automation](automation-dsc-getting-started.md).
- Aby dowiedzieć się, jak włączyć węzły, zobacz [Włączanie konfiguracji stanu Azure Automation](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [Konfigurowanie ciągłego wdrażania z czekoladą](automation-dsc-cd-chocolatey.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).