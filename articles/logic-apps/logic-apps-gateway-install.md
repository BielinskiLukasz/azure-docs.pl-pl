---
title: Zainstaluj lokalną bramę danych — Azure Logic Apps
description: Aby można było uzyskać dostęp do danych lokalnych z Azure Logic Apps, Pobierz i zainstaluj lokalną bramę danych.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/18/2019
ms.openlocfilehash: d515982dc2937b660de75ee723cebe39b33bec7d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580866"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Zainstaluj lokalną bramę danych dla Azure Logic Apps

Przed [nawiązaniem połączenia z lokalnymi źródłami danych z Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md)Pobierz i zainstaluj lokalną [bramę danych](https://aka.ms/on-premises-data-gateway-installer) na komputerze lokalnym. Brama działa jako most, który zapewnia szybkie przesyłanie i szyfrowanie danych między źródłami danych w środowisku lokalnym i aplikacjami logiki. Możesz użyć tej samej instalacji bramy z innymi usługami w chmurze, takich jak Power BI, Automatyzacja, aplikacje i Azure Analysis Services. Aby uzyskać informacje o sposobach korzystania z bramy za pomocą tych usług, zobacz następujące artykuły:

* [Lokalna Brama danych Power BI firmy Microsoft](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Lokalna Brama danych firmy Microsoft dla aplikacji zaawansowanych](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Automatyzacja lokalnej bramy danych](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services lokalnej bramy danych](../analysis-services/analysis-services-gateway.md)

W tym artykule pokazano, jak pobrać, zainstalować i skonfigurować lokalną bramę danych, aby można było uzyskać dostęp do lokalnych źródeł danych z Azure Logic Apps. Możesz również dowiedzieć się więcej o tym [, jak Brama danych działa](#gateway-cloud-service) w dalszej części tego tematu. Aby uzyskać więcej informacji na temat bramy, zobacz [co to jest brama lokalna](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)?

<a name="requirements"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure z subskrypcją, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

  * Aby zainstalować bramę na komputerze lokalnym i zarządzać nią, należy użyć tego samego konta platformy Azure.

    Podczas instalacji bramy możesz zalogować się przy użyciu konta platformy Azure, które łączy instalację bramy z kontem platformy Azure i tylko z tym kontem. Później w Azure Portal należy użyć tego samego konta platformy Azure do utworzenia zasobu bramy platformy Azure, który rejestruje i zgłasza instalację bramy. W Azure Logic Apps lokalne wyzwalacze i akcje używają zasobu bramy do łączenia się z lokalnymi źródłami danych.

    > [!NOTE]
    > Do siebie można połączyć tylko jedną instalację bramy i jeden zasób bramy platformy Azure. Nie można połączyć tej samej instalacji bramy z wieloma kontami platformy Azure lub zasobami bramy platformy Azure. Konto platformy Azure może jednak łączyć się z wieloma instalacjami bramy i zasobami bramy platformy Azure. W lokalnym wyzwalaczu lub akcji możesz wybrać spośród różnych subskrypcji platformy Azure, a następnie wybrać skojarzony zasób bramy.

  * Musisz zalogować się przy użyciu konta służbowego, znanego również jako konto *organizacji* , które wygląda jak `username@contoso.com`. Nie można używać kont B2B (gość) platformy Azure ani osobistych kont Microsoft, takich jak @hotmail.com lub @outlook.com.

    > [!TIP]
    > Jeśli zarejestrowano się w celu uzyskania oferty pakietu Office 365 i nie podano służbowego adresu e-mail, adres może wyglądać jak `username@domain.onmicrosoft.com`. Twoje konto jest przechowywane w dzierżawie w Azure Active Directory (Azure AD). W większości przypadków główna nazwa użytkownika (UPN) dla konta usługi Azure AD jest taka sama jak w przypadku Twojego adresu e-mail.
    >
    > Aby użyć [standardowej subskrypcji programu Visual Studio](https://visualstudio.microsoft.com/vs/pricing/) , która jest połączona z konto Microsoft, należy najpierw [utworzyć dzierżawę w usłudze Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) lub użyć domyślnego katalogu. Dodaj użytkownika z hasłem do katalogu, a następnie nadaj temu użytkownikowi dostęp do subskrypcji platformy Azure. Następnie możesz zalogować się podczas instalacji bramy przy użyciu tej nazwy użytkownika i hasła.

* Poniżej przedstawiono wymagania dotyczące komputera lokalnego:

  **Minimalne wymagania**

  * .NET Framework 4.7.2
  * 64 — bitowa wersja systemu Windows 7 lub Windows Server 2008 R2 (lub nowszego)

  **Zalecane wymagania**

  * 8-rdzeniowy procesor CPU
  * 8 GB pamięci
  * 64 — bitowa wersja systemu Windows Server 2012 R2 lub nowszego
  * Magazyn dysków półprzewodnikowych (SSD) do buforowania

  > [!NOTE]
  > Brama nie obsługuje systemu Windows Server Core.

* **Powiązane zagadnienia**

  * Bramę danych lokalnych można zainstalować tylko na komputerze lokalnym, a nie na kontrolerze domeny. Nie trzeba jednak instalować bramy na tym samym komputerze, na którym znajduje się źródło danych. Wymagana jest tylko jedna brama dla wszystkich źródeł danych, więc nie trzeba instalować bramy dla każdego źródła danych.

    > [!TIP]
    > Aby zminimalizować opóźnienie, można zainstalować bramę jak najbliżej źródła danych lub na tym samym komputerze, przy założeniu, że masz uprawnienia.

  * Zainstaluj bramę na komputerze, który znajduje się w sieci przewodowej, połączony z Internetem, zawsze włączone i przejdź do trybu uśpienia. W przeciwnym razie nie można uruchomić bramy, a wydajność sieci bezprzewodowej może być niedostępna.

  * Jeśli planujesz używanie uwierzytelniania systemu Windows, upewnij się, że brama jest zainstalowana na komputerze, który jest członkiem tego samego środowiska Active Directory, co źródła danych.

  * Region wybrany dla instalacji bramy jest tą samą lokalizacją, którą należy wybrać podczas późniejszego tworzenia zasobu bramy platformy Azure dla aplikacji logiki. Domyślnie ten region jest taka sama jak lokalizacja dzierżawy usługi Azure AD, która zarządza Twoim kontem platformy Azure. Można jednak zmienić lokalizację podczas instalacji bramy.

  * Brama ma dwa tryby: tryb standardowy i tryb osobisty, który ma zastosowanie tylko do Power BI. Na tym samym komputerze nie może znajdować się więcej niż jedna brama uruchomiona w tym samym trybie.

  * Azure Logic Apps obsługuje operacje odczytu i zapisu za pomocą bramy. Jednak te operacje mają [limity rozmiaru ładunku](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalowanie bramy danych

1. [Pobierz i uruchom Instalatora bramy na komputerze lokalnym](https://aka.ms/on-premises-data-gateway-installer).

1. Po otwarciu Instalatora wybierz pozycję **dalej**.

   ![Ekran Wprowadzenie dla Instalatora bramy](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Wybierz pozycję **lokalna Brama danych (zalecane)** , która jest trybem standardowym, a następnie wybierz przycisk **dalej**.

   ![Wybierz tryb uruchamiania dla bramy danych](./media/logic-apps-gateway-install/select-gateway-running-mode.png)

1. Zapoznaj się z minimalnymi wymaganiami, Zachowaj domyślną ścieżkę instalacji, zaakceptuj warunki użytkowania, a następnie wybierz pozycję **Zainstaluj**.

   ![Przejrzyj wymagania i zaakceptuj warunki użytkowania](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Po pomyślnym zainstalowaniu bramy Podaj adres e-mail swojego konta platformy Azure, a następnie wybierz pozycję **Zaloguj się**, na przykład:

   ![Zaloguj się przy użyciu konta służbowego](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Instalacja bramy może być połączona tylko z jednym kontem platformy Azure.

1. Wybierz pozycję **zarejestruj nową bramę na tym komputerze** , > **dalej**. Ten krok rejestruje instalację bramy w [usłudze bramy w chmurze](#gateway-cloud-service).

   ![Zarejestruj bramę na komputerze lokalnym](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Podaj te informacje na potrzeby instalacji bramy:

   * Nazwa bramy, która jest unikatowa w ramach dzierżawy usługi Azure AD
   * Klucz odzyskiwania, który musi mieć co najmniej osiem znaków, które mają być używane.
   * Potwierdzenie klucza odzyskiwania

   ![Podaj informacje dotyczące instalacji bramy](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Zapisz i Zachowaj klucz odzyskiwania w bezpiecznym miejscu. Ten klucz będzie potrzebny, jeśli kiedykolwiek chcesz zmienić lokalizację, przenieść, odzyskać lub przejmowanie instalacji bramy.

   Zwróć uwagę na to, że można **dodać do istniejącego klastra bramy**, który wybierasz podczas instalacji dodatkowych bram w [scenariuszach wysokiej dostępności](#high-availability).

1. Sprawdź region usługi bramy w chmurze i [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) używany przez instalację bramy. Domyślnie ten region jest taka sama jak lokalizacja dzierżawy usługi Azure AD dla Twojego konta platformy Azure.

   ![Potwierdź region usługi bramy i usługi Service Bus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Aby zaakceptować domyślny region, wybierz pozycję **Konfiguruj**. Jeśli jednak domyślny region nie jest tym, który znajduje się najbliżej Ciebie, możesz zmienić region.

   *Dlaczego należy zmienić region instalacji bramy?*

   Na przykład w celu zmniejszenia opóźnień można zmienić region bramy na ten sam region, w którym znajduje się aplikacja logiki. Możesz też wybrać region znajdujący się najbliżej lokalnego źródła danych. *Zasób bramy na platformie Azure* i aplikacja logiki mogą mieć różne lokalizacje.

   1. Obok bieżącego regionu wybierz pozycję **Zmień region**.

      ![Zmień bieżący region bramy](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na następnej stronie Otwórz listę **Wybierz region** , wybierz żądany region, a następnie wybierz pozycję **gotowe**.

      ![Wybierz inny region dla usługi bramy](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Przejrzyj informacje w oknie potwierdzenie końcowe. Ten przykład używa tego samego konta dla Logic Apps, Power BI, aplikacji zaawansowanych i automatyzacji, aby brama była dostępna dla wszystkich tych usług. Gdy wszystko będzie gotowe, wybierz pozycję **Zamknij**.

   ![Potwierdź informacje o bramie danych](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Teraz [Utwórz zasób platformy Azure na potrzeby instalacji bramy](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Sprawdzanie lub Dostosowywanie ustawień komunikacji

Lokalna Brama danych zależy od [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) łączności z chmurą i ustanawiania odpowiednich połączeń wychodzących do skojarzonego z nią regionu platformy Azure. Jeśli środowisko pracy wymaga, aby ruch przechodzi przez serwer proxy lub zaporę w celu uzyskania dostępu do Internetu, to ograniczenie może uniemożliwić lokalnej bramie danych nawiązanie połączenia z usługą bramy w chmurze i Azure Service Bus. Brama ma kilka ustawień komunikacji, które można dostosować. Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Dostosuj ustawienia komunikacji dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Konfigurowanie ustawień serwera proxy dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Obsługa wysokiej dostępności

Aby uniknąć pojedynczych punktów awarii w przypadku dostępu do danych lokalnych, można mieć wiele instalacji bramy (tylko tryb standardowy) z każdym innym komputerem i skonfigurować je jako klaster lub grupę. W ten sposób, jeśli Brama podstawowa jest niedostępna, żądania danych są kierowane do drugiej bramy i tak dalej. Ponieważ na komputerze można zainstalować tylko jedną bramę standardową, należy zainstalować każdą dodatkową bramę znajdującą się w klastrze na innym komputerze. Wszystkie łączniki, które współpracują z lokalną bramą danych, obsługują wysoką dostępność.

* Musisz mieć już co najmniej jedną instalację bramy z tym samym kontem platformy Azure co Brama podstawowa i klucz odzyskiwania dla tej instalacji.

* Brama podstawowa musi mieć uruchomioną aktualizację z listopada 2017 lub nowszą.

Po skonfigurowaniu bramy podstawowej po przejściu do pozycji zainstaluj inną bramę wybierz opcję **Dodaj do istniejącego klastra bramy**, wybierz bramę podstawową, która jest pierwszą zainstalowaną bramą, i Podaj klucz odzyskiwania dla tej bramy. Aby uzyskać więcej informacji, zobacz [klastrów o wysokiej dostępności dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Zmień lokalizację, Migruj, Przywróć lub Przejmij istniejącą bramę

Jeśli konieczna jest zmiana lokalizacji bramy, przeniesienie instalacji bramy na nowy komputer, odzyskanie uszkodzonej bramy lub przejęcie na własność istniejącej bramy wymaga klucza odzyskiwania, który został dostarczony podczas instalacji bramy.

1. Uruchom Instalatora bramy na komputerze, który ma istniejącą bramę. Jeśli nie masz najnowszego Instalatora bramy, [Pobierz najnowszą wersję bramy](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Przed przystąpieniem do przywracania bramy na komputerze z oryginalną instalacją bramy należy najpierw odinstalować bramę na tym komputerze. Ta akcja rozłącza pierwotną bramę.
   > Po usunięciu lub usunięciu klastra bramy dla dowolnej usługi w chmurze nie można przywrócić tego klastra.

1. Po otwarciu Instalatora Zaloguj się przy użyciu tego samego konta platformy Azure, które zostało użyte do zainstalowania bramy.

1. Wybierz kolejno pozycje **Migruj, Przywróć lub przejęcie istniejącej bramy** > **dalej**, na przykład:

   ![Wybieranie opcji "Migruj, Przywróć lub przejęcie istniejącej bramy"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Wybierz spośród dostępnych klastrów i bram, a następnie wprowadź klucz odzyskiwania dla wybranej bramy, na przykład:

   ![Wybierz bramę i Podaj klucz odzyskiwania](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Aby zmienić region, wybierz pozycję **Zmień region**, a następnie wybierz nowy region.

1. Gdy wszystko będzie gotowe, wybierz pozycję **Konfiguruj** , aby można było zakończyć zadanie.

## <a name="tenant-level-administration"></a>Administracja na poziomie dzierżawy

Aby uzyskać wgląd we wszystkie lokalne bramy danych w dzierżawie usługi Azure AD, Administratorzy globalni w tej dzierżawie mogą zalogować się do [Centrum administracyjnego platformy na platformie zarządzania](https://powerplatform.microsoft.com) jako Administrator dzierżawy i wybrać opcję **bramy danych** . Aby uzyskać więcej informacji, zobacz [administrowanie na poziomie dzierżawy dla lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Uruchom ponownie bramę

Domyślnie instalacja bramy na komputerze lokalnym działa jako konto usługi systemu Windows o nazwie "lokalna Brama danych". Jednak instalacja bramy używa nazwy `NT SERVICE\PBIEgwService` na potrzeby poświadczeń konta "Zaloguj się jako" i ma uprawnienia "Logowanie w trybie usługi".

> [!NOTE]
> Konto usługi systemu Windows różni się od konta używanego do łączenia się z lokalnymi źródłami danych i konta platformy Azure, które jest używane podczas logowania się do usług w chmurze.

Podobnie jak w przypadku każdej innej usługi systemu Windows, można uruchomić i zatrzymać bramę na różne sposoby. Aby uzyskać więcej informacji, zobacz [Ponowne uruchamianie lokalnej bramy danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Jak działa Brama

Użytkownicy w organizacji mogą uzyskiwać dostęp do danych lokalnych, do których mają już dostęp autoryzowany. Jednak zanim użytkownicy będą mogli połączyć się z lokalnym źródłem danych, należy zainstalować i skonfigurować lokalną bramę danych. Zazwyczaj administrator jest osobą, która instaluje i konfiguruje bramę. Te akcje mogą wymagać uprawnień administratora serwera lub specjalnej wiedzy o serwerach lokalnych.

Brama ułatwia szybkie i bezpieczne komunikowanie się w tle. Ta komunikacja odbywa się między użytkownikiem w chmurze, usługą bramy w chmurze i lokalnym źródłem danych. Usługa bramy w chmurze szyfruje i przechowuje poświadczenia źródła danych oraz szczegóły bramy. Usługa kieruje także zapytania i ich wyniki między użytkownikiem, bramą i lokalnym źródłem danych.

Brama współpracuje z zaporami i używa tylko połączeń wychodzących. Cały ruch pochodzący z agenta bramy jest zabezpieczonym ruchem wychodzącym. Brama przekazuje dane ze źródeł lokalnych w zaszyfrowanej kanale za pośrednictwem [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Ta usługa Service Bus tworzy kanał między bramą a usługą wywołującą, ale nie przechowuje żadnych danych. Wszystkie dane przesyłane przez bramę są szyfrowane.

![Architektura bramy danych lokalnych](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> W zależności od usługi w chmurze może być konieczne skonfigurowanie źródła danych dla bramy.

W tych krokach opisano, co się dzieje w przypadku korzystania z elementu połączonego z lokalnym źródłem danych:

1. Usługa w chmurze tworzy zapytanie wraz z zaszyfrowanymi poświadczeniami dla źródła danych. Następnie usługa wysyła zapytanie i poświadczenia do kolejki bramy w celu przetworzenia.

1. Usługa bramy w chmurze analizuje zapytanie i wypycha żądanie do Azure Service Bus.

1. Azure Service Bus wysyła oczekujące żądania do bramy.

1. Brama pobiera zapytanie, odszyfrowuje poświadczenia i nawiązuje połączenie z jednym lub wieloma źródłami danych przy użyciu tych poświadczeń.

1. Brama wysyła zapytanie do źródła danych w celu uruchomienia.

1. Wyniki są wysyłane ze źródła danych z powrotem do bramy, a następnie do usługi bramy w chmurze. Następnie Usługa bramy w chmurze używa wyników.

### <a name="authentication-to-on-premises-data-sources"></a>Uwierzytelnianie do lokalnych źródeł danych

Przechowywane poświadczenia są używane do nawiązywania połączenia z bramą z lokalnymi źródłami danych. Niezależnie od użytkownika, brama używa przechowywanych poświadczeń do nawiązania połączenia. Mogą wystąpić wyjątki uwierzytelniania dla określonych usług, takich jak zapytania bezpośrednie i LiveConnect Analysis Services w Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Usługi w chmurze firmy Microsoft używają usługi [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) do uwierzytelniania użytkowników. Dzierżawa usługi Azure AD zawiera nazwy użytkowników i grupy zabezpieczeń. Zwykle adres e-mail używany do logowania jest taki sam jak główna nazwa użytkownika (UPN) dla Twojego konta.

### <a name="what-is-my-upn"></a>Co to jest moja nazwa UPN?

Jeśli nie jesteś administratorem domeny, być może nie znasz nazwy UPN. Aby znaleźć nazwę UPN dla Twojego konta, uruchom polecenie `whoami /upn` na stacji roboczej. Mimo że wynik wygląda podobnie do adresu e-mail, wynik jest nazwą UPN dla lokalnego konta domeny.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synchronizowanie Active Directory lokalnego z usługą Azure AD

Nazwa UPN lokalnych kont Active Directory i kont usługi Azure AD musi być taka sama. Upewnij się, że każde konto Active Directory lokalnego jest zgodne z kontem usługi Azure AD. Usługi w chmurze wiedzą tylko o kontach w usłudze Azure AD. W związku z tym nie musisz dodawać konta do Active Directory lokalnego. Jeśli konto nie istnieje w usłudze Azure AD, nie można użyć tego konta.

Poniżej przedstawiono sposoby dopasowywania lokalnych kont Active Directory za pomocą usługi Azure AD.

* Ręczne dodawanie kont do usługi Azure AD.

  Utwórz konto w Azure Portal lub w centrum administracyjnym Microsoft 365. Upewnij się, że nazwa konta jest zgodna z nazwą UPN dla lokalnego konta Active Directory.

* Zsynchronizuj konta lokalne z dzierżawą usługi Azure AD za pomocą narzędzia Azure Active Directory Connect.

  Narzędzie Azure AD Connect udostępnia opcje synchronizacji katalogów i konfigurowania uwierzytelniania. Te opcje obejmują funkcję synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. Jeśli nie jesteś administratorem dzierżawy lub administratorem domeny lokalnej, skontaktuj się z administratorem IT, aby uzyskać konfigurację Azure AD Connect. Azure AD Connect zapewnia, że nazwa UPN usługi Azure AD pasuje do lokalnej Active Directory nazwy UPN. Takie dopasowanie pomaga w przypadku korzystania z Analysis Services połączeń na żywo z funkcją Power BI lub logowania jednokrotnego (SSO).

  > [!NOTE]
  > Synchronizowanie kont za pomocą narzędzia Azure AD Connect tworzy nowe konta w dzierżawie usługi Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Często zadawane pytania i rozwiązywanie problemów

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Lokalna brama danych — często zadawane pytania](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Rozwiązywanie problemów z lokalną bramą danych](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorowanie i Optymalizowanie wydajności bramy](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z danymi lokalnymi z usługi Logic Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Funkcje integracji przedsiębiorstwa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
