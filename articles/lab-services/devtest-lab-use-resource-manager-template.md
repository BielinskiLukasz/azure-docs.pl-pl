---
title: Wyświetlanie i używanie szablonu Azure Resource Manager maszyny wirtualnej
description: Dowiedz się, jak utworzyć inne maszyny wirtualne przy użyciu szablonu Azure Resource Manager z maszyny wirtualnej
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 7064fdeec04f4dc5ae2c73c1a3896cf2d10dd01d
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169115"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Tworzenie maszyn wirtualnych przy użyciu szablonu Azure Resource Manager 

Podczas tworzenia maszyny wirtualnej (VM) w DevTest Labs za pomocą [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)można wyświetlić szablon Azure Resource Manager przed zapisaniem maszyny wirtualnej. Szablonu można następnie użyć jako podstawy, aby utworzyć więcej maszyn wirtualnych laboratorium z tymi samymi ustawieniami.

W tym artykule opisano szablony Menedżer zasobów z obsługą jednej maszyny wirtualnej, a w tym przedstawiono sposób wyświetlania i zapisywania szablonu podczas tworzenia maszyny wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Szablony Menedżer zasobów z obsługą jednej maszyny wirtualnej a maszyną wirtualną
Istnieją dwa sposoby tworzenia maszyn wirtualnych w DevTest Labs przy użyciu szablonu Menedżer zasobów: zainicjuj zasób Microsoft. wspólny/Labs/virtualmachines lub zainicjuj zasób Microsoft. COMPUTE/virtualmachines. Każdy z nich jest używany w różnych scenariuszach i wymaga innych uprawnień.

- Menedżer zasobów szablonów używających typu zasobu Microsoft. wspólny/Labs/virtualmachines (zgodnie z deklaracją w właściwości "Resource" w szablonie) mogą udostępniać poszczególne maszyny wirtualne laboratorium. Każda maszyna wirtualna jest następnie wyświetlana jako pojedynczy element na liście maszyn wirtualnych DevTest Labs:

   ![Lista maszyn wirtualnych jako pojedyncze elementy na liście DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ten typ szablonu Menedżer zasobów można zainicjować za pomocą polecenia Azure PowerShell **New-AzResourceGroupDeployment** lub przy użyciu interfejsu wiersza polecenia platformy Azure **AZ Group Deployment Create**. Wymaga uprawnień administratora, więc użytkownicy, którzy są przypisani przy użyciu roli użytkownika DevTest Labs, nie mogą wykonać wdrożenia. 

- Menedżer zasobów szablony korzystające z typu zasobu Microsoft. COMPUTE/virtualmachines mogą obsługiwać wiele maszyn wirtualnych jako pojedyncze środowisko na liście maszyn wirtualnych DevTest Labs:

   ![Lista maszyn wirtualnych jako pojedyncze elementy na liście DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Maszyny wirtualne w tym samym środowisku mogą być zarządzane razem i współużytkować ten sam cykl życia. Użytkownicy, którzy są przypisani przy użyciu roli użytkownika DevTest Labs, mogą tworzyć środowiska przy użyciu tych szablonów, o ile administrator skonfigurował laboratorium w taki sposób.

W pozostałej części tego artykułu omówiono Menedżer zasobów szablonów korzystających z programu Microsoft. wspólny/Labs/virtualmachines. Są one używane przez administratorów laboratorium do automatyzowania tworzenia maszyn wirtualnych laboratorium (na przykład z maszynami wirtualnymi) lub do generowania obrazów (na przykład fabryki obrazów).

[Najlepsze rozwiązania dotyczące tworzenia szablonów Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferują wiele wytycznych i sugestii ułatwiających tworzenie Azure Resource Manager szablonów, które są niezawodne i łatwe w użyciu.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Wyświetlanie i zapisywanie szablonu Menedżer zasobów maszyny wirtualnej
1. Wykonaj kroki opisane w temacie [Tworzenie pierwszej maszyny wirtualnej w laboratorium,](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) aby rozpocząć tworzenie maszyny wirtualnej.
1. Wprowadź wymagane informacje dotyczące maszyny wirtualnej i Dodaj dowolne artefakty dla tej maszyny wirtualnej.
1. Przełącznik na kartę **Ustawienia zaawansowane** . 
1. W dolnej części okna Konfigurowanie ustawień wybierz pozycję **Wyświetl szablon ARM**.
1. Skopiuj i Zapisz szablon Menedżer zasobów, aby później utworzyć inną maszynę wirtualną.

   ![Menedżer zasobów szablon do zapisania do późniejszego użycia](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po zapisaniu szablonu Menedżer zasobów należy zaktualizować sekcję Parametry szablonu przed jego użyciem. Można utworzyć parametr. JSON, który dostosowuje tylko parametry, poza rzeczywistym szablonem Menedżer zasobów. 

![Dostosowywanie parametrów przy użyciu pliku JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Szablon Menedżer zasobów jest teraz gotowy do użycia w celu [utworzenia maszyny wirtualnej](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Ustaw datę wygaśnięcia
W scenariuszach takich jak szkolenia, pokazy i wersje próbne można tworzyć maszyny wirtualne i usuwać je automatycznie po upływie ustalonego czasu, aby nie ponosić niepotrzebnych kosztów. Można utworzyć maszynę wirtualną laboratorium z datą wygaśnięcia, określając właściwość **expirationDate** maszyny wirtualnej. Zapoznaj się z tym samym szablonem Menedżer zasobów w [naszym repozytorium GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [tworzenie środowisk z wieloma Maszynami wirtualnymi za pomocą szablonów usługi Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Wdróż szablon Menedżer zasobów, aby utworzyć maszynę wirtualną](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Poznaj więcej szablonów szybkiego startu usługi Resource Manager, związane z automatyzacją DevTest Labs z [publicznego repozytorium GitHub laboratoria DevTest](https://github.com/Azure/azure-quickstart-templates).
