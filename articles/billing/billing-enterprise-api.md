---
title: Usługi Azure Billing interfejsów API przedsiębiorstwa | Dokumentacja firmy Microsoft
description: Więcej informacji na temat raportowania interfejsów API, które umożliwiają klientom Enterprise Azure programowo ściągnąć dane dotyczące zużycia.
services: ''
documentationcenter: ''
author: anandedwin
manager: aedwin
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.openlocfilehash: b67e6202c470be46b3100c06e503c05415371c6e
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42920480"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Omówienie interfejsy API raportowania usługi dla klientów korporacyjnych
Interfejsy API raportowania pozwala klientom usługi Azure Enterprise programowo ściągnięcia zużycia i danych dotyczących rozliczeń do narzędzia do analizy danych preferowany. Klienci korporacyjni utworzyli [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) za pomocą platformy Azure na wynegocjowanym zobowiązań pieniężnych i uzyskać dostęp do ceny niestandardowe dla zasobów platformy Azure.

> [!div class="nextstepaction"]
> [Pomóż nam w usprawnianiu dokumentacja rozliczeń platformy Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="enabling-data-access-to-the-api"></a>Włączanie dostępu do danych w interfejsie API
* **Generowanie lub pobrać klucz interfejsu API** — Zaloguj się do witryny Enterprise portal i przejdź do raportów > Pobierz dane użycia > klucz dostępu interfejsu API, aby wygenerować lub pobrać klucza interfejsu API.
* **Przekazanie kluczy w interfejsie API** — klucz interfejsu API, które należy przekazać dla każdego wywołania do uwierzytelniania i autoryzacji. Następująca właściwość musi być z nagłówkami HTTP

|Klucz nagłówka żądania | Wartość|
|-|-|
|Autoryzacja| Określ wartość w następującym formacie: **bearer {API_KEY}** <br/> Przykład: eyr elementu nośnego... 09| 

## <a name="consumption-apis"></a>Użycie interfejsów API
Punktu końcowego struktury Swagger jest dostępna [tutaj](https://consumption.azure.com/swagger/ui/index) dla interfejsów API opisanego poniżej której powinna umożliwiają łatwe introspekcji interfejsu API oraz do generowania zestawów SDK klienta przy użyciu [AutoRest](https://github.com/Azure/AutoRest) lub [programu Swagger Generowanie kodu](http://swagger.io/swagger-codegen/). Dane, począwszy od 1 maja 2014 roku. jest dostępna za pośrednictwem tego interfejsu API. 

* **Informacji o saldach i Podsumowanie** — [informacji o saldach i podsumowanie interfejsu API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferuje miesięczne podsumowanie informacji na temat salda, nowe zakupy, opłaty za usługę portalu Azure Marketplace, korekt i opłaty za użycie nadwyżkowe.

* **Szczegóły użycia** — [interfejs API użycia szczegółów](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferuje rozbiciem na poszczególne dni ilości wykorzystanych i oszacowanie opłat przy rejestracji. Wynik zawiera również informacje na temat wystąpień, liczniki i działów. Interfejs API może być odpytywany za okres rozliczeniowy lub określonej daty rozpoczęcia i zakończenia. 

* **Opłaty w portalu Marketplace Store** — [interfejsu API opłaty Store Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) zwraca podział opłaty na podstawie użycia portalu marketplace dzienne dla danego okresu rozliczeniowego lub daty rozpoczęcia i zakończenia (jeden raz opłaty nie są uwzględnione).

* **Arkusz cen** — [interfejsu API arkusza cen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) przewiduje stawkę w ramach każdego miernika danego rejestracji i okres rozliczeniowy. 

## <a name="data-freshness"></a>Aktualność danych
Elementy etag zostaną zwrócone w odpowiedzi na wszystkie powyższe wywołanie interfejsu API. Zmiana elementu Etag wskazuje, że dane zostały odświeżone.  W kolejnych wywołaniach do tego samego interfejsu API, z tymi samymi parametrami należy przekazać przechwyconych Etag z kluczem "If-None-Match" w nagłówku żądania http. Kod stanu odpowiedzi będzie "NotModified", jeśli dane nie zostały odświeżone kolejne i zostaną zwrócone nie dane. Interfejs API zwróci pełny zestaw danych w okresie wymagana zawsze wtedy, gdy nie nastąpiła zmiana elementu etag.

## <a name="helper-apis"></a>Interfejsy API pomocy
 **Lista okresy fakturowania** — [interfejs API rozliczeń okresy](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) zwraca listę okresami rozliczeniowymi znajdują się dane użycia dla określonego rejestracji w odwrotnej kolejności chronologicznej. Każdego okresu zawiera właściwość wskazujący trasę interfejsu API dla czterech zestawów danych — BalanceSummary, UsageDetails, opłatach w portalu Marketplace i cenniku.


## <a name="api-response-codes"></a>Kody odpowiedzi interfejsu API   
|Kod stanu odpowiedzi|Komunikat|Opis|
|-|-|-|
|200| OK|Brak błędów|
|401| Brak autoryzacji| Klucz interfejsu API nie można odnaleźć, nieprawidłowy, wygasło itp.|
|404| Niedostępny| Nie znaleziono punktu końcowego raportu|
|400| Nieprawidłowe żądanie| Nieprawidłowe parametry — zakresy dat, liczb umowy EA itp.|
|500| Błąd serwera| Unexoected błąd podczas przetwarzania żądania| 









