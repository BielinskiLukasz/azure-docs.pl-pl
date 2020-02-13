---
title: Uwierzytelnianie i autoryzacja
description: Dowiedz się więcej na temat różnych sposobów uwierzytelniania aplikacji lub usługi w kotwicach przestrzennych platformy Azure oraz poziomów kontroli, które mają być bramą dostępu do kotwic przestrzennych platformy Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3de84e2d814acfca67bc722243a90fa41f6536e1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161685"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Uwierzytelnianie i autoryzacja w kotwicach przestrzennych platformy Azure

W tej sekcji omówiono różne sposoby uwierzytelniania w zakotwiczeniach przestrzennych platformy Azure z poziomu aplikacji lub usługi sieci Web, a także sposoby używania Access Control opartych na rolach w usłudze Azure Directory (Azure AD) w celu kontrolowania dostępu do kont zakotwiczeń przestrzennych.

## <a name="overview"></a>Omówienie

![Omówienie uwierzytelniania w kotwicach przestrzennych platformy Azure](./media/spatial-anchors-authentication-overview.png)

Aby uzyskać dostęp do danego konta zakotwiczeń przestrzennych platformy Azure, klienci muszą najpierw uzyskać token dostępu z usługi Azure Mixed Reality Security Token Service (STS). Tokeny uzyskane z usługi STS Live przez 24 godziny i zawierają informacje dotyczące usług kotwic przestrzennych w celu podejmowania decyzji dotyczących autoryzacji na koncie i zapewniają, że tylko autoryzowane podmioty zabezpieczeń mogą uzyskiwać dostęp do tego konta.

Tokeny dostępu można uzyskać w programie Exchange z dowolnego klucza konta lub z tokenów wystawionych przez usługę Azure AD.

Klucze kont umożliwiają szybkie rozpoczynanie pracy przy użyciu usługi zakotwiczeń przestrzennych platformy Azure. Jednak przed wdrożeniem aplikacji w środowisku produkcyjnym zaleca się zaktualizowanie aplikacji w taki sposób, aby korzystała z uwierzytelniania opartego na usłudze Azure AD.

Tokeny uwierzytelniania usługi Azure AD można uzyskać na dwa sposoby:

- Jeśli tworzysz aplikację dla przedsiębiorstw, a Twoja firma korzysta z usługi Azure AD jako systemu tożsamości, możesz użyć uwierzytelniania opartego na użytkownikach usługi Azure AD w aplikacji oraz udzielić dostępu do kont kotwicy przestrzennej przy użyciu istniejących grup zabezpieczeń usługi Azure AD lub bezpośrednio dla użytkowników w organizacji.
- W przeciwnym razie zalecane jest uzyskanie tokenów usługi Azure AD z usługi sieci Web obsługującej aplikację. Zalecaną metodą uwierzytelniania aplikacji produkcyjnych jest użycie dodatkowej usługi sieci Web, ponieważ pozwala to uniknąć osadzania poświadczeń w celu uzyskania dostępu do kotwic przestrzennych platformy Azure w aplikacji klienckiej.

## <a name="account-keys"></a>Klucze konta

Korzystanie z kluczy konta do uzyskiwania dostępu do konta zakotwiczeń przestrzennych platformy Azure jest najprostszym sposobem na rozpoczęcie pracy. Klucze kont znajdziesz na Azure Portal. Przejdź do swojego konta i wybierz kartę "klucze".

![Omówienie uwierzytelniania w kotwicach przestrzennych platformy Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Dostępne są dwa klucze, które jednocześnie są prawidłowe w celu uzyskania dostępu do konta kotwice przestrzenne. Zalecane jest regularne aktualizowanie klucza używanego do uzyskiwania dostępu do konta. posiadanie dwóch oddzielnych prawidłowych kluczy pozwala na takie aktualizacje bez przestojów. musisz tylko zaktualizować klucz podstawowy i klucz pomocniczy.

Zestaw SDK ma wbudowaną obsługę uwierzytelniania przy użyciu kluczy konta; Wystarczy ustawić właściwość AccountKey obiektu cloudSession.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++ZGŁOSIŁ](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++Środowiska](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Gdy to zrobisz, zestaw SDK będzie obsługiwał wymianę klucza konta dla tokenu dostępu i wymagane buforowanie tokenów dla aplikacji.

> [!WARNING]
> Użycie kluczy konta jest zalecane do szybkiego udostępniania, ale tylko w przypadku tworzenia/prototypowania. Zdecydowanie zaleca się, aby nie dostarczać aplikacji do środowiska produkcyjnego przy użyciu osadzonego klucza konta, a zamiast tego używać metod uwierzytelniania usługi Azure AD opartych na użytkownikach lub usługach, które znajdują się dalej.

## <a name="azure-ad-user-authentication"></a>Uwierzytelnianie użytkowników w usłudze Azure AD

W przypadku aplikacji przeznaczonych dla użytkowników Azure Active Directory Zalecanym podejściem jest użycie tokenu usługi Azure AD dla użytkownika, który można uzyskać przy użyciu [biblioteki MSAL](../../active-directory/develop/msal-overview.md). Należy wykonać czynności opisane w sekcji [Rejestrowanie przewodnika Szybki Start](../../active-directory/develop/quickstart-register-app.md)dotyczącego aplikacji, co obejmuje:

1. Konfiguracja w Azure Portal
    1.  Zarejestruj swoją aplikację w usłudze Azure AD jako **aplikację natywną**. W ramach rejestracji należy określić, czy aplikacja powinna mieć dostęp do wielu dzierżawców, a także podać adresy URL przekierowania dozwolone dla aplikacji.
        1.  Przejdź do karty **uprawnienia interfejsu API**
        2.  Wybierz pozycję **Dodaj uprawnienie**
            1.  Wybieranie **dostawcy zasobów rzeczywistości mieszanej** w obszarze **interfejsy API Moja organizacja używa** karty
            2.  Wybierz **uprawnienia delegowane**
            3.  Zaznacz pole wyboru dla **mixedreality. Zaloguj się** w obszarze **mixedreality**
            4.  Wybierz pozycję **Dodaj uprawnienia**
        3.  Wybierz pozycję **Udziel zgody administratora**
    2.  Udziel aplikacji lub użytkownikom dostępu do zasobu:
        1.  Przejdź do zasobu zakotwiczenia przestrzennego w Azure Portal
        2.  Przełącz na kartę **Kontrola dostępu (IAM)**
        3.  Trafienie **Dodaj przypisanie roli**
            1.  [Wybierz rolę](#role-based-access-control)
            2.  W polu **SELECT (Wybieranie** ) wprowadź nazwę użytkowników, grup i/lub aplikacji, do których chcesz przypisać prawa dostępu.
            3.  Wybierz pozycję **Zapisz**.
2. W kodzie:
    1.  Upewnij się, że używasz **identyfikatora aplikacji** i identyfikatora **URI przekierowania** własnej aplikacji usługi Azure AD jako **identyfikatora klienta** i parametrów **RedirectUri** w bibliotece ADAL
    2.  Ustaw informacje o dzierżawie:
        1.  Jeśli aplikacja obsługuje **tylko moją organizację**, Zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład contoso.Microsoft.com)
        2.  Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym**, Zastąp tę wartość **organizacją**
        3.  Jeśli aplikacja obsługuje **wszystkich konto Microsoft użytkowników**, Zastąp tę wartość **wspólną**
    3.  Na żądanie tokenu Ustaw **zasób** na "https://sts.mixedreality.azure.com". Ten "zasób" wskazuje usłudze Azure AD, że aplikacja żąda tokenu dla usługi zakotwiczeń przestrzennych platformy Azure.

Dzięki temu aplikacja powinna być w stanie uzyskać od MSAL tokenu usługi Azure AD; Możesz ustawić ten token usługi Azure AD jako **authenticationToken** w obiekcie konfiguracji sesji w chmurze.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++ZGŁOSIŁ](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++Środowiska](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Uwierzytelnianie usługi Azure AD

Zalecaną opcją wdrożenia aplikacji wykorzystujących kotwice przestrzenne platformy Azure w środowisku produkcyjnym jest użycie usługi wewnętrznej bazy danych, która będzie brokerem żądań uwierzytelniania. Schemat ogólny powinien być opisany w tym diagramie:

![Omówienie uwierzytelniania w kotwicach przestrzennych platformy Azure](./media/spatial-anchors-aad-authentication.png)

W tym miejscu zakłada się, że aplikacja używa własnego mechanizmu (na przykład: konto Microsoft, PlayFab, Facebook, Google ID, niestandardowej nazwy użytkownika/hasła itp.) w celu uwierzytelnienia w usłudze zaplecza. Po uwierzytelnieniu użytkowników w usłudze wewnętrznej bazy danych usługa ta może pobrać token usługi Azure AD, wymienić go na token dostępu dla kotwic przestrzennych platformy Azure i przywrócić go do aplikacji klienckiej.

Token dostępu usługi Azure AD jest pobierany przy użyciu [biblioteki MSAL](../../active-directory/develop/msal-overview.md). Należy wykonać czynności opisane w sekcji [Rejestrowanie przewodnika Szybki Start](../../active-directory/develop/quickstart-register-app.md)dotyczącego aplikacji, co obejmuje:

1.  Konfiguracja w Azure Portal:
    1.  Zarejestruj swoją aplikację w usłudze Azure AD:
        1.  W Azure Portal przejdź do **Azure Active Directory**i wybierz pozycję **rejestracje aplikacji**
        2.  Wybierz pozycję **rejestracja nowej aplikacji**
        3.  Wprowadź nazwę aplikacji, wybierz pozycję **Web App/API** jako typ aplikacji, a następnie wprowadź adres URL uwierzytelniania dla usługi. Następnie kliknij przycisk **Utwórz**.
        4.  W tej aplikacji naciśnij pozycję **Ustawienia**, a następnie wybierz kartę **klucze** . Wprowadź nazwę klucza, wybierz czas trwania i naciśnij pozycję **Zapisz**. Pamiętaj, aby zapisać wartość klucza, która jest wyświetlana w tym czasie, ponieważ należy ją uwzględnić w kodzie usługi sieci Web.
    2.  Przyznaj aplikacji i/lub użytkownikom dostęp do zasobu:
        1.  Przejdź do zasobu zakotwiczenia przestrzennego w Azure Portal
        2.  Przełącz na kartę **Kontrola dostępu (IAM)**
        3.  Trafienie **Dodaj przypisanie roli**
        1.  [Wybierz rolę](#role-based-access-control)
        2.  W polu **Wybierz** wprowadź nazwę utworzonych aplikacji i, do których chcesz przypisać dostęp. Jeśli chcesz, aby użytkownicy Twojej aplikacji mieli różne role względem konta zakotwiczeń przestrzennych, należy zarejestrować wiele aplikacji w usłudze Azure AD i przypisać je do każdej innej roli. Następnie Zaimplementuj logikę autoryzacji, aby korzystać z odpowiedniej roli dla użytkowników.
    3.  Wybierz pozycję **Zapisz**.
2.  W kodzie (Uwaga: możesz użyć przykładu usługi dostępnego w witrynie GitHub):
    1.  Upewnij się, że używasz identyfikatora aplikacji, klucza tajnego aplikacji i identyfikatora URI przekierowania dla własnej aplikacji usługi Azure AD jako parametry identyfikatora klienta, klucza tajnego i RedirectUri w bibliotece ADAL
    2.  Ustaw identyfikator dzierżawy na własny AAAzure Dodaj identyfikator dzierżawy w parametrze urzędu w bibliotece ADAL
    3.  Na żądanie tokenu Ustaw **zasób** na "https://sts.mixedreality.azure.com"

Dzięki temu usługa zaplecza może pobrać token usługi Azure AD. Następnie może go wymienić na token MR, który zwróci zwrot do klienta. Użycie tokenu usługi Azure AD w celu pobrania tokenu MR odbywa się za pośrednictwem wywołania REST. Oto przykładowe wywołanie:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Gdzie nagłówek autoryzacji jest sformatowany w następujący sposób: `Bearer <accoundId>:<accountKey>`

A odpowiedź zawiera token MR w postaci zwykłego tekstu.

Token MR jest następnie zwracany do klienta. Aplikacja kliencka może następnie ustawić ją jako token dostępu w konfiguracji sesji w chmurze.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++ZGŁOSIŁ](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++Środowiska](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby ułatwić kontrolę poziomu dostępu udzielonego aplikacjom, usługom lub użytkownikom usługi Azure AD w usłudze, zostały utworzone następujące role do przypisywania w razie potrzeby do kont zakotwiczeń przestrzennych platformy Azure:

- **Właściciele konta kotwic przestrzenny**: aplikacje lub Użytkownicy z tą rolą mogą tworzyć kotwice przestrzenne, wysyłać do nich zapytania i usuwać je. W przypadku uwierzytelniania na koncie przy użyciu kluczy konta rola **właściciela konta kotwicy przestrzennej** jest przypisana do uwierzytelnionego podmiotu zabezpieczeń.
- **Współautor konta kotwic przestrzenny**: aplikacje lub Użytkownicy, którzy mają tę rolę, mogą tworzyć kotwice przestrzenne, wysyłać do nich zapytania, ale nie mogą ich usuwać.
- **Czytnik kont kotwic przestrzenny**: aplikacje lub Użytkownicy, którzy mają tę rolę, mogą wykonywać zapytania dotyczące kotwic przestrzennych, ale nie mogą tworzyć nowych, usuwać istniejących ani aktualizować metadanych w kotwicach przestrzennych. Jest to zwykle używane w przypadku aplikacji, w których niektórzy użytkownicy nadzorują środowisko, a inne mogą odwoływać kotwice wcześniej umieszczone w tym środowisku.

## <a name="next-steps"></a>Następne kroki

Utwórz swoją pierwszą aplikację z zakotwiczeniami przestrzennymi platformy Azure.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
