---
title: Tworzenie aplikacji klasycznej, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web (omówienie)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23085e486972ef6a10b3bd2ee86ae1bb1dc3006
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293321"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenariusz: aplikacja klasyczna, która wywołuje interfejsy API sieci Web

Dowiedz się wszystkiego, co jest potrzebne do utworzenia aplikacji klasycznej, która wywołuje interfejsy API sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Rozpocznij

Jeśli jeszcze tego nie zrobiono, Utwórz swoją pierwszą aplikację, postępując zgodnie z przewodnikiem Szybki Start dla programu .NET, platforma uniwersalna systemu Windows (platformy UWP) — szybki start lub natywną aplikację macOS do przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji klasycznej systemu Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji platformy UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z natywnej aplikacji macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Przegląd

Napisz aplikację klasyczną i chcesz zalogować użytkowników do aplikacji i wywoływać interfejsy API sieci Web, takie jak Microsoft Graph, inne interfejsy API firmy Microsoft lub własny internetowy interfejs API. Masz kilka możliwości:

- Możesz użyć pozyskiwania tokenów interaktywnych:

  - Jeśli aplikacja klasyczna obsługuje kontrolki graficzne, na przykład jeśli jest to aplikacja Windows. form, aplikacja WPF lub aplikacja natywna macOS.
  - Lub, jeśli jest to aplikacja .NET Core i wyrażasz zgodę na interakcję uwierzytelniania z usługą Azure Active Directory (Azure AD) w przeglądarce systemowej.

- W przypadku aplikacji hostowanych w systemie Windows jest również możliwe, aby aplikacje działające na komputerach przyłączonych do domeny systemu Windows lub z usługą Azure AD przyłączone w trybie dyskretnym przy użyciu zintegrowanego uwierzytelniania systemu Windows.
- Na koniec, chociaż nie jest to zalecane, możesz użyć nazwy użytkownika i hasła w publicznych aplikacjach klienckich. Nadal jest to konieczne w niektórych scenariuszach, takich jak DevOps. Użycie nakłada ograniczenia dotyczące aplikacji. Na przykład nie może zalogować użytkownika, który musi wykonać uwierzytelnianie wieloskładnikowe (dostęp warunkowy). Ponadto aplikacja nie będzie korzystać z logowania jednokrotnego (SSO).

  Jest to również sprzeczne z zasadami nowoczesnego uwierzytelniania i są dostępne tylko dla starszych przyczyn.

  ![Aplikacja klasyczna](media/scenarios/desktop-app.svg)

- Jeśli nastąpi zapisanie przenośnego narzędzia wiersza polecenia, prawdopodobnie aplikacji .NET Core działającej w systemie Linux lub Mac, a po zaakceptowaniu tego uwierzytelniania zostanie przekazane do przeglądarki systemowej, można użyć uwierzytelniania interakcyjnego. Platforma .NET Core nie udostępnia [przeglądarki sieci Web](https://aka.ms/msal-net-uses-web-browser), więc uwierzytelnianie odbywa się w przeglądarce systemowej. W przeciwnym razie najlepszą opcją w tym przypadku jest użycie przepływu kodu urządzenia. Ten przepływ jest również używany w przypadku aplikacji bez przeglądarki, takich jak aplikacje IoT.

  ![Aplikacja bezprzeglądarki](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Szczegółowych informacji

Aplikacje klasyczne mają pewne różne wartości. Są one zależne od tego, czy aplikacja korzysta z uwierzytelniania interaktywnego, czy nie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja klasyczna: Rejestracja aplikacji](scenario-desktop-app-registration.md)
