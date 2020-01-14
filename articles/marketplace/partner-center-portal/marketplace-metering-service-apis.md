---
title: Interfejsy API usługi pomiaru Marketplace | Portal Azure Marketplace
description: Zdarzenie użycia dla ofert SaaS w portalu Azure Marketplace.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: dea950ff72eff2372fc10f989d4ce77fa746c4bf
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933587"
---
# <a name="marketplace-metering-service-apis"></a>Interfejsy API usługi pomiaru w witrynie Marketplace

Interfejs API zdarzeń użycia umożliwia emitowanie zdarzeń użycia dla określonej zakupionej jednostki. Żądanie zdarzenia użycia odwołuje się do wymiaru usług pomiarowych zdefiniowanego przez wydawcę podczas publikowania oferty.

## <a name="usage-event"></a>Zdarzenie użycia

**Wpis**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametry zapytania:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Wersja operacji do użycia dla tego żądania. Najnowsza wersja interfejsu API to 2018-08-31. |

*Nagłówki żądania:*

| Typ zawartości       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `x-ms-correlationid` | Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `authorization`   | [Pobierz token okaziciela sieci Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Uwaga: podczas wykonywania żądania HTTP prefiks `Bearer` jest tokenem uzyskanym z przywoływanego linku. |

*Żądając*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Odpowiedzi

Kod: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Kod: 400 <br>
Złe żądanie, brakujące lub nieprawidłowe dane lub wygasły

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kod: 403<br>
Złe żądanie, brakujące lub nieprawidłowe dane lub wygasły

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kod: 409<br>
Konflikt, gdy otrzymamy wywołanie użycia dla identyfikatora zasobu użycia i efektywne użycie już istnieje. Odpowiedź będzie zawierać pole `additionalInfo`, które zawiera informacje o zaakceptowanej wiadomości.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Zdarzenie użycia partii

Interfejs API zdarzeń użycia usługi Batch umożliwia emitowanie zdarzeń użycia dla więcej niż jednej zakupionej jednostki jednocześnie. Żądanie zdarzenia użycia usługi Batch odwołuje się do wymiaru usług pomiarowych zdefiniowanego przez wydawcę podczas publikowania oferty.

>[!Note]
>Możesz zarejestrować wiele ofert SaaS na komercyjnym rynku firmy Microsoft. Każda zarejestrowana oferta SaaS ma unikatową aplikację usługi Azure AD, która jest zarejestrowana na potrzeby uwierzytelniania i autoryzacji. Zdarzenia emitowane w usłudze Batch powinny należeć do ofert z tą samą aplikacją usługi Azure AD w momencie rejestracji oferty.

**Wpis:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametry zapytania:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Wersja operacji do użycia dla tego żądania. Najnowsza wersja interfejsu API to 2018-08-31. |

*Nagłówki żądania:*

| Typ zawartości       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Unikatowa wartość ciągu służąca do śledzenia żądania od klienta, najlepiej identyfikatora GUID. Jeśli ta wartość nie zostanie podana, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `x-ms-correlationid` | Unikatowa wartość ciągu dla operacji na kliencie. Ten parametr umożliwia skorelowanie wszystkich zdarzeń z operacji klienta ze zdarzeniami po stronie serwera. Jeśli ta wartość nie zostanie określona, zostanie wygenerowana i podana w nagłówkach odpowiedzi. |
| `authorization`      | [Pobierz token okaziciela sieci Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Uwaga: podczas wykonywania żądania HTTP prefiks `Bearer` jest tokenem uzyskanym z przywoływanego linku.  |

*Żądając*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Odpowiedzi

Kod: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Opis kodu stanu przywoływany w odpowiedzi interfejsu API `BatchUsageEvent`:

| Kod stanu  | Opis |
| ---------- | -------------------- |
| `Accepted` | Kod zaakceptowany. |
| `Expired` | Wygasłe użycie. |
| `Duplicate` | Podano zduplikowane użycie. |
| `Error` | Kod błędu. |
| `ResourceNotFound` | Podany zasób użycia jest nieprawidłowy. |
| `ResourceNotAuthorized` | Nie masz uprawnień do zapewnienia użycia dla tego zasobu. |
| `InvalidDimension` | Wymiar, dla którego jest przesyłane użycie, jest nieprawidłowy dla tej oferty/planu. |
| `InvalidQuantity` | Przenoszona ilość jest < 0. |
| `BadArgument` | Brak danych wejściowych lub jest nieprawidłowo sformułowany. |

Kod: 400<br>
Złe żądanie, brakujące lub nieprawidłowe dane lub wygasły

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Kod: 403<br>
Użytkownik nie ma autoryzacji do tego wywołania

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [SaaS zliczanie opłat](./saas-metered-billing.md).
