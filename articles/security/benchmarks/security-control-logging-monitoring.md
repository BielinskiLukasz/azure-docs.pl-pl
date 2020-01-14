---
title: Kontrola zabezpieczeń platformy Azure — rejestrowanie i monitorowanie
description: Rejestrowanie i monitorowanie kontroli zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ceb82bf729470f9996bc9ecc766329a0d1242ec4
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930030"
---
# <a name="security-control-logging-and-monitoring"></a>Kontrola zabezpieczeń: rejestrowanie i monitorowanie

Funkcja rejestrowania i monitorowania zabezpieczeń koncentruje się na działaniach związanych z włączaniem, uzyskiwaniem i przechowywaniem dzienników inspekcji dla usług platformy Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Firma Microsoft przechowuje źródła czasu dla zasobów platformy Azure, ale istnieje możliwość zarządzania ustawieniami synchronizacji czasu dla zasobów obliczeniowych.

Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych platformy Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.2 | 6,5, 6,6 | Klient |

Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor należy używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. Jak dołączyć wskaźnik na platformie Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Jak zbierać dzienniki platformy i metryki z Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Jak rozpocząć pracę z usługą Azure Monitor i integracją SIEM innych firm:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.3 | 6,2, 6,3 | Klient |

Włącz ustawienia diagnostyczne w zasobach platformy Azure, aby uzyskać dostęp do dzienników inspekcji, zabezpieczeń i diagnostyki. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

Jak zbierać dzienniki platformy i metryki z Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.4 | 6,2, 6,3 | Klient |

Jeśli zasób obliczeniowy należy do firmy Microsoft, firma Microsoft jest odpowiedzialna za ich monitorowanie. Jeśli zasób obliczeniowy należy do organizacji, odpowiedzialność za jego monitorowanie. Aby monitorować system operacyjny, można użyć Azure Security Center. Dane zbierane przez Security Center z systemu operacyjnego obejmują typ i wersję systemu operacyjnego, system operacyjny (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwę komputera, adresy IP i zalogowanego użytkownika. Agent Log Analytics również zbiera pliki zrzutu awaryjnego.

Jak zbierać dzienniki wewnętrznego hosta maszyny wirtualnej platformy Azure z Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Opis zbierania danych Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.5 | 6.4 | Klient |

W Azure Monitor Skonfiguruj okres przechowywania Log Analytics obszaru roboczego zgodnie z przepisami w zakresie zgodności w organizacji. Używaj kont usługi Azure Storage do przechowywania długoterminowego/archiwizowania.

Jak ustawić parametry przechowywania dziennika dla obszarów roboczych Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2,6 | 6.7 | Klient |

Analizuj i monitoruj dzienniki pod kątem nietypowego zachowania i regularnie Przeglądaj wyniki. Użyj Azure Monitor Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm. 

Jak dołączyć wskaźnik na platformie Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Informacje o obszarze roboczym Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak wykonywać niestandardowe zapytania w Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: Włączanie alertów dla nietypowego działania

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.7 | 6.8 | Klient |

Użyj Azure Security Center z Log Analytics obszarem roboczym do monitorowania i generowania alertów dotyczących nietypowego działania znalezionego w dziennikach i zdarzeniach zabezpieczeń.

Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej.

Jak dołączyć wskaźnik na platformie Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Jak zarządzać alertami w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Jak ostrzec dane dziennika usługi log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.8 | 8,6 | Klient |

Włącz zbieranie zdarzeń chroniących przed złośliwym kodem dla Virtual Machines platformy Azure i Cloud Services.

Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Virtual Machines:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Jak skonfigurować oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla Cloud Services:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Informacje na temat ochrony przed złośliwym oprogramowaniem firmy Microsoft:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.9 | 8.7 | Klient |

Zaimplementuj rozwiązanie innych firm do rejestrowania w systemie DNS.

## <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 2.1 | 8.8 | Klient |

Ręcznie skonfiguruj rejestrację konsoli i transkrypcję programu PowerShell w poszczególnych węzłach.

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [tożsamość i Access Control](security-control-identity-access-control.md)
