---
title: Eksportuj alerty Azure Security Center i zalecenia do rozwiązań Siem | Microsoft Docs
description: W tym artykule wyjaśniono, jak skonfigurować ciągły eksport alertów zabezpieczeń i zaleceń do rozwiązań Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: bad3be86bd85f7e5cebcf9445d14d836c73a87ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470800"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Eksportowanie alertów zabezpieczeń i zaleceń (wersja zapoznawcza)

Azure Security Center generuje szczegółowe alerty zabezpieczeń i zalecenia. Można je wyświetlać w portalu lub za pomocą narzędzi programistycznych. Może być również konieczne wyeksportowanie tych informacji lub wysłanie ich do innych narzędzi do monitorowania w danym środowisku. 

W tym artykule opisano zestaw narzędzi (wersja zapoznawcza) służących do eksportowania alertów i zaleceń ręcznie lub w stały sposób ciągły.

Za pomocą tych narzędzi możesz:

* Generuj szczegółowe raporty jako woluminy CSV
* Eksportuj do Log Analytics obszarów roboczych
* Eksportuj do Event Hubs platformy Azure (w przypadku integracji z usługą rozwiązań Siem innej firmy)

## <a name="setting-up-a-continuous-export"></a>Konfigurowanie eksportu ciągłego

1. Na pasku bocznym Security Center kliknij pozycję **cennik & Ustawienia**.

1. Wybierz określoną subskrypcję, dla której chcesz skonfigurować eksportowanie danych.
    
1. Na pasku bocznym strony Ustawienia dla tej subskrypcji wybierz pozycję **eksport ciągły (wersja zapoznawcza)** .

    [Opcje eksportu![w Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) W tym miejscu są wyświetlane opcje eksportowania. Dla każdego dostępnego elementu docelowego eksportu istnieje karta. 

1. Wybierz typ danych, który chcesz wyeksportować, i wybierz spośród filtrów dla każdego typu (na przykład wyeksportuj tylko alerty o wysokiej ważności).

1. W obszarze "Eksportuj element docelowy" Wybierz miejsce, w którym chcesz zapisać dane. Dane można zapisywać w miejscu docelowym w innej subskrypcji (na przykład w centralnym wystąpieniu centrum zdarzeń lub w centralnym obszarze roboczym Log Analytics).

1. Kliknij przycisk **Save** (Zapisz).

## <a name="continuous-export-through-azure-event-hubs"></a>Eksport ciągły za pomocą usługi Azure Event Hubs  

> [!NOTE]
> Najbardziej efektywną metodą przesyłania strumieniowego danych monitorowania do zewnętrznych narzędzi w większości przypadków jest użycie usługi Azure Event Hubs. [Ten artykuł](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) zawiera krótki opis sposobu przesyłania strumieniowego danych monitorowania z różnych źródeł do centrum zdarzeń oraz linki do szczegółowych wskazówek.

> [!NOTE]
> Jeśli wcześniej wyeksportowano Security Center alerty do SIEM przy użyciu dziennika aktywności platformy Azure, Poniższa procedura zastępuje tę metodologię.

Aby wyświetlić schematy zdarzeń wyeksportowanych typów danych, odwiedź [schematy zdarzeń centrum zdarzeń](https://aka.ms/ASCAutomationSchemas).

### <a name="to-integrate-with-a-siem"></a>Aby zintegrować z usługą SIEM 

Po skonfigurowaniu ciągłego eksportowania wybranych Security Center danych do usługi Azure Event Hubs można skonfigurować odpowiedni łącznik na SIEM, postępując zgodnie z poniższymi instrukcjami.

Postępuj zgodnie z instrukcjami dotyczącymi Twojego SIEM na [tej stronie](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) i użyj odpowiedniego łącznika:

* **Splunk** — użyj [dodatku Azure monitor dla Splunk](https://splunkbase.splunk.com/app/3534/)
* **IBM QRadar** — Użyj [ręcznie skonfigurowanego źródła dziennika](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight** — Użyj [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Jeśli używasz funkcji **wskaźnikowej platformy Azure**, Użyj dostępnego w tym celu [łącznika](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) Azure Security Center natywnych alertów.

Ponadto jeśli chcesz automatycznie przenieść ciągłe wyeksportowane dane ze skonfigurowanego centrum zdarzeń na platformę Azure Eksplorator danych, Skorzystaj z instrukcji w temacie pozyskiwanie [danych z centrum zdarzeń w usłudze azure Eksplorator danych](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).


## <a name="continuous-export-to-a-log-analytics-workspace"></a>Eksport ciągły do obszaru roboczego Log Analytics

Aby wyeksportować do obszaru roboczego Log Analytics, musisz mieć Security Centere warstwy Bezpłatna lub standardowa Log Analytics dostępne w obszarze roboczym. Jeśli używasz Azure Portal, rozwiązanie Security Center warstwy Bezpłatna jest automatycznie włączane po włączeniu eksportu ciągłego. Jeśli jednak konfigurujesz ustawienia eksportu ciągłego programowo, musisz ręcznie wybrać warstwę cenową dla wymaganego obszaru roboczego z poziomu **ustawień & cenowych**.  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabele i schematy

Alerty zabezpieczeń i zalecenia są przechowywane odpowiednio w tabelach *SecurityAlert* i *SecurityRecommendations* . Nazwa rozwiązania Log Analytics zawierającego te tabele zależy od tego, czy korzystasz z warstwy Bezpłatna, czy standardowa (zobacz [Cennik](security-center-pricing.md)): zabezpieczenia ("Security and Audit") lub SecurityCenterFree.

![Tabela * SecurityAlert * w Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Aby wyświetlić schematy zdarzeń wyeksportowanych typów danych, odwiedź [log Analytics schematy tabel](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Wyświetlanie wyeksportowanych alertów zabezpieczeń i zaleceń w Azure Monitor

W niektórych przypadkach można wyświetlić wyeksportowane alerty zabezpieczeń i/lub zalecenia w [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor zapewnia ujednolicone środowisko alertów dla różnych alertów platformy Azure, w tym dzienników diagnostycznych, alertów metryk i alertów niestandardowych opartych na zapytaniach Log Analytics obszaru roboczego.

Aby wyświetlić alerty i zalecenia z Security Center w Azure Monitor, skonfiguruj regułę alertu na podstawie zapytań Log Analytics (alert dziennika):

1. Na stronie **alerty** Azure Monitor kliknij pozycję **Nowa reguła alertu**.

    ![Strona alertów Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na stronie Tworzenie reguły Skonfiguruj nową regułę (w taki sam sposób jak w przypadku konfigurowania [reguły alertu dziennika w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * W obszarze **zasób**wybierz obszar roboczy log Analytics, do którego wyeksportowano alerty zabezpieczeń i zalecenia.

    * W obszarze **warunek**wybierz opcję **Wyszukiwanie w dzienniku niestandardowym**. Na wyświetlonej stronie Skonfiguruj zapytanie, okres lookback i okres częstotliwości. W zapytaniu wyszukiwania można wpisać *SecurityAlert* lub *SecurityRecommendation* , aby wykonać zapytanie dotyczące typów danych, które Security Center ciągle eksportować w miarę włączania eksportu ciągłego do log Analytics funkcji. 
    
    * Opcjonalnie Skonfiguruj [grupę akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) , którą chcesz wyzwolić. Grupy akcji mogą wyzwalać wysyłanie wiadomości e-mail, bilety narzędzia ITSM, elementy webhook i inne elementy.
    ![regułę alertu Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Zobaczysz teraz nowe alerty i zalecenia dotyczące Azure Security Center (w zależności od konfiguracji) w Azure Monitor alertach z automatycznym wyzwalaniem grupy akcji (jeśli została podana).

## <a name="manual-one-time-export-of-security-alerts"></a>Ręczne eksportowanie alertów zabezpieczeń jednorazowe

Aby pobrać raport CSV dotyczący alertów lub zaleceń, Otwórz stronę **alerty zabezpieczeń** lub **zalecenia** , a następnie kliknij przycisk **Pobierz raport CSV** .

[![pobierać dane alertów jako plik CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Te raporty zawierają alerty i zalecenia dotyczące zasobów z aktualnie wybranych subskrypcji.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania ciągłego eksportowania zaleceń i alertów. Wiesz również, jak pobrać dane alertów jako plik CSV. 

W przypadku pokrewnego materiału zapoznaj się z następującą dokumentacją: 

- [Dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentacja usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentacja usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Schematy automatyzacji przepływu pracy i typy danych eksportu ciągłego](https://aka.ms/ASCAutomationSchemas)
