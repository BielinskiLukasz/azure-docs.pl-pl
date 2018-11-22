---
title: Odkryj, jakie oprogramowanie jest zainstalowane na Twoich maszynach, za pomocą usługi Azure Automation | Microsoft Docs
description: Użyj spisu, aby dowiedzieć się, jakie oprogramowanie jest zainstalowane na maszynach w Twoim środowisku.
services: automation
keywords: spis, automatyzacja, zmiana, śledzenie
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.component: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: ad9fc7deb73cdcbcf83cc10b9b28f7d7e4ac7661
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283795"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Wykrywanie, jakie oprogramowanie jest zainstalowane na maszynach na platformie Azure i poza platformą Azure

W tym samouczku dowiesz się, jak wykryć, jakie oprogramowanie jest zainstalowane w danym środowisku. Możesz zbierać i wyświetlać spis oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows znajdujących się na Twoich komputerach. Śledzenie konfiguracji maszyn ułatwia identyfikowanie problemów operacyjnych w środowisku oraz lepsze rozumienie stanu maszyn.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie rozwiązania
> * Dołączanie maszyny wirtualnej platformy Azure
> * Dołączanie maszyny wirtualnej spoza platformy Azure
> * Wyświetlanie zainstalowanego oprogramowania
> * Dzienniki przeszukiwania zapasów dla zainstalowanego oprogramowania

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto automatyzacji](automation-offering-get-started.md) do przechowywania obserwatora i elementów Runbook akcji oraz zadania obserwatora.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) do dołączenia.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Włączanie śledzenia zmian i spisu

Najpierw należy włączyć śledzenie zmian i spisu na potrzeby tego samouczka. Jeśli rozwiązanie **Change Tracking** zostało wcześniej włączone, ten krok nie jest konieczny.

Przejdź do konta usługi Automation, a następnie wybierz pozycję **Spis** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**.

Wybierz obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij przycisk **Włącz**, aby włączyć to rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Transparent konfiguracji dołączony do spisu](./media/automation-tutorial-installed-software/enableinventory.png)

Aby włączyć rozwiązanie, skonfiguruj lokalizację, obszar roboczy usługi Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz**. Jeśli pola są wygaszone, oznacza to, że inne rozwiązanie automatyzacji jest włączone dla maszyny wirtualnej, a tym samym należy użyć tego samego obszaru roboczego i konta usługi Automation.

Obszar roboczy usługi [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) służy do zbierania danych generowanych przez funkcje i usługi, takie jak spis.
Obszar roboczy zawiera pojedynczą lokalizację do przeglądania i analizowania danych z wielu źródeł.

Włączanie rozwiązania może potrwać do 15 minut. W tym czasie nie należy zamykać okna przeglądarki.
Po włączeniu rozwiązania informacje dotyczące zainstalowanego oprogramowania i zmian w maszynie wirtualnej są przekazywane do usługi Log Analytics.
Udostępnienie danych do analizy może potrwać od 30 minut do 6 godzin.

## <a name="onboard-a-vm"></a>Dołączanie maszyny wirtualnej

W ramach konta usługi Automation przejdź do sekcji **Spis** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**.

Wybierz pozycję **+Dodaj maszynę wirtualną platformy Azure**, co spowoduje otwarcie strony **Maszyny wirtualne** i umożliwi wybór istniejącej maszyny wirtualnej z listy. Wybierz maszynę wirtualną, którą chcesz dołączyć. Na stronie, która zostanie otwarta, kliknij pozycję **Włącz**, aby włączyć rozwiązanie na maszynie wirtualnej. Agent Microsoft Management Agent został wdrożony na maszynie wirtualnej i umożliwia skonfigurowanie agenta, aby komunikował się z obszarem roboczym usługi Log Analytics skonfigurowanym podczas włączania rozwiązania. Ukończenie procesu dołączania może potrwać kilka minut. W tym momencie możesz wybrać nową maszynę wirtualną z listy i dołączyć ją.

## <a name="onboard-a-non-azure-machine"></a>Dołączanie maszyny spoza platformy Azure

Aby dodać maszyny spoza platformy Azure, zainstaluj agenta systemu [Windows](../log-analytics/log-analytics-agent-windows.md) lub [Linux](automation-linux-hrw-install.md) w zależności od używanego systemu operacyjnego. Po zainstalowaniu agenta przejdź do konta usługi Automation, a następnie wybierz pozycję **Spis** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**. Po kliknięciu pozycji **Zarządzaj maszynami** zostanie wyświetlona lista maszyn raportujących w Twoim obszarze roboczym usługi Log Analytics, dla których rozwiązanie nie jest włączone. Wybierz opcję odpowiednią dla Twojego środowiska.

* **Włącz na wszystkich dostępnych maszynach** — ta opcja włącza rozwiązanie na wszystkich maszynach aktualnie raportujących w Twoim obszarze roboczym usługi Log Analytics.
* **Włącz na wszystkich dostępnych i przyszłych maszynach** — ta opcja włącza rozwiązanie na wszystkich maszynach aktualnie raportujących w Twoim obszarze roboczym usługi Log Analytics oraz na wszystkich maszynach, które zostaną dodane do obszaru roboczego.
* **Włącz na wybranych maszynach** — ta opcja włącza rozwiązanie tylko na wybranych maszynach.

![Zarządzanie maszynami](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Wyświetlanie zainstalowanego oprogramowania

Po włączeniu rozwiązania śledzenia zmian i spisu możesz wyświetlić wyniki na stronie **Spis**.

Z poziomu konta usługi Automation wybierz pozycję **Spis** w obszarze **ZARZĄDZANIE KONFIGURACJĄ**.

Na stronie **Spis** kliknij kartę **Oprogramowanie**.

Na karcie **Oprogramowanie** znajduje się tabela z listą oprogramowania, które zostało odnalezione. Oprogramowanie jest grupowane według nazwy i wersji oprogramowania.

Szczegółowe informacje wysokiego poziomu dotyczące każdego rekordu oprogramowania są wyświetlane w tabeli. Te szczegółowe informacje obejmują nazwę oprogramowania, wersję, wydawcę, czas ostatniego odświeżenia (ostatni czas odświeżania zgłoszony przez maszynę w grupie) i maszyny (liczba maszyn z tym oprogramowaniem).

![Spis oprogramowania](./media/automation-tutorial-installed-software/inventory-software.png)

Kliknij wiersz, aby wyświetlić właściwości rekordu oprogramowania i nazwy maszyn z tym oprogramowaniem.

Aby znaleźć określone oprogramowanie lub grupę oprogramowania, możesz je wyszukać w polu tekstowym bezpośrednio nad listą oprogramowania.
Filtr umożliwia wyszukiwanie na podstawie nazwy oprogramowania, wersji lub wydawcy.

Na przykład wyszukiwanie „Contoso” zwraca całe oprogramowanie mające w nazwie, wydawcy lub wersji ciąg „Contoso”.

## <a name="search-inventory-logs-for-installed-software"></a>Dzienniki przeszukiwania zapasów dla zainstalowanego oprogramowania

Spis generuje dane dziennika, które są wysyłane do usługi Log Analytics. Aby wyszukiwać w dziennikach za pomocą uruchamiania zapytań, wybierz pozycję **Log Analytics** w górnej części okna **Spis**.

Dane spisu są przechowywane w obszarze typu **ConfigurationData**.
Następujące przykładowe zapytanie usługi Log Analytics zwraca wszystkie wyniki ze spisu, dla których parametr Publisher ma wartość „Microsoft Corporation”.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Aby dowiedzieć się więcej na temat uruchamiania i wyszukiwania plików dziennika w usłudze Log Analytics, zobacz [Azure Log Analytics](../log-analytics/log-analytics-queries.md).

### <a name="single-machine-inventory"></a>Spis dla pojedynczego komputera

Aby wyświetlić spis oprogramowania dla pojedynczego komputera, możesz uzyskać dostęp do spisu ze strony zasobu maszyny wirtualnej platformy Azure lub użyć usługi Log Analytics do odfiltrowania odpowiedniej maszyny.
Poniższe przykładowe zapytanie usługi Log Analytics zwraca listę oprogramowania dla maszyny o nazwie ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wyświetlania spisu oprogramowania, np.:

> [!div class="checklist"]
> * Włączanie rozwiązania
> * Dołączanie maszyny wirtualnej platformy Azure
> * Dołączanie maszyny wirtualnej spoza platformy Azure
> * Wyświetlanie zainstalowanego oprogramowania
> * Dzienniki przeszukiwania zapasów dla zainstalowanego oprogramowania

Przejdź do omówienia rozwiązania śledzenia zmian i spisu, aby uzyskać więcej informacji.

> [!div class="nextstepaction"]
> [Change management and Inventory solution](automation-change-tracking.md) (Rozwiązanie zarządzania zmianami i spisem)