---
title: Mapowanie — oświadczeń użytkowników współpracy B2B, usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dostosowywanie oświadczeń użytkowników, które są wydawane w tokenie SAML dla użytkowników B2B usługi Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 445d6c8b527b56f63a1156a39ac07393b4ffe115
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668551"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Użytkownik współpracy B2B Mapowanie oświadczeń w usłudze Azure Active Directory

Dostosowywanie oświadczeń wystawionych w tokenie SAML dla użytkowników współpracy B2B usługi Azure obsługuje usługi Active Directory (Azure AD). Jeśli użytkownik uwierzytelnia się do aplikacji, usługi Azure AD wystawia SAML token do aplikacji, która zawiera informacje (lub oświadczenia) o użytkowniku, który unikatowo identyfikuje je. Domyślnie w tym nazwę użytkownika, adres e-mail, imię i nazwisko użytkownika.

W [witryny Azure portal](https://portal.azure.com), można wyświetlić lub edytować oświadczenia, które są wysyłane w tokenie języka SAML do aplikacji. Aby uzyskać dostęp do ustawień, wybierz pozycję **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > aplikacji, który skonfigurowano na potrzeby logowania jednokrotnego > **logowanie jednokrotne** . Informacje o ustawieniach tokenu języka SAML w **atrybutów użytkownika** sekcji.

![Zawiera atrybuty tokenu języka SAML w Interfejsie użytkownika](media/claims-mapping/view-claims-in-saml-token.png)

Istnieją dwie możliwe przyczyny, dlaczego może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:

1. Aplikacja wymaga innego zestawu identyfikatorów URI lub wartości oświadczeń.

2. Aplikacja wymaga NameIdentifier roszczenia jako coś innego niż nazwę główną użytkownika (UPN), który jest przechowywany w usłudze Azure AD.

Aby dowiedzieć się, jak dodawać i edytować oświadczeń, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Współpracę B2B dzięki użytkowników, mapowanie międzydzierżawowa NameID i głównej nazwy użytkownika nie będą mogły ze względów bezpieczeństwa.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacji na temat właściwości użytkowników współpracy B2B, zobacz [właściwości użytkownika współpracy B2B usługi Azure Active Directory](user-properties.md).
- Aby uzyskać informacji na temat tokeny użytkowników dla użytkowników współpracy B2B, zobacz [zrozumieć tokeny użytkowników we współpracy B2B usługi Azure AD](user-token.md).

