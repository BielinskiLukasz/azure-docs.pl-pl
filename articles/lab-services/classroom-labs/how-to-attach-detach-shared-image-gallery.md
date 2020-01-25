---
title: Dołączanie lub odłączanie galerii obrazów udostępnionych w Azure Lab Services | Microsoft Docs
description: W tym artykule opisano sposób dołączania udostępnionej galerii obrazów do laboratorium zajęć w Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 98a21e9fa846d5a91ed1242701484afaa641cddc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718046"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Dołączanie lub odłączanie galerii obrazów udostępnionych w Azure Lab Services
Nauczyciele/administrator laboratorium mogą zapisać obraz szablonu maszyny wirtualnej w [galerii udostępnionych obrazów](../../virtual-machines/windows/shared-image-galleries.md) systemu Azure, aby był on ponownie używany przez inne osoby. Pierwszym krokiem jest dołączenie istniejącej galerii udostępnionych obrazów do konta laboratorium przez administratora laboratorium. Po dołączeniu do galerii obrazów udostępnionych laboratoria utworzone na koncie laboratorium mogą zapisywać obrazy w galerii obrazów udostępnionych. Inni nauczyciele mogą wybrać ten obraz z galerii obrazów udostępnionych, aby utworzyć szablon dla ich klas. 

W tym artykule opisano sposób dołączania lub odłączania udostępnionej galerii obrazów do konta laboratorium. 

> [!NOTE]
> Gdy obraz jest zapisywany w galerii obrazów udostępnionych, Azure Lab Services replikuje zapisany obraz do innych regionów dostępnych w tej samej lokalizacji [geograficznej](https://azure.microsoft.com/global-infrastructure/geographies/). Zapewnia, że obraz jest dostępny dla laboratoriów utworzonych w innych regionach w tej samej lokalizacji geograficznej. Zapisanie obrazów do udostępnionej galerii obrazów wiąże się z dodatkowym kosztem, który obejmuje koszt wszystkich zreplikowanych obrazów. Ten koszt jest oddzielony od kosztu użycia Azure Lab Services. Aby uzyskać więcej informacji na temat cen udostępnionych galerii obrazów, zobacz [Galeria obrazów udostępnionych — rozliczenia]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfiguruj podczas tworzenia konta laboratorium
Podczas tworzenia konta laboratorium możesz dołączyć udostępnioną galerię obrazów do konta laboratorium. Możesz wybrać istniejącą galerię obrazów udostępnionych z listy rozwijanej lub utworzyć nową. Aby utworzyć i dołączyć udostępnioną galerię obrazów do konta laboratorium, wybierz pozycję **Utwórz nowy**, wprowadź nazwę galerii, a następnie wprowadź **OK**. 

![Konfigurowanie udostępnionej galerii obrazów w momencie tworzenia konta laboratorium](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfiguruj po utworzeniu konta laboratorium
Po utworzeniu konta laboratorium można wykonać następujące zadania:

- Tworzenie i dołączanie galerii obrazów udostępnionych
- Dołącz udostępnioną galerię obrazów do konta laboratorium
- Odłączanie udostępnionej galerii obrazów z konta laboratorium

## <a name="create-and-attach-a-shared-image-gallery"></a>Tworzenie i dołączanie galerii obrazów udostępnionych
1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz pozycję **usługi laboratoryjne** w sekcji **DEVOPS** . Jeśli wybierzesz pozycję gwiazda (`*`) obok pozycji **usługi Lab Services**, zostanie ona dodana do sekcji **Ulubione** w menu po lewej stronie. Od następnego momentu wybierz pozycję **usługi laboratoryjne** w obszarze **Ulubione**.

    ![Wszystkie usługi — > usług Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Wybierz swoje konto laboratorium, aby wyświetlić stronę **konto laboratorium** . 
4. Wybierz pozycję **Galeria obrazów udostępnionych** w menu po lewej stronie, a następnie wybierz pozycję **+ Utwórz** na pasku narzędzi.  

    ![Przycisk tworzenia galerii obrazów udostępnionych](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. W oknie **Tworzenie galerii obrazów udostępnionych** wprowadź **nazwę** galerii, a następnie wprowadź **OK**. 

    ![Utwórz okno galerii obrazów udostępnionych](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services tworzy galerię obrazów udostępnionych i dołączy ją do konta laboratorium. Wszystkie laboratoria utworzone w ramach tego konta laboratorium mają dostęp do dołączonej galerii obrazów udostępnionych. 

    ![Galeria dołączonych obrazów](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    W dolnym okienku zobaczysz obrazy w galerii obrazów udostępnionych. W tej nowej galerii nie ma obrazów. Po przekazaniu obrazów do galerii są one widoczne na tej stronie.     

    Wszystkie obrazy z dołączonej udostępnionej galerii obrazów są domyślnie włączone. Można włączać lub wyłączać wybrane obrazy, zaznaczając je na liście i używając przycisku **Włącz wybrane obrazy** lub **Wyłącz wybrane obrazy** .

## <a name="attach-an-existing-shared-image-gallery"></a>Dołączanie istniejącej galerii obrazów udostępnionych
Poniższa procedura przedstawia sposób dołączania istniejącej galerii obrazów udostępnionych do konta laboratorium. 

1. Na stronie **konto laboratorium** wybierz pozycję **Galeria obrazów udostępnionych** w menu po lewej stronie, a następnie wybierz pozycję **Dołącz** na pasku narzędzi. 

    ![Galeria obrazów udostępnionych — przycisk Dodaj](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na stronie **dołączanie istniejącej galerii obrazów udostępnionych** Wybierz galerię udostępnionych obrazów, a następnie wybierz **przycisk OK**.

    ![Wybierz istniejącą galerię](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zobaczysz następujący ekran: 

    ![Moja Galeria na liście](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    W tym przykładzie nie ma jeszcze obrazów w galerii obrazów udostępnionych.

    Azure Lab Services tożsamość jest dodawana jako współautor do galerii obrazów udostępnionych, która jest dołączona do laboratorium. Umożliwia nauczycielom/administratorom IT zapisywanie obrazów maszyn wirtualnych w galerii obrazów udostępnionych. Wszystkie laboratoria utworzone w ramach tego konta laboratorium mają dostęp do dołączonej galerii obrazów udostępnionych. 

    Wszystkie obrazy z dołączonej udostępnionej galerii obrazów są domyślnie włączone. Można włączać lub wyłączać wybrane obrazy, zaznaczając je na liście i używając przycisku **Włącz wybrane obrazy** lub **Wyłącz wybrane obrazy** . 

## <a name="detach-a-shared-image-gallery"></a>Odłączanie galerii obrazów udostępnionych
Tylko jedna udostępniona Galeria obrazów może być dołączona do laboratorium. Jeśli chcesz dołączyć kolejną galerię obrazów udostępnionych, odłącz ją od bieżącego przed dołączeniem nowego. Aby odłączyć galerię obrazów udostępnionych z laboratorium, wybierz opcję **Odłącz** na pasku narzędzi i potwierdź operację odłączania. 

![Odłączanie galerii obrazów udostępnionych z konta laboratorium](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak zapisać obraz laboratorium w galerii obrazów udostępnionych lub użyć obrazu z galerii obrazów udostępnionych, aby utworzyć maszynę wirtualną, zobacz [jak używać galerii obrazów udostępnionych](how-to-use-shared-image-gallery.md).

Aby uzyskać więcej informacji na temat ogólnie udostępnionych galerii obrazów, zobacz [Galeria obrazów udostępnionych](../../virtual-machines/windows/shared-image-galleries.md).
