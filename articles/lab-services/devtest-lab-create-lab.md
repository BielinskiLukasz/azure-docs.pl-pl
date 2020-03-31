---
title: Tworzenie laboratorium w usłudze Azure DevTest Labs | Microsoft Docs
description: W tym artykule opracowywanie procesu tworzenia laboratorium przy użyciu witryny Azure portal i laboratoriów deweloperów platformy Azure.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 5cd675823b85e975dcb8dfe152c27b2d30463c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759741"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Tworzenie laboratorium w usłudze Azure DevTest Labs
Azure DevTest Labs to infrastruktura, która obejmuje wiele różnych zasobów (np. maszyn wirtualnych) i umożliwia lepsze zarządzanie tymi zasobami przez określenie ograniczeń i przydziałów. W tym artykule szczegółowo omówiono proces tworzenia laboratorium przy użyciu witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć laboratorium, potrzebne są:

* Subskrypcja platformy Azure. Aby zapoznać się z opcjami zakupu platformy Azure, zobacz [Jak kupić system Azure](https://azure.microsoft.com/pricing/purchase-options/) lub [Bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/). Aby utworzyć laboratorium, trzeba być właścicielem subskrypcji.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Procedura tworzenia laboratorium w usłudze Azure DevTest Labs
Następujące kroki ilustrują tworzenie laboratorium w usłudze Azure DevTest Labs przy użyciu witryny Azure Portal. 

1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Z menu głównego po lewej stronie wybierz pozycję **Wszystkie usługi** (w górnej części listy). Wybierz * (gwiazdkę) obok **DevTest Labs** w sekcji **DEVOPS.** Ta akcja dodaje **DevTest Labs** do lewego menu nawigacyjnego, dzięki czemu można uzyskać do niego łatwy dostęp następnym razem. 

    ![Wszystkie usługi - wybierz DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Teraz wybierz **DevTest Labs** w lewym menu nawigacyjnym. Wybierz **pozycję Dodaj** na pasku narzędzi. 
   
    ![Dodawanie laboratorium](./media/devtest-lab-create-lab/add-lab-button.png)
1. Na stronie **Tworzenie laboratorium devtest** wykonaj następujące czynności: 
    1. Wprowadź **nazwę** laboratorium.
    2. W pozycji **Subskrypcja** wybierz subskrypcję do skojarzenia z laboratorium.
    3. Wprowadź **nazwę grupy zasobów** dla laboratorium. 
    4. Wybierz **lokalizację,** w której ma być przechowywane laboratorium.
    4. Wybierz pozycję **Automatyczne zamykanie**, aby włączyć opcję i określić parametry automatycznego zamykania wszystkich maszyn wirtualnych laboratorium. Funkcja automatycznego zamykania służy głównie do osiągnięcia oszczędności. Pozwala określić, kiedy ma dojść do automatycznego zamknięcia maszyny wirtualnej. Ustawienia automatycznego zamykania można zmienić po utworzeniu laboratorium, wykonując kroki opisane w artykule [Zarządzanie wszystkimi zasadami dla laboratorium w laboratorium usługi Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Wprowadź informacje w polach **NAZWA** i **WARTOŚĆ** w obszarze **Tagi**, jeśli chcesz utworzyć niestandardowe tagowanie, które będzie dodawane do każdego zasobu utworzonego w laboratorium. Tagi są przydatne do porządkowania zasobów laboratorium według kategorii i zarządzania nimi. Aby uzyskać więcej informacji na temat tagów, w tym na temat sposobu dodawania tagów po utworzeniu laboratorium, zobacz [Dodawanie tagów do laboratorium](devtest-lab-add-tag.md).
    6. Wybierz pozycję **Opcje automatyzacji**, aby uzyskać szablony usługi Azure Resource Manager dotyczące automatyzacji konfiguracji. 
    7. Wybierz **pozycję Utwórz**. Stan procesu tworzenia laboratorium można monitorować, obserwując obszar **Powiadomienia**. 
    
        ![Tworzenie sekcji laboratorium usługi DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Po zakończeniu wybierz **pozycję Przejdź do zasobu** w powiadomieniu. Alternatywnie odśwież stronę **DevTest Labs,** aby wyświetlić nowo utworzone laboratorium na liście laboratoriów.  Wybierz laboratorium na liście. Zostanie wyświetlona strona główna laboratorium. 

        ![Strona główna laboratorium](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po utworzeniu laboratorium warto rozważyć poniższe kroki:

* [Zabezpieczanie dostępu do laboratoriów](devtest-lab-add-devtest-user.md)
* [Ustawianie zasad laboratoryjnych](devtest-lab-set-lab-policy.md)
* [Tworzenie szablonu laboratorium](devtest-lab-create-template.md)
* [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych](devtest-lab-artifact-author.md)
* [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)

