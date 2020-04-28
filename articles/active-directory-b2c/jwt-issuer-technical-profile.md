---
title: Zdefiniuj profil techniczny dla wystawcy JWT w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny dla wystawców tokenów sieci Web JSON (JWT) w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c23648d70192607b2a5b977dcdd445931e995154
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78671810"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny dla wystawcy token JWT w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje kilka typów tokenów zabezpieczających podczas przetwarzania poszczególnych przepływów uwierzytelniania. Profil techniczny wystawcy token JWT emituje token JWT zwracanego z powrotem do aplikacji jednostki uzależnionej. Zazwyczaj ten profil techniczny to ostatni krok aranżacji w podróży użytkownika.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **name** elementu **Protocol** musi być ustawiony na `None`. Ustaw element **OutputTokenFormat** na `JWT`wartość.

W poniższym przykładzie przedstawiono profil techniczny dla `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Oświadczenia danych wejściowych, wyjściowych i utrwalania

Elementy **InputClaims**, **OutputClaims**i **PersistClaims** są puste lub nie istnieją. Obecne są również elementy **InutputClaimsTransformations** i **OutputClaimsTransformations** .

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Tak | Żądanie, które ma być używane jako żądanie tożsamości użytkownika w ramach kodów autoryzacji OAuth2 i odświeżanie tokenów. Domyślnie należy ją ustawić na `objectId`wartość, chyba że określono inny typ SubjectNamingInfo. |
| SendTokenResponseBodyWithJsonNumbers | Nie | Zawsze ustawiaj `true`na. W przypadku starszego formatu, gdzie wartości liczbowe są podawane jako ciągi zamiast liczb JSON, `false`ustaw wartość. Ten atrybut jest wymagany w przypadku klientów, którzy korzystali z zależności od wcześniejszej implementacji, która zwróciła takie właściwości jako ciągi. |
| token_lifetime_secs | Nie | Okresy istnienia tokenu dostępu. Okres istnienia tokenu okaziciela OAuth 2,0, który służy do uzyskiwania dostępu do chronionego zasobu. Wartość domyślna to 3 600 sekund (1 godzina). Minimalna (włącznie) to 300 sekund (5 minut). Wartość maksymalna (włącznie) to 86 400 sekund (24 godziny). |
| id_token_lifetime_secs | Nie | Okresy istnienia tokenów identyfikatorów. Wartość domyślna to 3 600 sekund (1 godzina). Minimalna (włącznie) to 300 sekund (5 minut). Wartość maksymalna (włącznie) jest sekund 86 400 (24 godziny). |
| refresh_token_lifetime_secs | Nie | Odświeżanie okresów istnienia tokenu. Maksymalny przedział czasu, przez który token odświeżania może zostać użyty do uzyskania nowego tokenu dostępu, jeśli Twoja aplikacja uzyskała zakres offline_access. Wartość domyślna to 120, 9600 sekund (14 dni). Minimalna (włącznie) to 86 400 sekund (24 godziny). Wartość maksymalna (włącznie) to 7 776 000 sekund (90 dni). |
| rolling_refresh_token_lifetime_secs | Nie | Zaodświeżaj okres istnienia okna przewijania tokenu. Po upływie tego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od okresu ważności ostatniego tokenu odświeżania nabytego przez aplikację. Jeśli nie chcesz wymusić okresu istnienia ruchomego okna, ustaw wartość allow_infinite_rolling_refresh_token na `true`. Wartość domyślna to 7 776 000 sekund (90 dni). Minimalna (włącznie) to 86 400 sekund (24 godziny). Wartość maksymalna (włącznie) to 31 536 000 sekund (365 dni). |
| allow_infinite_rolling_refresh_token | Nie | Jeśli jest ustawiona `true`na, okres istnienia okna przewijania tokenu odświeżania nigdy nie wygasa. |
| IssuanceClaimPattern | Nie | Kontroluje wierzytelność wystawcy (ISS). Jedna z wartości:<ul><li>AuthorityAndTenantGuid — zgłoszenie ISS obejmuje nazwę domeny, taką jak `login.microsoftonline` lub `tenant-name.b2clogin.com`, i identyfikator dzierżawy https:\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp — zgłoszenie ISS obejmuje nazwę domeny, taką jak `login.microsoftonline` lub `tenant-name.b2clogin.com`, identyfikator dzierżawy i nazwę zasad jednostki uzależnionej. https:\//login.microsoftonline.com/TFP/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-Sign-in/v2.0/</li></ul> Wartość domyślna: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | Nie | Kontroluje wartość `acr` żądania.<ul><li>Brak — Azure AD B2C nie wystawia żądania ACR</li><li>PolicyId — to `acr` zgłoszenie zawiera nazwę zasad</li></ul>Opcje ustawiania tej wartości to TFP (zasady struktury zaufania) i ACR (odwołanie kontekstu uwierzytelniania). Zaleca się ustawienie tej wartości na TFP, aby ustawić wartość, upewnij się, że `<Item>` z `Key="AuthenticationContextReferenceClaimPattern"` istnieje i wartość jest `None`. W zasadach jednostki uzależnionej Dodaj `<OutputClaims>` element, Dodaj ten element. `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />` Upewnij się również, że zasady zawierają typ zgłoszenia`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Nie | Identyfikator przejazdu użytkownika, który powinien zostać wykonany podczas odświeżania żądania opublikowania [tokenu dostępu](authorization-code-flow.md#4-refresh-the-token) do `/token` punktu końcowego. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element CryptographicKeys zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| issuer_secret | Tak | Certyfikat x509 (zestaw kluczy RSA) służący do podpisywania tokenu JWT. Jest to `B2C_1A_TokenSigningKeyContainer` klucz, który można znaleźć w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). |
| issuer_refresh_token_key | Tak | Certyfikat x509 (zestaw kluczy RSA) służący do szyfrowania tokenu odświeżania. `B2C_1A_TokenEncryptionKeyContainer` Klucz został skonfigurowany w temacie [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md) |














