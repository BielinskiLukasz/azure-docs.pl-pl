---
title: Interfejsy API do obsługi rozliczeń przedsiębiorstw na platformie Azure | Microsoft Docs
description: Dowiedz się więcej na temat interfejsów API raportowania, które umożliwiają klientom korporacyjnym programowe ściąganie danych użycia z platformy Azure.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.topic: reference
ms.date: 02/14/2020
ms.author: banders
ms.openlocfilehash: 9f9552c134fa7653e4100cae3c731e4c670d44e3
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560545"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Przegląd interfejsów API raportowania dla klientów korporacyjnych
Interfejsy API raportowania umożliwiają klientom korporacyjnym platformy Azure programowe ściąganie danych użycia i rozliczeń do preferowanych narzędzi do analizy danych. Klienci korporacyjni podpisali umowę [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) na platformie Azure, na podstawie której mają wynegocjowane zobowiązania pieniężne i dostęp do niestandardowych cen zasobów platformy Azure.

Wszystkie parametry daty i godziny wymagane dla interfejsów API muszą być reprezentowane jako wartości uniwersalnego czasu koordynowanego (UTC). Wartości zwracane przez interfejsy API są wyświetlane w formacie UTC.

## <a name="enabling-data-access-to-the-api"></a>Włączanie dostępu do danych dla interfejsu API
* **Generowanie lub pobieranie klucza interfejsu API** — zaloguj się do witryny Enterprise Portal i przejdź do pozycji Reports > Download Usage > API Access Key (Raporty > Pobierz zestawienie użycia > Klucz dostępu interfejsu API), aby wygenerować lub pobrać klucz interfejsu API.
* **Przekazywanie kluczy w interfejsie API** — klucz interfejsu API powinien zostać przekazany do każdego wywołania uwierzytelniania i autoryzacji. Następująca właściwość musi znajdować się w nagłówkach HTTP

|Klucz nagłówka żądania | Wartość|
|-|-|
|Autoryzacja| Określ wartość w tym formacie: **bearer {API_KEY}** <br/> Przykład: bearer eyr....09|

## <a name="consumption-apis"></a>Interfejsy API użycia
Punkt końcowy struktury Swagger dla opisanych poniżej interfejsów API jest dostępny [tutaj](https://consumption.azure.com/swagger/ui/index). Powinien on umożliwić łatwą introspekcję interfejsu API i dać możliwość wygenerowania zestawów SDK klienta przy użyciu funkcji [AutoRest](https://github.com/Azure/AutoRest) lub narzędzia [Swagger CodeGen](https://swagger.io/swagger-codegen/). Dane są dostępne za pośrednictwem tego interfejsu API od 1 maja 2014 r.

* **Saldo i podsumowanie** — [interfejs API salda i podsumowania](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferuje comiesięczne podsumowanie informacji dotyczących sald, nowych zakupów, opłat za usługę Azure Marketplace, korekt i opłat za użycie nadwyżkowe.

* **Szczegółowe zestawienie użycia** — [interfejs API szczegółowego zestawienia użycia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferuje dzienny podział ilości wykorzystanych zasobów i szacowane opłaty według rejestracji. Wynik zawiera również informacje na temat wystąpień, liczników i działów. Do tego interfejsu API można wysyłać zapytania według okresu rozliczeniowego lub określonej daty rozpoczęcia i zakończenia.

* **Opłaty za sklep Marketplace** — [interfejs API opłat za sklep Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) zwraca opłaty za witrynę Marketplace obliczone na podstawie użycia według dnia dla określonego okresu rozliczeniowego lub dat rozpoczęcia i zakończenia (jednorazowe opłaty nie są uwzględniane).

* **Arkusz cen** — [interfejs API arkusza cen](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) udostępnia odpowiednią stawkę za każdy miernik dla danej rejestracji i okresu rozliczeniowego.

* **Szczegóły wystąpienia zarezerwowanego** — [interfejs API użycia wystąpienia zarezerwowanego](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) zwraca użycie zakupów wystąpienia zarezerwowanego. [Interfejs API opłat za wystąpienia zarezerwowane](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) pokazuje dokonane transakcje rozliczeniowe.

## <a name="data-freshness"></a>Aktualność danych
W odpowiedzi na wywołania wszystkich powyższych interfejsów API zostaną zwrócone elementy ETag. Zmiana elementu ETag wskazuje, że dane zostały odświeżone.  W kolejnych wywołaniach tego samego interfejsu API z użyciem tych samych parametrów przekaż przechwycony element ETag z kluczem „If-None-Match” w nagłówku żądania HTTP. Jeśli dane nie zostały odświeżone, zostanie zwrócony kod stanu odpowiedzi „NotModified” i nie zostaną zwrócone żadne dane. Jeśli element ETag zostanie zmieniony, interfejs API zwróci pełen zestaw danych dla wymaganego okresu.

## <a name="helper-apis"></a>Interfejsy API pomocnika
 **Wyświetlanie listy okresów rozliczeniowych** — [interfejs API okresów rozliczeniowych](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) zwraca listę okresów rozliczeniowych, które mają dane dotyczące użycia dla określonej rejestracji w odwrotnej kolejności chronologicznej. Każdy okres zawiera właściwość wskazującą trasę interfejsu API dla czterech zestawów danych — BalanceSummary, UsageDetails, Marketplace Charges i Price Sheet.


## <a name="api-response-codes"></a>Kody odpowiedzi interfejsu API   
|Kod stanu odpowiedzi|Komunikat|Opis|
|-|-|-|
|200| OK|Brak błędów|
|401| Brak autoryzacji| Nie znaleziono klucza interfejsu API, jest on nieprawidłowy, wygasł itd.|
|404| Niedostępny| Nie znaleziono punktu końcowego raportu|
|400| Nieprawidłowe żądanie| Nieprawidłowe parametry — zakresy dat, numery EA itd.|
|500| Błąd serwera| Nieoczekiwany błąd podczas przetwarzania żądania|
