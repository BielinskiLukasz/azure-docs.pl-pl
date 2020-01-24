---
title: Konfigurowanie punktów końcowych | Microsoft Docs
description: Jak znaleźć punkty końcowe uwierzytelniania dla aplikacji niestandardowej, którą tworzysz lub rejestrujesz w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 5dbe9d6eed6c546af0ed2cb35a0a62e9943503a9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702797"
---
# <a name="how-to-configure-endpoints"></a>Jak skonfigurować punkty końcowe

Punkty końcowe uwierzytelniania dla aplikacji można znaleźć w [Azure Portal](https://portal.azure.com).

-   Przejdź do witryny [Azure Portal](https://portal.azure.com).

-   W okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.

-   Kliknij pozycję **rejestracje aplikacji** i wybierz **punkty końcowe**.

-   Spowoduje to otwarcie strony **punkty końcowe** , w której znajduje się lista wszystkich punktów końcowych uwierzytelniania dla dzierżawy.

-   Użyj punktu końcowego specyficznego dla używanego protokołu uwierzytelniania, w połączeniu z IDENTYFIKATORem aplikacji, aby wysłać żądanie uwierzytelniania specyficzne dla aplikacji.

## <a name="next-steps"></a>Następne kroki
[Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
