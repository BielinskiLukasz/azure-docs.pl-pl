---
title: Uwierzytelnianie i autoryzacja interfejsu API — Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla aplikacji niestandardowej, która wywołuje interfejs API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: ff5f7a80e2dcedb1795bae14ee9140c2842303a5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984604"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja interfejsu API Azure Time Series Insights

W tym dokumencie opisano sposób rejestrowania aplikacji w Azure Active Directory przy użyciu bloku nowy Azure Active Directory. Aplikacje zarejestrowane w Azure Active Directory umożliwiają użytkownikom uwierzytelnianie w usłudze i Zezwalanie na korzystanie z interfejsu API usługi Azure Time Series Insights skojarzonego ze środowiskiem Time Series Insights.

> [!IMPORTANT]
> Azure Time Series Insights obsługuje obie następujące biblioteki uwierzytelniania:
> * Najnowsza [Biblioteka uwierzytelniania firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Biblioteka Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Nazwa główna usługi

W poniższych sekcjach opisano sposób konfigurowania aplikacji w celu uzyskiwania dostępu do interfejsu API Time Series Insights w imieniu aplikacji. Aplikacja może następnie badać lub publikować dane referencyjne w środowisku Time Series Insights przy użyciu własnych poświadczeń aplikacji za pośrednictwem Azure Active Directory.

## <a name="summary-and-best-practices"></a>Podsumowanie i najlepsze rozwiązania

Przepływ rejestracji aplikacji Azure Active Directory obejmuje trzy główne kroki.

1. [Zarejestruj aplikację](#azure-active-directory-app-registration) w Azure Active Directory.
1. Autoryzuj aplikację w taki sposób, aby miała [dostęp do danych środowiska Time Series Insights](#granting-data-access).
1. Użyj **identyfikatora aplikacji** i **klucza tajnego klienta** , aby uzyskać token z `https://api.timeseries.azure.com/` w [aplikacji klienckiej](#client-app-initialization). Tokenu można następnie użyć do wywołania interfejsu API Time Series Insights.

Na **krok 3**oddzielenie poświadczeń aplikacji i użytkownika pozwala:

* Przypisywanie uprawnień do tożsamości aplikacji, które różnią się od własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone tylko do wymaganej aplikacji. Można na przykład zezwolić aplikacji na odczytywanie danych tylko z określonego środowiska Time Series Insights.
* Odizolowanie zabezpieczeń aplikacji od tworzenia poświadczeń uwierzytelniania użytkownika przy użyciu **klucza tajnego klienta** lub certyfikatu zabezpieczeń. W związku z tym poświadczenia aplikacji nie są zależne od poświadczeń określonego użytkownika. Jeśli rola użytkownika ulegnie zmianie, aplikacja nie musi wymagać nowych poświadczeń lub dalszej konfiguracji. Jeśli użytkownik zmieni hasło, cały dostęp do aplikacji nie wymaga nowych poświadczeń ani kluczy.
* Uruchom skrypt nienadzorowany przy użyciu **klucza tajnego klienta** lub certyfikatu zabezpieczeń, a nie poświadczeń określonego użytkownika (wymaganie ich obecności).
* Użyj certyfikatu zabezpieczeń zamiast hasła, aby zabezpieczyć dostęp do interfejsu API Azure Time Series Insights.

> [!IMPORTANT]
> Postępuj zgodnie z zasadami **rozdzielenia problemów** (opisanymi w tym scenariuszu) podczas konfigurowania zasad zabezpieczeń Azure Time Series Insights.

> [!NOTE]
> * Artykuł koncentruje się na aplikacji z jedną dzierżawą, w której aplikacja jest przeznaczona do działania tylko w jednej organizacji.
> * Zwykle używasz aplikacji z jedną dzierżawą dla aplikacji biznesowych, które działają w organizacji.

## <a name="detailed-setup"></a>Szczegółowa konfiguracja

### <a name="azure-active-directory-app-registration"></a>Rejestracja aplikacji Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udzielanie dostępu do danych

1. Dla środowiska Time Series Insights wybierz pozycję **zasady dostępu do danych** i wybierz pozycję **Dodaj**.

   [![dodać nowe zasady dostępu do danych do środowiska Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. W oknie dialogowym **Wybieranie użytkownika** wklej **nazwę aplikacji** lub **Identyfikator aplikacji** z sekcji Azure Active Directory rejestracji aplikacji.

   [![znaleźć aplikacji w oknie dialogowym Wybieranie użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Wybierz rolę. Wybierz opcję **czytelnik** , aby wykonać zapytanie dotyczące danych lub **współautora** , aby wykonać zapytanie o dane i zmienić dane referencyjne Kliknij przycisk **OK**.

   [![Wybieranie czytnika lub współautora w oknie dialogowym Wybieranie roli użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Zapisz zasady, wybierając **przycisk OK**.

   > [!TIP]
   > Aby uzyskać zaawansowane opcje dostępu do danych, należy przeczytać artykuł [udzielanie dostępu do danych](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inicjowanie aplikacji klienta

* Deweloperzy mogą używać [biblioteki Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) lub [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) do uwierzytelniania przy użyciu Azure Time Series Insights.

* Na przykład w celu uwierzytelnienia przy użyciu biblioteki ADAL:

   1. Użyj **identyfikatora aplikacji** i klucza **tajnego klienta** (klucza aplikacji) z sekcji Azure Active Directory rejestracji aplikacji, aby uzyskać token w imieniu aplikacji.

   1. W C#programie Poniższy kod może uzyskać token w imieniu aplikacji. Aby uzyskać kompletny przykład, Przeczytaj [dane zapytania przy C#użyciu ](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Token można następnie przesłać do nagłówka `Authorization`, gdy aplikacja wywołuje interfejs API Time Series Insights.

* Alternatywnie deweloperzy mogą zdecydować się na uwierzytelnianie za pomocą MSAL. Zapoznaj się z informacjami na temat [migracji do MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) i Zobacz nasze [dane referencyjne zarządzania C# dla środowiska Azure Time Series Insights za pomocą](time-series-insights-manage-reference-data-csharp.md) artykułu, aby dowiedzieć się więcej. 

## <a name="common-headers-and-parameters"></a>Wspólne nagłówki i parametry

W tej sekcji opisano typowe nagłówki i parametry żądań HTTP służące do wykonywania zapytań dotyczących Time Series Insights GA i Podgląd interfejsów API. Wymagania dotyczące interfejsu API są szczegółowo opisane w [dokumentacji dotyczącej interfejsu API REST Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Przeczytaj informacje o [interfejsie API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/) , aby dowiedzieć się więcej na temat korzystania z interfejsów API REST, wykonywania żądań HTTP i obsługi odpowiedzi HTTP.

### <a name="authentication"></a>Authentication

Aby wykonać uwierzytelnione zapytania dotyczące [Time Series Insights interfejsów API REST](https://docs.microsoft.com/rest/api/time-series-insights/), należy przesłać prawidłowy token okaziciela OAuth 2,0 w [nagłówku autoryzacji](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) za pomocą wybranego przez siebie klienta REST (Poster, JavaScript, C#). 

> [!TIP]
> Zapoznaj się z [przykładową wizualizacją zestawu SDK klienta](https://tsiclientsample.azurewebsites.net/) hostowanego Azure Time Series Insights, aby dowiedzieć się, jak uwierzytelniać się za pomocą interfejsu API Time Series Insights programowo przy użyciu [zestawu SDK klienta JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) oraz wykresów i grafów

### <a name="http-headers"></a>Nagłówki HTTP

Wymagane nagłówki żądań są opisane poniżej.

| Wymagany nagłówek żądania | Opis |
| --- | --- |
| Autoryzacja | Aby uwierzytelnić się za pomocą Time Series Insights, należy przesłać prawidłowy token okaziciela OAuth 2,0 w nagłówku **autoryzacji** . | 

> [!IMPORTANT]
> Token musi być wystawiony dokładnie dla `https://api.timeseries.azure.com/` zasobu (znany również jako "odbiorcy" tokenu).
> * W związku z tym **AuthURL** Twojego [ogłaszania](https://www.getpostman.com/) będzie: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` jest prawidłowy, ale `https://api.timeseries.azure.com` nie jest.

Opcjonalne nagłówki żądań są opisane poniżej.

| Opcjonalny nagłówek żądania | Opis |
| --- | --- |
| Typ zawartości | Obsługiwane są tylko `application/json`. |
| x-ms-client-request-id | Identyfikator żądania klienta. Usługa rejestruje tę wartość. Umożliwia usłudze śledzenie operacji między usługami. |
| x-MS-Client-Session-ID | Identyfikator sesji klienta. Usługa rejestruje tę wartość. Umożliwia usłudze Śledzenie grupy powiązanych operacji między usługami. |
| x-MS-Client-Application-Name | Nazwa aplikacji, która wygenerowała to żądanie. Usługa rejestruje tę wartość. |

Opcjonalne, ale zalecane nagłówki odpowiedzi są opisane poniżej.

| Nagłówek odpowiedzi | Opis |
| --- | --- |
| Typ zawartości | Obsługiwane są tylko `application/json`. |
| x-MS-Request-ID | Identyfikator żądania wygenerowany przez serwer. Można go użyć do skontaktowania się z firmą Microsoft w celu zbadania żądania. |
| zachowanie x-MS-Property-not----------- | Opcjonalny nagłówek odpowiedzi interfejsu API usługi GA. Możliwe wartości to `ThrowError` (wartość domyślna) lub `UseNull`. |

### <a name="http-parameters"></a>Parametry HTTP

> [!TIP]
> Więcej informacji na temat wymaganych i opcjonalnych informacji o zapytaniach znajduje się w [dokumentacji referencyjnej](https://docs.microsoft.com/rest/api/time-series-insights/).

Parametry ciągu zapytania wymaganego adresu URL są zależne od wersji interfejsu API.

| Wydawanie | Możliwe wartości wersji interfejsu API |
| --- |  --- |
| Ogólna dostępność | `api-version=2016-12-12`|
| Wersja zapoznawcza | `api-version=2018-11-01-preview` |
| Wersja zapoznawcza | `api-version=2018-08-15-preview` |

Opcjonalne parametry ciągu zapytania URL obejmują ustawianie limitu czasu dla czasów wykonywania żądań HTTP.

| Opcjonalny parametr zapytania | Opis | Wersja |
| --- |  --- | --- |
| `timeout=<timeout>` | Limit czasu po stronie serwera dla wykonywania żądania HTTP. Dotyczy tylko [zdarzeń Get Environment](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) i [Get Environment](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) API. Wartość limitu czasu powinna mieć format czasu trwania ISO 8601, na przykład `"PT20S"` i powinna znajdować się w zakresie `1-30 s`. Wartość domyślna to `30 s`. | Ogólna dostępność |
| `storeType=<storeType>` | W przypadku środowisk w wersji zapoznawczej z włączonym magazynem ciepłym zapytanie można wykonać w `WarmStore` lub `ColdStore`. Ten parametr w zapytaniu definiuje magazyn, dla którego ma zostać wykonane zapytanie. Jeśli nie zostanie zdefiniowana, zapytanie zostanie wykonane w chłodnym magazynie. Aby zbadać magazyn ciepły, należy ustawić wartość **magazynutype** na `WarmStore`. Jeśli nie zostanie zdefiniowana, zapytanie zostanie wykonane względem zimnego magazynu. | Wersja zapoznawcza |

## <a name="next-steps"></a>Następne kroki

- Przykładowy kod, który wywołuje interfejs API Time Series Insights GA, Odczytaj [dane zapytania C#przy użyciu ](./time-series-insights-query-data-csharp.md).

- Przykłady kodu interfejsu API Time Series Insights w wersji zapoznawczej można znaleźć w artykule [Podgląd zapytań danych przy użyciu polecenia C# ](./time-series-insights-update-query-data-csharp.md).

- Informacje referencyjne dotyczące interfejsu API można znaleźć w dokumentacji dotyczącej [interfejsu API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) .

- Dowiedz się, jak [utworzyć jednostkę usługi](../active-directory/develop/howto-create-service-principal-portal.md).
