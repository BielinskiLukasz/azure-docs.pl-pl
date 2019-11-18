---
title: Zarządzanie grupami zasobów — Azure Portal
description: Użyj Azure Portal do zarządzania grupami zasobów przy użyciu Azure Resource Manager. Pokazuje, jak tworzyć, wyświetlać i usuwać grupy zasobów.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: e3402f496371fe89c832dc22c8288a1b94378c82
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147162"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Zarządzanie grupami zasobów Azure Resource Manager przy użyciu Azure Portal

Dowiedz się, jak używać [Azure Portal](https://portal.azure.com) z [Azure Resource Manager](resource-group-overview.md) do zarządzania grupami zasobów platformy Azure. Aby zarządzać zasobami platformy Azure, zobacz [Zarządzanie zasobami platformy Azure przy użyciu Azure Portal](./manage-resources-portal.md).

Inne artykuły dotyczące zarządzania grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](./manage-resources-cli.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Co to jest Grupa zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc, Dodaj zasoby, które mają ten sam cykl życia do tej samej grupy zasobów, dzięki czemu możesz łatwo wdrażać, aktualizować i usuwać je jako grupę.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. W przypadku określania lokalizacji grupy zasobów należy określić miejsce przechowywania metadanych.

## <a name="create-resource-groups"></a>Tworzenie grup zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **grupy zasobów**

    ![Dodaj grupę zasobów](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Wybierz pozycję **Dodaj**.
4. Wprowadź następujące wartości:

   - **Subskrypcja**: wybierz subskrypcję platformy Azure. 
   - **Grupa zasobów**: wprowadź nową nazwę grupy zasobów. 
   - **Region**: Wybierz lokalizację platformy Azure, na przykład **środkowe stany USA**.

     ![Utwórz grupę zasobów](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Wybieranie opcji **Recenzja + tworzenie**
6. Wybierz pozycję **Utwórz**. Utworzenie grupy zasobów może potrwać kilka sekund.
7. Wybierz pozycję **Odśwież** z górnego menu, aby odświeżyć listę grup zasobów, a następnie wybierz nowo utworzoną grupę zasobów, aby ją otworzyć. Lub wybierz pozycję **powiadomienie**(ikona dzwonka) u góry, a następnie wybierz pozycję **Przejdź do grupy zasobów** , aby otworzyć nowo utworzoną grupę zasobów.

    ![Przejdź do grupy zasobów](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Wyświetl listę grup zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Aby wyświetlić listę grup zasobów, wybierz pozycję **grupy zasobów**

    ![Przeglądaj grupy zasobów](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Aby dostosować informacje wyświetlane dla grup zasobów, wybierz opcję **Edytuj kolumny**. Poniższy zrzut ekranu przedstawia dodatkowe kolumny, które można dodać do ekranu:

## <a name="open-resource-groups"></a>Otwórz grupy zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Grupy zasobów**.
3. Wybierz grupę zasobów, którą chcesz otworzyć.

## <a name="delete-resource-groups"></a>Usuń grupy zasobów

1. Otwórz grupę zasobów, którą chcesz usunąć.  Zobacz [otwieranie grup zasobów](#open-resource-groups).
2. Wybierz pozycję **Usuń grupę zasobów**.

    ![Usuń grupę zasobów platformy Azure](./media/manage-resource-groups-portal/delete-group.png)

Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Resource Manager kolejności usuwania zasobów, zobacz [Azure Resource Manager Usuwanie grupy zasobów](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Wdrażanie zasobów w grupie zasobów

Po utworzeniu szablonu Menedżer zasobów można użyć Azure Portal do wdrożenia zasobów platformy Azure. Aby utworzyć szablon, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md). Aby wdrożyć szablon przy użyciu portalu, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure Portal](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Przenieś do innej grupy zasobów lub subskrypcji

Zasoby w grupie można przenieść do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resource-groups"></a>Zablokuj grupy zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usuwanie lub modyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, Grupa zasobów lub zasób. 

1. Otwórz grupę zasobów, którą chcesz usunąć.  Zobacz [otwieranie grup zasobów](#open-resource-groups).
2. W lewym okienku wybierz pozycję **blokady**.
3. Aby dodać blokadę do grupy zasobów, wybierz pozycję **Dodaj**.
4. Wprowadź **nazwę blokady**, **Typ blokady**i **notatki**. Typy blokad obejmują **tylko do odczytu**i **delete**.

    ![Zablokuj grupę zasobów platformy Azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Aby uzyskać więcej informacji, zobacz [Zablokuj zasoby, aby zapobiec nieoczekiwanym zmianom](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Dodawanie tagów do grup zasobów

Możesz zastosować znaczniki do grup zasobów i zasobów, aby logicznie organizować zasoby. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grup zasobów do szablonów

Aby uzyskać informacje na temat eksportowania szablonów, zobacz [jeden i wiele zasobów — eksport do portalu szablonów](export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).