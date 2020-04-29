---
title: Zarządzanie jednostkami administracyjnymi (wersja zapoznawcza) — Azure AD | Microsoft Docs
description: Korzystanie z jednostek administracyjnych na potrzeby bardziej szczegółowego delegowania uprawnień w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406456"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Zarządzanie jednostkami administracyjnymi w Azure Active Directory (wersja zapoznawcza)

W tym artykule opisano jednostki administracyjne w Azure Active Directory (Azure AD). Jednostka administracyjna to zasób usługi Azure AD, który może być kontenerem dla innych zasobów usługi Azure AD. W tej wersji zapoznawczej jednostka administracyjna może zawierać tylko użytkowników i grupy.

Jednostki administracyjne umożliwiają Przyznawanie uprawnień administratora, które są ograniczone do działu, regionu lub innego segmentu zdefiniowanej organizacji. Jednostki administracyjne mogą służyć do delegowania uprawnień do administratorów regionalnych lub do ustawiania zasad na poziomie szczegółowym. Administrator konta użytkownika może na przykład zaktualizować informacje o profilu, zresetować hasła i przypisać licencje dla użytkowników tylko w ich jednostce administracyjnej.

 Na przykład delegowanie do specjalistów pomocy technicznej regionalnej rola [administratora punktu](directory-assign-admin-roles.md#helpdesk-administrator) pomocy jest ograniczona do zarządzania tylko użytkownikami w regionie, w którym są one obsługiwane.

## <a name="deployment-scenario"></a>Scenariusz wdrożenia

Ograniczanie zakresu administracyjnego przy użyciu jednostek administracyjnych może być przydatne w organizacjach, które składają się z niezależnych oddziałów dowolnego rodzaju. Rozważmy przykład dużej uczelni, która składa się z wielu szkół autonomicznych (szkoły, szkoły inżynieryjnej i tak dalej), że każdy z nich ma zespół administratorów IT kontrolujący dostęp, zarządzanie użytkownikami i Ustawianie zasad dla swojej szkoły. Administrator centralny może:

- Utwórz rolę z uprawnieniami administracyjnymi tylko dla użytkowników usługi Azure AD w jednostce administracyjnej szkoły biznesowej
- Tworzenie jednostki administracyjnej dla szkoły biznesowej
- Wypełnij jednostkę administracyjną wyłącznie uczniami i personelem szkoły szkolnej
- Dodawanie zespołu służbowego IT do roli z ich zakresem

## <a name="license-requirements"></a>Wymagania licencyjne

Korzystanie z jednostek administracyjnych wymaga Azure Active Directory — wersja Premium licencji dla każdego administratora jednostki administracyjnej i Azure Active Directory — wersja Bezpłatna licencji dla członków jednostki administracyjnej. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Zarządzanie jednostkami administracyjnymi

W tej wersji zapoznawczej można zarządzać jednostkami administracyjnymi przy użyciu Azure Portal, poleceń cmdlet programu PowerShell i skryptów lub Microsoft Graph. Aby uzyskać szczegółowe informacje, możesz zapoznać się z naszą dokumentacją:

- [Twórz, usuwaj, wypełniaj i dodawaj role do jednostek administracyjnych](roles-admin-units-manage.md): wykonaj procedury opisane w procedurach
- [Praca z jednostkami](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)administracyjnymi: jak pracować z jednostkami administracyjnymi przy użyciu programu PowerShell
- [Obsługa grafu jednostki administracyjnej](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): szczegółowa dokumentacja dotycząca Microsoft Graph dla jednostek administracyjnych.

### <a name="planning-your-administrative-units"></a>Planowanie jednostek administracyjnych

Jednostki administracyjne mogą służyć do logicznego grupowania zasobów usługi Azure AD. Na przykład w przypadku organizacji, której dział IT jest rozproszeni globalnie, warto utworzyć jednostki administracyjne, które definiują te granice geograficzne. W innym scenariuszu, w którym organizacja wielonarodowy ma różne "podorganizacje", które są częściowo autonomiczne w operacjach, każda podorganizacja może być reprezentowana przez jednostkę administracyjną.

Kryteria, w których są tworzone jednostki administracyjne, będą prowadzone zgodnie z unikatowymi wymaganiami organizacji. Jednostki administracyjne są typowym sposobem definiowania struktury w ramach usług M365 Services. Zalecamy przygotowanie jednostek administracyjnych do korzystania z usług M365 na uwadze. Możesz uzyskać maksymalną wartość z jednostek administracyjnych, gdy można kojarzyć wspólne zasoby w M365 w jednostce administracyjnej.

Aby utworzyć jednostki administracyjne w organizacji, możesz przejść przez następujące etapy:

1. Początkowe wdrożenie: organizacja rozpocznie tworzenie jednostek administracyjnych na podstawie początkowych kryteriów, a liczba jednostek administracyjnych zwiększy się, gdy kryteria zostaną ulepszone.
1. Oczyszczanie: po określeniu kryterium jednostki administracyjne, które nie są już wymagane, zostaną usunięte.
1. Stabilizacja: Struktura organizacyjna jest dobrze zdefiniowana, a liczba jednostek administracyjnych nie zmienia się znacząco w krótkim czasie trwania.

## <a name="currently-supported-scenarios"></a>Obecnie obsługiwane scenariusze

Administratorzy globalni lub administrator ról uprzywilejowanych mogą używać portalu usługi Azure AD do tworzenia jednostek administracyjnych, dodawać użytkowników jako członków jednostek administracyjnych, a następnie przypisywać personelowi IT do ról administratora z zakresem jednostek administracyjnych. Administratorzy z zakresem administracyjnym mogą następnie użyć portalu Office 365 do podstawowego zarządzania użytkownikami w ich jednostkach administracyjnych.

Ponadto grupy mogą być dodawane jako członkowie jednostki administracyjnej, a administrator grupy z zakresem jednostki administratora może zarządzać nimi przy użyciu programu PowerShell, Microsoft Graph i portalu usługi Azure AD.

W poniższej tabeli opisano bieżące wsparcie dla scenariuszy jednostek administracyjnych.

### <a name="administrative-unit-management"></a>Zarządzanie jednostkami administracyjnymi

Uprawnienia |   Program MS Graph/program PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Tworzenie i usuwanie jednostek administracyjnych   |    Obsługiwane    |   Obsługiwane   |    Nieobsługiwane
Dodawanie i usuwanie członków jednostki administracyjnej osobno    |   Obsługiwane    |   Obsługiwane   |    Nieobsługiwane
Zbiorcze dodawanie i usuwanie członków jednostek administracyjnych przy użyciu pliku CSV   |    Nieobsługiwane     |  Obsługiwane   |    Brak planu do obsługi
Przypisywanie administratorów z zakresem administracyjnym  |     Obsługiwane    |   Obsługiwane    |   Nieobsługiwane
Dynamiczne dodawanie i usuwanie członków funkcji AU na podstawie atrybutów | Nieobsługiwane | Nieobsługiwane | Nieobsługiwane

### <a name="user-management"></a>Zarządzanie użytkownikami

Uprawnienia |   Program MS Graph/program PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Zarządzanie właściwościami użytkownika, hasłami i licencjami w zakresie jednostki administracyjnej   |    Obsługiwane     |  Obsługiwane   |   Obsługiwane
Blokowanie w zakresie jednostki administracyjnej i odblokowywanie logowania użytkowników    |   Obsługiwane   |    Obsługiwane   |    Obsługiwane
Zarządzanie poświadczeniami usługi MFA użytkownika w zakresie jednostki administracyjnej   |    Obsługiwane   |   Obsługiwane   |   Nieobsługiwane

### <a name="group-management"></a>Zarządzanie grupami

Uprawnienia |   Program MS Graph/program PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Zarządzanie właściwościami grupy i członkami w zakresie jednostki administracyjnej     |  Obsługiwane   |    Obsługiwane    |  Nieobsługiwane
Zarządzanie licencjonowaniem grupowym w zakresie jednostki administracyjnej   |    Obsługiwane  |    Obsługiwane   |   Nieobsługiwane

> [!NOTE]
>
> Administratorzy z zakresem jednostki administracyjnej nie mogą zarządzać regułami dynamicznego członkostwa w grupach.

Jednostki administracyjne stosują zakres tylko do uprawnień zarządzania. Nie uniemożliwiają członkom ani administratorom korzystania z ich [domyślnych uprawnień użytkownika](../fundamentals/users-default-permissions.md) do przeglądania innych użytkowników, grup lub zasobów poza jednostką administracyjną. W portalu pakietu Office 365 użytkownicy spoza jednostek administracyjnych administratora zakresu są odfiltrowani, ale można przeglądać innych użytkowników w portalu usługi Azure AD, programie PowerShell i innych usługach firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie nimi](roles-admin-units-manage.md)
- [Zarządzanie użytkownikami w programie Australia](roles-admin-units-add-manage-users.md)
- [Zarządzanie grupami w programie Australia](roles-admin-units-add-manage-groups.md)
- [Przypisywanie ról objętych zakresem do funkcji AU](roles-admin-units-assign-roles.md)