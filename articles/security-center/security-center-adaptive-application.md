---
title: "Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center | Microsoft Docs"
description: "Ten dokument ułatwia korzystanie z funkcji adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center w celu tworzenia listy dozwolonych aplikacji uruchamianych na maszynach wirtualnych platformy Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: yurid
ms.openlocfilehash: b1c30b6a0d768f04608c4e46a2e29d20f566964a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center (wersja zapoznawcza)
Z tego przewodnika dowiesz się, jak skonfigurować funkcje sterowania aplikacjami w usłudze Azure Security Center.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Co to są funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center?
Funkcje adaptacyjnego sterowania aplikacjami ułatwiają sterowanie tym, które aplikacje mogą być uruchamiane na maszynach wirtualnych na platformie Azure, co pozwala między innymi wzmocnić ochronę tych maszyn przed złośliwym oprogramowaniem. Usługa Security Center analizuje procesy uruchomione na maszynie wirtualnej przy użyciu uczenia maszynowego i za pomocą tej analizy ułatwia zastosowanie listy reguł elementów dozwolonych. Ta funkcja znacząco upraszcza proces konfigurowania i konserwowania list dozwolonych aplikacji, co pozwala na:

- Blokowanie lub ostrzeganie w przypadku prób uruchomienia złośliwych aplikacji, w tym takich, które w przeciwnym razie mogłyby zostać niezauważone przez rozwiązania chroniące przed złośliwym oprogramowaniem.
- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.
- Unikanie używania w danym środowisku niechcianego oprogramowania.
- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.
- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.
- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

## <a name="how-to-enable-adaptive-application-controls"></a>Jak włączyć funkcje adaptacyjnego sterowania aplikacjami?
Funkcje adaptacyjnego sterowania aplikacjami ułatwiają zdefiniowanie zestawu aplikacji, które mogą być uruchamiane w skonfigurowanych grupach zasobów. Ta funkcja jest dostępna tylko dla maszyn z systemem Windows (we wszystkich wersjach, klasycznych lub usługi Azure Resource Manager). Poniższe kroki umożliwiają skonfigurowanie listy dozwolonych aplikacji w usłudze Security Center:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W okienku po lewej stronie wybierz pozycję **Funkcje adaptacyjnego sterowania aplikacjami** w obszarze **Zaawansowana ochrona w chmurze**.

    ![Ochrona](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

Zostanie wyświetlona strona **Adaptacyjne kontrolki aplikacji**.

![funkcje sterowania](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

Sekcja **Grupy zasobów** zawiera trzy karty:

* **Skonfigurowane**: lista grup zasobów zawierających maszyny wirtualne, dla których skonfigurowano sterowanie aplikacjami.
* **Zalecane**: lista grup zasobów, dla których zaleca się sterowanie aplikacjami. Usługa Security Center przy użyciu uczenia maszynowego identyfikuje maszyny wirtualne odpowiednie do zastosowania funkcji sterowania aplikacjami na podstawie tego, czy na tych maszynach są spójnie uruchamiane te same aplikacje.
* **Brak zaleceń**: lista grup zasobów zawierających maszyny wirtualne bez żadnych zaleceń dotyczących sterowania aplikacjami. Na przykład maszyny wirtualne, na których aplikacje stale się zmieniają i nie osiągają stanu stabilnego.

### <a name="configure-a-new-application-control-policy"></a>Konfigurowanie nowych zasad sterowania aplikacjami
1. Kliknij kartę **Zalecane**, aby uzyskać listę grup zasobów z zaleceniami dotyczącymi sterowania aplikacjami:

  ![Zalecane](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  Lista zawiera:

  - **NAZWA**: nazwa subskrypcji i grupy zasobów
  - **MASZYNY WIRTUALNE**: liczba maszyn wirtualnych w grupie zasobów
  - **STAN**: stan zaleceń (w większości przypadków będzie to stan otwarty)
  - **WAŻNOŚĆ**: poziom ważności zaleceń

2. Wybierz grupę zasobów, aby otworzyć opcję **Utwórz reguły sterowania aplikacjami**.

  ![Reguły sterowania aplikacjami](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. W obszarze **Wybierz maszyny wirtualne** zapoznaj się z listą zalecanych maszyn wirtualnych i wyczyść zaznaczenie pól wyboru obok tych, do których nie ma być stosowane sterowanie aplikacjami. W obszarze **Wybierz procesy dla reguł umieszczania na białej liście** zapoznaj się z listą zalecanych aplikacji i wyczyść zaznaczenie pól wyboru obok tych, do których nie ma być stosowana ta funkcja. Lista zawiera:

  - **NAZWA**: pełna ścieżka aplikacji
  - **PROCESY**: ile aplikacji znajduje się w każdej ścieżce
  - **WSPÓLNE**: wartość „tak” wskazuje, że te procesy są wykonywane na większości maszyn wirtualnych w tej grupie zasobów.
  - **MOŻLIWE DO WYKORZYSTANIA**: ikona ostrzeżenia wskazuje, czy osoba atakująca może wykorzystać aplikacje do pominięcia listy dozwolonych aplikacji. Zaleca się dokonanie przeglądu tych aplikacji przed ich zatwierdzeniem.
  - **UŻYTKOWNICY**: użytkownicy uprawnieni do uruchamiania aplikacji.

4. Po wybraniu opcji wybierz przycisk **Utwórz**.

Domyślnie usługa Security Center zawsze uruchamia sterowanie aplikacjami w trybie *inspekcji*. Po upewnieniu się, że lista dozwolonych nie wpływa niekorzystnie na używane obciążenia, można zmienić tryb na tryb *wymuszania*.

Usługa Security Center używa danych z co najmniej dwóch tygodni w celu utworzenia planu bazowego i przygotowania unikatowych zaleceń dla grup maszyn wirtualnych. Nowi klienci usługi Security Center w warstwie Standardowa powinni spodziewać się, że najpierw ich grupy maszyn wirtualnych pojawią się na karcie *Brak zaleceń*.

> [!NOTE]
> Zgodnie z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń usługa Security Center zawsze próbuje utworzyć regułę wydawcy dla aplikacji, która powinna znaleźć się na liście dozwolonych. Jedynie w przypadku aplikacji bez informacji o wydawcy (bez podpisu) zostanie utworzona reguła ścieżki dla pełnej ścieżki konkretnego pliku EXE.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Edytowanie i monitorowanie grupy, dla której skonfigurowano sterowanie aplikacjami

1. Aby edytować i monitorować grupę, w której skonfigurowano kontrolę aplikacji, wróć do strony **Funkcje adaptacyjnego sterowania aplikacjami** i wybierz pozycję **SKONFIGUROWANE** w obszarze **Grupy zasobów**:

  ![Grupy zasobów](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  Lista zawiera:

  - **NAZWA**: nazwa subskrypcji i grupy zasobów
  - **MASZYNY WIRTUALNE**: liczba maszyn wirtualnych w grupie zasobów
  - **TRYB**: w trybie inspekcji będą rejestrowane próby uruchomienia aplikacji spoza listy dozwolonych, a w trybie blokowania uruchamianie takich aplikacji będzie blokowane
  - **PROBLEMY**: wszystkie bieżące naruszenia zasad

2. Wybierz grupę zasobów, aby wprowadzić zmiany na stronie **Edytuj zasady sterowania aplikacjami**.

  ![Ochrona](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. W obszarze **Tryb ochrony** do wyboru są następujące opcje:

  - **Inspekcja**: w tym trybie rozwiązanie sterowania aplikacjami nie będzie wymuszać reguł, a jedynie prowadzić inspekcję działań na chronionych maszynach wirtualnych. Jest to zalecane w scenariuszach, w których trzeba najpierw przyjrzeć się ogólnym zachowaniom, zanim zablokuje się uruchamianie aplikacji na docelowej maszynie wirtualnej.
  - **Wymuszanie**: w tym trybie rozwiązanie sterowania aplikacjami będzie wymuszać reguły, a aplikacje, które nie powinny być uruchamiane, będą blokowane.

  Jak wcześniej wspomniano, domyślnie nowe zasady sterowania aplikacjami są zawsze skonfigurowane w trybie *inspekcji*. W obszarze **Rozszerzenie zasad** można dodać własne ścieżki aplikacji, które mają znaleźć się na liście dozwolonych. Po dodaniu tych ścieżek usługa Security Center utworzy odpowiednie reguły dla tych aplikacji i doda je do już wdrożonych reguł.

  W sekcji **Ostatnie problemy** są wyświetlane wszelkie bieżące naruszenia.

  ![Problemy](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  Ta lista zawiera:
  - **PROBLEMY**: wszelkie zarejestrowane naruszenia. Mogą to być:

      - **Naruszenia zablokowane (ViolationsBlocked)**: gdy rozwiązanie jest w trybie wymuszania i próbowano uruchomić aplikację spoza listy dozwolonych.
      - **Naruszenia poddane inspekcji (ViolationsAudited)**: gdy rozwiązanie jest w trybie inspekcji i uruchomiono aplikację spoza listy dozwolonych.
      - **Ręczne naruszenie reguł (RulesViolatedManually)**: gdy użytkownik próbował ręcznie skonfigurować reguły na maszynach wirtualnych, a nie za pomocą portalu zarządzania ASC.

 - **LICZBA MASZYN WIRTUALNYCH**: liczba maszyn wirtualnych z tym typem problemu.

  Kliknięcie dowolnego z tych wierszy spowoduje przekierowanie do strony [dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), na której można sprawdzić informacje na temat wszystkich maszyn wirtualnych z naruszeniem tego typu. Kliknięcie trzech kropek znajdujących się na końcu wiersza pozwala usunąć dany wpis. Sekcja **Skonfigurowane maszyny wirtualne** zawiera informacje o maszynach wirtualnych, których dotyczą te reguły.

  ![Skonfigurowane maszyny wirtualne](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  Lista wyświetlana w obszarze **Reguły umieszczania na białej liście na podstawie dostawcy** zawiera następujące pozycje:

  - **REGUŁA**: lista aplikacji, dla których utworzono reguły wydawcy na podstawie informacji z certyfikatów znalezionych dla poszczególnych aplikacji
  - **UŻYTKOWNICY**: liczba użytkowników uprawnionych do uruchamiania poszczególnych aplikacji

  Aby uzyskać więcej informacji, zobacz [Opis reguł wydawców w funkcji Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker).

  ![Reguły umieszczania na liście dozwolonych](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  Kliknięcie trzech kropek znajdujących się na końcu wiersza pozwala usunąć daną regułę lub edytować listę uprawnionych użytkowników.

  Sekcja **Reguły umieszczania na białej liście na podstawie ścieżki** zawiera całą ścieżkę aplikacji (obejmującą plik wykonywalny) dla aplikacji, które nie są podpisane za pomocą certyfikatu cyfrowego, ale są obecne w regułach umieszczania na liście dozwolonych.

  > [!NOTE]
  > Domyślnie, zgodnie z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń, usługa Security Center zawsze próbuje utworzyć regułę wydawcy dla pliku EXE, który powinien znaleźć się na liście dozwolonych. Jedynie w przypadku pliku EXE bez informacji o wydawcy (bez podpisu) zostanie utworzona reguła ścieżki dla pełnej ścieżki konkretnego pliku EXE.

  ![Reguły umieszczania na białej liście na podstawie ścieżki](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  Lista zawiera:
  - **NAZWA**: pełna ścieżka pliku wykonywalnego
  - **UŻYTKOWNICY**: liczba użytkowników uprawnionych do uruchamiania poszczególnych aplikacji

  Kliknięcie trzech kropek znajdujących się na końcu wiersza pozwala usunąć daną regułę lub edytować listę uprawnionych użytkowników.

4. Po wprowadzeniu zmian na stronie **Funkcje adaptacyjnego sterowania aplikacjami** kliknij przycisk **Zapisz**. Jeśli nie chcesz stosować zmian, kliknij przycisk **Odrzuć**.

### <a name="not-recommended-list"></a>Lista bez zaleceń

Usługa Security Center zaleca dodanie aplikacji do listy dozwolonych tylko w przypadku maszyn wirtualnych, na których uruchamiane są stabilne zestawy aplikacji. Zalecenia nie są tworzone w przypadku ciągłych zmian aplikacji na skojarzonych maszynach wirtualnych.

![Zalecenie](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Lista zawiera:
- **NAZWA**: nazwa subskrypcji i grupy zasobów
- **MASZYNY WIRTUALNE**: liczba maszyn wirtualnych w grupie zasobów

## <a name="next-steps"></a>Następne kroki
W tym dokumencie omówiono korzystanie z funkcji adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center w celu tworzenia listy dozwolonych aplikacji uruchamianych na maszynach wirtualnych platformy Azure. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
