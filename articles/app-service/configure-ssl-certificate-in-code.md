---
title: Używanie certyfikatu TLS/SSL w kodzie
description: Dowiedz się, jak używać certyfikatów klienta w kodzie. Uwierzytelnianie za pomocą zasobów zdalnych przy użyciu certyfikatu klienta lub wykonywanie do nich zadań kryptograficznych.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80811697"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Użyj certyfikatu TLS/SSL w kodzie w Azure App Service

W kodzie aplikacji można uzyskać dostęp do [publicznych lub prywatnych certyfikatów, które są dodawane do App Service](configure-ssl-certificate.md). Kod aplikacji może pełnić rolę klienta i uzyskać dostęp do usługi zewnętrznej wymagającej uwierzytelniania przy użyciu certyfikatu lub może być konieczne wykonanie zadań kryptograficznych. W tym przewodniku opisano sposób używania certyfikatów publicznych lub prywatnych w kodzie aplikacji.

Takie podejście do używania certyfikatów w kodzie korzysta z funkcji protokołu TLS w App Service, co wymaga, aby aplikacja była w warstwie **podstawowa** lub wyższa. Jeśli Twoja aplikacja znajduje się w warstwie **bezpłatna** lub **współdzielona** , możesz [dołączyć plik certyfikatu do repozytorium aplikacji](#load-certificate-from-file).

Gdy zezwolisz App Service na zarządzanie certyfikatami TLS/SSL, możesz osobno zarządzać certyfikatami i kodem aplikacji oraz chronić poufne dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

- [Utwórz aplikację usługi App Service](/azure/app-service/)
- [Dodawanie certyfikatu do aplikacji](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Znajdowanie odcisku palca

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>z menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL**, a następnie wybierz pozycję **certyfikaty kluczy prywatnych (pfx)** lub **Certyfikaty klucza publicznego (CER)**.

Znajdź certyfikat, którego chcesz użyć, i Skopiuj odcisk palca.

![Skopiuj odcisk palca certyfikatu](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Udostępnianie certyfikatu

Aby uzyskać dostęp do certyfikatu w kodzie aplikacji, Dodaj jego odcisk palca do `WEBSITE_LOAD_CERTIFICATES` Ustawienia aplikacji, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Aby udostępnić wszystkie certyfikaty, ustaw wartość na `*` .

## <a name="load-certificate-in-windows-apps"></a>Ładowanie certyfikatu w aplikacjach systemu Windows

`WEBSITE_LOAD_CERTIFICATES`Ustawienie aplikacji sprawia, że określone certyfikaty są dostępne dla aplikacji hostowanej w systemie Windows w magazynie certyfikatów systemu Windows, a lokalizacja zależy od [warstwy cenowej](overview-hosting-plans.md):

- Warstwa **izolowana** — [Machine\My lokalny](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Wszystkie inne warstwy — w [bieżącym User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

W kodzie C# można uzyskać dostęp do certyfikatu za pomocą odcisku palca certyfikatu. Poniższy kod ładuje certyfikat z odciskiem palca `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

W kodzie Java można uzyskać dostęp do certyfikatu z magazynu "Windows-MY" przy użyciu pola Nazwa pospolita podmiotu (zobacz [certyfikat klucza publicznego](https://en.wikipedia.org/wiki/Public_key_certificate)). Poniższy kod przedstawia sposób ładowania certyfikatu klucza prywatnego:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

W przypadku języków, które nie obsługują programu lub nie oferują wystarczającej obsługi dla magazynu certyfikatów systemu Windows, zobacz [ładowanie certyfikatu z pliku](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Ładowanie certyfikatu w aplikacjach systemu Linux

`WEBSITE_LOAD_CERTIFICATES`Ustawienia aplikacji umożliwiają dostęp do określonych certyfikatów dla aplikacji hostowanych w systemie Linux (w tym niestandardowych aplikacji kontenerów) jako plików. Pliki znajdują się w następujących katalogach:

- Certyfikaty prywatne — `/var/ssl/private` ( `.p12` pliki)
- Certyfikaty publiczne — `/var/ssl/certs` ( `.der` pliki)

Nazwa pliku certyfikatu jest odciskiem palca certyfikatu. Poniższy kod w języku C# przedstawia sposób ładowania certyfikatu publicznego w aplikacji systemu Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Aby dowiedzieć się, jak załadować certyfikat TLS/SSL z pliku w Node.js, PHP, Python, Java lub Ruby, zapoznaj się z dokumentacją odpowiedniego języka lub platformy sieci Web.

## <a name="load-certificate-from-file"></a>Załaduj certyfikat z pliku

Jeśli zachodzi potrzeba załadowania pliku certyfikatu, który zostanie przekazany ręcznie, lepiej jest przekazać certyfikat przy użyciu [FTPS](deploy-ftp.md) zamiast [git](deploy-local-git.md), na przykład. Należy zachować poufne dane, takie jak certyfikat prywatny, z kontroli źródła.

> [!NOTE]
> ASP.NET i ASP.NET Core w systemie Windows muszą uzyskać dostęp do magazynu certyfikatów nawet w przypadku ładowania certyfikatu z pliku. Aby załadować plik certyfikatu w aplikacji systemu Windows .NET, Załaduj bieżący profil użytkownika przy użyciu następującego polecenia w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Takie podejście do używania certyfikatów w kodzie korzysta z funkcji protokołu TLS w App Service, co wymaga, aby aplikacja była w warstwie **podstawowa** lub wyższa.

Poniższy przykład kodu w języku C# ładuje certyfikat publiczny ze ścieżki względnej w aplikacji:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Aby dowiedzieć się, jak załadować certyfikat TLS/SSL z pliku w Node.js, PHP, Python, Java lub Ruby, zapoznaj się z dokumentacją odpowiedniego języka lub platformy sieci Web.

## <a name="more-resources"></a>Dodatkowe zasoby

* [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md)
* [Wymuszanie protokołu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Wymuszanie protokołu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Często zadawane pytania: certyfikaty App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
