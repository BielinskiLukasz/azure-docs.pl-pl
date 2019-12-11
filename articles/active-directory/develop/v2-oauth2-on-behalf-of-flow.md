---
title: Microsoft Identity platform & OAuth 2.0 w imieniu przepływu | Azure
description: W tym artykule opisano sposób korzystania z komunikatów HTTP w celu zaimplementowania uwierzytelniania usługi do usługi przy użyciu przepływu uwierzytelniania OAuth 2.0 w imieniu użytkownika.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa58f63e70c09e17328b849e7728604a65cb7ae1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964323"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Platforma tożsamości firmy Microsoft i protokół OAuth 2,0 w imieniu użytkownika

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Przepływ uwierzytelniania OAuth 2,0 w imieniu (OBO) obsługuje przypadek użycia, w którym aplikacja wywołuje usługę/interfejs API sieci Web, co z kolei musi wywołać inną usługę/internetowy interfejs API. Pomysłem jest propagowanie tożsamości i uprawnień delegowanych użytkowników za pomocą łańcucha żądań. W przypadku usługi warstwy środkowej aby żądania były uwierzytelniane w usłudze podrzędnej, należy zabezpieczyć token dostępu z platformy tożsamości firmy Microsoft w imieniu użytkownika.

W tym artykule opisano, jak programować bezpośrednio w odniesieniu do protokołu w aplikacji.  Jeśli to możliwe, zalecamy korzystanie z obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast [uzyskiwać tokeny i wywoływać zabezpieczone interfejsy API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z MSAL](sample-v2-code.md).

> [!NOTE]
>
> - Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich scenariuszy i funkcji. Aby określić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj artykuł [ograniczenia dotyczące platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md). W szczególnych przypadkach znane aplikacje klienckie nie są obsługiwane w przypadku aplikacji z usługami konto Microsoft (MSA) i odbiorcami usługi Azure AD. W ten sposób wspólny wzorzec zgody dla OBO nie będzie działał w przypadku klientów, którzy logują się zarówno do kont osobistych, jak i służbowych. Aby dowiedzieć się więcej o tym, jak obsłużyć ten krok przepływu, zobacz [Uzyskaj zgodę na aplikację warstwy środkowej](#gaining-consent-for-the-middle-tier-application).
> - Począwszy od maja 2018, niektóre pochodne `id_token` pochodnych nie mogą być używane dla przepływu OBO. Aplikacje jednostronicowe (aplikacji jednostronicowych) powinny przekazać token **dostępu** do klienta poufnego w warstwie środkowej, aby w zamian wykonać przepływy OBO. Aby uzyskać więcej informacji o tym, którzy klienci mogą wykonywać wywołania OBO, zobacz [ograniczenia](#client-limitations).

## <a name="protocol-diagram"></a>Diagram protokołu

Załóżmy, że użytkownik został uwierzytelniony w aplikacji przy użyciu [przepływu przyznawania kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md). W tym momencie aplikacja ma token dostępu *dla interfejsu API a* (token a) z oświadczeniami użytkownika i wyraża zgodę na dostęp do internetowego interfejsu API sieci Web (API A). Teraz interfejs API A musi wykonać uwierzytelnione żądanie do podrzędnego interfejsu API sieci Web (API B).

Kroki, które należy wykonać, stanowią "OBO Flow" i objaśniono na poniższym diagramie.

![Pokazuje przepływ protokołu OAuth 2.0 w imieniu użytkownika](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Aplikacja kliencka wysyła żądanie do interfejsu API A z tokenem A (z `aud`m domaganym interfejsem API A).
1. Interfejs API A uwierzytelnia się w punkcie końcowym wystawiania tokenów platformy tożsamości firmy Microsoft i żąda tokenu w celu uzyskania dostępu do interfejsu API B.
1. Punkt końcowy wystawiania tokenów platformy tożsamości firmy Microsoft sprawdza poprawność poświadczeń interfejsu API A i wystawia token dostępu dla interfejsu API B (token B).
1. Token B jest ustawiany w nagłówku autoryzacji żądania do interfejsu API B.
1. Dane z zabezpieczonego zasobu są zwracane przez interfejs API B.

> [!NOTE]
> W tym scenariuszu Usługa warstwy środkowej nie ma interakcji z użytkownikiem, aby uzyskać zgodę użytkownika na dostęp do podrzędnego interfejsu API. W związku z tym, opcja udzielenia dostępu do podrzędnego interfejsu API jest przedstawiona jako część kroku wyrażania zgody podczas uwierzytelniania. Aby dowiedzieć się, jak skonfigurować to ustawienie dla aplikacji, zobacz temat [Uzyskiwanie zgody na aplikację warstwy środkowej](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Żądanie tokenu dostępu między usługami

Aby zażądać tokenu dostępu, wprowadź wpis HTTP do punktu końcowego tokenu platformy tożsamości firmy Microsoft dla dzierżawy z następującymi parametrami.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Istnieją dwa przypadki, w zależności od tego, czy aplikacja kliencka ma być zabezpieczona przez wspólny klucz tajny czy certyfikat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu przy użyciu wspólnego klucza tajnego

Gdy jest używany wspólny klucz tajny, żądanie tokenu dostępu między usługami zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| `grant_type` | Wymagane | Typ żądania tokenu. Dla żądania korzystającego z tokenu JWT wartość musi być `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Wymagane | Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `client_secret` | Wymagane | Wpis tajny klienta wygenerowany dla aplikacji na stronie Azure Portal-Rejestracje aplikacji. |
| `assertion` | Wymagane | Wartość tokenu użytego w żądaniu. |
| `scope` | Wymagane | Rozdzielana spacjami lista zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md). |
| `requested_token_use` | Wymagane | Określa, w jaki sposób żądanie powinno być przetwarzane. W przepływie OBO wartość musi być ustawiona na `on_behalf_of`. |

#### <a name="example"></a>Przykład

Następujące HTTP POST żąda tokenu dostępu i tokenu odświeżania z zakresem `user.read` dla https://graph.microsoft.com internetowego interfejsu API.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem

Żądanie tokenu dostępu między usługami i certyfikatem zawiera następujące parametry:

| Parametr |  | Opis |
| --- | --- | --- |
| `grant_type` | Wymagane | Typ żądania tokenu. Dla żądania korzystającego z tokenu JWT wartość musi być `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Wymagane |  Identyfikator aplikacji (klienta), którą strona [Rejestracje aplikacji Azure Portala](https://go.microsoft.com/fwlink/?linkid=2083908) została przypisana do aplikacji. |
| `client_assertion_type` | Wymagane | Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Wymagane | Potwierdzenie (token sieci Web JSON), które należy utworzyć i podpisać przy użyciu certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenia, zobacz [poświadczenia certyfikatu](active-directory-certificate-credentials.md). |
| `assertion` | Wymagane | Wartość tokenu użytego w żądaniu. |
| `requested_token_use` | Wymagane | Określa, w jaki sposób żądanie powinno być przetwarzane. W przepływie OBO wartość musi być ustawiona na `on_behalf_of`. |
| `scope` | Wymagane | Rozdzielana spacjami lista zakresów dla żądania tokenu. Aby uzyskać więcej informacji, zobacz [zakresy](v2-permissions-and-consent.md).|

Zwróć uwagę, że parametry są prawie takie same, jak w przypadku żądania przez wspólny klucz tajny, z tą różnicą, że parametr `client_secret` jest zastępowany przez dwa parametry: `client_assertion_type` i `client_assertion`.

#### <a name="example"></a>Przykład

Poniższe żądanie HTTP POST ma token dostępu z zakresem `user.read` dla https://graph.microsoft.com internetowego interfejsu API z certyfikatem.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Odpowiedź na token dostępu do usługi

Odpowiedź sukcesu to odpowiedź OAuth 2,0 JSON z poniższymi parametrami.

| Parametr | Opis |
| --- | --- |
| `token_type` | Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez platformę tożsamości firmy Microsoft jest `Bearer`. Aby uzyskać więcej informacji o tokenach okaziciela, zobacz [Framework uwierzytelniania OAuth 2,0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Zakres dostępu udzielony w tokenie. |
| `expires_in` | Czas (w sekundach), przez jaki token dostępu jest prawidłowy. |
| `access_token` | Żądany token dostępu. Usługa wywołująca może używać tego tokenu do uwierzytelniania w usłudze odbiorczej. |
| `refresh_token` | Token odświeżania dla żądanego tokenu dostępu. Usługa wywołująca może używać tego tokenu do żądania innego tokenu dostępu po wygaśnięciu bieżącego tokenu dostępu. Token odświeżania jest dostępny tylko wtedy, gdy zażądano zakresu `offline_access`. |

### <a name="success-response-example"></a>Przykład odpowiedzi sukcesu

Poniższy przykład przedstawia Pomyślne odpowiedzi na żądanie tokenu dostępu dla internetowego interfejsu API https://graph.microsoft.com.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Powyższy token dostępu jest tokenem sformatowanym w wersji 1.0. Wynika to z faktu, że token jest określony na podstawie zasobu, do którego uzyskiwany jest dostęp. Microsoft Graph żąda tokenów v 1.0, więc platforma Microsoft Identity platform tworzy tokeny dostępu w wersji 1.0, gdy klient żąda tokenów dla Microsoft Graph. Tylko aplikacje powinny przeglądać tokeny dostępu. Klienci nie muszą ich sprawdzać.

### <a name="error-response-example"></a>Przykład odpowiedzi na błąd

W przypadku próby uzyskania tokenu dostępu dla podrzędnego interfejsu API zwracany jest komunikat o błędzie, jeśli w przypadku interfejsu API podrzędnego jest ustawiona zasada dostępu warunkowego (na przykład uwierzytelnianie wieloskładnikowe). Usługa warstwy środkowej powinna wystawić ten błąd w aplikacji klienckiej, dzięki czemu aplikacja kliencka może zapewnić interakcję użytkownika w celu spełnienia zasad dostępu warunkowego.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Korzystanie z tokenu dostępu w celu uzyskania dostępu do zabezpieczonego zasobu

Teraz usługa warstwy środkowej może użyć tokenu pozyskanego powyżej, aby wykonać uwierzytelnione żądania do podrzędnego interfejsu API sieci Web, ustawiając token w nagłówku `Authorization`.

### <a name="example"></a>Przykład

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Uzyskanie zgody na aplikację warstwy środkowej

W zależności od odbiorców aplikacji można wziąć pod uwagę różne strategie, aby upewnić się, że przepływ OBO zakończył się pomyślnie. We wszystkich przypadkach ostatecznym celem jest zapewnienie właściwej zgody. W takim przypadku zależy jednak od tego, którzy użytkownicy obsługują aplikację.

### <a name="consent-for-azure-ad-only-applications"></a>Wyrażanie zgody na aplikacje tylko w usłudze Azure AD

#### <a name="default-and-combined-consent"></a>/.default i połączona zgoda

W przypadku aplikacji, które wymagają tylko zalogowania się do konta służbowego, wystarczy zastosować tradycyjne podejście "znane aplikacje klienckie". Aplikacja warstwy środkowej dodaje klienta do listy znanych aplikacji klienckich w jego manifeście, a następnie klient może wyzwolić połączony przepływ zgody zarówno dla siebie, jak i dla aplikacji warstwy środkowej. W punkcie końcowym platformy tożsamości firmy Microsoft odbywa się to przy użyciu [zakresu`/.default`](v2-permissions-and-consent.md#the-default-scope). Gdy Wyzwalasz ekran wyrażania zgody przy użyciu znanych aplikacji klienckich i `/.default`, na ekranie zgody zostaną wyświetlone uprawnienia zarówno dla klienta, jak i do interfejsu API warstwy środkowej, a także żądania uprawnień wymaganych przez interfejs API warstwy środkowej. Użytkownik wyraża zgodę na obie aplikacje, a następnie przepływ OBO działa.

W tej chwili osobisty system konto Microsoft nie obsługuje połączonej zgody i dlatego to podejście nie działa w przypadku aplikacji, które chcą zalogować się do kont osobistych. Osobiste konta Microsoft używane jako konta gościa w dzierżawie są obsługiwane przy użyciu systemu Azure AD i mogą przejść przez połączone wyrażenie.

#### <a name="pre-authorized-applications"></a>Wstępnie autoryzowane aplikacje

Funkcja portalu aplikacji to "wstępnie autoryzowane aplikacje". W ten sposób zasób może wskazywać, że dana aplikacja zawsze ma uprawnienia do odbierania określonych zakresów. Jest to szczególnie przydatne w przypadku, gdy połączenia między klientem frontonu i zasobem zaplecza są bardziej bezproblemowe. Zasób może deklarować wiele wstępnie autoryzowanych aplikacji — każda taka aplikacja może zażądać tych uprawnień w przepływie OBO i odbierać je bez zgody użytkownika.

#### <a name="admin-consent"></a>zgoda administratora

Administrator dzierżawy może zagwarantować, że aplikacje mają uprawnienia do wywoływania wymaganych interfejsów API, zapewniając zgodę administratora na aplikację warstwy środkowej. Aby to zrobić, administrator może znaleźć aplikację warstwy środkowej w swojej dzierżawie, otworzyć stronę wymagane uprawnienia i wybrać opcję udzielenia uprawnienia do aplikacji. Aby dowiedzieć się więcej na temat zgody administratora, zapoznaj się z [dokumentacją dotyczącą zgody i uprawnień](v2-permissions-and-consent.md).

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Wyrażanie zgody na aplikacje usługi Azure AD i konto Microsoft

Ze względu na ograniczenia w modelu uprawnień dla kont osobistych i braku dzierżawy zarządzającej wymagania dotyczące zgody dla kont osobistych są nieco inne niż w przypadku usługi Azure AD. Brak dzierżawców, aby zapewnić zgodę na dostęp do dzierżawy, nie ma możliwości łączenia się z nią. W ten sposób inne strategie same są obecne — należy pamiętać, że są one przeznaczone dla aplikacji, które również muszą obsługiwać tylko konta usługi Azure AD.

#### <a name="use-of-a-single-application"></a>Korzystanie z pojedynczej aplikacji

W niektórych scenariuszach może istnieć tylko jedno skojarzenie klienta warstwy środkowej i frontonu. W tym scenariuszu może być łatwiejsze tworzenie tej pojedynczej aplikacji, co wyklucza konieczność całkowitego zastosowania aplikacji warstwy środkowej. Aby przeprowadzić uwierzytelnianie między frontonem a interfejsem API sieci Web, można użyć plików cookie, id_token lub tokenu dostępu żądanego dla aplikacji. Następnie Zażądaj zgody tej pojedynczej aplikacji na zasób zaplecza.

## <a name="client-limitations"></a>Ograniczenia klienta

Jeśli klient używa niejawnego przepływu do uzyskania id_token, a ten klient ma także symbole wieloznaczne w adresie URL odpowiedzi, nie można użyć id_token dla przepływu OBO.  Jednak tokeny dostępu nabyte za pomocą niejawnego przepływu dotacji nadal mogą być realizowane przez poufnego klienta, nawet jeśli klient inicjujący ma zarejestrowany adres URL odpowiedzi z symbolem wieloznacznym.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o protokole OAuth 2,0 i innym sposobie przeprowadzania uwierzytelniania usługi przy użyciu poświadczeń klienta.

* [Przyznawanie poświadczeń klienta OAuth 2,0 na platformie tożsamości firmy Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Przepływ kodu OAuth 2,0 na platformie tożsamości firmy Microsoft](v2-oauth2-auth-code-flow.md)
* [Używanie zakresu `/.default`](v2-permissions-and-consent.md#the-default-scope)
