---
title: Co to jest federacja w usłudze Azure AD Connect? | Microsoft Docs
description: Opisuje federację w usłudze Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8794ffa1654e49690f3bd31a380ba2051b4b1da7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177142"
---
# <a name="what-is-federation-with-azure-ad"></a>Co to jest federacja w usłudze Azure AD Connect?

Federacja to kolekcja domen, które mają ustanowioną relację zaufania. Poziom zaufania może się różnić — zwykle obejmuje uwierzytelnianie i niemal zawsze autoryzację. Typowa federacja może obejmować kilka organizacji, które mają ustanowioną relację zaufania na potrzeby dostępu współdzielonego do zestawu zasobów.

Można sfederować środowisko lokalne z usługą Azure AD i użyć tej federacji do celów uwierzytelniania i autoryzacji.  Dzięki tej metodzie logowania cały proces uwierzytelniania użytkowników odbywa się lokalnie.  Ta metoda umożliwia administratorom wdrożenie bardziej rygorystycznych poziomów kontroli dostępu. Dostępne są federacje w usługach AD FS i PingFederate.

![Tożsamość federacyjna](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Jeśli zdecydujesz się na użycie federacji z usługami Active Directory Federation Services (AD FS), możesz opcjonalnie skonfigurować synchronizację skrótów haseł jako kopię zapasową na wypadek awarii infrastruktury usług AD FS.


## <a name="next-steps"></a>Następne kroki

- [Co to jest tożsamość hybrydowa?](whatis-phs.md)
- [Co to są programy Azure AD Connect i Connect Health?](whatis-azure-ad-connect.md)
- [Co to jest synchronizacja skrótów haseł?](whatis-phs.md)
- [Co to jest federacja?](whatis-fed.md)
- [Co to jest logowanie jednokrotne?](how-to-connect-sso.md)
- [How federation works (Jak działa federacja)](how-to-connect-fed-whatis.md)
- [Konfigurowanie federacji z serwerem PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
