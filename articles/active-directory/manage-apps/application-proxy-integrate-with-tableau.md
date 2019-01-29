---
title: Serwer Proxy aplikacji usługi Azure Active Directory i Tableau | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć serwera Proxy aplikacji usługi Azure Active Directory (Azure AD), aby zapewnić dostęp zdalny dla danego wdrożenia Tableau.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: b775d9377b95c3c63e3c38cd477ce47209a6f419
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149973"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Serwer Proxy aplikacji usługi Azure Active Directory i Tableau 

Usługa Azure Active Directory serwera Proxy aplikacji i Tableau wspólnie opracowały upewnij się, że możesz łatwo udostępnić dostępu zdalnego we wdrożeniu Tableau za pomocą serwera Proxy aplikacji. W tym artykule opisano sposób konfigurowania tego scenariusza.  

## <a name="prerequisites"></a>Wymagania wstępne 

Scenariusz, w tym artykule założono, że masz:

- [TABLEAU](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) skonfigurowane. 

- [Łącznik serwera Proxy aplikacji](application-proxy-add-on-premises-application.md) zainstalowane. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Włączanie serwera Proxy aplikacji dla firmy Tableau 

Serwer Proxy aplikacji obsługuje uwierzytelniania OAuth 2.0 przepływ przyznawania, co jest niezbędne do Tableau zapewnić prawidłowe działanie. Oznacza to, że już nie ma żadnych specjalnych czynności wymagane do włączenia tej aplikacji, niż jego konfigurowania, wykonując poniższe kroki publikowania.


## <a name="publish-your-applications-in-azure"></a>Publikuj swoje aplikacje na platformie Azure 

Aby opublikować Tableau, musisz opublikować aplikację w witrynie Azure Portal.

W przypadku:

- Szczegółowe instrukcje z kroków 1 – 8, zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). 
- Informacje o sposobach znajdowania Tableau wartości dla pól serwera Proxy aplikacji można znaleźć w dokumentacji Tableau.  

**Aby opublikować aplikację**: 


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny. 

2. Wybierz **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**. 

3. Wybierz **Dodaj** w górnej części bloku. 

4. Wybierz **aplikacje lokalne**. 

5. Wypełnij wymagane pola informacji o nowej aplikacji. Użyj poniższych wskazówek, aby ustawienia: 

    - **Wewnętrzny adres URL**: Ta aplikacja powinna mieć wewnętrzny adres URL, który jest adresem URL Tableau, sam. Na przykład `https://adventure-works.tableau.com`. 

    - **Metoda wstępnego uwierzytelnienia**: Usługa Azure Active Directory (zalecane, ale nie wymagane). 

6. Wybierz **Dodaj** w górnej części bloku. Aplikacja zostanie dodany, a zostanie otwarte menu skrócone. 

7. Wybierz z menu skrócone **przypisać użytkownika do testowania**, i Dodaj co najmniej jednego użytkownika do aplikacji. Upewnij się, że to konto testu ma dostęp do aplikacji w środowisku lokalnym. 

8. Wybierz **przypisać** można zapisać przypisania użytkownika testowego. 

9. (Opcjonalnie) Na stronie zarządzania aplikacji wybierz **logowanie jednokrotne**. Wybierz **zintegrowane uwierzytelnianie Windows** z menu rozwijanego, a następnie wypełnij wymagane pola, zgodnie z konfiguracją Tableau. Wybierz pozycję **Zapisz**. 

 

## <a name="testing"></a>Testowanie 

Twoja aplikacja jest teraz gotowe do testu. Dostęp zewnętrzny adres URL używany do publikowania Tableau, i zaloguj się jako użytkownik przypisany do obydwu aplikacji.



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o serwerze Proxy aplikacji usługi Azure AD, zobacz [jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md).

