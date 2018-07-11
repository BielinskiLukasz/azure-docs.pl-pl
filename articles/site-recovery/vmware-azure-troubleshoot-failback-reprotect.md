---
title: Rozwiązywanie problemów z błędami podczas powrotu po awarii maszyn wirtualnych platformy Azure do lokalnych zasobów programu VMware przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposoby rozwiązywania typowych problemów podczas powrotu po awarii i ponownego włączania ochrony podczas powrotu po awarii do programu VMware na platformie Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: c5ba0f1d98f3e4023e767bf3eea7a0b497d47686
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920975"
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Rozwiązywanie problemów podczas powrotu po awarii z platformy Azure do programu VMware

W tym artykule opisano sposób rozwiązywania problemów, które mogą występować w przypadku maszyn wirtualnych z powrotem do platformy Azure po awarii do lokalnej infrastruktury VMware po włączeniu trybu failover na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

Powrót po awarii jest zasadniczo obejmuje dwa podstawowe kroki. W pierwszym kroku po przejściu w tryb failover należy ponownie włączyć ochronę maszyn wirtualnych platformy Azure do serwera lokalnego, aby rozpoczęcia replikacji. Drugim krokiem jest uruchamianie trybu failover z platformy Azure, aby powrócić po awarii do lokacji lokalnej.

## <a name="troubleshoot-reprotection-errors"></a>Rozwiązywanie problemów z błędami ponownego włączania ochrony

W tej sekcji przedstawiono typowe błędy ponownego włączania ochrony i ich rozwiązania.

### <a name="error-code-95226"></a>Kod błędu: 95226

**Ponowne włączanie ochrony nie powiodła się, ponieważ maszyna wirtualna platformy Azure nie mógł uzyskać dostęp do serwera konfiguracji w środowisku lokalnym.**

Ten błąd występuje, gdy:

* Maszyny Wirtualnej platformy Azure nie można nawiązać połączenia z serwerem konfiguracji w środowisku lokalnym. Maszyny Wirtualnej nie można odnaleźć i zarejestrowany na serwerze konfiguracji.
* Usługa aplikacji InMage Scout nie jest uruchomiony na maszynie Wirtualnej platformy Azure po włączeniu trybu failover. Usługa jest wymagana do komunikacji z lokalnym serwerem konfiguracji.

Aby rozwiązać ten problem:

* Sprawdź, czy sieć maszyny Wirtualnej platformy Azure umożliwia maszynie Wirtualnej platformy Azure do komunikowania się z lokalnym serwerem konfiguracji. Można skonfigurować sieć VPN lokacja lokacja do lokalnego centrum danych lub skonfigurować połączenie usługi ExpressRoute platformy Azure z prywatną komunikację równorzędną w sieci wirtualnej z maszyny Wirtualnej platformy Azure.
* Jeśli maszyna wirtualna może komunikować się z lokalnym serwerem konfiguracji, zaloguj się do maszyny Wirtualnej. Następnie sprawdź, czy usługa aplikacji InMage Scout. Jeśli widzisz, że nie jest uruchomiona, uruchom usługę ręcznie. Sprawdź, czy typ uruchomienia usługi jest równa **automatyczne**.

### <a name="error-code-78052"></a>Kod błędu: 78052

**Nie można ukończyć włączania ochrony dla maszyny wirtualnej.**

Ten problem może się zdarzyć, jeśli istnieje już maszyna wirtualna o takiej samej nazwie na główny serwer docelowy do którego w przypadku przechodzenia wstecz.

Aby rozwiązać ten problem:

* Wybierz innego głównego serwera docelowego na inny host, ponownego włączania ochrony tworzy maszynę na innego hosta, w którym nie powodują kolizję nazw.
* Możesz również użyć narzędzia vMotion przenieść głównego elementu docelowego do innego hosta, gdzie nie nastąpi kolizji nazw. Jeśli istniejącej maszyny Wirtualnej jest zabłąkany maszyny, należy ją zmienić, tak, aby na tym samym hoście ESXi można utworzyć nowej maszyny Wirtualnej.


### <a name="error-code-78093"></a>Kod błędu: 78093

**Maszyna wirtualna nie działa, zawiesiła, lub jest niedostępny.**

Aby rozwiązać ten problem:

Do ponownego włączenia ochrony maszyny Wirtualnej w trybie failed-over, maszyny Wirtualnej platformy Azure musi być uruchomiona, tak aby rejestry usługę mobilności z konfiguracji serwera w środowisku lokalnym i może rozpocząć replikacji, komunikując się z serwerem przetwarzania. Jeśli komputer znajduje się w nieprawidłowych, sieci i nie jest uruchomiona (się stan lub Zamknij w dół), serwer konfiguracji nie można nawiązać połączenia z usługi mobilności na maszynie Wirtualnej, aby rozpocząć ponowne włączanie ochrony.

* Uruchom ponownie maszynę Wirtualną tak, aby można było uruchamiać podczas komunikowania się wstecz w środowisku lokalnym.
* Po uruchomieniu maszyny wirtualnej platformy Azure, uruchom ponownie zadanie włączania ponownej ochrony.

### <a name="error-code-8061"></a>Kod błędu: 8061

**Magazyn danych nie jest dostępny z hosta ESXi.**

Sprawdź [master target wymagań wstępnych i obsługiwanymi magazynami danych](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) powrotu po awarii.


## <a name="troubleshoot-failback-errors"></a>Rozwiązywanie problemów z błędami podczas powrotu po awarii

W tej sekcji opisano typowe błędy, które mogą wystąpić podczas powrotu po awarii.

### <a name="error-code-8038"></a>Kod błędu: 8038

**Nie można przywołać lokalnej maszyny wirtualnej z powodu błędu.**

Ten problem występuje, gdy lokalna maszyna wirtualna jest przenoszone na hoście, który nie ma wystarczającej ilości pamięci aprowizowane. 

Aby rozwiązać ten problem:

* Aprowizuj większej ilości pamięci na hoście ESXi.
* Ponadto można użyć narzędzia vMotion przenieść maszynę Wirtualną do innego hosta ESXi, który ma za mało pamięci do rozruchu maszyny Wirtualnej.
