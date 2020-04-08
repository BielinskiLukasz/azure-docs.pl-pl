---
title: Przewodnik po planowaniu i operacjach centrum zabezpieczeń
description: Ten dokument ułatwia planowanie przed wdrożeniem Centrum zabezpieczeń Azure oraz zawiera informacje o jego codziennych operacjach.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: f31c084be2fb017c0db521328e4ccdff9dd2aa25
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810475"
---
# <a name="planning-and-operations-guide"></a>Przewodnik dotyczący planowania i operacji
Ten przewodnik jest przeznaczony dla specjalistów technologii informatycznych,architektów IT, analityków zabezpieczeń informacji i administratorów chmury planujących korzystanie z usługi Azure Security Center.


## <a name="planning-guide"></a>Przewodnik planowania
W tym przewodniku omówiono zadania, które można wykonać w celu optymalizacji korzystania z usługi Security Center na podstawie wymagań dotyczących zabezpieczeń organizacji i modelu zarządzania chmurą. Aby w pełni wykorzystać zalety usługi Security Center, trzeba zrozumieć, w jak różny sposób będą używać usługi poszczególne osoby i zespoły w organizacji, gdyż umożliwi to spełnienie wymagań związanych z bezpieczeństwem programowania i obsługi, monitorowaniem, zarządzaniem i reagowaniem na zdarzenia. Kluczowe obszary, które należy wziąć pod uwagę podczas planowania korzystania z usługi Security Center:

* Role zabezpieczeń i kontrola dostępu
* Zasady zabezpieczeń i zalecenia w tym zakresie
* Zbieranie i przechowywanie danych
* Dołączanie zasobów innych niż platformy Azure
* Bieżące monitorowanie zabezpieczeń
* Reagowanie na zdarzenia

W następnej sekcji dowiesz się, jak utworzyć plan dla każdego z tych obszarów i zastosować te zalecenia w zależności od wymagań.


> [!NOTE]
> Artykuł [Azure Security Center frequently asked questions (FAQ)](faq-general.md) (Centrum zabezpieczeń Azure — często zadawane pytania) zawiera listę często zadawanych pytań, która również może być przydatna w fazie projektowania i planowania.

## <a name="security-roles-and-access-controls"></a>Role zabezpieczeń i kontrola dostępu
W zależności od rozmiaru i struktury organizacji wiele osób oraz zespołów może korzystać z Centrum zabezpieczeń, aby wykonywać różne zadania związane z zabezpieczeniami. Poniższy diagram przedstawia przykład fikcyjnych osób oraz ich ról i obowiązków związanych z zabezpieczeniami:

![Role](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Usługa Security Center umożliwia tym osobom wypełnianie różnych obowiązków. Przykład:

**Jan (właściciel obciążenia)**

* Zarządza obciążeniem chmury i powiązanymi zasobami
* Odpowiada za zaimplementowanie i utrzymanie ochrony zgodnie z zasadami zabezpieczeń firmy

**Aneta (CISO/CIO)**

* Odpowiada za wszystkie aspekty zabezpieczeń firmy
* Chce zrozumieć poziom zabezpieczeń firmy w przypadku różnych obciążeń chmury
* Potrzebuje informacji o najpoważniejszych atakach i zagrożeniach

**Daniel (Zabezpieczenia informatyczne)**

* Określa firmowe zasady zabezpieczeń w celu zapewnienia odpowiedniej ochrony
* Monitoruje zgodność z zasadami
* Generuje raporty dla kierownictwa lub audytorów

**Magda (operacje zabezpieczeń)**

* Bez przerwy monitoruje alerty zabezpieczeń i reaguje na nie
* Eskaluje do właściciela obciążenia chmury lub analityka zabezpieczeń informatycznych

**Stanisław (analityk zabezpieczeń)**

* Bada ataki
* Praca z właścicielem obciążenia chmury w celu zastosowania rozwiązania

Usługa Security Center używa [kontroli dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) udostępniającej [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług na platformie Azure. Gdy użytkownik otwiera usługę Security Center, widzi tylko informacje dotyczące zasobów, do których ma dostęp. Oznacza to, że użytkownik ma przypisaną rolę właściciela, współautora lub czytelnika subskrypcji albo grupy zasobów, do której należy zasób. Oprócz tych ról istnieją dwie określone role usługi Security Center:

- **Czytelnik zabezpieczeń**: użytkownik, który należy do tej roli, może wyświetlać tylko konfiguracje usługi Security Center, co obejmuje zalecenia, alerty, zasady i kondycję, ale nie może wprowadzać zmian.
- **Administrator zabezpieczeń**: użytkownik ma takie same uprawnienia, jak w przypadku roli Czytelnik zabezpieczeń, ale ma również możliwość aktualizowania zasad zabezpieczeń oraz odrzucania zaleceń i alertów.

Opisane powyżej role usługi Security Center nie mają dostępu do innych obszarów usług platformy Azure, takich jak magazyn, sieć internetowa i aplikacje mobilne oraz Internet rzeczy.

W przypadku osób opisanych na poprzednim diagramie będzie potrzebna następująca kontrola dostępu oparta na rolach:

**Jan (właściciel obciążenia)**

* Właściciel/współautor grupy zasobów

**Aneta (CISO/CIO)**

* Właściciel/współautor subskrypcji lub administrator zabezpieczeń

**Daniel (Zabezpieczenia informatyczne)**

* Właściciel/współautor subskrypcji lub administrator zabezpieczeń

**Magda (operacje zabezpieczeń)**

* Czytelnik subskrypcji lub czytelnik zabezpieczeń przeglądający alerty
* Właściciel/współautor subskrypcji lub administrator zabezpieczeń wymagany do odrzucenia alertów

**Stanisław (analityk zabezpieczeń)**

* Czytelnik subskrypcji przeglądający alerty
* Właściciel/współautor subskrypcji wymagany do odrzucenia alertów
* Może być wymagany dostęp do obszaru roboczego

Niektóre inne istotne informacje, które należy wziąć pod uwagę:

* Tylko właściciele lub współautorzy subskrypcji i administratorzy zabezpieczeń mogą edytować zasady zabezpieczeń.
* Tylko właściciele i współautorzy subskrypcji i grupy zasobów mogą stosować zalecenia dotyczące zabezpieczeń zasobu.

Podczas planowania kontroli dostępu przy użyciu kontroli dostępu opartej na rolach dla usługi Security Center musisz wiedzieć, kto w Twojej organizacji będzie korzystał z usługi. Ponadto musisz wiedzieć, jakiego rodzaju zadania będą wykonywały te osoby, a następnie odpowiednio skonfigurować kontrolę dostępu opartą na rolach.

> [!NOTE]
> Zaleca się przypisanie użytkownikom najbardziej ograniczonej roli wystarczającej do wykonywania zadań. Na przykład użytkownikom, którzy muszą tylko przeglądać informacje o stanie zabezpieczeń zasobów, ale nie muszą podejmować działań, np. stosować zaleceń ani edytować zasad, należy przypisać rolę czytelnika.
>
>

## <a name="security-policies-and-recommendations"></a>Zasady zabezpieczeń i zalecenia w tym zakresie
Zasady zabezpieczeń definiują pożądaną konfigurację Twoich obciążeń oraz pomagają zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy. W usłudze Security Center możesz zdefiniować zasady dla swoich subskrypcji platformy Azure, które mogą być dopasowane do typu obciążenia oraz poufności danych.

Zasady usługi Security Center zawierają następujące składniki:
- [Zbieranie danych](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): ustawienia agenta aprowizacji i zbierania danych.
- [Zasady zabezpieczeń:](https://docs.microsoft.com/azure/security-center/security-center-policies) [zasady platformy Azure,](../governance/policy/overview.md) która określa, które formanty są monitorowane i zalecane przez Usługę Security Center, lub użyj zasady platformy Azure do tworzenia nowych definicji, definiowania dodatkowych zasad i przypisywania zasad między grupami zarządzania.
- [Wiadomości e-mail z powiadomieniami](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): ustawienia kontaktów i powiadomień dotyczących zabezpieczeń.
- [Warstwa cenowa](https://docs.microsoft.com/azure/security-center/security-center-pricing): wybór wersji bezpłatnej lub ceny standardowej, który określa, które funkcje usługi Security Center są dostępne dla zasobów w zakresie (można określić dla subskrypcji, grupy zasobów i obszarów roboczych).

> [!NOTE]
> Określenie kontaktu dotyczącego zabezpieczeń, za pomocą którego zespół platformy Azure może się skontaktować z odpowiednią osobą w Twojej organizacji, jeśli wystąpi incydent związany z zabezpieczeniami. Aby uzyskać więcej informacji na temat sposobu włączania tego zalecenia, przeczytaj [Provide security contact details in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) (Wprowadzanie danych kontaktowych na potrzeby zabezpieczeń w usłudze Azure Security Center).

### <a name="security-policies-definitions-and-recommendations"></a>Definicje i zalecenia dotyczące zasad zabezpieczeń
Usługa Security Center automatycznie tworzy domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. Możesz edytować zasady w usłudze Security Center lub użyć usługi Azure Policy do utworzenia nowych definicji, zdefiniowania dodatkowych zasad i przypisania zasad w grupach zarządzania (które mogą reprezentować całą organizację, jednostki biznesowe w niej itp.) oraz monitorowania zgodności z tymi zasadami w tych zakresach.

Przed skonfigurowaniem zasad zabezpieczeń przejrzyj poszczególne [zalecenia dotyczące zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-recommendations) i określ, czy te zasady są właściwe dla różnych subskrypcji i grup zasobów. Ważne jest również, aby zrozumieć, jakie działania powinny zostać podjęte w celu wypełnienia zaleceń dotyczących zabezpieczeń oraz kto w organizacji będzie odpowiedzialny za monitorowanie pod kątem nowych zaleceń i podejmowanie wymaganych działań.

## <a name="data-collection-and-storage"></a>Zbieranie i przechowywanie danych
Usługa Azure Security Center używa agenta usługi Log Analytics — jest to ten sam agent używany przez usługę Azure Monitor — do zbierania danych zabezpieczeń z maszyn wirtualnych. [Dane zebrane](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) od tego agenta będą przechowywane w obszarze roboczym usługi Log Analytics.

### <a name="agent"></a>Agent

Gdy automatyczne inicjowanie obsługi administracyjnej jest włączone w zasadach zabezpieczeń, agent usługi Log Analytics (dla [systemu Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) lub [Linux)](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)jest zainstalowany na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i wszystkich nowych, które są tworzone. Jeśli maszyna wirtualna lub komputer ma już zainstalowanego agenta usługi Log Analytics, usługa Azure Security Center wykorzysta bieżącego zainstalowanego agenta. Proces agenta jest zaprojektowany tak, aby był nieinwazyjny i miał bardzo minimalny wpływ na wydajność maszyny wirtualnej.

Agent analizy dzienników dla systemu Windows wymaga użycia portu TCP 443. Przeczytaj [artykuł na temat rozwiązywania problemów](security-center-troubleshooting-guide.md), aby uzyskać więcej informacji.

Jeśli w dowolnym momencie chcesz wyłączyć funkcję zbierania danych, możesz to zrobić w zasadach zabezpieczeń. Jednak ponieważ agent usługi Log Analytics może być używany przez inne usługi zarządzania i monitorowania platformy Azure, agent nie zostanie automatycznie odinstalowany po wyłączeniu zbierania danych w usłudze Security Center. W razie potrzeby można odinstalować agenta ręcznie.

> [!NOTE]
> Lista obsługiwanych maszyn wirtualnych znajduje się w artykule [Azure Security Center frequently asked questions (FAQ)](faq-vms.md) (Azure Security Center — często zadawane pytania [FAQ]).

### <a name="workspace"></a>Workspace

Obszar roboczy to zasób platformy Azure, który służy jako kontener dla danych. Ty i inni członkowie organizacji możecie używać wielu obszarów roboczych, aby zarządzać różnymi zestawami danych zebranymi z całej infrastruktury IT lub jej części.

Dane zebrane z agenta usługi Log Analytics (w imieniu usługi Azure Security Center) będą przechowywane w istniejących obszarach roboczych usługi Log Analytics skojarzonych z subskrypcją platformy Azure lub w nowych obszarach roboczych, z uwzględnieniem obszaru geograficznego maszyny Wirtualnej.

W witrynie Azure Portal można wyświetlić listę swoich obszarów roboczych usługi Log Analytics, w tym wszystkich utworzonych przez usługę Azure Security Center. W przypadku nowych obszarów roboczych zostanie utworzona powiązana grupa zasobów. W obu przypadkach stosowana będzie następująca konwencja nazewnictwa:

* Obszar roboczy: *DefaultWorkspace-[identyfikator-subskrypcji]-[lokalizacja-geograficzna]*
* Grupa zasobów: *DefaultResourceGroup-[geo]*

W przypadku obszarów roboczych utworzonych przez usługę Azure Security Center dane są przechowywane przez 30 dni. W przypadku istniejących obszarów przechowywanie zależy od warstwy cenowej obszaru roboczego. Jeśli chcesz, możesz również użyć istniejącego obszaru roboczego.

> [!NOTE]
> Firma Microsoft zobowiązuje się chronić poufność i bezpieczeństwo tych danych. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi. Aby uzyskać więcej informacji na temat obsługi danych i poufności, należy przeczytać artykuł [Azure Security Center — bezpieczeństwo danych](security-center-data-security.md).
>

## <a name="onboarding-non-azure-resources"></a>Dołączanie zasobów innych niż platformy Azure

Usługa Security Center może monitorować stan bezpieczeństwa komputerów nienależących do platformy Azure, ale musisz najpierw dołączyć te zasoby. Przeczytaj artykuł [Onboarding to Azure Security Center Standard for enhanced security](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers) (Dołączanie do standardowej usługi Azure Security Center w celu poprawy zabezpieczeń), aby uzyskać więcej informacji na temat sposobu dołączania zasobów nienależących do platformy Azure.

## <a name="ongoing-security-monitoring"></a>Bieżące monitorowanie zabezpieczeń
Po wstępnej konfiguracji i zastosowaniu zaleceń usługi Security Center następny krok polega na uwzględnieniu procesów operacyjnych usługi Security Center.

Omówienie usługi Security Center zapewnia spójny widok zabezpieczeń dla wszystkich podłączonych przez Ciebie zasobów należących do platformy Azure i nienależących do niej. W poniższym przykładzie pokazano środowisko zawierające wiele problemów do rozwiązania:

![pulpit nawigacyjny](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Usługa Security Center nie zakłóca zwykłych procedur operacyjnych, pasywnie monitoruje wdrożenia i zapewnia zalecenia na podstawie włączonych zasad zabezpieczeń.

Po podjęciu decyzji o pierwszym użyciu usługi Security Center dla bieżącego środowiska platformy Azure pamiętaj, aby przejrzeć wszystkie zalecenia. Można to zrobić na kafelku **Zalecenia** lub względem poszczególnych zasobów (**Obliczenia**, **Sieci**, **Magazyn i dane** oraz **Aplikacja**).

Po wykonaniu wszystkich zaleceń sekcja **Zapobieganie** powinna być zielona dla wszystkich przejrzanych zasobów. Na tym etapie monitorowanie ciągłe staje się łatwiejsze, ponieważ działania są podejmowane tylko w oparciu o zmiany kondycji zabezpieczeń zasobów oraz kafelki zaleceń.

Sekcja **Wykrywanie** jest bardziej reaktywna. Zawiera ona alerty dotyczące problemów, które występują teraz lub wystąpiły w przeszłości, wykrytych przez kontrolki usługi Security Center i systemy innych firm. Kafelek Alerty zabezpieczeń wyświetli wykresy słupkowe reprezentujące liczbę alertów znalezionych w każdym dniu i ich rozkład między różnymi kategoriami ważności (niski, średni, wysoki). Więcej informacji na temat alertów zabezpieczeń znajduje się w artykule [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure](security-center-managing-and-responding-alerts.md).

Zaplanuj odwiedzenie opcji analizy zagrożeń jako część codziennych operacji zabezpieczeń. Możesz tam zidentyfikować zagrożenia bezpieczeństwa środowiska, takie jak ustalenie, czy dany komputer jest częścią botnetu.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitorowanie nowych lub zmodyfikowanych zasobów
Większość środowisk platformy Azure są dynamiczne, z zasobów regularnie tworzone, obracane w górę lub w dół, ponownie skonfigurowane i zmienione. Usługa Security Center pomaga zagwarantować widoczność stanu zabezpieczeń nowych zasobów.

Po dodaniu nowych zasobów (maszyn wirtualnych, baz danych SQL) do środowiska Azure usługa Security Center wykrywa je automatycznie i rozpoczyna monitorowanie ich zabezpieczeń. Obejmuje to także role procesu roboczego i role sieci Web usługi PaaS. Jeśli zbieranie danych jest włączone w [zasadach zabezpieczeń,](tutorial-security-policy.md)dodatkowe funkcje monitorowania zostaną włączone automatycznie dla maszyn wirtualnych.

![Kluczowe obszary](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. W przypadku maszyn wirtualnych kliknij pozycję **Oblicz & aplikacje**w sekcji **Higiena zabezpieczeń zasobów.** Wszystkie problemy z włączaniem danych lub powiązane zalecenia zostaną wyświetlone na karcie **Omówienie** i w sekcji **Zalecenia dotyczące monitorowania**.
2. Wyświetl **zalecenia**, aby sprawdzić, jakie zagrożenia dla bezpieczeństwa, jeśli w ogóle wystąpiły, zostały zidentyfikowane dla nowego zasobu.
3. Często zdarza się, że podczas dodawania do środowiska nowych maszyn wirtualnych na początku zostaje zainstalowany tylko system operacyjny. Właściciel zasobu może potrzebować trochę czasu, aby wdrożyć inne aplikacje, które będą używane przez te maszyny wirtualne.  Najlepiej byłoby znać stan docelowy danego obciążenia. Czy będzie to serwer aplikacji? W zależności od tego, jaką rolę będzie pełnić nowe obciążenie, możesz włączyć odpowiednie **zasady zabezpieczeń**, co stanowi trzeci krok w tym przepływie pracy.
4. W miarę dodawania nowych zasobów do środowiska platformy Azure na kafelku Alerty zabezpieczeń mogą pojawić się nowe **alerty.** Poszukaj nowych alertów w tym kafelku i postępuj zgodnie z zaleceniami.

Należy również regularnie monitorować istniejące zasoby pod kątem zmian konfiguracji, które mogły spowodować zagrożenia bezpieczeństwa, oddalają się od zalecanych linii bazowych i alertów zabezpieczeń. Rozpocznij na pulpicie nawigacyjnym usługi Security Center. Stamtąd, masz trzy główne obszary do przeglądu w sposób spójny.

![Operacje](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. Panel **Zapobieganie** zapewnia szybki dostęp do kluczowych zasobów. Opcja ta służy do monitorowania zasobów Obliczenia, Sieci, Magazyn i dane oraz Aplikacje.
2. Panel **Zalecenia** umożliwia przeglądanie zaleceń usługi Security Center. Podczas bieżącego monitorowania może się okazać, że nie masz zaleceń na co dzień, co jest normalne, ponieważ zostały uwzględnione wszystkie zalecenia dotyczące początkowej konfiguracji centrum zabezpieczeń. Z tego powodu nowe informacje w tej sekcji mogą nie występować codziennie i trzeba uzyskiwać do nich dostęp w zależności od potrzeb.
3. Na panelu **Wykrywanie** zmiany mogą pojawiać się bardzo często lub bardzo rzadko. Zawsze czytaj alerty zabezpieczeń i podejmuj działania na podstawie zaleceń usługi Security Center.

### <a name="hardening-access-and-applications"></a>Wzmacnianie ochrony dostępu i aplikacji

W ramach operacji zabezpieczeń należy również zastosować środki zapobiegawcze w celu ograniczenia dostępu do maszyn wirtualnych i kontroli aplikacji, które są uruchomione na maszynach wirtualnych. Blokując ruch przychodzący na maszyny wirtualne platformy Azure, zmniejszasz narażenie na ataki, jednocześnie zapewniając łatwy dostęp do nawiązywania połączenia z maszynami wirtualnymi w razie potrzeby. Użyj funkcji dostępu [do maszyn wirtualnych just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) do hartowania dostępu do maszyn wirtualnych.

[Formantów aplikacji adaptacyjnych](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) można użyć, aby ograniczyć, które aplikacje mogą działać na maszynach wirtualnych znajdujących się na platformie Azure. Między innymi pomaga to wzmocnić maszyny wirtualne przed złośliwym oprogramowaniem. Korzystając z uczenia maszynowego, Usługa Security Center analizuje procesy uruchomione na maszynie wirtualnej, aby ułatwić tworzenie reguł białej listy.


## <a name="incident-response"></a>Reagowanie na zdarzenia
Usługa Security Center wykrywa zagrożenia i powiadamia o nich, gdy tylko wystąpią. Firmy powinny śledzić pojawianie się nowych alertów zabezpieczeń i w razie potrzeby podejmować działania w celu zbadania ataku lub usunięcia jego skutków. Aby uzyskać więcej informacji na temat działania ochrony przed zagrożeniami w centrum zabezpieczeń, przeczytaj [artykuł Jak usługa Azure Security Center wykrywa zagrożenia i reaguje na nie](security-center-alerts-overview.md#detect-threats).

Chociaż w tym artykule nie ma zamiaru pomóc w tworzeniu własnego planu reagowania na incydenty, będziemy używać microsoft Azure Security Response w cyklu życia chmury jako podstawa dla etapów reagowania na zdarzenia. Poszczególne etapy przedstawiono na poniższym diagramie:

![Podejrzane działania](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> W tworzeniu takiego planu może pomóc [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Przewodnik obsługi zdarzeń zabezpieczeń komputera) Narodowego Instytutu Norm i Technologii (NIST).
>

Na poniższych etapach możesz używać alertów usługi Security Center:

* **Wykrywanie**: zidentyfikuj podejrzane działanie w co najmniej jednym zasobie.
* **Ocenianie**: wykonaj wstępną ocenę, aby uzyskać więcej informacji na temat podejrzanego działania.
* **Diagnozowanie**: zastosuj czynności zaradcze, aby przeprowadzić procedurę techniczną rozwiązania problemu.

Każdy alert zabezpieczeń zawiera informacje, które mogą ułatwić lepsze zrozumienie natury ataku i zasugerować możliwe ograniczenie jego skutków. Niektóre alerty zapewniają również linki do większej ilości informacji lub do innych źródeł informacji w ramach platformy Azure. Dostarczonych informacji możesz użyć do dalszej analizy i rozpoczęcia rozwiązywania problemu. Możliwe jest również wyszukiwanie danych związanych z zabezpieczeniami przechowywanych w obszarze roboczym.

W poniższym przykładzie pokazano podejrzane działanie w protokole RDP:

![Podejrzane działania](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Ta strona pokazuje szczegóły dotyczące godziny, o której nastąpił atak, nazwy hosta źródłowego, docelowej maszyny wirtualnej oraz poszczególnych kroków zaleceń. W niektórych okolicznościach informacje źródłowe ataku mogą być puste. Więcej informacji na temat działania tego typu znajduje się w artykule [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) (Brakujące informacje źródłowe w alertach Centrum zabezpieczeń Azure).

Na tej stronie możesz również uruchomić dochodzenie, aby lepiej zrozumieć oś czasu ataku, sposób przeprowadzenia ataku, które systemy zostały potencjalnie naruszone, które poświadczenia zostały użyte i zobaczyć graficzną reprezentację całego łańcucha ataku.

Po zidentyfikowaniu systemu, którego bezpieczeństwo zostało naruszone, można uruchomić [automatyzację przepływu pracy,](workflow-automation.md) która została wcześniej utworzona. Są to kolekcje procedur, które mogą być wykonywane z usługi Security Center po wyzwoleniu przez alert.

W [programie Jak wykorzystać pakiet Azure Security Center & Microsoft Operations Management Suite w](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) przypadku odpowiedzi na incydenty— możesz zobaczyć niektóre demonstracje, które mogą pomóc w zrozumieniu, w jaki sposób usługa Security Center może być używana na każdym z tych etapów.

> [!NOTE]
> Przeczytaj artykuł [Zarządzanie alertami zabezpieczeń i odpowiadanie na nie w usłudze Azure Security Center,](security-center-managing-and-responding-alerts.md) aby uzyskać więcej informacji na temat korzystania z funkcji usługi Security Center w celu pomocy podczas procesu reagowania na incydenty.
>
>

## <a name="next-steps"></a>Następne kroki
W tym dokumencie omówiono, jak zaplanować wykorzystanie usługi Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Zarządzanie alertami zabezpieczeń i odpowiadanie na nie w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Monitorowanie rozwiązań partnerskich za pomocą usługi Azure Security Center](security-center-partner-solutions.md) — dowiedz się, jak monitorować stan kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](faq-general.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — znajdź wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure.
