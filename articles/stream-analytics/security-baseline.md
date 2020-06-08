---
title: Podstawa zabezpieczeń platformy Azure dla Stream Analytics
description: Podstawa zabezpieczeń platformy Azure dla Stream Analytics
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9d085ba494ea6bb6e9e80490d85e50f100fc0908
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485651"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>Podstawa zabezpieczeń platformy Azure dla Stream Analytics

Podstawą zabezpieczeń platformy Azure dla Stream Analytics są zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

Punkt odniesienia dla tej usługi jest rysowany w [wersji 1,0 usługi Azure Security test](https://docs.microsoft.com/azure/security/benchmarks/overview), która zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure z naszymi najlepszymi wskazówkami.

Aby uzyskać więcej informacji, zobacz [podstawy zabezpieczeń platformy Azure — omówienie](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Bezpieczeństwo sieci

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: zabezpieczenia sieci](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrona zasobów platformy Azure w ramach sieci wirtualnych

**Wskazówki**: Azure Stream Analytics nie obsługuje korzystania z sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) i zapory platformy Azure.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych

**Wskazówki**: Azure Stream Analytics nie obsługuje korzystania z sieci wirtualnych i podsieci.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="13-protect-critical-web-applications"></a>1,3: Ochrona krytycznych aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: odmowa komunikacji ze znanymi złośliwymi adresami IP

**Wskazówki**: Użyj Azure Security Center ochrony przed zagrożeniami, aby wykrywać i otrzymywać alerty dotyczące komunikacji ze znanymi złośliwymi lub nieużywanymi adresami IP.

* [Ochrona przed zagrożeniami dla warstwy usług platformy Azure w Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="15-record-network-packets"></a>1,5: rejestrowanie pakietów sieciowych

**Wskazówki**: Azure Stream Analytics nie korzysta z sieciowych grup zabezpieczeń, a dzienniki przepływów dla Azure Key Vault nie są przechwytywane.

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: wdrażanie opartych na sieci systemów zapobiegania wykrywaniem i dostępem intruzów (identyfikatorów/adresów IP)

**Wskazówki**: Użyj Azure Security Center ochrony przed zagrożeniami, aby wykrywać nietypowe lub potencjalnie szkodliwe operacje w środowisku subskrypcji platformy Azure.

* [Ochrona przed zagrożeniami dla warstwy usług platformy Azure w Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="17-manage-traffic-to-web-applications"></a>1,7: zarządzanie ruchem do aplikacji sieci Web

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizacja złożoności i kosztów administracyjnych reguł zabezpieczeń sieci

**Wskazówki**: Azure Stream Analytics nie obsługuje korzystania z sieci wirtualnych i reguł sieci.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Obsługa standardowych konfiguracji zabezpieczeń dla urządzeń sieciowych

**Wskazówki**: Azure Stream Analytics nie obsługuje korzystania z sieci wirtualnych i urządzeń sieciowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="110-document-traffic-configuration-rules"></a>1,10: udokumentowanie reguł konfiguracji ruchu

**Wskazówki**: Azure Stream Analytics nie obsługuje korzystania z sieci wirtualnych i reguł konfiguracji ruchu.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Użyj zautomatyzowanych narzędzi do monitorowania konfiguracji zasobów sieciowych i wykrywania zmian

**Wskazówki**: Użyj dziennika aktywności platformy Azure do monitorowania konfiguracji zasobów i wykrywania zmian dotyczących zasobów Stream Analytics. Utwórz alerty w Azure Monitor, które będą wyzwalane po wprowadzeniu zmian w zasobach krytycznych.

* [Jak wyświetlać i pobierać zdarzenia dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Jak utworzyć alerty w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Użyj źródeł synchronizacji zatwierdzonego czasu

**Wskazówki**: Firma Microsoft utrzymuje źródło czasu używane dla zasobów platformy Azure, takie jak Stream Analytics.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Skonfiguruj centralne zarządzanie dziennikami zabezpieczeń

**Wskazówki**: pobieranie dzienników za pośrednictwem Azure monitor do agregowania danych zabezpieczeń, takich jak zdarzenia inspekcji i żądania. W ramach Azure Monitor używać Log Analytics obszarów roboczych do wykonywania zapytań i wykonywania analiz oraz używania magazynu Azure Storage accountyfor Long-Term/archiwalnego, opcjonalnie z funkcjami zabezpieczeń, takimi jak niezmienny magazyn i wymuszone przechowywanie blokad.

* [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Włącz ustawienia diagnostyczne na Azure Stream Analytics, aby uzyskać dostęp do dzienników administracyjnych, zabezpieczeń i diagnostycznych. Możesz również włączyć ustawienia diagnostyczne dziennika aktywności platformy Azure i wysłać dzienniki do tego samego obszaru roboczego Log Analytics lub konta magazynu.

* [Azure Stream Analytics dostarcza dzienników diagnostycznych i danych działań do przeglądu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: Zbierz dzienniki zabezpieczeń z systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurowanie przechowywania magazynu dzienników zabezpieczeń

**Wskazówki**: podczas przechowywania dzienników zdarzeń zabezpieczeń na koncie usługi Azure Storage lub w obszarze roboczym log Analytics można ustawić zasady przechowywania zgodnie z wymaganiami organizacji.

* [Azure Stream Analytics dostarcza dzienników diagnostycznych i danych działań do przeglądu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

* [Jak skonfigurować zasady przechowywania dla dzienników kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

* [Zmień okres przechowywania danych w Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="26-monitor-and-review-logs"></a>2,6: dzienniki monitorowania i przeglądania

**Wskazówki**: analizowanie i monitorowanie dzienników pod kątem nietypowego zachowania oraz regularne przeglądanie wyników dotyczących zasobów Stream Analytics. Użyj Azure Monitor Log Analytics obszaru roboczego do przeglądania dzienników i wykonywania zapytań dotyczących danych dziennika. Alternatywnie możesz włączyć i dołączyć dane do usługi Azure wskaźnikowej lub SIEM innych firm.

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Aby uzyskać więcej informacji na temat obszaru roboczego Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Jak wykonywać niestandardowe zapytania w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: Włączanie alertów dla nietypowych działań

**Wskazówki**: Włączanie ustawień diagnostycznych dla Stream Analytics i wysyłanie dzienników do log Analytics obszaru roboczego. Dołącz obszar roboczy Log Analytics do usługi Azure o, ponieważ zapewnia ona rozwiązanie do automatycznej reakcji aranżacji zabezpieczeń (). Pozwala to na tworzenie i używanie automatycznych rozwiązań elementy PlayBook w celu korygowania problemów z zabezpieczeniami.

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Jak ostrzec dane dziennika usługi log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

* [Azure Stream Analytics dostarcza dzienników diagnostycznych i danych działań do przeglądu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="28-centralize-anti-malware-logging"></a>2,8: scentralizowanie rejestrowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; Stream Analytics nie przetwarza ani nie tworzy dzienników związanych z oprogramowaniem chroniącym przed złośliwym kodem.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="29-enable-dns-query-logging"></a>2,9: Włączanie rejestrowania zapytań DNS

**Wskazówki**: rozwiązanie Azure DNS Analytics (wersja zapoznawcza) w Azure monitor zbiera szczegółowe informacje o INFRASTRUKTURze DNS w zakresie zabezpieczeń, wydajności i operacji. Obecnie nie jest to obsługiwane Azure Stream Analytics jednak można użyć rozwiązania do rejestrowania w systemie DNS innej firmy.

* [Zbierz informacje o infrastrukturze DNS przy użyciu rozwiązania DNS Analytics w wersji zapoznawczej](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="210-enable-command-line-audit-logging"></a>2,10: Włączanie rejestrowania inspekcji w wierszu polecenia

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: obsługa spisu kont administracyjnych

**Wskazówki**: usługa Azure AD ma wbudowane role, które muszą być jawnie przypisane. Role mogą być zapytania w celu odnalezienia członkostwa. Za pomocą modułu Azure AD PowerShell można wykonywać zapytania ad hoc w celu odnajdywania kont należących do grup administracyjnych.

* [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Zmień domyślne hasła, jeśli ma to zastosowanie

**Wskazówki**: Stream Analytics nie ma koncepcji domyślnych haseł, ponieważ uwierzytelnianie jest dostarczane z Azure Active Directory i zabezpieczone przez kontrolę dostępu opartą na ROLACH (RBAC) do zarządzania usługą. W zależności od usług strumienia iniekcji i usług wyjściowych należy obrócić poświadczenia skonfigurowane w zadaniach.

* [Obróć poświadczenia logowania dla danych wejściowych i wyjściowych zadania Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-login-credentials-inputs-outputs)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

Wskazówki: Tworzenie planu zabezpieczeń **dotyczącego**zarządzania tożsamościami i ról, zgodnie z najlepszymi rozwiązaniami, w tym z zasadą najmniejszego uprzywilejowanego dostępu dla ról administratorów. Użyj usługi Azure Privileged Identity Management (PIM), aby zapewnić uprzywilejowany dostęp do usługi Azure AD i zasobów platformy Azure w odpowiednim czasie. Użyj alertów usługi Azure PIM i historii inspekcji do monitorowania aktywności kont administracyjnych. Użyj raportów zabezpieczeń usługi Azure AD, aby ułatwić identyfikację kont administracyjnych, które mogły zostać naruszone.

* [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Korzystaj z logowania jednokrotnego (SSO) z usługą Azure Active Directory

**Wskazówki**: wszędzie tam, gdzie to możliwe, użyj Azure Active Directory rejestracji jednokrotnej zamiast konfigurować poświadczenia autonomiczne dla poszczególnych usług. Zaimplementuj &amp; zalecenia dotyczące dostępu do tożsamości Azure Security Center.

* [Opis logowania jednokrotnego w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Użyj uwierzytelniania wieloskładnikowego, aby uzyskać dostęp oparty na Azure Active Directory

**Wskazówki**: Włącz Azure Active Directory uwierzytelnianie wieloskładnikowe (MFA) i postępuj zgodnie z zaleceniami Azure Security Center dotyczące zarządzania tożsamościami i dostępem, aby pomóc w ochronie zasobów Stream Analytics.

* [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Jak monitorować tożsamość i dostęp w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) z uwierzytelnianiem wieloskładnikowym (MFA) skonfigurowanym do logowania się i konfigurowania zasobów Stream Analytics.

* [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak włączyć usługę MFA na platformie Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Rejestruj i Ostrzegaj o podejrzanych działaniach z kont administracyjnych

**Wskazówki**: używanie raportów zabezpieczeń Azure Active Directory do generowania dzienników i alertów w przypadku wystąpienia podejrzanych lub niebezpiecznych działań w środowisku. Użyj Azure Security Center, aby monitorować działania związane z tożsamościami i dostępem.

* [Identyfikowanie użytkowników usługi Azure AD oflagowanych w celu działania ryzykownego](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Jak monitorować działania związane z tożsamościami i dostępem użytkowników w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: zarządzanie zasobami platformy Azure tylko z zatwierdzonych lokalizacji

**Wskazówki**: Użyj dostępu warunkowego o nazwie Locations, aby zezwolić na dostęp tylko do określonych logicznych grup zakresów adresów IP lub krajów/regionów.

* [Jak skonfigurować nazwane lokalizacje na platformie Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="39-use-azure-active-directory"></a>3,9: Użyj Azure Active Directory

**Wskazówki**: Użyj Azure Active Directory (Azure AD) jako centralnego systemu uwierzytelniania i autoryzacji. Usługa Azure AD zapewnia kontrolę dostępu opartą na rolach (RBAC) na potrzeby precyzyjnej kontroli dostępu klienta do zasobów Stream Analytics.

* [Jak utworzyć i skonfigurować wystąpienie usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: Zapoznaj się z dziennikami Azure Active Directory, aby ułatwić odnalezienie starych kont, które mogą obejmować role administracyjne konta magazynu. Ponadto za pomocą przeglądów dostępu do tożsamości platformy Azure można efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkowników powinien być regularnie przeglądany, aby upewnić się, że tylko Ci użytkownicy mają ciągły dostęp.

* [Informacje o raportowaniu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: Monitor próbuje uzyskać dostęp do zdezaktywowanych poświadczeń

**Wskazówki**: Włączanie ustawień diagnostycznych dla Azure Stream Analytics i Azure Active Directory, wysyłanie wszystkich dzienników do obszaru roboczego log Analytics. Skonfiguruj żądane alerty (takie jak próby uzyskania dostępu do wyłączonych wpisów tajnych) w ramach Log Analytics.

* [Integrowanie dzienników usługi Azure AD z dziennikami Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: odchylenia zachowania podczas logowania do konta

**Wskazówki**: Użyj funkcji ochrony przed ryzykiem i tożsamością Azure Active Directory, aby skonfigurować automatyczne odpowiedzi na wykryte podejrzane działania związane z zasobami Stream Analytics. Aby zaimplementować odpowiedzi na zabezpieczenia organizacji, należy włączyć automatyczne odpowiedzi za pomocą wskaźnikowego platformy Azure.

* [Jak wyświetlić ryzykowne logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Jak skonfigurować i włączyć zasady dotyczące ryzyka związanego z ochroną tożsamości](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Jak dołączyć wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Zapewnij firmie Microsoft dostęp do odpowiednich danych klienta w scenariuszach pomocy technicznej

**Wskazówki**: nie dotyczy; Azure Stream Analytics nie Skrytka klienta obsługiwane.

* [Obsługiwane usługi i scenariusze ogólnie dostępne](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

**Wskazówki**: Użyj tagów, aby pomóc w śledzeniu zasobów Stream Analytics, które przechowują lub przetwarzają informacje poufne.

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

**Wskazówki**: Izoluj Stream Analytics zadania, umieszczając dane wejściowe, wyjściowe i konta magazynu w tej samej subskrypcji. Możesz ograniczyć Stream Analytics, aby kontrolować poziom dostępu do zasobów Stream Analytics, których wymagają Twoje aplikacje i środowiska korporacyjne. Dostęp do Azure Stream Analytics można kontrolować za pośrednictwem usługi Azure AD RBAC.

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Informacje o danych wejściowych dla Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-add-inputs)

* [Poznanie danych wyjściowych z Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

**Wskazówki**: funkcje ochrony przed utratą danych nie są jeszcze dostępne dla Azure Stream Analytics zasobów. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i chroni przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

* [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

* [Zabezpieczanie kont usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

**Wskazówki**: Azure Stream Analytics szyfruje całą komunikację przychodzącą i wychodzącą i obsługuje protokół TLS 1,2. Wbudowane punkty kontrolne również są szyfrowane.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

**Wskazówki**: funkcje identyfikacji danych nie są jeszcze dostępne dla zasobów Azure Stream Analytics. Zaimplementuj rozwiązanie innych firm, jeśli jest wymagane na potrzeby zgodności.

* [Zrozumienie ochrony danych klientów na platformie Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: stosowanie kontroli dostępu opartej na rolach w celu kontrolowania dostępu do zasobów

**Wskazówki**: Używanie kontroli dostępu opartej na ROLACH (RBAC) do kontrolowania sposobu, w jaki użytkownicy pracują z usługą.

* [Jak skonfigurować RBAC na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

**Wskazówki**: Stream Analytics nie przechowuje danych przychodzących, ponieważ wszystkie procesy przetwarzania są wykonywane w pamięci. Wszystkie dane prywatne, w tym zapytania i funkcje, które są wymagane do utrwalenia przez Stream Analytics są przechowywane na skonfigurowanym koncie magazynu. Klucze zarządzane przez klienta (CMK) umożliwiają szyfrowanie danych wyjściowych przechowywanych na kontach magazynu. Nawet bez CMK, Stream Analytics automatycznie korzysta z najlepszych w swojej klasie standardów szyfrowania w celu zaszyfrowania i zabezpieczenia danych.

* [Ochrona danych w Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dla sytuacji, w których zmiany są wprowadzane do wystąpień produkcyjnych Azure Stream Analytics zasobów.

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Zarządzanie lukami w zabezpieczeniach](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: uruchamianie narzędzi do skanowania automatycznych luk w zabezpieczeniach

**Wskazówki**: Postępuj zgodnie z zaleceniami Azure Security Center na Zabezpieczanie zasobów Azure Stream Analytics.

Firma Microsoft przeprowadza zarządzanie lukami w systemach podstawowych, które obsługują Azure Stream Analytics.

* [Informacje na temat Azure Security Center zaleceń](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Wdróż automatyczne rozwiązanie do zarządzania poprawkami systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Wdróż rozwiązanie zautomatyzowanego zarządzania poprawkami dla tytułów oprogramowania innych firm

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porównanie luk w zabezpieczeniach z tyłu do tyłu

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Użyj procesu oceny ryzyka, aby określić priorytety korygowania odkrytych luk w zabezpieczeniach

**Wskazówki**: Użyj domyślnych ocen ryzyka ("Secure Score") dostarczonych przez Azure Security Center.

* [Informacje na temat Azure Security Center zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania wszystkich zasobów (takich jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji. Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

* [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Opis kontroli RBAC platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

**Wskazówki**: używanie tagowania, grup zarządzania i oddzielnych subskrypcji, gdzie to konieczne, do organizowania i śledzenia zasobów Azure Stream Analytics. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Ponadto należy używać Azure Policy do umieszczania ograniczeń dotyczących typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Jak utworzyć Grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Tworzenie i używanie tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

Ponadto za pomocą usługi Azure Resource Graph można wykonywać zapytania/odnajdywać zasoby w ramach subskrypcji.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak tworzyć zapytania za pomocą usługi Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych i platformy Azure jako całości.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w subskrypcjach klientów, przy użyciu następujących wbudowanych definicji zasad:
- Niedozwolone typy zasobów
- Dozwolone typy zasobów

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak odmówić określonego typu zasobu za pomocą Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: przechowywanie spisu zatwierdzonych tytułów oprogramowania

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager

**Wskazówki**: Skonfiguruj dostęp warunkowy platformy Azure, aby ograniczyć możliwość korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

* [Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone dla aplikacji sieci Web działających na Azure App Service lub zasobach obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="secure-configuration"></a>Bezpieczna konfiguracja

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: bezpieczna konfiguracja](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. StreamAnalytics", aby utworzyć niestandardowe zasady inspekcji lub wymuszania konfiguracji Azure Stream Analytics. Mogą również korzystać z wbudowanych definicji zasad związanych z Azure Stream Analytics, takich jak dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone.

* [Jak wyświetlić dostępne aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy wbudowane definicje zasad](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies)

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

**Wskazówki**: Użyj Azure Policy [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Zrozumienie efektów Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

**Wskazówki**: nie dotyczy; te wytyczne są przeznaczone dla zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

**Wskazówki**: Użyj Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim, w tym niestandardowe zasady platformy Azure, szablony Azure Resource Manager, skrypty konfiguracji żądanego stanu, funkcje zdefiniowane przez użytkownika i zapytania. Aby uzyskać dostęp do zasobów zarządzanych w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w Azure Active Directory (Azure AD) w przypadku integracji z usługą Azure DevOps lub Active Directory w przypadku integracji z programem TFS.

* [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informacje o uprawnieniach i grupach na platformie Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. StreamAnalytics", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla zasobów platformy Azure

**Wskazówki**: Użyj aliasów Azure Policy w przestrzeni nazw "Microsoft. StreamAnalytics", aby utworzyć zasady niestandardowe na potrzeby alertów, inspekcji i wymuszania konfiguracji systemu. Użyj Azure Policy [Audit], [Odmów] i [Wdróż, jeśli nie istnieje], aby automatycznie wymuszać konfiguracje dla zasobów Azure Stream Analytics.

* [Jak skonfigurować Azure Policy i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure

**Wskazówki**: Szczegóły połączenia dla zasobów wejściowych lub wyjściowych, które są używane przez zadanie Stream Analytics, są przechowywane na skonfigurowanym koncie magazynu. Zaszyfruj konto magazynu, aby zabezpieczyć wszystkie Twoje dane. Należy również regularnie obrócić poświadczenia dla danych wejściowych lub wyjściowych zadania Stream Analytics.

* [Ochrona danych w Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection)

* [Obróć poświadczenia logowania dla danych wejściowych i wyjściowych zadania Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-login-credentials-inputs-outputs)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

**Wskazówki**: uwierzytelnianie tożsamości zarządzanej dla danych wyjściowych daje Stream Analytics zadania bezpośredniego dostępu do usługi, w tym Power BI, konto magazynu, zamiast używać parametrów połączenia.

* [Uwierzytelnianie Stream Analytics do Azure Data Lake Storage Gen1 przy użyciu tożsamości zarządzanych](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls)

* [Uwierzytelnianie zadania Azure Stream Analytics za pomocą tożsamości zarządzanej na platformie Azure Blob Storage dane wyjściowe](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)

* [Za pomocą tożsamości zarządzanej można uwierzytelniać zadanie Azure Stream Analytics w Power BI](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

**Wskazówki**: Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault.

* [Jak skonfigurować skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="malware-defense"></a>Ochrona przed złośliwym oprogramowaniem

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Obrona złośliwego oprogramowania](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Użyj centralnie zarządzanego oprogramowania chroniącego przed złośliwym oprogramowaniem

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: przeskanuj pliki przed przekazaniem do zasobów platformy Azure, które nie są obliczeniowe

**Wskazówki**: oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft jest włączone na podstawowym hoście obsługującym usługi platformy Azure (na przykład Azure Stream Analytics), ale nie jest ono uruchamiane w treści klienta.

Wstępnie Skanuj zawartość przekazywaną do zasobów platformy Azure, takich jak App Service, Stream Analytics, Blob Storage itd. Firma Microsoft nie może uzyskać dostępu do danych w tych wystąpieniach.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Upewnij się, że oprogramowanie chroniące przed złośliwym oprogramowaniem i podpisy zostały zaktualizowane

**Wskazówki**: nie dotyczy; to zalecenie jest przeznaczone do zasobów obliczeniowych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="data-recovery"></a>Odzyskiwanie danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odzyskiwanie danych](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Zapewnij regularne zautomatyzowane przywracanie awaryjne

**Wskazówki**: na podstawie typu wybranej usługi wyjściowej. można wykonywać automatyczne tworzenie kopii zapasowych danych wyjściowych zgodnie z zalecanymi wskazówkami dotyczącymi usługi wyjściowej. Dane wewnętrzne, w tym funkcje zdefiniowane przez użytkownika, zapytania i migawki danych są przechowywane na skonfigurowanym koncie magazynu, które można regularnie tworzyć kopie zapasowe.

Dane na koncie magazynu Microsoft Azure są zawsze automatycznie replikowane w celu zapewnienia trwałości i wysokiej dostępności. Usługa Azure Storage kopiuje dane tak, aby były chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi katastrofami naturalnymi. Dane można replikować w tym samym centrum danych, w różnych centrach danych w tym samym regionie lub w regionach geograficznie rozdzielonych.

Za pomocą funkcji zarządzania cyklem życia można także tworzyć kopie zapasowe danych w warstwie archiwum. Ponadto Włącz usuwanie nietrwałe dla kopii zapasowych przechowywanych na koncie magazynu.

* [Ochrona danych w Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Informacje o nadmiarowości usługi Azure Storage i umowach dotyczących poziomu usług](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

* [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)

Usuwanie nietrwałe dla obiektów BLOB usługi Azure Storage:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta

**Wskazówki**: dane wewnętrzne, w tym funkcje zdefiniowane przez użytkownika, zapytania i migawki danych są przechowywane na skonfigurowanym koncie magazynu, które można regularnie tworzyć kopie zapasowe.

Aby można było tworzyć kopie zapasowe danych z obsługiwanych usług konta magazynu, istnieje wiele dostępnych metod obejmujących używanie AzCopy lub narzędzi innych firm. Niezmienny magazyn usługi Azure Blob Storage umożliwia użytkownikom przechowywanie obiektów danych o kluczowym znaczeniu dla firmy w ROBAKu (zapis jeden raz, odczyt wielu). Ten stan sprawia, że dane nie są wymazywane i nie są modyfikowane dla interwału określonego przez użytkownika.

* [Ochrona danych w Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Wprowadzenie do narzędzia AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

* [Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

Klucze zarządzane/dostarczone przez klienta można tworzyć w ramach Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

* [Jak utworzyć kopię zapasową kluczy magazynu kluczy na platformie Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

**Wskazówki**: okresowe wykonywanie przywracania danych kopii zapasowej w celu przetestowania integralności danych.

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zapewnianie ochrony kopii zapasowych i kluczy zarządzanych przez klienta

**Wskazówki**: Stream Analytics kopie zapasowe przechowywane w usłudze Azure Storage domyślnie obsługują szyfrowanie i nie mogą być wyłączone. Kopie zapasowe należy traktować jako dane poufne i zastosować odpowiednie środki kontroli dostępu i ochrony danych w ramach tej linii bazowej.

* [Ochrona danych w Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [Autoryzowanie dostępu do danych w usłudze Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Monitorowanie Azure Security Center**: obecnie niedostępna

**Odpowiedzialność**: klient

## <a name="incident-response"></a>Reagowanie na zdarzenia

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: odpowiedź na zdarzenia](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

**Wskazówki**: Tworzenie przewodnika odpowiedzi na zdarzenia dla organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania z wykrywania do oceny po zdarzeniu.

* [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Klient może również korzystać z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

**Wskazówki**: Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne. Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

* [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Organizowanie zasobów platformy Azure przy użyciu tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

**Wskazówki**: przeprowadzanie ćwiczeń w celu przetestowania możliwości reagowania na zdarzenia systemów w regularnych erzeach w celu ochrony zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy i popraw plan zgodnie z wymaganiami.

* [Zapoznaj się z publikacją NIST: Przewodnik dotyczący testowania, uczenia i ćwiczeń programów dla planów i możliwości IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

**Wskazówki**: informacje kontaktowe dotyczące zdarzenia zabezpieczeń będą używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

* [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorowanie Azure Security Center**: tak

**Odpowiedzialność**: klient

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

**Wskazówki**: eksportowanie alertów i zaleceń dotyczących Azure Security Center przy użyciu funkcji eksportu ciągłego, która pomaga identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

* [Jak skonfigurować eksport ciągły](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

**Wskazówki**: Użyj funkcji automatyzacji przepływu pracy w Azure Security Center, aby automatycznie wyzwalać odpowiedzi poprzez "Logic Apps" dotyczące alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

* [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: klient

## <a name="penetration-tests-and-red-team-exercises"></a>Testy penetracyjne i ćwiczenia typu „red team”

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: testy penetracji i czerwone ćwiczenia zespołu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Przeprowadź regularne testowanie penetracji zasobów platformy Azure i zadbaj o skorygowanie wszystkich krytycznych ustaleń dotyczących zabezpieczeń

**Wskazówki**: 

* [Postępuj zgodnie z zasadami firmy Microsoft dotyczącymi zaangażowania, aby upewnić się, że testy penetracji nie naruszają zasad firmy Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [W tym miejscu znajdziesz więcej informacji na temat strategii i wykonywania trójwymiarowych operacji tworzenia zespołu i testowania aplikacji na żywo w witrynie Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorowanie Azure Security Center**: nie dotyczy

**Odpowiedzialność**: udostępnione

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
