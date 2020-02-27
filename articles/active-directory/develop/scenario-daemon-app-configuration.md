---
title: Skonfiguruj aplikacje demona, które wywołują interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak skonfigurować kod dla aplikacji demona, która wywołuje interfejsy API sieci Web (Konfiguracja aplikacji)
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
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77611839"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji demona, która wywołuje interfejsy API sieci Web.

## <a name="msal-libraries-that-support-daemon-apps"></a>Biblioteki MSAL obsługujące aplikacje demona

Te biblioteki firmy Microsoft obsługują aplikacje demona:

  Biblioteka MSAL | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Platformy .NET Framework i .NET Core są obsługiwane na potrzeby tworzenia aplikacji demona. (Platformy UWP, Xamarin. iOS i Xamarin. Android nie są obsługiwane, ponieważ te platformy są używane do tworzenia publicznych aplikacji klienckich).
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Obsługa aplikacji demona w języku Python.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Obsługa aplikacji demona w języku Java.

## <a name="configure-the-authority"></a>Konfigurowanie urzędu

Aplikacje demona używają uprawnień aplikacji zamiast uprawnień delegowanych. Typ konta, który jest obsługiwany, nie może być kontem w żadnym katalogu organizacyjnym ani żadnym konto Microsoft osobistym (na przykład Skype, Xbox, Outlook.com). Brak administratora dzierżawy do udzielenia zgody na aplikację demona dla konta osobistego firmy Microsoft. Musisz wybrać *konta w mojej organizacji* lub *kontach w dowolnej organizacji*.

W związku z tym Urząd określony w konfiguracji aplikacji powinien być dzierżawcą (określając identyfikator dzierżawy lub nazwę domeny skojarzoną z Twoją organizacją).

Jeśli jesteś niezależnym dostawcą oprogramowania i chcesz udostępnić narzędzie wielodostępne, możesz użyć `organizations`. Należy jednak pamiętać, że należy również wyjaśnić swoim klientom sposób udzielania zgody administratora. Aby uzyskać szczegółowe informacje, zobacz [żądanie zgody dla całej dzierżawy](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). Ponadto obecnie obowiązuje ograniczenie MSAL: `organizations` jest dozwolony tylko wtedy, gdy poświadczenia klienta są wpisem tajnym aplikacji (nie certyfikatem).

## <a name="configure-and-instantiate-the-application"></a>Konfigurowanie i tworzenie wystąpienia aplikacji

W bibliotekach MSAL poświadczenia klienta (klucz tajny lub certyfikat) są przesyłane jako parametr konstrukcji poufnej aplikacji klienckiej.

> [!IMPORTANT]
> Nawet jeśli aplikacja jest aplikacją konsolową, która działa jako usługa, jeśli jest to aplikacja demona, musi być poufną aplikacją kliencką.

### <a name="configuration-file"></a>Plik konfiguracji

Plik konfiguracji definiuje:

- Urząd lub wystąpienie w chmurze i identyfikator dzierżawy.
- Identyfikator klienta, który został uzyskany z rejestracji aplikacji.
- Wpis tajny klienta lub certyfikat.

# <a name="net"></a>[.NET](#tab/dotnet)

plik [appSettings. JSON](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) z przykładu [demona konsoli .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) .

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Podaj `ClientSecret` lub `CertificateName`. Te ustawienia są wyłączność.

# <a name="python"></a>[Python](#tab/python)

W przypadku tworzenia poufnego klienta z kluczami tajnymi klienta plik config [. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) w przykładzie [demona języka Python](https://github.com/Azure-Samples/ms-identity-python-daemon) jest następujący:

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Podczas budowania klienta poufnego za pomocą certyfikatów, plik konfiguracyjny [Parameters. JSON](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) w przykładzie [demona języka Python](https://github.com/Azure-Samples/ms-identity-python-daemon) jest następujący:

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

Aby utworzyć wystąpienie aplikacji MSAL, należy dodać, odwołać lub zaimportować pakiet MSAL (w zależności od języka).

Konstrukcja różni się w zależności od tego, czy są używane klucze tajne klienta czy certyfikaty (lub, jako zaawansowany scenariusz, podpisane potwierdzenia).

#### <a name="reference-the-package"></a>Odwoływanie się do pakietu

Odwołuje się do pakietu MSAL w kodzie aplikacji.

# <a name="net"></a>[.NET](#tab/dotnet)

Dodaj pakiet NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) do swojej aplikacji.
W MSAL.NET, poufna aplikacja kliencka jest reprezentowana przez interfejs `IConfidentialClientApplication`.
Użyj przestrzeni nazw MSAL.NET w kodzie źródłowym.

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

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu klucza tajnego klienta

Oto kod służący do tworzenia wystąpienia poufnej aplikacji klienckiej przy użyciu klucza tajnego klienta:

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

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu certyfikatu klienta

Oto kod służący do kompilowania aplikacji z certyfikatem:

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

W MSAL Java istnieją dwóch konstruktorów, które umożliwiają utworzenie wystąpienia poufnej aplikacji klienckiej przy użyciu certyfikatów:

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

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Scenariusz zaawansowany: Tworzenie wystąpienia poufnej aplikacji klienckiej za pomocą potwierdzeń klienta

# <a name="net"></a>[.NET](#tab/dotnet)

Zamiast klucza tajnego klienta lub certyfikatu poufna aplikacja kliencka może również udowodnić swoją tożsamość za pomocą potwierdzeń klientów.

MSAL.NET ma dwie metody, aby zapewnić podpisane potwierdzenia do poufnej aplikacji klienckiej:

- `.WithClientAssertion()`
- `.WithClientClaims()`

W przypadku korzystania z `WithClientAssertion`należy zapewnić podpisany token JWT. Ten zaawansowany scenariusz jest szczegółowy w przypadku [potwierdzeń klientów](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Gdy używasz `WithClientClaims`, MSAL.NET będzie generował podpisane potwierdzenie zawierające oświadczenia oczekiwane przez usługę Azure AD oraz dodatkowe oświadczenia klienta, które chcesz wysłać.
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

Aby uzyskać szczegółowe informacje, zobacz [potwierdzenia klientów](msal-net-client-assertions.md).

# <a name="python"></a>[Python](#tab/python)

W MSAL Python można zapewnić oświadczenia klienta przy użyciu oświadczeń, które będą podpisane przez klucz prywatny tego `ConfidentialClientApplication`.

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

Aby uzyskać szczegółowe informacje, zobacz dokumentację MSAL języka Python dla [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

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
> [Aplikacja demona — uzyskiwanie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikacja demona — uzyskiwanie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikacja demona — uzyskiwanie tokenów dla aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
