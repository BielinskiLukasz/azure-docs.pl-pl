---
title: Tworzenie dzierżawy usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak utworzyć dzierżawę usługi Azure AD na potrzeby rejestrowania i tworzenia aplikacji.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/05/2019
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 0ffde84a39ef2955f14af66a05195e35109d41bb
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692141"
---
# <a name="quickstart-set-up-a-dev-environment"></a>Szybki start: Konfigurowanie środowiska deweloperskiego

Platforma tożsamości firmy Microsoft umożliwia deweloperom tworzenie aplikacji przeznaczonych dla wielu różnych niestandardowych środowisk oraz tożsamości usługi Microsoft 365. Aby rozpocząć korzystanie z platformy tożsamości firmy Microsoft, należy uzyskać dostęp do środowiska, nazywanego również dzierżawą usługi Azure AD, które umożliwia rejestrowania aplikacji i zarządzanie nimi, uzyskać dostęp do danych platformy Microsoft 365 i wdrożyć niestandardowe ograniczenia dostępu warunkowego i dzierżawy.

Dzierżawa reprezentuje organizację. Jest to dedykowane wystąpienie usługi Azure AD, którą organizacja lub deweloper aplikacji otrzymuje po utworzeniu relacji z firmą Microsoft, na przykład zarejestrowaniu się na platformie Azure, w usłudze Microsoft Intune lub Microsoft 365.

Każda dzierżawa usługi Azure AD jest odrębna i oddzielona od innych dzierżaw usługi Azure AD i ma swoją własną reprezentację tożsamości służbowych, tożsamości klientów (jeśli jest to dzierżawa usługi Azure AD B2C) i rejestracji aplikacji. Rejestrowanie aplikacji w dzierżawie umożliwia uwierzytelnianie tylko z poziomu kont w ramach dzierżawy lub ze wszystkich dzierżaw.

## <a name="determining-environment-type"></a>Określanie typu środowiska

Istnieją dwa typy środowiska, które można utworzyć. To, które jest Ci potrzebne, zależy wyłącznie od typu użytkowników, których będzie uwierzytelniać aplikacja.

* Konta służbowe (konta usługi Azure AD) lub konta Microsoft (takie jak outlook.com i live.com)
* Konta społecznościowe i lokalne (Azure AD B2C)

Podręcznik Szybki start został podzielony na dwa scenariusze, w zależności od rodzaju aplikacji, którą chcesz utworzyć. Jeśli potrzebujesz dodatkowej pomocy podczas określania docelowego typu tożsamości, zapoznaj się ze stroną [About Microsoft identity platform (Informacje dotyczące platformy tożsamości firmy Microsoft)](about-microsoft-identity-platform.md)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Konta służbowe lub osobiste konta Microsoft

### <a name="use-an-existing-tenant"></a>Używanie istniejącej dzierżawy

Wielu deweloperów uzyskało już dzierżawy za pośrednictwem usług lub subskrypcji, które są powiązane z dzierżawami usługi Azure AD, na przykład subskrypcji rozwiązania Microsoft 365 lub platformy Azure.

1. Aby sprawdzić dzierżawę, zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta, którego chcesz użyć do zarządzania aplikacją.
1. Sprawdź prawy górny róg. Jeśli masz dzierżawę, nastąpi automatyczne zalogowanie do niej, a nazwa dzierżawy zostanie wyświetlona bezpośrednio pod nazwą Twojego konta.
   * Umieść wskaźnik myszy na nazwie konta w prawym górnym rogu witryny Azure Portal, aby wyświetlić swoją nazwę, adres e-mail, identyfikator katalogu/dzierżawy (GUID) oraz domenę.
   * Jeśli Twoje konto jest skojarzone z wieloma dzierżawami, możesz wybrać nazwę swojego konta, aby otworzyć menu, w którym można przełączać się między dzierżawami. Każda dzierżawa ma własny identyfikator dzierżawy.

> [!TIP]
> Aby znaleźć identyfikator dzierżawy, możesz wykonać następujące czynności:
* Umieść wskaźnik myszy nad nazwą konta, aby uzyskać identyfikator katalogu/dzierżawy. Ewentualnie:
* Wybierz kolejno pozycje **Azure Active Directory > Właściwości > Identyfikator katalogu** w witrynie Azure Portal

Jeśli nie masz istniejącej dzierżawy skojarzonej z kontem, pod nazwą konta zostanie wyświetlony identyfikator GUID i nie będzie można przykład zarejestrować aplikacji, dopóki nie zostaną wykonane czynności opisane w kolejnej sekcji.

### <a name="create-a-new-azure-ad-tenant"></a>Tworzenie nowej dzierżawy usługi Azure AD

Jeśli nie masz jeszcze dzierżawy usługi Azure AD lub chcesz utworzyć nową do celów deweloperskich, wykonaj czynności opisane w artykule dotyczącym [środowiska tworzenia katalogu](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Aby utworzyć nową dzierżawę, należy podać następujące informacje:

- **Nazwa organizacji**
- **Domena początkowa** — to część adresu *. onmicrosoft.com. Możesz dodatkowo dostosować domenę w przyszłości.
- **Kraj lub region**

> [!NOTE]
> Podczas określania nazwy dzierżawy używaj znaków alfanumerycznych. Znaki specjalne są niedozwolone. Długość nazwy nie może przekraczać 256 znaków.

## <a name="social-and-local-accounts"></a>Konta społecznościowe i lokalne

Aby rozpocząć tworzenie aplikacji, które umożliwiają logowanie za pomocą kont społecznościowych i lokalnych, musisz utworzyć dzierżawę usługi Azure AD B2C. Aby rozpocząć, wykonaj czynności opisane w artykule dotyczącym [tworzenia dzierżawy usługi Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z podręcznikiem Szybki start dotyczącym kodowania i zacznij uwierzytelniać użytkowników. 
* Aby uzyskać więcej szczegółowych przykładów kodu, zapoznaj się z sekcją **Samouczki** w dokumentacji.
* Chcesz wdrożyć aplikację w chmurze? Zapoznaj się z artykułem dotyczącym[wdrażania kontenerów na platformie Azure](https://docs.microsoft.com/azure/index#pivot=products&panel=containers). 
