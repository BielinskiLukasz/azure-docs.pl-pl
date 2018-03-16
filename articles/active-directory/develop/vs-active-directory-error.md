---
title: "Jak diagnozowanie błędów w usłudze Azure Active Directory połączona usługa"
description: "W usłudze active directory połączone wykryto typ uwierzytelniania niezgodne"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: ab81c3385479a96fbfa7e68c4e81129ff327ed4b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnozowanie błędów przy użyciu usługi Azure Active Directory połączone

Podczas wykrywania poprzedni kod uwierzytelniania, połączenia usługi Azure Active Directory server wykryto typ uwierzytelniania niezgodne.

Aby poprawnie wykrywać poprzedni kod uwierzytelniania w projekcie, projektu muszą zostać skompilowane.  Jeśli wystąpił błąd i nie masz poprzedni kod uwierzytelniania w projekcie, skompiluj ponownie i spróbuj ponownie.

## <a name="project-types"></a>Typy projektów

Podłączonej usługi sprawdza typ projektu, który projektujesz tak go wstrzyknąć logika uwierzytelniania prawo do projektu. W przypadku dowolnego kontrolera, która jest pochodną `ApiController` projektu jest uznawany za projektu WebAPI w projekcie. Jeśli występują tylko te kontrolery, które pochodzą z `MVC.Controller` w projekcie, projektu jest uznawany za projekt platformy MVC. Podłączonej usługi nie obsługuje żadnego typu projektu.

## <a name="compatible-authentication-code"></a>Kod uwierzytelniania zgodne

Ustawienia uwierzytelniania, które zostały wcześniej skonfigurowane lub są zgodne z usługą sprawdza również podłączonej usługi. Jeśli wszystkie ustawienia są obecne, jest uznawany za przypadek wielobieżnej i otwiera podłączonej usługi wyświetlić ustawienia.  Jeśli tylko niektóre ustawienia są obecne, jest uznawane za przypadek błędu.

W projekcie MVC podłączonej usługi sprawdza dla żadnej z następujących ustawień, wynikające z poprzednich korzystania z usługi:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Ponadto podłączonej usługi sprawdza następujące ustawienia w projekcie interfejsu API sieci Web, wynikające z poprzednich korzystania z usługi:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Kod uwierzytelniania niezgodne

Na koniec podłączonej usługi próbuje wykryć wersji kod uwierzytelniania, które zostały skonfigurowane z poprzednich wersji programu Visual Studio. Jeśli wystąpił ten błąd, oznacza to, że projekt zawiera typ uwierzytelniania niezgodne. Podłączonej usługi wykrywa następujące typy uwierzytelniania z poprzednich wersji programu Visual Studio:

* Uwierzytelnianie systemu Windows
* Indywidualne konta użytkowników
* Konta organizacyjne

Do wykrywania uwierzytelniania systemu Windows w projekcie programu MVC, połączonych szuka `authentication` element w Twojej `web.config` pliku.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Do wykrywania uwierzytelniania systemu Windows w projekcie interfejsu API sieci Web, podłączonej usługi szuka `IISExpressWindowsAuthentication` elementu do projektu `.csproj` pliku:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

Wyszukuje podłączonej usługi do wykrywania uwierzytelniania indywidualnych kont użytkowników, element pakietu w Twojej `packages.config` pliku.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

Aby wykrywać stare formy uwierzytelniania konto organizacyjne, podłączonej usługi szuka następujący element w`web.config`:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

Aby zmienić typ uwierzytelniania, Usuń typ uwierzytelniania niezgodne, a następnie spróbuj ponownie dodać podłączonej usługi.

Aby uzyskać więcej informacji, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](active-directory-authentication-scenarios.md).