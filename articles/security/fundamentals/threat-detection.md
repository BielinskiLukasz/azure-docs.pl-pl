---
title: Wykrywanie zaawansowanych zagrożeń na platformie Azure | Microsoft Docs
description: Dowiedz się więcej na temat Azure AD Identity Protection i jego możliwości.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 500ae75f9bf53c1633290e706e33165dd9d67a88
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726609"
---
# <a name="azure-advanced-threat-detection"></a>Zaawansowane wykrywanie zagrożeń na platformie Azure

Usługa Azure oferuje wbudowaną funkcję wykrywania zagrożeń za pomocą usług, takich jak Azure Active Directory (Azure AD), dzienniki Azure Monitor i Azure Security Center. Ta kolekcja usług i funkcji zabezpieczeń zapewnia prosty i szybki sposób zrozumienia, co dzieje się w ramach wdrożeń platformy Azure.

System Azure oferuje szeroką gamę opcji konfigurowania i dostosowywania zabezpieczeń w celu spełnienia wymagań wdrożeń aplikacji. W tym artykule omówiono sposób spełniania tych wymagań.

## <a name="azure-active-directory-identity-protection"></a>Ochrona tożsamości w usłudze Azure Active Directory

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) to funkcja [Azure Active Directory — wersja Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) Edition, która oferuje przegląd zdarzeń ryzyka i potencjalnych luk w zabezpieczeniach, które mogą wpływać na tożsamość organizacji. Funkcja ochrony tożsamości korzysta z istniejących funkcji wykrywania anomalii usługi Azure AD, które są dostępne za pośrednictwem [raportów anomalii dotyczących aktywności usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports), i wprowadza nowe typy zdarzeń ryzyka, które mogą wykrywać anomalie w czasie rzeczywistym.

![Diagram Azure AD Identity Protection](./media/threat-detection/azure-threat-detection-fig1.png)

Ochrona tożsamości używa adaptacyjnych algorytmów uczenia maszynowego i heurystycznych, aby wykrywać anomalie i zdarzenia ryzyka, które mogą wskazywać na naruszenie bezpieczeństwa tożsamości. Przy użyciu tych danych Ochrona tożsamości generuje raporty i alerty, aby można było zbadać te zdarzenia ryzyka i podjąć odpowiednie działania naprawcze lub zaradcze.

Azure Active Directory Identity Protection jest więcej niż narzędzie do monitorowania i raportowania. Na podstawie zdarzeń o podwyższonym ryzyku Ochrona tożsamości oblicza poziom ryzyka użytkownika dla każdego użytkownika, dzięki czemu można skonfigurować zasady oparte na ryzyku, aby automatycznie chronić tożsamości organizacji.

Te zasady oparte na ryzyku, oprócz innych [kontroli dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) , które są udostępniane przez Azure Active Directory i [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), mogą automatycznie blokować lub oferować akcje adaptacyjnego korygowania, które obejmują resetowanie haseł i wieloskładnikowe Wymuszanie uwierzytelniania.

### <a name="identity-protection-capabilities"></a>Możliwości ochrony tożsamości

Azure Active Directory Identity Protection jest więcej niż narzędzie do monitorowania i raportowania. Aby chronić tożsamości organizacji, można skonfigurować zasady oparte na ryzyku, które automatycznie reagują na wykryte problemy po osiągnięciu określonego poziomu ryzyka. Te zasady, oprócz innych kontroli dostępu warunkowego zapewniane przez Azure Active Directory i EMS, mogą automatycznie blokować lub inicjować adaptacyjne akcje zaradcze, w tym resetowanie haseł i Wymuszanie uwierzytelniania wieloskładnikowego.

Przykłady niektórych sposobów, w których usługa Azure Identity Protection, może pomóc w zabezpieczaniu Twoich kont i tożsamości:

[Wykrywanie zdarzeń ryzyka i ryzykownych kont](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Wykrywaj sześć typów zdarzeń ryzyka przy użyciu uczenia maszynowego i reguł heurystycznych.
-   Oblicz poziomy ryzyka użytkownika.
-   Zapewnianie niestandardowych zaleceń w celu poprawy ogólnej stan zabezpieczeń poprzez wyróżnienie luk w zabezpieczeniach.

[Badanie zdarzeń ryzyka](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Wysyłaj powiadomienia dla zdarzeń o podwyższonym ryzyku.
-   Zbadaj zdarzenia dotyczące ryzyka, korzystając z odpowiednich i kontekstowych informacji.
-   Zapewniają podstawowe przepływy pracy do śledzenia dochodzeń.
-   Zapewnianie łatwego dostępu do akcji korygowania, takich jak resetowanie hasła.

[Zasady dostępu warunkowego oparte na ryzyku](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
-   Ogranicz ryzykowne logowania, blokując logowania lub wymagając wyzwań związanych z uwierzytelnianiem wieloskładnikowym.
-   Blokuj lub Zabezpiecz ryzykowne konta użytkowników.
-   Wymaganie, aby użytkownicy rejestrowali się w celu korzystania z uwierzytelniania wieloskładnikowego.

### <a name="azure-ad-privileged-identity-management"></a>Usługa Azure AD Privileged Identity Management

Za pomocą [Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)można zarządzać dostępem, kontrolować i monitorować je w organizacji. Ta funkcja obejmuje dostęp do zasobów w usłudze Azure AD i innych Usługi online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

![Diagram Azure AD Privileged Identity Management](./media/threat-detection/azure-threat-detection-fig2.png)

Program PIM ułatwia:

-   Otrzymuj alerty i raporty dotyczące administratorów usługi Azure AD oraz dostępu administracyjnego just-in-Time (JIT) do programu Microsoft Usługi online, takich jak Office 365 i Intune.

-   Otrzymuj raporty o historii dostępu administratora i zmianach w przypisaniach administratorów.

-   Otrzymuj alerty dotyczące dostępu do roli uprzywilejowanej.

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki Azure monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) to oparte na chmurze rozwiązanie do zarządzania działem IT firmy Microsoft, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę. Ponieważ dzienniki Azure Monitor są implementowane jako usługa oparta na chmurze, możesz szybko ją uruchomić przy minimalnych inwestycjach w usługi infrastruktury. Nowe funkcje zabezpieczeń są dostarczane automatycznie, dzięki czemu można zaoszczędzić bieżące koszty konserwacji i uaktualniania.

Oprócz zapewniania cennych usług samodzielnie dzienniki Azure Monitor można zintegrować ze składnikami programu System Center, takimi jak [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), aby zwiększyć istniejące inwestycje związane z zarządzaniem zabezpieczeniami na chmurę. Dzienniki programu System Center i Azure Monitor mogą współdziałać ze sobą, aby zapewnić pełne środowisko zarządzania hybrydowego.

### <a name="holistic-security-and-compliance-posture"></a>Kompleksowe zabezpieczenia i zgodność stan

[Pulpit nawigacyjny Security and Audit log Analytics](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) zapewnia kompleksowy wgląd w stan zabezpieczeń IT Twojej organizacji, dzięki wbudowanym zapytaniom wyszukiwania dla istotnych problemów, które wymagają uwagi. Pulpit nawigacyjny Security and Audit jest ekranem głównym dla wszystkich elementów związanych z zabezpieczeniami w dziennikach Azure Monitor. Zawiera ogólne informacje o stanie zabezpieczeń komputerów. Możesz również wyświetlić wszystkie zdarzenia z ostatnich 24 godzin, 7 dni lub dowolnego innego niestandardowego przedziału czasu.

Dzienniki Azure Monitor ułatwiają szybkie i łatwe zrozumienie ogólnych stan zabezpieczeń w każdym środowisku, a wszystko to w kontekście operacji IT, w tym oceny aktualizacji oprogramowania, oceny ochrony przed złośliwym kodem i linii bazowych konfiguracji. Dane dziennika zabezpieczeń są łatwo dostępne, aby usprawnić procesy inspekcji zabezpieczeń i zgodności.

![Pulpit nawigacyjny Security and Audit Log Analytics](./media/threat-detection/azure-threat-detection-fig3.jpg)

Pulpit nawigacyjny Security and Audit Log Analytics jest podzielony na cztery główne kategorie:

-   **Domeny zabezpieczeń**: Umożliwia dalsze Eksplorowanie rekordów zabezpieczeń w czasie; dostęp do oceny złośliwego oprogramowania; oceny aktualizacji; Wyświetl informacje o zabezpieczeniach, tożsamości i dostępie sieci; Wyświetlanie komputerów ze zdarzeniami zabezpieczeń; i szybko Uzyskuj dostęp do pulpitu nawigacyjnego Azure Security Center.

-   Istotne **problemy**: Umożliwia szybką identyfikację liczby aktywnych problemów i ważności problemów.

-   **Wykrycia (wersja zapoznawcza)** : Umożliwia zidentyfikowanie wzorców ataków przez wyświetlanie alertów zabezpieczeń w miarę ich występowania z zasobami.

-   **Analiza zagrożeń**: Umożliwia zidentyfikowanie wzorców ataków, wyświetlając łączną liczbę serwerów z wychodzącym złośliwym ruchem IP, typ złośliwego zagrożenia oraz mapę lokalizacji adresów IP.

-   **Typowe zapytania dotyczące zabezpieczeń**: Wyświetla listę najbardziej typowych zapytań zabezpieczeń, których można użyć do monitorowania środowiska. Po wybraniu dowolnego zapytania zostanie otwarte okienko wyszukiwania i zostaną wyświetlone wyniki dla tego zapytania.

### <a name="insight-and-analytics"></a>Wgląd i analiza
W centrum [dzienników Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) jest repozytorium, które jest hostowane przez platformę Azure.

![Diagram usługi Insight and Analytics](./media/threat-detection/azure-threat-detection-fig4.png)

Dane można zbierać do repozytorium z połączonych źródeł przez skonfigurowanie źródeł danych i dodanie rozwiązań do subskrypcji.

![Pulpit nawigacyjny dzienników Azure Monitor](./media/threat-detection/azure-threat-detection-fig5.png)

Wszystkie źródła danych i rozwiązania tworzą oddzielne typy rekordów z własnymi zestawami właściwości, ale można je analizować razem w zapytaniach do repozytorium. Możesz użyć tych samych narzędzi i metod do pracy z różnymi danymi, które są zbierane przez różne źródła.


Większość interakcji z dziennikami Azure Monitor polega na Azure Portal, które działają w dowolnej przeglądarce i zapewniają dostęp do ustawień konfiguracji oraz wielu narzędzi do analizowania zebranych danych i wykonywania na nich działań. W portalu możesz użyć:
* [Przeszukiwanie dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) , w których są konstruowane zapytania w celu analizowania zebranych danych.
* [Pulpity nawigacyjne](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards), które można dostosować za pomocą widoków graficznych najbardziej cennych wyszukiwań.
* [Rozwiązania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), które oferują dodatkowe funkcje i narzędzia do analizy.

![Narzędzia do analizy](./media/threat-detection/azure-threat-detection-fig6.png)

Rozwiązania umożliwiają dodawanie funkcji do dzienników Azure Monitor. Są one wykonywane głównie w chmurze i zapewniają analizę danych zebranych w repozytorium usługi log Analytics. Rozwiązania mogą również definiować nowe typy rekordów do zebrania, które można analizować za pomocą wyszukiwania w dziennikach lub przy użyciu dodatkowego interfejsu użytkownika, który oferuje rozwiązanie na pulpicie nawigacyjnym usługi log Analytics.

Pulpit nawigacyjny Security and Audit jest przykładem tego typu rozwiązań.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatyzacja i kontrola: Alert dotyczący dryfów konfiguracji zabezpieczeń

Azure Automation automatyzuje procesy administracyjne z elementami Runbook opartymi na programie PowerShell i uruchamianymi w chmurze. Elementy Runbook mogą być również wykonywane na serwerze w lokalnym centrum danych w celu zarządzania zasobami lokalnymi. Azure Automation zapewnia zarządzanie konfiguracją przy użyciu konfiguracji żądanego stanu programu PowerShell (DSC).

![Diagram Azure Automation](./media/threat-detection/azure-threat-detection-fig7.png)

Można tworzyć zasoby DSC hostowane na platformie Azure i zarządzać nimi, a także stosować je do systemów w chmurze i lokalnych. Dzięki temu można zdefiniować i automatycznie wymusić swoją konfigurację lub uzyskać raporty dotyczące dryfowania, aby zapewnić, że konfiguracje zabezpieczeń pozostają w ramach zasad.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomaga chronić zasoby platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. W ramach usługi można zdefiniować zasady dla subskrypcji i [grup zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) platformy Azure w celu uzyskania większego stopnia szczegółowości.

![Diagram Azure Security Center](./media/threat-detection/azure-threat-detection-fig8.png)

Pracownicy naukowo-badawczy firmy Microsoft stale poszukują nowych zagrożeń. Mają oni dostęp do szerokiej gamy rozwiązań telemetrycznych uzyskanych dzięki obecności firmy Microsoft na całym świecie zarówno w chmurze, jak i lokalnie. Bogata i różnorodna kolekcja zestawów danych pozwala firmie Microsoft odnajdywać nowe trendy i wzorce ataków, które pojawiają się w jej lokalnych produktach dla firm i osób prywatnych, a także usługach online.

W ten sposób Security Center może szybko zaktualizować swoje algorytmy wykrywania, ponieważ atakujący wynowią nowe i coraz bardziej zaawansowane luki w zabezpieczeniach. Takie podejście pomaga zapewnić tempo szybkiego przenoszenia środowiska zagrożeń.

![Security Center wykrywanie zagrożeń](./media/threat-detection/azure-threat-detection-fig9.jpg)

Wykrywanie zagrożeń za pomocą usługi Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach uzyskanych z zasobów platformy Azure, sieci i powiązanych rozwiązań partnerskich. Analizuje ona te informacje, skorelowane informacje z wielu źródeł, aby identyfikować zagrożenia.

Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo.

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Do analizowania zdarzeń w całej sieci [](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) szkieletowej w chmurze są używane przełomowe technologie danych Big Data i uczenia maszynowego. Zaawansowana analiza może wykrywać zagrożenia, które byłyby niemożliwe do identyfikacji poprzez ręczne podejście i przewidywanie ewolucji ataków. Te typy usługi Security Analytics zostały omówione w następnych sekcjach.

### <a name="threat-intelligence"></a>Analiza zagrożeń

Firma Microsoft ma dostęp do ogromnąej ilości globalnej analizy zagrożeń.

Dane telemetryczne są przepływów z wielu źródeł, takich jak Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Zbrodnis Unit (DCU) i Microsoft Security Response Center (MSRC).

![Wyniki analizy zagrożeń](./media/threat-detection/azure-threat-detection-fig10.jpg)

Badacze otrzymują również informacje o analizie zagrożeń, które są współużytkowane przez głównych dostawców usług w chmurze, i subskrybują źródła analizy zagrożeń od stron trzecich. Usługa Azure Security Center może użyć tych informacji, aby ostrzec użytkownika przed zagrożeniami ze strony znanych złośliwych podmiotów. Oto niektóre przykłady:

-   **Wykorzystanie możliwości uczenia maszynowego**: Azure Security Center ma dostęp do ogromnych ilości danych dotyczących aktywności sieci w chmurze, które mogą być używane do wykrywania zagrożeń ukierunkowanych na wdrożenia platformy Azure.

-   **Wykrywanie siły**na drodze: Uczenie maszynowe służy do tworzenia historycznego wzorca prób dostępu zdalnego, który umożliwia wykrywanie ataków typu "odmowa" na Secure Shell (SSH), Remote Desktop Protocol (RDP) i portów SQL.

-   **Wykrywanie wychodzących DDoS i botnet**: Typowym celem ataków na zasoby w chmurze jest wykorzystanie mocy obliczeniowej tych zasobów do wykonywania innych ataków.

-   **Nowe serwery analizy behawioralnej i maszyny wirtualne**: Gdy serwer lub maszyna wirtualna zostanie naruszona, osoby atakujące wykorzystują wiele różnych technik do wykonywania złośliwego kodu w tym systemie, unikając wykrywania, zapewnienia trwałości i co eliminuje kontroli zabezpieczeń.

-   **Azure SQL Database wykrywanie zagrożeń**: Wykrywanie zagrożeń dla Azure SQL Database, które identyfikuje anomalie działania bazy danych, które wskazują nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

### <a name="behavioral-analytics"></a>Analiza behawioralna

Analiza behawioralna to metoda, która polega na analizie danych i porównywaniu ich z kolekcją znanych wzorców. Wzorce te nie są jednak prostymi sygnaturami. Określa się je za pośrednictwem złożonych algorytmów uczenia maszynowego, które są stosowane w przypadku wielkich zestawów danych.

![Wyniki analizy behawioralnej](./media/threat-detection/azure-threat-detection-fig11.jpg)

Wzorce są również określane przez analityków eksperta przy użyciu starannej analizy złośliwych zachowań. Azure Security Center może korzystać z analizy behawioralnej w celu identyfikowania zasobów narażonych na podstawie analizy dzienników maszyn wirtualnych, dzienników urządzeń sieci wirtualnej, dzienników sieci szkieletowej, zrzutów awaryjnych i innych źródeł.

Ponadto wzorce są skorelowane z innymi sygnałami, aby sprawdzać, czy są wspierane. Ta korelacja pomaga w identyfikacji zdarzeń, które są zgodne ze sprawdzonymi wskaźnikami naruszenia bezpieczeństwa.

Oto niektóre przykłady:
-   **Podejrzane wykonanie procesu**: Osoby atakujące wykorzystują kilka technik do wykonywania złośliwego oprogramowania bez konieczności wykrywania. Na przykład osoba atakująca może nadać złośliwemu oprogramowaniu takie same nazwy jak legalne pliki systemowe, ale umieścić te pliki w innej lokalizacji, użyć nazwy podobnej do nieszkodliwego pliku lub zamaskować rozszerzenie true dla pliku. Security Center modeluje zachowania procesów i monitorują wykonywanie procesów w celu wykrywania wartości odstających, takich jak te.

-   **Ukryte próby złośliwego oprogramowania i ataków**: Wyrafinowane złośliwe oprogramowanie może wyrównać tradycyjne produkty chroniące przed złośliwym kodem, nigdy nie zapisuj na dysku lub szyfrując składniki oprogramowania przechowywane na dysku. Takie złośliwe oprogramowanie może być jednak wykryte za pomocą analizy pamięci, ponieważ złośliwe oprogramowanie musi pozostawić ślady w pamięci do działania. Jeśli wystąpi awaria oprogramowania, zrzut awaryjny przechwytuje część pamięci w chwili pojawienia się awarii. Analizując pamięć w zrzucie awaryjnym, Azure Security Center mogą wykrywać techniki używane do luki w zabezpieczeniach oprogramowania, uzyskiwania dostępu do poufnych danych i dyskretnego pozostania się w obrębie zagrożonego komputera bez wpływu na wydajność maszynie.

-   **Ruchy boczne i wewnętrzne Rekonesans**: Aby zachować bezpieczeństwo w sieci i zlokalizować i zebrać cenne dane, osoby atakujące często próbują przenieść się później z zagrożonego komputera do innych osób w tej samej sieci. Security Center monitoruje działania procesowe i logowania w celu odnalezienia prób rozszerzania przyczółka osoby atakującej w sieci, takich jak zdalne wykonywanie poleceń, badanie sieci i wyliczanie kont.

-   **Złośliwe skrypty programu PowerShell**: Program PowerShell może być używany przez osoby atakujące do wykonywania złośliwego kodu na docelowych maszynach wirtualnych w różnych celach. Usługa Security Center sprawdza działanie programu PowerShell w poszukiwaniu dowodów na podejrzaną aktywność.

-   **Ataki**wychodzące: Osoby atakujące często kierują zasoby w chmurze w celu wykorzystania tych zasobów do instalowania dodatkowych ataków. Naruszone maszyny wirtualne mogą na przykład służyć do uruchamiania ataków w postaci pełnego wymuszania na inne maszyny wirtualne, wysyłania spamu lub skanowania otwartych portów i innych urządzeń w Internecie. Dzięki zastosowaniu uczenia maszynowego do ruchu sieciowego usługa Security Center może wykryć, kiedy wychodząca komunikacja sieciowa przekracza normy. W przypadku wykrycia spamu Security Center również skorelowanie nietypowego ruchu poczty e-mail z analizą z pakietu Office 365, aby określić, czy wiadomość jest prawdopodobnie szkodliwa, czy też z powodu uzasadnionej kampanii poczty e-mail.

### <a name="anomaly-detection"></a>Wykrywanie anomalii

Usługa Azure Security Center używa również wykrywania anomalii do identyfikowania zagrożeń. W przeciwieństwie do analizy behawioralnej (która zależy od znanych wzorców wyprowadzonych z dużych zestawów danych), wykrywanie anomalii jest bardziej „spersonalizowane” i koncentruje się na liniach bazowych, które są specyficzne dla przeprowadzonych wdrożeń. Uczenie maszynowe jest stosowane do określania normalnego działania wdrożeń, a następnie generowania reguł w celu zdefiniowania odstających warunków, które mogą reprezentować zdarzenie zabezpieczeń. Oto przykład:

-   **Ataki na ruch przychodzący RDP/SSH**: Wdrożenia mogą mieć zajęte maszyny wirtualne z wieloma logowaniami codziennie i innymi maszynami wirtualnymi, które mają kilka logowań. Azure Security Center można określić działania związane z logowaniem bazowym dla tych maszyn wirtualnych i użyć uczenia maszynowego do definiowania działania zwykłych logowań. W przypadku niezgodności z punktem odniesienia zdefiniowanym dla właściwości związanych z logowaniem może zostać wygenerowany alert. To uczenie maszynowe określa co jest istotne.

### <a name="continuous-threat-intelligence-monitoring"></a>Ciągłe monitorowanie analizy zagrożeń

Azure Security Center współpracuje z zespołami badań i analizy danych na całym świecie, które stale monitorują zmiany w poziomie zagrożenia. Obejmuje to następujące inicjatywy:

-   **Monitorowanie analizy zagrożeń**: Analiza zagrożeń obejmuje mechanizmy, wskaźniki, implikacje i porady umożliwiające podejmowanie akcji dotyczące istniejących lub powstających zagrożeń. Te informacje są udostępniane w społeczności zabezpieczeń, a firma Microsoft nieustannie monitoruje źródła danych analizy zagrożeń ze źródeł wewnętrznych i zewnętrznych.

-   **Udostępnianie sygnałów**: Szczegółowe informacje od zespołów ds. zabezpieczeń w szerokiej ofercie firmy Microsoft w chmurze i lokalnych usługach, serwerach i klientach urządzeń klienckich są udostępniane i analizowane.

-   **Specjaliści ds. zabezpieczeń firmy Microsoft**: Ciągłe zaangażowanie z zespołami w firmie Microsoft, którzy pracują w wyspecjalizowanych polach zabezpieczeń, takich jak dowodowych i wykrywanie ataków w sieci Web.

-   **Dostrajanie wykrywania**: Algorytmy są uruchamiane na rzeczywistych zestawach danych klienta, a badacze bezpieczeństwa pracują z klientami w celu weryfikacji wyników. Wyniki prawdziwie i fałszywie dodatnie są używane w celu udoskonalania algorytmów uczenia maszynowego.

Te połączone wysiłki skutkująją się w nowych i ulepszonych wykryciach, z których można korzystać natychmiast. Nie ma żadnych działań do wykonania.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Funkcje zaawansowanego wykrywania zagrożeń: Inne usługi platformy Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Maszyny wirtualne: Oprogramowanie chroniące przed złośliwym kodem

[Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla systemu Azure to rozwiązanie o pojedynczym agencie dla aplikacji i środowisk dzierżawców, przeznaczone do uruchamiania w tle bez interwencji człowieka. Ochronę można wdrożyć na podstawie potrzeb obciążeń aplikacji z podstawową, bezpieczną lub zaawansowaną konfiguracją niestandardową, w tym z monitorowaniem złośliwego oprogramowania. Usługa Azure chroniąca przed złośliwym kodem jest opcją zabezpieczeń dla maszyn wirtualnych platformy Azure, które są automatycznie instalowane na wszystkich maszynach wirtualnych usługi Azure PaaS.

#### <a name="microsoft-antimalware-core-features"></a>Podstawowe funkcje programu Microsoft chroniące przed złośliwym oprogramowaniem

Poniżej przedstawiono funkcje platformy Azure, które wdrażają i włączają oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla aplikacji:

-   **Ochrona w czasie rzeczywistym**: Monitoruje aktywność usług Cloud Services i na maszynach wirtualnych w celu wykrywania i blokowania wykonywania złośliwego oprogramowania.

-   **Zaplanowane skanowanie**: Program okresowo przeprowadza skanowanie w celu wykrywania złośliwego oprogramowania, w tym aktywnie działających programów.

-   **Korygowanie złośliwego oprogramowania**: Program automatycznie działa w przypadku wykrytego złośliwego oprogramowania, takiego jak usuwanie lub poddawania kwarantannie złośliwych plików, a także czyszczenie złośliwych wpisów rejestru.

-   **Aktualizacje sygnatur**: Program automatycznie instaluje najnowsze sygnatury ochrony (definicje wirusów) w celu zapewnienia aktualności ochrony przed ustaloną częstotliwością.

-   **Antimalware Engine aktualizacje**: Automatycznie aktualizuje Antimalware Engine firmy Microsoft.

-   **Aktualizacje platformy chroniącej przed złośliwym kodem**: Automatycznie aktualizuje platformę ochrony przed złośliwym oprogramowaniem firmy Microsoft.

-   **Aktywna ochrona**: Raportuje dane telemetryczne dotyczące wykrytych zagrożeń i podejrzanych zasobów Microsoft Azure, aby zapewnić szybką odpowiedź na rozwijającą się zagrożenie, co umożliwia przekazywanie synchronicznych podpisów w czasie rzeczywistym za pomocą systemu Microsoft Active Protection.

-   **Przykłady raportowania**: Dostarcza i raportuje przykłady do usługi Microsoft chroniącej przed złośliwym kodem, aby ułatwić udoskonalanie usługi i rozwiązywanie problemów.

-   **Wykluczenia**: Umożliwia administratorom aplikacji i usługi Konfigurowanie określonych plików, procesów i dysków w celu wykluczania ochrony i skanowania pod kątem wydajności i innych przyczyn.

-   **Zbieranie zdarzeń ochrony przed złośliwym kodem**: Rejestruje kondycję usługi chroniącej przed złośliwym kodem, podejrzane działania i akcje korygowania podejmowane w dzienniku zdarzeń systemu operacyjnego i zbiera je na koncie usługi Azure Storage klienta.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database wykrywanie zagrożeń

[Azure SQL Database wykrywanie zagrożeń](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) jest nową funkcją analizy zabezpieczeń wbudowaną w usługę Azure SQL Database. Pracując z zegarem, aby poznać, profilować i wykrywać nietypowe działania bazy danych, wykrywanie zagrożeń Azure SQL Database identyfikuje potencjalne zagrożenia dla bazy danych.

Urzędy zabezpieczeń lub inni Wyznaczeni Administratorzy mogą natychmiast otrzymywać powiadomienia o podejrzanych działaniach związanych z bazą danych. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zaleca dalsze badanie i łagodzenie zagrożeń.

Obecnie Azure SQL Database wykrywanie zagrożeń wykrywa potencjalne luki w zabezpieczeniach i ataki SQL oraz nietypowe wzorce dostępu do bazy danych.

Po otrzymaniu powiadomienia e-mail o wykryciu zagrożeń użytkownicy mogą nawigować i przeglądać odpowiednie rekordy inspekcji za pośrednictwem głębokiego linku w wiadomości e-mail. Spowoduje to otwarcie podglądu inspekcji lub wstępnie skonfigurowanego szablonu programu Excel, który pokazuje odpowiednie rekordy inspekcji wokół czasu podejrzanego zdarzenia, zgodnie z poniższymi:

-   Przeprowadź inspekcję magazynu dla bazy danych/serwera przy użyciu nietypowych działań bazy danych.

-   Właściwa tabela magazynu inspekcji, która została użyta w momencie zdarzenia do zapisania dziennika inspekcji.

-   Przeprowadź inspekcję rekordów godziny bezpośrednio po wystąpieniu zdarzenia.

-   Rekordy inspekcji o podobnym IDENTYFIKATORze zdarzenia w czasie zdarzenia (opcjonalnie dla niektórych detektorów).

SQL Database detektory zagrożeń korzystają z jednej z następujących metod wykrywania:

-   **Wykrywanie deterministyczne**: Wykrywa podejrzane wzorce (oparte na regułach) w zapytaniach klienta SQL, które pasują do znanych ataków. Ta metodologia ma duże wykrywanie i niską wartość fałszywą dodatnią, ale ograniczone pokrycie, ponieważ znajduje się w kategorii "wykrycia niepodzielne".

-   **Wykrywanie zachowań**: Wykrywa nietypowe działanie, które jest nietypowym zachowaniem w bazie danych, która nie była widoczna w ciągu ostatnich 30 dni. Przykładami działania anomalia klienta SQL może być skok nieudanych logowań lub zapytań, duża ilość danych, które są wyodrębniane, nietypowe zapytania kanoniczne lub nieznane adresy IP używane do uzyskiwania dostępu do bazy danych.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway zapory aplikacji sieci Web

[Zapora aplikacji sieci Web (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) to funkcja [platformy Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) , która zapewnia ochronę aplikacji sieci Web, które używają bramy aplikacji do standardowych funkcji [kontroli dostarczania aplikacji](https://kemptechnologies.com/in/application-delivery-controllers) . Zapora aplikacji sieci Web wykonuje te działania, chroniąc je przed większością [10 typowych luk w zabezpieczeniach w sieci Web (OWASP)](https://www.owasp.org/index.php/Top_10_2010-Main).

![Diagram zapory aplikacji sieci Web Application Gateway](./media/threat-detection/azure-threat-detection-fig13.png)

Ochrony obejmują:

-   Ochrona iniekcji SQL.

-   Ochrona wielu skryptów między lokacjami.

-   Wspólna ochrona sieci Web, taka jak iniekcja poleceń, przejmowanie żądania HTTP, podjęcie odpowiedzi HTTP i ataki zdalnego dołączania plików.

-   Ochrona przed naruszeniami protokołu HTTP.

-   Ochrona przed anomaliami protokołu HTTP, takimi jak brak agenta użytkownika hosta i akceptowanie nagłówków.

-   Ochrona przed botów, przeszukiwarkami i skanerami.

-   Wykrywanie typowych niespotykanych konfiguracji aplikacji (to jest Apache, IIS itd.).

Skonfigurowanie WAF na bramie aplikacji zapewnia następujące korzyści:

-   Chroni aplikację sieci Web przed lukami w zabezpieczeniach sieci Web i atakami bez konieczności modyfikacji kodu zaplecza.

-   Chroni wiele aplikacji sieci Web w tym samym czasie za bramą aplikacji. Brama aplikacji obsługuje obsługę maksymalnie 20 witryn sieci Web.

-   Monitoruje aplikacje sieci Web przed atakami przy użyciu raportów w czasie rzeczywistym, które są generowane przez dzienniki WAF usługi Application Gateway.

-   Program pomaga spełnić wymagania dotyczące zgodności. Niektóre funkcje kontroli zgodności wymagają, aby wszystkie punkty końcowe dostępne z Internetu były chronione przez rozwiązanie WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Interfejs API wykrywania anomalii: Kompilacja z Azure Machine Learning

Interfejs API wykrywania anomalii jest interfejsem API, który jest przydatny do wykrywania rozmaitych nietypowych wzorców w danych szeregów czasowych. Interfejs API przypisuje wynik anomalii do każdego punktu danych w szeregu czasowym, który może być używany do generowania alertów, monitorowania za pomocą pulpitów nawigacyjnych lub łączenia się z systemami biletów.

[Interfejs API wykrywania anomalii](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) może wykryć następujące typy anomalii w danych szeregów czasowych:

-   Wartości graniczne **i wartości DIP**: Gdy monitorujesz liczbę niepowodzeń logowania do usługi lub liczbę wyewidencjonowania w witrynie handlu elektronicznego, nietypowe skoki lub DIP mogą wskazywać ataki na zabezpieczenia lub przerwy w świadczeniu usług.

-   **Trendy dodatnie i ujemne**: Gdy monitorujesz użycie pamięci w obliczeniach, zmniejszenie rozmiaru wolnej pamięci wskazuje Potencjalny przeciek pamięci. W przypadku monitorowania długości kolejki usług trwały trend w górę może wskazywać na podstawowy problem z oprogramowaniem.

-   **Zmiany poziomów i zmiany w dynamicznym zakresie wartości**: Zmiany na poziomie opóźnień usługi po uaktualnieniu usługi lub niższych poziomach wyjątków po uaktualnieniu mogą być interesujące do monitorowania.

Interfejs API oparty na uczeniu maszynowym umożliwia:

-   **Elastyczne i niezawodne wykrywanie**: Modele wykrywania anomalii umożliwiają użytkownikom konfigurowanie ustawień czułości i wykrywanie anomalii między sezonowymi i niesezonowymi zestawami danych. Użytkownicy mogą dostosować model wykrywania anomalii, aby interfejs API wykrywania był mniej lub bardziej czuły zgodnie z ich potrzebami. Oznacza to wykrywanie mniej lub większej liczby widocznych anomalii w danych z lub bez wzorców sezonowych.

-   **Skalowalne i czasowe wykrywanie**: Tradycyjny sposób monitorowania z obecnymi progami ustawionymi przez wiedzę o domenach ekspertów jest kosztowny i nieskalowalny do milionów dynamicznie zmieniających się zestawów danych. W tym interfejsie API są poznanie modeli wykrywania anomalii, a modele są dostrajane automatycznie zarówno z danych historycznych, jak i w czasie rzeczywistym.

-   **Proaktywne i wykrywania akcji**: Do wczesnego wykrywania anomalii można zastosować powolne wykrywanie trendu i zmianę poziomu. Wykryto wczesne nietypowe sygnały, które mogą być używane do kierowania ludzi do badania i działania w obszarach problemu. Ponadto modele analiz głównych przyczyn i narzędzi alertów można opracowywać na podstawie tej usługi interfejsu API wykrywania anomalii.

Interfejs API wykrywania anomalii jest skutecznym i wydajnym rozwiązaniem dla szerokiego zakresu scenariuszy, takich jak monitorowanie kondycji usług i kluczowe wskaźniki wydajności, monitorowanie IoT, monitorowania wydajności i monitorowanie ruchu sieciowego. Poniżej przedstawiono niektóre popularne scenariusze, w których ten interfejs API może być przydatny:

- Działy IT potrzebują narzędzi do śledzenia zdarzeń, kodu błędu, dziennika użycia i wydajności (procesora CPU, pamięci itd.) w odpowiednim czasie.

-   Witryny handlu online umożliwiają śledzenie działań klientów, wyświetleń stron, kliknięć i tak dalej.

-   Firmy narzędziowe chcą śledzić użycie wody, gazu, energii elektrycznej i innych zasobów.

-   Usługa lub budowanie usług zarządzania chcą monitorować temperaturę, wilgotność, ruch i tak dalej.

-   IoT/producenci chcą używać danych czujników w szeregach czasowych do monitorowania przepływu pracy, jakości i tak dalej.

-   Dostawcy usług, tacy jak centra wywołań, muszą monitorować trend popytu usług, wielkość zdarzenia, Długość kolejki oczekiwania itd.

-   Grupy analizy biznesowej chcą monitorować kluczowe wskaźniki wydajności (takie jak wolumen sprzedaży, mową klienta lub cennik) nietypowy ruch w czasie rzeczywistym.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) jest krytycznym składnikiem Microsoft Cloud stosu zabezpieczeń. Jest to kompleksowe rozwiązanie, które może pomóc w organizacji podczas przenoszenia, aby w pełni wykorzystać możliwości aplikacji w chmurze. Pozwala ona na kontrolowanie, dzięki ulepszonemu wglądowi w działanie. Pomaga też zwiększyć ochronę kluczowych danych przechowywanych w aplikacjach w chmurze.

Organizacja może bezpieczniej przenieść się do chmury i jednocześnie utrzymać kontrolę nad danymi krytycznymi za pomocą narzędzi pomagających w ujawnianiu zasobów informatycznych niezatwierdzonych przez dział IT, ocenie ryzyka, egzekwowaniu zasad, badaniu działań i zatrzymywaniu zagrożeń.

| | |
|---|---|
| Wykrywanie | Odkrywaj cień przy użyciu Cloud App Security. Zyskaj widoczność przez odnajdywanie aplikacji, działań, użytkowników, danych i plików w środowisku chmury. Odkryj aplikacje innych firm, które są połączone z chmurą.|
|Badanie | Zbadaj swoje aplikacje w chmurze, używając narzędzi dowodowych w chmurze, aby uzyskać ryzykowne aplikacje, określonych użytkowników i pliki w sieci. Znajdź wzorce w danych zbieranych z chmury. Generuj raporty, aby monitorować chmurę. |
| Formant | Ograniczanie ryzyka przez skonfigurowanie zasad i alertów w celu osiągnięcia maksymalnej kontroli nad ruchem w chmurze sieci. Użyj Cloud App Security, aby przeprowadzić migrację użytkowników do bezpiecznych, oficjalnie zaakceptowanych aplikacji w chmurze. |
| Ochrona | Użyj Cloud App Security do zaakceptowania lub zabronienia działania aplikacji, wymuszania ochrony przed utratą danych, kontrolowania uprawnień i udostępniania oraz generowania niestandardowych raportów i alertów. |
| Formant | Ograniczanie ryzyka przez skonfigurowanie zasad i alertów w celu osiągnięcia maksymalnej kontroli nad ruchem w chmurze sieci. Użyj Cloud App Security, aby przeprowadzić migrację użytkowników do bezpiecznych, oficjalnie zaakceptowanych aplikacji w chmurze. |
| | |


![Diagram Cloud App Security](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integruje widoczność z chmurą przez:

-   Używanie Cloud Discovery do mapowania i identyfikowania środowiska chmury oraz aplikacji w chmurze, które są używane w organizacji.

-   Zaakceptowanie i Zabroń używania aplikacji w chmurze.

-   Przy użyciu łatwych do wdrożenia łączników aplikacji, które wykorzystują interfejsy API dostawcy, do wglądu i zarządzania aplikacjami, z którymi się łączysz.

-   Pomoc w zapewnianiu ciągłej kontroli według ustawienia, a następnie ciągłego dostrajania, zasad.

Przy zbieraniu danych z tych źródeł Cloud App Security uruchamia zaawansowaną analizę. Natychmiast powiadamia o nietypowych działaniach i zapewnia wgląd w środowisko chmury. Zasady można skonfigurować w Cloud App Security i używać ich do ochrony wszystkich elementów w środowisku chmury.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Zaawansowane możliwości wykrywania zagrożeń innych firm za pomocą witryny Azure Marketplace

### <a name="web-application-firewall"></a>Zapora aplikacji internetowej

Zapora aplikacji sieci Web sprawdza przychodzący ruch internetowy i blokuje iniekcje SQL, skrypty między lokacjami, operacje przekazywania złośliwego oprogramowania, ataki DDoS aplikacji oraz inne ataki, które są używane w aplikacjach sieci Web. Sprawdza także odpowiedzi z serwerów zaplecza sieci Web w celu zapobiegania utracie danych (DLP). Zintegrowany aparat kontroli dostępu umożliwia administratorom tworzenie szczegółowych zasad kontroli dostępu do uwierzytelniania, autoryzacji i ewidencjonowania aktywności (AAA), które zapewniają organizacjom silne uwierzytelnianie i kontrolę użytkownika.

Zapora aplikacji sieci Web zapewnia następujące korzyści:

-   Wykrywa i blokuje iniekcje SQL, skrypty między lokacjami, przekazywanie złośliwego oprogramowania, DDoS aplikacji lub inne ataki na aplikację.

-   Uwierzytelnianie i kontrola dostępu.

-   Skanuje ruch wychodzący, aby wykrywać poufne dane i maskować lub blokować przecieki informacji.

-   Przyspiesza dostarczanie zawartości aplikacji sieci Web przy użyciu takich funkcji, jak buforowanie, kompresja i inne optymalizacje ruchu.

Przykłady zapór aplikacji sieci Web, które są dostępne w portalu Azure Marketplace, można znaleźć w temacie [Barracuda WAF, brokat Virtual Web Application Firewall (vWAF), Imperva SecureSphere i Zapora IP ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Następne kroki

- [Możliwości wykrywania Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): Pomaga identyfikować aktywne zagrożenia przeznaczone dla zasobów platformy Azure i zapewnia szczegółowe informacje potrzebne do szybkiego reagowania.

- [Azure SQL Database wykrywanie zagrożeń](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Pomaga w rozwiązywaniu problemów dotyczących potencjalnych zagrożeń dla baz danych.
