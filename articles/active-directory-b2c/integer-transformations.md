---
title: Liczba całkowita przykłady przekształcania oświadczeń tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Liczba całkowita oświadczeń przykłady przekształcania tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 358ee07b8fd32edded084d406e490cae9f557fdd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159523"
---
# <a name="integer-claims-transformations"></a>Liczba całkowita oświadczeń przekształcenia

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera przykłady dotyczące używania przekształcenia oświadczeń całkowitą schematu, struktura środowiska tożsamości w usłudze Azure Active Directory (Azure AD) B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Konwertuje ciąg znaków typu danych long — typ danych.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Oświadczenie InputClaim | długi | Typ oświadczenia do przekonwertowania na ciąg. |
| oświadczenie outputClaim | oświadczenie outputClaim | ciąg | Typ oświadczenia, które są generowane po wywołaniu tego ClaimsTransformation. |

W tym przykładzie `numericUserId` oświadczenia za pomocą wartości typu long jest konwertowany na `UserId` oświadczenie z typem wartości ciągu.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **inputClaim**: 12334 (long)
- Oświadczeń danych wyjściowych: 
    - **oświadczenie outputClaim**: "12334" (ciąg)

