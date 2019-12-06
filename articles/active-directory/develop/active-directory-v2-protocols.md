---
title: Dowiedz się więcej na temat protokołów autoryzacji obsługiwanych przez platformę tożsamości firmy Microsoft | Azure
description: Przewodnik dotyczący protokołów uwierzytelniania OAuth 2,0 i OpenID Connect, które są obsługiwane przez punkt końcowy platformy tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f2b4d5da2cdda35de0adc5dc646065f2b64a41
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844706"
---
# <a name="microsoft-identity-platform-protocols"></a>Protokoły Microsoft Identity platform

Punkt końcowy platformy tożsamości firmy Microsoft dla tożsamości jako usługi z użyciem standardowych protokołów branżowych, OpenID Connect Connect i OAuth 2,0. Mimo że usługa jest zgodna ze standardami, mogą istnieć drobne różnice między dwoma implementacjami tych protokołów. Informacje w tym miejscu będą przydatne w przypadku wybrania pisania kodu przez bezpośrednie wysłanie i obsługę żądań HTTP lub użycie biblioteki typu open-source innej firmy zamiast korzystania z jednej z naszych [bibliotek typu open source](reference-v2-libraries.md).

> [!NOTE]
> Nie wszystkie scenariusze i funkcje usługi Azure AD są obsługiwane przez punkt końcowy platformy tożsamości firmy Microsoft. Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Podstawy

W prawie wszystkie przepływy za pomocą protokołu OAuth 2,0 i OpenID Connect łączą się cztery strony w wymianie:

![Diagram przedstawiający role OAuth 2,0](./media/active-directory-v2-flows/protocols-roles.svg)

* **Serwer autoryzacji** jest punktem końcowym platformy tożsamości firmy Microsoft, który jest odpowiedzialny za zapewnienie tożsamości użytkownika, przydzielanie i odwoływanie dostępu do zasobów oraz wystawianie tokenów. Serwer autoryzacji znany również jako dostawca tożsamości — bezpiecznie obsługuje wszystko, aby zrobić z informacjami o użytkowniku, ich dostępem i relacjami zaufania między stronami w przepływie.
* **Właścicielem zasobu** jest zazwyczaj użytkownik końcowy. Jest to strona, która jest właścicielem danych i ma możliwość zezwalania stronom trzecim na dostęp do tych danych lub zasobów.
* **Klient OAuth** jest aplikacją identyfikowaną przez identyfikator aplikacji. Klient uwierzytelniania OAuth jest zwykle stroną, z którą użytkownik końcowy współdziała, i żąda tokenów z serwera autoryzacji. Klient musi mieć przyznane uprawnienie dostępu do zasobu przez właściciela zasobu.
* **Serwer zasobów** jest miejscem, w którym znajdują się zasoby lub dane. Ufa serwerowi autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta OAuth i używa tokenów dostępu okaziciela, aby zapewnić, że dostęp do zasobu można udzielić.

## <a name="app-registration"></a>Rejestracja aplikacji

Każda aplikacja, która chce akceptować konta osobiste i służbowe, musi zostać zarejestrowana przez **rejestracje aplikacji** środowisko w [Azure Portal](https://aka.ms/appregistrations) , zanim będzie mogła podpisać tych użytkowników przy użyciu protokołu OAuth 2,0 lub OpenID Connect Connect. Proces rejestracji aplikacji będzie zbierać i przypisywać kilka wartości do aplikacji:

* **Identyfikator aplikacji** , który jednoznacznie identyfikuje aplikację
* **Identyfikator URI przekierowania** (opcjonalnie), który może służyć do kierowania odpowiedzi z powrotem do aplikacji
* Kilka innych wartości specyficznych dla scenariusza.

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z procedurą [rejestracji aplikacji](quickstart-register-app.md).

## <a name="endpoints"></a>Punkty końcowe

Po zarejestrowaniu aplikacja komunikuje się z platformą tożsamości firmy Microsoft, wysyłając żądania do punktu końcowego:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Gdzie `{tenant}` może przyjmować jedną z czterech różnych wartości:

| Wartość | Opis |
| --- | --- |
| `common` | Zezwala użytkownikom na logowanie się do aplikacji zarówno dla osobistych kont Microsoft, jak i kont służbowych z usługi Azure AD. |
| `organizations` | Umożliwia logowanie się do aplikacji wyłącznie użytkownikom z kontami służbowymi z usługi Azure AD. |
| `consumers` | Umożliwia logowanie się do aplikacji wyłącznie użytkownikom z osobistymi kontami Microsoft (MSA). |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` | Zezwala tylko użytkownikom z kontami służbowymi z określonej dzierżawy usługi Azure AD na logowanie do aplikacji. Można użyć przyjaznej nazwy domeny dzierżawy usługi Azure AD lub identyfikatora GUID dzierżawcy. |

Aby dowiedzieć się, jak korzystać z tych punktów końcowych, wybierz konkretny typ aplikacji w sekcji [Protokoły](#protocols) i postępuj zgodnie z linkami, aby uzyskać więcej informacji.

> [!TIP]
> Wszystkie aplikacje zarejestrowane w usłudze Azure AD mogą korzystać z punktu końcowego platformy tożsamości firmy Microsoft, nawet jeśli nie logują się na kontach osobistych.  Dzięki temu można migrować istniejące aplikacje na platformę tożsamości firmy Microsoft i [MSAL](reference-v2-libraries.md) bez konieczności ponownego tworzenia aplikacji.  

## <a name="tokens"></a>Tokeny

Implementacja platformy tożsamości firmy Microsoft w zakresie uwierzytelniania OAuth 2,0 i OpenID Connect przy użyciu połączenia z usługą jest bardzo obszerna, w tym tokeny okaziciela reprezentowane jako JWTs. Token okaziciela jest lekkim tokenem zabezpieczającym, który przyznaje "okaziciela" dostęp do chronionego zasobu. W tym sensie "okaziciela" to każda Strona, która może przedstawić token. Chociaż strona musi najpierw przeprowadzić uwierzytelnienie przy użyciu platformy tożsamości firmy Microsoft w celu uzyskania tokenu okaziciela, jeśli wymagane kroki nie są podejmowane w celu zabezpieczenia tokenu w przesyłaniu i magazynie, można je przechwycić i wykorzystać przez niezamierzoną stronę. Chociaż niektóre tokeny zabezpieczające mają wbudowany mechanizm uniemożliwiający korzystanie z nich przez nieuprawnionych stron, tokeny okaziciela nie mają tego mechanizmu i muszą być transportowane w bezpiecznym kanale, takim jak Transport Layer Security (HTTPS). Jeśli token okaziciela zostanie przesłany w sposób oczywisty, złośliwa strona może korzystać z ataku typu man-in-the-Middle w celu uzyskania tokenu i używania go w celu uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie w przypadku przechowywania lub buforowania tokenów okaziciela na potrzeby późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła tokeny okaziciela i przechowuje je w bezpieczny sposób. Aby uzyskać więcej informacji dotyczących zabezpieczeń dotyczących tokenów okaziciela, zobacz [RFC 6750, sekcja 5](https://tools.ietf.org/html/rfc6750).

Dodatkowe szczegóły dotyczące różnych typów tokenów używanych w punkcie końcowym platformy tożsamości firmy Microsoft są dostępne w temacie [Informacje o tokenie punktu końcowego platformy tożsamości firmy Microsoft](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoły

Jeśli chcesz zobaczyć przykładowe żądania, zacznij korzystać z jednego z poniższych samouczków. Każdy z nich odpowiada konkretnemu scenariuszowi uwierzytelniania. Jeśli potrzebujesz pomocy w ustaleniu, który jest odpowiednim przepływem, sprawdź [typy aplikacji, które można skompilować za pomocą platformy tożsamości firmy Microsoft](v2-app-types.md).

* [Tworzenie aplikacji mobilnych i natywnych przy użyciu protokołu OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Tworzenie aplikacji sieci Web za pomocą OpenID Connect Connect](v2-protocols-oidc.md)
* [Twórz aplikacje jednostronicowe za pomocą niejawnego przepływu OAuth 2,0](v2-oauth2-implicit-grant-flow.md)
* [Tworzenie demonów lub procesów po stronie serwera za pomocą przepływu poświadczeń klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md)
* [Pobieranie tokenów w interfejsie API sieci Web za pomocą przepływu OAuth 2,0 w imieniu](v2-oauth2-on-behalf-of-flow.md)
