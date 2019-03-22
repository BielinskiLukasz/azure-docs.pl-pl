---
title: Przykłady kodu usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dostarcza indeks dostępne usługi Azure Active Directory (punkt końcowy w wersji 2), przykłady kodu, uporządkowane według scenariusza.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b518235efa4cd236afd8f79cb70cf30696931fdb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086772"
---
# <a name="azure-active-directory-code-samples-v20-endpoint"></a>Przykłady kodu usługi Azure Active Directory (punktu końcowego v2.0)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Możesz użyć platformy tożsamości firmy Microsoft, aby:

- Dodawanie uwierzytelniania i autoryzacji do aplikacji sieci web i interfejsy API sieci web.
- Wymagaj tokenu dostępu do uzyskania dostępu do chronionego internetowego interfejsu API.

W tym artykule krótko opisano oraz zawiera łącza do przykładów dla punktu końcowego v2.0 usługi Azure AD. Te przykłady pokazują, jak to się robi, wraz z fragmentów kodu, których można używać w aplikacjach. Na stronie przykładowy kod znajdziesz szczegółowe readme tematów, w których pomocy z wymaganiami, instalacji i konfigurowania. Komentarze w kodzie, istnieją ułatwiające zrozumienie sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię wersja 1.0 przykłady, zobacz [przykładów kodu usługi Azure AD (punkt końcowy w wersji 1.0)](sample-v1-code.md).

Aby poznać podstawowy scenariusz dla każdego typu przykładowe, zobacz [typy aplikacji dla punktu końcowego v2.0 usługi Azure Active Directory](v2-app-types.md).

Ponadto można przekazywać przykłady w witrynie GitHub. Aby dowiedzieć się więcej, zobacz temat [Microsoft Azure Active Directory, przykłady i dokumentację](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications-spa"></a>Aplikacji jednostronicowej (SPA)

Te przykłady przedstawiają sposób pisania aplikacji jednostronicowej, zabezpieczony za pomocą usługi Azure AD. Te przykłady, użyj jednej z odmian MSAL.js:

* [Microsoft Authentication Library dla języka JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)
* [Biblioteka Microsoft Authentication Library dla platformy Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)
* [Biblioteka Microsoft Authentication Library dla AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)

  Platforma |  Wywołania programu Microsoft Graph
  -------- |  ---------------------
  ![JavaScript](media/sample-v2-code/logo_js.png) JavaScript (msal.js)  | [javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2)
  ![Angular JS](media/sample-v2-code/logo_angular.png) JavaScript (MSAL AngularJS) | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
  ![Angular](media/sample-v2-code/logo_angular.png) JavaScript (MSAL Angular) | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp)

## <a name="web-applications"></a>Aplikacje internetowe

Poniższe przykłady ilustrują, aplikacje sieci web, które logują się użytkownicy. Niektóre przykłady pokazują również aplikacji wywołanie programu Microsoft Graph lub własnego interfejsu API sieci web przy użyciu tożsamości użytkownika.

 Platforma | Użytkownikom zalogowanie się tylko | Użytkownikom zalogowanie się i wymaga programu Microsoft Graph
 -------- | ------------------- | ---------------------------------
![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.1 | [Samouczek logowania użytkowników podstawowych aplikacji sieci Web platformy ASP.NET](https://aka.ms/aspnetcore-webapp-sign-in) | Przykładowe takie same w [aplikację sieci Web platformy ASP.NET Core wymaga programu Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fazy
![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Przewodnik Szybki Start platformy ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p>[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
![Node.js](media/sample-v2-code/logo_nodejs.png)  |                   | [Przewodnik Szybki Start platformy node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)
![Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp)

## <a name="desktop-and-mobile-public-client-apps"></a>Aplikacje komputerowe i mobilne publicznych klienta

Poniższe przykłady pokazują publicznych klienta aplikacji (aplikacji desktop/mobile), uzyskujących dostęp do interfejsu API programu Microsoft Graph lub własnego interfejsu API sieci Web, nazwę użytkownika. Wszystkie te aplikacje klienckie korzystają bibliotek uwierzytelniania firmy Microsoft (MSAL).

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph | Wywołania internetowego interfejsu API platformy ASP.NET Core 2.0
------------------ | -------- |  ----------| ---------- | -------------------------
Pulpitu (WPF)      | ![.NET/C#](media/sample-v2-code/logo_NET.png) | Interaktywne | [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://github.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Pulpitu (Konsola)   | ![.NET / C# (wersja klasyczna)](media/sample-v2-code/logo_NET.png) | Zintegrowane uwierzytelnianie systemu Windows |[dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2)
Pulpitu (Konsola)   | ![.NET / C# (wersja klasyczna)](media/sample-v2-code/logo_NETcore.png) | Nazwa użytkownika/hasło |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)
Mobile (UWP)   | ![.NET/C# (UWP)](media/sample-v2-code/logo_windows.png) | Interaktywne |[dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobile (Android, iOS, UWP)   | ![.NET / C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktywne |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | ![dla systemu iOS / Objective C lub swift.](media/sample-v2-code/logo_iOS.png) | Interaktywne |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobile (Android)   | ![Android / Java](media/sample-v2-code/logo_Android.png) | Interaktywne |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="daemon-applications"></a>Aplikacje demona

Poniższe przykłady pokazują aplikację, która uzyskuje dostęp do interfejsu API programu Microsoft Graph przy użyciu własnej tożsamości (nie użytkownika).

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph
------------------ | -------- | ---------- | --------------------
Konsola | ![.NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | Poświadczenia klienta | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)
Aplikacja internetowa | ![ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | Poświadczenia klienta | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2)

## <a name="headless-applications"></a>Bezobsługowe aplikacji

Poniższy przykład pokazuje aplikacji publicznych klienta, na urządzeniu bez przeglądarki sieci web. Aplikacja może być narzędziem wiersza polecenia lub w systemie Linux lub Mac lub aplikacji IoT. Przykład funkcji aplikacji dostęp do interfejsu API programu Microsoft Graph, nazwę użytkownika, który logowania interakcyjnego na innym urządzeniu (np. telefonu komórkowego). Ta aplikacja kliencka używa bibliotek uwierzytelniania firmy MicroSoft (MSAL).

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołania programu Microsoft Graph
------------------ | -------- |  ----------| ----------
Pulpitu (Konsola)   | ![.NET / C# (wersja klasyczna)](media/sample-v2-code/logo_NETcore.png) | Przepływ kodu urządzenia |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)

## <a name="web-apis"></a>Interfejsy API sieci Web

Poniższy przykład pokazuje, jak chronić internetowy interfejs API z punktem końcowym v2.0 usługi Azure AD. Ten interfejs API jest wykonywane przez aplikację WPF, ale może ona zostać wywołana przez dowolną aplikację.

Platforma | Sample
 -------- | -------------------
![.NET/C#](media/sample-v2-code/logo_NET.png) | WebAPI (usługa) z [dotnet-native-aspnetcore-v2](https://github.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)

## <a name="other-microsoft-graph-samples"></a>Inne przykłady z programu Microsoft Graph

Aby dowiedzieć się więcej na temat [przykłady](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) i samouczków, które przedstawiają różnych wzorców użycia dla interfejsu API programu Graph firmy Microsoft, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [przykłady przygotowane przez społeczność programu Microsoft Graph i samouczki](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz także

[Przewodnik dewelopera usługi Azure Active Directory](v1-overview.md)

[Funkcji Azure AD Graph pojęć związanych z interfejsu API i dokumentacja](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteka pomocnicza interfejsu API programu Graph usługi Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
