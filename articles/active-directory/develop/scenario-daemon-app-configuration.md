---
title: Konfigurowanie aplikacji demonów, które wywołują internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak skonfigurować kod aplikacji demona, która wywołuje internetowe interfejsy API (konfiguracja aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: fc441ef64f98ace04b7b847c03d575215656f9db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611839"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplikacja Demon, która wywołuje internetowe interfejsy API - konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji demona, która wywołuje internetowe interfejsy API.

## <a name="msal-libraries-that-support-daemon-apps"></a>Biblioteki MSAL obsługujące aplikacje demonów

Te biblioteki firmy Microsoft obsługują aplikacje demonów:

  Biblioteka MSAL | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Platformy .NET Framework i .NET Core są obsługiwane do tworzenia aplikacji demonów. (Platformy uniwersalne, Xamarin.iOS i Xamarin.Android nie są obsługiwane, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MĘTÓW MSAL | Obsługa aplikacji demonów w Pythonie.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Obsługa aplikacji demonów w javie.

## <a name="configure-the-authority"></a>Konfigurowanie urzędu

Aplikacje demonów używają uprawnień aplikacji, a nie uprawnień delegowanych. Dlatego ich obsługiwany typ konta nie może być kontem w żadnym katalogu organizacyjnym ani żadnym osobistym koncie Microsoft (na przykład Skype, Xbox, Outlook.com). Nie ma administratora dzierżawy, aby udzielić zgody na aplikację demona dla konta osobistego Microsoft. Musisz wybrać konta *w mojej organizacji* lub *konta w dowolnej organizacji*.

Dlatego urząd określony w konfiguracji aplikacji powinien być dzierżawiony (określając identyfikator dzierżawy lub nazwę domeny skojarzoną z organizacją).

Jeśli jesteś niezależną instytucją żyłacą i chcesz `organizations`udostępnić narzędzie wielodostępne, możesz użyć programu . Pamiętaj jednak, że musisz również wyjaśnić swoim klientom, jak udzielić zgody administratora. Aby uzyskać szczegółowe informacje, zobacz [Żądanie zgody dla całej dzierżawy.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) Ponadto obecnie istnieje ograniczenie w MSAL: `organizations` jest dozwolone tylko wtedy, gdy poświadczenia klienta są kluczem tajnym aplikacji (nie certyfikat).

## <a name="configure-and-instantiate-the-application"></a>Konfigurowanie i tworzenie wystąpienia aplikacji

W bibliotekach MSAL poświadczenia klienta (klucz tajny lub certyfikat) są przekazywane jako parametr konstrukcji poufnej aplikacji klienckiej.

> [!IMPORTANT]
> Nawet jeśli aplikacja jest aplikacją konsoli, która działa jako usługa, jeśli jest to aplikacja demona, musi być poufną aplikacją kliencką.

### <a name="configuration-file"></a>Plik konfiguracji

Plik konfiguracyjny definiuje:

- Urząd lub wystąpienie chmury i identyfikator dzierżawy.
- Identyfikator klienta, który otrzymałeś z rejestracji aplikacji.
- Klucz tajny klienta lub certyfikat.

# <a name="net"></a>[.NET](#tab/dotnet)

[appsettings.json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) z próbki [demona konsoli .NET Core.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Podajesz `ClientSecret` albo lub `CertificateName`. Te ustawienia są wyłączne.

# <a name="python"></a>[Python](#tab/python)

Podczas tworzenia poufnego klienta z wpisami tajnymi klienta plik konfiguracyjny [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) w przykładzie [demona Języka Python](https://github.com/Azure-Samples/ms-identity-python-daemon) jest następujący:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Podczas tworzenia poufnego klienta z certyfikatami plik konfiguracyjny [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) w przykładzie [demona Języka Python](https://github.com/Azure-Samples/ms-identity-python-daemon) jest następujący:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

---

### <a name="instantiate-the-msal-application"></a>Tworzenie wystąpienia aplikacji MSAL

Aby utworzyć wystąpienia aplikacji MSAL, należy dodać, odwołać się lub zaimportować pakiet MSAL (w zależności od języka).

Konstrukcja jest inna, w zależności od tego, czy używasz wpisów tajnych klienta lub certyfikatów (lub, jako zaawansowany scenariusz, podpisane potwierdzenia).

#### <a name="reference-the-package"></a>Odwołanie się do pakietu

Odwołaj się do pakietu MSAL w kodzie aplikacji.

# <a name="net"></a>[.NET](#tab/dotnet)

Dodaj pakiet [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet do aplikacji.
W MSAL.NET poufne aplikacji klienckiej jest reprezentowana przez `IConfidentialClientApplication` interfejs.
Użyj MSAL.NET obszaru nazw w kodzie źródłowym.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej z kluczem tajnym klienta

Oto kod do tworzenia wystąpienia poufnej aplikacji klienckiej z kluczem tajnym klienta:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej z certyfikatem klienta

Oto kod do tworzenia aplikacji z certyfikatem:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="java"></a>[Java](#tab/java)

W msal Java, istnieją dwa konstruktorów do tworzenia wystąpienia poufnej aplikacji klienckiej z certyfikatami:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

lub

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Scenariusz zaawansowany: tworzenie wystąpienia poufnej aplikacji klienckiej z potwierdzeniami klienta

# <a name="net"></a>[.NET](#tab/dotnet)

Zamiast klucza tajnego klienta lub certyfikatu poufnej aplikacji klienckiej można również udowodnić swoją tożsamość przy użyciu potwierdzeń klienta.

MSAL.NET ma dwie metody dostarczania podpisanych potwierdzeń do poufnej aplikacji klienta:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Podczas korzystania `WithClientAssertion`z programu należy podać podpisany JWT. Ten zaawansowany scenariusz jest szczegółowo opisany w [potwierdzeniach klienta](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Podczas korzystania `WithClientClaims`z programu MSAL.NET spowoduje powstanie podpisanego potwierdzenia, które zawiera oświadczenia oczekiwane przez usługę Azure AD oraz dodatkowe oświadczenia klientów, które chcesz wysłać.
Ten kod pokazuje, jak to zrobić:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Ponownie, aby uzyskać szczegółowe informacje, zobacz [potwierdzenia klienta](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

W języku MSAL Python można dostarczyć oświadczeń klienta przy użyciu `ConfidentialClientApplication`oświadczeń, które zostaną podpisane przez ten klucz prywatny.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Aby uzyskać szczegółowe informacje, zobacz dokumentację referencyjną języka MSAL Python dla [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

---

## <a name="next-steps"></a>Następne kroki

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikacja Daemon - pobieranie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikacja Daemon - pobieranie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikacja Daemon - pobieranie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
