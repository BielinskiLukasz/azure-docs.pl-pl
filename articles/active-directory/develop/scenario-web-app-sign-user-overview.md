---
title: Logowanie użytkowników z aplikacji sieci Web — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (omówienie)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 403f589702fd7142f0515a3b6f19ee1b9bbb6420
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701556"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenariusz: aplikacja sieci Web, która loguje użytkowników

Dowiedz się, co musisz zrobić, aby utworzyć aplikację sieci Web korzystającą z platformy tożsamości firmy Microsoft do logowania użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Jeśli chcesz utworzyć swoją pierwszą aplikację sieci Web przenośną (ASP.NET Core), która loguje użytkowników, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Szybki Start: ASP.NET Core aplikacji sieci Web, która loguje użytkowników](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Jeśli chcesz zrozumieć, jak dodać logowanie do istniejącej aplikacji sieci Web ASP.NET, spróbuj wykonać następujące czynności:

> [!div class="nextstepaction"]
> [Szybki Start: ASP.NET Web App, który loguje użytkowników](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Jeśli jesteś deweloperem języka Java, spróbuj wykonać następujące czynności:

> [!div class="nextstepaction"]
> [Szybki Start: Dodawanie logowania do aplikacji sieci Web w języku Java przez firmę Microsoft](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Jeśli tworzysz przy użyciu języka Python, wypróbuj następujący Przewodnik Szybki Start:

> [!div class="nextstepaction"]
> [Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web w języku Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Przegląd

Należy dodać uwierzytelnianie do aplikacji sieci Web, aby umożliwić użytkownikom logowanie się. Dodanie uwierzytelniania umożliwia aplikacji sieci Web dostęp do ograniczonych informacji o profilach w celu dostosowania środowiska dla użytkowników. 

Aplikacje sieci Web uwierzytelniają użytkownika w przeglądarce internetowej. W tym scenariuszu aplikacja sieci Web kieruje przeglądarkę użytkownika do podpisania ich w usłudze Azure Active Directory (Azure AD). Usługa Azure AD zwraca odpowiedź logowania za pomocą przeglądarki użytkownika, która zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Podpisywanie użytkowników korzysta z protokołu [Open ID Connect](./v2-protocols-oidc.md) Standard, uproszczonego przy użyciu [bibliotek](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)pośredniczących.

![Znaki aplikacji sieci Web w użytkownikach](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

W drugiej fazie można umożliwić aplikacji wywoływanie interfejsów API sieci Web w imieniu zalogowanego użytkownika. Ta kolejna faza jest innym scenariuszem, który można znaleźć w [aplikacji sieci Web, która wywołuje interfejsy API sieci Web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Dodawanie logowania do aplikacji sieci Web polega na tym, jak chronić aplikację sieci Web i sprawdzać token użytkownika, który jest biblioteką **oprogramowania pośredniczącego** . W przypadku platformy .NET ten scenariusz nie wymaga jeszcze biblioteki uwierzytelniania firmy Microsoft (MSAL), która ma na celu uzyskanie tokenu do wywołania chronionych interfejsów API. Biblioteki uwierzytelniania zostaną wprowadzone w scenariuszu kontynuacji, gdy aplikacja sieci Web wymaga wywołania interfejsów API sieci Web.

## <a name="specifics"></a>Szczegółowych informacji

- Podczas rejestracji aplikacji należy podać jeden lub kilka (Jeśli aplikacja jest wdrażana w kilku lokalizacjach) identyfikatorów URI odpowiedzi. W niektórych przypadkach (ASP.NET i ASP.NET Core) należy włączyć token identyfikatora. Na koniec należy skonfigurować identyfikator URI, aby aplikacja działała w celu wylogowania użytkowników.
- W kodzie aplikacji należy podać uprawnienia do logowania pełnomocników aplikacji sieci Web. Możesz chcieć dostosować sprawdzanie poprawności tokenów (w szczególności w scenariuszach partnerskich).
- Aplikacje sieci Web obsługują wszystkie typy kont. Aby uzyskać więcej informacji, zobacz [obsługiwane typy kont](v2-supported-account-types.md).

## <a name="next-steps"></a>Następne kroki

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
