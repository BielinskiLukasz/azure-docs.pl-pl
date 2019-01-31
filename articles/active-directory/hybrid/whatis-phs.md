---
title: Co to jest synchronizacja skrótów haseł w usłudze Azure AD? | Microsoft Docs
description: Opisuje synchronizację skrótów haseł.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: d11759fbc3354364af3f78599ff9cfb25674c612
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175154"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Co to jest synchronizacja skrótów haseł w usłudze Azure AD?
Synchronizacja skrótów haseł to jedna z metod logowania używana do zapewnienia tożsamości hybrydowej. Program Azure AD Connect synchronizuje skróty haseł użytkowników z lokalnego wystąpienia usługi Active Directory do wystąpienia usługi Azure AD w chmurze.

Synchronizacja skrótów haseł to rozszerzenie funkcji synchronizacji katalogów wdrożonej przez synchronizację programu Azure AD Connect. Dzięki tej funkcji możesz logować się w usługach Azure AD, na przykład Office 365. Aby zalogować w usłudze, użyj tego samego hasła, którego używasz do zalogowania się w lokalnym wystąpieniu usługi Active Directory.

![Co to jest program Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

Synchronizacja skrótów haseł pomaga, ponieważ dzięki niej użytkownicy potrzebują tylko jednego hasła. Synchronizacja skrótów haseł umożliwia:

* Zwiększenie produktywność użytkowników.
* Obniżenie kosztów pomocy technicznej.  

Jeśli zdecydujesz się na użycie [federacji z usługami Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) jako metody logowania, możesz opcjonalnie skonfigurować synchronizację skrótów haseł jako awaryjną metodę logowania.

Aby użyć synchronizacji skrótów haseł w swoim środowisku, musisz:

* Zainstalować program Azure AD Connect.  
* Skonfigurować synchronizację katalogów pomiędzy lokalnym wystąpieniem usługi Active Directory a wystąpieniem usługi Azure Active Directory.
* Włączyć synchronizację skrótów haseł.



Aby uzyskać więcej informacji, zobacz [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Następne kroki

- [Co to jest tożsamość hybrydowa?](whatis-phs.md)
- [Co to są programy Azure AD Connect i Connect Health?](whatis-azure-ad-connect.md)
- [Co to jest uwierzytelnianie przekazywane?](how-to-connect-pta.md)
- [Co to jest federacja?](whatis-fed.md)
- [Co to jest logowanie jednokrotne?](how-to-connect-sso.md)
- [How Password hash synchronization works (Jak działa synchronizacja skrótów haseł)](how-to-connect-password-hash-synchronization.md)
