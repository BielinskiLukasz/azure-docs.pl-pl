---
title: Konfigurowanie logowania za pomocą konta Amazon przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Skonfiguruj logowanie się przy użyciu konta Amazon w Azure Active Directory B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4a0767938f8a470c63ec7fe3d7789212b1baf871
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846629"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania za pomocą konta Amazon przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania dla użytkowników z konta Amazon przy użyciu [zasad niestandardowych](custom-policy-overview.md) w programie Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md).
- Jeśli nie masz jeszcze konta Amazon, utwórz je na [https://www.amazon.com/](https://www.amazon.com/).

## <a name="register-the-application"></a>Rejestrowanie aplikacji

Aby włączyć Logowanie użytkowników z konta Amazon, należy utworzyć aplikację Amazon.

1. Zaloguj się do [Centrum deweloperów Amazon](https://login.amazon.com/) przy użyciu poświadczeń konta usługi Amazon.
2. Jeśli jeszcze tego nie zrobiono, kliknij pozycję **Utwórz konto**, postępuj zgodnie z procedurami rejestracji dla deweloperów i zaakceptuj zasady.
3. Wybierz pozycję **zarejestruj nową aplikację**.
4. Wprowadź **nazwę**, **Opis**i **adres URL informacji o ochronie prywatności**, a następnie kliknij przycisk **Zapisz**. Powiadomienie o zasadach zachowania poufności informacji to strona, którą można zarządzać, która zapewnia użytkownikom informacje o ochronie prywatności.
5. W sekcji **Ustawienia sieci Web** Skopiuj wartości **Identyfikator klienta**. Wybierz pozycję **Pokaż klucz tajny** , aby uzyskać klucz tajny klienta, a następnie skopiuj go. Musisz mieć oba te elementy, aby skonfigurować konto Amazon jako dostawcę tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
6. W sekcji **Ustawienia sieci Web** wybierz pozycję **Edytuj**, a następnie wprowadź `https://your-tenant-name.b2clogin.com` w polu **dozwolone źródła języka JavaScript** i `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w polu **dozwolone adresy URL**. Zastąp `your-tenant-name` nazwą dzierżawy. Podczas wprowadzania nazwa dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z wielkich liter w usłudze Azure AD B2C, należy używać małych liter.
7. Kliknij pozycję **Zapisz**.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny klienta, który został wcześniej zarejestrowany w dzierżawie Azure AD B2C.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje**wybierz pozycję `Manual`.
7. Wprowadź **nazwę** klucza zasad. Na przykład `AmazonSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **kluczu tajnym**wprowadź wcześniej zarejestrowany klucz tajny klienta.
9. W obszarze **użycie klucza**wybierz pozycję `Signature`.
10. Kliknij pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu konta Amazon, musisz zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto Amazon można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.


1. Otwórz *plik TrustFrameworkExtensions. XML*.
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ustaw **client_id** na identyfikator aplikacji z rejestracji aplikacji.
5. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Teraz zasady zostały skonfigurowane tak, aby Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przekazać plik rozszerzenia zasad tylko, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions. XML* .
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji/logowania. Aby można było go udostępnić, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał także dostawcę tożsamości Amazon.

1. Otwórz plik *TrustFrameworkBase. XML* z pakietu początkowego.
2. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *plik TrustFrameworkExtensions. XML* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
4. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInAmazon`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji/logowania. Jeśli dodasz element **ClaimsProviderSelection** dla konta Amazon, zostanie wyświetlony nowy przycisk, gdy użytkownik zostanie wystawiony na stronie.

1. Znajdź element **OrchestrationStep** , który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelects**Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `AmazonExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikowania się z kontem Amazon w celu uzyskania tokenu.

1. Znajdź **OrchestrationStep** , który zawiera `Order="2"` w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla identyfikatora, który został użyty dla **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** na identyfikator utworzonego wcześniej profilu technicznego. Na przykład `Amazon-OAuth`.

3. Zapisz plik *TrustFrameworkExtensions. XML* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji Azure AD B2C

Komunikacja z Azure AD B2C odbywa się za pomocą aplikacji zarejestrowanej w dzierżawie B2C. W tej sekcji przedstawiono kroki opcjonalne, które można wykonać, aby utworzyć aplikację testową, jeśli nie została jeszcze wykonana.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Utwórz kopię *pliku SignUpOrSignIn. XML* w katalogu roboczym i zmień jego nazwę. Na przykład zmień nazwę na *SignUpSignInAmazon. XML*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInAmazon`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_amazon`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi nowej podróży użytkownika, która została utworzona (SignUpSignAmazon).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady z listy.
6. Upewnij się, że utworzona aplikacja Azure AD B2C została wybrana w polu **Wybierz aplikację** , a następnie przetestuj ją, klikając polecenie **Uruchom teraz**.
