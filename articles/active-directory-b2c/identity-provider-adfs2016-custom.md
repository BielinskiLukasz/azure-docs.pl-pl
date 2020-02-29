---
title: Dodawanie usług AD FS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Konfigurowanie usług AD FS 2016 przy użyciu protokołu SAML i zasad niestandardowych w Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bfe39d9528927f995d14772e07e02b2a0528e5e0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188532"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie usług AD FS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania do konta użytkownika usług AD FS przy użyciu [zasad niestandardowych](custom-policy-overview.md) w programie Azure Active Directory B2C (Azure AD B2C). Aby włączyć logowanie, Dodaj [profil techniczny SAML](saml-technical-profile.md) do zasad niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Azure Active Directory B2C](custom-policy-get-started.md).
- Upewnij się, że masz dostęp do pliku PFX certyfikatu z kluczem prywatnym. Możesz wygenerować własny podpisany certyfikat i przekazać go do Azure AD B2C. Azure AD B2C używa tego certyfikatu do podpisywania żądania SAML wysyłanego do dostawcy tożsamości SAML.
- Aby system Azure zaakceptował hasło pliku PFX, hasło musi być zaszyfrowane za pomocą opcji TripleDES-SHA1 w narzędziu eksportu magazynu certyfikatów systemu Windows, a nie AES256-SHA256.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Musisz przechowywać certyfikat w dzierżawie Azure AD B2C.

1. Zaloguj się do [Azure portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje**wybierz pozycję `Upload`.
7. Wprowadź **nazwę** klucza zasad. Na przykład `SamlCert`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. Przejdź do pliku certyfikatu PFX z kluczem prywatnym i wybierz go.
9. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu konta usług AD FS, musisz zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto usług AD FS można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad. Aby uzyskać więcej informacji, zobacz [Zdefiniuj profil techniczny SAML](saml-technical-profile.md).

1. Otwórz *plik TrustFrameworkExtensions. XML*.
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zastąp `your-ADFS-domain` nazwą domeny usług AD FS i Zastąp wartość **identityProvider** wynikowe w systemie DNS (arbitralną wartość wskazującą domenę).

1. Znajdź sekcję `<ClaimsProviders>` i Dodaj następujący fragment kodu XML. Jeśli zasady zawierają już profil techniczny `SM-Saml-idp`, przejdź do następnego kroku. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie sesjami logowania](custom-policy-reference-sso.md)jednokrotnego.

    ```XML
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zapisz plik.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Teraz skonfigurowano zasady, aby Azure AD B2C wie, jak komunikować się z kontem usług AD FS. Spróbuj przekazać plik rozszerzenia zasad tylko, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions. XML* .
3. Kliknij pozycję **Przekaż**.

> [!NOTE]
> Rozszerzenie Visual Studio Code B2C używa "socialIdpUserId". Zasady społecznościowe są również wymagane dla usług AD FS.
>

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji lub logowania. Aby można było go udostępnić, należy utworzyć duplikat istniejącej przejazdu użytkownika szablonu, a następnie zmodyfikować go tak, aby miał także dostawcę tożsamości usług ADFS.

1. Otwórz plik *TrustFrameworkBase. XML* z pakietu początkowego.
2. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"`.
3. Otwórz *plik TrustFrameworkExtensions. XML* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
4. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInADFS`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz element **ClaimsProviderSelection** dla konta usług AD FS, nowy przycisk będzie wyświetlany, gdy użytkownik zostanie wystawiony na stronie.

1. Znajdź element **OrchestrationStep** , który zawiera `Order="1"` w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelections**Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikowania się z kontem usług AD FS w celu uzyskania tokenu.

1. Znajdź **OrchestrationStep** , który zawiera `Order="2"` w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla identyfikatora, który został użyty dla **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** na identyfikator utworzonego wcześniej profilu technicznego. Na przykład `Contoso-SAML2`.

3. Zapisz plik *TrustFrameworkExtensions. XML* i przekaż go ponownie w celu weryfikacji.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurowanie zaufania jednostki uzależnionej usług ADFS

Aby użyć usług AD FS jako dostawcy tożsamości w Azure AD B2C, należy utworzyć relację zaufania jednostki uzależnionej ADFS z Azure AD B2C metadanych SAML. Poniższy przykład przedstawia adres URL metadanych SAML Azure AD B2C profilu technicznego:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **dzierżawy** z nazwą dzierżawy, taką jak Your-tenant.onmicrosoft.com.
- **Twoje zasady** z nazwą zasad. Na przykład B2C_1A_signup_signin_adfs.
- **Twój profil techniczny** o nazwie profilu technicznego dostawcy tożsamości SAML. Na przykład contoso-SAML2.

Otwórz przeglądarkę i przejdź do adresu URL. Upewnij się, że wpisany adres URL jest poprawny i że masz dostęp do pliku metadanych XML. Aby dodać nowe zaufanie jednostki uzależnionej za pomocą przystawki Zarządzanie usługami AD FS i ręcznie skonfigurować ustawienia, należy wykonać poniższą procedurę na serwerze federacyjnym. Minimalnym wymaganiem do wykonania tej procedury jest członkostwo w **grupie Administratorzy** lub równoważnej na komputerze lokalnym.

1. W Menedżer serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Zarządzanie usługami AD FS**.
2. Wybierz pozycję **Dodaj zaufanie jednostki uzależnionej**.
3. Na stronie **powitalnej** wybierz pozycję **oświadczenia**, a następnie kliknij przycisk **Uruchom**.
4. Na stronie **Wybierz źródło danych** wybierz pozycję **Importuj dane dotyczące jednostki uzależnionej Publikuj online lub w sieci lokalnej**, podaj adres URL metadanych Azure AD B2C, a następnie kliknij przycisk **dalej**.
5. Na stronie **Określanie nazwy wyświetlanej** wprowadź **nazwę wyświetlaną**, w obszarze **uwagi**wprowadź opis tego zaufania jednostki uzależnionej, a następnie kliknij przycisk **dalej**.
6. Na stronie **Wybierz zasady Access Control** wybierz zasady, a następnie kliknij przycisk **dalej**.
7. Na stronie **Wszystko gotowe do dodania zaufania** przejrzyj ustawienia, a następnie kliknij przycisk **Dalej**, aby zapisać informacje o zaufaniu jednostki uzależnionej.
8. Na stronie **zakończenie** kliknij przycisk **Zamknij**, ta akcja spowoduje automatyczne wyświetlenie okna dialogowego **Edytowanie reguł dotyczących roszczeń** .
9. Wybierz pozycję **Dodaj regułę**.
10. W **Szablon reguł oświadczeń**, wybierz opcję **Wyślij atrybuty LDAP jako oświadczenia**.
11. Podaj **nazwę reguły dla roszczeń**. W polu **magazyn atrybutów**wybierz pozycję **Wybierz Active Directory**, Dodaj następujące oświadczenia, a następnie kliknij przycisk **Zakończ** i **OK**.

    | Atrybut LDAP | Typ zgłoszenia wychodzącego |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Nazwisko | family_name |
    | Imię i nazwisko | given_name |
    | E-Mail-Address | e-mail |
    | Nazwa wyświetlana | name |

    Należy zauważyć, że te nazwy nie będą wyświetlane na liście rozwijanej Typ zgłoszenia wychodzącego. Należy ręcznie wpisać je w. (Lista rozwijana jest w rzeczywistości edytowalna).

12.  Na podstawie typu certyfikatu może być konieczne ustawienie algorytmu wyznaczania wartości skrótu. W oknie Właściwości zaufania jednostki uzależnionej (Demonstracja B2C) wybierz kartę **Zaawansowane** i Zmień **algorytm Secure Hash** na `SHA-256`, a następnie kliknij przycisk **OK**.
13. W Menedżer serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Zarządzanie usługami AD FS**.
14. Wybierz utworzoną relację zaufania jednostki uzależnionej, wybierz pozycję **Aktualizuj z metadanych Federacji**, a następnie kliknij przycisk **Aktualizuj**.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji Azure AD B2C

Komunikacja z Azure AD B2C odbywa się za pomocą aplikacji zarejestrowanej w dzierżawie B2C. W tej sekcji przedstawiono kroki opcjonalne, które można wykonać, aby utworzyć aplikację testową, jeśli nie została jeszcze wykonana.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Utwórz kopię *pliku SignUpOrSignIn. XML* w katalogu roboczym i zmień jego nazwę. Na przykład zmień nazwę na *SignUpSignInADFS. XML*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInADFS`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi nowej podróży użytkownika, która została utworzona (SignUpSignInADFS).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady z listy.
6. Upewnij się, że utworzona aplikacja Azure AD B2C została wybrana w polu **Wybierz aplikację** , a następnie przetestuj ją, klikając polecenie **Uruchom teraz**.

