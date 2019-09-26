---
title: Konfigurowanie logowania dla dostawcy tożsamości usługi Azure AD z wieloma dzierżawcami przy użyciu zasad niestandardowych w programie Azure Active Directory B2C
description: Dodawanie dostawcy tożsamości usługi Azure AD z wieloma dzierżawami przy użyciu zasad niestandardowych — Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fdad2f2dfec6f13fe4a40641db3417f29273349c
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315048"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania do Azure Active Directory z wieloma dzierżawcami przy użyciu zasad niestandardowych w programie Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania dla użytkowników przy użyciu wielodostępnego punktu końcowego dla Azure Active Directory (Azure AD) przy użyciu [zasad niestandardowych](active-directory-b2c-overview-custom.md) w programie Azure AD B2C. Dzięki temu użytkownicy z wielu dzierżawców usługi Azure AD mogą logować się przy użyciu Azure AD B2C, bez konieczności konfigurowania dostawcy tożsamości dla każdej dzierżawy. Jednak członkowie gościa w żadnej z tych dzierżawców **nie będą** mogli się zalogować. W tym celu należy [indywidualnie skonfigurować każdą dzierżawę](active-directory-b2c-setup-aad-custom.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Zarejestruj aplikację

Aby włączyć Logowanie użytkowników z określonej organizacji usługi Azure AD, musisz zarejestrować aplikację w ramach organizacji dzierżawy usługi Azure AD.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu zawierającego swoją organizacyjną dzierżawę usługi Azure AD (na przykład contoso.com). Wybierz **Filtr katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog, który zawiera dzierżawę.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład `Azure AD B2C App`.
1. Wybierz **konta w dowolnym katalogu organizacyjnym** dla tej aplikacji.
1. Dla **identyfikatora URI przekierowania**Zaakceptuj wartość **sieci Web**i wprowadź następujący adres URL w postaci małych liter, gdzie `your-B2C-tenant-name` jest zastępowana nazwą dzierżawy Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Wybierz pozycję **Zarejestruj**. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
1. Wybierz pozycję **certyfikaty &** wpisy tajne, a następnie wybierz pozycję **nowy klucz tajny klienta**.
1. Wprowadź **Opis** wpisu tajnego, wybierz pozycję Wygaśnięcie, a następnie wybierz pozycję **Dodaj**. Zapisz **wartość** wpisu tajnego do użycia w późniejszym kroku.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz aplikacji utworzony w dzierżawie Azure AD B2C.

1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. Wybierz **Filtr katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. W obszarze **zasady**wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje**wybierz `Manual`opcję.
1. Wprowadź **nazwę** klucza zasad. Na przykład `AADAppSecret`.  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza podczas jego tworzenia, więc jego odwołanie w kodzie XML w poniższej sekcji ma *B2C_1A_AADAppSecret*.
1. W **kluczu tajnym**wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W obszarze **użycie klucza**wybierz `Signature`opcję.
1. Wybierz pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu usługi Azure AD, musisz zdefiniować usługę Azure AD jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń, dodając usługę Azure AD do elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik *TrustFrameworkExtensions. XML* .
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/common/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. W elemencie **ClaimsProvider** należy zaktualizować wartość dla **domeny** do unikatowej wartości, która może być używana do odróżnienia od innych dostawców tożsamości.
1. W elemencie **profilu technicznym** zaktualizuj wartość parametru **DisplayName**, `Contoso Employee`na przykład. Ta wartość jest wyświetlana na przycisku logowania na stronie logowania.
1. Ustaw **client_id** na identyfikator aplikacji aplikacji wielodostępnej dla wielodostępnej usługi Azure AD, która została zarejestrowana wcześniej.
1. W obszarze **CryptographicKeys**zaktualizuj wartość **identyfikatorze storagereferenceid** do nazwy utworzonego wcześniej klucza zasad. Na przykład `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Ogranicz dostęp

> [!NOTE]
> Użycie `https://sts.windows.net` jako wartości parametru **ValidTokenIssuerPrefixes** umożliwia wszystkim użytkownikom usługi Azure AD logowanie się do aplikacji.

Należy zaktualizować listę prawidłowych wystawców tokenów i ograniczyć dostęp do określonej listy użytkowników dzierżawy usługi Azure AD, którzy mogą się zalogować.

Aby uzyskać wartości, przyjrzyj się metadanych odnajdywania OpenID Connect Connect dla każdej dzierżawy usługi Azure AD, z której chcesz się zalogować. Format adresu URL metadanych jest podobny do `https://login.windows.net/your-tenant/.well-known/openid-configuration`, gdzie `your-tenant` jest nazwą dzierżawy usługi Azure AD. Na przykład:

`https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`

Wykonaj następujące kroki dla każdej dzierżawy usługi Azure AD, która ma zostać użyta do zalogowania:

1. Otwórz przeglądarkę i przejdź do adresu URL metadanych OpenID Connect Connect dla dzierżawy. Znajdź obiekt **Issuer** i Zapisz jego wartość. Powinien wyglądać podobnie do `https://sts.windows.net/00000000-0000-0000-0000-000000000000/`.
1. Skopiuj i wklej wartość do klucza **ValidTokenIssuerPrefixes** . Rozdziel wiele wystawców średnikami. Przykład z dwoma wystawcami pojawia się w poprzednim `ClaimsProvider` przykładzie kodu XML.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Teraz zasady zostały skonfigurowane tak, aby Azure AD B2C wie, jak komunikować się z katalogami usługi Azure AD. Spróbuj przekazać plik rozszerzenia zasad tylko, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions. XML* .
3. Wybierz pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji/logowania. Aby można było go udostępnić, należy utworzyć duplikat istniejącej przejazdu użytkownika szablonu, a następnie zmodyfikować go tak, aby miał także dostawcę tożsamości usługi Azure AD.

1. Otwórz plik *TrustFrameworkBase. XML* z pakietu początkowego.
2. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *plik TrustFrameworkExtensions. XML* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
4. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInContoso`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji/logowania. Jeśli dodasz element **ClaimsProviderSelection** dla usługi Azure AD, zostanie wyświetlony nowy przycisk, gdy użytkownik zostanie wystawiony na stronie.

1. Znajdź element **OrchestrationStep** , który zawiera `Order="1"` w podróży użytkownika, który został utworzony w *TrustFrameworkExtensions. XML*.
1. W obszarze **ClaimsProviderSelects**Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikacji z usługą Azure AD w celu uzyskania tokenu. Połącz przycisk z akcją, łącząc profil techniczny dostawcy oświadczeń usługi Azure AD.

1. Znajdź **OrchestrationStep** obejmujący `Order="2"` w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla **identyfikatora** , który został użyty dla **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** na **Identyfikator** utworzonego wcześniej profilu technicznego. Na przykład `Common-AAD`.

3. Zapisz plik *TrustFrameworkExtensions. XML* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji Azure AD B2C

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP) inicjujący utworzoną przez Ciebie podróż użytkownika:

1. Utwórz kopię *pliku SignUpOrSignIn. XML* w katalogu roboczym i zmień jego nazwę. Na przykład zmień nazwę na *SignUpSignContoso. XML*.
1. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInContoso`.
1. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi podróży użytkownika, który został utworzony wcześniej. Na przykład *SignUpSignInContoso*.
1. Zapisz zmiany i Przekaż plik.
1. W obszarze **zasady niestandardowe**wybierz nowe zasady z listy.
1. Z listy rozwijanej **Wybierz aplikację** Wybierz utworzoną wcześniej aplikację Azure AD B2C. Na przykład *testapp1*.
1. Skopiuj **punkt końcowy Uruchom teraz** i otwórz go w prywatnym oknie przeglądarki, na przykład w trybie incognito w przeglądarce Google Chrome lub w oknie InPrivate w programie Microsoft Edge. Otwieranie w prywatnym oknie przeglądarki umożliwia przetestowanie pełnej podróży użytkowników, nie używając obecnie buforowanych poświadczeń usługi Azure AD.
1. Wybierz przycisk Zaloguj usługi Azure AD, na przykład pracownik firmy *contoso*, a następnie wprowadź poświadczenia dla użytkownika w jednej z dzierżawców organizacyjnych usługi Azure AD. Zostanie wyświetlona prośba o autoryzację aplikacji, a następnie wprowadzenie informacji dla Twojego profilu.

Jeśli proces logowania zakończy się pomyślnie, przeglądarka zostanie przekierowana do `https://jwt.ms`, która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

Aby przetestować możliwość logowania z wieloma dzierżawcami, wykonaj ostatnie dwa kroki przy użyciu poświadczeń dla użytkownika, który istnieje w innej dzierżawie usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z zasadami niestandardowymi czasami mogą być potrzebne dodatkowe informacje podczas rozwiązywania problemów z zasadami podczas jej tworzenia.

Aby ułatwić diagnozowanie problemów, można tymczasowo wprowadzić zasady do trybu dewelopera i zebrać dzienniki za pomocą usługi Azure Application Insights. Dowiedz się, [jak w Azure Active Directory B2C: Zbieranie dzienników](active-directory-b2c-troubleshoot-custom.md).
