---
title: Publikowanie aplikacji w galerii aplikacji usługi Azure AD
description: Dowiedz się, jak wyświetlić listę aplikacji, która obsługuje logowanie jednokrotne w galerii aplikacji Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: dc271fa768bee66107e66a1b8d4f16c1188ce418
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439748"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Publikowanie aplikacji w galerii aplikacji usługi Azure AD

Aplikację można opublikować w galerii aplikacji usługi Azure AD. Gdy aplikacja zostanie opublikowana, zostanie wyświetlona jako opcja dla klientów, którzy będą dodawać aplikacje do swojej dzierżawy. 

Niektóre zalety dodawania aplikacji do galerii usługi Azure AD obejmują:

- Klienci mogą znaleźć najlepszą możliwą obsługę logowania jednokrotnego dla aplikacji.
- Konfiguracja aplikacji jest prosta i minimalna.
- Szybkie wyszukiwanie umożliwia znalezienie aplikacji w galerii.
- Wszyscy klienci usługi Azure AD w wersji bezpłatna, podstawowa i Premium mogą korzystać z tej integracji.
- Podzielni klienci mogą uzyskać samouczek konfigurowania krok po kroku.

Ponadto istnieje wiele korzyści, gdy klienci korzystają z usługi Azure AD jako dostawcy tożsamości dla Twojej aplikacji. Oto niektóre poprawki:

- Udostępnianie użytkownikom logowania jednokrotnego. Logowanie jednokrotne pozwala zmniejszyć koszty pomocy technicznej, ułatwiając klientom logowanie jednokrotne. Jeśli włączono Logowanie jednokrotne, Administratorzy IT klientów nie muszą dowiedzieć się, jak skonfigurować aplikację do użycia w organizacji. Aby dowiedzieć się więcej o logowaniu jednokrotnym, zobacz [co to jest logowanie jednokrotne?](../manage-apps/what-is-single-sign-on.md).
- Aplikację można znaleźć w galerii aplikacji Microsoft 365, w obszarze uruchamiania aplikacji Microsoft 365 i w ramach wyszukiwania firmy Microsoft w witrynie Office.com. 
- Zintegrowane zarządzanie aplikacjami. Aby dowiedzieć się więcej o zarządzaniu aplikacjami w usłudze Azure AD, zobacz [co to jest zarządzanie aplikacjami?](../manage-apps/what-is-application-management.md).
- Twoja aplikacja może korzystać z [interfejs API programu Graph](https://docs.microsoft.com/graph/) , aby uzyskać dostęp do danych, które umożliwiają produktywność użytkowników w ekosystemie firmy Microsoft.
- Dokumentacja specyficzna dla aplikacji utworzona wspólnie z zespołem usługi Azure AD dla naszych wzajemnych klientów upraszcza wdrażanie.
- Klienci mogą w całości zarządzać uwierzytelnianiem i autoryzacją tożsamości pracowników i Gości.
- Zapewnienie odpowiedzialności za zarządzanie kontami i zgodność z właścicielem tych tożsamości przez klienta.
- Zapewnienie możliwości włączenia lub wyłączenia logowania jednokrotnego dla określonych dostawców tożsamości, grup lub użytkowników w celu spełnienia potrzeb firmy.
- Zwiększ swój poziom rynkowy i jego przyjęcie. Wiele dużych organizacji wymaga, aby (lub zależy) pracownicy mogli bezproblemowo korzystać z logowania jednokrotnego we wszystkich aplikacjach. Łatwe tworzenie logowania jednokrotnego.
- Zmniejszenie liczby użytkowników końcowych, co może zwiększyć użycie użytkowników końcowych i zwiększyć przychody.
- Klienci korzystający z systemu do zarządzania tożsamościami w różnych domenach ([Standard scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) mogą korzystać z aprowizacji dla tej samej aplikacji.
- Dodaj zabezpieczenia i wygodę, gdy użytkownicy logują się do aplikacji za pomocą logowania jednokrotnego usługi Azure AD i usuwając potrzebę oddzielenia poświadczeń.

> [!TIP]
> W przypadku oferowania aplikacji do użytku przez inne firmy za pomocą zakupu lub subskrypcji możesz udostępnić swoją aplikację klientom w ramach swoich dzierżaw platformy Azure. Jest to tzw. Tworzenie aplikacji z wieloma dzierżawcami. Aby zapoznać się z omówieniem tego pojęcia, zobacz wielodostępne [aplikacje na platformie Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) i [dzierżawę w Azure Active Directory](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Aby opublikować aplikację w galerii usługi Azure AD, musisz wyrazić zgodę na określone warunki i postanowienia. Przed rozpoczęciem upewnij się, że Odczytuj i zgadzasz się na [warunki i postanowienia](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

Kroki umożliwiające opublikowanie aplikacji w galerii aplikacji usługi Azure AD:
1. Wybierz odpowiedni standard logowania jednokrotnego dla aplikacji.
2. Zaimplementuj Logowanie jednokrotne w aplikacji.
3. Utwórz dzierżawę platformy Azure i przetestuj swoją aplikację.
4. Utwórz i Opublikuj dokumentację.
5. Prześlij swoją aplikację.
6. Dołącz do sieci Microsoft Partner Network.


## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz stałego konta do testowania z co najmniej dwoma zarejestrowanymi użytkownikami.


## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Krok 1 — wybór odpowiedniego standardu logowania jednokrotnego dla aplikacji

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, należy zaimplementować co najmniej jedną z obsługiwanych opcji logowania jednokrotnego. Aby poznać opcje logowania jednokrotnego i sposób konfigurowania ich przez klientów w usłudze Azure AD, zobacz [Opcje logowania jednokrotnego](../manage-apps/sso-options.md).

Poniższa tabela zawiera porównanie głównych standardów: Open Authentication 2,0 (OAuth 2,0) z OpenID Connect Connect (OIDC), SAML (SAML) i federacyjnych usług sieci Web (w usłudze WS-karmione).

| Możliwość| OAuth/OIDC| Protokół SAML/WS-karmione |
| - |-|-|
| Logowanie jednokrotne oparte na sieci Web| √| √ |
| Logowanie jednokrotne oparte na sieci Web| √| √ |
| Logowanie jednokrotne oparte na urządzeniach przenośnych| √| √* |
| Logowanie jednokrotne oparte na urządzeniach przenośnych| √| √* |
| Zasady dostępu warunkowego dla aplikacji mobilnych| √| X |
| Bezproblemowe środowisko MFA dla aplikacji mobilnych| √| X |
| Microsoft Graph dostępu| √| X |

* Możliwe, ale firma Microsoft nie udostępnia próbek ani wskazówek.

### <a name="oauth-20-and-openid-connect"></a>Protokoły OAuth 2.0 i OpenID Connect
Uwierzytelnianie OAuth 2,0 jest [standardowym](https://oauth.net/2/) protokołem do autoryzacji. OpenID Connect Connect (OIDC) to [standardowa](https://openid.net/connect/) warstwa uwierzytelniania tożsamości oparta na protokole OAuth 2,0. 

**Powody wybierania protokołu OAuth/OIDC**
- Autoryzacja nieodłączna w tych protokołach umożliwia aplikacji dostęp do i integrowanie z rozbudowanymi danymi użytkownika i organizacji za pomocą interfejsu API Microsoft Graph.
- Upraszcza pracę użytkowników końcowych w przypadku przyjmowania rejestracji Jednokrotnej dla aplikacji. Można łatwo definiować wymagane zestawy uprawnień, które są następnie automatycznie reprezentowane przez administratora lub użytkownika końcowego.
- Korzystanie z tych protokołów umożliwia klientom korzystanie z zasad dostępu warunkowego i usługi Multi-Factor Authentication (MFA) w celu kontrolowania dostępu do aplikacji. 
- Firma Microsoft udostępnia biblioteki i [przykłady kodu na wielu platformach technologicznych](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) , aby pomóc w rozwoju.  

**Niektóre kwestie, które należy wziąć pod uwagę**
- Jeśli zaimplementowano już Logowanie jednokrotne oparte na protokole SAML dla aplikacji, możesz nie chcieć zaimplementować nowego standardu w celu pobrania aplikacji w galerii.

### <a name="saml-20-or-ws-fed"></a>SAML 2,0 lub WS-karmione

Język SAML to wczesne i szeroko przyjęte, jednokrotne [Logowanie](https://www.oasis-open.org/standards#samlv2.0) do aplikacji sieci Web. Aby dowiedzieć się więcej na temat używania języka SAML przez platformę Azure, zobacz [jak platforma Azure używa protokołu SAML](active-directory-saml-protocol-reference.md). 

Federacja usług sieci Web (WS-karmione) jest [standardem branżowym](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) zwykle używanym przez aplikacje sieci Web, które są opracowywane przy użyciu platformy .NET.

**Powody wybierania protokołu SAML**
- SAML 2,0 to wersja najbardziej dojrzałej i większość platform technologicznych obsługuje biblioteki typu open source dla protokołu SAML 2,0. 
- Możesz udostępnić klientom interfejs administracyjny, aby skonfigurować Logowanie jednokrotne SAML. Mogą konfigurować Logowanie jednokrotne SAML dla Microsoft Azure AD i wszystkich innych dostawców tożsamości, które obsługują protokół SAML.

**Niektóre kwestie, które należy wziąć pod uwagę**
- W przypadku korzystania z protokołów SAML 2,0 lub WSFed dla aplikacji mobilnych niektóre zasady dostępu warunkowego, w tym uwierzytelnianie wieloskładnikowe (MFA), będą mieć obniżoną wydajność.
- Jeśli chcesz uzyskać dostęp do Microsoft Graph, musisz zaimplementować autoryzację za pomocą protokołu OAuth 2,0 w celu wygenerowania niezbędnych tokenów. 

### <a name="password-based"></a>Oparte na hasłach
Logowanie jednokrotne oparte na hasłach, nazywane również magazynem haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest to również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać pojedyncze konto, na przykład na kontach aplikacji Media społecznościowych w organizacji.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Krok 2. Implementowanie logowania jednokrotnego w aplikacji
Każda aplikacja w galerii musi implementować jedną z obsługiwanych opcji logowania jednokrotnego. Aby dowiedzieć się więcej o obsługiwanych opcjach, zobacz [Opcje logowania jednokrotnego](../manage-apps/sso-options.md).

W przypadku uwierzytelniania OAuth i OIDC zobacz [wskazówki dotyczące wzorców uwierzytelniania](v2-app-types.md) i [przykładów kodu usługi Azure Active Directory](sample-v2-code.md).

W przypadku języka SAML i protokołu WS-karmionego aplikacja musi mieć możliwość integracji z logowaniem jednokrotnym w trybie SP lub dostawcy tożsamości. Przed przesłaniem żądania upewnij się, że ta funkcja działa prawidłowo.

Aby dowiedzieć się więcej o uwierzytelnianiu, zobacz [co to jest uwierzytelnianie?](../azuread-dev/v1-authentication-scenarios.md).

> [!IMPORTANT]
> W przypadku aplikacji federacyjnych (OpenID Connect i SAML/WS-karmione) aplikacja musi obsługiwać model oprogramowanie jako usługa (SaaS). Aplikacje z galerii usługi Azure AD muszą obsługiwać wiele konfiguracji klientów i nie powinny być specyficzne dla żadnego pojedynczego klienta.

### <a name="implement-oauth-20-and-openid-connect"></a>Zaimplementuj uwierzytelnianie OAuth 2,0 i OpenID Connect

W przypadku programu OpenID Connect Connect aplikacja musi mieć dostęp do wielu dzierżawców, a [Struktura wyrażania zgody usługi Azure AD](consent-framework.md) musi być poprawnie zaimplementowana dla aplikacji. Użytkownik może wysłać żądanie logowania do wspólnego punktu końcowego, aby klient mógł wyrazić zgodę na dostęp do aplikacji. Możesz kontrolować dostęp użytkowników na podstawie identyfikatora dzierżawy oraz nazwy UPN użytkownika otrzymanej w tokenie.

Aby zapoznać się z konkretnymi przykładami, zobacz [przykłady kodu platformy tożsamości firmy Microsoft](sample-v2-code.md). 

Aby przejrzeć przykłady dotyczące urządzeń przenośnych, zobacz: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [platforma uniwersalna systemu Windows](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>Zaimplementuj element SAML 2,0

Jeśli aplikacja obsługuje protokół SAML 2,0, możesz zintegrować go bezpośrednio z dzierżawą usługi Azure AD. Aby dowiedzieć się więcej na temat konfiguracji protokołu SAML za pomocą usługi Azure AD, zobacz Konfigurowanie logowania jednokrotnego [opartego na protokole SAML](../manage-apps/configure-saml-single-sign-on.md).

Firma Microsoft nie udostępnia lub nie zaleca bibliotek dla implementacji SAML. Dostępnych jest wiele bibliotek Open Source.

### <a name="implement-ws-fed"></a>Implementowanie protokołu WS-karmionego
Aby dowiedzieć się więcej na temat usługi WS-in ASP.NET Core, zobacz [uwierzytelnianie użytkowników przy użyciu usługi WS-Federation w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Implementowanie magazynu haseł

Utwórz aplikację sieci Web, która ma stronę logowania w formacie HTML. Upewnij się, że aplikacja obsługuje uwierzytelnianie formularzy, aby można było utworzyć magazynowanie haseł w celu zapewnienia, że logowanie jednokrotne będzie działać zgodnie z oczekiwaniami.


## <a name="step-3---create-your-azure-tenant-and-test-your-app"></a>Krok 3. Tworzenie dzierżawy platformy Azure i testowanie aplikacji

Do przetestowania aplikacji potrzebna jest dzierżawa usługi Azure AD. Aby skonfigurować środowisko programistyczne, zobacz [Szybki Start: Konfigurowanie dzierżawy](quickstart-create-new-tenant.md).

Alternatywnie dzierżawa usługi Azure AD jest dostarczana z każdą subskrypcją Microsoft 365. Aby skonfigurować bezpłatne środowisko deweloperskie Microsoft 365, zobacz [dołączanie do programu Microsoft 365 Developer](https://docs.microsoft.com/office/developer-program/microsoft-365-developer-program).

Po uzyskaniu dzierżawy musisz włączyć i przetestować Logowanie jednokrotne. 

**W przypadku aplikacji OIDC lub Oath** [zarejestruj aplikację](quickstart-register-app.md) jako aplikację wielodostępną. W obszarze obsługiwane typy kont wybierz opcję konta z dowolnego katalogu organizacyjnego i osobiste konta Microsoft.

W **przypadku aplikacji opartych na protokole SAML i WS-** based aplikacje można [konfigurować](../manage-apps/configure-saml-single-sign-on.md) za pomocą ogólnego szablonu SAML w usłudze Azure AD.

W razie potrzeby można również [przekonwertować aplikację z jedną dzierżawą na wiele dzierżawców](howto-convert-app-to-be-multi-tenant.md) .


## <a name="step-4---create-and-publish-documentation"></a>Krok 4 — Tworzenie i publikowanie dokumentacji

### <a name="documentation-on-your-site"></a>Dokumentacja witryny

Łatwość wdrażania jest istotnym czynnikiem w podejmowaniu decyzji dotyczących oprogramowania w przedsiębiorstwie. Łatwa w obsłudze dokumentacja pozwala klientom na ich wdrażanie i zmniejsza koszty pomocy technicznej. Pracując z tysiącami dostawców oprogramowania, firma Microsoft widziała, co działa.

Zalecamy, aby dokumentacja w witrynie zawierała co najmniej następujące elementy.

* Wprowadzenie do funkcji logowania jednokrotnego
  * Obsługiwane protokoły
  * Wersja i jednostka SKU
  * Lista obsługiwanych dostawców tożsamości z linkami do dokumentacji
* Informacje o licencjonowaniu dla aplikacji
* Kontrola dostępu oparta na rolach na potrzeby konfigurowania logowania jednokrotnego
* Kroki konfiguracji logowania jednokrotnego
  * Elementy konfiguracji interfejsu użytkownika dla SAML z oczekiwanymi wartościami od dostawcy
  * Informacje o dostawcy usług, które mają zostać przesłane do dostawców tożsamości
* Jeśli OIDC/OAuth
  * Lista uprawnień wymaganych do wyrażania zgody z uzasadnieniem biznesowym
* Kroki testowania dla użytkowników pilotażowych
* Informacje dotyczące rozwiązywania problemów, w tym kody błędów i komunikaty
* Mechanizmy obsługi dla klientów

### <a name="documentation-on-the-microsoft-site"></a>Dokumentacja w witrynie firmy Microsoft

Po wyświetleniu listy aplikacji za pomocą galerii aplikacji Azure Active Directory, która również publikuje aplikację w portalu Azure Marketplace, firma Microsoft będzie generować dokumentację dla naszych klientów, którzy objaśniają proces krok po kroku. Przykład można zobaczyć [tutaj](https://aka.ms/appstutorial). Ta dokumentacja jest tworzona na podstawie przesłanej do galerii i można ją łatwo zaktualizować w przypadku wprowadzania zmian w aplikacji przy użyciu konta usługi GitHub.


## <a name="step-5---submit-your-app"></a>Krok 5. przesyłanie aplikacji

Po przetestowaniu współpracy aplikacji z usługą Azure AD Prześlij żądanie aplikacji w [portalu sieci aplikacji firmy Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Przy pierwszej próbie zalogowania się do portalu zostanie wyświetlony jeden z dwóch ekranów. 

Jeśli zostanie wyświetlony komunikat "nie zadziałał", musisz skontaktować się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Podaj konto e-mail, którego chcesz użyć do przesłania żądania. Służbowy adres e-mail, taki jak `name@yourbusiness.com` jest preferowany. Zespół usługi Azure AD doda konto w portalu sieci aplikacji firmy Microsoft.

Jeśli zostanie wyświetlona strona "Żądaj dostępu", wprowadź uzasadnienie biznesowe i wybierz pozycję **Żądaj dostępu**.

Po dodaniu konta możesz zalogować się do portalu sieci aplikacji firmy Microsoft i przesłać żądanie, wybierając kafelek **Prześlij żądanie (ISV)** na stronie głównej.

![Kafelek przesyłania żądania (ISV) na stronie głównej](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Problemy z logowaniem do portalu

Jeśli ten błąd występuje podczas logowania, poniżej przedstawiono szczegółowe informacje o tym problemie, co pozwala na jego rozwiązanie.

* Jeśli logowanie zostało zablokowane, jak pokazano poniżej:

  ![Rozwiązywanie problemów z aplikacją w galerii](./media/howto-app-gallery-listing/blocked.png)

**Co się dzieje:**

Użytkownik-Gość jest federacyjny dla dzierżawy głównej, która jest również usługą Azure AD. Użytkownik-Gość jest w wysokim ryzyku. Firma Microsoft nie zezwala użytkownikom wysokiego ryzyka na dostęp do zasobów. Wszyscy użytkownicy o wysokim ryzyku (pracownicy lub Goście/dostawcy) muszą skorygować/zamknąć ryzyko uzyskania dostępu do zasobów firmy Microsoft. Dla użytkowników-Gości ten czynnik ryzyka pochodzi z dzierżawy głównej, a zasady pochodzą z dzierżawy zasobów (w tym przypadku firmy Microsoft).
 
**Bezpieczne rozwiązania:**

* Zarejestrowani użytkownicy-Goście zarejestrowany przez usługę MFA korygują własne ryzyko użytkownika. Może to zostać zrobione przez użytkownika-gościa wykonującego bezpieczną zmianę lub zresetowanie hasła ( https://aka.ms/sspr) w swojej dzierżawie domowej (wymaga to usługi MFA i SSPR w dzierżawie głównej). Bezpieczne Zmienianie lub Resetowanie hasła musi być inicjowane w usłudze Azure AD, a nie w Premium.

* Użytkownicy-Goście mają swoje administratorów korygują ryzyko. W takim przypadku administrator wykona Resetowanie hasła (tymczasowe generowanie hasła). Nie wymaga to ochrony tożsamości. Administrator użytkownika-gościa może przejść do https://aka.ms/RiskyUsers i kliknąć pozycję "Resetuj hasło".

* Użytkownicy-Goście mają swoich administratorów blisko lub odrzucają ryzyko. Nie wymaga to ochrony tożsamości. Administrator może przejść do https://aka.ms/RiskyUsers i kliknąć pozycję "Odrzuć ryzyko związane z użytkownikiem". Jednak administrator musi wykonać należytą staranność, aby upewnić się, że było to fałszywe pozytywne oceny ryzyka przed zamknięciem ryzyka użytkownika. W przeciwnym razie, narażamy zasoby firmy Microsoft na ryzyko, pomijając ocenę ryzyka bez badania.

> [!NOTE]
> Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Opcje specyficzne dla implementacji
Jeśli chcesz dodać aplikację do listy w galerii za pomocą OpenID Connect Connect, wybierz pozycję **OpenID Connect connect & OAuth 2,0** jak pokazano.

![Wyświetlanie aplikacji OpenID Connect Connect w galerii](./media/howto-app-gallery-listing/openid.png)

Jeśli chcesz dodać aplikację do listy w galerii za pomocą **saml 2,0** lub **WS-karmione**, wybierz pozycję **SAML 2.0/WS-karmione** , jak pokazano.

![Wyświetlanie listy aplikacji SAML 2,0 lub WS-karmione w galerii](./media/howto-app-gallery-listing/saml.png)

Jeśli chcesz dodać aplikację do listy w galerii przy użyciu hasła SSO logowania jednokrotnego, wybierz pozycję **Logowanie jednokrotne hasła** , jak pokazano.

![Wyświetlanie listy aplikacji Logowanie jednokrotne w galerii](./media/howto-app-gallery-listing/passwordsso.png)

Jeśli wdrażasz punkt końcowy Standard scim 2,0 na potrzeby aprowizacji użytkowników, wybierz opcję, jak pokazano. 

   ![Żądanie aprowizacji użytkownika](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Zaktualizuj lub Usuń istniejącą listę

Możesz zaktualizować lub usunąć istniejącą aplikację galerii w [portalu sieci aplikacji firmy Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

![Wyświetlanie listy aplikacji SAML w galerii](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Jeśli masz problemy z dostępem, zapoznaj się z poprzednią sekcją dotyczącą tworzenia konta. Jeśli to nie zadziała, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).


### <a name="timelines"></a>Osie czasu

Oś czasu procesu tworzenia listy aplikacji SAML 2,0 lub WS-karmione w galerii wynosi od 7 do 10 dni roboczych.

![Oś czasu do wyświetlania aplikacji SAML w galerii](./media/howto-app-gallery-listing/timeline.png)

Oś czasu procesu tworzenia listy aplikacji OpenID Connect Connect w galerii to od 2 do 5 dni roboczych.

![Oś czasu do wyświetlania aplikacji OpenID Connect Connect w galerii](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Ich eskalacji

W przypadku wszelkich eskalacji Wyślij wiadomość e-mail do [zespołu ds. integracji SSO usługi Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com)i będziemy reagować tak szybko, jak to możliwe.


## <a name="step-6---join-the-microsoft-partner-network"></a>Krok 6. dołączanie do programu Microsoft Partner Network
Microsoft Partner Network zapewnia natychmiastowy dostęp do wyłącznych zasobów, programów, narzędzi i połączeń. Aby dołączyć do sieci i utworzyć plan rynkowy, zobacz dotrzeć do [komercyjnych klientów](https://partner.microsoft.com/explore/commercial#gtm).


## <a name="next-steps"></a>Następne kroki
* [Tworzenie punktu końcowego Standard scim i Konfigurowanie aprowizacji użytkowników](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Scenariusze uwierzytelniania dla usługi Azure AD](authentication-flows-app-scenarios.md)
