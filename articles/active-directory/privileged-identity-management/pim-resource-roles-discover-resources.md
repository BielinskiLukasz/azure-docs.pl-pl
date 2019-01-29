---
title: Odnajdź zasoby platformy Azure do zarządzania w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odnajdywać zasoby platformy Azure do zarządzania w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/23/2019
ms.author: rolyon
ms.openlocfilehash: e6548c2635afd8e1ac784a1930c2277548647cfd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192761"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Odnajdź zasoby platformy Azure do zarządzania w usłudze PIM

Za pomocą usługi Azure AD Privileged Identity Management (PIM), możesz zwiększyć ochronę zasobów platformy Azure. Jest to przydatne, aby organizacje, które już używają do ochrony ról katalogu usługi Azure AD PIM i właściciele grupy i subskrypcji zarządzania, którzy chcą zabezpieczanie zasobów w środowisku produkcyjnym.

Po skonfigurowaniu usługi PIM dla zasobów platformy Azure, należy odnaleźć i wybrać zasoby, które mają być chronione przez usługę PIM. Nie ma żadnego limitu liczby zasobów, którymi można zarządzać za pomocą usługi PIM. Firma Microsoft zaleca jednak począwszy od najbardziej krytycznych zasobów (produkcja).

## <a name="discover-resources"></a>Odnajdź zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

    Jeśli po raz pierwszy przy użyciu usługi PIM dla zasobów platformy Azure, zostanie wyświetlone okienko odnajdywanie zasobów.

    ![Odnajdź zasoby — po raz pierwszy](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Jeśli innego zasobu lub katalogu administratora w organizacji jest już zarządzać zasobami platformy Azure w usłudze PIM, zobaczysz listę zasobów, które są obecnie zarządzane.

    ![Odnajdź zasoby okienko](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Kliknij przycisk **odnajdywania zasobów** można uruchomić środowiska odnajdywania.

    ![W okienku odnajdywania](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. W okienku odnajdywania użyj **filtr stanu zasobów** i **wybierz typ zasobu** do filtrowania zarządzania grupy lub subskrypcji masz uprawnienie do zapisu do. Najłatwiej prawdopodobnie zaczynać **wszystkich** początkowo.

    Można jedynie Wyszukaj i wybierz zasoby do grupy lub subskrypcji zarządzania można zarządzać za pomocą usługi PIM. Jeśli zarządzasz grupę zarządzania lub subskrypcji w usłudze PIM, możesz zarządzać jego zasoby podrzędne.

1. Dodaj znacznik wyboru obok niezarządzane zasoby, które mają być zarządzane.

1. Kliknij przycisk **zarządzanie zasobami** do rozpoczęcia zarządzania wybranych zasobów.

    > [!NOTE]
    > Po grupę zarządzania lub subskrypcji jest ustawiony do zarządzanego, nie może być niezarządzane. Zapobiega to usunięcie ustawień usługi PIM przez innego administratora zasobów.

    ![Odnajdywanie — zarządzanie zasobami](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Jeśli zostanie wyświetlony komunikat o potwierdzenie dołączanie wybranego zasobu do zarządzania, kliknij przycisk **tak**.

    ![Odnajdywanie — Zarządzanie komunikat zasobu](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-role-settings.md)
- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
