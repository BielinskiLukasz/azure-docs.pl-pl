---
title: Przypisywanie ról katalogu użytkownikom — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu przypisywania ról administratora i użytkowników niebędących administratorami dla użytkowników w usłudze Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 067362796ec64e7060a055144729e2b0f7e53c99
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097888"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Przypisywanie ról administratora i użytkowników niebędących administratorami do użytkowników z usługą Azure Active Directory
Jeśli użytkownik w organizacji wymaga uprawnień do zarządzania zasobami usługi Azure Active Directory (Azure AD), należy przypisać użytkownika odpowiednią rolę w usłudze Azure AD oparty na czynności użytkownika wymaga uprawnień do wykonania.

Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby uzyskać więcej informacji na temat dodawania użytkowników, zobacz [Dodawanie nowych użytkowników do usługi Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Przypisywanie ról
Typowym sposobem przypisać role usługi Azure AD do użytkownika znajduje się na **roli w katalogu** strony dla użytkownika.

Można także przypisać ról za pomocą Privileged Identity Management (PIM). Aby uzyskać szczegółowe informacje o sposobie używania usługi PIM, zobacz [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Aby przypisać rolę użytkownikowi
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wyszukaj i wybierz użytkownika pobierania przypisania roli. Na przykład _Alain Charon_.

3. Na **Alain Charon — profil** wybierz opcję **roli w katalogu**.

    **Alain Charon - rola katalogu** zostanie wyświetlona strona.

4. Wybierz **Dodaj rolę**, wybierz rolę do przypisania do Alain (na przykład _administrator aplikacji_), a następnie wybierz **wybierz**.

    ![Strona Role katalogu, wyświetlanie wybranej roli](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rola administrator aplikacji jest przypisywana do Alain Charon i pojawia się w **Alain Charon - rola katalogu** strony.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli
Jeśli musisz usunąć przypisania roli z danych użytkownika, możesz również zrobić z **Alain Charon - rola katalogu** strony.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Usuwanie przypisania roli użytkownika

1. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wyszukaj i wybierz użytkownika pobierania przypisania roli usunięty. Na przykład _Alain Charon_.

2. Wybierz **roli w katalogu**, wybierz opcję **administrator aplikacji**, a następnie wybierz pozycję **Usuń rolę**.

    ![Strona Role katalogu, wyświetlanie wybranej roli i opcji Usuń](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rola administrator aplikacji zostanie usunięty z Alain Charon i nie będzie już wyświetlany na **Alain Charon - rola katalogu** strony.

## <a name="next-steps"></a>Kolejne kroki
- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Dodać lub zmienić informacje o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md)

Lub można wykonywać inne zadania zarządzania użytkownika, takich jak przypisanie delegatów, przy użyciu zasad oraz udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [dokumentacja zarządzania użytkownika usługi Azure Active Directory](../users-groups-roles/index.yml).


