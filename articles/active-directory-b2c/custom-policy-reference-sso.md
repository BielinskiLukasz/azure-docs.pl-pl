---
title: Zarządzanie sesjami logowania jednokrotnego przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zarządzać sesjami logowania jednokrotnego przy użyciu zasad niestandardowych w programie Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bdea51c6cb53222f31a07906785a94073a0293a1
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226800"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Zarządzanie sesjami logowania jednokrotnego w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa logowania jednokrotnego (SSO) w programie Azure Active Directory B2C (Azure AD B2C) umożliwia administratorowi sterowanie interakcją z użytkownikiem po jego uwierzytelnieniu. Administrator może na przykład określić, czy jest wyświetlany wybór dostawców tożsamości, czy też należy wprowadzić ponownie szczegóły konta lokalnego. W tym artykule opisano sposób konfigurowania ustawień logowania jednokrotnego dla Azure AD B2C.

Zarządzanie sesjami logowania jednokrotnego ma dwie części. Pierwsza z nich zajmuje się interakcją użytkownika bezpośrednio z Azure AD B2Cami i innymi pochodzącymi w interakcjach z użytkownikami zewnętrznymi, takimi jak Facebook. Azure AD B2C nie przesłania ani nie pomija sesji logowania jednokrotnego, które mogą być przechowywane przez strony zewnętrzne. Zamiast trasy do Azure AD B2C do strony zewnętrznej jest "zapamiętane", unikając konieczności monitowania użytkownika o wybranie dostawcy tożsamości społecznościowej lub korporacyjnej. Ostateczna decyzja dotycząca logowania jednokrotnego pozostaje ze stroną zewnętrzną.

Zarządzanie sesjami SSO korzysta z tej samej semantyki co inny profil techniczny w zasadach niestandardowych. Po wykonaniu kroku aranżacji w profilu technicznym skojarzonym z krokiem jest wysyłana Kwerenda dotycząca odwołania `UseTechnicalProfileForSessionManagement`. Jeśli taki istnieje, oznacza to, że dostawca sesji rejestracji jednokrotnej, którego dotyczy odwołanie, jest sprawdzany w celu sprawdzenia, czy użytkownik jest uczestnikiem sesji. W takim przypadku dostawca sesji logowania jednokrotnego jest używany do ponownego zapełnienia sesji. Podobnie po zakończeniu wykonywania kroku aranżacji dostawca jest używany do przechowywania informacji w sesji, jeśli określono dostawcę sesji logowania jednokrotnego.

Azure AD B2C zdefiniował wielu dostawców sesji rejestracji jednokrotnej, których można użyć:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Klasy zarządzania logowaniem jednokrotnym są określane przy użyciu elementu `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` profilu technicznego.

## <a name="input-claims"></a>Oświadczenia wejściowe

Element `InputClaims` jest pusty lub nie istnieje.

## <a name="persisted-claims"></a>Utrwalone oświadczenia

Oświadczenia, które muszą zostać zwrócone do aplikacji lub używane przez warunki wstępne w kolejnych krokach, powinny być przechowywane w sesji lub uzupełnione przez odczyt z profilu użytkownika w katalogu. Korzystanie z utrwalonych oświadczeń gwarantuje, że w przypadku brakujących oświadczeń przejazdów uwierzytelniania nie powiedzie się. Aby dodać oświadczenia w sesji, użyj elementu `<PersistedClaims>` w profilu technicznym. Gdy dostawca jest używany do ponownego wypełniania sesji, utrwalane oświadczenia są dodawane do zbioru oświadczeń.

## <a name="output-claims"></a>Oświadczenia wyjściowe

`<OutputClaims>` jest używany do pobierania oświadczeń z sesji.

## <a name="session-providers"></a>Dostawcy sesji

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Jak nazywa się, ten dostawca niczego nie robi. Ten dostawca może służyć do pomijania zachowania logowania jednokrotnego dla określonego profilu technicznego. Następujący `SM-Noop` profil techniczny jest zawarty w [pakiecie startowym zasad niestandardowych](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Tego dostawcy można używać do przechowywania oświadczeń w sesji. Ten dostawca jest zwykle przywoływany w profilu technicznym używanym do zarządzania kontami lokalnymi. Następujący `SM-AAD` profil techniczny jest zawarty w [pakiecie startowym zasad niestandardowych](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

Poniższy `SM-MFA` profil techniczny znajduje się w `SocialAndLocalAccountsWithMfa`[zasad niestandardowych pakietu](custom-policy-get-started.md#custom-policy-starter-pack) . Ten profil techniczny służy do zarządzania sesją uwierzytelniania wieloskładnikowego.

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ten dostawca służy do pomijania ekranu "wybieranie dostawcy tożsamości". Zwykle jest to przywoływane w profilu technicznym skonfigurowanym dla zewnętrznego dostawcy tożsamości, takiego jak Facebook. Następujący `SM-SocialLogin` profil techniczny jest zawarty w [pakiecie startowym zasad niestandardowych](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Nie | Obecnie nie są używane, można je zignorować. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ten dostawca służy do zarządzania Azure AD B2C sesji SAML między aplikacją jednostki uzależnionej lub dostawcą federacyjnego tożsamości SAML. Gdy jest używany dostawca rejestracji jednokrotnej do przechowywania sesji dostawcy tożsamości SAML, `RegisterServiceProviders` musi być ustawiona na `false`. Następujący `SM-Saml-idp` profil techniczny jest używany przez [profil techniczny SAML](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

W przypadku korzystania z dostawcy do przechowywania sesji B2C SAML, `RegisterServiceProviders` musi mieć ustawioną `true`. Wylogowanie sesji SAML wymaga wykonania `SessionIndex` i `NameID`.

Następujący `SM-Saml-idp` profil techniczny jest używany przez [profil techniczny wystawcy SAML](connect-with-saml-service-providers.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis|
| --- | --- | --- |
| IncludeSessionIndex | Nie | Obecnie nie są używane, można je zignorować.|
| RegisterServiceProviders | Nie | Wskazuje, że dostawca powinien rejestrować wszystkich dostawców usług SAML, którzy wystawiły potwierdzenie. Możliwe wartości: `true` (wartość domyślna) lub `false`.|



