---
title: Jak obsłużyć zmiany plików cookie SameSite w przeglądarce Chrome | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać zmiany plików cookie SameSite w przeglądarce Chrome.
services: active-directory
documentationcenter: ''
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 8fc1fab89a89fbf7e20414f292a1b02f77ac7907
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776367"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Obsługuj SameSite zmiany plików cookie w przeglądarce Chrome

## <a name="what-is-samesite"></a>Co to jest SameSite?

`SameSite` to właściwość, którą można ustawić w plikach cookie protokołu HTTP, aby zapobiec atakom CSRF (cross-site) w aplikacjach sieci Web:

- Gdy `SameSite` jest ustawiona na **swobodny**, plik cookie jest wysyłany w żądaniach w ramach tej samej lokacji i w żądaniach Get z innych lokacji. Nie jest on wysyłany w żądaniach GET należących do wielu domen.
- Wartość **Strict** gwarantuje, że plik cookie jest wysyłany w żądaniach tylko w ramach tej samej lokacji.

Domyślnie wartość `SameSite` nie jest ustawiona w przeglądarkach i dlatego nie ma żadnych ograniczeń dotyczących plików cookie wysyłanych w żądaniach. Aplikacja musiałaby zadecydować, aby CSRF ochronę przez ustawienie **swobodny** lub **rygorystyczne** zgodnie z ich wymaganiami.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite zmiany i wpływ na uwierzytelnianie

Najnowsze [aktualizacje standardów SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) zaproponują ochronę aplikacji przez zachowanie domyślnego zachowania `SameSite`, gdy żadna wartość nie jest ustawiona na swobodny. Takie łagodzenie oznacza, że pliki cookie będą ograniczone do żądań HTTP, z wyjątkiem przeprowadzonych z innych lokacji. Dodatkowo wprowadzono wartość **Brak** , aby usunąć ograniczenia dotyczące wysyłanych plików cookie. Te aktualizacje zostaną wkrótce udostępnione w nadchodzącej wersji przeglądarki Chrome.

Gdy aplikacje sieci Web są uwierzytelniane za pomocą platformy tożsamości firmy Microsoft przy użyciu trybu odpowiedzi "form_post", serwer logowania odpowiada aplikacji za pomocą polecenia POST protokołu HTTP w celu wysłania tokenów lub kodu uwierzytelniania. Ponieważ to żądanie jest żądaniem obejmującym wiele domen (od `login.microsoftonline.com` do domeny — w przypadku wystąpienia https://contoso.com/auth) pliki cookie, które zostały ustawione przez aplikację, są teraz przydzielone do nowych reguł w programie Chrome. Pliki cookie, które muszą być używane w scenariuszach między lokacjami, to pliki cookie, które przechowują wartości *stanu* i *jednorazowego* , które są również wysyłane w żądaniu logowania. Istnieją inne pliki cookie porzucone przez usługę Azure AD do przechowywania sesji.

Jeśli nie zaktualizujesz aplikacji sieci Web, to nowe zachowanie spowoduje błędy uwierzytelniania.

## <a name="mitigation-and-samples"></a>Środki zaradcze i przykłady

W celu pokonania błędów uwierzytelniania aplikacje sieci Web uwierzytelniane za pomocą platformy tożsamości firmy Microsoft mogą ustawić właściwość `SameSite` na `None` dla plików cookie, które są używane w scenariuszach międzydomenowych podczas uruchamiania w przeglądarce Chrome.
Inne przeglądarki (zobacz [tutaj](https://www.chromium.org/updates/same-site/incompatible-clients) , aby uzyskać pełną listę) postępuj zgodnie z poprzednim zachowaniem `SameSite` i nie Uwzględniaj plików cookie, jeśli jest ustawiony `SameSite=None`.
Dlatego w celu obsługi uwierzytelniania w wielu przeglądarkach aplikacje sieci Web będą musieli ustawić wartość `SameSite` na `None` tylko w programie Chrome i pozostawić wartość pustą w innych przeglądarkach.

Ta metoda jest przedstawiona w naszym przykładowym kodzie.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

W poniższej tabeli przedstawiono żądania ściągnięcia, które działały wokół zmian SameSite w naszych ASP.NET i ASP.NET Coreych.

| Przykład | Żądanie ściągnięcia |
| ------ | ------------ |
|  [ASP.NET Core przyrostowy samouczek aplikacji sieci Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Ta sama Naprawa plików cookie lokacji #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [Przykład aplikacji sieci Web ASP.NET MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Ta sama Naprawa plików cookie lokacji #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-dotnet-administrator-z ograniczeniami-Scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Ta sama Naprawa plików cookie lokacji #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

Aby uzyskać szczegółowe informacje na temat obsługi plików cookie SameSite w ASP.NET i ASP.NET Core, zobacz również:

- [Pracuj z plikami cookie SameSite w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [Blog ASP.NET na temat problemu SameSite](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="pythontabpython"></a>[Python](#tab/python)

| Przykład |
| ------ |
|  [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="javatabjava"></a>[Java](#tab/java)

| Przykład | Żądanie ściągnięcia |
| ------ | ------------ |
|  [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Ta sama Naprawa plików cookie lokacji #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-WebApi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Ta sama Naprawa plików cookie lokacji #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat SameSite i scenariusza aplikacji sieci Web:

> [!div class="nextstepaction"]
> [Często zadawane pytania dotyczące usługi Google Chrome w witrynie SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Strona SameSite chromu](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja sieci Web, która loguje użytkowników](scenario-web-app-sign-user-overview.md)