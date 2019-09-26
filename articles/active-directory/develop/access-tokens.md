---
title: Informacje o tokenach dostępu do platformy tożsamości firmy Microsoft | Azure
description: Dowiedz się więcej o tokenach dostępu emitowanych przez punkty końcowe usługi Azure AD v 1.0 i Microsoft Identity platform (v 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 780ec85438990959b7b0ac686e05ad5db3f9eedf
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291084"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Tokeny dostępu platformy tożsamości firmy Microsoft

Tokeny dostępu umożliwiają klientom bezpieczne wywoływanie interfejsów API chronionych przez platformę Azure. Tokeny dostępu platformy tożsamości firmy Microsoft to [JWTs](https://tools.ietf.org/html/rfc7519), zakodowane w formacie base64 obiekty JSON podpisane przez platformę Azure. Klienci powinni traktować tokeny dostępu jako ciągi nieprzezroczyste, ponieważ zawartość tokenu jest przeznaczona tylko dla zasobu. W celu sprawdzenia poprawności i debugowania deweloperzy mogą zdekodować JWTs za pomocą witryny, takiej jak [JWT.MS](https://jwt.ms). Klient może uzyskać token dostępu z punktu końcowego v 1.0 lub punktu końcowego v 2.0 przy użyciu różnych protokołów.

Gdy klient zażąda tokenu dostępu, usługa Azure AD zwraca również metadane dotyczące tokenu dostępu do użycia aplikacji. Te informacje obejmują czas wygaśnięcia tokenu dostępu i zakresy, dla których jest on prawidłowy. Te dane umożliwiają aplikacji inteligentne buforowanie tokenów dostępu bez konieczności analizowania samego tokenu dostępu.

Jeśli aplikacja jest zasobem (Web API), do którego klienci mogą żądać dostępu, tokeny dostępu zapewniają przydatne informacje do uwierzytelniania i autoryzacji, takie jak użytkownik, klient, wystawca, uprawnienia itd.

Zapoznaj się z następującymi sekcjami, aby dowiedzieć się, jak zasób może sprawdzić poprawność i użyć oświadczeń w ramach tokenu dostępu.

> [!IMPORTANT]
> Tokeny dostępu są tworzone na podstawie *odbiorców* tokenu, co oznacza, że aplikacja jest właścicielem zakresów w tokenie.  Jest to sposób ustawienia `accessTokenAcceptedVersion` zasobu w [manifeście aplikacji](reference-app-manifest.md#manifest-reference) , dzięki któremu `2` klient wywołujący punkt końcowy v 1.0 otrzymuje token dostępu w wersji 2.0.  Analogicznie, dlatego zmiana [opcjonalnych oświadczeń](active-directory-optional-claims.md) tokenu dostępu dla klienta nie zmienia tokenu dostępu otrzymanego, gdy wymagany jest token dla `user.read`, który należy do zasobu programu MS Graph.  
> Z tego samego powodu podczas testowania aplikacji klienckiej przy użyciu konta osobistego (takiego jak hotmail.com lub outlook.com), może się okazać, że token dostępu otrzymany przez klienta jest nieprzezroczystym ciągiem. Wynika to z faktu, że dostęp do zasobu wymaga starszych biletów (konto Microsoft), które są zaszyfrowane i nie mogą być zrozumiałe dla klienta.

## <a name="sample-tokens"></a>Przykładowe tokeny

tokeny 1.0 i v 2.0 wyglądają podobnie i zawierają wiele z tych samych oświadczeń. Przykładem każdego z nich jest tutaj.

### <a name="v10"></a>Wersja 1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Wyświetl ten token v 1.0 w [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>Wersja 2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Wyświetl ten token v 2.0 w [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Oświadczenia w tokenach dostępu

JWTs są podzielone na trzy sztuki:

* **Nagłówek** — zawiera informacje na temat [weryfikowania tokenu, w](#validating-tokens) tym informacje o typie tokenu i sposobie jego podpisywania.
* **Ładunek** — zawiera wszystkie ważne dane dotyczące użytkownika lub aplikacji próbujących wywołać usługę.
* **Signature** — jest surowcem używanym do sprawdzania poprawności tokenu.

Każdy element jest oddzielony kropką (`.`) i osobno zakodowany w formacie base64.

Oświadczenia są obecne tylko wtedy, gdy istnieje wartość do wypełnienia. W związku z tym aplikacja nie powinna być zależne od obecnego żądania. Przykładowo `family_name` (nie każdy dzierżawca wymaga hasła) lub (przepływy[poświadczeń klienta](v1-oauth2-client-creds-grant-flow.md) znajdują się w imieniu aplikacji, które nie mają nazw). `pwd_exp` Oświadczenia używane do sprawdzania poprawności tokenu dostępu będą zawsze obecne.

> [!NOTE]
> Niektóre oświadczenia są używane do zabezpieczania tokenów usługi Azure AD w przypadku ponownego użycia. Są one oznaczane jako nieprzeznaczone do użycia publicznego w opisie jako "nieprzezroczyste". Te oświadczenia mogą lub nie mogą występować w tokenie, a nowe mogą zostać dodane bez powiadomienia.

### <a name="header-claims"></a>Oświadczenia nagłówka

|Oświadczenie | Format | Opis |
|--------|--------|-------------|
| `typ` | Ciąg — zawsze "JWT" | Wskazuje, że token jest JWT.|
| `nonce` | String | Unikatowy identyfikator używany do ochrony przed atakami polegającymi na powtarzaniu tokenu. Zasób może zapisać tę wartość, aby chronić przed odtwarzaniem. |
| `alg` | String | Wskazuje algorytm, który został użyty do podpisania tokenu, na przykład "RS256" |
| `kid` | String | Określa odcisk palca klucza publicznego, który jest używany do podpisywania tego tokenu. Emitowane w tokenach dostępu zarówno w wersji 1.0, jak i 2.0. |
| `x5t` | String | Działa tak samo (w użyciu i wartości) jak `kid`. `x5t`czy starsze zgłoszenie jest emitowane tylko w tokenach dostępu w wersji 1.0 dla celów zgodności. |

### <a name="payload-claims"></a>Oświadczenia ładunku

| Oświadczenie | Format | Opis |
|-----|--------|-------------|
| `aud` | Ciąg, identyfikator URI aplikacji | Identyfikuje zamierzony odbiorcę tokenu. W tokenach identyfikatorów odbiorcy jest identyfikator aplikacji aplikacji przypisany do aplikacji w Azure Portal. Twoja aplikacja powinna sprawdzić poprawność tej wartości i odrzucić token, jeśli wartość nie jest zgodna. |
| `iss` | Ciąg, identyfikator URI usługi STS | Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token oraz dzierżawę usługi Azure AD, w której użytkownik został uwierzytelniony. Jeśli token wystawiony jest tokenem v 2.0 (patrz `ver` "Claim"), identyfikator URI `/v2.0`zakończy się. Identyfikator GUID, który wskazuje, że użytkownik jest użytkownikiem konsumenta z konto Microsoft to `9188040d-6c67-4c5b-b112-36a304b66dad`. Twoja aplikacja powinna używać identyfikatora GUID w ramach żądania, aby ograniczyć zbiór dzierżawców, którzy mogą logować się do aplikacji, jeśli ma to zastosowanie. |
|`idp`| Ciąg, zazwyczaj identyfikator URI usługi STS | Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość odszkodowania wystawcy, chyba że konto użytkownika nie znajduje się w tej samej dzierżawie co wystawcy, na przykład. Jeśli oświadczenia nie jest obecny, oznacza to, że `iss` można użyć zamiast niego wartości.  W przypadku kont osobistych używanych w kontekście organizacyjnym (np. konta osobistego zaproszonego do dzierżawy usługi Azure AD) `idp` może istnieć wartość "Live.com" lub identyfikator URI usługi STS zawierający dzierżawcę `9188040d-6c67-4c5b-b112-36a304b66dad`konto Microsoft. |  
| `iat` | int, sygnatura czasowa systemu UNIX | "Wystawiony w" wskazuje, kiedy wystąpiło uwierzytelnianie dla tego tokenu. |
| `nbf` | int, sygnatura czasowa systemu UNIX | Wartość "NBF" (nie wcześniej) określa czas, po którym nie można zatwierdzić tokenu JWT do przetwarzania. |
| `exp` | int, sygnatura czasowa systemu UNIX | Wartość "EXP" (czas wygaśnięcia) określa czas wygaśnięcia w dniu lub, po którym nie można zaakceptować tokenu JWT do przetworzenia. Należy pamiętać, że zasób może odrzucić token przed tym terminem, na przykład jeśli wymagana jest zmiana uwierzytelniania lub wykryto odwołanie do tokenu. |
| `aio` | Ciąg nieprzezroczysty | Deklaracja wewnętrzna używana przez usługę Azure AD do rejestrowania danych do ponownego użycia tokenu. Zasoby nie powinny używać tego żądania. |
| `acr` | Ciąg, "0" lub "1" | Występuje tylko w tokenach v 1.0. Wartość żądania "Klasa kontekstu uwierzytelniania". Wartość "0" wskazuje, że uwierzytelnianie użytkownika końcowego nie spełnia wymagań ISO/IEC 29115. |
| `amr` | Tablica JSON ciągów | Występuje tylko w tokenach v 1.0. Określa sposób uwierzytelniania podmiotu tokenu. Aby uzyskać więcej informacji [, zobacz sekcję "AMR](#the-amr-claim) ". |
| `appid` | Ciąg, identyfikator GUID | Występuje tylko w tokenach v 1.0. Identyfikator aplikacji klienta korzystającej z tokenu. Aplikacja może działać jako sama lub w imieniu użytkownika. Identyfikator aplikacji zazwyczaj reprezentuje obiekt aplikacji, ale może również reprezentować obiekt główny usługi w usłudze Azure AD. |
| `appidacr` | "0", "1" lub "2" | Występuje tylko w tokenach v 1.0. Wskazuje, w jaki sposób klient został uwierzytelniony. W przypadku klienta publicznego wartością jest "0". Jeśli używasz identyfikatora klienta i klucza tajnego klienta, wartość jest równa "1". Jeśli certyfikat klienta został użyty do uwierzytelnienia, wartość jest równa "2". |
| `azp` | Ciąg, identyfikator GUID | Występuje tylko w przypadku tokenów v 2.0, zamiennik `appid`dla. Identyfikator aplikacji klienta korzystającej z tokenu. Aplikacja może działać jako sama lub w imieniu użytkownika. Identyfikator aplikacji zazwyczaj reprezentuje obiekt aplikacji, ale może również reprezentować obiekt główny usługi w usłudze Azure AD. |
| `azpacr` | "0", "1" lub "2" | Występuje tylko w przypadku tokenów v 2.0, zamiennik `appidacr`dla. Wskazuje, w jaki sposób klient został uwierzytelniony. W przypadku klienta publicznego wartością jest "0". Jeśli używasz identyfikatora klienta i klucza tajnego klienta, wartość jest równa "1". Jeśli certyfikat klienta został użyty do uwierzytelnienia, wartość jest równa "2". |
| `preferred_username` | String | Podstawowa nazwa użytkownika, która reprezentuje użytkownika. Może to być adres e-mail, numer telefonu lub ogólna nazwa użytkownika bez określonego formatu. Jego wartość jest modyfikowalna i może ulec zmianie w czasie. Ponieważ jest modyfikowalny, ta wartość nie może być używana do podejmowania decyzji dotyczących autoryzacji.  Można go użyć dla wskazówek dotyczących nazwy użytkownika. `profile` Zakres jest wymagany w celu otrzymania tego żądania. |
| `name` | String | Zapewnia czytelną dla człowieka wartość, która identyfikuje podmiot tokenu. Wartość nie może być unikatowa, jest modyfikowalna i przeznaczona do użycia tylko do celów wyświetlania. `profile` Zakres jest wymagany w celu otrzymania tego żądania. |
| `scp` | Ciąg, rozdzielana spacjami lista zakresów | Zestaw zakresów uwidocznionych przez aplikację, dla których aplikacja kliencka zażądała (i została odebrana). Aplikacja powinna sprawdzić, czy te zakresy są prawidłowe dla danej aplikacji, i podejmować decyzje dotyczące autoryzacji na podstawie wartości tych zakresów. Uwzględnione tylko w przypadku [tokenów użytkowników](#user-and-application-tokens). |
| `roles` | Tablica ciągów, lista uprawnień | Zestaw uprawnień uwidocznionych przez aplikację, dla których aplikacja żądająca lub użytkownik przyznał uprawnienia do wywoływania. W przypadku [tokenów aplikacji](#user-and-application-tokens)ta wartość jest używana podczas przepływu [poświadczeń klienta](v1-oauth2-client-creds-grant-flow.md) zamiast zakresów użytkownika.  W przypadku [tokenów użytkowników](#user-and-application-tokens) jest to wypełniane rolami przypisanymi do użytkownika w aplikacji docelowej. |
| `wids` | Tablica identyfikatorów GUID [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) | Wskazuje role w całej dzierżawie przypisane do tego użytkownika, w sekcji ról znajdującej się na [stronie role administratora](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids).  To zgłoszenie jest konfigurowane dla poszczególnych aplikacji, przez `groupMembershipClaims` Właściwość [manifestu aplikacji](reference-app-manifest.md).  Ustawienie go na "All" lub "DirectoryRole" jest wymagane.  Może nie być obecny w tokenach uzyskanych za pomocą niejawnego przepływu ze względu na długość tokenu. |
| `groups` | Tablica JSON identyfikatorów GUID | Dostarcza identyfikatory obiektów, które reprezentują członkostwo w grupach podmiotu. Te wartości są unikatowe (zobacz identyfikator obiektu) i mogą być bezpiecznie używane do zarządzania dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grupy zawarte w ramach roszczeń grup są konfigurowane dla poszczególnych aplikacji, przez `groupMembershipClaims` Właściwość [manifestu aplikacji](reference-app-manifest.md). Wartość null spowoduje wykluczenie wszystkich grup, a wartość "Security Group" będzie zawierać tylko Active Directory członkostwa w grupie zabezpieczeń, a wartość "All" będzie obejmować zarówno grupy zabezpieczeń, jak i listy dystrybucyjne pakietu Office 365. <br><br>Zapoznaj `hasgroups` się z poniższym zgłoszeniem, aby `groups` uzyskać szczegółowe informacje na temat używania roszczeń z niejawnym udzieleniem. <br>W przypadku innych przepływów, jeśli liczba grup, do których należy użytkownik, przekracza limit (150 dla protokołu SAML, 200 dla tokenu JWT), do źródeł roszczeń zostanie dodana wartość nadwyżkowa, która wskazuje punkt końcowy grafu usługi AAD zawierający listę grup dla użytkownika. |
| `hasgroups` | Boolean | Jeśli jest obecny, `true`zawsze oznacza to, że użytkownik należy do co najmniej jednej grupy. Używane zamiast `groups` roszczeń dla JWTs w niejawnej postaci, jeśli w ramach żądania Full Groups zostanie rozszerzona wartość fragmentu identyfikatora URI poza limitami długości adresów URL (obecnie 6 lub więcej grup). Wskazuje, że klient powinien użyć grafu do określenia grup użytkownika (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Obiekt JSON | W przypadku żądań tokenów, które nie mają ograniczonej `hasgroups` długości (patrz powyżej), ale wciąż za duże dla tokenu, zostanie uwzględniony link do listy pełnych grup dla użytkownika. W przypadku JWTs jako roszczeń rozproszonych, w przypadku protokołu SAML jako nowego odszkodowania zamiast `groups` zgłoszenia. <br><br>**Przykładowa wartość JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | Ciąg, identyfikator GUID | Podmiot zabezpieczeń, dla którego token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Może służyć do bezpiecznego sprawdzania autoryzacji, na przykład gdy token jest używany w celu uzyskania dostępu do zasobu i może być używany jako klucz w tabelach bazy danych. Ponieważ podmiot jest zawsze obecny w tokenach, które są problemy z usługą Azure AD, zalecamy użycie tej wartości w systemie autoryzacji ogólnego przeznaczenia. Podmiot jest jednak identyfikatorem parowania — jest unikatowy dla określonego identyfikatora aplikacji. W związku z tym, jeśli pojedynczy użytkownik zaloguje się do dwóch różnych aplikacji przy użyciu dwóch różnych identyfikatorów klienta, te aplikacje otrzymają dwie różne wartości dla zgłoszenia podmiotu. Może to być niepotrzebne, w zależności od wymagań dotyczących architektury i ochrony prywatności. Zobacz też, `oid` jak również to jest takie samo, jak w przypadku wszystkich aplikacji w ramach dzierżawy. |
| `oid` | Ciąg, identyfikator GUID | Niezmienny identyfikator dla obiektu na platformie tożsamości firmy Microsoft, w tym przypadku, konto użytkownika. Może również służyć do bezpiecznego sprawdzania autoryzacji i jako klucz w tabelach bazy danych. Ten identyfikator jednoznacznie identyfikuje użytkownika w wielu aplikacjach — dwie różne aplikacje, które logują się w tym samym użytkowniku, otrzymają taką samą wartość w `oid` ramach roszczeń. W związku `oid` z tym, mogą być używane podczas wykonywania zapytań do usługi online firmy Microsoft, takich jak Microsoft Graph. Microsoft Graph zwróci ten identyfikator jako `id` właściwość dla danego konta użytkownika. Ponieważ umożliwia wielu aplikacjom skorelowanie użytkowników `profile` , zakres jest wymagany w celu otrzymania tego żądania. `oid` Należy pamiętać, że jeśli pojedynczy użytkownik istnieje w wielu dzierżawach, użytkownik będzie zawierać inny identyfikator obiektu w każdej dzierżawie — jest uznawany za różne konta, nawet jeśli użytkownik loguje się do każdego konta z tymi samymi poświadczeniami. |
| `tid` | Ciąg, identyfikator GUID | Reprezentuje dzierżawę usługi Azure AD, z której korzysta użytkownik. W przypadku kont służbowych identyfikator GUID jest niezmiennym IDENTYFIKATORem dzierżawy organizacji, do której należy użytkownik. W przypadku kont osobistych wartość to `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Zakres jest wymagany w celu otrzymania tego żądania. |
| `unique_name` | String | Występuje tylko w tokenach v 1.0. Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie powinna być unikatowa w ramach dzierżawy i powinna być używana tylko do wyświetlania. |
| `uti` | Ciąg nieprzezroczysty | Wyjątek wewnętrzny używany przez platformę Azure do weryfikacji tokenów. Zasoby nie powinny korzystać z tego żądania. |
| `rh` | Ciąg nieprzezroczysty | Wyjątek wewnętrzny używany przez platformę Azure do weryfikacji tokenów. Zasoby nie powinny używać tego żądania. |
| `ver` | Ciąg znaków `1.0` lub`2.0` | Wskazuje wersję tokenu dostępu. |


> [! Zgłoszenie nadwyżkowe grupy] aby mieć pewność, że rozmiar tokenu nie przekracza limitów rozmiaru nagłówka HTTP, usługa Azure AD ogranicza liczbę identyfikatorów obiektów uwzględnionych w ramach żądania grup. Jeśli użytkownik jest członkiem większej liczby grup niż limit nadwyżkowy (150 dla tokenów SAML, 200 dla tokenów JWT), usługa Azure AD nie emituje roszczeń grupowych w tokenie. Zamiast tego zawiera w tokenie wystąpienie nadwyżkowe, które wskazuje aplikacji, w której ma być wysyłana kwerenda interfejs API programu Graph w celu pobrania członkostwa w grupie użytkownika.
> { ... "_claim_names": {"Groups": "src1"}, {"_claim_sources": {"src1": {"Endpoint": "[adres URL programu Graph do pobrania członkostwa w grupie tego użytkownika]"}}    
    ... } Możesz użyć `BulkCreateGroups.ps1` podanego w folderze [Skrypty tworzenia aplikacji](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims/blob/master/AppCreationScripts/) , aby pomóc w testowaniu scenariuszy.

#### <a name="v10-basic-claims"></a>podstawowe oświadczenia 1.0

Następujące oświadczenia zostaną uwzględnione w tokenach w wersji 1.0, jeśli ma zastosowanie, ale nie są domyślnie uwzględniane w tokenach programu v 2.0. Jeśli używasz programu v 2.0 i potrzebujesz jednego z tych oświadczeń, zażądaj ich przy użyciu [opcjonalnych oświadczeń](active-directory-optional-claims.md).

| Oświadczenie | Format | Opis |
|-----|--------|-------------|
| `ipaddr`| String | Adres IP, z którego użytkownik został uwierzytelniony. |
| `onprem_sid`| Ciąg w [formacie identyfikatora SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | W przypadkach, w których użytkownik ma uwierzytelnianie lokalne, to zgłoszenie zapewnia swój identyfikator SID. Programu można użyć `onprem_sid` do autoryzacji w starszych aplikacjach.|
| `pwd_exp`| int, sygnatura czasowa systemu UNIX | Wskazuje, kiedy wygasa hasło użytkownika. |
| `pwd_url`| String | Adres URL, pod którym można wysyłać użytkowników w celu zresetowania hasła. |
| `in_corp`| boolean | Sygnalizuje, czy klient loguje się z sieci firmowej. Jeśli nie, oświadczenia nie są uwzględniane. |
| `nickname`| String | Dodatkowa nazwa użytkownika, oddzielona od imię lub nazwisko.|
| `family_name` | String | Zawiera nazwisko, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika. |
| `given_name` | String | Udostępnia imię i nazwisko użytkownika, zgodnie z ustawieniem obiektu użytkownika. |
| `upn` | String | Nazwa użytkownika. Może to być numer telefonu, adres e-mail lub niesformatowany ciąg. Powinna być używana tylko do celów wyświetlania i dostarczająca wskazówki dotyczące nazwy użytkownika w scenariuszach ponownego uwierzytelniania. |

#### <a name="the-amr-claim"></a>`amr` Zgłoszenie

Tożsamości firmy Microsoft mogą być uwierzytelniane na różne sposoby, które mogą być odpowiednie dla Twojej aplikacji. To jest tablica, która może zawierać wiele elementów, takich jak `["mfa", "rsa", "pwd"]`, do uwierzytelniania, które używały zarówno hasła, jak i aplikacji uwierzytelniania. `amr`

| Value | Opis |
|-----|-------------|
| `pwd` | Uwierzytelnianie hasła albo hasło użytkownika firmy Microsoft lub klucz tajny klienta aplikacji. |
| `rsa` | Uwierzytelnianie było oparte na weryfikacji klucza RSA, na przykład z [aplikacją Microsoft Authenticator](https://aka.ms/AA2kvvu). Obejmuje to, czy uwierzytelnianie zostało wykonane przy użyciu certyfikatu JWT z podpisem własnym z certyfikatem x509 usługi. |
| `otp` | Jednorazowy kod dostępu przy użyciu wiadomości e-mail lub wiadomości tekstowej. |
| `fed` | Zostało użyte potwierdzenie uwierzytelniania federacyjnego (takie jak JWT lub SAML). |
| `wia` | Zintegrowane uwierzytelnianie systemu Windows |
| `mfa` | Użyto uwierzytelniania wieloskładnikowego. Gdy jest obecny, zostaną uwzględnione również inne metody uwierzytelniania. |
| `ngcmfa` | Równoważne z `mfa`, używane do inicjowania obsługi niektórych zaawansowanych typów poświadczeń. |
| `wiaormfa`| Użytkownik użył systemu Windows lub poświadczenia usługi MFA do uwierzytelnienia. |
| `none` | Nie zostało wykonane żadne uwierzytelnianie. |

## <a name="validating-tokens"></a>Sprawdzanie poprawności tokenów

Aby sprawdzić poprawność id_token lub access_token, aplikacja powinna sprawdzić poprawność podpisu tokenu i oświadczeń. Aby sprawdzić poprawność tokenów dostępu, aplikacja powinna również sprawdzić poprawność wystawcy, odbiorców i tokenów podpisywania. Należy sprawdzić poprawność tych wartości w dokumencie odnajdywania OpenID Connect. Na przykład wersja dokumentu niezależna od dzierżawy znajduje się w lokalizacji [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Oprogramowanie pośredniczące usługi Azure AD ma wbudowane funkcje do sprawdzania poprawności tokenów dostępu. możesz przeglądać nasze [przykłady](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) , aby znaleźć je w wybranym języku. Aby uzyskać więcej informacji na temat jawnego sprawdzania poprawności tokenu JWT, zapoznaj się z [przykładem ręcznej walidacji JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).

Udostępniamy biblioteki i przykłady kodu, które pokazują, jak łatwo obsługiwać sprawdzanie poprawności tokenu. Poniższe informacje są dostępne dla osób, które chcą zrozumieć proces podstawowy. Istnieje również kilka bibliotek Open-Source innych firm dostępnych do sprawdzania poprawności tokenu JWT — istnieje co najmniej jedna opcja dla niemal każdej platformy i języka. Aby uzyskać więcej informacji na temat bibliotek uwierzytelniania i przykładów kodu usługi Azure AD, zobacz biblioteki [uwierzytelniania v 1.0](active-directory-authentication-libraries.md) i [biblioteki uwierzytelniania w wersji 2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Weryfikowanie podpisu

Token JWT zawiera trzy segmenty, które są oddzielane `.` znakiem. Pierwszy segment jest znany jako **nagłówek**, drugi jako **treść**, a trzeci jako **sygnatura**. Segment podpisu może służyć do weryfikowania autentyczności tokenu, aby mógł być zaufany przez aplikację.

Tokeny wystawione przez usługę Azure AD są podpisywane przy użyciu standardowych algorytmów szyfrowania asymetrycznego, takich jak RSA 256. Nagłówek JWT zawiera informacje na temat metody klucza i szyfrowania użytej do podpisania tokenu:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

To `alg` zastrzeżenie wskazuje algorytm, który został użyty do podpisania tokenu, podczas gdy `kid` to zgłoszenie wskazuje określony klucz publiczny, który został użyty do zweryfikowania tokenu.

W dowolnym momencie usługa Azure AD może podpisać id_token przy użyciu dowolnego zestawu par kluczy publiczny-prywatny. Usługa Azure AD umożliwia okresowe obracanie możliwego zestawu kluczy, dlatego należy napisać aplikację w celu automatycznego obsłużenia tych zmian. Rozsądna częstotliwość sprawdzania dostępności aktualizacji kluczy publicznych używanych przez usługę Azure AD wynosi co 24 godziny.

Możesz uzyskać dane klucza podpisywania niezbędne do zweryfikowania podpisu za pomocą [dokumentu OpenID Connect Connect Metadata](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) w lokalizacji:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Wypróbuj ten [adres URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) w przeglądarce.

Ten dokument metadanych:

* Jest obiektem JSON zawierającym kilka przydatnych informacji, takich jak lokalizacja różnych punktów końcowych wymaganych do wykonania uwierzytelniania OpenID Connect Connect.
* Zawiera element `jwks_uri`, który zawiera lokalizację zestawu kluczy publicznych używanych do podpisywania tokenów. Dokument JSON znajdujący się w `jwks_uri` zawiera wszystkie informacje o kluczu publicznym używany w tym konkretnym momencie. Twoja aplikacja może użyć `kid` w nagłówku JWT żądania, aby wybrać klucz publiczny w tym dokumencie, który został użyty do podpisania określonego tokenu. Następnie można sprawdzić poprawność podpisu przy użyciu prawidłowego klucza publicznego i wskazanego algorytmu.

> [!NOTE]
> Punkt końcowy v 1.0 zwraca zarówno `x5t` oświadczenia, jak i `kid` punkt końcowy v 2.0 `kid` reagują tylko na oświadczenie. W przyszłości zalecamy użycie tego `kid` żądania do zweryfikowania tokenu.

Sprawdzanie poprawności podpisu jest poza zakresem tego dokumentu — istnieje wiele dostępnych bibliotek typu open source, które ułatwiają wykonywanie tych czynności w razie potrzeby.  Jednak platforma tożsamości firmy Microsoft ma jedno rozszerzenie podpisywania tokenu do standardów — niestandardowe klucze podpisywania.  

Jeśli aplikacja ma niestandardowe klucze podpisywania w wyniku użycia funkcji [mapowania oświadczeń](active-directory-claims-mapping.md) , należy dołączyć `appid` parametr zapytania zawierający `jwks_uri` identyfikator aplikacji, aby uzyskać wskazanie informacji o kluczu podpisywania aplikacji, które powinny być używane dla zatwierdzenia. Na przykład: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` zawiera `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autoryzacja oparta na oświadczeniach

W ramach logiki biznesowej aplikacji zostanie wyznaczony ten krok, niektóre typowe metody autoryzacji są opisane poniżej.

* `scp` Sprawdź lub `roles` Przejmij, aby sprawdzić, czy wszystkie obecne zakresy są zgodne z tymi, które są udostępniane przez interfejs API, i zezwól klientowi na wykonywanie żądanych akcji.
* Upewnij się, że klient wywołujący może wywołać interfejs API przy `appid` użyciu tego żądania.
* Sprawdź poprawność stanu uwierzytelniania klienta `appidacr` wywołującego, ale nie powinna być równa 0, jeśli klienci publiczni nie mogą wywoływać interfejsu API.
* Sprawdź listę wcześniejszych `nonce` oświadczeń, aby sprawdzić, czy token nie jest odtwarzany.
* Sprawdź, czy `tid` jest zgodny z dzierżawcą, który może wywołać interfejs API.
* Użyj tego `acr` żądania, aby sprawdzić, czy użytkownik wykonał uwierzytelnianie wieloskładnikowe. Należy to wymusić przy użyciu [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Jeśli zażądano `roles` oświadczeń lub `groups` oświadczenia w tokenie dostępu, należy sprawdzić, czy użytkownik znajduje się w grupie, która może wykonać tę akcję.
  * W przypadku tokenów pobranych przy użyciu niejawnego przepływu prawdopodobnie trzeba będzie wykonać zapytanie dotyczące [Microsoft Graph](https://developer.microsoft.com/graph/) dla tych danych, ponieważ jest ono często zbyt duże, aby zmieścić je w tokenie.

## <a name="user-and-application-tokens"></a>Tokeny użytkownika i aplikacji

Twoja aplikacja może otrzymywać tokeny w imieniu użytkownika (zwykły przepływ) lub bezpośrednio z aplikacji (za pośrednictwem [przepływu poświadczeń klienta](v1-oauth2-client-creds-grant-flow.md)). Te tokeny obsługujące tylko aplikacje wskazują, że to wywołanie pochodzi z aplikacji i nie ma do niego kopii zapasowej. Te tokeny są obsługiwane w dużym stopniu, a niektóre różnice:

* Tokeny tylko do aplikacji nie będą miały `scp` żadnego odszkodowania i mogą `roles` mieć takie wnioski. Jest to miejsce, w którym będą rejestrowane uprawnienia aplikacji (w przeciwieństwie do delegowanych uprawnień). Aby uzyskać więcej informacji na temat uprawnień delegowanych i aplikacji, zobacz uprawnienie i zgoda w [wersji 1.0](v1-permissions-and-consent.md) i [2.0](v2-permissions-and-consent.md).
* Brak wielu oświadczeń specyficznych dla człowieka, takich jak `name` lub. `upn`
* Oświadczenia `sub` i`oid` będą takie same. 

## <a name="token-revocation"></a>Odwołanie do tokenu

Tokeny odświeżania można unieważniać lub odwołać w dowolnym momencie, z różnych powodów. Są one podzielone na dwie główne kategorie: przekroczenia limitu czasu i odwołania.

### <a name="token-timeouts"></a>Limity czasu tokenu

* MaxInactiveTime: Jeśli token odświeżania nie został użyty w czasie określonym przez MaxInactiveTime, token odświeżania nie będzie już prawidłowy.
* MaxSessionAge: Jeśli MaxAgeSessionMultiFactor lub MaxAgeSessionSingleFactor zostały ustawione na inne niż domyślne (do momentu odwołania), uwierzytelnianie będzie wymagane po upływie czasu określonego w MaxAgeSession *.
* Przykłady:
  * Dzierżawa ma MaxInactiveTime przez pięć dni, a użytkownik wyszedł urlop przez tydzień, a więc usługa Azure AD nie widziała nowego żądania tokenu od użytkownika w ciągu 7 dni. Gdy następnym razem użytkownik zażąda nowego tokenu, zobaczy token odświeżenia, który został odwołany, i musi wprowadzić ponownie swoje poświadczenia.
  * Poufne aplikacje mają MaxAgeSessionSingleFactor jeden dzień. Jeśli użytkownik zaloguje się w poniedziałek i we wtorek (po 25 godzinach upłynął), będzie wymagane do ponownego uwierzytelnienia.

### <a name="revocation"></a>Unieważni

|   | Plik cookie oparty na hasłach | Token oparty na hasłach | Plik cookie bez hasła | Token nieoparty na haśle | Poufny token klienta |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Hasło wygasa | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna |
| Hasło zostało zmienione przez użytkownika | Odwołano | Odwołano | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna |
| Użytkownik wykonuje SSPR | Odwołano | Odwołano | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna |
| Administrator resetuje hasło | Odwołano | Odwołano | Pozostaje aktywna | Pozostaje aktywna | Pozostaje aktywna |
| Użytkownik odwołuje tokeny odświeżania [za pośrednictwem programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Odwołano | Odwołano | Odwołano | Odwołano | Odwołano |
| Administrator odwołuje wszystkie tokeny odświeżania dla dzierżawy [za pośrednictwem programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Odwołano | Odwołano |Odwołano | Odwołano | Odwołano |
| [Logowanie](v1-protocols-openid-connect-code.md#single-sign-out) jednokrotne w sieci Web | Odwołano | Pozostaje aktywna | Odwołano | Pozostaje aktywna | Pozostaje aktywna |

> [!NOTE]
> Nazwa logowania oparta na hasłach jest taka, w której użytkownik nie pobrał hasła, aby je pobrać. Na przykład przy użyciu funkcji Windows Hello, klucza FIDO lub numeru PIN.
>
> Istnieje znany problem z podstawowym tokenem odświeżania systemu Windows. Jeśli PRT jest uzyskiwany za pośrednictwem hasła, a następnie użytkownik loguje się za pośrednictwem powitania, nie spowoduje to zmiany pochodzenia PRT i zostanie odwołane, jeśli użytkownik zmieni hasło.
>
> Tokeny odświeżania nie są unieważnione lub odwoływane, gdy są używane do pobierania nowego tokenu dostępu i odświeżania tokenu.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [ `id_tokens` usłudze Azure AD](id-tokens.md).
* Uzyskaj informacje na temat uprawnień i zgody w [wersjach 1.0](v1-permissions-and-consent.md) i [2.0](v2-permissions-and-consent.md).
