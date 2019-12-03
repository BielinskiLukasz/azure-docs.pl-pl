---
title: Zasady pomocy technicznej dla usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat zasad pomocy technicznej usługi Azure Kubernetes Service (AKS), współdzielonej odpowiedzialności oraz funkcji dostępnych w wersji zapoznawczej (lub Alpha lub beta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: c018e511bbeed41bc9caf721562349a37ad0e748
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707220"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Zasady pomocy technicznej dla usługi Azure Kubernetes Service

Ten artykuł zawiera szczegółowe informacje na temat zasad i ograniczeń pomocy technicznej dla usługi Azure Kubernetes Service (AKS). W tym artykule opisano również szczegóły zarządzania węzłami procesu roboczego, zarządzane składniki płaszczyzny kontroli, składniki Open-Source innych firm oraz zarządzanie zabezpieczeniami i poprawkami.

## <a name="service-updates-and-releases"></a>Aktualizacje i wydania usługi

* Aby uzyskać informacje o wersji, zobacz [AKS informacji o wersji](https://github.com/Azure/AKS/releases).
* Aby uzyskać informacje na temat funkcji dostępnych w wersji zapoznawczej, zobacz [AKS Preview Features and pokrewne projekty](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Funkcje zarządzane w programie AKS

Podstawowe składniki infrastruktury jako usługi (IaaS), takie jak składniki obliczeniowe lub sieciowe, umożliwiają użytkownikom dostęp do formantów niskiego poziomu i opcji dostosowywania. Z kolei AKS udostępnia wdrożenie gotowe Kubernetes, które zapewnia klientom wspólny zestaw konfiguracji i możliwości, których potrzebują. Klienci AKS mają ograniczoną możliwość dostosowywania, wdrażania i innych opcji. Ci klienci nie muszą bezpośrednio martwić się o klastry Kubernetes ani zarządzać nimi.

W przypadku AKS klient otrzymuje w pełni zarządzaną *płaszczyznę kontroli*. Płaszczyzna kontroli zawiera wszystkie składniki i usługi, które klient musi obsługiwać i dostarcza klastry Kubernetes użytkownikom końcowym. Wszystkie składniki Kubernetes są utrzymywane i obsługiwane przez firmę Microsoft.

Firma Microsoft zarządza następującymi składnikami i monitoruje je za pomocą okienka sterowania:

* Serwery interfejsu API Kubelet lub Kubernetes
* Etcd lub zgodny magazyn wartości klucz-wartość, zapewniający Quality of Service (QoS), skalowalność i środowisko uruchomieniowe
* Usługi DNS (na przykład polecenia-DNS lub CoreDNS)
* Kubernetes serwer proxy lub sieci

AKS nie jest rozwiązaniem w pełni zarządzanym klastrem. Niektóre składniki, takie jak węzły procesu roboczego, mają *wspólną odpowiedzialność*, w której użytkownicy muszą pomóc w utrzymaniu klastra AKS. Dane wejściowe użytkownika są wymagane na przykład w celu zastosowania poprawki zabezpieczeń systemu operacyjnego węzła procesu roboczego.

Usługi są *zarządzane* w sensie, że firma Microsoft i zespół AKS wdrażają, działają i są odpowiedzialne za dostępność i funkcjonalność usługi. Klienci nie mogą zmieniać tych składników zarządzanych. Firma Microsoft ogranicza dostosowanie w celu zapewnienia spójnego i skalowalnego środowiska użytkownika. Aby uzyskać w pełni dostosowywalne rozwiązanie, zobacz [aparat AKS](https://github.com/Azure/aks-engine).

> [!NOTE]
> Węzły procesu roboczego AKS są wyświetlane w Azure Portal jako zwykłe zasoby IaaS platformy Azure. Jednak te maszyny wirtualne są wdrażane w niestandardowej grupie zasobów platformy Azure (z prefiksem MC\\*). Istnieje możliwość zmiany węzłów procesu roboczego AKS. Na przykład można użyć Secure Shell (SSH), aby zmienić AKS węzły procesu roboczego w sposób zmieniania normalnych maszyn wirtualnych (nie można jednak zmienić podstawowego obrazu systemu operacyjnego, a zmiany mogą nie zostać zachowane przez aktualizację lub ponowny rozruch), a także dołączyć inne zasoby platformy Azure do usługi AKS węzły procesu roboczego. Jednak po wprowadzeniu zmian w *zarządzaniu poza pasmem i dostosowaniu* klaster AKS może stać się nieobsługiwany. Należy unikać zmiany węzłów procesu roboczego, chyba że pomoc techniczna firmy Microsoft nie kieruje się do wprowadzania zmian.

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Po utworzeniu klastra Klient definiuje Kubernetes węzły procesu roboczego, które tworzy AKS. Obciążenia klientów są wykonywane w tych węzłach. Klienci i mogą wyświetlać lub modyfikować węzły procesu roboczego.

Ponieważ węzły klastra klienta wykonują kod prywatny i przechowują dane poufne, pomoc techniczna firmy Microsoft mogą uzyskać do nich dostęp tylko w ograniczony sposób. Pomoc techniczna firmy Microsoft nie może zalogować się do programu, wykonywać poleceń w programie lub wyświetlić dzienników dla tych węzłów bez wyraźnej zgody lub pomocy technicznej.

Ze względu na to, że węzły procesu roboczego są poufne, firma Microsoft bardzo dobrze jest ograniczyć zarządzanie w tle. W wielu przypadkach obciążenie będzie nadal działać, nawet jeśli Kubernetes główne węzły, etcd i inne składniki zarządzane przez firmę Microsoft zakończą się niepowodzeniem. Carelessly zmodyfikowane węzły procesu roboczego mogą spowodować utratę danych i obciążeń i może renderować klaster nieobsługiwany.

## <a name="aks-support-coverage"></a>AKS pomocy technicznej

Firma Microsoft zapewnia pomoc techniczną dla następujących:

* Łączność ze wszystkimi składnikami Kubernetes obsługiwanymi przez usługę Kubernetes, takimi jak serwer interfejsu API.
* Zarządzanie, czas pracy, QoS i operacje usług Kubernetes kontroli płaszczyzny (na przykład węzły główne Kubernetes, serwery API, etcd i polecenia-DNS).
* Etcd. Obsługa obejmuje automatyczne i przezroczyste kopie zapasowe wszystkich danych etcd co 30 minut w przypadku planowania awarii i przywracania stanu klastra. Te kopie zapasowe nie są bezpośrednio dostępne dla klientów lub użytkowników. Zapewniają one niezawodność i spójność danych.
* Wszystkie punkty integracji w sterowniku dostawcy chmury platformy Azure dla Kubernetes. Obejmują one integrację z innymi usługami platformy Azure, takimi jak moduły równoważenia obciążenia, woluminy trwałe lub sieci (Kubernetes i Azure CNI).
* Pytania lub problemy związane z dostosowywaniem składników płaszczyzny kontroli, takich jak Kubernetes API Server, etcd i polecenia-DNS.
* Problemy dotyczące sieci, takie jak Azure CNI, korzystającą wtyczki kubenet lub inne problemy z dostępem do sieci i funkcjami. Problemy mogą obejmować rozpoznawanie nazw DNS, utratę pakietów, Routing i tak dalej. Firma Microsoft obsługuje różne scenariusze sieci:
  * Korzystającą wtyczki kubenet (podstawowa) i zaawansowane sieci (Azure CNI) w klastrze i skojarzonych składnikach
  * Łączność z innymi usługami i aplikacjami platformy Azure
  * Konfiguracje usług przychodzących i przychodzących lub modułów równoważenia obciążenia
  * Wydajność i opóźnienie sieci

Firma Microsoft nie zapewnia pomocy technicznej w zakresie następujących czynności:

* Pytania dotyczące korzystania z programu Kubernetes. Na przykład pomoc techniczna firmy Microsoft nie zawiera wskazówek na temat tworzenia niestandardowych kontrolerów transferu danych przychodzących, korzystania z obciążeń aplikacji lub pakietów oprogramowania lub narzędzi innych firm lub Open Source.
  > [!NOTE]
  > Pomoc techniczna firmy Microsoft może zalecić działanie klastra AKS, dostosowanie i dostrajanie (na przykład problemy z operacjami Kubernetes i procedurami).
* Projekty typu "open source" innych firm, które nie są dostarczane jako część płaszczyzny kontroli Kubernetes ani wdrożone z klastrami AKS. Te projekty mogą obejmować Istio, Helm, wysłannika lub inne.
  > [!NOTE]
  > Firma Microsoft może zapewnić najlepszą pomoc techniczną dla projektów typu "open source" innych firm, takich jak Helm i Kured. Jeśli narzędzie Open-Source innej firmy integruje się z dostawcą usług w chmurze Kubernetes systemu Azure lub innymi błędami specyficznymi dla AKS, firma Microsoft obsługuje przykłady i aplikacje z dokumentacji firmy Microsoft.
* Oprogramowanie dotyczące oprogramowania zamkniętego innej firmy. To oprogramowanie może obejmować narzędzia do skanowania zabezpieczeń i urządzenia sieciowe lub oprogramowanie.
* Problemy związane z kompilacjami wielochmurowymi lub wielodostawcami. Na przykład firma Microsoft nie obsługuje problemów związanych z uruchamianiem federacyjnego rozwiązania dostawcy chmury z chmurą publiczną.
* Dostosowania sieci inne niż wymienione w [dokumentacji AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Firma Microsoft obsługuje problemy i błędy związane z grupami zabezpieczeń sieci (sieciowych grup zabezpieczeń). Na przykład pomoc techniczna firmy Microsoft może odpowiedzieć na pytania dotyczące błędu sieciowej grupy zabezpieczeń lub nieoczekiwane zachowanie usługi sieciowej grupy zabezpieczeń lub równoważenia obciążenia.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS obsługi dla węzłów procesu roboczego

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Obowiązki firmy Microsoft dla węzłów procesu roboczego AKS

Firma Microsoft i klienci mogą korzystać z odpowiedzialności za węzły procesu roboczego Kubernetes, gdzie:

* Podstawowy obraz systemu operacyjnego ma wymagane Dodatki (na przykład monitorowanie i agenci sieci).
* Węzły procesu roboczego automatycznie otrzymują poprawki systemu operacyjnego.
* Problemy ze składnikami płaszczyzny kontrolnej Kubernetes, które działają w węzłach procesu roboczego, są automatycznie korygowane. Dostępne są następujące składniki:
  * Polecenia — proxy
  * Tunele sieciowe dostarczające ścieżki komunikacji do składników głównych Kubernetes
  * Kubelet
  * Demon Docker lub Moby

> [!NOTE]
> Jeśli składnik płaszczyzny kontroli nie działa w węźle procesu roboczego, zespół AKS może potrzebować ponownego uruchomienia poszczególnych składników lub całego węzła procesu roboczego. Te operacje ponownego uruchamiania są zautomatyzowane i zapewniają automatyczne korygowanie typowych problemów. Te ponownych uruchomień odbywają się tylko na poziomie _węzła_ , a nie w klastrze, o ile nie istnieje awaryjna konserwacja lub przestoje.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Obowiązki klienta dotyczące węzłów procesu roboczego AKS

Firma Microsoft nie automatycznie ponownie uruchomi węzły procesu roboczego w celu zastosowania poprawek na poziomie systemu operacyjnego. Mimo że poprawki systemu operacyjnego są dostarczane do węzłów procesu roboczego, *Klient* jest odpowiedzialny za ponowne uruchomienie węzłów procesu roboczego w celu zastosowania zmian. Biblioteki udostępnione, demony, takie jak dysk hybrydowy SSD (SSHD) i inne składniki na poziomie systemu lub systemu operacyjnego są automatycznie poprawiane.

Klienci są odpowiedzialni za wykonywanie uaktualnień Kubernetes. Umożliwiają one wykonywanie uaktualnień za pomocą panelu sterowania platformy Azure lub interfejsu wiersza polecenia platformy Azure. Dotyczy to aktualizacji, które zawierają udoskonalenia zabezpieczeń lub funkcjonalności do Kubernetes.

> [!NOTE]
> Ponieważ AKS jest *usługą zarządzaną*, jej cele końcowe obejmują usunięcie odpowiedzialności za poprawki, aktualizacje i zbieranie dzienników, aby zapewnić pełniejsze i bezobsługowe zarządzanie usługami. W miarę wzrostu pojemności usługi w celu kompleksowego zarządzania przyszłe wersje mogą pominąć niektóre funkcje (na przykład ponowne uruchomienie węzła i automatyczne stosowanie poprawek).

### <a name="security-issues-and-patching"></a>Problemy z zabezpieczeniami i stosowanie poprawek

W przypadku znalezienia usterki dotyczącej zabezpieczeń w jednym lub większej liczbie składników programu AKS zespół AKS będzie mógł zastosować poprawki wszystkich klastrów, których dotyczy problem. Alternatywnie zespół zapewni użytkownikom wskazówki dotyczące uaktualniania.

W przypadku węzłów procesu roboczego, których dotyczy luka w zabezpieczeniach, jeśli jest dostępna poprawka bez przestojów, zespół AKS będzie stosował tę poprawkę i powiadomiać użytkowników o zmianie.

Gdy poprawka zabezpieczeń wymaga ponownego uruchomienia węzła procesu roboczego, firma Microsoft powiadomi Klienta o tym wymaganiu. Klient jest odpowiedzialny za ponowne uruchomienie lub aktualizację w celu pobrania poprawki klastra. Jeśli użytkownicy nie zastosują poprawek zgodnie ze wskazówkami AKS, ich klaster będzie nadal narażony na problem z zabezpieczeniami.

### <a name="node-maintenance-and-access"></a>Konserwacja i dostęp do węzła

Węzły procesu roboczego są wspólną odpowiedzialnością i należą do klientów. W związku z tym klienci mogą zalogować się do swoich węzłów procesów roboczych i wprowadzać potencjalnie szkodliwe zmiany, takie jak aktualizacje jądra i Instalowanie lub usuwanie pakietów.

Jeśli klienci wprowadzają destrukcyjne zmiany lub spowodują, że składniki płaszczyzny kontroli przechodzą w tryb offline lub staną się niefunkcjonalne, AKS wykryje ten błąd i automatycznie przywróci węzeł procesu roboczego do poprzedniego stanu roboczego.

Mimo że klienci mogą zalogować się do i zmieniać węzły procesu roboczego, nie jest to zalecane, ponieważ zmiany mogą sprawić, że klaster jest nieobsługiwany.

## <a name="network-ports-access-and-nsgs"></a>Porty sieciowe, dostęp i sieciowych grup zabezpieczeń

Jako usługa zarządzana AKS ma określone wymagania dotyczące sieci i łączności. Te wymagania są mniej elastyczne niż wymagania dotyczące normalnych składników IaaS. W AKS, operacje takie jak Dostosowywanie reguł sieciowej grupy zabezpieczeń, blokowanie określonego portu (na przykład przy użyciu reguł zapory blokujących port wychodzący 443), a adresy URL listy dozwolonych mogą sprawić, że klaster jest nieobsługiwany.

> [!NOTE]
> Obecnie AKS nie pozwala na całkowite zablokowanie ruchu wyjściowego z klastra. Aby kontrolować listę adresów URL i portów, które mogą być używane przez klaster dla ruchu wychodzącego, zobacz [ograniczanie ruchu ruchowego](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nieobsługiwane funkcje Kubernetes Alpha i beta

AKS obsługuje tylko stabilne funkcje w projekcie Kubernetes nadrzędnego. O ile nie określono inaczej, AKS nie obsługuje funkcji alfa i beta, które są dostępne w nadrzędnym projekcie Kubernetes.

W dwóch scenariuszach funkcje Alpha lub beta mogą zostać wycofane, zanim staną się ogólnie dostępne:

* Klienci spełnili AKS produkt, pomoc techniczną lub zespoły inżynieryjne i zostali zaproszeni o wypróbowanie tych nowych funkcji.
* Te funkcje zostały [włączone przez flagę funkcji](https://github.com/Azure/AKS/blob/master/previews.md). Klienci muszą jawnie korzystać z tych funkcji.

## <a name="preview-features-or-feature-flags"></a>Funkcje wersji zapoznawczej lub flagi funkcji

Aby uzyskać funkcje i funkcje, które wymagają rozszerzonego testowania i opinii użytkowników, firma Microsoft udostępnia nowe funkcje lub funkcje w wersji zapoznawczej za flagą funkcji. Te funkcje należy traktować jako funkcje wersji wstępnej lub wersji beta.

Funkcje w wersji zapoznawczej lub funkcje flagi funkcji nie są przeznaczone do produkcji. Bieżące zmiany w interfejsach API i zachowania, poprawki błędów i inne zmiany mogą spowodować niestabilne klastry i przestoje.

Funkcje w publicznej wersji zapoznawczej są objęte wsparciem "najlepsze wysiłki", ponieważ te funkcje są w wersji zapoznawczej i nie są przeznaczone do produkcji i są obsługiwane przez zespoły pomocy technicznej AKS w godzinach pracy. Aby uzyskać dodatkowe informacje, zobacz:

* [Pomoc techniczna platformy Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Funkcje w wersji zapoznawczej działają na poziomie *subskrypcji* platformy Azure. Nie instaluj funkcji wersji zapoznawczej w ramach subskrypcji produkcyjnej. W ramach subskrypcji produkcyjnej funkcje w wersji zapoznawczej mogą zmieniać domyślne zachowanie interfejsu API i wpływać na regularne operacje.

## <a name="upstream-bugs-and-issues"></a>Błędy i problemy z usługą nadrzędnego

Mając na względnie prędkość rozwoju w projekcie Kubernetes, usterki niezmiennie powstają. Niektóre z tych usterek nie mogą zostać poprawione lub nie działały w systemie AKS. Zamiast tego poprawki błędów wymagają większych poprawek do projektów nadrzędnych (takich jak Kubernetes, systemy operacyjne węzłów lub procesów roboczych oraz jądra). W przypadku składników należących do firmy Microsoft (takich jak dostawca chmury platformy Azure) AKS i personel platformy Azure są zaangażowane w sposób rozwiązywania problemów w społeczności.

Gdy problem z pomocą techniczną jest katalogiem głównym spowodowanym przez jedną lub więcej błędów nadrzędnych, zespoły pomocy technicznej AKS i inżynierów inżynieryjnych będą:

* Zidentyfikuj i Połącz błędy nadrzędnego z dowolnymi szczegółami pomocniczymi, aby wyjaśnić, dlaczego ten problem wpływa na klaster lub obciążenie. Klienci odbierają linki do wymaganych repozytoriów, aby mogli oglądać problemy i zobaczyć, kiedy nowe wydanie udostępni poprawki.
* Zapewnij potencjalne obejścia lub środki zaradcze. Jeśli problem może zostać skorygowany, w repozytorium AKS zostanie zgłoszony [znany problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) . Informacje o znanych problemach są następujące:
  * Problem, w tym linki do błędów nadrzędnych.
  * Obejście i szczegółowe informacje o uaktualnieniu lub innej trwałości rozwiązania.
  * Przybliżone osie czasu na potrzeby dołączenia problemu w oparciu o wersję erze.
