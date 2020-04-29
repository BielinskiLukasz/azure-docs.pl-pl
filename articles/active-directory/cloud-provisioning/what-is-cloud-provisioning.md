---
title: Co to jest Azure AD Connect aprowizacji w chmurze. | Microsoft Docs
description: Opisuje Azure AD Connect aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a86d34fca9a88b0df601533a0f3de1cc97ad1a2f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80050595"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Co to jest aprowizacja w chmurze programu Azure AD Connect?
Azure AD Connect aprowizacji w chmurze to nowy Agent firmy Microsoft, który zaprojektowano w celu spełnienia celów tożsamości hybrydowej dla synchronizacji użytkowników, grup i kontaktów z usługą Azure AD.  Można go używać razem z Azure AD Connect synchronizacją i zapewnia następujące korzyści:
    
- Obsługa synchronizowania z dzierżawą usługi Azure AD z wielu lasów odłączonych Active Directory środowiska lasu: typowe scenariusze obejmują proces scalania & pozyskiwania, gdzie lasy usługi AD nabyte w firmie są odizolowane od lasów i firm DOMENOWYCH w firmie nadrzędnej, w których historycznie istniały wiele lasów usługi AD.
- Uproszczona instalacja z użyciem lekkich agentów aprowizacji: agenci działają jako most z usługi AD do usługi Azure AD, ze wszystkimi konfiguracją synchronizacji zarządzaną w chmurze. 
- Wielu agentów aprowizacji można użyć do uproszczenia wdrożeń o wysokiej dostępności, szczególnie krytycznych dla organizacji korzystających z synchronizacji skrótów haseł z usługi AD do usługi Azure AD.


![Co to jest program Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Jak Azure AD Connect aprowizacji w chmurze różni się od synchronizacji Azure AD Connect?
Dzięki Azure AD Connect aprowizacji w chmurze Inicjowanie obsługi administracyjnej z usługi AD do usługi Azure AD jest zorganizowane w usługach online firmy Microsoft. Organizacja musi tylko wdrożyć w środowisku lokalnym i IaaS, lekki Agent, który działa jako Most między usługą Azure AD i usługą AD. Konfiguracja aprowizacji jest przechowywana w usłudze Azure AD i zarządzana jako część usługi.

Poniższa tabela zawiera porównanie między Azure AD Connect i Azure AD Connect aprowizacji w chmurze:

| Funkcja | Synchronizacja Azure Active Directory Connect| Azure Active Directory Connect aprowizacji w chmurze |
|:--- |:---:|:---:|
|Połączenie z pojedynczym lokalnym lasem usługi AD|● |● |
| Połączenie z wieloma lokalnymi lasami usługi AD |● |● |
| Nawiązywanie połączenia z wieloma odłączonymi lokalnymi lasami usługi AD | |● |
| Model instalacji uproszczonego agenta | |● |
| Wielu aktywnych agentów w celu zapewnienia wysokiej dostępności | |● |
| Łączenie z katalogami LDAP|●| | 
| Obsługa obiektów użytkownika |● |● |
| Obsługa obiektów grupy |● |● |
| Obsługa obiektów kontaktów |● |● |
| Obsługa obiektów urządzeń |● | |
| Zezwalaj na podstawowe dostosowywanie przepływów atrybutów |● |● |
| Sychronize atrybuty usługi Exchange Online |● |● |
| Synchronizuj atrybuty rozszerzenia 1-15 |● |● |
| Synchronizowanie atrybutów usługi AD zdefiniowanych przez klienta (rozszerzenia katalogów) |● | |
| Obsługa synchronizacji skrótów haseł |●|●|
| Obsługa uwierzytelniania przekazywanego |●||
| Obsługa federacji |●|●|
| Bezproblemowe logowanie jednokrotne|● |●|
| Obsługuje instalację na kontrolerze domeny |● |● |
| Obsługa systemu Windows Server 2012 i Windows Server 2012 R2 |● |● |
| Filtrowanie według domen/jednostek organizacyjnych/grup |● |● |
| Filtrowanie wartości atrybutów obiektów |● | |
| Umożliwia synchronizowanie minimalnego zestawu atrybutów (MinSync) |● |● |
| Umożliwia usuwanie atrybutów z przepływu z usługi AD do usługi Azure AD |● |● |
| Umożliwia zaawansowane dostosowywanie przepływów atrybutów |● | |
| Obsługa zapisywania zwrotnego (hasła, urządzenia, grupy) |● | |
| Obsługa Azure AD Domain Services|● | |
| [Stornowanie hybrydowe programu Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Obsługa ponad 50 000 obiektów na domenę usługi AD |● | |

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Zainstaluj aprowizacji w chmurze](how-to-install.md)
