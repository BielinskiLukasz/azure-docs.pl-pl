---
title: Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal
titlesuffix: Azure Virtual Network
description: Dowiedz się, w jaki sposób używać Azure DDoS Protection standardową telemetrię w Azure Monitor, aby ograniczyć ataki.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 37a8799ca1ea986d5b47dad6e17781d7dfbacfab
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261678"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Zarządzanie standardem Azure DDoS Protection przy użyciu Azure Portal

Informacje na temat włączania i wyłączania rozproszonej ochrony typu "odmowa usługi" (DDoS) oraz używania telemetrii w celu ograniczenia ataku DDoS przy użyciu standardu Azure DDoS Protection. DDoS Protection Standard chroni zasoby platformy Azure, takie jak maszyny wirtualne, moduły równoważenia obciążenia i bramy aplikacji z przypisanym [publicznym adresem IP](virtual-network-public-ip-address.md) platformy Azure. Aby dowiedzieć się więcej na temat DDoS Protection Standard i jego możliwości, zobacz [omówienie DDoS Protection Standard](ddos-protection-overview.md).

Przed wykonaniem kroków opisanych w tym samouczku Zaloguj się do Azure Portal przy https://portal.azure.com użyciu konta przypisanego do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-ddos-protection-plan"></a>Tworzenie planu ochrony przed atakami DDoS

Plan ochrony DDoS definiuje zbiór sieci wirtualnych, dla których włączono standard ochrony DDoS w ramach subskrypcji. Można skonfigurować jeden plan ochrony DDoS dla Twojej organizacji i połączyć sieci wirtualne z wielu subskrypcji w ramach tego samego planu. Sam plan DDoS Protection jest również skojarzony z subskrypcją, którą wybiera się podczas tworzenia planu. Plan DDoS Protection działa między regionami i subskrypcjami. Przykład — możesz utworzyć plan w regionie Wschodnie stany USA i link do subskrypcji #1 w dzierżawie. Ten sam plan może być połączony z sieciami wirtualnymi z innych subskrypcji w różnych regionach w ramach dzierżawy. Subskrypcja skojarzona z planem polega na miesięcznym rozliczeniu za plan, a także opłat za użycie nadwyżkowe, w przypadku gdy liczba chronionych publicznych adresów IP przekracza 100. Aby uzyskać więcej informacji na temat cennika usługi DDoS, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/ddos-protection/).

Tworzenie więcej niż jednego planu nie jest wymagane w przypadku większości organizacji. Nie można przenieść planu między subskrypcjami. Jeśli chcesz zmienić subskrypcję, a plan znajduje się w, musisz [usunąć istniejący plan](#work-with-ddos-protection-plans) i utworzyć nowy.

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
2. Wyszukaj *DDoS*. Po pojawieniu się **planu ochrony DDoS** w wynikach wyszukiwania zaznacz je.
3. Wybierz pozycję **Utwórz**.
4. Wprowadź lub wybierz własne wartości albo wprowadź lub wybierz poniższe przykładowe wartości, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie        |Wartość                                              |
    |---------      |---------                                          |
    |Name           | myDdosProtectionPlan                              |
    |Subscription   | Wybierz subskrypcję.                         |
    |Resource group | Wybierz pozycję **Utwórz nową** i wprowadź nazwę *zasobu* |
    |Location       | East US                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Włącz DDoS dla nowej sieci wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wprowadź lub wybierz własne wartości, wpisz lub wybierz poniższe przykładowe wartości, zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie         | Wartość                                                        |
    | ---------       | ---------                                                    |
    | Name (Nazwa)            | myVirtualNetwork                                             |
    | Subscription    | Wybierz subskrypcję.                                    |
    | Resource group  | Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**. |
    | Location        | East US                                                      |
    | Ochrona DDos | Wybierz pozycję **standardowa** , a następnie w obszarze **Ochrona DDoS**wybierz pozycję **myDdosProtectionPlan**. Wybrany plan może być w tej samej lub innej subskrypcji niż sieć wirtualna, ale obie subskrypcje muszą być skojarzone z tą samą dzierżawą Azure Active Directory.|

Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji, gdy dla sieci wirtualnej jest włączony Standard DDoS. Jeśli musisz przenieść sieć wirtualną z włączonym standardem DDoS, wyłącz najpierw pozycję DDoS Standard, Przenieś sieć wirtualną, a następnie Włącz Standard DDoS. Po przeniesieniu wartości progowe dla wszystkich chronionych publicznych adresów IP w sieci wirtualnej zostaną zresetowane.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Włącz DDoS dla istniejącej sieci wirtualnej

1. Utwórz plan ochrony DDoS, wykonując kroki opisane w temacie [Tworzenie planu ochrony DDoS](#create-a-ddos-protection-plan), jeśli nie masz istniejącego planu ochrony DDoS.
2. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal.
3. Wprowadź nazwę sieci wirtualnej, dla której chcesz włączyć DDoS Protection Standard dla w **polu Wyszukaj zasoby, usługi i dokumenty** w górnej części portalu. Gdy nazwa sieci wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
4. W obszarze **Ustawienia**wybierz pozycję **Ochrona DDoS**.
5. Wybierz opcję **Standardowa**. W obszarze **Plan ochrony DDoS**wybierz istniejący plan ochrony DDoS lub plan, który został utworzony w kroku 1, a następnie wybierz pozycję **Zapisz**. Wybrany plan może być w tej samej lub innej subskrypcji niż sieć wirtualna, ale obie subskrypcje muszą być skojarzone z tą samą dzierżawą Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Wyłącz DDoS dla sieci wirtualnej

1. W **polu Wyszukaj zasoby, usługi i dokumenty** w górnej części portalu wprowadź nazwę sieci wirtualnej, dla której chcesz wyłączyć usługę DDoS Protection w warstwie Standardowa. Gdy nazwa sieci wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
2. W obszarze **Ustawienia**wybierz pozycję **Ochrona DDoS**.
3. Wybierz pozycję **podstawowa** w obszarze **Plan ochrony DDoS** , a następnie wybierz pozycję **Zapisz**.

## <a name="work-with-ddos-protection-plans"></a>Współpraca z planami ochrony DDoS

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź *DDoS* w polu **Filtr** . Gdy **plany ochrony DDoS** są wyświetlane w wynikach, zaznacz je.
3. Wybierz z listy plan ochrony, który chcesz wyświetlić.
4. Zostaną wyświetlone wszystkie sieci wirtualne skojarzone z planem.
5. Jeśli chcesz usunąć plan, najpierw usuń skojarzenie wszystkich sieci wirtualnych. Aby usunąć skojarzenie planu z sieci wirtualnej, zapoznaj się z tematem [wyłączanie DDoS dla sieci wirtualnej](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurowanie alertów dotyczących metryk ochrony DDoS

Można wybrać dowolną z dostępnych metryk ochrony DDoS, aby poinformować użytkownika o aktywnym ograniczeniu podczas ataku przy użyciu konfiguracji alertu Azure Monitor. Gdy warunki są spełnione, określony adres otrzymuje wiadomość e-mail z alertem:

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź wartość *monitor* w polu **Filtr** . Gdy **monitor** pojawi się w wynikach, wybierz go.
3. Wybierz pozycję **metryki** w obszarze **usługi udostępnione**.
4. Wprowadź lub wybierz własne wartości lub wprowadź następujące przykładowe wartości, zaakceptuj pozostałe ustawienia domyślne, a następnie wybierz przycisk **OK**:

    |Ustawienie                  |Wartość                                                                                               |
    |---------                |---------                                                                                           |
    |Name                     | myDdosAlert                                                                                        |
    |Subscription             | Wybierz subskrypcję zawierającą publiczny adres IP, dla którego chcesz otrzymywać alerty.        |
    |Resource group           | Wybierz grupę zasobów zawierającą publiczny adres IP, dla którego chcesz otrzymywać alerty.      |
    |Resource                 | Wybierz publiczny adres IP, który zawiera publiczny adres IP, dla którego chcesz otrzymywać alerty. DDoS monitoruje publiczne adresy IP przypisane do zasobów w ramach sieci wirtualnej. Jeśli nie masz żadnych zasobów z publicznymi adresami IP w sieci wirtualnej, musisz najpierw utworzyć zasób z publicznym adresem IP. Możesz monitorować publiczny adres IP wszystkich zasobów wdrożonych za pomocą Menedżer zasobów (nieklasyczny) wymienionych w [sieci wirtualnej dla usług platformy Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), z wyjątkiem środowisk Azure App Service i platformy Azure VPN Gateway. Aby kontynuować pracę z tym samouczkiem, możesz szybko utworzyć maszynę wirtualną z [systemem Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .                   |
    |Metryka                   | W obszarze atak DDoS                                                                            |
    |Próg                | 1- **1** oznacza, że jesteś w trakcie ataku. **0** oznacza, że nie jesteś w trakcie ataku.                         |
    |Okres                   | Wybierz dowolną wybraną wartość.                                                                   |
    |Powiadom pocztą E-mail         | Zaznacz pole wyboru                                                                                  |
    |Dodatkowy administrator | Wprowadź swój adres e-mail, jeśli nie jesteś właścicielem, współautorem lub czytelnikem poczty e-mail dla subskrypcji. |

    W ciągu kilku minut wykrywania ataków otrzymasz wiadomość e-mail od Azure Monitor metryk, które wyglądają podobnie do poniższej ilustracji:

    ![Alert ataku](./media/manage-ddos-protection/ddos-alert.png)


Aby zasymulować atak DDoS w celu zweryfikowania alertu, zobacz [Validate DDoS Detection](#validate-ddos-detection).

Możesz również dowiedzieć się więcej o [konfigurowaniu elementów webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na potrzeby tworzenia alertów.

## <a name="use-ddos-protection-telemetry"></a>Korzystanie z telemetrii ochrony DDoS

Dane telemetryczne dla ataku są udostępniane za pośrednictwem Azure Monitor w czasie rzeczywistym. Dane telemetryczne są dostępne tylko dla czasu, w którym publiczny adres IP jest objęty ograniczeniem. Nie widzisz danych telemetrycznych przed usunięciem ataku lub po nim.

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź wartość *monitor* w polu **Filtr** . Gdy **monitor** pojawi się w wynikach, wybierz go.
3. Wybierz pozycję **metryki**w obszarze **usługi udostępnione**.
4. Wybierz **subskrypcję** i **grupę zasobów** , która zawiera publiczny adres IP, dla którego chcesz użyć danych telemetrycznych.
5. Wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz użyć danych telemetrycznych.
6. Seria **dostępnych metryk** pojawia się po lewej stronie ekranu. Te metryki, po wybraniu, są przedstawiane na wykresie **metryk Azure monitor** na ekranie przegląd.
7. Wybierz typ **agregacji** jako **maksimum**

Nazwy metryk składają się z różnych typów pakietów i bajtów zamiast pakietów, a podstawowa konstrukcja nazw tagów w każdej metryce w następujący sposób:

- **Porzucona nazwa tagu** (na przykład **pakiety przychodzące porzucone DDoS**): Liczba pakietów porzuconych/wyczyszczonych przez system ochrony DDoS.
- **Nazwa tagu przekazanego** (na przykład **przekazane pakiety przychodzące DDoS**): Liczba pakietów przesłanych przez system DDoS do docelowego adresu VIP — ruch, który nie został przefiltrowany.
- **Brak nazwy tagu** (na przykład **pakiety przychodzące DDoS**): Całkowita liczba pakietów, które zostały dołączone do systemu do przemywania — reprezentują sumę pakietów porzuconych i przesłanych dalej.

Aby zasymulować atak DDoS na potrzeby weryfikowania telemetrii, zobacz [Weryfikowanie DDoS Detection](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Wyświetlanie zasad łagodzenia DDoS

DDoS Protection standard stosuje trzy zasady ograniczania z autodostrajania (TCP SYN, TCP & UDP) dla każdego publicznego adresu IP chronionego zasobu w sieci wirtualnej, w której włączono DDoS. Progi zasad można wyświetlić, wybierając **przychodzące pakiety TCP, aby wyzwolić DDoS łagodzenie** i **przychodzące pakiety UDP do wyzwalania metryk ograniczenia DDoS** z typem **agregacji** jako "Max", jak pokazano na poniższej ilustracji:

![Wyświetlanie zasad ograniczenia](./media/manage-ddos-protection/view-mitigation-policies.png)

Progi zasad są konfigurowane przy użyciu profilowania sieci opartych na usłudze Azure Machine Learning. Tylko wtedy, gdy próg zasad został naruszony, nastąpi ograniczenie DDoS dla adresu IP w ramach ataku.

## <a name="configure-ddos-attack-analytics"></a>Konfigurowanie analizy ataków DDoS
Azure DDoS Protection Standard zapewnia szczegółowe informacje o atakach i wizualizacje z DDoSą analizą ataków. Klienci chroniący sieci wirtualne przed atakami DDoS mają szczegółowy wgląd w ruch związany z atakami i działania podejmowane w celu ograniczenia ataku za pośrednictwem raportów ograniczenia ataków, które &ją dzienniki przepływów ograniczenia. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurowanie raportów ograniczenia ataków DDoS
Raporty ograniczenia ataków używają danych z protokołu Flow, które są agregowane w celu zapewnienia szczegółowych informacji o ataku na zasób. Gdy zasób publicznego adresu IP jest w trakcie ataku, generowanie raportu rozpocznie się zaraz po rozpoczęciu środków zaradczych. Zostanie wygenerowany raport przyrostowy generowany co 5 minut, a raport po pominięciu dotyczy całego okresu zaradczego. Ma to na celu zapewnienie, że w przypadku gdy atak DDoS będzie kontynuowany przez dłuższy czas, będzie można wyświetlić najbardziej aktualną migawkę raportu o ograniczeniach co 5 minut i pełne podsumowanie po przekroczeniu tego ograniczenia. 

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź wartość *monitor* w polu **Filtr** . Gdy **monitor** pojawi się w wynikach, wybierz go.
3. W obszarze **Ustawienia**wybierz pozycję **Ustawienia diagnostyczne**.
4. Wybierz **subskrypcję** i **grupę zasobów** ZAWIERAJĄCą publiczny adres IP, który chcesz zarejestrować.
5. Wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz rejestrować metryki.
6. Wybierz pozycję **Włącz diagnostykę, aby zebrać dziennik DDoSMitigationReports** , a następnie wybierz dowolną z następujących opcji:

    - **Archiwizuj na koncie magazynu**: Dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Archiwizowanie dzienników diagnostycznych](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Przesyłaj strumieniowo do centrum zdarzeń**: Zezwala odbiorcy dziennika na pobieranie dzienników przy użyciu centrum zdarzeń platformy Azure. Centra zdarzeń umożliwiają integrację z systemem Splunk lub innymi systemami SIEM. Aby dowiedzieć się więcej na temat tej opcji, zobacz [przesyłanie strumieniowe dzienników diagnostycznych do centrum zdarzeń](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Wyślij do log Analytics**: Zapisuje dzienniki w usłudze Azure Monitor. Aby dowiedzieć się więcej na temat tej opcji, zobacz [zbieranie dzienników do użycia w dziennikach Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zarówno przyrostowe, & raporty dotyczące ograniczania ataków po ataku są następujące pola
- Wektory ataków
- Statystyka ruchu
- Przyczyna porzuconych pakietów
- Uwzględnione protokoły
- 10 najważniejszych krajów lub regionów źródłowych
- 10 najważniejszych źródeł WPW

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfiguruj dzienniki przepływu ograniczenia ataków DDoS
Za pomocą dzienników przepływów zaradczych ataków można zapoznać się z porzuconym ruchem, przekazywanym ruchem i innymi interesującymi się punktami danych podczas aktywnego ataku DDoS w czasie niemal rzeczywistym. Możesz pozyskać stałe strumienie tych danych do systemów SIEM za pomocą centrum zdarzeń w celu monitorowania niemal w czasie rzeczywistym, podejmować potencjalne działania i rozwiązywać potrzeby operacji obrony. 

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź wartość *monitor* w polu **Filtr** . Gdy **monitor** pojawi się w wynikach, wybierz go.
3. W obszarze **Ustawienia**wybierz pozycję **Ustawienia diagnostyczne**.
4. Wybierz **subskrypcję** i **grupę zasobów** ZAWIERAJĄCą publiczny adres IP, który chcesz zarejestrować.
5. Wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz rejestrować metryki.
6. Wybierz pozycję **Włącz diagnostykę, aby zebrać dziennik DDoSMitigationFlowLogs** , a następnie wybierz dowolną z następujących opcji:

    - **Archiwizuj na koncie magazynu**: Dane są zapisywane na koncie usługi Azure Storage. Aby dowiedzieć się więcej na temat tej opcji, zobacz [Archiwizowanie dzienników diagnostycznych](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Przesyłaj strumieniowo do centrum zdarzeń**: Zezwala odbiorcy dziennika na pobieranie dzienników przy użyciu centrum zdarzeń platformy Azure. Centra zdarzeń umożliwiają integrację z systemem Splunk lub innymi systemami SIEM. Aby dowiedzieć się więcej na temat tej opcji, zobacz [przesyłanie strumieniowe dzienników diagnostycznych do centrum zdarzeń](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Wyślij do log Analytics**: Zapisuje dzienniki w usłudze Azure Monitor. Aby dowiedzieć się więcej na temat tej opcji, zobacz [zbieranie dzienników do użycia w dziennikach Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Aby wyświetlić dane dzienników przepływu na pulpicie nawigacyjnym usługi Azure Analytics, można zaimportować przykładowego pulpitu nawigacyjnego z https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Dzienniki przepływów będą zawierać następujące pola: 
- Źródłowy adres IP
- Docelowy adres IP
- Port źródłowy 
- Port docelowy 
- Typ protokołu 
- Akcja podjęta podczas łagodzenia



## <a name="validate-ddos-detection"></a>Weryfikowanie wykrywania DDoS

Firma Microsoft połączyła się z [chmurą BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) , aby utworzyć interfejs, w którym można generować ruch dla publicznych adresów IP z włączoną obsługą DDoS Protection na potrzeby symulacji. Symulacja chmury w punkcie przerwania umożliwia:

- Sprawdź, jak Microsoft Azure DDoS Protection chroni zasoby platformy Azure przed atakami DDoS
- Optymalizowanie procesu reagowania na zdarzenia w ramach ataku DDoS
- Zgodność dokumentu DDoS
- Uczenie zespołów zabezpieczeń sieci

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Wyświetlanie alertów dotyczących ochrony DDoS w Azure Security Center

Azure Security Center zawiera listę [alertów zabezpieczeń](/azure/security-center/security-center-managing-and-responding-alerts)z informacjami ułatwiającymi badanie i korygowanie problemów. Dzięki tej funkcji można uzyskać ujednolicony widok alertów, w tym alerty związane z atakiem DDoS oraz działania podejmowane w celu ograniczenia ataków w czasie prawie czasu.
Istnieją dwa konkretne alerty, które będą widoczne w przypadku wykrywania ataków DDoS i łagodzenia rozwiązań:

- **Wykryto atak DDoS dla publicznego adresu IP**: Ten alert jest generowany, gdy Usługa ochrony DDoS wykryje, że jeden z publicznych adresów IP jest celem ataku DDoS.
- **Atak DDoS został skorygowany dla publicznego adresu IP**: Ten alert jest generowany, gdy atak na publiczny adres IP został skorygowany.
Aby wyświetlić alerty, Otwórz **Security Center** w Azure Portal. W obszarze **Ochrona przed zagrożeniami**wybierz pozycję **alerty zabezpieczeń**. Poniższy zrzut ekranu przedstawia przykład alertów ataku DDoS.

![Alert DDoS w Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Alerty obejmują ogólne informacje o publicznym adresie IP, który jest objęty atakiem, informacjami o analizie geograficznym i zagrożeń oraz kroki zaradcze.

## <a name="permissions"></a>Uprawnienia

Aby można było korzystać z planów ochrony DDoS, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie działania wymienione w poniższej tabeli:

| Action                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Odczytaj plan ochrony DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Utwórz lub zaktualizuj plan ochrony DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Usuń plan ochrony DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Dołącz do planu ochrony DDoS              |

Aby włączyć ochronę DDoS dla sieci wirtualnej, konto musi również mieć przypisane odpowiednie [działania dotyczące sieci wirtualnych](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Następne kroki

- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych