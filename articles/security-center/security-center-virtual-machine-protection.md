---
title: Ochrona maszyn i aplikacji
description: Tym dokumencie przedstawiono zalecenia w usłudze Security Center, które ułatwiają ochronę maszyn wirtualnych i komputerów i aplikacji sieci web i środowisk usługi App Service.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: bcf92838483fbb6b54802cc0d44cc44ea086d705
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282642"
---
# <a name="protect-your-machines-and-applications"></a>Ochrona maszyn i aplikacji
Gdy Azure Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek do zabezpieczania i zabezpieczania zasobów.

W tym artykule opisano stronę **obliczenia i aplikacje** w sekcji zabezpieczenia zasobów Security Center.

Aby zapoznać się z pełną listą zaleceń, które mogą zostać wyświetlone na tej stronie, zobacz [zalecenia dotyczące obliczeń i aplikacji](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Wyświetlanie zabezpieczeń zasobów obliczeniowych i aplikacji

[Pulpit nawigacyjny Security Center ![](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Aby wyświetlić stan zasobów obliczeniowych i aplikacji, w okienku po lewej stronie w obszarze Security Center wybierz pozycję **compute & Apps**. Dostępne są następujące karty:

* **Przegląd**: zawiera listę zaleceń dotyczących wszystkich zasobów obliczeniowych i aplikacji, a także ich bieżący stan zabezpieczeń 

* [**Maszyny wirtualne i serwery**](#vms-and-computers): zawiera listę zaleceń dotyczących maszyn wirtualnych, komputerów i bieżącego stanu zabezpieczeń każdego

* [**Zestawy skalowania maszyn wirtualnych**](#vmscale-sets): zawiera listę zaleceń dla zestawów skalowania, 

* [**Cloud Services**](#cloud-services): zawiera listę zaleceń dotyczących ról sieci Web i procesów roboczych monitorowanych przez Security Center

* [**App Services**](#app-services): zawiera listę zaleceń dla środowisk usługi App Service i bieżący stan zabezpieczeń każdego

* [**Kontenery**](#containers): zawiera listę zaleceń dotyczących kontenerów i oceny zabezpieczeń ich konfiguracji

* **Zasoby obliczeniowe**: zawiera listę zaleceń dotyczących zasobów obliczeniowych, takich jak klastry Service Fabric i centra zdarzeń

### <a name="whats-in-each-tab"></a>Co znajduje się na każdej karcie?

Każda karta zawiera wiele sekcji, a w każdej sekcji można przejść do szczegółów, aby wyświetlić dodatkowe szczegóły dotyczące wyświetlanego elementu.

Na każdej karcie zobaczysz również zalecenia dotyczące odpowiednich zasobów w monitorowanym środowisku. Pierwsza kolumna zawiera listę zaleceń, drugi pokazuje łączną liczbę zasobów, a trzecia pokazuje ważność problemu.

Każdy zalecenie obejmuje zestaw akcji, które można wykonać po jej wybraniu. Na przykład w przypadku wybrania opcji **brakujące aktualizacje systemu**zostanie wyświetlona liczba maszyn wirtualnych i komputerów, na których brakuje poprawek, oraz ważność brakującej aktualizacji.

> [!NOTE]
> Zalecenia dotyczące zabezpieczeń są takie same jak te na stronie **zalecenia** , ale w tym miejscu są one filtrowane według wybranego typu zasobu. Aby uzyskać więcej informacji o sposobach rozwiązywania zaleceń, zobacz [implementowanie zaleceń dotyczących zabezpieczeń w Azure Security Center](security-center-recommendations.md).
>




### <a name="vms-and-computers"></a>Maszyny wirtualne i serwery
Sekcja maszyny wirtualne i komputery zawiera przegląd wszystkich zaleceń dotyczących zabezpieczeń dla maszyn wirtualnych i komputerów. Uwzględniono cztery typy maszyn:

![Komputer spoza platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Komputer bez platformy Azure.

![Maszyna wirtualna platformy Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure Resource Manager.

![Klasyczne maszyny Wirtualnej platformy Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasyczna maszyna wirtualna platformy Azure.

![Maszyny wirtualne wskazane w obszarze roboczym](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Maszyny wirtualne, które są identyfikowane tylko z obszaru roboczego będącego częścią przeglądanej subskrypcji. Obejmuje to maszyny wirtualne z innych subskrypcji, które są raportowane do obszaru roboczego w tej subskrypcji, oraz maszyny wirtualne, które zostały zainstalowane przy użyciu agenta Operations Manager bezpośredniego, i nie mają identyfikatora zasobu.

Ikona wyświetlana pod każdym zaleceniem pomaga szybko zidentyfikować maszyny Wirtualnej, a komputer, który wymaga uwagi oraz jakiego typu zalecenie ma do nich. Możesz również użyć filtrów, aby przeszukać listę według **typu zasobów** i według **ważności**.

Aby przejść do szczegółów zaleceń dotyczących zabezpieczeń dla każdej maszyny wirtualnej, kliknij maszynę wirtualną.
W tym miejscu wyświetlane szczegóły zabezpieczeń dotyczące maszyny Wirtualnej lub komputera. Na dole można zobaczyć zalecaną akcję i ważność poszczególnych problemów.

[![Cloud Services](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="vmscale-sets"></a>Zestawy skalowania maszyn wirtualnych
Security Center automatycznie wykrywa, czy masz zestawy skalowania i zaleca się zainstalowanie na nich Microsoft Monitoring Agent.

Aby zainstalować Microsoft Monitoring Agent: 

1. Wybierz zalecenie **Zainstaluj agenta monitorowania w zestawie skalowania maszyn wirtualnych**. Otrzymujesz listę niemonitorowanych zestawów skalowania.

1. Wybierz zestaw skalowania w złej kondycji. Postępuj zgodnie z instrukcjami, aby zainstalować agenta monitorowania przy użyciu istniejącego wypełnionego obszaru roboczego lub utworzyć nowy. Upewnij się, że ustawiono [warstwę cenową](security-center-pricing.md) obszaru roboczego, jeśli nie została ustawiona.

   ![Instalowanie MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Aby ustawić nowe zestawy skalowania tak, aby automatycznie instalowały Microsoft Monitoring Agent:
1. Przejdź do Azure Policy i kliknij pozycję **definicje**.

1. Wyszukaj zasady **wdróż log Analytics Agent dla systemu Windows Virtual Machine Scale Sets** i kliknij je.

1. Kliknij przycisk **Przypisz**.

1. Ustaw **zakres** i **obszar roboczy log Analytics** i kliknij przycisk **Przypisz**.

Jeśli chcesz ustawić wszystkie istniejące zestawy skalowania w celu zainstalowania Microsoft Monitoring Agent, w Azure Policy, przejdź do **korygowania** i Zastosuj istniejące zasady do istniejących zestawów skalowania.





### <a name="cloud-services"></a>Cloud Services
W przypadku usług w chmurze zalecenie jest tworzone, gdy wersja systemu operacyjnego jest nieaktualna.

![Usługi w chmurze](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

W scenariuszu, w którym masz zalecenie, postępuj zgodnie z instrukcjami w zaleceniu, aby zaktualizować system operacyjny. Po udostępnieniu aktualizacji zostanie wystawiony alert (czerwony lub pomarańczowy — w zależności od wagi problemu). Aby uzyskać pełne wyjaśnienie tego zalecenia, kliknij pozycję **Aktualizuj wersję systemu operacyjnego** w kolumnie **Opis** .






### <a name="app-services"></a>App Services
Aby wyświetlić informacje o App Service, musisz mieć Security Center standardowej warstwy cenowej i włączyć App Service w ramach subskrypcji. Aby uzyskać instrukcje dotyczące włączania tej funkcji, zobacz [ochrona App Service przy użyciu Azure Security Center](security-center-app-services.md).

W obszarze **usługi App Services**znajdziesz listę środowisk usługi App Service oraz podsumowanie kondycji w oparciu o wykonane Security Center oceny.

![Usługi aplikacji](./media/security-center-virtual-machine-recommendations/app-services.png)

Są wyświetlane trzy typy usług aplikacji:

![Środowiska usług aplikacji](./media/security-center-virtual-machine-recommendations/ase.png) Środowiska usług aplikacji

![Aplikacja internetowa](./media/security-center-virtual-machine-recommendations/web-app.png) Aplikacja internetowa

![Aplikacja funkcji](./media/security-center-virtual-machine-recommendations/function-app.png) Aplikacja funkcji

W przypadku wybrania aplikacji sieci Web zostanie otwarty widok podsumowania z trzema kartami:

   - **Zalecenia**: na podstawie ocen wykonanych przez Security Center zakończonych niepowodzeniem.
   - Przeprowadzono **oceny**: Lista ocen wykonanych przez Security Center, które zakończono.
   - **Niedostępne oceny**: Lista ocen, których uruchomienie nie powiodło się z powodu błędu lub zalecenie nie jest istotne dla określonej usługi App Service

   W obszarze **zalecenia** znajduje się lista zaleceń dotyczących wybranej aplikacji sieci Web i ważności poszczególnych zaleceń.

   ![Zalecenia dotyczące App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Wybierz zalecenie, aby wyświetlić opis zalecenia i listę zasobów w złej kondycji, zdrowe zasoby i niezeskanowane zasoby.

   - W kolumnie **przekazana oceny** jest wyświetlana lista zakończonych ocen. Ważność te oceny zawsze ma kolor zielony.

   - Wybierz ocenę przekazany z listy opis oceny, lista zasobów w złej kondycji i w dobrej kondycji i listę nieprzeskanowane zasoby. Ma kartę zasoby w złej kondycji, ale ta lista jest zawsze pusta, ponieważ przekazana oceny.





### <a name="containers"></a>Opakowania

Po otwarciu karty **kontenery** w zależności od środowiska mogą pojawić się dowolne z trzech typów zasobów:

![Host kontenerów](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Hosty kontenerów — maszyny wirtualne korzystające z platformy Docker 

![Usługa Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Klastry usługi Azure Kubernetes Service (AKS)

![Rejestr kontenerów](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Rejestry Azure Container Registry (ACR) — wyświetlane tylko w przypadku korzystania z warstwy cenowej standardowa i w przypadku włączenia pakietu Azure Container Registry.

Aby uzyskać instrukcje dotyczące korzystania z funkcji zabezpieczeń kontenerów, zobacz [monitorowanie zabezpieczeń kontenerów](monitor-container-security.md).

Zalety pakietu Azure Container Registry są wyjaśnione w [tym miejscu](azure-container-registry-integration.md)

Zalety pakietu usług Kubernetes Services są wyjaśnione w [tym miejscu](azure-kubernetes-service-integration.md)

[Karta ![kontenerów](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Aby zapoznać się z zaleceniami dotyczącymi określonego zasobu na liście, kliknij ten zasób.

#### <a name="visibility-into-container-registries"></a>Widoczność rejestrów kontenerów

Na przykład kliknięcie rejestru ASC-demonstracyjnej ACR z listy pokazanej w grafice powyżej prowadzi do tej strony szczegółów:

[![zalecenia dotyczące określonego rejestru ACR](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Wgląd w kontenery hostowane na maszynach z systemem Linux IaaS

Po kliknięciu jednej z maszyn wirtualnych z systemem Docker zostanie wyświetlona strona szczegółów z informacjami dotyczącymi kontenerów na komputerze, takich jak wersja platformy Docker i Liczba obrazów uruchomionych na hoście.

![Zalecenia dotyczące maszyny wirtualnej z systemem Docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Zalecenia dotyczące zabezpieczeń oparte na teście usług CIS dla platformy Docker

Usługa Security Center skanuje konfiguracje platformy Docker i zapewnia wgląd w błędy konfiguracji poprzez dostarczenie listy wszystkich ocenionych reguł zakończonych niepowodzeniem. Security Center zawiera wytyczne ułatwiające szybkie rozwiązywanie tych problemów i oszczędność czasu. Usługa Security Center stale ocenia konfiguracje platformy Docker i udostępnia Ci ich najnowszy stan.

![Karta kontener](./media/security-center-container-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące artykuły:

* [Pełna lista referencyjna zaleceń dotyczących zabezpieczeń Azure Security Center](recommendations-reference.md)
* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w Azure Security Center](security-center-sql-service-recommendations.md)