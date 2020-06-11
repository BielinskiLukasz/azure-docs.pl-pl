---
title: 'Samouczek: rejestrowanie aplikacji'
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zarejestrować aplikację sieci Web w Azure Active Directory B2C przy użyciu Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3be9136f260d73647e144d4d00bb58ce5e6774d
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84672616"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Samouczek: rejestrowanie aplikacji sieci Web w Azure Active Directory B2C

Aby [aplikacje](application-types.md) mogły korzystać z Azure Active Directory B2C (Azure AD B2C), należy je zarejestrować w dzierżawie, którą zarządzasz. W tym samouczku pokazano, jak zarejestrować aplikację sieci Web przy użyciu Azure Portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Tworzenie klucza tajnego klienta

Jeśli używasz aplikacji natywnej (np. iOS, Android, Mobile & Desktop), Dowiedz się, [jak zarejestrować natywną aplikację kliencką](add-native-application.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze własnej [dzierżawy Azure AD B2C](tutorial-create-tenant.md), utwórz ją teraz. Możesz użyć istniejącej dzierżawy Azure AD B2C.

## <a name="register-a-web-application"></a>Rejestrowanie aplikacji internetowej

Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *webapp1*.
1. W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym lub dowolnym dostawcy tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C**.
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Sieć Web**, a następnie wprowadź `https://jwt.ms` wartość w polu tekstowym adres URL.

    Identyfikator URI przekierowania jest punktem końcowym, do którego użytkownik jest wysyłany przez serwer autoryzacji (Azure AD B2C w tym przypadku) po zakończeniu interakcji z użytkownikiem oraz do którego zostanie wysłany token dostępu lub kod autoryzacji po pomyślnej autoryzacji. W aplikacji produkcyjnej zwykle jest dostępny publicznie punkt końcowy, w którym działa aplikacja, na przykład `https://contoso.com/auth-response` . Do celów testowych, takich jak ten samouczek, można ustawić dla niego `https://jwt.ms` aplikację sieci Web firmy Microsoft, która wyświetla zdekodowaną zawartość tokenu (zawartość tokenu nigdy nie opuszcza przeglądarki). Podczas tworzenia aplikacji możesz dodać punkt końcowy, w którym aplikacja nasłuchuje lokalnie, na przykład `https://localhost:5000` . Można w dowolnym momencie dodawać i modyfikować identyfikatory URI przekierowań w zarejestrowanych aplikacjach.

    Identyfikatory URI przekierowania dotyczą następujących ograniczeń:

    * Adres URL odpowiedzi musi rozpoczynać się od schematu `https` .
    * W adresie URL odpowiedzi jest rozróżniana wielkość liter. Jego wielkość liter musi być zgodna z wielkością liter w ścieżce URL działającej aplikacji. Na przykład jeśli aplikacja zawiera jako część swojej ścieżki `.../abc/response-oidc` , nie należy określać jej `.../ABC/response-oidc` w adresie URL odpowiedzi. Ponieważ przeglądarka sieci Web traktuje ścieżki w miarę uwzględniania wielkości liter, pliki cookie skojarzone z programem `.../abc/response-oidc` mogą zostać wykluczone w przypadku przekierowania do niezgodnego z wielkością liter `.../ABC/response-oidc` adresów URL.

1. W obszarze **uprawnienia**zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.

Po zakończeniu rejestracji aplikacji Włącz przepływ niejawnego przydzielenia:

1. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**.
1. W obszarze **niejawne przyznanie**zaznacz pola wyboru **tokeny dostępu** i **tokeny identyfikatora** .
1. Wybierz pozycję **Zapisz**.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **aplikacje (starsza wersja)**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *webapp1*.
1. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
1. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. Na przykład można ustawić, aby nasłuchiwać lokalnie w `https://localhost:44316` . Jeśli nie znasz jeszcze numeru portu, możesz wprowadzić wartość symbolu zastępczego i zmienić ją później.

    Do celów testowych, takich jak ten samouczek, można ustawić, aby `https://jwt.ms` wyświetlić zawartość tokenu do inspekcji. Na potrzeby tego samouczka Ustaw **adres URL odpowiedzi** na `https://jwt.ms` .

    Do adresów URL odpowiedzi są stosowane następujące ograniczenia:

    * Adres URL odpowiedzi musi rozpoczynać się od schematu `https` .
    * W adresie URL odpowiedzi jest rozróżniana wielkość liter. Jego wielkość liter musi być zgodna z wielkością liter w ścieżce URL działającej aplikacji. Na przykład jeśli aplikacja zawiera jako część swojej ścieżki `.../abc/response-oidc` , nie należy określać jej `.../ABC/response-oidc` w adresie URL odpowiedzi. Ponieważ przeglądarka sieci Web traktuje ścieżki w miarę uwzględniania wielkości liter, pliki cookie skojarzone z programem `.../abc/response-oidc` mogą zostać wykluczone w przypadku przekierowania do niezgodnego z wielkością liter `.../ABC/response-oidc` adresów URL.

1. Wybierz pozycję **Utwórz** , aby zakończyć rejestrację aplikacji.

* * *

## <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Jeśli aplikacja wymienia kod autoryzacji dla tokenu dostępu, należy utworzyć klucz tajny aplikacji.


#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Na stronie **Azure AD B2C rejestracje aplikacji** Wybierz utworzoną aplikację, na przykład *webapp1*.
1. W obszarze **Zarządzaj**wybierz pozycję **Certyfikaty & wpisy tajne**.
1. Wybierz pozycję **Nowy wpis tajny klienta**.
1. Wprowadź opis wpisu tajnego klienta w polu **Opis** . Na przykład *clientsecret1*.
1. W obszarze **wygaśnięcie**wybierz czas trwania, dla którego wpis tajny jest prawidłowy, a następnie wybierz pozycję **Dodaj**.
1. Zapisz **wartość**klucza tajnego. Ta wartość jest używana jako klucz tajny aplikacji w kodzie aplikacji.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Na stronie **Azure AD B2C — aplikacje** Wybierz utworzoną aplikację, na przykład *webapp1*.
1. Wybierz pozycję **klucze** , a następnie pozycję **Generuj klucz**.
1. Wybierz pozycję **Zapisz** , aby wyświetlić klucz. Zanotuj wartość pola **Klucz aplikacji**. Ta wartość jest używana jako klucz tajny aplikacji w kodzie aplikacji.

* * *

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie aplikacji internetowej
> * Tworzenie klucza tajnego klienta

Następnie Dowiedz się, jak tworzyć przepływy użytkowników, aby umożliwić użytkownikom rejestrowanie i logowanie się oraz zarządzanie profilami.

> [!div class="nextstepaction"]
> [Tworzenie przepływów użytkowników w Azure Active Directory B2C >](tutorial-create-user-flows.md)
