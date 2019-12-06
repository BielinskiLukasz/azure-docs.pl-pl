---
title: Warunki użytkowania usługi i zasady zachowania poufności informacji dla aplikacji | Azure
description: Dowiedz się, jak skonfigurować warunki użytkowania usługi i zasady zachowania poufności informacji dla aplikacji zarejestrowanych w celu korzystania z usługi Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca59120bcfadc6c75e3687be84a0e70f97fc3c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842814"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Instrukcje: Konfigurowanie warunków użytkowania i zasad zachowania poufności informacji dla aplikacji

Deweloperzy, którzy tworzą aplikacje, które integrują się z usługą Azure Active Directory (Azure AD) i konta Microsoft, powinny zawierać linki do warunków użytkowania i zasad zachowania poufności informacji aplikacji. Warunki użytkowania usługi i zasady zachowania poufności informacji są udostępniane użytkownikom za pomocą funkcji wyrażania zgody użytkownika. Pomagają użytkownikom wiedzieć, że mogą ufać aplikacji. Warunki użytkowania usługi i zasady zachowania poufności informacji są szczególnie ważne w przypadku aplikacji wielodostępnych przeznaczonych dla użytkowników — aplikacji, które są używane przez wiele katalogów lub są dostępne dla dowolnej konto Microsoft.

Użytkownik jest odpowiedzialny za tworzenie dokumentów dotyczących warunków użytkowania i zasad zachowania poufności informacji dla aplikacji oraz dostarczanie adresów URL do tych dokumentów. W przypadku aplikacji wielodostępnych, w przypadku których nie podano tych linków, środowisko zgody użytkownika dla aplikacji będzie zawierało alert, który może zniechęcać użytkowników do Twojej aplikacji.

> [!NOTE]
> * Aplikacje z jedną dzierżawą nie będą wyświetlać alertu.
> * Jeśli brakuje jednego lub obu dwóch linków, aplikacja wyświetli alert.

## <a name="user-consent-experience"></a>Środowisko zgody użytkownika

W poniższych przykładach przedstawiono sposób wyrażania zgody użytkownika w przypadku skonfigurowania warunków użytkowania usługi i zasad zachowania poufności informacji oraz gdy te linki nie są skonfigurowane.

![Zrzuty ekranu z i bez zasad zachowania poufności informacji oraz dostarczonych warunków świadczenia usługi](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatowanie linków do dokumentów dotyczących warunków użytkowania i zasad zachowania poufności informacji

Przed dodaniem linków do dokumentów dotyczących warunków użytkowania i zasad zachowania poufności informacji upewnij się, że adresy URL są zgodne z tymi wskazówkami.

| Wytyczna     | Opis                           |
|---------------|---------------------------------------|
| Format        | Prawidłowy adres URL                             |
| Prawidłowe schematy | HTTP i HTTPS<br/>Zalecamy korzystanie z protokołu HTTPS |
| Długość maksymalna    | 2048 znaków                       |

Przykłady: `https://myapp.com/terms-of-service` i `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Dodawanie linków do warunków użytkowania usługi i zasad zachowania poufności informacji

Gdy warunki użytkowania usługi i zasady zachowania poufności informacji są gotowe, możesz dodać linki do tych dokumentów w aplikacji przy użyciu jednej z następujących metod:

* [Za pomocą Azure Portal](#azure-portal)
* [Używanie pliku JSON obiektu App](#app-object-json)
* [Korzystanie z interfejsu API REST MSGraph beta](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Korzystanie z Azure Portal
Wykonaj następujące kroki w Azure Portal.

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Przejdź do sekcji **rejestracje aplikacji** i wybierz aplikację.
3. Otwórz okienko **znakowania** .
4. Wypełnij pola adres **URL warunków** użytkowania i **zasady zachowania poufności informacji** .
5. Zapisz zmiany.

    ![Właściwości aplikacji zawierają adresy URL warunków użytkowania usługi i zasad zachowania poufności informacji](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Używanie pliku JSON obiektu App

Jeśli wolisz bezpośrednio modyfikować kod JSON obiektu App, możesz użyć edytora manifestu w portalu Azure Portal lub rejestracji aplikacji, aby dołączyć linki do warunków użytkowania i zasad zachowania poufności informacji aplikacji.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Korzystanie z interfejsu API REST MSGraph beta

Aby programowo zaktualizować wszystkie swoje aplikacje, możesz użyć interfejsu API REST MSGraph beta w celu zaktualizowania wszystkich aplikacji, aby zawierały linki do dokumentów warunków użytkowania usługi i zasad zachowania poufności informacji.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Należy zachować ostrożność, aby nie zastąpić żadnych istniejących wcześniej wartości przypisanych do któregokolwiek z następujących pól: `supportUrl`, `marketingUrl`i `logoUrl`
> * Interfejs API REST MSGraph beta będzie działał tylko po zalogowaniu się przy użyciu konta usługi Azure AD. Osobiste konta Microsoft nie są obsługiwane.
