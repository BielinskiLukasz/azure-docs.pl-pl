---
title: Monit o Nieoczekiwane wyrażenie zgody podczas logowania się do aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z po użytkownik zobaczy monit o wyrażenie zgody dla aplikacji, które mają zintegrowany z usługą Azure AD, który użytkownik nie oczekiwano elementu
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 760efde967ca9ca65e517d01da0016ce49f1502c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477873"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Monit o Nieoczekiwane wyrażenie zgody podczas logowania się do aplikacji

Wiele aplikacji, które integrują się z usługą Azure Active Directory wymaga uprawnień do różnych zasobów, aby można było uruchomić. Podczas tych zasobów są także zintegrowana z usługą Azure Active Directory, uprawnienia dostępu do nich jest wymagany przy użyciu usługi Azure AD platformy wyrażania zgody. 

Skutkuje to zgody monit jest wyświetlany przy pierwszym uruchomieniu aplikacji jest używany, co ma często miejsce to jednorazowa operacja. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenariusze, w których użytkownicy widzą zgody monity

Dodatkowe monity można spodziewać się w różnych scenariuszach:

* Zmieniono zestaw uprawnień wymaganych przez aplikację.

* Administrator nie jest użytkownik, który pierwotnie wyraża zgodę na aplikację, a teraz różne (użytkownik niebędący administratorem) używa aplikacji po raz pierwszy.

* Użytkownik, który pierwotnie wyraża zgodę na aplikację została administratora, ale nie wyraził zgody w imieniu całej organizacji.

* Aplikacja używa [zgody przyrostowych i dynamiczne](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) celu zażądania dodatkowych uprawnień po początkowym przyznaniu zgody. Jest to często używane, gdy funkcje opcjonalne dodatkowych aplikacji wymaga uprawnień poza tymi wymagane do obsługi funkcji linii bazowej.

* Zgoda został odwołany po początkowym zostanie im przyznany.

* Deweloper skonfigurował aplikacji, aby wymagać monit o zgodę za każdym razem, gdy jest używany (Uwaga: to nie jest zalecane).

## <a name="next-steps"></a>Kolejne kroki

-   [Aplikacje, uprawnienia i zgody w usłudze Azure Active Directory (punkt końcowy w wersji 1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Zakresy, uprawnienia i zgody w usłudze Azure Active Directory (punktu końcowego v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


