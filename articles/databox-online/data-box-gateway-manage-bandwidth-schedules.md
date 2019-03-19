---
title: Zarządzanie harmonogramami przepustowości w usłudze Azure Data Box Gateway | Microsoft Docs
description: W tym artykule opisano sposób zarządzania harmonogramami przepustowości w usłudze Azure Data Box Gateway przy użyciu witryny Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 10/09/2018
ms.author: alkohli
ms.openlocfilehash: aefbe4265cb97a003b908c5631e1f9ed4e0192cc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102128"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Zarządzanie harmonogramami przepustowości w usłudze Azure Data Box Gateway przy użyciu witryny Azure Portal  

W tym artykule opisano sposób zarządzania harmonogramami przepustowości w usłudze Azure Data Box Gateway. Harmonogramy przepustowości pozwalają konfigurować użycie przepustowości sieci w wielu harmonogramach dotyczących pory dnia. Te harmonogramy można zastosować do operacji przekazywania i pobierania wykonywanych z urządzenia do chmury. 

Harmonogramy przepustowości w usłudze Data Box Gateway można dodawać, modyfikować lub usuwać za pomocą witryny Azure Portal.

> [!IMPORTANT]
> - Usługa Data Box Gateway jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie harmonogramu
> * Modyfikowanie harmonogramu
> * Usuwanie harmonogramu 


## <a name="add-a-schedule"></a>Dodawanie harmonogramu

Wykonaj następujące czynności w witrynie Azure Portal, aby dodać użytkownika.

1. W witrynie Azure Portal przejdź do zasobu usługi Data Box Gateway, a następnie do sekcji **Przepustowość**.
2. W okienku po prawej stronie kliknij przycisk **+ Dodaj harmonogram**.

    ![Klikanie przycisku Dodaj użytkownika](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. W obszarze **Dodaj harmonogram**: 

   1. Podaj **Dzień rozpoczęcia**, **Dzień zakończenia**, **Godzinę rozpoczęcia** i **Godzinę zakończenia** uruchomienia harmonogramu. 
   2. Jeśli ten harmonogram jest uruchamiany przez cały dzień, możesz zaznaczyć opcję **Cały dzień**. 
   3. **Współczynnik przepustowości** określa w megabitach na sekundę (Mb/s) przepustowość wykorzystywaną przez urządzenie w operacjach dotyczących chmury — zarówno przekazywania, jak i pobierania danych. W tym polu podaj liczbę z zakresu od 1 do 1000. 
   4. Zaznacz pole **Nieograniczona** przepustowość, jeśli nie chcesz ustalać czasu przekazywania i pobierania. 
   5. Kliknij pozycję **Add** (Dodaj).

      ![Klikanie przycisku Dodaj użytkownika](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. Harmonogram zostanie utworzony przy użyciu wybranych parametrów. Następnie harmonogram zostanie wyświetlony w portalu na liście harmonogramów przepustowości.


## <a name="edit-schedule"></a>Edytowanie harmonogramu

Wykonaj poniższe czynności, aby edytować harmonogram przepustowości. 

1. W witrynie Azure Portal przejdź do zasobu usługi Data Box Gateway, a następnie do sekcji Przepustowość. 
2. Na liście harmonogramów przepustowości kliknij harmonogram, który chcesz zmodyfikować.
    ![Modyfikowanie harmonogramu](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Wprowadź i zapisz odpowiednie zmiany.

    ![Modyfikowanie harmonogramu](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Lista harmonogramów zostanie zaktualizowana w celu uwzględnienia zmodyfikowanego harmonogramu.

    ![Modyfikowanie harmonogramu](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

Wykonaj następujące czynności, aby usunąć harmonogram przepustowości skojarzony z urządzeniem Data Box Gateway.

1. W witrynie Azure Portal przejdź do zasobu usługi Data Box Gateway, a następnie do sekcji **Przepustowość**.  

2. Na liście harmonogramów przepustowości wybierz harmonogram, który chcesz usunąć. Kliknij prawym przyciskiem myszy, aby wyświetlić menu kontekstowe, a następnie kliknij polecenie **Usuń**. 

   ![Usuwanie użytkownika](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Po usunięciu harmonogramu lista harmonogramów zostanie zaktualizowana.



## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [zarządzać przepustowością](data-box-gateway-manage-bandwidth-schedules.md).
