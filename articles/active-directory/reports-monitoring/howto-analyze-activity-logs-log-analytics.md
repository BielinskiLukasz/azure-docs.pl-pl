---
title: Analizowanie dzienników aktywności przy użyciu dzienników Azure Monitor | Microsoft Docs
description: Dowiedz się, jak analizować Azure Active Directory dzienniki aktywności przy użyciu dzienników Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d6212692465270182db541889bed5f03a08a345
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74008289"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analizowanie dzienników aktywności usługi Azure AD za pomocą dzienników Azure Monitor

Po [zintegrowaniu dzienników aktywności usługi Azure AD z dziennikami Azure monitor](howto-integrate-activity-logs-with-log-analytics.md)można wykorzystać możliwości dzienników Azure monitor, aby uzyskać wgląd w swoje środowisko. Możesz również zainstalować widoki usługi [log Analytics dla dzienników aktywności w usłudze Azure AD](howto-install-use-log-analytics-views.md) , aby uzyskać dostęp do wstępnie skompilowanych raportów dotyczących zdarzeń inspekcji i logowania w Twoim środowisku.

W tym artykule dowiesz się, jak analizować dzienniki aktywności usługi Azure AD w obszarze roboczym Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne 

Aby wykonać następujące czynności, potrzebne są:

* Log Analytics obszar roboczy w ramach subskrypcji platformy Azure. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Najpierw wykonaj kroki umożliwiające [kierowanie dzienników aktywności usługi Azure AD do obszaru roboczego log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
*  [Dostęp](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) do obszaru roboczego usługi log Analytics
* Poniższe role w Azure Active Directory (Jeśli uzyskujesz dostęp do Log Analytics za pomocą portalu Azure Active Directory)
    - Administrator zabezpieczeń
    - Czytelnik zabezpieczeń
    - Czytelnik raportu
    - Administrator globalny
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Przejdź do obszaru roboczego Log Analytics

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **dzienniki** w sekcji **monitorowanie** , aby otworzyć obszar roboczy log Analytics. Obszar roboczy zostanie otwarty z zapytaniem domyślnym.

    ![Zapytanie domyślne](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Wyświetl schemat dzienników aktywności usługi Azure AD

Dzienniki są wypychane do tabel **AuditLogs** i **SigninLogs** w obszarze roboczym. Aby wyświetlić schemat dla następujących tabel:

1. W obszarze domyślny widok zapytania w poprzedniej sekcji Wybierz pozycję **schemat** i rozwiń obszar roboczy. 

2. Rozwiń sekcję **Zarządzanie dziennikami** , a następnie rozwiń pozycję **AuditLogs** lub **SignInLogs** , aby wyświetlić schemat dziennika.
    ![Dzienniki inspekcji](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![dzienników logowania](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Wykonywanie zapytań względem dzienników aktywności usługi Azure AD

Teraz, gdy masz dzienniki w obszarze roboczym, możesz teraz uruchamiać zapytania względem nich. Na przykład aby uzyskać najważniejsze aplikacje używane w ostatnim tygodniu, Zastąp zapytanie domyślne następującym poleceniem i wybierz polecenie **Uruchom** .

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Aby uzyskać najważniejsze zdarzenia inspekcji z ostatniego tygodnia, użyj następującego zapytania:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Alert dotyczący danych dziennika aktywności usługi Azure AD

Możesz również skonfigurować alerty w zapytaniu. Na przykład, aby skonfigurować alert, gdy w ostatnim tygodniu użyto więcej niż 10 aplikacji:

1. W obszarze roboczym wybierz pozycję **Ustaw Alert** , aby otworzyć stronę **Tworzenie reguły** .

    ![Ustawianie alertu](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Wybierz domyślne **kryteria alertów** utworzone w alercie i zaktualizuj **próg** w domyślnej metryce do 10.

    ![Kryteria alertu](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Wprowadź nazwę i opis alertu, a następnie wybierz poziom ważności. W naszym przykładzie możemy ustawić ją na **informacyjną**.

4. Wybierz **grupę akcji** , która będzie otrzymywać alerty w przypadku wystąpienia sygnału. Możesz wybrać powiadomienie zespołu za pośrednictwem poczty e-mail lub wiadomości SMS lub można zautomatyzować akcję za pomocą elementów webhook, Azure Functions lub Logic Apps. Dowiedz się więcej na temat [tworzenia grup alertów i zarządzania nimi w Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Po skonfigurowaniu alertu wybierz pozycję **Utwórz alert** , aby go włączyć. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instalowanie i używanie wstępnie utworzonych widoków dla dzienników aktywności usługi Azure AD

Możesz również pobrać wstępnie skompilowane widoki usługi log Analytics dla dzienników aktywności w usłudze Azure AD. Widoki zawierają kilka raportów związanych z typowymi scenariuszami związanymi z inspekcją i zdarzeniami logowania. Możesz również otrzymywać alerty dotyczące dowolnych danych zawartych w raportach, korzystając z kroków opisanych w poprzedniej sekcji.

* **Zdarzenia aprowizacji konta usługi Azure AD**: ten widok przedstawia raporty dotyczące aktywności związanej z obsługą inspekcji, takie jak liczba nowych użytkowników i niepowodzenia aprowizacji, liczba zaktualizowanych użytkowników i aktualizacja niepowodzeń oraz liczba użytkowników, którym cofnięto Inicjowanie obsługi administracyjnej i odpowiadające im błędy.    
* **Zdarzenia logowania**: ten widok przedstawia najbardziej odpowiednie raporty związane z monitorowaniem aktywności, takie jak logowania według aplikacji, użytkownika, urządzenia, a także widok podsumowania, który śledzi liczbę logowań w czasie.
* **Użytkownicy**korzystający z zgody: ten widok przedstawia raporty dotyczące zgody użytkownika, takie jak zgoda użytkownika, logowania użytkowników, którzy udzieliły zgody, a także logowania przez aplikacje dla wszystkich aplikacji opartych na zgodzie. 

Dowiedz się, jak [zainstalować widoki analizy dzienników dla dzienników aktywności usługi Azure AD i używać ich](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z zapytaniami w dziennikach Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Tworzenie grup alertów i zarządzanie nimi w Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Instalowanie i Używanie widoków usługi log Analytics na potrzeby Azure Active Directory](howto-install-use-log-analytics-views.md)
