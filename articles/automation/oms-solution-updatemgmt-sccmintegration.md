---
title: Korzystanie z usługi Azure Update Management z klientami Configuration Manager
description: Ten artykuł ma na celu ułatwienie konfiguracji programu Microsoft Endpoint Configuration Manager przy użyciu tego rozwiązania w celu wdrażania aktualizacji oprogramowania na klientach programu ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: f0ca836e3b53c3cce755d45b50fe168073f0bbaa
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513137"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Wdrażanie aktualizacji na klientach programu Microsoft Endpoint Configuration Manager z Update Management

Klienci, którzy zainwestowali w program Microsoft Endpoint Configuration Manager w celu zarządzania komputerami, serwerami i urządzeniami przenośnymi, również polegają na jej sile i dojrzałości w zarządzaniu aktualizacjami oprogramowania w ramach cyklu zarządzania aktualizacjami oprogramowania (SUM).

Można zgłaszać i aktualizować zarządzane serwery Windows, tworząc i wstępnie przemieszczając wdrożenia aktualizacji oprogramowania w programie Configuration Manager i uzyskać szczegółowy stan ukończonych wdrożeń aktualizacji przy użyciu [rozwiązania do zarządzania aktualizacjami](automation-update-management.md). W przypadku używania Configuration Manager do raportowania zgodności aktualizacji, ale nie do zarządzania wdrożeniami aktualizacji z serwerami z systemem Windows, można kontynuować raportowanie do Configuration Manager, gdy aktualizacje zabezpieczeń są zarządzane przy użyciu rozwiązania Update Management.

## <a name="prerequisites"></a>Wymagania wstępne

* Konieczne jest posiadanie [rozwiązanie do zarządzania aktualizacjami](automation-update-management.md) dodane do Twojego konta usługi Automation.
* Serwery z systemem Windows, które są obecnie zarządzane przez środowisko Configuration Manager, również muszą zgłosić do obszaru roboczego Log Analytics z włączonym rozwiązaniem Update Management.
* Ta funkcja jest włączona w Configuration Manager bieżącej gałęzi w wersji 1606 lub nowszej. Aby zintegrować Configuration Manager centralną lokację administracyjną lub autonomiczną lokację główną z dziennikami Azure Monitor i importować kolekcje, przejrzyj [Configuration Manager połączenia w celu Azure monitor dzienników](../azure-monitor/platform/collect-sccm.md).  
* Agenci dla systemu Windows muszą być skonfigurowani do komunikowania się z serwerem Windows Server Update Services (WSUS) albo mieć dostęp do usługi Microsoft Update, jeśli nie otrzymują aktualizacji zabezpieczeń z programu Configuration Manager.   

To, w jaki sposób zarządzasz klientami hostowanymi w usłudze Azure IaaS za pomocą istniejącego środowiska programu Configuration Manager, zależy głównie od połączenia istniejącego między centrami danych platformy Azure a Twoją infrastrukturą. Połączenie to ma wpływ na wszelkie zmiany projektowe, których wprowadzenie do infrastruktury programu Configuration Manager może być konieczne, oraz wpływa na powiązane koszty obsługi tych niezbędnych zmian. Aby zrozumieć, jakie kwestie planowania należy ocenić przed kontynuowaniem, zobacz [Menedżer konfiguracji Azure — często zadawane pytania](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfigurowanie

### <a name="manage-software-updates-from-configuration-manager"></a>Zarządzanie aktualizacjami oprogramowania z programu Configuration Manager 

Jeśli chcesz kontynuować zarządzanie wdrożeniami aktualizacji z programu Configuration Manager, wykonaj następujące kroki. Usługa Azure Automation nawiązuje połączenie z programem Configuration Manager do stosowania aktualizacji na komputerach klienckich, połączonego z obszarem roboczym usługi Log Analytics. Zawartość aktualizacji jest dostępna w pamięci podręcznej komputera klienckiego tak, jakby wdrożenie było zarządzane przez program Configuration Manager.

1. Utwórz wdrożenie aktualizacji oprogramowania z lokacji najwyższego poziomu w hierarchii Configuration Manager przy użyciu procesu opisanego w temacie [wdrażanie aktualizacji oprogramowania](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). Jedynym ustawieniem, które musi być skonfigurowane inaczej niż dla standardowego wdrożenia, jest opcja **Nie instaluj aktualizacji oprogramowania** służąca do sterowania zachowaniem pakietu wdrożeniowego podczas pobierania. To zachowanie jest zarządzane przez rozwiązanie do zarządzania aktualizacjami poprzez utworzenie planowego wdrożenia aktualizacji w następnym kroku.

1. W usłudze Azure Automation, wybierz **rozwiązania Update Management**. Utwórz nowe wdrożenie wykonując kroki opisane w [tworzenie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz **zaimportowane grupy** na **typu** listy rozwijanej można wybrać odpowiednią Kolekcja programu Configuration Manager. Należy pamiętać o następujących kwestiach:. Jeśli okno obsługi jest zdefiniowane w wybranej kolekcji urządzeń programu Configuration Manager, elementy członkowskie kolekcji przestrzegać go zamiast **czas trwania** ustawień zdefiniowanych w zaplanowanym wdrożeniu.
    b. Członkowie kolekcji docelowej muszą mieć połączenie z Internetem (bezpośrednio, za pośrednictwem serwera proxy lub za pośrednictwem bramy usługi Log Analytics).

Po zakończeniu wdrażania aktualizacji za pomocą usługi Azure Automation, komputerów docelowych, które są członkami wybranej grupy komputerów zainstalują aktualizacje o zaplanowanym czasie z ich lokalnej pamięci podręcznej klienta. Możesz [wyświetlić stan wdrożenia aktualizacji](automation-tutorial-update-management.md#view-results-of-an-update-deployment), aby monitorować wyniki swojego wdrożenia.

### <a name="manage-software-updates-from-azure-automation"></a>Zarządzanie aktualizacjami oprogramowania z usługi Azure Automation

Do zarządzania aktualizacjami maszyn wirtualnych z systemem Windows Server, które są klientami programu Configuration Manager, należy skonfigurować zasady klienta, aby wyłączyć funkcję zarządzania aktualizacjami oprogramowania dla wszystkich klientów zarządzanych przez to rozwiązanie. Domyślnie ustawienia klienta dotyczą wszystkich urządzeń w hierarchii. Aby uzyskać więcej informacji na temat tego ustawienia zasad i sposobu jego konfiguracji, zobacz [jak skonfigurować ustawienia klienta w Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Po wprowadzeniu tej zmiany konfiguracji, możesz utworzyć nowe wdrożenie wykonując kroki opisane w [tworzenie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz **zaimportowane grupy** na **typu** listy rozwijanej można wybrać odpowiednią kolekcję programu Configuration Manager.

## <a name="next-steps"></a>Następne kroki

