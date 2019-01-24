---
title: Usługa Azure Service Fabric — skonfiguruj monitorowanie za pomocą usługi Log Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować usługi Log Analytics umożliwiające wizualizowanie i analizowanie zdarzeń do monitorowania klastrów usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: dc48194f4740216089f3daa869576596899e272b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54439055"
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Konfigurowanie usługi Log Analytics dla klastra

Usługa log Analytics jest nasze zalecenie, aby monitorować zdarzenia poziomu klastra. Można skonfigurować obszar roboczy usługi Log Analytics, za pośrednictwem usługi Azure Resource Manager, programu PowerShell lub portalu Azure Marketplace. Jeśli zachowasz zaktualizowany szablon usługi Resource Manager, który wdrożenia do użycia w przyszłości, należy użyć tego samego szablonu można skonfigurować środowisko usługi Log Analytics. Wdrożenia za pośrednictwem witryny Marketplace jest łatwiejsze, jeśli masz już klaster, który został wdrożony z włączoną diagnostyką. Jeśli nie masz dostępu na poziomie subskrypcji na koncie, do której jest wdrażany z, należy wdrożyć przy użyciu programu PowerShell lub szablonu usługi Resource Manager.

> [!NOTE]
> Aby skonfigurować usługi Log Analytics do monitorowania klastra, musisz mieć włączone, aby wyświetlić zdarzenia z poziomu klastra lub poziom platformy diagnostyki. Zapoznaj się [jak konfigurowanie diagnostyki w klastrach Windows](service-fabric-diagnostics-event-aggregation-wad.md) i [sposób konfigurowania diagnostyki w klastrach systemu Linux](service-fabric-diagnostics-event-aggregation-lad.md) Aby uzyskać więcej informacji

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Wdrażanie obszaru roboczego usługi Log Analytics przy użyciu portalu Azure Marketplace

Jeśli chcesz dodać obszar roboczy usługi Log Analytics, po wdrożeniu klastra, przejdź do portalu Azure Marketplace w portalu i poszukaj **analiza usługi Service Fabric**. To niestandardowe rozwiązanie w przypadku wdrożeń usługi Service Fabric zawiera dane specyficzne dla usługi Service Fabric. W ramach tego procesu możesz tworzyć rozwiązania (pulpit nawigacyjny, aby wyświetlić szczegółowe informacje) oraz obszaru roboczego (agregacji danych bazowych klastra).

1. Wybierz **New** w menu nawigacji po lewej stronie. 

2. Wyszukaj **usługi Service Fabric Analytics**. Wybierz zasób, który pojawia się.

3. Wybierz pozycję **Utwórz**.

    ![Analiza usługi Service Fabric w witrynie Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. W oknie tworzenia analiza usługi Service Fabric, wybierz **wybierz obszar roboczy** dla **obszaru roboczego pakietu OMS** pola, a następnie **Utwórz nowy obszar roboczy**. Wypełnij wymagane wpisy. Jedynym wymaganiem jest, czy subskrypcja dla klastra usługi Service Fabric i obszaru roboczego jest taki sam. Po sprawdzeniu poprawności wpisy rozpocznie wdrażanie obszaru roboczego. Przeprowadzenie wdrożenia zajmuje tylko kilka minut.

5. Po zakończeniu wybierz pozycję **Utwórz** ponownie w dolnej części okna Tworzenie analiza usługi Service Fabric. Upewnij się, że nowy obszar roboczy będzie wyświetlane w **obszaru roboczego pakietu OMS**. Ta akcja dodaje rozwiązania do obszaru roboczego, który został utworzony.

Jeśli używasz Windows Kontynuuj poniższe kroki, aby nawiązać połączenie z usługi Log Analytics na koncie magazynu przechowywania zdarzenia klastra. 

>[!NOTE]
>Włączenie tego środowiska, w przypadku klastrów systemu Linux nie jest jeszcze dostępna. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Połącz obszar roboczy usługi Log Analytics z klastrem 

1. Obszaru roboczego musi być podłączony do danych diagnostycznych z klastra. Przejdź do grupy zasobów, w którym utworzono rozwiązania analiza usługi Service Fabric. Wybierz **ServiceFabric\<nameOfWorkspace\>**  i przejdź do strony Przegląd. Z tego miejsca można zmienić ustawienia rozwiązania, ustawienia obszaru roboczego i dostęp do obszaru roboczego usługi Log Analytics.

2. W menu nawigacji po lewej stronie w obszarze **źródła danych obszaru roboczego**, wybierz opcję **dzienniki kont magazynu**.

3. Na **dzienniki konta magazynu** wybierz opcję **Dodaj** u góry, aby dodać dzienniki klastra do obszaru roboczego.

4. Wybierz **konta magazynu** dodać odpowiednie konto utworzone w klastrze. Jeśli używana jest domyślna nazwa, konto magazynu jest **sfdg\<resourceGroupName\>**. Można również to potwierdzić, przy użyciu szablonu usługi Azure Resource Manager umożliwia wdrażanie klastra, sprawdzając wartość **applicationDiagnosticsStorageAccountName**. Jeśli nazwa nie jest widoczne, przewiń w dół i wybierz **Załaduj więcej**. Wybierz nazwę konta magazynu.

5. Określ typ danych. Ustaw ją na **usługi Service Fabric zdarzenia**.

6. Upewnij się, że źródłem jest automatycznie ustawiana na **WADServiceFabric\*EventTable**.

7. Wybierz **OK** nawiązać obszaru dzienniki usługi klastra.

    ![Dodaj dzienniki konta magazynu do usługi Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Konto teraz wyświetlane jako część konta magazynu dzienników w źródłach danych obszaru roboczego.

Rozwiązania analiza usługi Service Fabric zostały dodane, w obszarze roboczym usługi Log Analytics, która jest teraz prawidłowo podłączone do klastra platformy oraz tabeli dziennika aplikacji. W ten sam sposób, można dodać dodatkowe źródła do obszaru roboczego.


## <a name="deploy-log-analytics-with-azure-resource-manager"></a>Wdrażanie usługi Log Analytics z usługą Azure Resource Manager

Podczas wdrażania klastra przy użyciu szablonu usługi Resource Manager szablon tworzy nowy obszar roboczy usługi Log Analytics, dodająca rozwiązanie usługi Service Fabric do obszaru roboczego i konfiguruje go do odczytywania danych z tabel odpowiedniego magazynu.

Możesz użyć i zmodyfikuj [ten przykładowy szablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) zgodnie z wymaganiami. Ten szablon wykonuje następujące czynności

* Tworzy klaster usługi Service Fabric z 5 węzłami
* Tworzy obszar roboczy usługi Log Analytics i rozwiązania usługi Service Fabric
* Konfiguruje agenta usługi Log Analytics do zbierania i wysyłania 2 liczniki wydajności przykładowe do obszaru roboczego
* Konfiguruje WAD do zbierania usługi Service Fabric, a następnie wysyła je do tabel usługi Azure storage (WADServiceFabric * EventTable)
* Konfiguruje obszaru roboczego usługi Log Analytics do odczytywania zdarzeń z tych tabel


Można wdrożyć tego szablonu jako uaktualniania przy użyciu usługi Resource Manager z klastrem przy użyciu `New-AzureRmResourceGroupDeployment` interfejsu API w moduł AzureRM PowerShell. Przykładowe polecenie będzie następująca:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Usługa Azure Resource Manager wykryje, że to polecenie aktualizacji istniejącego zasobu. Przetwarza tylko zmiany między szablonu zachęcanie istniejącego wdrożenia i nowy szablon, pod warunkiem.

## <a name="deploy-log-analytics-with-azure-powershell"></a>Wdrażanie usługi Log Analytics przy użyciu programu Azure PowerShell

Można także wdrożyć zasobie usługi Log Analytics za pośrednictwem programu PowerShell przy użyciu `New-AzureRmOperationalInsightsWorkspace` polecenia. Aby użyć tej metody, upewnij się, zainstalowano [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.1.1). Użyj tego skryptu, aby utworzyć nowy obszar roboczy usługi Log Analytics i Dodawanie rozwiązania do usługi Service Fabric do niego: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Gdy wszystko będzie gotowe, wykonaj kroki opisane w poprzedniej sekcji, konto magazynu odpowiednie nawiązać połączenia usługi Log Analytics.

Można również dodać inne rozwiązania lub wprowadzić inne zmiany do swojego obszaru roboczego usługi Log Analytics przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz [Zarządzanie Log Analytics przy użyciu programu PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Kolejne kroki
* [Wdróż agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md) na węzły do zbierania liczników wydajności i zbieranie statystyk platformy docker i dzienniki kontenerów
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../log-analytics/log-analytics-log-searches.md) dostępnymi w ramach usługi Log Analytics
* [Tworzenie niestandardowych widoków w usłudze Log Analytics za pomocą projektanta widoku](../azure-monitor/platform/view-designer.md)
