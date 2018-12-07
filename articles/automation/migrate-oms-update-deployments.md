---
title: Zmigruj swoje wdrożenia aktualizacji pakietu OMS na platformę Azure
description: W tym artykule opisano sposób migrowania istniejących wdrożeń aktualizacji pakietu OMS na platformę Azure
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5380372cc1f2928b79b0d20f4dd46e429a93dc5e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992258"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Zmigruj swoje wdrożenia aktualizacji pakietu OMS na platformę Azure

Trwa portalu Operations Management Suite (OMS) [przestarzałe](../azure-monitor/platform/oms-portal-transition.md). Wszystkie funkcje, które były dostępne w portalu pakietu OMS dla rozwiązania Update Management jest dostępna w witrynie Azure portal. Ten artykuł zawiera informacje, których potrzebujesz, aby przeprowadzić migrację do witryny Azure portal.

## <a name="key-information"></a>Informacje o kluczu

* Istniejące wdrożenia będą nadal działać. Gdy zostały utworzone ponownie wdrożenie na platformie Azure, można usunąć starego wdrożenia z pakietu OMS.
* Wszystkie funkcje istniejących, masz w pakiecie OMS są dostępne na platformie Azure, aby dowiedzieć się więcej na temat zarządzania aktualizacjami, zobacz [— Omówienie rozwiązania Update Management](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Dostęp do witryny Azure portal

W obszarze roboczym usługi OMS kliknij **Open na platformie Azure**. Powoduje to przejście do obszaru roboczego usługi Log Analytics, używanym w pakiecie OMS.

![Otwórz w programie Azure - portal pakietu OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

W witrynie Azure portal kliknij pozycję **konta usługi Automation**

![Log Analytics](media/migrate-oms-update-deployments/log-analytics.png)

Na koncie usługi Automation kliknij **rozwiązania Update Management** otwarcie rozwiązania Update Management.

![Zarządzanie aktualizacjami](media/migrate-oms-update-deployments/azure-automation.png)

W przyszłości możesz przejść bezpośrednio do witryny Azure portal w obszarze **wszystkich usług**, wybierz opcję **kont usługi Automation** w obszarze **narzędzia do zarządzania**, wybieranie odpowiedniej usługi Automation Konta, a następnie kliknij przycisk **rozwiązania Update Management**.

## <a name="recreate-existing-deployments"></a>Utwórz ponownie istniejące wdrożenia

Wszystkie wdrożenia aktualizacji, utworzone w portalu pakietu OMS masz [zapisanego wyszukiwania](../azure-monitor/platform/computer-groups.md) znany także jako grupę komputerów, z taką samą nazwę jak wdrożenie aktualizacji, który istnieje. Zapisanego kryterium wyszukiwania zawiera listę komputerów, które zostały zaplanowane wdrożenie aktualizacji.

![Zarządzanie aktualizacjami](media/migrate-oms-update-deployments/oms-deployment.png)

Aby użyć tego istniejącego zapisanego wyszukiwania, wykonaj następujące kroki:

Aby utworzyć nową aktualizację wdrożenia, przejdź do witryny Azure portal, wybierz konto usługi Automation, która jest używana, a następnie kliknij przycisk **zarządzania aktualizacjami**. Kliknij przycisk **Zaplanuj wdrażanie aktualizacji**.

![Zaplanuj wdrażanie aktualizacji](media/migrate-oms-update-deployments/schedule-update-deployment.png)

**Nowe wdrożenie aktualizacji** zostanie otwarte okienko. Wprowadź wartości dla właściwości opisane w poniższej tabeli, a następnie kliknij przycisk **Utwórz**:

W przypadku maszyny do zaktualizowania wybrać zapisanego wyszukiwania, które są używane przez istniejące wdrożenie pakietu OMS.

| Właściwość | Opis |
| --- | --- |
|Name (Nazwa) |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
|System operacyjny| Wybierz **Linux** lub **Windows**.|
|Maszyny do zaktualizowania |Wybierz zapisane wyszukiwanie, zaimportowane grupy, lub wybrać maszynę z listy rozwijanej i wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**.</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w usłudze Log Analytics, zobacz [Grupy komputerów w usłudze Log Analytics](../azure-monitor/platform/computer-groups.md) |
|Aktualizuj klasyfikacje|Wybierz wszystkie klasyfikacje aktualizacji, które są potrzebne. CentOS nie obsługuje to gotowe.|
|Aktualizacje do wykluczenia|Wprowadź aktualizacje do wykluczenia. Windows, można wprowadzić w artykule KB bez **KB** prefiks. Dla systemu Linux wprowadź nazwę pakietu, lub użyć symbolu wieloznacznego.  |
|Ustawienia harmonogramu|Wybierz godzinę rozpoczęcia, a następnie wybierz opcję **raz** lub **cyklicznie** cyklu.|| Okno obsługi |Liczba minut dla aktualizacji. Wartość nie może być mniejsza niż 30 minut lub więcej niż 6 godzin. |
| Okno obsługi |Liczba minut dla aktualizacji. Wartość może nie być mniej niż 30 minut, a nie więcej niż 6 godzin |
| Ponowne uruchomienie kontroli| Zaznaczenia prostokącie obsługi jest uruchamiany ponownie.</br>Dostępne opcje:</br>Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|

Kliknij przycisk **zaplanowane wdrożenia aktualizacji** Aby wyświetlić stan wdrożenia aktualizacji nowo utworzony.

![nowe wdrożenie aktualizacji](media/migrate-oms-update-deployments/new-update-deployment.png)

Jak wspomniano wcześniej, po skonfigurowaniu nowych wdrożeń za pośrednictwem witryny Azure portal, można usunąć istniejące wdrożenia z poziomu portalu pakietu OMS.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rozwiązania Update Management na platformie Azure, zobacz [rozwiązania Update Management](automation-update-management.md)