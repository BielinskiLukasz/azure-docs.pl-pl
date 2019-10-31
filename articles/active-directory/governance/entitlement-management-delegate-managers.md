---
title: Delegowanie dostępu ładu w celu uzyskania dostępu do menedżerów pakietów w usłudze Azure AD uprawnień zarządzanie — Azure Active Directory
description: Dowiedz się, jak delegować nadzór dostępu od administratorów IT, aby uzyskiwać dostęp do menedżerów pakietów i menedżerów projektów, aby mogli zarządzać samymi prawami dostępu.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174356"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegowanie dostępu ładu w celu uzyskania dostępu do menedżerów pakietów w usłudze Azure AD uprawnień zarządzanie

Aby delegować tworzenie i zarządzanie pakietami dostępu w wykazie, należy dodać użytkowników do roli Menedżer pakietów dostępu. Menedżerowie pakietów muszą znać potrzeby uzyskiwania dostępu do zasobów w katalogu. Na przykład jeśli katalog jest używany w projekcie, potencjalnym liderem projektu może być Menedżer pakietów dostępu dla tego wykazu.  Menedżerowie pakietów dostępu nie mogą dodawać zasobów do wykazu, ale mogą zarządzać pakietami i zasadami dostępu w wykazie.  W przypadku delegowania do Menedżera pakietów programu Access osoba ta może być odpowiedzialna za:

- Jakie role użytkownicy będą musieli dysponować zasobami w wykazie
- Kto będzie potrzebować dostępu
- Kto musi zatwierdzić żądania dostępu
- Jak długo projekt będzie ostatni

Ten film wideo zawiera omówienie sposobów delegowania dostępu ładu z wykazu do Menedżera pakietów.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Jako właściciel katalogu delegowanie do Menedżera pakietów dostępu

Wykonaj następujące kroki, aby przypisać użytkownika do roli menedżera pakietów dostępu:

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **wykazy** , a następnie otwórz wykaz, do którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij pozycję **role i Administratorzy**.

    ![Role i Administratorzy katalogu](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kliknij pozycję **Dodaj menedżerów pakietów dostępu** , aby wybrać członków dla tych ról.

1. Kliknij pozycję **Wybierz** , aby dodać tych członków.

## <a name="remove-an-access-package-manager"></a>Usuwanie Menedżera pakietów programu Access

Wykonaj następujące kroki, aby usunąć użytkownika z roli menedżera pakietów dostępu:

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika lub właściciel katalogu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **wykazy** , a następnie otwórz wykaz, do którego chcesz dodać administratorów.

1. W menu po lewej stronie kliknij pozycję **role i Administratorzy**.

1. Dodaj znacznik wyboru obok Menedżera pakietów programu Access, który chcesz usunąć.

1. Kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- [Utwórz nowy pakiet dostępu](entitlement-management-access-package-create.md)
