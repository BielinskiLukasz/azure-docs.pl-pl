---
title: Uzyskaj token dla internetowego interfejsu API, który wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć interfejs API sieci Web, który wywołuje interfejsy API sieci Web, które wymagają uzyskiwania tokenu dla aplikacji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7e0701cc5a9bb14800a48e2281dba1eb6ea0cf72
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026462"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: uzyskiwanie tokenu dla aplikacji

Po skompilowaniu obiektu aplikacji klienckiej Użyj go, aby uzyskać token, którego można użyć do wywołania interfejsu API sieci Web.

## <a name="code-in-the-controller"></a>Kod w kontrolerze

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Oto przykład kodu używającego elementu Microsoft. Identity. Web, który jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API o nazwie *todolist*. Aby uzyskać token wywołujący podrzędny interfejs API, należy wstrzyknąć `ITokenAcquisition` usługę przez wstrzyknięcie do niego w konstruktorze (lub w konstruktorze strony, jeśli używasz Blazor), i używać jej w akcjach kontrolera, pobierając token dla użytkownika ( `GetAccessTokenForUserAsync` ) lub aplikacji ( `GetAccessTokenForAppAsync` ) w przypadku scenariusza demona.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Aby uzyskać szczegółowe informacje na temat `callTodoListService` metody, zobacz [internetowy interfejs API, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API](scenario-web-api-call-api-call-api.md).

# <a name="java"></a>[Java](#tab/java)
Oto przykład kodu, który jest wywoływany w akcjach kontrolerów interfejsu API. Wywołuje podrzędny interfejs API-Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Interfejs API sieci Web w języku Python będzie musiał użyć oprogramowania pośredniczącego w celu zweryfikowania tokenu okaziciela otrzymanego od klienta. Interfejs API sieci Web może następnie uzyskać token dostępu dla podrzędnego interfejsu API przy użyciu biblioteki języka Python MSAL, wywołując [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metodę. Przykład pokazujący ten przepływ przy użyciu języka MSAL Python nie jest jeszcze dostępny.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: wywoływanie interfejsu API](scenario-web-api-call-api-call-api.md)
