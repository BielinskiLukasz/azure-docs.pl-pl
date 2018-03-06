---
title: "Testowanie aplikacji klasycznej z obsługą usługi Azure AD B2C"
description: "Użyj przewodnika Szybki start, aby wypróbować przykładową aplikację klasyczną platformy ASP.NET korzystającą z usługi Azure Active Directory B2C do udostępniania funkcji logowania użytkownika."
services: active-directory-b2c
author: PatAltimore
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 2/13/2018
ms.author: patricka
ms.openlocfilehash: 18c378f82255df3a999703bc319d551af4b2705c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="quickstart-test-drive-an-azure-ad-b2c-enabled-desktop-app"></a>Szybki start: testowanie aplikacji klasycznej z obsługą usługi Azure AD B2C

Usługa Azure Active Directory (Azure AD) B2C umożliwia zarządzanie tożsamościami w chmurze, chroniąc Twoją aplikację, firmę i klientów. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie względem kont społecznościowych i firmowych za pomocą protokołów zgodnych z otwartymi standardami.

W tym przewodniku Szybki start przykładowa aplikacja klasyczna platformy Windows Presentation Foundation (WPF) z obsługą usługi Azure AD B2C jest używana do logowania za pomocą dostawcy tożsamości społecznościowych i wywoływania chronionego internetowego interfejsu API usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z pakietem roboczym **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych**. 
* Konto w sieci społecznościowej: Facebook, Google, Microsoft lub Twitter.

## <a name="download-the-sample"></a>Pobierz przykład

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

## <a name="run-the-app-in-visual-studio"></a>Uruchamianie aplikacji w programie Visual Studio

W folderze projektu przykładowej aplikacji otwórz rozwiązanie `active-directory-b2c-wpf.sln` w programie Visual Studio.

Naciśnij klawisz **F5**, aby debugować aplikację.

## <a name="create-an-account"></a>Tworzenie konta

Kliknij pozycję **Zaloguj**, aby uruchomić przepływ pracy **Rejestrowanie lub logowanie** oparty na zasadach usługi Azure AD B2C.

![Przykładowa aplikacja](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

Przykład obsługuje kilka opcji rejestracji, w tym przy użyciu dostawcy tożsamości dla sieci społecznościowej, lub tworzenia konta lokalnego przy użyciu adresu e-mail. Dla tego przewodnika Szybki start należy użyć konta dostawcy tożsamości dla sieci społecznościowej Facebook, Google, Microsoft lub Twitter. 

### <a name="sign-up-using-a-social-identity-provider"></a>Rejestrowanie się przy użyciu dostawcy tożsamości dla sieci społecznościowej

Dla przykładowej aplikacji internetowej usługa Azure AD B2C wyświetla niestandardową stronę logowania fikcyjnej marki Wingtip Toys. 

1. Aby zarejestrować się przy użyciu dostawcy tożsamości dla sieci społecznościowej, kliknij przycisk dostawcy tożsamości, którego chcesz użyć. 

    ![Dostawca logowania lub rejestracji](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    Uwierzytelniasz się (logujesz) za pomocą poświadczeń konta w sieci społecznościowej i autoryzujesz aplikację do odczytywania informacji z Twojego konta w sieci społecznościowej. Po udzieleniu dostępu aplikacji może ona pobrać informacje z profilu na koncie w sieci społecznościowej, takie jak Twoje nazwisko i miasto. 

2. Zakończ proces logowania dla dostawcy tożsamości. Na przykład jeśli wybranym dostawcą jest Twitter, wprowadź swoje poświadczenia usługi Twitter i kliknij przycisk **Zaloguj się**.

    ![Uwierzytelnianie i autoryzacja przy użyciu konta w sieci społecznościowej](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

    Szczegóły profilu na nowym koncie są wstępnie wypełnione informacjami z konta w sieci społecznościowej. 

3. Zmodyfikuj szczegóły, jeśli chcesz, i kliknij pozycję **Kontynuuj**. Wprowadzone przez Ciebie wartości są używane w profilu na koncie użytkownika w usłudze Azure AD B2C.

    ![Szczegóły profilu rejestracji nowego konta](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

    Pomyślnie utworzono nowe konto użytkownika usługi Azure AD B2C, które używa dostawcy tożsamości. Po zalogowaniu token dostępu zostanie wyświetlony w polu tekstowym *Informacje o tokenie*. Token dostępu jest używany podczas uzyskiwania dostępu do zasobu interfejsu API.

## <a name="edit-your-profile"></a>Edycja profilu

Usługa Azure Active Directory B2C zawiera funkcję umożliwiającą użytkownikom aktualizowanie profilów.  Przykładowa aplikacja internetowa używa zasad profilu edycji usługi Azure AD B2C dla przepływu pracy. 

1. Kliknij pozycję **Edytuj profil**, aby edytować utworzony profil.

    ![Edytowanie profilu](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Wybierz dostawcę tożsamości skojarzonego z utworzonym kontem. Jeśli na przykład użyto usługi Twitter jako dostawcy tożsamości podczas tworzenia konta, wybierz usługę Twitter, aby zmodyfikować szczegóły skojarzonego profilu.

3. Zmień wartość **Nazwa wyświetlana** lub **Miasto** i kliknij pozycję **Kontynuuj**.

    Nowy token dostępu zostanie wyświetlony w polu tekstowym *Informacje o tokenie*. Jeśli chcesz zweryfikować zmiany profilu, skopiuj i wklej token dostępu do dekodera tokenów na stronie https://jwt.ms.

## <a name="access-a-protected-web-api-resource"></a>Uzyskiwanie dostępu do zasobu chronionego internetowego interfejsu API

Kliknij pozycję **Wywołaj interfejs API**, aby wysłać żądanie do chronionego zasobu usługi Azure AD B2C — https://fabrikamb2chello.azurewebsites.net/hello. 

![Wywoływanie interfejsu API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

Aplikacja dołącza token dostępu usługi Azure AD do żądania wysłanego do chronionego zasobu internetowego interfejsu API. Internetowy interfejs API odsyła z powrotem nazwę wyświetlaną zawartą w tokenie dostępu.

Pomyślnie użyto konta użytkownika usługi Azure AD B2C do wykonania autoryzowanego wywołania chronionego internetowego interfejsu API usługi Azure AD B2C.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne przewodniki Szybki start lub samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest utworzenie własnej dzierżawy usługi Azure AD B2C i skonfigurowanie przykładu pod kątem uruchamiania przy użyciu dzierżawy. 

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy usługi Azure Active Directory B2C w witrynie Azure Portal](active-directory-b2c-get-started.md)
