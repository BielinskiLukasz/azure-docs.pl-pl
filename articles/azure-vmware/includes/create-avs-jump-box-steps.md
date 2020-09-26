---
title: Utwórz pole skoku rozwiązania Azure VMware
description: Procedura tworzenia pola skoku rozwiązania Azure VMware.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: c46bd61d9003f157877a1fd0df9adb9e8143a63f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298506"
---
<!-- Used in avs-deployment.md and tutorial-access-private-cloud.md -->

1. W grupie zasobów wybierz pozycję **+ Dodaj** , a następnie wyszukaj i wybierz pozycję **Microsoft Windows 10**, a następnie wybierz pozycję **Utwórz**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Dodaj nową maszynę wirtualną z systemem Windows 10 dla pola skoku." border="true":::

1. Wprowadź wymagane informacje w polach, a następnie wybierz pozycję **Przegląd + Utwórz**. 

   Aby uzyskać więcej informacji na temat pól, zobacz poniższą tabelę.

   | Pole | Wartość |
   | --- | --- |
   | **Subskrypcja** | Wartość jest wstępnie wypełniona dla subskrypcji należącej do grupy zasobów. |
   | **Grupa zasobów** | Wartość jest wstępnie wypełniona dla bieżącej grupy zasobów, która została utworzona w poprzednim samouczku.  |
   | **Nazwa maszyny wirtualnej** | Wprowadź unikatową nazwę maszyny wirtualnej. |
   | **Region** | Wybierz lokalizację geograficzną maszyny wirtualnej. |
   | **Opcje dostępności** | Pozostaw wybraną wartość domyślną. |
   | **Obraz** | Wybierz obraz maszyny wirtualnej. |
   | **Rozmiar** | Pozostaw wartość domyślną rozmiar. |
   | **Authentication type** (Typ uwierzytelniania)  | Wybierz pozycję **hasło**. |
   | **Nazwa użytkownika** | Wprowadź nazwę użytkownika, aby zalogować się do maszyny wirtualnej. |
   | **Hasło** | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. |
   | **Potwierdź hasło** | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. |
   | **Publiczne porty ruchu przychodzącego** | Wybierz pozycję **Brak**. W przypadku wybrania opcji Brak można użyć [dostępu JIT](../../security-center/security-center-just-in-time.md#jit-configure) , aby kontrolować dostęp do maszyny wirtualnej tylko wtedy, gdy chcesz uzyskać do niej dostęp.  |


1. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia maszyny wirtualnej.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Utwórz nową maszynę wirtualną z systemem Windows 10 dla pola skoku." border="true":::