---
title: Test porównawczy zabezpieczeń systemu Azure v2 — rejestrowanie i wykrywanie zagrożeń
description: Rejestrowanie testów zabezpieczeń Azure w wersji 2 i wykrywanie zagrożeń
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 24ddca37a729a459382fc96a2f6e0ef1a7832f37
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059298"
---
# <a name="security-control-logging-and-threat-detection"></a>Kontrola zabezpieczeń: rejestrowanie i wykrywanie zagrożeń

Rejestrowanie i wykrywanie zagrożeń obejmują kontrolę w zakresie wykrywania zagrożeń na platformie Azure oraz włączanie, zbieranie i przechowywanie dzienników inspekcji dla usług platformy Azure. Obejmuje to Włączanie procesów wykrywania, badania i korygowania przy użyciu kontrolek w celu generowania alertów o wysokiej jakości z natywnym wykrywaniem zagrożeń w usługach platformy Azure. obejmuje to również zbieranie dzienników z Azure Monitor, scentralizowanie analizy zabezpieczeń dzięki wskaźnikom danych platformy Azure, synchronizacji czasu i przechowywaniu dzienników. 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Włączanie wykrywania zagrożeń dla zasobów platformy Azure

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Upewnij się, że monitorujesz różne typy zasobów platformy Azure pod kątem potencjalnych zagrożeń i anomalii. Skup się na otrzymywaniu alertów o wysokiej jakości, aby zmniejszyć liczbę fałszywych dodatnich dla analityków do sortowania. Alerty mogą być źródłem danych dziennika, agentów lub innych danych.

Użyj Azure Security Center wbudowanej funkcji wykrywania zagrożeń, która jest oparta na monitorowaniu danych telemetrycznych usługi platformy Azure i analizowaniu dzienników usług. Dane są zbierane przy użyciu agenta Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z systemu i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy. 

Ponadto przy użyciu wskaźnikowego platformy Azure można tworzyć reguły analityczne, które wskazują na zagrożenia, które pasują do określonych kryteriów w danym środowisku. Reguły generują zdarzenia w przypadku dopasowania kryteriów, aby można było zbadać każde zdarzenie. Na platformie Azure można także zaimportować analizę zagrożeń innych firm, aby zwiększyć możliwości wykrywania zagrożeń. 

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../../security-center/threat-protection.md)

- [Przewodnik dotyczący podręcznika Azure Security Center alertów zabezpieczeń](../../security-center/alerts-reference.md)

- [Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń](../../sentinel/tutorial-detect-threats-custom.md)

- [Analiza zagrożeń cybernetycznymi z użyciem platformy Azure](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operacje zabezpieczeń (SecOP)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Włączanie wykrywania zagrożeń na potrzeby zarządzania tożsamościami i dostępem na platformie Azure

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| LT-2 | 6.8 | AU-3, AU-6, AU-12, SI-4 |

Usługa Azure AD udostępnia następujące dzienniki użytkowników, które mogą być wyświetlane w usłudze Azure AD Reporting lub zintegrowane z Azure Monitorm, wskaźnikiem na platformie Azure lub innymi narzędziami SIEM/monitorowania, aby uzyskać bardziej zaawansowane przypadki monitorowania i analizy: 
-   Logowania — raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.

-   Dzienniki inspekcji — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone w zasobach w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.

-   Ryzykowne logowania — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

-   Użytkownicy oflagowani w związku z ryzykiem — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone.

Azure Security Center może również otrzymywać alerty dotyczące niektórych podejrzanych działań, takich jak nadmierna liczba nieudanych prób uwierzytelnienia i przestarzałe konta w subskrypcji. Oprócz podstawowego monitorowania higieny zabezpieczeń Azure Security Center moduł ochrony przed zagrożeniami może również zbierać bardziej szczegółowe alerty zabezpieczeń z poszczególnych zasobów obliczeniowych platformy Azure (takich jak maszyny wirtualne, kontenery, usługa App Service), zasoby danych (np. SQL DB i magazyn) oraz warstwy usług platformy Azure. Ta funkcja umożliwia wyświetlanie anomalii kont wewnątrz poszczególnych zasobów.

- [Raporty dotyczące inspekcji w usłudze Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Włącz usługę Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](../../security-center/threat-protection.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operacje zabezpieczeń (SecOP)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Włączanie rejestrowania dla aktywności sieci platformy Azure

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| LT-3 | 9,3, 12,2, 12,5, 12,8 | AU-3, AU-6, AU-12, SI-4 |

Umożliwiają włączanie i zbieranie dzienników zasobów sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), dzienników przepływów sieciowej grupy zabezpieczeń, dzienników zapory platformy Azure i dzienników zapory aplikacji sieci Web (WAF) na potrzeby analizy zabezpieczeń, aby obsługiwać badania zdarzeń, łowiectwo zagrożeń oraz generowanie alertów zabezpieczeń. Dzienniki przepływów można wysłać do obszaru roboczego Log Analytics Azure Monitor, a następnie użyć Analiza ruchu w celu uzyskania szczegółowych informacji. Upewnij się, że zbierasz dzienniki zapytań DNS, aby pomóc w skorelowaniu innych danych sieciowych.

- [Jak włączyć dzienniki przepływu sieciowych grup zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Dzienniki i metryki usługi Azure Firewall](../../firewall/logs-and-metrics.md)

- [Jak włączyć i używać Analiza ruchu](../../network-watcher/traffic-analytics.md)

- [Monitorowanie za pomocą Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

- [Rozwiązania do monitorowania sieci platformy Azure w Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md)

- [Zbierz informacje o infrastrukturze DNS przy użyciu rozwiązania DNS Analytics](../../azure-monitor/insights/dns-analytics.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operacje zabezpieczeń (SecOP)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Włączanie rejestrowania dla zasobów platformy Azure

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| LT-4 | 6,2, 6,3, 8,8 | AU-3, AU-12 |

Włącz rejestrowanie zasobów platformy Azure, aby spełnić wymagania dotyczące zgodności, wykrywania zagrożeń i badania oraz łowiectwa zagrożeń. 

Azure Security Center i Azure Policy umożliwiają włączenie dzienników zasobów i danych dzienników zbieranych w zasobach platformy Azure w celu uzyskania dostępu do dzienników inspekcji, zabezpieczeń i zasobów. Dzienniki aktywności, które są automatycznie dostępne, obejmują źródło zdarzeń, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy. 

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../../azure-monitor/platform/platform-logs-overview.md)

- [Omówienie zbierania danych Azure Security Center](../../security-center/security-center-enable-data-collection.md)

- [Włączanie i konfigurowanie monitorowania ochrony przed złośliwym kodem](../fundamentals/antimalware.md#enable-and-configure-antimalware-monitoring-using-powershell-cmdlets)

**Odpowiedzialność**: udostępnione

**Uczestnicy zabezpieczeń klientów**:

- [Operacje zabezpieczeń (SecOP)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Zabezpieczenia infrastruktury i punktu końcowego 

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: scentralizowane zarządzanie dziennikami zabezpieczeń i analiza

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| LT-5 | 6,5, 6,6 | AU-3, SI-4 |

Scentralizowany magazyn rejestrowania i analiza w celu włączenia korelacji. Dla każdego źródła dziennika upewnij się, że masz przypisanego właściciela danych, wskazówki dotyczące dostępu, lokalizację magazynu, narzędzia używane do przetwarzania i uzyskiwania dostępu do danych oraz wymagania dotyczące przechowywania danych.

Upewnij się, że masz integrację dzienników aktywności platformy Azure z centralnym logowaniem. Pozyskiwanie dzienników za pośrednictwem Azure Monitor do agregowania danych zabezpieczeń wygenerowanych przez urządzenia, zasoby sieciowe i inne systemy zabezpieczeń. W Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i przeprowadzania analiz oraz używania kont usługi Azure Storage do przechowywania długoterminowego i archiwizowania.

Ponadto należy włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

Wiele organizacji decyduje się na korzystanie z platformy Azure na potrzeby danych "gorąca", które są często używane, i usługi Azure Storage dla danych "zimnych", które są rzadziej używane. 

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

- [Jak dołączyć wskaźnik na platformie Azure](../../sentinel/quickstart-onboard.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurowanie przechowywania magazynu dzienników

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

Skonfiguruj przechowywanie dzienników zgodnie ze zgodnością, rozporządzeniem i innymi wymaganiami. 

W Azure Monitor można ustawić okres przechowywania Log Analytics obszaru roboczego zgodnie z regulacjami zgodności w organizacji. Użyj usługi Azure Storage Data Lake lub Log Analytics konta obszaru roboczego do przechowywania długoterminowego i archiwizowania.

- [Zmień okres przechowywania danych w Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

Jak skonfigurować zasady przechowywania dzienników kont usługi Azure Storage: ttps://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging

- [Azure Security Center alertów i zaleceń — eksport](../../security-center/continuous-export.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Operacje zabezpieczeń (SecOP)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: korzystanie ze źródeł synchronizacji zatwierdzonego czasu

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Firma Microsoft przechowuje źródła czasu dla większości usług Azure PaaS i SaaS. W przypadku maszyn wirtualnych Użyj domyślnego serwera NTP firmy Microsoft do synchronizacji czasu, chyba że masz określone wymagania.  Jeśli musisz zainstalować własny serwer protokołu NTP (Network Time Protocol), pamiętaj, aby zabezpieczyć Port usługi UDP 123.

Wszystkie dzienniki wygenerowane przez zasoby w ramach platformy Azure zapewniają sygnatury czasowe z określoną wartością domyślną.

- [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych systemu Windows Azure](../../virtual-machines/windows/time-sync.md)

- [Jak skonfigurować synchronizację czasu dla zasobów obliczeniowych systemu Azure Linux](../../virtual-machines/linux/time-sync.md)

- [Jak wyłączyć protokół UDP dla ruchu przychodzącego dla usług platformy Azure](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Odpowiedzialność**: udostępnione

**Uczestnicy zabezpieczeń klientów**:

- [Zasady i standardy](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Zabezpieczenia aplikacji i DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

