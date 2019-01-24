---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/24/2018
ms.openlocfilehash: 05331c710817e575deb7729189c9b2d8ccbafd7d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54489591"
---
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu poświadczeń subskrypcji platformy Azure, możesz użyć. 

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard.png)

1. W lewym górnym rogu portalu, wybierz **Utwórz zasób**.

   ![Tworzenie zasobu w witrynie Azure Portal](./media/aml-create-in-portal/portal-create-a-resource.png)

1. Na pasku wyszukiwania wprowadź **uczenia maszynowego**. Wybierz **obszaru roboczego usługi Machine Learning** wynik wyszukiwania.

   ![Wyszukiwania dla obszaru roboczego](./media/aml-create-in-portal/allservices-search.PNG)

1. W **obszar roboczy usługi uczenie Maszynowe** okienko, przewiń do dołu i wybierz pozycję **Utwórz** do rozpoczęcia.

   ![Przycisk Utwórz](./media/aml-create-in-portal/portal-create-button.png)

1. W **obszar roboczy usługi uczenie Maszynowe** okienku konfigurowanie obszaru roboczego.

   Pole|Opis
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę, która identyfikuje obszaru roboczego. W tym przykładzie używamy **docs ws**. Nazwy muszą być unikatowe w obrębie grupy zasobów. Użyj nazwy, który jest łatwy do odwołania i odróżnienia jej od obszarów roboczych utworzonych przez innych użytkowników.  
   Subskrypcja |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Grupa zasobów | Użyj istniejącej grupy zasobów w ramach subskrypcji, lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. W tym przykładzie używamy **docs aml**. 
   Lokalizacja | Wybierz lokalizację najbliżej Twoich użytkowników i zasobów danych. Ta lokalizacja jest tworzona dla obszaru roboczego.

   ![Tworzenie obszaru roboczego](./media/aml-create-in-portal/workspace-create.png)

1. Aby rozpocząć proces tworzenia, wybierz opcję **Utwórz**. Może upłynąć kilka minut, aby utworzyć obszar roboczy.

1. Aby sprawdzić stan wdrożenia, wybierz ikonę powiadomienia **dzwonka**, na pasku narzędzi.

1. Po zakończeniu procesu pojawi się komunikat o powodzeniu wdrożenia. Jest również obecny w sekcji powiadomienia. Aby wyświetlić nowy obszar roboczy, wybierz **przejdź do zasobu**.

   ![Stan tworzenia obszaru roboczego](./media/aml-create-in-portal/notifications.png)
