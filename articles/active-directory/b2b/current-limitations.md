---
title: Ograniczenia współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Bieżące ograniczenia współpracy B2B usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bf4d1d0f510ccad614452db74c291f6c61dcf54
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672308"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Ograniczenia współpracy B2B usługi Azure AD
Współpraca z usługą Azure Active Directory (Azure AD) B2B jest obecnie dostępna z zastrzeżeniem ograniczeń, opisane w tym artykule.

## <a name="possible-double-multi-factor-authentication"></a>Możliwe double uwierzytelnianie wieloskładnikowe
Dzięki usłudze Azure AD B2B można wymusić uwierzytelnianie wieloskładnikowe w organizacji zasobów (organizacji zapraszającej). Przyczyny tego podejścia są szczegółowo opisane w [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md). Jeśli partner ma już uwierzytelnianie wieloskładnikowe, konfigurowanie i wymuszane, użytkownicy może być konieczne przeprowadzenie uwierzytelniania raz w swojej organizacji macierzystej a następnie ponownie w należy do Ciebie.

## <a name="instant-on"></a>Natychmiastowa
W przepływach współpracy B2B możemy dodać użytkowników do katalogu i dynamicznie aktualizować podczas realizacja zaproszenia, przypisywania aplikacji i tak dalej. Aktualizacje i zapisy zwykle odbywa się w wystąpieniu jednego katalogu i musi zostać zreplikowana we wszystkich wystąpieniach. Replikacja jest wykonywana po wszystkich wystąpień są aktualizowane. Czasami w przypadku, gdy obiekt są zapisywane lub aktualizowane w jednym wystąpieniu i wywołanie do pobrania tego obiektu jest do innego wystąpienia, może wystąpić opóźnienia w replikacji. Jeśli tak się stanie, należy odświeżyć lub ponów próbę, aby pomóc. Jeśli piszesz aplikację przy użyciu naszego interfejsu API ponownych prób przy użyciu niektórych wycofywania jest rozwiązaniem dobre, obrony w celu złagodzenie tego problemu.

## <a name="azure-ad-directories"></a>Katalogi usługi Azure AD
Usługa Azure AD B2B jest podlegają usługi Azure AD directory limitów usług. Szczegółowe informacje na temat liczby katalogów, użytkownik może utworzyć i liczba katalogów do których użytkownik lub użytkownik-Gość może należą, zobacz [usługi Azure AD, ograniczenia i limity](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Delegowanie zaproszeń współpracy B2B](delegate-invitations.md)

