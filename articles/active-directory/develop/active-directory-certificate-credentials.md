---
title: Poświadczenia certyfikatu platformy tożsamości firmy Microsoft
titleSuffix: Microsoft identity platform
description: W tym artykule omówiono rejestrację i użycie poświadczeń certyfikatu na potrzeby uwierzytelniania aplikacji.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6330621aac78d5e9df52f2cd3ad9c3968bb0120d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853382"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Poświadczenia certyfikatu uwierzytelniania aplikacji platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft umożliwia aplikacji używanie własnych poświadczeń na potrzeby uwierzytelniania, na przykład w przepływie  [przydzielenia poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0 i przepływie [w imieniu](v2-oauth2-on-behalf-of-flow.md) (OBO).

Jedną z poświadczeń, których może używać aplikacja do uwierzytelniania, jest potwierdzenie [tokenu sieci Web JSON](./security-tokens.md#json-web-tokens-jwts-and-claims) (JWT) podpisane przy użyciu certyfikatu, którego właścicielem jest aplikacja.

## <a name="assertion-format"></a>Format potwierdzenia

Aby obliczyć potwierdzenie, można użyć jednej z wielu bibliotek JWT w wybranym języku. Informacje są przewożone przez token w jego [nagłówku](#header), [oświadczeniach](#claims-payload)i [podpisie](#signature).

### <a name="header"></a>Header

| Parametr |  Dyskusji |
| --- | --- |
| `alg` | Powinien być **RS256** |
| `typ` | Powinien być **JWT** |
| `x5t` | Wartość skrótu certyfikatu X. 509 (określana także jako *odcisk palca*SHA-1 certyfikatu) zakodowana w postaci wartości ciągu Base64. Na przykład, przy użyciu skrótu certyfikatu X. 509 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (szesnastkowo), wartość tego `x5t` żądania będzie `hOBcHZi846VCHSJbFAs26Go9VTQ=` (base64). |

### <a name="claims-payload"></a>Oświadczenia (ładunek)

| Parametr |  Uwagi |
| --- | --- |
| `aud` | Odbiorcy: powinien być `https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` |
| `exp` | Data wygaśnięcia: Data wygaśnięcia tokenu. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0: 0Z) UTC do momentu wygaśnięcia ważności tokenu. Zalecamy użycie krótkiego czasu wygaśnięcia — 10 minut na godzinę.|
| `iss` | Wystawca: powinien być client_id (*Identyfikator klienta)* usługi klienta. |
| `jti` | GUID: Identyfikator JWT |
| `nbf` | Nie przed: Data, przed upływem którego nie można użyć tokenu. Czas jest reprezentowany jako liczba sekund od 1 stycznia 1970 (1970-01-01T0:0: 0Z) UTC do momentu utworzenia potwierdzenia. |
| `sub` | Podmiot: jako dla `iss` , powinien być client_id (*Identyfikator aplikacji)* usługi klienta) |

### <a name="signature"></a>Podpis

Podpis jest obliczany przez zastosowanie certyfikatu zgodnie z opisem w [specyfikacji RFC7519 tokenu internetowego JSON](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Przykład dekodowanego potwierdzenia JWT

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Przykład zaszyfrowanego potwierdzenia JWT

Następujący ciąg jest przykładem zaszyfrowanego potwierdzenia. Jeśli zauważysz uważnie, zobaczysz trzy sekcje oddzielone kropkami ( `.` ):

* Pierwsza sekcja koduje *nagłówek*
* Druga sekcja koduje *oświadczenia* (ładunek)
* Ostatnia sekcja jest *sygnaturą* obliczaną przy użyciu certyfikatów z zawartości pierwszych dwóch sekcji

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Rejestrowanie certyfikatu przy użyciu platformy tożsamości firmy Microsoft

Poświadczenie certyfikatu można skojarzyć z aplikacją kliencką na platformie tożsamości firmy Microsoft za pośrednictwem Azure Portal przy użyciu dowolnej z następujących metod:

### <a name="uploading-the-certificate-file"></a>Przekazywanie pliku certyfikatu

W usłudze Azure App Registration dla aplikacji klienckiej:
1. Wybierz pozycję **certyfikaty & wpisy tajne**.
2. Kliknij pozycję **Przekaż certyfikat** i wybierz plik certyfikatu do przekazania.
3. Kliknij pozycję **Dodaj**.
  Po przekazaniu certyfikatu zostaną wyświetlone wartości odcisku palca, Data rozpoczęcia i wygaśnięcie.

### <a name="updating-the-application-manifest"></a>Aktualizowanie manifestu aplikacji

Mając certyfikat, należy obliczyć:

- `$base64Thumbprint` — Zakodowana algorytmem Base64 wartość skrótu certyfikatu
- `$base64Value` — Zakodowana algorytmem Base64 wartość danych pierwotnych certyfikatu

Należy również podać identyfikator GUID, aby zidentyfikować klucz w manifeście aplikacji ( `$keyId` ).

W usłudze Azure App Registration dla aplikacji klienckiej:
1. Wybierz pozycję **manifest** , aby otworzyć manifest aplikacji.
2. Zastąp Właściwość *poświadczeniami* , podając nowe informacje o certyfikacie, korzystając z poniższego schematu.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Zapisz zmiany w manifeście aplikacji, a następnie Przekaż manifest do platformy tożsamości firmy Microsoft.

   `keyCredentials`Właściwość jest wielowartościowa, więc można przekazać wiele certyfikatów do rozbudowanego zarządzania kluczami.

## <a name="next-steps"></a>Następne kroki

[Aplikacja konsolowa demona .NET Core używająca przykładu kodu platformy Microsoft Identity](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) w witrynie GitHub pokazuje, w jaki sposób aplikacja korzysta z własnych poświadczeń do uwierzytelniania. Przedstawiono w nim również, jak [utworzyć certyfikat z](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) podpisem własnym za pomocą `New-SelfSignedCertificate` polecenia cmdlet programu PowerShell. Możesz również użyć [skryptów tworzenia aplikacji](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) w przykładowym repozytorium do tworzenia certyfikatów, obliczania odcisku palca i tak dalej.
