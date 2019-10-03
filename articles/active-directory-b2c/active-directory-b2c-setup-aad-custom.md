---
title: Konfigurowanie logowania za pomocą konta Azure Active Directory w Azure Active Directory B2C przy użyciu zasad niestandardowych
description: Skonfiguruj logowanie za pomocą konta Azure Active Directory w Azure Active Directory B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2aa2ed6fe4d8218737c42bb3d76084c5d677623f
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71826944"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania za pomocą konta Azure Active Directory przy użyciu zasad niestandardowych w programie Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania dla użytkowników z organizacji Azure Active Directory (Azure AD) przy użyciu [zasad niestandardowych](active-directory-b2c-overview-custom.md) w Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Rejestrowanie aplikacji

Aby włączyć Logowanie użytkowników z określonej organizacji usługi Azure AD, musisz zarejestrować aplikację w ramach organizacji dzierżawy usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu zawierającego swoją organizacyjną dzierżawę usługi Azure AD (na przykład contoso.com). Wybierz **Filtr katalog + subskrypcja** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład `Azure AD B2C App`.
1. Zaakceptuj domyślny wybór **kont w tym katalogu organizacyjnym tylko** dla tej aplikacji.
1. W polu **Identyfikator URI przekierowania**Zaakceptuj wartość **sieci Web**i wprowadź następujący adres URL w postaci małych liter, gdzie `your-B2C-tenant-name` jest zastępowana nazwą dzierżawy Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Wybierz pozycję **zarejestruj**. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
1. Wybierz pozycję **certyfikaty & wpisy tajne**, a następnie wybierz pozycję **nowy klucz tajny klienta**.
1. Wprowadź **Opis** wpisu tajnego, wybierz pozycję Wygaśnięcie, a następnie wybierz pozycję **Dodaj**. Zapisz **wartość** wpisu tajnego do użycia w późniejszym kroku.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz aplikacji utworzony w dzierżawie Azure AD B2C.

1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. Wybierz **Filtr katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady**wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje**wybierz pozycję `Manual`.
1. Wprowadź **nazwę** klucza zasad. Na przykład `ContosoAppSecret`.  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza podczas jego tworzenia, więc jego odwołanie w kodzie XML w poniższej sekcji ma *B2C_1A_ContosoAppSecret*.
1. W **kluczu tajnym**wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W obszarze **użycie klucza**wybierz pozycję `Signature`.
1. Wybierz pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu usługi Azure AD, musisz zdefiniować usługę Azure AD jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń, dodając usługę Azure AD do elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik *TrustFrameworkExtensions. XML* .
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```XML
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. W elemencie **ClaimsProvider** należy zaktualizować wartość dla **domeny** do unikatowej wartości, która może być używana do odróżnienia od innych dostawców tożsamości. Na przykład `Contoso`. Nie umieszczasz `.com` na końcu tego ustawienia domeny.
5. W elemencie **ClaimsProvider** zaktualizuj wartość parametru **DisplayName** do przyjaznej nazwy dostawcy oświadczeń. Ta wartość nie jest obecnie używana.

### <a name="update-the-technical-profile"></a>Aktualizowanie profilu technicznego

Aby uzyskać token z punktu końcowego usługi Azure AD, należy zdefiniować protokoły, które Azure AD B2C powinny być używane do komunikacji z usługą Azure AD. Jest to wykonywane w elemencie **profilu technicznym** elementu **ClaimsProvider**.

1. Zaktualizuj identyfikator elementu **profilu technicznym** . Ten identyfikator służy do odwoływania się do tego profilu technicznego z innych części zasad.
1. Zaktualizuj wartość parametru **DisplayName**. Ta wartość będzie wyświetlana na przycisku logowania na ekranie logowania.
1. Zaktualizuj wartość w polu **Opis**.
1. Usługa Azure AD korzysta z protokołu Connect OpenID Connect, więc upewnij się, że wartość **protokołu** to `OpenIdConnect`.
1. Ustaw wartość **metadanych** na `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration`, gdzie `your-AD-tenant-name` jest nazwą dzierżawy usługi Azure AD. Na przykład:`https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`
1. Otwórz przeglądarkę i przejdź do adresu URL **metadanych** , który właśnie został zaktualizowany, Wyszukaj obiekt **Issuer** , a następnie skopiuj i wklej wartość do wartości **ProviderName** w pliku XML.
1. Ustaw **client_id** na identyfikator aplikacji z rejestracji aplikacji.
1. W obszarze **CryptographicKeys**zaktualizuj wartość **identyfikatorze storagereferenceid** do nazwy utworzonego wcześniej klucza zasad. Na przykład `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Teraz zasady zostały skonfigurowane tak, aby Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przekazać plik rozszerzenia zasad tylko, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
1. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions. XML* .
1. Kliknij przycisk **Przekaż**.

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest jeszcze dostępny na żadnej stronie rejestracji/logowania. Aby udostępnić ten element, Utwórz duplikat istniejącej przez użytkownika szablonu, a następnie zmodyfikuj go tak, aby miał także dostawcę tożsamości usługi Azure AD:

1. Otwórz plik *TrustFrameworkBase. XML* z pakietu początkowego.
1. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , która zawiera `Id="SignUpOrSignIn"`.
1. Otwórz *plik TrustFrameworkExtensions. XML* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
1. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
1. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInContoso`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na stronie rejestracji/logowania. Jeśli dodasz element **ClaimsProviderSelection** dla usługi Azure AD, zostanie wyświetlony nowy przycisk, gdy użytkownik zostanie wystawiony na stronie.

1. Znajdź element **OrchestrationStep** , który zawiera `Order="1"` w podróży użytkownika, który został utworzony w *TrustFrameworkExtensions. XML*.
1. W obszarze **ClaimsProviderSelections**Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikacji z usługą Azure AD w celu uzyskania tokenu. Połącz przycisk z akcją, łącząc profil techniczny dostawcy oświadczeń usługi Azure AD:

1. Znajdź **OrchestrationStep** , który obejmuje `Order="2"` w podróży użytkownika.
1. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla **identyfikatora** , który został użyty dla **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** na **Identyfikator** utworzonego wcześniej profilu technicznego. Na przykład `ContosoProfile`.

1. Zapisz plik *TrustFrameworkExtensions. XML* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji Azure AD B2C

Komunikacja z Azure AD B2C odbywa się za pomocą aplikacji zarejestrowanej w dzierżawie B2C. W tej sekcji przedstawiono kroki opcjonalne, które można wykonać, aby utworzyć aplikację testową, jeśli nie została jeszcze wykonana.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Utwórz kopię *pliku SignUpOrSignIn. XML* w katalogu roboczym i zmień jego nazwę. Na przykład zmień nazwę na *SignUpSignInContoso. XML*.
1. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInContoso`.
1. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi podróży użytkownika, który został utworzony wcześniej. Na przykład *SignUpSignInContoso*.
1. Zapisz zmiany i Przekaż plik.
1. W obszarze **zasady niestandardowe**wybierz nowe zasady z listy.
1. Z listy rozwijanej **Wybierz aplikację** Wybierz utworzoną wcześniej aplikację Azure AD B2C. Na przykład *testapp1*.
1. Skopiuj **punkt końcowy Uruchom teraz** i otwórz go w prywatnym oknie przeglądarki, na przykład w trybie incognito w przeglądarce Google Chrome lub w oknie InPrivate w programie Microsoft Edge. Otwieranie w prywatnym oknie przeglądarki umożliwia przetestowanie pełnej podróży użytkowników, nie używając obecnie buforowanych poświadczeń usługi Azure AD.
1. Wybierz przycisk Zaloguj usługi Azure AD, na przykład pracownik firmy *contoso*, a następnie wprowadź poświadczenia dla użytkownika w dzierżawie organizacyjnej usługi Azure AD. Zostanie wyświetlona prośba o autoryzację aplikacji, a następnie wprowadzenie informacji dla Twojego profilu.

Jeśli proces logowania zakończy się pomyślnie, przeglądarka zostanie przekierowana do `https://jwt.ms`, która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z zasadami niestandardowymi czasami mogą być potrzebne dodatkowe informacje podczas rozwiązywania problemów z zasadami podczas jej tworzenia.

Aby ułatwić diagnozowanie problemów, można tymczasowo wprowadzić zasady do trybu dewelopera i zebrać dzienniki za pomocą usługi Azure Application Insights. Dowiedz się, jak w [Azure Active Directory B2C: zbieranie dzienników](active-directory-b2c-troubleshoot-custom.md).
