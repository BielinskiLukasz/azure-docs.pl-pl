---
title: Przeglądarki sieci Web w bibliotece uwierzytelniania firmy Microsoft dla platformy .NET | Azure
description: Informacje o określonych kwestiach dotyczących korzystania z platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1285c5c61cee25e387ca5fb598f0e062088e549
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532505"
---
# <a name="using-web-browsers-in-msalnet"></a>Korzystanie z przeglądarek sieci Web w MSAL.NET
Do uwierzytelniania interakcyjnego są wymagane przeglądarki sieci Web. Domyślnie MSAL.NET obsługuje [systemową przeglądarkę sieci Web](#system-web-browser-on-xamarinios-xamarinandroid) w oprogramowaniu Xamarin. iOS i Xamarin. Android. [Możesz również włączyć osadzoną przeglądarkę internetową](#enable-embedded-webviews-on-ios-and-android) w zależności od wymagań (UX, co jest potrzebne do logowania jednokrotnego (SSO), zabezpieczeń) w aplikacjach [Xamarin. iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) i [Xamarin. Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) . Możesz nawet [dynamicznie wybrać](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) , która przeglądarka sieci Web ma być używana w oparciu o obecność przeglądarki Chrome lub w przeglądarce obsługującej niestandardowe karty programu Chrome w systemie Android. MSAL.NET obsługuje tylko przeglądarkę systemową w aplikacjach klasycznych platformy .NET Core.

## <a name="web-browsers-in-msalnet"></a>Przeglądarki sieci Web w MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interakcja odbywa się w przeglądarce internetowej

Ważne jest, aby zrozumieć, że w przypadku interakcyjnego pozyskiwania tokenu zawartość okna dialogowego nie jest udostępniana przez bibliotekę, ale przez usługi STS (Security Token Service). Punkt końcowy uwierzytelniania wysyła z powrotem kod HTML i kod JavaScript, który kontroluje interakcję, która jest renderowana w przeglądarce sieci Web lub kontrolce sieci Web. Umożliwienie usłudze STS obsłużenia interakcji HTML ma wiele zalet:

- Hasło (jeśli zostało wpisane) nigdy nie jest przechowywane przez aplikację ani w bibliotece uwierzytelniania.
- Umożliwia przekierowania innym dostawcom tożsamości (na przykład logowanie się przy użyciu konta szkoły służbowej lub konta osobistego przy użyciu usługi MSAL lub konta społecznościowego z Azure AD B2C).
- Umożliwia usłudze STS kontrolę dostępu warunkowego, na przykład przez przeprowadzenie uwierzytelniania wieloskładnikowego (MFA) w fazie uwierzytelniania (wprowadzenie numeru PIN usługi Windows Hello lub wywołanie na telefonie lub w aplikacji uwierzytelniania na telefonie). W przypadkach, gdy wymagane uwierzytelnianie wieloskładnikowe nie jest jeszcze skonfigurowane, użytkownik może je skonfigurować w tym samym czasie w tym samym oknie dialogowym.  Użytkownik wprowadza swój numer telefonu komórkowego, a następnie przeprowadza instalację aplikacji uwierzytelniania i skanuje tag QR, aby dodać swoje konto. Ta interakcja oparta na serwerze jest doskonałym doświadczeniem!
- Umożliwia użytkownikowi zmianę hasła w tym samym oknie dialogowym po wygaśnięciu hasła (podanie dodatkowych pól dla starego hasła i nowego hasła).
- Umożliwia znakowanie dzierżawy lub aplikacji (obrazów), które są kontrolowane przez administratora dzierżawy usługi Azure AD lub właściciela aplikacji.
- Umożliwia użytkownikom wyrażanie zgody na zezwolenie aplikacji na dostęp do zasobów/zakresów w ich imieniu po uwierzytelnieniu.

### <a name="embedded-vs-system-web-ui"></a>Osadzony interfejs użytkownika sieci Web systemu vs

MSAL.NET to biblioteka wieloplatformowa, która ma kod specyficzny dla platformy, który umożliwia hostowanie przeglądarki w kontrolce interfejsu użytkownika (na przykład w przypadku klasycznej platformy .NET używa WinForms, w programie Xamarin używa natywnych kontrolek mobilnych itp.). Ta kontrolka jest `embedded` nazywana interfejsem użytkownika sieci Web. Alternatywnie, MSAL.NET może również uruchamiać przeglądarki systemu operacyjnego.

Ogólnie rzecz biorąc, zaleca się korzystanie z ustawień domyślnych platformy i jest to zwykle przeglądarka systemu. Przeglądarka systemowa jest lepsza do zapamiętywania użytkowników, którzy zalogowali się wcześniej. Jeśli musisz zmienić to zachowanie, użyj`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Błyskawicznie

| Architektura        | Osadzić | System | Domyślny |
| ------------- |-------------| -----| ----- |
| Klasyczny .NET     | Tak | Tak ^ | Osadzić |
| .NET Core     | Nie | Tak ^ | System |
| .NET Standard | Nie | Tak ^ | System |
| Platforma UWP | Tak | Nie | Osadzić |
| Xamarin.Android | Tak | Tak  | System |
| Xamarin.iOS | Tak | Tak  | System |
| Xamarin.Mac| Tak | Nie | Osadzić |

^ Wymaga "http://localhost" URI przekierowania

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systemowa przeglądarka sieci Web na platformie Xamarin. iOS, Xamarin. Android

Domyślnie program MSAL.NET obsługuje przeglądarkę sieci Web w systemie Xamarin. iOS, Xamarin. Android i .NET Core. Dla wszystkich platform, które udostępniają interfejs użytkownika (to nie jest .NET Core), okno dialogowe jest udostępniane przez bibliotekę, która osadza formant przeglądarki sieci Web. MSAL.NET używa również osadzonego widoku sieci Web dla programu .NET Desktop i WAB dla platformy platformy UWP. Jednak wykorzystuje domyślnie **systemową przeglądarkę sieci Web** dla aplikacji Xamarin iOS i Xamarin dla systemu Android. W systemie iOS, nawet wybiera widok sieci Web, który ma być używany w zależności od wersji systemu operacyjnego (iOS12, iOS11 i wcześniejsze).

Korzystanie z przeglądarki systemowej ma znaczną zaletę udostępniania stanu logowania jednokrotnego z innymi aplikacjami i aplikacjami sieci Web bez potrzeby brokera (Portal firmy/wystawca uwierzytelnienia). Przeglądarka systemowa została użyta domyślnie w MSAL.NET dla platform Xamarin iOS i Xamarin Android, ponieważ na tych platformach przeglądarka sieci Web systemu zajmuje cały ekran i środowisko użytkownika jest lepiej. Widok sieci Web systemu nie jest odróżniany od okna dialogowego. W przypadku systemu iOS użytkownik może chcieć wyrazić zgodę na wyzwolenie aplikacji przez przeglądarkę, co może być irytujące.

## <a name="system-browser-experience-on-net-core"></a>Środowisko przeglądarki systemu w programie .NET Core

W programie .NET Core MSAL.NET uruchomi przeglądarkę systemową jako oddzielny proces. MSAL.NET nie ma kontroli nad tą przeglądarką, ale po zakończeniu uwierzytelniania przez użytkownika strona sieci Web zostanie przekierowana w taki sposób, aby MSAL.NET mógł przechwycić identyfikator URI.

Możesz również skonfigurować aplikacje przeznaczone dla platformy .NET Classic do korzystania z tej przeglądarki, określając

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET nie może wykryć, czy użytkownik nawiguje lub po prostu zamyka przeglądarkę. Aplikacje korzystające z tej techniki są zachęcane do definiowania limitu `CancellationToken`czasu (za pośrednictwem). Zalecamy przekroczenie limitu czasu co najmniej kilku minut, aby wziąć pod uwagę sytuacje, w których użytkownik zostanie poproszony o zmianę hasła lub przeprowadzenie uwierzytelniania wieloskładnikowego.

### <a name="how-to-use-the-default-os-browser"></a>Jak używać domyślnej przeglądarki systemu operacyjnego

MSAL.NET musi nasłuchiwać `http://localhost:port` i przechwycić kod, który wysyła do usługi AAD, gdy użytkownik wykonuje uwierzytelnianie (zobacz [kod autoryzacji](v2-oauth2-auth-code-flow.md) , aby uzyskać szczegółowe informacje).

Aby włączyć przeglądarkę systemową:

1. Podczas rejestracji aplikacji Skonfiguruj `http://localhost` jako identyfikator URI przekierowania (obecnie nie jest to obsługiwane przez B2C).
2. Podczas konstruowania PublicClientApplication Określ ten identyfikator URI przekierowania:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> W przypadku skonfigurowania `http://localhost`programu wewnętrznie MSAL.NET będzie znajdować losowy otwarty port i korzystać z niego.

### <a name="linux-and-mac"></a>Linux i MAC

W systemie Linux MSAL.NET otworzy domyślną przeglądarkę systemu operacyjnego za pomocą narzędzia xdg-Open. Aby rozwiązać problem, uruchom narzędzie z terminalu na przykład`xdg-open "https://www.bing.com"`  
Na komputerze Mac przeglądarka jest otwierana przez wywołanie`open <url>`

### <a name="customizing-the-experience"></a>Dostosowywanie środowiska

> [!NOTE]
> Dostosowanie jest dostępne w programie MSAL.NET 4.1.0 lub nowszym.

MSAL.NET jest w stanie odpowiedzieć z komunikatem HTTP po odebraniu tokenu lub w przypadku błędu. Możesz wyświetlić komunikat HTML lub przekierować do wybranego adresu URL:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Otwieranie określonej przeglądarki (wersja eksperymentalna)

Możesz dostosować sposób, w jaki MSAL.NET otwiera przeglądarkę. Na przykład zamiast używania dowolnej przeglądarki jest domyślną, można wymusić otwarcie określonej przeglądarki:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>PLATFORMY UWP nie używa systemu WebView

Jednak w przypadku aplikacji klasycznych uruchamianie systemu WebView prowadzi do środowiska użytkownika subpar, ponieważ użytkownik widzi przeglądarkę, w której mogą mieć już otwarte inne karty. Po zakończeniu uwierzytelniania użytkownicy pobierają stronę z prośbą o zamknięcie tego okna. Jeśli użytkownik nie zwróci uwagi, może zamknąć cały proces (w tym inne karty, które nie są związane z uwierzytelnianiem). Korzystanie z przeglądarki systemu na komputerze wymaga również otwarcia lokalnych portów i nasłuchiwania na nich, co może wymagać zaawansowanego dostępu do aplikacji. W przypadku deweloperów, użytkowników lub administratorów może być zniechęcić o tym wymaganiu.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Włączanie osadzonych widoków WebView w systemach iOS i Android

Możesz również włączyć osadzone widoki WebView w aplikacjach Xamarin. iOS i Xamarin. Android. Począwszy od MSAL.NET 2.0.0 — wersja zapoznawcza, MSAL.NET obsługuje również korzystanie z osadzonej opcji WebView. Dla ADAL.NET osadzony widok WebView jest jedyną obsługiwaną opcją.

Jako programista korzystający z platformy Xamarin, MSAL.NET jako element docelowy, możesz użyć osadzonych widoków WebViews lub przeglądarek systemu. Jest to wybór w zależności od środowiska użytkownika i kwestii związanych z bezpieczeństwem, które chcesz określić jako docelowe.

Obecnie MSAL.NET nie obsługuje jeszcze brokerów systemów Android i iOS. W związku z tym, jeśli musisz udostępnić Logowanie jednokrotne, przeglądarka systemowa może nadal być lepszym rozwiązaniem. Obsługa brokerów z osadzoną przeglądarką sieci Web znajduje się na liście prac MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Różnice między osadzonym widokiem WebView a przeglądarką systemu
Istnieją pewne różnice wizualne między osadzonym widokiem WebView a przeglądarką systemową w programie MSAL.NET.

**Logowanie interakcyjne przy użyciu usługi MSAL.NET z osadzonym widokiem WebView:**

![osadzić](media/msal-net-web-browsers/embedded-webview.png)

**Interakcyjne logowanie za pomocą usługi MSAL.NET przy użyciu przeglądarki systemowej:**

![Przeglądarka systemowa](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opcje dewelopera

Jako deweloper korzystający z usługi MSAL.NET masz kilka opcji wyświetlania interaktywnego okna dialogowego z usługi STS:

- **Przeglądarka systemu.** Przeglądarka systemowa jest domyślnie ustawiana w bibliotece. W przypadku korzystania z systemu Android Przeczytaj [przeglądarki systemowe](msal-net-system-browser-android-considerations.md) , aby uzyskać szczegółowe informacje na temat tego, które przeglądarki są obsługiwane na potrzeby uwierzytelniania. W przypadku korzystania z przeglądarki systemowej w systemie Android zaleca się, aby urządzenie miało przeglądarkę, która obsługuje niestandardowe karty programu Chrome.  W przeciwnym razie uwierzytelnianie może zakończyć się niepowodzeniem.
- **Osadzony widok WebView.** Aby użyć tylko osadzonego widoku WebView w MSAL.NET `AcquireTokenInteractively` , Konstruktor parametrów `WithUseEmbeddedWebView()` zawiera metodę.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Wybór między osadzoną przeglądarką sieci Web lub przeglądarką systemową na platformie Xamarin. iOS

W aplikacji `ParentWindow` dla systemu iOS można `AppDelegate.cs` zainicjować w usłudze. `null` Nie jest on używany w systemie iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Wybieranie między osadzoną przeglądarką sieci Web lub przeglądarką systemową na platformie Xamarin. Android

W aplikacji `MainActivity.cs` systemu Android można ustawić działanie nadrzędne, tak aby wynik uwierzytelniania został przywrócony do:

```csharp
 App.ParentWindow = this;
```

Następnie w `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Wykrywanie obecności kart niestandardowych w aplikacji Xamarin. Android

Jeśli chcesz użyć przeglądarki sieci Web systemu, aby włączyć logowanie jednokrotne w aplikacjach działających w przeglądarce, ale martwisz się się o środowisko użytkownika dla urządzeń z systemem Android bez przeglądarki z obsługą kart niestandardowych, możesz zdecydować, wywołując `IsSystemWebViewAvailable()` metodę w < c 2 > `IPublicClientApplication` . Ta metoda zwraca `true` Jeśli pakiet packagemanager wykryje niestandardowe karty `false` i nie wykryje ich na urządzeniu.

W oparciu o wartość zwróconą przez tę metodę i wymagania można podjąć decyzję:

- Do użytkownika można zwrócić niestandardowy komunikat o błędzie. Przykład: "Zainstaluj program Chrome, aby kontynuować uwierzytelnianie"-lub-
- Można wrócić do osadzonej opcji WebView i uruchomić interfejs użytkownika jako osadzony widok WebView.

Poniższy kod pokazuje osadzoną opcję widoku WebView:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>Platforma .NET Core nie obsługuje interakcyjnego uwierzytelniania przy użyciu osadzonej przeglądarki

W przypadku platformy .NET Core uzyskiwanie tokenów w trybie interaktywnym jest dostępne tylko za pomocą przeglądarki sieci Web systemu, a nie z osadzonymi widokami sieci Web. W rzeczywistości platforma .NET Core nie udostępnia jeszcze interfejsu użytkownika.
Aby dostosować środowisko przeglądania przy użyciu przeglądarki sieci Web systemu, można zaimplementować interfejs [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) , a nawet udostępnić własną przeglądarkę.
