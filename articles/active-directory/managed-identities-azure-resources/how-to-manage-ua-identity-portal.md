---
title: Zarządzanie tożsamością zarządzaną przez użytkownika w Azure Portal — Azure AD
description: Instrukcje krok po kroku dotyczące tworzenia, wyświetlania, usuwania i przypisywania roli do tożsamości zarządzanej przypisanej przez użytkownika.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10caa9e5e61dc1dd0c1062583f55a7357c643ce5
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547460"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Utwórz, Wyświetl, Usuń lub Przypisz rolę do zarządzanej tożsamości przypisanej przez użytkownika przy użyciu Azure Portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z zarządzaną tożsamością w Azure Active Directory. Ta tożsamość służy do uwierzytelniania w usługach, które obsługują uwierzytelnianie usługi Azure AD, bez konieczności podania poświadczeń w kodzie. 

W tym artykule dowiesz się, jak tworzyć, wyświetlać, usuwać lub przypisywać rolę do zarządzanej tożsamości przypisanej przez użytkownika przy użyciu witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Do utworzenia tożsamości zarządzanej przypisanej przez użytkownika konto wymaga przypisania roli [współautor zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure w celu utworzenia tożsamości zarządzanej przypisanej przez użytkownika.
2. W polu wyszukiwania wpisz *tożsamości zarządzane*, a w obszarze **usługi**kliknij pozycję **tożsamości zarządzane**.
3. Kliknij przycisk **Dodaj** i wprowadź wartości w następujących polach w obszarze **Tworzenie zarządzanej tożsamości przypisanej przez użytkownika** w okienku:
   - **Nazwa zasobu**: jest to nazwa tożsamości zarządzanej przypisanej przez użytkownika, na przykład UAI1.
   - **Subskrypcja**: wybierz subskrypcję, aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika w obszarze
   - **Grupa zasobów**: Utwórz nową grupę zasobów, aby zawierała tożsamość zarządzaną przez użytkownika, lub wybierz pozycję **Użyj istniejącej** do utworzenia tożsamości zarządzanej przypisanej przez użytkownika w istniejącej grupie zasobów.
   - **Lokalizacja**: Wybierz lokalizację do wdrożenia tożsamości zarządzanej przypisanej przez użytkownika, na przykład **zachodnie stany USA**.
4. Kliknij pozycję **Utwórz**.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Wyświetlanie tożsamości zarządzanych przypisanych przez użytkownika

Aby wyświetlić/odczytać tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga [operatora zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub przypisania roli [współautor zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby wyświetlić listę zarządzanych tożsamości przypisanych do użytkownika.
2. W polu wyszukiwania wpisz *tożsamości zarządzane*, a w obszarze usługi kliknij pozycję **tożsamości zarządzane**.
3. Zostanie zwrócona lista tożsamości zarządzanych przypisanych przez użytkownika dla subskrypcji.  Aby wyświetlić szczegółowe informacje o tożsamości zarządzanej przypisanej przez użytkownika, kliknij jej nazwę.

![Wyświetlanie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika, Twoje konto wymaga przypisania roli [współautor zarządzanej tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Usunięcie tożsamości przypisanej przez użytkownika nie powoduje usunięcia jej z maszyny wirtualnej lub zasobu, do którego została przypisana.  Aby usunąć tożsamość przypisaną przez użytkownika z maszyny wirtualnej, zobacz temat [usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure w celu usunięcia tożsamości zarządzanej przypisanej przez użytkownika.
2. Wybierz tożsamość zarządzaną przypisaną przez użytkownika, a następnie kliknij pozycję **Usuń**.
3. W polu potwierdzenia wybierz pozycję **tak**.

![Usuwanie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika 

Aby przypisać rolę do tożsamości zarządzanej przypisanej przez użytkownika, Twoje konto musi mieć przypisaną rolę [administratora dostępu użytkownika](/azure/role-based-access-control/built-in-roles#user-access-administrator) .

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, aby wyświetlić listę zarządzanych tożsamości przypisanych do użytkownika.
2. W polu wyszukiwania wpisz *tożsamości zarządzane*, a w obszarze usługi kliknij pozycję **tożsamości zarządzane**.
3. Zostanie zwrócona lista tożsamości zarządzanych przypisanych przez użytkownika dla subskrypcji.  Wybierz tożsamość zarządzaną przypisaną przez użytkownika, która ma zostać przypisana do roli.
4. Wybierz pozycję **Kontrola dostępu (IAM)** , a następnie wybierz pozycję **Dodaj przypisanie roli**.

   ![Rozpoczęcie tożsamości zarządzanej przypisanej przez użytkownika](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. W bloku Dodaj przypisanie roli skonfiguruj następujące wartości, a następnie kliknij przycisk **Zapisz**:
   - **Rola** — rola do przypisania
   - **Przypisywanie dostępu do** zasobu do przypisywania tożsamości zarządzanej przypisanej przez użytkownika
   - **Wybierz** — element członkowski, do którego ma zostać przypisany dostęp
   
   ![Zarządzana przez użytkownika IAM tożsamości](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
