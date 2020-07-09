---
title: Jak dodać kolejnych właścicieli do laboratorium w Azure Lab Services
description: W tym artykule opisano, jak administrator może dodać użytkownika jako właściciela do laboratorium w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6671a3070dae672769eecf59d614d3b75455ef5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445869"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Jak dodać dodatkowych właścicieli do istniejącego laboratorium w Azure Lab Services
W tym artykule opisano, jak administrator może dodać dodatkowych właścicieli do istniejącego laboratorium.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Dodawanie użytkownika do roli czytelnik dla konta laboratorium
Aby dodać użytkownika jako dodatkowego właściciela do istniejącego laboratorium, musisz najpierw przyznać uprawnienia do **odczytu** użytkownikowi na koncie laboratorium.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wyszukaj pozycję **usługi laboratoryjne**, a następnie wybierz ją.
3. Wybierz z listy **konto laboratorium** . 
2. Na **stronie konto laboratorium**wybierz pozycję **Access Control (IAM)** w menu po lewej stronie. 
2. Na stronie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**.

    ![Przypisanie roli dla konta laboratorium ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności: 
    1. Wybierz opcję **czytelnik** dla **roli**. 
    2. Wybierz użytkownika. 
    3. Wybierz pozycję **Zapisz**. 

        ![Dodawanie użytkownika do roli czytelnik dla konta laboratorium ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Dodawanie użytkownika do roli właściciela laboratorium

1. Na stronie **konto laboratorium** wybierz pozycję **Wszystkie laboratoria** w menu po lewej stronie.
2. Wybierz **laboratorium** , do którego chcesz dodać użytkownika jako właściciela. 
    
    ![Wybieranie laboratorium ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. Na stronie **laboratorium** wybierz pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie.
4. Na stronie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**.
5. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności: 
    1. Wybierz **właściciela** dla **roli**. 
    2. Wybierz użytkownika. 
    3. Wybierz pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki
Upewnij się, że użytkownik widzi laboratorium po zalogowaniu się do [portalu usługi Lab Services](https://labs.azure.com).