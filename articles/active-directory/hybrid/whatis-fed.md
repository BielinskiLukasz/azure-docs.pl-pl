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
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7a5b186ebc98babe1baacc9f56f39dc8fbc093
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049374"
---
# <a name="what-is-federation-with-azure-ad"></a>Co to jest federacja w usłudze Azure AD Connect?

Federacja to kolekcja domen, które mają ustanowioną relację zaufania. Poziom zaufania może się różnić — zwykle obejmuje uwierzytelnianie i niemal zawsze autoryzację. Typowa federacja może obejmować kilka organizacji, które mają ustanowioną relację zaufania na potrzeby dostępu współdzielonego do zestawu zasobów.

Można sfederować środowisko lokalne z usługą Azure AD i użyć tej federacji do celów uwierzytelniania i autoryzacji.  Dzięki tej metodzie logowania cały proces uwierzytelniania użytkowników odbywa się lokalnie.  Ta metoda umożliwia administratorom wdrożenie bardziej rygorystycznych poziomów kontroli dostępu. Dostępne są federacje w usługach AD FS i PingFederate.

![Tożsamość federacyjna](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Jeśli zdecydujesz się na użycie federacji z usługami Active Directory Federation Services (AD FS), możesz opcjonalnie skonfigurować synchronizację skrótów haseł jako kopię zapasową na wypadek awarii infrastruktury usług AD FS.


## <a name="next-steps"></a>Następne kroki

- [Co to jest tożsamość hybrydowa?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Co to jest usługa Azure AD Connect i connect health?](whatis-azure-ad-connect.md)
- [Co to jest synchronizacja skrótów haseł?](whatis-phs.md)
- [Co to jest federacja?](whatis-fed.md)
- [Co to jest logowanie jednokrotne?](how-to-connect-sso.md)
- [Jak działa federacja](how-to-connect-fed-whatis.md)
- [Federacja z serwerem PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
