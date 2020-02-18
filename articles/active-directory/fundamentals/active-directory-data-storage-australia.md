---
title: Magazyn danych tożsamości dla klientów Australii i Nowej Zelandii — Azure AD
description: Informacje o tym, gdzie Azure Active Directory są przechowywane dane dotyczące tożsamości dla klientów Australii.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370296"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Magazyn danych tożsamości dla klientów Australii i Nowej Zelandii w Azure Active Directory

Dane tożsamości są przechowywane w usłudze Azure AD w lokalizacji geograficznej na podstawie adresu dostarczonego przez organizację w przypadku subskrybowania usługi online firmy Microsoft, takiej jak Office 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane klienta tożsamości, możesz użyć sekcji [gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Centrum zaufania Microsoft.

> [!NOTE]
> Usługi i aplikacje, które integrują się z usługą Azure AD, mają dostęp do tożsamości klienta danych. Oceń poszczególne usługi i aplikacje, których używasz, aby określić, jak dane klienta tożsamości są przetwarzane przez daną usługę i aplikację oraz czy spełniają wymagania dotyczące magazynu danych w firmie. Aby uzyskać więcej informacji o miejscu zamieszkania usług firmy Microsoft, zobacz sekcję gdzie znajdują się dane? w centrum zaufania firmy Microsoft.

W przypadku klientów, którzy podały adres w Australii lub Nowej Zelandii, usługa Azure AD przechowuje dane tożsamości dla tych usług w australijskich centrach danych: 
- Zarządzanie katalogami usługi Azure AD 
- Uwierzytelnianie

Wszystkie inne usługi Azure AD Services przechowują dane klienta w globalnych centrach danych. Aby zlokalizować centrum danych dla usługi, zobacz [Azure Active Directory — gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure uwierzytelnianie wieloskładnikowe (MFA)

Usługi MFA przechowują dane klienta tożsamości w globalnych centrach danych. Aby dowiedzieć się więcej na temat informacji o użytkowniku zebranych i przechowywanych przez usługę Azure MFA opartą na chmurze i serwer usługi Azure MFA, zobacz [zbieranie danych użytkowników w usłudze azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat funkcji i funkcji opisanych powyżej, zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
