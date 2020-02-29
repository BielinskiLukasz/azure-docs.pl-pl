---
title: ClaimsProviders — Azure Active Directory B2C | Microsoft Docs
description: Określ element ClaimsProvider zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189773"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dostawca oświadczeń zawiera zestaw [profilów technicznych](technicalprofiles.md). Każdy dostawca oświadczeń musi mieć co najmniej jeden profil techniczny, który określa punkty końcowe i protokoły wymagane do komunikowania się z dostawcą oświadczeń. Dostawca oświadczeń może mieć wiele profilów technicznych. Można na przykład zdefiniować wiele profilów technicznych, ponieważ dostawca oświadczeń obsługuje wiele protokołów, różne punkty końcowe z różnymi możliwościami lub zwalnia różne oświadczenia na różnych poziomach gwarancji. Możliwe jest zaakceptowanie poufnych oświadczeń w jednej podróży użytkownika, ale nie w innej.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

Element **ClaimsProviders** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | Akredytowany Dostawca oświadczeń, który może być używany w różnych przejazdach użytkownika. |

## <a name="claimsprovider"></a>ClaimsProvider

Element **ClaimsProvider** zawiera następujące elementy podrzędne:

| Element | Wystąpień | Opis |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Ciąg, który zawiera nazwę domeny dostawcy zgłoszeń. Na przykład jeśli dostawca oświadczeń zawiera profil techniczny w serwisie Facebook, nazwa domeny to Facebook.com. Ta nazwa domeny jest używana dla wszystkich profilów technicznych zdefiniowanych w dostawcy oświadczeń, chyba że zostanie zastąpiona przez profil techniczny. Nazwa domeny może być również przywoływana w **domain_hint**. Aby uzyskać więcej informacji, zobacz sekcję logowanie za pomocą funkcji **przekierowanie do dostawcy społecznościowego** [Konfigurowanie bezpośredniego logowania przy użyciu Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Ciąg, który zawiera nazwę dostawcy oświadczeń. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Zestaw profilów technicznych obsługiwanych przez dostawcę usług |

**ClaimsProvider** organizuje sposób, w jaki profile techniczne odnoszą się do dostawcy oświadczeń. Poniższy przykład przedstawia dostawcę oświadczeń Azure Active Directory z profilami technicznymi Azure Active Directory:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Poniższy przykład przedstawia dostawcę oświadczeń Facebook z profilem technicznym **Facebook-OAuth** .

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
