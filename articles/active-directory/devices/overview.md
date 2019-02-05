---
title: Co to jest zarządzanie urządzeniami w usłudze Azure Active Directory? | Microsoft Docs
description: Dowiedz się, jak zarządzanie urządzeniami może pomóc w sprawowaniu kontroli nad urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/25/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 704f854d7be9930923cb6e1955c3670f296035ed
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299830"
---
# <a name="what-is-device-management-in-azure-active-directory"></a>Co to jest zarządzanie urządzeniami w usłudze Azure Active Directory?

W świecie zdominowanym przez urządzenia przenośne i rozwiązania chmurowe usługa Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne na urządzeniach oraz w usługach i aplikacjach z dowolnego miejsca. Powszechne stosowanie urządzeń — w tym modelu Przynieś własne urządzenie (BYOD, Bring Your Own Device) — stawia dwa przeciwstawne cele przed specjalistami IT:

- Umożliwienie użytkownikom końcowym produktywnej pracy w dowolnym czasie i miejscu.
- Ochrona zasobów firmowych przez cały czas.

Za pomocą urządzeń użytkownicy uzyskują dostęp do zasobów firmowych. Aby chronić zasoby firmowe, administrator IT chce mieć kontrolę nad tymi urządzeniami. W ten sposób może zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają standardy dotyczące zabezpieczeń i zgodności. 

Zarządzanie urządzeniami stanowi również podstawę w przypadku [dostępu warunkowego opartego na urządzeniach](../conditional-access/require-managed-devices.md). Korzystając z dostępu warunkowego opartego na urządzeniach, można zagwarantować, że dostęp do zasobów w środowisku jest możliwy tylko przy użyciu urządzeń zarządzanych.   

W tym artykule wyjaśniono, jak działa zarządzanie urządzeniami w usłudze Azure Active Directory.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Rozpoczynanie kontrolowania urządzeń w usłudze Azure AD

Istnieją dwie możliwości rozpoczęcia kontrolowania urządzenia w usłudze Azure AD:

- Zarejestrowanie 
- Dołączenie

**Zarejestrowanie** urządzenia w usłudze Azure AD umożliwia zarządzanie tożsamością urządzenia. Po zarejestrowaniu urządzenia usługa rejestracji urządzeń w usłudze Azure AD zapewnia urządzeniu tożsamość, która jest używana do uwierzytelniania urządzenia podczas logowania użytkownika w usłudze Azure AD. Możesz użyć tożsamości, aby włączyć lub wyłączyć urządzenie.

W przypadku połączenia z rozwiązaniem do zarządzania urządzeniami przenośnymi (MDM, mobile device management), takim jak usługa Microsoft Intune, atrybuty urządzenia w usłudze Azure AD są aktualizowane przy użyciu dodatkowych informacji o urządzeniu. Umożliwia to tworzenie reguł dostępu warunkowego, które wymuszają dostęp z urządzeń spełniających określone standardy zabezpieczeń i zgodności. Aby uzyskać więcej informacji na temat rejestrowania urządzeń w usłudze Microsoft Intune, zobacz artykuł [Co to jest rejestrowanie urządzenia?](https://docs.microsoft.com/intune/device-enrollment)

**Dołączenie** urządzenia jest rozszerzeniem zarejestrowania urządzenia. Oznacza to zapewnienie wszystkich korzyści z rejestrowania urządzenia oraz zmianę stanu lokalnego urządzenia. Zmiana stanu lokalnego umożliwia użytkownikom logowanie do urządzenia przy użyciu konta służbowego w organizacji zamiast konta osobistego.

## <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane w usłudze Azure AD   

Celem stosowania urządzeń zarejestrowanych w usłudze Azure AD jest zapewnianie obsługi scenariusza **Przynieś własne urządzenie (BYOD)**. W tym scenariuszu użytkownik może uzyskać dostęp do zasobów kontrolowanych przez usługę Azure Active Directory w organizacji przy użyciu urządzenia osobistego.  

![Urządzenia zarejestrowane w usłudze Azure AD](./media/overview/03.png)

Dostęp jest oparty na koncie służbowym wprowadzonym na urządzeniu.  
Na przykład system Windows 10 umożliwia użytkownikom dodanie konta służbowego do komputera, tabletu lub telefonu osobistego.  
Gdy użytkownik doda konto służbowe, urządzenie zostanie zarejestrowane w usłudze Azure AD oraz, opcjonalnie, w systemie zarządzania urządzeniami przenośnymi (MDM) skonfigurowanym przez organizację. Użytkownicy organizacji mogą wygodnie dodać konto służbowe do urządzenia osobistego:

- Podczas uzyskiwania dostępu do aplikacji roboczej po raz pierwszy
- Ręcznie za pośrednictwem menu **Ustawienia** w przypadku systemu Windows 10 

Urządzenia zarejestrowane w usłudze Azure AD możesz skonfigurować dla systemów Windows 10, iOS, Android oraz macOS.

## <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Celem urządzeń dołączonych do usługi Azure AD jest uproszczenie następujących kwestii:

- Wdrożenia urządzeń należących do firmy w systemie Windows 
- Uzyskiwanie dostępu do aplikacji i zasobów organizacji z dowolnego urządzenia z systemem Windows
- Oparte na chmurze zarządzanie urządzeniami należącymi do firmy

![Urządzenia zarejestrowane w usłudze Azure AD](./media/overview/02.png)

Dołączenie do usługi Azure AD można wdrożyć przy użyciu dowolnej z następujących metod: 
 - [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [Wdrożenie zbiorcze](https://docs.microsoft.com/intune/windows-bulk-enroll)
 - [Środowisko samoobsługowe](azuread-joined-devices-frx.md) 

**Dołączenie do usługi Azure AD** jest przeznaczone dla organizacji, które chcą głównie używać chmury (tj. głównie używają usług w chmurze, aby zredukować użycie infrastruktury lokalnej) lub które używają wyłącznie chmury (bez infrastruktury lokalnej). Nie ma żadnych ograniczeń w zakresie rozmiaru lub typu organizacji, które mogą wdrożyć dołączenie do usługi Azure AD. Dołączenie do usługi Azure AD działa dobrze nawet w środowiskach hybrydowych, zapewniając dostęp do aplikacji i zasobów przechowywanych w chmurze i lokalnie.

Zaimplementowanie urządzeń dołączonych do usługi Azure AD zapewnia następujące korzyści:

- **Logowanie jednokrotne** do aplikacji i usług SaaS zarządzanych na platformie Azure. Użytkownicy nie zobaczą dodatkowych monitów o uwierzytelnienie podczas uzyskiwania dostępu do zasobów służbowych. Funkcja logowania jednokrotnego jest dostępna nawet wtedy, gdy użytkownicy nie są podłączeni do sieci z domeną.

- **Roaming zgodny z wymaganiami przedsiębiorstwa** dla ustawień użytkowników na dołączonych urządzeniach. Użytkownicy nie muszą łączyć konta Microsoft (np. Hotmail), aby zobaczyć ustawienia na urządzeniach.

- **Dostęp do sklepu Windows Store dla firm** przy użyciu konta usługi Azure AD. Użytkownicy mogą wybrać ze spisu aplikacji wstępnie wybranych przez organizację.

- Obsługa funkcji **Windows Hello** na potrzeby bezpiecznego i wygodnego dostępu do zasobów służbowych.

- **Ograniczenie dostępu** do aplikacji tylko do urządzeń spełniających zasady zgodności.

- **Bezproblemowy dostęp do zasobów lokalnych**, jeżeli urządzenie ma bezpośredni kontakt z kontrolerem domeny w środowisku lokalnym. 


Chociaż dołączanie do usługi Azure AD jest przeznaczone głównie dla organizacji, które nie mają lokalnej infrastruktury usługi Active Directory systemu Windows Server, możesz bez obaw użyć tej funkcji w następujących scenariuszach:

- Chcesz przenieść zasoby do infrastruktury opartej na chmurze przy użyciu usługi Azure AD oraz funkcji zarządzania urządzeniami przenośnymi, np. usługi Intune.

- Nie możesz zastosować dołączania do domeny lokalnej, np. jeśli potrzebujesz kontrolować urządzenia przenośne, takie jak tablety i telefony.

- Użytkownicy przede wszystkim potrzebują dostępu do usługi Office 365 lub innych aplikacji SaaS zintegrowanych z usługą Azure AD.

- Chcesz zarządzać grupą użytkowników w usłudze Azure AD zamiast w usłudze Active Directory. Może to mieć zastosowanie na przykład w przypadku pracowników sezonowych, podwykonawców lub uczniów.

- Chcesz zapewnić możliwość dołączania pracownikom w odległych oddziałach z ograniczoną infrastrukturą lokalną.

Funkcję urządzeń dołączonych do usługi Azure AD możesz skonfigurować w przypadku urządzeń z systemem Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

Przez ponad dekadę wiele organizacji używało przyłączania do domeny lokalnej usługi Active Directory, aby umożliwić wykonywanie następujących czynności:

- Zarządzanie urządzeniami firmowymi z centralnej lokalizacji przez działy IT.

- Logowanie się przez użytkowników na urządzeniach przy użyciu kont służbowych usługi Active Directory. 

Zazwyczaj organizacje z dużą ilością zasobów lokalnych polegają na metodach obrazowania w przypadku aprowizacji urządzeń, a także często używają programu **System Center Configuration Manager (SCCM)** lub **zasad grupy** do zarządzania nimi.

Jeśli środowisko ma lokalną infrastrukturę usługi AD i chcesz również wykorzystać możliwości zapewniane przez usługę Azure Active Directory, możesz wdrożyć urządzenia dołączone hybrydowo do usługi Azure AD. Są to urządzenia dołączone do lokalnej usługi Active Directory oraz zarejestrowane w usłudze Azure Active Directory.

![Urządzenia zarejestrowane w usłudze Azure AD](./media/overview/01.png)


Urządzeń dołączonych hybrydowo do usługi Azure AD należy używać w następujących przypadkach:

- Masz aplikacje Win32 wdrożone na tych urządzeniach, które polegają na uwierzytelnianiu maszynowym usługi Active Directory.

- Potrzebujesz zasad grupy do zarządzania urządzeniami.

- Chcesz nadal używać rozwiązań obrazowania do konfigurowania urządzeń dla pracowników.

Funkcję urządzeń hybrydowo dołączonych do usługi Azure AD możesz skonfigurować dla urządzeń z systemem Windows 10 oraz starszymi systemami, np. Windows 8 i Windows 7.

## <a name="summary"></a>Podsumowanie

Zarządzanie urządzeniami w usłudze Azure AD zapewnia następujące korzyści: 

- Uproszczenie procesu wdrażania kontroli nad urządzeniami w usłudze Azure AD

- Zapewnienie użytkownikom łatwego dostępu do zasobów organizacji opartych na chmurze

Ogólnie omówione typy urządzeń mają następujące zastosowania:

- Urządzenia zarejestrowane w usłudze Azure AD:

    - Urządzenia osobiste 

    - Ręczne rejestrowanie urządzeń w usłudze Azure AD

- Urządzenia dołączone do usługi Azure AD: 

    - Urządzenia, które są własnością organizacji

    - Urządzenia, które **nie** są dołączone do lokalnej usługi AD

    - Ręczne rejestrowanie urządzeń w usłudze Azure AD

    - Zmiana stanu lokalnego urządzenia

- Urządzenia dołączone hybrydowo do usługi Azure AD, jeśli te urządzenia są dołączone do lokalnej usługi AD:     

    - Urządzenia, które są własnością organizacji

    - Urządzenia, które są dołączone do lokalnej usługi AD

    - Automatyczne rejestrowanie urządzeń w usłudze Azure AD

    - Zmiana stanu lokalnego urządzenia



## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure Portal, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md)

- Aby dowiedzieć się więcej o dostępie warunkowym opartym na urządzeniach, zobacz [konfigurowanie zasad dostępu warunkowego opartego na urządzeniach w usłudze Azure Active Directory](../conditional-access/require-managed-devices.md).

- Informacje o konfigurowaniu:
    - Urządzenia z systemem Windows 10 zarejestrowane w usłudze Azure Active Directory — zobacz [sposób konfigurowania urządzeń z systemem Windows 10 zarejestrowanych w usłudze Azure Active Directory](../user-help/device-management-azuread-registered-devices-windows10-setup.md)
    - Urządzenia dołączone do usługi Azure Active Directory — zobacz [sposób konfigurowania urządzeń dołączonych do usługi Azure Active Directory](../user-help/device-management-azuread-joined-devices-setup.md)
    - Urządzenia dołączone hybrydowo do usługi Azure AD — zobacz [Jak planować implementację z dołączaniem hybrydowym do usługi Azure Active Directory](hybrid-azuread-join-plan.md).


