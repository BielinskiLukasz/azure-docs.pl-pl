---
title: Przykłady logicznej transformacji oświadczeń dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady logicznej transformacji oświadczeń dla schematu programu Identity Experience Framework (IEF) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e470ea65085bf71f0052567d5bf367661852d1cb
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268022"
---
# <a name="boolean-claims-transformations"></a>Przekształcenia logiczne oświadczeń

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń logicznej oświadczeń schematu programu Identity Experience Framework w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>Wnoszonych

Wykonuje operację i dwie inputClaims logiczne i ustawia oświadczenie outputclaim z wynikiem operacji.

| Element  | TransformationClaimType  | Typ danych  | Uwagi |
|-------| ------------------------ | ---------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | wartość logiczna | Pierwszy ClaimType do obliczenia. |
| Oświadczenie inputclaim | inputClaim2  | wartość logiczna | Drugie wyrażenie Claim, które ma zostać obliczone. |
|Oświadczenie outputclaim | Oświadczenie outputclaim | wartość logiczna | Oświadczenia są tworzone po wywołaniu tej transformacji oświadczeń (true lub false). |

Następująca transformacja oświadczeń pokazuje, jak i dwa elementy Claims Boolean: `isEmailNotExist`i `isSocialAccount`. `presentEmailSelfAsserted` oświadczenia wyjściowego jest ustawiona na `true`, jeśli wartość obu oświadczeń wejściowych jest `true`. W kroku aranżacji możesz użyć warunku wstępnego, aby wstępnie zapewnieł stronę, tylko jeśli adres e-mail konta społecznościowego jest pusty.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **inputClaim1**: true
    - **inputClaim2**: FAŁSZ
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: FAŁSZ


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Sprawdza, czy wartości logiczne dwóch oświadczeń są równe, i zgłasza wyjątek, jeśli nie.

| Element | TransformationClaimType  | Typ danych  | Uwagi |
| ---- | ------------------------ | ---------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | wartość logiczna | Wartość oświadczenia, która ma zostać potwierdzona. |
| InputParameter |valueToCompareTo | wartość logiczna | Wartość do porównania (true lub false). |

Przekształcenie oświadczeń **AssertBooleanClaimIsEqualToValue** jest zawsze wykonywane z poziomu [weryfikacji profilu technicznego](validation-technical-profile.md) , który jest wywoływany przez [własny profil techniczny](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** z własnym profilem technicznym metadane są kontrolowane komunikat o błędzie, który zostanie wyświetlony przez profil techniczny.

![AssertStringClaimsAreEqual wykonywanie](./media/boolean-transformations/assert-execution.png)

Następująca transformacja oświadczeń pokazuje, jak sprawdzić wartość elementu ClaimType Boolean z wartością `true`. Jeśli wartość elementu `accountEnabled` ClaimType ma wartość false, zostanie zgłoszony komunikat o błędzie.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Profil techniczny weryfikacji `login-NonInteractive` wywołuje transformację `AssertAccountEnabledIsTrue` oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Profil techniczny z własnym potwierdzeniem wywołuje profil techniczny **logowania weryfikacji — nieinteraktywny** .

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: FAŁSZ
    - **valueToCompareTo**: true
- Wynik: zgłoszono błąd

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Sprawdza, czy wartość logiczna żądania jest równa `true` lub `false`, i zwraca wynik kompresji.

| Element | TransformationClaimType  | Typ danych  | Uwagi |
| ---- | ------------------------ | ---------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | wartość logiczna | Wartość oświadczenia, która ma zostać potwierdzona. |
| InputParameter |valueToCompareTo | wartość logiczna | Wartość do porównania (true lub false). |
| Oświadczenie outputclaim | compareResult | wartość logiczna | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |


Następująca transformacja oświadczeń pokazuje, jak sprawdzić wartość elementu ClaimType Boolean z wartością `true`. Jeśli wartość `IsAgeOver21Years` ClaimType jest równa `true`, transformacja oświadczeń zwraca `true`, w przeciwnym razie `false`.

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
      <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: FAŁSZ
- Parametry wejściowe:
    - **valueToCompareTo**: true
- Oświadczenia wyjściowe:
    - **compareResult**: FAŁSZ



## <a name="notclaims"></a>NotClaims

Wykonuje operację not dla oświadczenie inputclaim logicznego i ustawia oświadczenie outputclaim z wynikiem operacji.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | wartość logiczna | Zastrzeżenie, które ma być obsługiwane. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | wartość logiczna | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation (true lub false). |

Ta transformacja jest używana do wykonywania logicznego negacji dla żądania.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: FAŁSZ
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true

## <a name="orclaims"></a>OrClaims

Oblicza wartość lub z dwóch inputClaims logicznych i ustawia oświadczenie outputclaim z wynikiem operacji.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | wartość logiczna | Pierwszy ClaimType do obliczenia. |
| Oświadczenie inputclaim | inputClaim2 | wartość logiczna | Drugie wyrażenie Claim, które ma zostać obliczone. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | wartość logiczna | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation (true lub false). |

Następująca transformacja oświadczeń pokazuje, jak `Or` dwa elementy Claims Boolean. W kroku aranżacji można użyć warunku wstępnego, aby wstępnie zatwierdzić stronę, jeśli wartość jednego z oświadczeń jest `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **inputClaim1**: true
    - **inputClaim2**: FAŁSZ
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true
