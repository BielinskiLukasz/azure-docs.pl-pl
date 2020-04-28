---
title: Co to są zarejestrowane urządzenia usługi Azure AD?
description: Dowiedz się, jak zarządzanie tożsamościami urządzeń może ułatwić zarządzanie urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67462761"
---
# <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane w usłudze Azure AD

Celem zarejestrowanych urządzeń usługi Azure AD jest zapewnienie użytkownikom pomocy technicznej na potrzeby scenariuszy przynoszenia własnych urządzeń (BYOD) lub urządzeń przenośnych. W tych scenariuszach użytkownik może uzyskać dostęp do Azure Active Directory kontrolowanych zasobów organizacji przy użyciu urządzenia osobistego.

|   | Zarejestrowane usługi Azure AD |
| --- | --- |
| **Definicja** | Zarejestrowano w usłudze Azure AD bez konieczności logowania się na urządzeniu przy użyciu konta organizacyjnego |
| **Odbiorcy podstawowe** | Dotyczy wszystkich użytkowników z następującymi kryteriami: |
|   | „Przynieś własne urządzenie” (BYOD) |
|   | Urządzenia przenośne |
| **Własność urządzeń** | Użytkownik lub organizacja |
| **Systemy operacyjne** | Windows 10, iOS, Android i MacOS |
| **Inicjowanie obsługi** | Windows 10 — ustawienia |
|   | iOS/Android — aplikacja Portal firmy lub Microsoft Authenticator |
|   | MacOS — Portal firmy |
| **Opcje logowania urządzenia** | Poświadczenia lokalne użytkownika końcowego |
|   | Hasło |
|   | Windows Hello |
|   | Kod PIN |
|   | Biometria lub wzorzec dla innych urządzeń |
| **Zarządzanie urządzeniami** | Zarządzanie urządzeniami przenośnymi (przykład: Microsoft Intune) |
|   | zarządzanie aplikacjami mobilnymi |
| **Najważniejsze możliwości** | Logowanie jednokrotne do zasobów w chmurze |
|   | Dostęp warunkowy po zarejestrowaniu w usłudze Intune |
|   | Dostęp warunkowy za pomocą zasad ochrony aplikacji |
|   | Włącza logowanie za pomocą telefonu przy użyciu aplikacji Microsoft Authenticator |

![Urządzenia zarejestrowane w usłudze Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Zarejestrowane urządzenia usługi Azure AD są zalogowane do korzystania z konta lokalnego, takiego jak konto Microsoft na urządzeniu z systemem Windows 10, ale dodatkowo załączono konto usługi Azure AD w celu uzyskania dostępu do zasobów organizacji. Dostęp do zasobów w organizacji może być dodatkowo ograniczony w zależności od tego, czy konto usługi Azure AD i zasady dostępu warunkowego są stosowane do tożsamości urządzenia.

Administratorzy mogą bezpiecznie i bardziej kontrolować te zarejestrowane urządzenia usługi Azure AD przy użyciu narzędzi do zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune. Zarządzanie urządzeniami przenośnymi umożliwia wymuszenie konfiguracji wymaganych przez organizację, takich jak wymaganie zaszyfrowania magazynu, złożoność hasła i aktualizowanie oprogramowania zabezpieczającego. 

Rejestrację usługi Azure AD można wykonać podczas pierwszego uzyskiwania dostępu do aplikacji służbowej lub ręcznie przy użyciu menu Ustawienia systemu Windows 10. 

## <a name="scenarios"></a>Scenariusze

Użytkownik w organizacji chce uzyskiwać dostęp do narzędzi do obsługi poczty e-mail, raportowania czasu wolnego i uzyskiwania korzyści z rejestracji z komputera domowego. Organizacja zawiera te narzędzia za zasadami dostępu warunkowego, które wymagają dostępu z urządzenia zgodnego z usługą Intune. Użytkownik dodaje konto organizacji i rejestruje swój komputer domowy w usłudze Azure AD, a wymagane zasady usługi Intune są wymuszane, dając użytkownikowi dostęp do swoich zasobów.

Inny użytkownik chce uzyskać dostęp do swojej organizacyjnej poczty e-mail na swoim osobistym telefonie z systemem Android, który został odblokowany. Firma wymaga zgodnego urządzenia i utworzyła zasady zgodności usługi Intune w celu zablokowania urządzeń z odblokowanym dostępem. Pracownik nie jest w stanie uzyskać dostępu do zasobów organizacji na tym urządzeniu.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami urządzeń przy użyciu Azure Portal](device-management-azure-portal.md)
- [Zarządzanie nieaktywnymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
