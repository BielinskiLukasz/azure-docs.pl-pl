---
title: Przeglądanie danych rozliczeń subskrypcji platformy Azure za pomocą interfejsu API REST | Microsoft Docs
description: Dowiedz się, jak używać interfejsów API REST platformy Azure do przeglądania szczegółów rozliczeń subskrypcji.
author: lleonard-msft
ms.service: cost-management-billing
ms.topic: article
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 4746c5a541118b6018f3b6601b7eb0662503fd3a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199555"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Przegląd rozliczeń subskrypcji przy użyciu interfejsów API REST

Interfejsy API usługi Azure Reporting ułatwiają przeglądanie kosztów korzystania z platformy Azure i zarządzanie nimi.

Filtry pomagają dostosować wyniki do Twoich potrzeb.

Tutaj dowiesz się, jak używać interfejsu API REST, aby uzyskać szczegóły rozliczeń subskrypcji dla danego zakresu dat.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Tworzenie żądania

Parametr `{subscriptionID}` jest wymagany i identyfikuje subskrypcję docelową.

Parametr `{billingPeriod}` jest wymagany i określa bieżący [okres rozliczeniowy](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

Parametry `${startDate}` i `${endDate}` są wymagane w tym przykładzie, ale opcjonalne dla punktu końcowego. Określają one zakres dat jako ciągi w formacie RRRR-MM-DD (np. `'20180501'` i `'20180615'`).

Wymagane są następujące nagłówki:

|Nagłówek żądania|Opis|
|--------------------|-----------------|
|*Content-Type:*|Wymagany. Ustaw wartość `application/json`.|
|*Authorization:*|Wymagany. Ustaw na prawidłowy [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

## <a name="response"></a>Odpowiedź

Kod stanu 200 (OK) jest zwracany dla pomyślnej odpowiedzi, która zawiera listę szczegółowych kosztów dla Twojego konta.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Każdy element w sekcji **value** reprezentuje szczegóły dotyczące korzystania z usługi:

|Właściwość odpowiedzi|Opis|
|----------------|----------|
|**subscriptionGuid** | Globalnie unikatowy identyfikator subskrypcji. |
|**startDate** | Data rozpoczęcia użycia. |
|**endDate** | Data zakończenia użycia. |
|**useageQuantity** | Wykorzystana ilość. |
|**billableQuantity** | Ilość rzeczywiście rozliczona. |
|**pretaxCost** | Zafakturowany koszt przed naliczeniem należnych podatków. |
|**meterDetails** | Szczegółowe informacje na temat użycia. |
|**nextLink**| Po ustawieniu określa adres URL dla następnej „strony” ze szczegółami. Właściwość pozostaje pusta, gdy jest to ostatnia strona. |

Ten przykład jest skrócony. Zobacz [Szczegóły użycia listy](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy), aby uzyskać pełny opis każdego pola odpowiedzi.

Inne kody stanu wskazują na błędy. W takich przypadkach obiekt odpowiedzi wyjaśnia, dlaczego żądanie nie powiodło się.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki
- Przejrzyj temat [Przegląd raportowania korporacyjnego](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Zapoznaj się z tematem [Interfejs API REST rozliczania korporacyjnego](https://docs.microsoft.com/rest/api/billing/)
- [Rozpoczynanie pracy z interfejsem API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/)
