---
title: Aktywuj rolę niestandardową usługi Azure AD w Privileged Identity Management (PIM) | Microsoft Docs
description: Jak aktywować rolę niestandardową usługi Azure AD na potrzeby przypisywania Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
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
ms.openlocfilehash: 06ceb2d2c98c3f9b6b184b98ddaadea610d525d2
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896555"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Aktywuj rolę niestandardową usługi Azure AD w Privileged Identity Management

Privileged Identity Management w usłudze Azure Active Directory (Azure AD) obsługuje teraz przypisanie "just-in-Time" do ról niestandardowych utworzonych na potrzeby zarządzania aplikacjami w środowisku administracyjnym zarządzania tożsamościami i dostępem. Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych w celu delegowania zarządzania aplikacjami w usłudze Azure AD, zobacz [Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogu w wersji zapoznawczej. Gdy funkcja jest ogólnie dostępna, zarządzanie rolami będzie odbywać się w interfejsie wbudowanym.

## <a name="activate-a-role"></a>Aktywuj rolę

Gdy musisz aktywować rolę niestandardową usługi Azure AD, Żądaj aktywacji, wybierając opcję nawigacji moje role w Privileged Identity Management.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
1. Otwórz [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)usługi Azure AD.

1. Wybierz **role niestandardowe usługi Azure AD** , aby wyświetlić listę kwalifikujących się przypisań ról niestandardowych usługi Azure AD.

   ![Zobacz listę kwalifikujących się przypisań ról niestandardowych usługi Azure AD](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Przed przypisaniem roli należy utworzyć/skonfigurować rolę. Aby uzyskać więcej informacji na temat konfigurowania ról niestandardowych usługi AAD, zobacz [tutaj] (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

1. Na stronie **role niestandardowe usługi Azure AD (wersja zapoznawcza)** Znajdź wymagane przypisanie.
1. Wybierz pozycję **Aktywuj rolę** , aby otworzyć stronę **Aktywacja** .
1. Jeśli Twoja rola wymaga uwierzytelniania wieloskładnikowego, wybierz opcję **Weryfikuj swoją tożsamość przed kontynuowaniem**. Wymagane jest uwierzytelnienie tylko raz na sesję.
1. Wybierz pozycję **Weryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.
1. Aby określić niestandardowy zakres aplikacji, wybierz pozycję **zakres** , aby otworzyć okienko filtru. Należy zażądać dostępu do roli w wymaganym minimalnym zakresie. Jeśli przypisanie znajduje się w zakresie aplikacji, można aktywować tylko w tym zakresie.

   ![Przypisywanie zakresu zasobów usługi Azure AD do przypisania roli](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. W razie konieczności Określ czas rozpoczęcia aktywacji niestandardowej. Gdy jest używany, członek roli jest aktywowany o określonej godzinie.
1. W polu **Przyczyna** wprowadź przyczynę żądania aktywacji. Są one wymagane lub nie są dostępne w ustawieniu roli.
1. Wybierz pozycję **Aktywuj**.

Jeśli rola nie wymaga zatwierdzenia, zostanie aktywowana zgodnie z ustawieniami i zostanie dodana do listy aktywnych ról. Jeśli chcesz użyć aktywowanej roli, Zacznij od kroków z sekcji [Przypisywanie roli niestandardowej usługi Azure AD w Privileged Identity Management](azure-ad-custom-roles-assign.md).

Jeśli rola wymaga zatwierdzenia do aktywacji, otrzymasz powiadomienie na platformie Azure z informacją o tym, że żądanie oczekuje na zatwierdzenie.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuń lub zaktualizuj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Skonfiguruj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
