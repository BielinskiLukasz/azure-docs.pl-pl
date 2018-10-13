---
title: Data przykłady przekształcania oświadczeń tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Data oświadczeń przykłady przekształcania tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b287e7f3846de4391de02cce2cedd6a5df3cbc4a
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167651"
---
# <a name="date-claims-transformations"></a>Data oświadczeń przekształcenia

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera przykłady dotyczące używania przekształcenia oświadczeń daty schematu, struktura środowiska tożsamości w usłudze Azure Active Directory (Azure AD) B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Sprawdza, czy jeden daty i godziny oświadczenia (string — typ danych) jest większy niż drugi daty i czasu oświadczeń (string — typ danych) i zgłasza wyjątek.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | leftOperand | ciąg | Typ pierwszego oświadczenia, który powinien być większy niż drugi oświadczenia. |
| Oświadczenie InputClaim | rightOperand | ciąg | Typ drugiego oświadczenia, która powinna być mniejsza od pierwszego oświadczenia. |
| InputParameter | AssertIfEqualTo | wartość logiczna | Określa, czy ta asercja należy przekazać, jeśli lewy operand jest równa prawy operand. |
| InputParameter | AssertIfRightOperandIsNotPresent | wartość logiczna | Określa, czy ta asercja należy przekazywać, jeśli brakuje prawy operand. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Określa liczbę milisekund, aby umożliwić między tymi dwoma daty i godziny wziąć pod uwagę czas, w którym równe (na przykład konto dla zegara). |

**AssertDateTimeIsGreaterThan** przekształcania oświadczeń jest zawsze wykonywana z [profilu technicznego weryfikacji](validation-technical-profile.md) który jest wywoływany [własnym potwierdzone profilu technicznego](self-asserted-technical-profile.md). **DateTimeGreaterThan** samodzielnie profilu technicznego określa profil techniczny wyświetlane dla użytkownika komunikat o błędzie.

![Wykonanie AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

W poniższym przykładzie porównano `currentDateTime` oświadczenia `approvedDateTime` oświadczenia. Błąd jest generowany, jeśli `currentDateTime` jest większa niż `approvedDateTime`. Transformacja traktuje wartości jako równe, jeśli są one w ciągu 5 minut (w milisekundach 30000) różnicę.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

`login-NonInteractive` Wywołania profilu technicznego weryfikacji `AssertApprovedDateTimeLaterThanCurrentDateTime` przekształcania oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samodzielnie profilu technicznego wywołuje weryfikacji **logowania nieinterakcyjnego** profilu technicznego.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Wynik: Zgłoszony błąd


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Konwertuje **data** oświadczenia do **daty/godziny** typu oświadczenia. Przekształcanie oświadczeń konwertuje format czasu i dodaje 12:00:00 AM do daty.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Oświadczenie InputClaim | date | Oświadczenia, który ma zostać przekonwertowany. |
| oświadczenie outputClaim | oświadczenie outputClaim | Data i godzina | Typ oświadczenia, które są generowane po wywołaniu tego ClaimsTransformation. |

W poniższym przykładzie pokazano konwersji oświadczenia `dateOfBirth` (Data — typ danych) do innego roszczenia `dateOfBirthWithTime` (typ danych Data/godzina).

```XML
<ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **oświadczenie inputClaim**: 2019-06-01
- Oświadczeń danych wyjściowych:
    - **oświadczenie outputClaim**: 1559347200 (1 czerwiec maja 2019 r 12:00:00 AM)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Pobierz bieżąca data i Godzina UTC i Dodaj wartość do typu oświadczenia.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| oświadczenie outputClaim | currentDateTime | Data i godzina | Typ oświadczenia, które są generowane po wywołaniu tego ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

* Oświadczeń danych wyjściowych:
    * **currentDateTime**: 1534418820 (sierpień 16 2018 r. 11:27:00 AM)

## <a name="datetimecomparison"></a>DateTimeComparison

Ustal, czy jeden daty/godziny jest większa, mniejszy lub równy innemu. Wynik jest nowy, logiczna boolean typu oświadczenia o wartości true lub false.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | firstDateTime | Data i godzina | Pierwszą datę i godzinę do porównania. Wartość null zgłasza wyjątek. |
| Oświadczenie InputClaim | secondDateTime | Data i godzina | Drugi element dateTime do ukończenia. Wartość null traktuje jako bieżący datetTime. |
| InputParameter | Operator | ciąg | Jedną z następujących wartości: ten sam, późniejsza niż lub wcześniejsza niż. |
| InputParameter | timeSpanInSeconds | int | Dodaj zakres czasu do pierwszego daty/godziny. |
| oświadczenie outputClaim | wynik | wartość logiczna | Typ oświadczenia, które są generowane po wywołaniu tego ClaimsTransformation. |

Użycie oświadczeń to przekształcenie, aby określić, czy dwa ClaimTypes są równe, większe lub mniejsze od siebie nawzajem. Na przykład mogą przechowywać czas ostatniego użytkownika zaakceptowano warunków użytkowania usługi (TOS). Po upływie 3 miesięcy możesz poprosić użytkownika o ponowne dostępu OT.
Aby uruchomić przekształcania oświadczeń, należy najpierw uzyskać od bieżącej godziny i ostatniego użytkownika czasu akceptuje także OT.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>      
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Parametry wejściowe:
    - **operator**: późniejsza niż
    - **timeSpanInSeconds**: 7776000 (90 dni)
- Oświadczeń danych wyjściowych: 
    - **wynik**: true

