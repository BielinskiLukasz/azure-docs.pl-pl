---
title: Jak skonfigurować uwierzytelnianie serwisu Google dla aplikacji usługi App Services
description: Dowiedz się, jak skonfigurować uwierzytelnianie serwisu Google dla aplikacji usługi App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 35e02defb8b28c60c70c8b50c28bba8499392377
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961542"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Jak skonfigurować aplikację App Service do używania logowania usługi Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie przedstawiono sposób konfigurowania usługi Azure App Service, aby użyć Google jako dostawcy uwierzytelniania.

Aby ukończyć tę procedurę w tym temacie, musisz mieć konto Google ze zweryfikowanym adresem e-mail. Aby utworzyć nowe konto Google, przejdź do strony [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Zarejestrować aplikację w usłudze Google
1. Zaloguj się do [Azure Portal], a następnie przejdź do aplikacji. Kopiuj swoje **adresu URL**, której użyjesz później skonfigurować aplikację Google.
2. Przejdź do [interfejsy API Google](https://go.microsoft.com/fwlink/p/?LinkId=268303) witryny sieci Web, zaloguj się przy użyciu poświadczeń konta Google, kliknij przycisk **Tworzenie projektu**, podaj **Nazwa projektu**, następnie kliknij przycisk  **Utwórz**.
3. Po utworzeniu projektu, wybierz ją. Na pulpicie nawigacyjnym projektu kliknij **przejdź do przeglądu interfejsów API**.
4. Wybierz **Włącz interfejsy API i usług**. Wyszukaj **interfejsu API Google +**, a następnie wybierz ją. Następnie kliknij przycisk **Włącz**.
5. W obszarze nawigacji po lewej stronie **poświadczenia** > **ekran zgody OAuth**, a następnie wybierz swoje **adres E-mail**, wprowadź **nazwa produktu**i kliknij przycisk **Zapisz**.
6. W **poświadczenia** kliknij pozycję **Utwórz poświadczenia** > **identyfikator klienta OAuth**.
7. Na ekranie "Utwórz identyfikator klienta" Wybierz **aplikacji sieci Web**.
8. Wklej usługi App Service **adresu URL** zostanie wcześniej skopiowany do **autoryzowanych źródeł JavaScript**, Wklej Twojego przekierowania URI do **autoryzacji identyfikator URI przekierowania**. Przekierowania URI jest adres URL aplikacji dołączany ścieżki, */.auth/login/google/callback*. Na przykład `https://contoso.azurewebsites.net/.auth/login/google/callback`. Upewnij się, że będą używać schematu HTTPS. Następnie kliknij pozycję **Utwórz**.
9. Na następnym ekranie zanotuj wartości Identyfikatora klienta i klucz tajny klienta.

    > [!IMPORTANT]
    > Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń. Udostępnij ten wpis tajny osobom lub nie rozpowszechnić ją w aplikacji klienckiej.


## <a name="secrets"> </a>Dodaj informacje o Google do aplikacji
1. Ponownie [Azure Portal], przejdź do aplikacji. Kliknij przycisk **ustawienia**, a następnie **uwierzytelniania / autoryzacji**.
2. Jeśli uwierzytelnianie / Autoryzacja nie jest aktywna, ustaw przełącznik na **na**.
3. Kliknij przycisk **Google**. Wklej wartości Identyfikatora aplikacji i klucz tajny aplikacji, które uzyskany wcześniej, a opcjonalnie włączyć wszystkie zakresy, wymaganych przez aplikację. Następnie kliknij przycisk **OK**.
   
   ![][1]
   
   Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. W kodzie aplikacji musi autoryzować użytkowników.
4. (Opcjonalnie) Aby ograniczyć dostęp do witryny tylko użytkownicy uwierzytelnieni przez firmę Google, należy ustawić **akcji do wykonania w przypadku nieuwierzytelnionego żądania** do **Google**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do firmy Google do uwierzytelniania.
5. Kliknij pozycję **Zapisz**.

Teraz można przystąpić do użycia Google na potrzeby uwierzytelniania w aplikacji.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

