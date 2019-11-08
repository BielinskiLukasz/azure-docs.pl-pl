---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 0b5c5fdddeea961858a2c3b8cd69c365bb28f26e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800009"
---
Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć bieżącego środowiska **aplikacji** lub naszego nowego interfejsu Unified **rejestracje aplikacji (wersja zapoznawcza)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *nativeapp1*.
1. W przypadku **klienta natywnego**wybierz pozycję **tak**.
1. Wprowadź **niestandardowy identyfikator URI przekierowania** z unikatowym schematem. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Istnieją dwie ważne kwestie do rozważenia podczas wybierania identyfikatora URI przekierowania:
    * **Unikatowy**: schemat identyfikatora URI przekierowania musi być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` jest to schemat `com.onmicrosoft.contosob2c.exampleapp`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik zdecyduje się nieprawidłowo, logowanie nie powiedzie się.
    * **Ukończono**: identyfikator URI przekierowania musi mieć zarówno schemat, jak i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//oauth/` działa, gdy `//oauth` nie powiedzie się. Nie dołączaj znaków specjalnych w identyfikatorze URI, na przykład podkreślenia.
1. Wybierz pozycję **Utwórz**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)** , a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *nativeapp1*.
1. W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym lub dowolnym dostawcy tożsamości**.
1. W obszarze **Identyfikator URI przekierowania**Użyj listy rozwijanej, aby wybrać opcję **Klient publiczny/natywny (Mobile & Desktop)** .
1. Wprowadź identyfikator URI przekierowania z unikatowym schematem. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Istnieją dwie ważne kwestie do rozważenia podczas wybierania identyfikatora URI przekierowania:
    * **Unikatowy**: schemat identyfikatora URI przekierowania musi być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` jest to schemat `com.onmicrosoft.contosob2c.exampleapp`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik zdecyduje się nieprawidłowo, logowanie nie powiedzie się.
    * **Ukończono**: identyfikator URI przekierowania musi mieć zarówno schemat, jak i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//oauth/` działa, gdy `//oauth` nie powiedzie się. Nie dołączaj znaków specjalnych w identyfikatorze URI, na przykład podkreślenia.
1. W obszarze **uprawnienia**zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.