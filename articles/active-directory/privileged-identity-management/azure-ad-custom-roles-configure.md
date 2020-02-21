---
title: Konfigurowanie roli niestandardowej usługi Azure AD — Privileged Identity Management (PIM)
description: Jak skonfigurować role niestandardowe usługi Azure AD w Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f087ffe824a198c578f076857cca7fdf0f0a60c
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498680"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Konfigurowanie ról niestandardowych usługi Azure AD w Privileged Identity Management

Administrator ról uprzywilejowanych może zmienić ustawienia roli, które mają zastosowanie do użytkownika, gdy aktywują przypisanie do roli niestandardowej i dla innych administratorów aplikacji, którzy przypisują role niestandardowe.

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogu w wersji zapoznawczej. Gdy funkcja jest ogólnie dostępna, zarządzanie rolami będzie odbywać się w interfejsie wbudowanym. Jeśli zobaczysz poniższy transparent, te role powinny być zarządzane [przy użyciu wbudowanej roli środowisko](pim-how-to-activate-role.md) i ten artykuł nie ma zastosowania:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli usługi Azure AD.

1. Zaloguj się do [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) w Azure Portal przy użyciu konta użytkownika przypisanego do roli administrator ról uprzywilejowanych.
1. Wybierz **role niestandardowe usługi Azure AD (wersja zapoznawcza)** .

    ![Wybieranie ról niestandardowych usługi Azure AD w wersji zapoznawczej, aby zobaczyć kwalifikujące się przypisania ról](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Wybierz pozycję **ustawienie** , aby otworzyć stronę **Ustawienia** . Wybierz rolę dla ustawień, które chcesz skonfigurować.
1. Wybierz pozycję **Edytuj** , aby otworzyć stronę **Ustawienia roli** .

    ![Otwórz rolę niestandardową usługi Azure AD, aby edytować ustawienia](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Ustawienia roli

Istnieje kilka ustawień, które można skonfigurować.

### <a name="assignment-duration"></a>Czas trwania przypisywania

Można wybrać jedną z dwóch opcji czasu trwania przypisania dla każdego typu przypisania (kwalifikujące się lub aktywne) podczas konfigurowania ustawień dla roli. Te opcje stają się domyślnym maksymalnym czasem trwania, gdy członek jest przypisany do roli w Privileged Identity Management.

Można wybrać jedną *z następujących opcji* czasu trwania przypisywania.

- **Zezwalaj na trwałe przypisanie**: Administratorzy mogą przypisywać trwałe członkostwo w prawo.
- **Wygaśnie przypisanie kwalifikujące się po**: Administratorzy mogą wymagać, aby wszystkie kwalifikujące się przypisania miały określoną datę początkową i końcową.

Ponadto możesz wybrać jedną z następujących opcji *aktywnego* przydziału czasu trwania:

- **Zezwalaj na trwałe aktywne przypisanie**: Administratorzy mogą przypisywać trwałe aktywne członkowstwo.
- **Wygasa aktywne przypisanie po**: Administratorzy mogą wymagać, aby wszystkie aktywne przypisania miały określoną datę początkową i końcową.

### <a name="require-azure-multi-factor-authentication"></a>Wymagaj Multi-Factor Authentication platformy Azure

Privileged Identity Management zapewnia opcjonalne wymuszanie Multi-Factor Authentication platformy Azure dla dwóch różnych scenariuszy.

- **Wymagaj Multi-Factor Authentication przy aktywnym przypisaniu**

  Jeśli chcesz przypisać element członkowski do roli przez krótki czas trwania (na przykład jeden dzień), być może jest zbyt długi, aby wymagać od przypisanych elementów członkowskich żądania aktywacji. W tym scenariuszu Privileged Identity Management nie może wymusić uwierzytelniania wieloskładnikowego, gdy użytkownik aktywuje przypisanie roli, ponieważ jest już aktywne w roli od momentu, w którym są przypisane. Aby upewnić się, że administrator spełniający przypisanie, należy zaznaczyć pole wyboru **wymagaj Multi-Factor Authentication w aktywnym przypisaniu** .

- **Wymagaj Multi-Factor Authentication podczas aktywacji**

  Możesz wymagać od uprawnionych użytkowników przypisanych do roli w celu zarejestrowania się w usłudze Azure Multi-Factor Authentication, zanim będzie możliwe jej aktywowanie. Ten proces zapewnia, że użytkownik, który żąda aktywacji, jest odpowiedzialny za uzyskanie odpowiednich pewności. Wymuszanie tej opcji chroni role krytyczne w sytuacjach, gdy nastąpiło naruszenie zabezpieczeń konta użytkownika. Aby wymagać, aby uprawniony członek mógł uruchomić usługę Azure Multi-Factor Authentication przed aktywacją, zaznacz pole **wymagaj Multi-Factor Authentication na aktywację** .

Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie wieloskładnikowe i Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Maksymalny czas trwania aktywacji

Suwak **Maksymalny czas trwania aktywacji** umożliwia ustawienie maksymalnego czasu (w godzinach), przez który rola pozostaje aktywna przed wygaśnięciem. Wartość ta może wynosić od, 1 do 24 godzin.

### <a name="require-justification"></a>Wymagaj uzasadnienia

Możesz wymagać, aby członkowie mogli wprowadzić uzasadnienie aktywnego przypisania lub kiedy aktywują. Aby wymagać uzasadnienia, zaznacz pole wyboru **Wymagaj uzasadnienia dla aktywnego przypisania** lub pole **Wymagaj uzasadnienia przy aktywacji** .

### <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia do aktywacji

Jeśli chcesz wymagać zatwierdzenia w celu aktywowania roli, wykonaj następujące czynności.

1. Zaznacz pole wyboru **Wymagaj zatwierdzenia do uaktywnienia** .
1. Wybierz pozycję **Wybierz osoby zatwierdzające** , aby otworzyć listę **Wybierz członka lub grupę** .

    ![Otwórz rolę niestandardową usługi Azure AD, aby edytować ustawienia](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Wybierz co najmniej jednego członka lub grupę, a następnie kliknij przycisk **Wybierz**. Musisz wybrać co najmniej jedną osobę zatwierdzającą. Brak domyślnych osób zatwierdzających. Wybrane opcje zostaną wyświetlone na liście wybranych osób zatwierdzających.
1. Po określeniu ustawień roli wybierz pozycję **Aktualizuj** , aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

- [Aktywuj rolę niestandardową usługi Azure AD](azure-ad-custom-roles-activate.md)
- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuń lub zaktualizuj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Definicje ról w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
