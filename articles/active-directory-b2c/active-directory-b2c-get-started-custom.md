---
title: Wprowadzenie do zasad niestandardowych — Azure Active Directory B2C
description: Dowiedz się, jak rozpocząć pracę z zasadami niestandardowymi w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66b361a7eb82610d12a10c9c190f2872c072d7ba
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664067"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Wprowadzenie do zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Zasady niestandardowe](active-directory-b2c-overview-custom.md) to pliki konfiguracji, które definiują zachowanie dzierżawy Azure Active Directory B2C (Azure AD B2C). W tym artykule opisano tworzenie niestandardowych zasad, które obsługują rejestrację lub logowanie do konta lokalnego przy użyciu adresu e-mail i hasła. Możesz również przygotować środowisko do dodawania dostawców tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jeszcze tego nie zrobiono, [Utwórz dzierżawę Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.
- [Zarejestruj swoją aplikację](tutorial-register-applications.md) w utworzonej dzierżawie, aby mogła ona komunikować się z Azure AD B2C.
- Wykonaj kroki opisane w temacie [Konfigurowanie rejestracji i logowania przy użyciu konta w serwisie Facebook](active-directory-b2c-setup-fb-app.md) , aby skonfigurować aplikację w serwisie Facebook.

## <a name="add-signing-and-encryption-keys"></a>Dodaj klucze podpisywania i szyfrowania

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Użyj filtru **katalogów i subskrypcji** w górnym menu, aby wybrać katalog, który zawiera dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości** z okienka **zasady** .

### <a name="create-the-signing-key"></a>Utwórz klucz podpisywania

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje**wybierz pozycję `Generate`.
1. W polu **Nazwa**wprowadź `TokenSigningKeyContainer`. Prefiks `B2C_1A_` może zostać dodany automatycznie.
1. W obszarze **Typ klucza**wybierz pozycję **RSA**.
1. W obszarze **użycie klucza**wybierz pozycję **podpis**.
1. Wybierz pozycję **Utwórz**.

### <a name="create-the-encryption-key"></a>Utwórz klucz szyfrowania

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje**wybierz pozycję `Generate`.
1. W polu **Nazwa**wprowadź `TokenEncryptionKeyContainer`. Prefiks `B2C_1A`_ może zostać dodany automatycznie.
1. W obszarze **Typ klucza**wybierz pozycję **RSA**.
1. W obszarze **użycie klucza**wybierz pozycję **szyfrowanie**.
1. Wybierz pozycję **Utwórz**.

### <a name="create-the-facebook-key"></a>Tworzenie klucza Facebook

Dodaj [wpis tajny](active-directory-b2c-setup-fb-app.md) aplikacji usługi Facebook jako klucz zasad. Możesz użyć klucza tajnego aplikacji utworzonej w ramach wymagań wstępnych tego artykułu.

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje**wybierz pozycję `Manual`.
1. W obszarze **Nazwa**wprowadź `FacebookSecret`. Prefiks `B2C_1A_` może zostać dodany automatycznie.
1. W **kluczu tajnym**wprowadź *klucz tajny* aplikacji w serwisie Facebook z Developers.Facebook.com. Ta wartość jest kluczem tajnym, a nie IDENTYFIKATORem aplikacji.
1. W obszarze **użycie klucza**wybierz pozycję **podpis**.
1. Wybierz pozycję **Utwórz**.

## <a name="register-identity-experience-framework-applications"></a>Rejestrowanie aplikacji platformy obsługi tożsamości

Azure AD B2C wymaga zarejestrowania dwóch aplikacji używanych do rejestracji i logowania użytkowników przy użyciu kont lokalnych: *IdentityExperienceFramework*, internetowego interfejsu API i *ProxyIdentityExperienceFramework*, natywnej aplikacji z delegowanym uprawnieniem do aplikacji IdentityExperienceFramework. Użytkownicy mogą zarejestrować się przy użyciu adresu e-mail lub nazwy użytkownika i hasła w celu uzyskania dostępu do aplikacji zarejestrowanych w ramach dzierżawy, co powoduje utworzenie "konta lokalnego". Konta lokalne istnieją tylko w dzierżawie Azure AD B2C.

Musisz zarejestrować te dwie aplikacje w dzierżawie Azure AD B2C tylko raz.

### <a name="register-the-identityexperienceframework-application"></a>Rejestrowanie aplikacji IdentityExperienceFramework

Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć bieżącego środowiska **aplikacji** lub naszego nowego interfejsu Unified **rejestracje aplikacji (wersja zapoznawcza)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikacje](#tab/applications/)

1. Zaznacz **wszystkie usługi** w lewym górnym rogu Azure Portal.
1. W polu wyszukiwania wpisz `Azure Active Directory`.
1. W wynikach wyszukiwania wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** w menu po lewej stronie wybierz pozycję **rejestracje aplikacji (starsza wersja)** .
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. W obszarze **Nazwa**wprowadź `IdentityExperienceFramework`.
1. W obszarze **Typ aplikacji**wybierz pozycję **aplikacja sieci Web/interfejs API**.
1. W obszarze **adres URL logowania**wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, gdzie `your-tenant-name` jest nazwą domeny dzierżawy Azure AD B2C. Wszystkie adresy URL powinny teraz używać [b2clogin.com](b2clogin.md).
1. Wybierz pozycję **Utwórz**. Po jego utworzeniu Skopiuj identyfikator aplikacji i Zapisz go do późniejszego użycia.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)** , a następnie wybierz pozycję **Nowa rejestracja**.
1. W obszarze **Nazwa**wprowadź `IdentityExperienceFramework`.
1. W obszarze **obsługiwane typy kont**wybierz opcję **konta tylko w tym katalogu organizacji**.
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Sieć Web**, a następnie wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, gdzie `your-tenant-name` jest nazwą domeny dzierżawy Azure AD B2C.
1. W obszarze **uprawnienia**zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.
1. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Następnie udostępnienie interfejsu API przez dodanie zakresu:

1. W obszarze **Zarządzaj**wybierz pozycję **uwidaczniaj interfejs API**.
1. Wybierz pozycję **Dodaj zakres**, a następnie wybierz pozycję **Zapisz i Kontynuuj** , aby zaakceptować domyślny identyfikator URI aplikacji.
1. Wprowadź następujące wartości, aby utworzyć zakres, który umożliwia wykonywanie zasad niestandardowych w dzierżawie Azure AD B2C:
    * **Nazwa zakresu**: `user_impersonation`
    * **Nazwa wyświetlana zgody administratora**: `Access IdentityExperienceFramework`
    * **Opis zgody administratora**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Wybierz pozycję **Dodaj zakres**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Rejestrowanie aplikacji ProxyIdentityExperienceFramework

#### <a name="applicationstabapplications"></a>[Aplikacje](#tab/applications/)

1. W **rejestracje aplikacji (starsza wersja)** wybierz pozycję **rejestracja nowej aplikacji**.
1. W obszarze **Nazwa**wprowadź `ProxyIdentityExperienceFramework`.
1. W obszarze **Typ aplikacji**wybierz opcję **natywny**.
1. W obszarze **Identyfikator URI przekierowania**wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, gdzie `your-tenant-name` jest dzierżawcą Azure AD B2C.
1. Wybierz pozycję **Utwórz**. Po jego utworzeniu Skopiuj identyfikator aplikacji i Zapisz go do późniejszego użycia.
1. Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**.
1. Wybierz **pozycję Wybierz interfejs API**, Wyszukaj i wybierz pozycję **IdentityExperienceFramework**, a następnie kliknij pozycję **Wybierz**.
1. Zaznacz pole wyboru obok pozycji **dostęp do IdentityExperienceFramework**, kliknij pozycję **Wybierz**, a następnie kliknij pozycję **gotowe**.
1. Wybierz pozycję **Udziel uprawnień**, a następnie potwierdź, wybierając pozycję **tak**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)** , a następnie wybierz pozycję **Nowa rejestracja**.
1. W obszarze **Nazwa**wprowadź `ProxyIdentityExperienceFramework`.
1. W obszarze **obsługiwane typy kont**wybierz opcję **konta tylko w tym katalogu organizacji**.
1. W obszarze **Identyfikator URI przekierowania**Użyj listy rozwijanej, aby wybrać opcję **Klient publiczny/natywny (Mobile & Desktop)** .
1. W obszarze **Identyfikator URI przekierowania**wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, gdzie `your-tenant-name` jest dzierżawcą Azure AD B2C.
1. W obszarze **uprawnienia**zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.
1. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Następnie określ, że aplikacja powinna być traktowana jako klient publiczny:

1. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**.
1. Wybierz opcję **Wypróbuj nowe środowisko** (jeśli zostało wyświetlone).
1. W obszarze **Ustawienia zaawansowane**Włącz **Traktuj aplikację jako klienta publicznego** (wybierz opcję **tak**).
1. Wybierz pozycję **Zapisz**.

Teraz Udziel uprawnień do zakresu interfejsu API, który został uwidoczniony we wcześniejszej części rejestracji *IdentityExperienceFramework* :

1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API** , a następnie wybierz aplikację **IdentityExperienceFramework** .
1. W obszarze **uprawnienie**wybierz wcześniej zdefiniowany zakres **user_impersonation** .
1. Wybierz pozycję **Dodaj uprawnienia**. Poczekaj kilka minut, zanim przejdziesz do kolejnego kroku.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)** .
1. Wybierz obecnie zalogowane konto administratora lub Zaloguj się przy użyciu konta w dzierżawie Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* .
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan** dla obu zakresów. Propagowanie uprawnień może potrwać kilka minut.

* * *

## <a name="custom-policy-starter-pack"></a>Pakiet startowy zasad niestandardowych

Zasady niestandardowe są zestawem plików XML przekazywanym do dzierżawy Azure AD B2C, aby zdefiniować profile techniczne i przedziały użytkowników. Udostępniamy pakiety początkowe z kilkoma wstępnie utworzonymi zasadami, aby szybko rozpocząć pracę. Każdy z tych pakietów początkowych zawiera najmniejszą liczbę profilów technicznych i podróży użytkowników, które są konieczne do osiągnięcia opisanych scenariuszy:

- **LocalAccounts** — umożliwia korzystanie tylko z kont lokalnych.
- **SocialAccounts** — umożliwia korzystanie tylko z kont społecznościowych (lub federacyjnych).
- **SocialAndLocalAccounts** — umożliwia korzystanie z kont lokalnych i społecznościowych.
- **SocialAndLocalAccountsWithMFA** — umożliwia korzystanie z opcji uwierzytelniania społecznościowego, lokalnego i wieloskładnikowego.

Każdy początkowy pakiet zawiera:

- **Plik podstawowy** — do podstawy wymagane są kilka modyfikacji. Przykład: *TrustFrameworkBase. XML*
- **Plik rozszerzenia** — ten plik jest miejscem, w którym wprowadzane są większość zmian konfiguracji. Przykład: *TrustFrameworkExtensions. XML*
- **Pliki jednostek uzależnionych** — pliki specyficzne dla zadania wywoływane przez aplikację. Przykłady: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

W tym artykule opisano edytowanie niestandardowych plików zasad XML w pakiecie **SocialAndLocalAccounts** Starter. Jeśli potrzebujesz edytora XML, wypróbuj [Visual Studio Code](https://code.visualstudio.com/download), lekki Edytor Międzyplatformowy.

### <a name="get-the-starter-pack"></a>Pobierz pakiet startowy

Pobierz pakiety początkowe dla zasad niestandardowych z usługi GitHub, a następnie zaktualizuj pliki XML w pakiecie SocialAndLocalAccounts Starter przy użyciu nazwy dzierżawy Azure AD B2C.

1. [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) lub Sklonuj repozytorium:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. We wszystkich plikach w katalogu **SocialAndLocalAccounts** Zastąp ciąg `yourtenant` nazwą Azure AD B2C dzierżawy.

    Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosotenant*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku rozszerzeń *TrustFrameworkExtensions. XML*.

1. Otwórz **`TrustFrameworkExtensions.xml`** `SocialAndLocalAccounts/`i znajdź element `<TechnicalProfile Id="login-NonInteractive">`.
1. Zastąp oba wystąpienia `IdentityExperienceFrameworkAppId` IDENTYFIKATORem aplikacji utworzonego wcześniej.
1. Zastąp oba wystąpienia `ProxyIdentityExperienceFrameworkAppId` IDENTYFIKATORem aplikacji utworzonego wcześniej.
1. Zapisz plik.

## <a name="upload-the-policies"></a>Przekazywanie zasad

1. Wybierz element menu **Struktura środowiska tożsamości** w dzierżawie B2C w Azure Portal.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. W tej kolejności należy przekazać pliki zasad:
    1. *TrustFrameworkBase. XML*
    1. *TrustFrameworkExtensions. XML*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset. XML*

Podczas przekazywania plików platforma Azure dodaje prefiks `B2C_1A_` do każdego z nich.

> [!TIP]
> Jeśli Edytor XML obsługuje walidację, sprawdź poprawność plików względem schematu XML `TrustFrameworkPolicy_0.3.0.0.xsd` znajdującego się w katalogu głównym pakietu początkowego. Walidacja schematu XML identyfikuje błędy przed przekazaniem.

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. W obszarze **zasady niestandardowe**wybierz pozycję **B2C_1A_signup_signin**.
1. Dla **opcji wybierz aplikację** na stronie Przegląd zasad niestandardowych wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana.
1. Upewnij się, że **adres URL odpowiedzi** jest `https://jwt.ms`.
1. Wybierz pozycję **Uruchom teraz**.
1. Utwórz konto przy użyciu adresu e-mail.
1. Ponownie wybierz pozycję **Uruchom teraz** .
1. Zaloguj się przy użyciu tego samego konta, aby potwierdzić, że konfiguracja jest poprawna.

## <a name="add-facebook-as-an-identity-provider"></a>Dodawanie usługi Facebook jako dostawcy tożsamości

1. W pliku `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** Zastąp wartość `client_id` identyfikatorem aplikacji Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Przekaż plik *TrustFrameworkExtensions. XML* do dzierżawy.
1. W obszarze **zasady niestandardowe**wybierz pozycję **B2C_1A_signup_signin**.
1. Wybierz pozycję **Uruchom teraz** i wybierz pozycję Facebook, aby zalogować się za pomocą usługi Facebook i przetestować zasady niestandardowe.

## <a name="next-steps"></a>Następne kroki

Następnie spróbuj dodać Azure Active Directory (Azure AD) jako dostawcę tożsamości. Plik podstawowy używany w tym przewodniku wprowadzającym zawiera już część zawartości potrzebną do dodawania innych dostawców tożsamości, takich jak usługa Azure AD.

Aby uzyskać informacje o konfigurowaniu usługi Azure AD jako dostawcy tożsamości, zobacz [Konfigurowanie logowania i logowanie przy użyciu konta Azure Active Directory za pomocą zasad niestandardowych Active Directory B2C](active-directory-b2c-setup-aad-custom.md).
