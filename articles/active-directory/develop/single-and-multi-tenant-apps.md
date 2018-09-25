---
title: Jednym i wieloma dzierżawami aplikacji w usłudze Azure Active Directory
description: Dowiedz się więcej o funkcjach i różnice między jedną dzierżawą i wielodostępne aplikacje w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 8986f18b9f1f2953ad0612679d31996628870cb1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988227"
---
# <a name="tenancy-in-azure-active-directory"></a>Dzierżawy w usłudze Azure Active Directory

Azure Active Directory (Azure AD) organizuje obiektów, takich jak użytkownicy i aplikacje w grupy o nazwie *dzierżaw*. Dzierżawcy pozwalają administratorowi ustawić zasady dla użytkowników w organizacji i aplikacje, które organizacji w celu spełnienia ich zabezpieczeń i zasad operacyjnych. 

## <a name="who-can-sign-in-to-your-app"></a>Kto może logować się do aplikacji?

Jeśli chodzi o tworzenie aplikacji, deweloperom możliwość konfigurowania aplikacji jako pojedynczej dzierżawy lub wielodostępne podczas rejestracji aplikacji w [witryny Azure portal](http://portal.azure.com).
* Aplikacje z jedną dzierżawą są dostępne tylko w dzierżawie, na których zostały one zarejestrowane w znany także jako swojej głównej dzierżawy.
* Aplikacje z wieloma dzierżawami, są dostępne dla użytkowników w ich dzierżawy głównej i w innych dzierżaw.

W witrynie Azure portal można skonfigurować aplikację na pojedynczej dzierżawy lub wielodostępne, ustawiając odbiorców w następujący sposób.

| Grupy odbiorców | Jednym/wielu-tenant | Kto może się zarejestrować | 
|----------|--------| ---------|
| Konta, w tym katalogu tylko | Pojedyncza dzierżawa | Wszystkie konta użytkowników i gościa w katalogu, można użyć do aplikacji lub interfejsu API.<br>*Użyj tej opcji, jeśli sprecyzowaną grupę odbiorców jest wewnętrzną aplikacją organizacji.* |
| Konta w każdym katalogiem usługi Azure AD | Wiele dzierżaw | Wszyscy użytkownicy i gości za pomocą konta służbowego lub szkolnego, od firmy Microsoft, można użyć do aplikacji lub interfejsu API. Obejmuje to szkół i firm, które korzysta z usługi Office 365.<br>*Użyj tej opcji, jeśli sprecyzowaną grupę odbiorców jest firm i klientów edukacyjnych.* |
| Konta w dowolnym katalogu usługi Azure AD i osobistych kont Microsoft (np. Skype, Xbox, Outlook.com) | Wiele dzierżaw | Wszyscy użytkownicy z pracy lub szkoły lub osobiste konto Microsoft służy do aplikacji lub interfejsu API. Zawiera szkół i firm korzystających z usługi Office 365, a także konta osobiste, które są używane do logowania do usług, takich jak Xbox i Skype.<br>*Użyj tej opcji pod kątem możliwie najszerszej zbiór kont Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Najlepsze rozwiązania dla aplikacji wielodostępnych

Tworzyć wspaniałe aplikacje z wieloma dzierżawami może być trudne ze względu na liczbę różnych zasad, które Administratorzy IT mogą skonfigurować w swoich dzierżaw. Jeśli wybierzesz do tworzenia aplikacji z wieloma dzierżawami, wykonaj następujące najlepsze rozwiązania:

* Przetestuj swoją aplikację w dzierżawie, która została skonfigurowana [zasady dostępu warunkowego](conditional-access-dev-guide.md).
* Postępuj zgodnie z zasadą najniższych dostępu użytkowników, aby upewnić się, że żądane przez aplikację tylko uprawnienia, które jest faktycznie potrzebny. Należy unikać żądające uprawnień, które wymagają zgody administratora, ponieważ może to uniemożliwić użytkownikom pobieranie aplikacji na wszystkich w niektórych organizacjach. 
* Podaj odpowiednie nazwy i opisy dla wszelkich uprawnień, które należy udostępnić jako część aplikacji. Pomoże to użytkownikom i administratorom wiedzieć, co ich akceptuje po podjęciu próby użycia interfejsów API aplikacji. Aby uzyskać więcej informacji, zobacz sekcję najlepszych praktyk w [Przewodnik po uprawnieniach](v1-permissions-and-consent.md).

## <a name="next-steps"></a>Kolejne kroki

* [Jak konwertować aplikacji wielodostępnych](howto-convert-app-to-be-multi-tenant.md)
