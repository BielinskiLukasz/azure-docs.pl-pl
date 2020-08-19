---
title: Skonfiguruj automatyczne zamykanie maszyn wirtualnych w Azure Lab Services
description: W tym artykule opisano sposób konfigurowania automatycznego zamykania maszyn wirtualnych na koncie laboratorium.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8dbc54ad14530006b56bf336d9d78e7d59843485
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88603877"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Konfigurowanie automatycznego zamykania maszyn wirtualnych dla konta laboratorium

Możesz włączyć kilka funkcji kontroli kosztów automatycznego zamykania, aby aktywnie uniknąć dodatkowych kosztów, gdy maszyny wirtualne nie są aktywnie używane. Kombinacja następujących trzech funkcji automatycznego zamykania i rozłączania przechwytuje większość przypadków, w których użytkownicy przypadkowo opuszczają maszyny wirtualne z systemem:
 
- Automatyczne rozłączanie użytkowników z maszyn wirtualnych, które system operacyjny uzna za bezczynne (tylko system Windows).
- Automatycznie zamykaj maszyny wirtualne podczas odłączania użytkowników (Windows & Linux).
- Automatycznie Zamykaj uruchomione maszyny wirtualne, ale użytkownicy nie łączą się.

Zapoznaj się z bardziej szczegółowymi informacjami na temat funkcji automatycznego zamykania w sekcji [Maksymalizuj koszt kontroli przy użyciu ustawień automatycznego zamykania](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

## <a name="enable-automatic-shutdown"></a>Włącz automatyczne zamykanie

1. W [Azure Portal](https://portal.azure.com/) przejdź do strony **konto laboratorium** .
1. Wybierz pozycję **Ustawienia laboratoria** w menu po lewej stronie.
1. Wybierz ustawienia automatycznego zamykania, które są odpowiednie dla danego scenariusza.  

    > [!div class="mx-imgBorder"]
    > ![Ustawienie automatycznego zamykania na koncie laboratorium](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    Te ustawienia mają zastosowanie do wszystkich laboratoriów utworzonych w ramach konta laboratorium. Twórca laboratorium (nauczycieli) może zastąpić to ustawienie na poziomie laboratorium. Zmiana tego ustawienia na koncie laboratorium będzie miała wpływ tylko na laboratoria, które są tworzone po wprowadzeniu zmiany.

    Aby wyłączyć ustawienia, usuń zaznaczenie pól wyboru na tej stronie. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak właściciel laboratorium może skonfigurować lub zastąpić to ustawienie na poziomie laboratorium, zobacz [Konfigurowanie automatycznego zamykania maszyn wirtualnych dla laboratorium](how-to-enable-shutdown-disconnect.md)
