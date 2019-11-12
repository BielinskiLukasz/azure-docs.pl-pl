---
title: Typy aplikacji dla platformy tożsamości firmy Microsoft | Azure
description: Typy aplikacji i scenariuszy obsługiwane przez punkt końcowy Microsoft Identity platform (v 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21f1da8738946078cb625c76e11ce1bfd62d97ac
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927155"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Typy aplikacji dla platformy tożsamości firmy Microsoft

Punkt końcowy Microsoft Identity platform (v 2.0) obsługuje uwierzytelnianie dla różnych nowoczesnych architektur aplikacji, wszystkie z nich oparte na standardach branżowych [OAuth 2,0 lub OpenID Connect Connect](active-directory-v2-protocols.md). W tym artykule opisano typy aplikacji, które można skompilować przy użyciu platformy tożsamości firmy Microsoft, niezależnie od preferowanego języka lub platformy. Te informacje ułatwiają zrozumienie scenariuszy wysokiego poziomu przed [rozpoczęciem pracy z kodem](v2-overview.md#getting-started).

> [!NOTE]
> Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich scenariuszy i funkcji usługi Azure Active Directory (Azure AD). Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Podstawy

Należy zarejestrować każdą aplikację korzystającą z punktu końcowego platformy tożsamości firmy Microsoft w nowym [portalu rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908). Proces rejestracji aplikacji zbiera i przypisuje te wartości dla aplikacji:

* **Identyfikator aplikacji (klienta)** , który jednoznacznie identyfikuje aplikację
* **Identyfikator URI przekierowania** , za pomocą którego można kierować odpowiedzi z powrotem do aplikacji
* Kilka innych wartości specyficznych dla scenariusza, takich jak obsługiwane typy kont

Aby uzyskać szczegółowe informacje, Dowiedz się, jak [zarejestrować aplikację](quickstart-register-app.md).

Po zarejestrowaniu aplikacji aplikacja komunikuje się z platformą tożsamości firmy Microsoft przez wysyłanie żądań do punktu końcowego. Udostępniamy struktury i biblioteki typu open source, które obsługują szczegóły tych żądań. Istnieje również możliwość zaimplementowania logiki uwierzytelniania samodzielnie przez tworzenie żądań do tych punktów końcowych:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplikacje jednostronicowe (JavaScript)

Wiele nowoczesnych aplikacji ma fronton aplikacji jednostronicowej, który jest przeznaczony głównie do pisania w języku JavaScript. Często jest on pisany przy użyciu struktury, takiej jak kątowy, reaguje lub Vue. Punkt końcowy platformy tożsamości firmy Microsoft obsługuje te aplikacje przy użyciu [niejawnego przepływu OAuth 2,0](v2-oauth2-implicit-grant-flow.md).

W tym przepływie aplikacja otrzymuje tokeny bezpośrednio od platformy tożsamości firmy Microsoft autoryzuje punkt końcowy, bez żadnych wymian serwer-serwer. Cała logika uwierzytelniania i obsługa sesji odbywa się całkowicie w kliencie JavaScript bez dodatkowych przekierowań stron.

![Pokazuje przepływ niejawnego uwierzytelniania](./media/v2-app-types/convergence-scenarios-implicit.svg)

Aby zapoznać się z tym scenariuszem, wypróbuj jeden z przykładów kodu aplikacji jednostronicowych w sekcji [wprowadzenie do platformy tożsamości firmy Microsoft](v2-overview.md#getting-started) .

## <a name="web-apps"></a>Aplikacje sieci Web

W przypadku aplikacji sieci Web (.NET, PHP, Java, Ruby, Python, Node), do których użytkownik uzyskuje dostęp za pomocą przeglądarki, można użyć programu [OpenID Connect Connect](active-directory-v2-protocols.md) do logowania użytkownika. W programie OpenID Connect Connect aplikacja sieci Web otrzymuje token ID. Token identyfikatora to token zabezpieczający, który weryfikuje tożsamość użytkownika i zawiera informacje o użytkowniku w formie oświadczeń:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Dodatkowe szczegóły dotyczące różnych typów tokenów używanych w punkcie końcowym platformy tożsamości firmy Microsoft są dostępne w dokumentacji [tokenu dostępu](access-tokens.md) i [id_token](id-tokens.md)

W aplikacjach serwera sieci Web przepływ uwierzytelniania logowania wykonuje następujące czynności wysokiego poziomu:

![Pokazuje przepływ uwierzytelniania aplikacji sieci Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Tożsamość użytkownika można sprawdzić, sprawdzając token identyfikatora przy użyciu publicznego klucza podpisywania otrzymanego z punktu końcowego platformy tożsamości firmy Microsoft. Ustawiono plik cookie sesji, który może służyć do identyfikowania użytkownika na kolejnych żądaniach strony.

Aby zapoznać się z tym scenariuszem w działaniu, wypróbuj jedną z przykładów kodu logowania aplikacji sieci Web w sekcji [wprowadzenie do platformy tożsamości firmy Microsoft](v2-overview.md#getting-started) .

Oprócz prostej logowania aplikacja serwera sieci Web może potrzebować dostępu do innej usługi sieci Web, takiej jak interfejs API REST. W takim przypadku aplikacja serwera sieci Web jest zaangażowana w połączony przepływ OpenID Connect Connect i OAuth 2,0 przy użyciu [przepływu kodu autoryzacji oauth 2,0](active-directory-v2-protocols.md). Aby uzyskać więcej informacji na temat tego scenariusza, zapoznaj [się z artykułem wprowadzenie do usługi Web Apps i interfejsów API sieci Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Interfejsy API sieci Web

Możesz użyć punktu końcowego platformy tożsamości firmy Microsoft do zabezpieczenia usług sieci Web, takich jak internetowy interfejs API aplikacji RESTful. Interfejsy API sieci Web można zaimplementować na wielu platformach i w różnych językach. Można je również zaimplementować przy użyciu wyzwalaczy HTTP w Azure Functions. Zamiast tokenów identyfikatorów i plików cookie sesji, internetowy interfejs API używa tokenu dostępu OAuth 2,0 do zabezpieczania danych i uwierzytelniania żądań przychodzących. Obiekt wywołujący interfejs API sieci Web dołącza token dostępu w nagłówku autoryzacji żądania HTTP, tak jak to:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Internetowy interfejs API używa tokenu dostępu do weryfikowania tożsamości obiektu wywołującego interfejsu API i wyodrębniania informacji o wywołującym z oświadczeń, które są zakodowane w tokenie dostępu. Dodatkowe szczegóły dotyczące różnych typów tokenów używanych w punkcie końcowym platformy tożsamości firmy Microsoft są dostępne w dokumentacji [tokenu dostępu](access-tokens.md) i [id_token](id-tokens.md)

Internetowy interfejs API może zapewnić użytkownikom możliwość wyboru lub rezygnacji z określonych funkcji lub danych przez ujawnienie uprawnień, nazywanych również [zakresami](v2-permissions-and-consent.md). Aby aplikacja wywołująca uzyskała uprawnienie do zakresu, użytkownik musi wyrazić zgodę na zakres w przepływie. Punkt końcowy platformy tożsamości firmy Microsoft prosi użytkownika o zgodę, a następnie rejestruje uprawnienia we wszystkich tokenach dostępu odbieranych przez internetowy interfejs API. Internetowy interfejs API sprawdza poprawność tokenów dostępu odbieranych na każdym wywołaniu i wykonuje sprawdzanie autoryzacji.

Internetowy interfejs API może odbierać tokeny dostępu z wszystkich typów aplikacji, w tym aplikacji serwera sieci Web, aplikacji klasycznych i mobilnych, aplikacji jednostronicowych, demonów po stronie serwera, a nawet innych interfejsów API sieci Web. Przepływ wysokiego poziomu dla interfejsu API sieci Web wygląda następująco:

![Pokazuje przepływ uwierzytelniania interfejsu API sieci Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Aby dowiedzieć się, jak zabezpieczyć internetowy interfejs API za pomocą tokenów dostępu OAuth2, zapoznaj się z przykładami kodu interfejsu API sieci Web w sekcji [wprowadzenie do platformy tożsamości firmy Microsoft](v2-overview.md#getting-started) .

W wielu przypadkach interfejsy API sieci Web muszą również wykonywać żądania wychodzące do innych podrzędnych interfejsów API sieci Web zabezpieczonych przez platformę tożsamości firmy Microsoft. W tym celu interfejsy API sieci Web mogą korzystać z usługi **Flow,** co umożliwia interfejsowi API sieci Web wymianę przychodzącego tokenu dostępu dla innego tokenu dostępu, który będzie używany w żądaniach wychodzących. Aby uzyskać więcej informacji, zobacz [Microsoft Identity platform i OAuth 2,0 w imieniu usługi Flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplikacje mobilne i natywne

Aplikacje zainstalowane na urządzeniu, takie jak aplikacje mobilne i klasyczne, często muszą uzyskiwać dostęp do usług zaplecza lub interfejsów API sieci Web, które przechowują dane i wykonują funkcje w imieniu użytkownika. Te aplikacje mogą dodawać logowanie i autoryzację do usług zaplecza przy użyciu [przepływu kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md).

W tym przepływie aplikacja otrzymuje kod autoryzacji z punktu końcowego platformy tożsamości firmy Microsoft po zalogowaniu się użytkownika. Kod autoryzacji reprezentuje uprawnienia aplikacji do wywoływania usług zaplecza w imieniu zalogowanego użytkownika. Aplikacja może wymienić kod autoryzacji w tle dla tokenu dostępu OAuth 2,0 i tokenu odświeżania. Aplikacja może używać tokenu dostępu do uwierzytelniania do interfejsów API sieci Web w żądaniach HTTP i używać tokenu odświeżania do uzyskiwania nowych tokenów dostępu po wygaśnięciu starszych tokenów dostępu.

![Pokazuje przepływ uwierzytelniania aplikacji natywnych](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Demony i aplikacje po stronie serwera

Aplikacje, które mają długotrwałe procesy lub działają bez interakcji z użytkownikiem, muszą również mieć dostęp do zabezpieczonych zasobów, takich jak interfejsy API sieci Web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji, a nie delegowanej tożsamości użytkownika, za pomocą przepływu poświadczeń klienta OAuth 2,0. Tożsamość aplikacji można udowodnić przy użyciu klucza tajnego klienta lub certyfikatu. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie na platformie tożsamości firmy Microsoft w aplikacjach demonów z certyfikatami](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/).

W tym przepływie aplikacja współdziała bezpośrednio z punktem końcowym `/token`, aby uzyskać dostęp:

![Pokazuje przepływ uwierzytelniania aplikacji demona](./media/v2-app-types/convergence-scenarios-daemon.svg)

Aby skompilować aplikację demona, zapoznaj się z [dokumentacją dotyczącą poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md)lub wypróbuj [przykładową aplikację platformy .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
