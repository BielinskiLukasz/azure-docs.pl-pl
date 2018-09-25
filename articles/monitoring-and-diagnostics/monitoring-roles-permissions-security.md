---
title: Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń za pomocą usługi Azure Monitor
description: Dowiedz się, jak ograniczyć dostęp do monitorowania zasobów za pomocą usługi Azure Monitor wbudowane role i uprawnienia.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: c99186d73886041d92bea38b0dd4dc17f55001e4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977863"
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń za pomocą usługi Azure Monitor
Wiele zespołów należy ściśle regulowania dostępu do danych monitorowania i ustawień. Na przykład, jeśli posiadasz elementy członkowskie zespołu, którzy pracują wyłącznie na temat monitorowania (pracowników działu pomocy technicznej, inżynierom devops) lub korzystając z dostawcą usługi zarządzanej, można przyznać im dostęp do danych monitorowania tylko jednocześnie ograniczając możliwość tworzenia, modyfikowania, lub Usuń zasoby. W tym artykule pokazano, jak szybko wbudowana rola RBAC monitorowania są stosowane do użytkownika na platformie Azure lub utworzyć własne niestandardowe rolę dla użytkownika, który musi mieć ograniczone uprawnienia monitorowania. Następnie omówiono zagadnienia dotyczące zabezpieczeń na zasoby dotyczące usługi Azure Monitor i jak można ograniczyć dostęp do danych, które zawierają.

## <a name="built-in-monitoring-roles"></a>Wbudowane role monitorowania
Usługa Azure Monitor wbudowane role mają na celu ograniczenia dostępu do zasobów w ramach subskrypcji, ograniczając osoby odpowiedzialne za monitorowanie infrastruktury, aby uzyskać i skonfigurować je. Usługa Azure Monitor zapewnia dwie role out-of--box: monitorowanie Czytelnik i współautor monitorowania.

### <a name="monitoring-reader"></a>Czytelnik monitorowania
Osoby z przypisaną rolą Czytelnik monitorowania można wyświetlić wszystkie dane monitorowania w ramach subskrypcji, ale nie można zmodyfikować dowolnego zasobu lub edytować wszelkie ustawienia związane z monitorowania zasobów. Ta rola jest odpowiednia dla użytkowników w organizacji, takie jak operacje lub pomocy technicznej inżynierów, którzy muszą mieć możliwość:

* Wyświetlać pulpity nawigacyjne monitorowania w portalu i Utwórz swoje własne prywatne pulpity nawigacyjne monitorowania.
* Wyświetl reguły alertu zdefiniowane w [Azure Alerts](monitoring-overview-unified-alerts.md)
* Zapytanie dotyczące korzystania z metryk [interfejsu API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), [poleceń cmdlet programu PowerShell](insights-powershell-samples.md), lub [międzyplatformowego interfejsu wiersza polecenia](insights-cli-samples.md).
* Wyślij zapytanie do dziennika aktywności przy użyciu portalu, interfejsu API REST usługi Azure Monitor, poleceń cmdlet programu PowerShell lub wieloplatformowego interfejsu wiersza polecenia.
* Widok [ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) dla zasobu.
* Widok [profil dziennika](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) dla subskrypcji.
* Wyświetl ustawienia automatycznego skalowania.
* Wyświetl działania alertu i ustawienia.
* Dostęp do danych usługi Application Insights i wyświetlanie danych analitycznych w sztucznej Inteligencji.
* Wyszukiwanie danych obszaru roboczego usługi Log Analytics, w tym dane użycia dla obszaru roboczego.
* Wyświetlanie grup zarządzania usługi Log Analytics.
* Pobieranie schematu wyszukiwania usługi Log Analytics.
* Lista pakietów intelligence Pack usługi Log Analytics.
* Pobierz i wykonaj zapisanego wyszukiwania analizy dzienników.
* Pobieranie konfiguracji magazynu analizy dzienników.

> [!NOTE]
> Ta rola nie daje dostęp do odczytu danych dziennika, które są przesyłane strumieniowo do Centrum zdarzeń lub przechowywane na koncie magazynu. [Zobacz poniżej](#security-considerations-for-monitoring-data) informacji na temat konfigurowania dostępu do tych zasobów.
> 
> 

### <a name="monitoring-contributor"></a>Współautor monitorowania
Osoby przypisane do roli Współautor monitorowania można wyświetlić wszystkie dane monitorowania w ramach subskrypcji i utworzyć lub zmodyfikować ustawienia monitorowania, ale nie można modyfikować żadnych innych zasobów. Ta rola jest podzbiorem roli Czytelnik monitorowania i jest przeznaczona dla członków zespołu monitorowania lub dostawcy usług zarządzanych, które oprócz powyższego, uprawnienia muszą być również możliwość organizacji:

* Publikuj monitorowania pulpitów nawigacyjnych jako udostępniony pulpit nawigacyjny.
* Ustaw [ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) dla resource.*
* Ustaw [profil dziennika](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) dla subscription.*
* Działanie reguły alertów i ustawienia za pośrednictwem [Azure Alerts](monitoring-overview-unified-alerts.md).
* Utwórz testy sieci web usługi Application Insights i składników.
* Obszar roboczy usługi Log Analytics listy udostępnionych kluczy.
* Włącz lub wyłącz pakietów intelligence Pack usługi Log Analytics.
* Tworzenie i usuwanie i wykonywanie zapisanego wyszukiwania analizy dzienników.
* Tworzenie i usuwanie konfiguracji magazynu analizy dzienników.

* użytkownik musi również oddzielnie udzielane uprawnienie ListKeys nad zasobem docelowym (magazynu konta lub zdarzenia przestrzeń nazw Centrum) można ustawić profil dziennika lub ustawienie diagnostyczne.

> [!NOTE]
> Ta rola nie daje dostęp do odczytu danych dziennika, które są przesyłane strumieniowo do Centrum zdarzeń lub przechowywane na koncie magazynu. [Zobacz poniżej](#security-considerations-for-monitoring-data) informacji na temat konfigurowania dostępu do tych zasobów.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitorowanie uprawnień i ról RBAC niestandardowych
Jeśli powyższe wbudowanych ról nie dokładne potrzeb Twojego zespołu, możesz to zrobić [utworzyć niestandardową rolę RBAC](../role-based-access-control/custom-roles.md) przy użyciu bardziej szczegółowych uprawnień. Poniżej przedstawiono typowe operacje usługi Azure Monitor RBAC przy użyciu ich opisy.

| Operacja | Opis |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Grupy akcji odczytu/zapisu/usuwania. |
| Microsoft.Insights/ActivityLogAlerts/[Read, zapis, usuwanie] |Alerty dziennika aktywności odczytu/zapisu/usuwania. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Odczyt/zapis/usuwanie reguły alertów (z alertów klasycznych). |
| Microsoft.Insights/AlertRules/Incidents/Read |Lista zdarzeń (Historia reguły alertu wyzwalane) dla reguł alertów. Dotyczy to tylko do portalu. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Ustawienia skalowania automatycznego odczytu/zapisu/usuwania. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Odczyt/zapis/usuwanie ustawień diagnostycznych. |
| Microsoft.Insights/EventCategories/Read |Wylicza wszystkie kategorie w dzienniku aktywności. Używane przez witryny Azure portal. |
| Microsoft.Insights/eventtypes/digestevents/Read |To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. |
| Microsoft.Insights/eventtypes/values/Read |Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w ramach subskrypcji. To uprawnienie ma zastosowanie do portalu i programowy dostęp do dziennika aktywności. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Odczyt/zapis/usuwanie ustawień diagnostycznych dla dzienników przepływu sieci. |
| Microsoft.Insights/LogDefinitions/Read |To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Profile dziennika odczytu/zapisu/usuwania (przesyłanie strumieniowe dzienników aktywności do event hub lub konta magazynu). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Odczyt/zapis/usuwanie niemal w czasie rzeczywistym alertów dotyczących metryk |
| Microsoft.Insights/MetricDefinitions/Read |Przeczytaj definicje metryk (lista dostępnych typów metryki dla zasobu). |
| Microsoft.Insights/Metrics/Read |Odczytać metryki dla zasobu. |
| Microsoft.Insights/Register/Action |Zarejestruj dostawcę zasobów usługi Azure Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Alerty dziennika odczytu/zapisu/usuwania usługi Application Insights. |



> [!NOTE]
> Dostęp do alertów, ustawienia diagnostyczne i metryki dla zasobu wymaga, czy użytkownik ma dostęp do odczytu jako typ zasobu, a zakres tego zasobu. Profil diagnostyczny ustawienie lub dziennika, które powoduje archiwizowanie na koncie magazynu lub strumienie do usługi event hubs, tworzenie ("Zapisz") wymaga użytkownik również musi mieć uprawnienie ListKeys nad zasobem docelowym.
> 
> 

Na przykład przy użyciu powyższej tabeli, można utworzyć niestandardową rolę RBAC dla "działania czytnik dziennika" następująco:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Zagadnienia dotyczące zabezpieczeń na potrzeby danych monitorowania
Monitorowanie danych — szczególnie plików dziennika — mogą zawierać poufne informacje, takie jak adresy IP lub nazwy użytkownika. Monitorowanie danych z platformy Azure jest oferowana w trzech głównych form:

1. Dziennik aktywności w tym artykule opisano wszystkie akcje płaszczyznę kontroli w ramach subskrypcji platformy Azure.
2. Dzienniki diagnostyczne, które są dzienniki emitowane przez zasób.
3. Metryk, które są emitowane przez zasoby.

Wszystkie trzy typy danych mogą być przechowywane na koncie magazynu lub przesyłane strumieniowo do Centrum zdarzeń, które są ogólnego przeznaczenia zasobów platformy Azure. Ponieważ są to zasoby ogólnego przeznaczenia, tworzenie, usuwanie i uzyskiwać dostęp do nich jest operacją wymagającą odpowiednich uprawnień, zarezerwowane dla administratora. Sugerujemy, użyj następujących wskazówek dla zasobów związanych z monitorowaniem aby uniemożliwić nadużycia:

* Użyj konta magazynu jednego, przeznaczonego do monitorowania danych. Jeśli potrzebujesz oddzielnych danych monitorowania do wielu kont magazynu, nigdy nie udostępniaj użycia konta magazynu między monitorowania i -monitorowania danych, ponieważ przypadkowo mogą dawać tych, którzy potrzebują dostępu tylko do monitorowania danych (na przykład SIEM innych firm) dostęp do innych monitorowania danych.
* Użyj jednego, przeznaczonego nazw usługi Service Bus lub Centrum zdarzeń we wszystkich ustawień diagnostycznych dla tego samego powodu opisanych powyżej.
* Ograniczanie dostępu do konta usługi storage związanych z monitorowaniem lub centrów zdarzeń, przechowując je w oddzielnej grupie zasobów, a [Użyj zakresu](../role-based-access-control/overview.md#scope) Twoja monitorowania ról, aby ograniczyć dostęp do tej grupy zasobów.
* Nigdy nie należy przyznać uprawnienia klucze listy dla konta magazynu lub centrów zdarzeń w zakresie subskrypcji, po użytkownik potrzebuje tylko dostępu do danych monitorowania. Zamiast tego należy nadać te uprawnienia do użytkownika na zasób lub grupa zasobów (Jeśli masz dedykowane monitorowania grupy zasobów) zakresu.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Ograniczanie dostępu do konta usługi storage związanych z monitorowaniem
Gdy użytkownik lub aplikacja potrzebuje dostępu do danych na koncie magazynu monitorowania, należy [Generowanie sygnatury dostępu Współdzielonego konta](https://msdn.microsoft.com/library/azure/mt584140.aspx) na koncie magazynu, który zawiera dane monitorowania z poziomu usługi dostęp tylko do odczytu do magazynu obiektów blob. W programie PowerShell może to wyglądać jak:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Można następnie przekazać ten token do jednostki, musi odczytać z magazynu konta i jego można wyświetlić listę i odczytywanie wszystkich obiektów blob w ramach tego konta magazynu.

Alternatywnie Jeśli wymagane jest sterowanie to uprawnienie z RBAC, można przyznać tej jednostki uprawnienie Microsoft.Storage/storageAccounts/listkeys/action na tym koncie magazynu określonym. Jest to niezbędne dla użytkowników, którzy muszą mieć możliwość ustawienia diagnostyczne lub dziennika profilu zarchiwizować na koncie magazynu. Na przykład można utworzyć następujące niestandardową rolę RBAC dla użytkownika lub aplikacji, która musi tylko do odczytu z jednego konta magazynu:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> Uprawnienia ListKeys pozwala użytkownikowi na liście kluczy konta magazynu podstawowego i pomocniczego. Te klucze Przyznaj użytkownikowi wszystkie podpisane uprawnienia (Odczyt, zapis, Utwórz obiekty BLOB, usuwanie obiektów blob, itp.) na wszystkich podpisany usług (obiektu blob, kolejka, tabela, plik) w ramach tego konta magazynu. Zalecamy używanie sygnatury dostępu Współdzielonego konta opisanych powyżej, jeśli jest to możliwe.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Ograniczanie dostępu do centrów zdarzeń związanych z monitorowaniem
Podobny wzorzec można wykonać za pomocą usługi event hubs, ale najpierw należy utworzyć dedykowane reguły autoryzacji nasłuchiwania. Jeśli chcesz udzielić dostępu do aplikacji, która musi tylko do nasłuchiwania centra zdarzeń związanych z monitorowaniem, wykonaj następujące czynności:

1. Utwórz zasady dostępu współdzielonego na koncentratory zdarzeń, które zostały utworzone dla przesyłania strumieniowego danych monitorowania z tylko oświadczenia nasłuchiwania. Można to zrobić w portalu. Na przykład może być wywołasz ją "monitoringReadOnly." Jeśli to możliwe należy udzielić tego klucza bezpośrednio do użytkownika i pomiń następny krok.
2. Jeśli użytkownik musi można pobrać klucza usługi ad-hoc, Przyznaj użytkownikowi akcję klucze listy dla tego Centrum zdarzeń. To jest również dla użytkowników, którzy muszą mieć możliwość ustawienia diagnostyczne lub profil dziennika do strumienia usługi event hubs. Na przykład można utworzyć regułę RBAC:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorowanie w zabezpieczonej sieci wirtualnej

Usługa Azure Monitor wymaga dostępu do zasobów platformy Azure do świadczenia usług, które zostanie włączone. Jeśli chcesz monitorować zasobów platformy Azure, zabezpieczając nadal je przed dostępem do publicznej sieci Internet, aby umożliwić następujące ustawienia.

### <a name="secured-storage-accounts"></a>Konta magazynu zabezpieczone 

Monitorowanie danych często są zapisywane na koncie magazynu. Warto upewnić się, że dane skopiowane do konta magazynu nie są dostępne przez nieautoryzowanych użytkowników. Dla dodatkowego bezpieczeństwa możesz zablokować dostęp do sieci, aby zezwalać tylko na autoryzowanych zasobów i zaufanych dostępu do usług firmy Microsoft do konta magazynu przez ograniczenie konta magazynu do użycia "wybranych sieci".
![Okno dialogowe Ustawienia usługi Azure Storage](./media/monitoring-roles-permissions-security/secured-storage-example.png) usługi Azure Monitor jest uważany za jeden z nich "zaufanych usług firmy Microsoft" Jeśli zezwolisz na dostęp zabezpieczony magazyn do zaufanych usług firmy Microsoft, usługa Azure monitor będzie mieć dostęp do konta magazynu zabezpieczone; Włączanie zapisywanie dzienników diagnostycznych usługi Azure Monitor, dzienników aktywności i metryki do swojego konta magazynu w tych warunkach chronionych. Umożliwi to również usługi Log Analytics w celu odczytu dzienników z bezpiecznego magazynu.   

Aby uzyskać więcej informacji, zobacz [Network security i Azure Storage](../storage/common/storage-network-security.md)
 
### <a name="secured-virtual-networks-with-service-endpoints"></a>Zabezpieczone sieci wirtualnych z punktami końcowymi usługi 

Sieci wirtualne (Vnet) umożliwiają ograniczanie ruchu, aby zezwolić tylko na określony ruch do komunikowania się z zasobami platformy Azure. Możesz określić punkty końcowe usługi do rozszerzenia Twojej sieci wirtualnej, aby uwzględnić usługi Azure Monitor; Spowoduje to włączenie zasobów w celu conitinue bezpiecznie wysyłać informacje rejestrowania i metryk do usługi Azure Monitor z sieciami wirtualnymi.  

Aby uzyskać więcej informacji, zobacz [punkty końcowe sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md). 


## <a name="next-steps"></a>Kolejne kroki
* [Przeczytaj o ROLACH i uprawnienia w usłudze Resource Manager](../role-based-access-control/overview.md)
* [Zapoznaj się z omówieniem monitorowania na platformie Azure](monitoring-overview.md)

