---
title: Jak administrować usługą Azure cache for Redis | Microsoft Docs
description: Dowiedz się, jak wykonywać zadania administracyjne, takie jak ponowne uruchamianie i Planowanie aktualizacji usługi Azure cache for Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: eb6773d1547499fcd3a73aebf8f17ec61b6dc06a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827599"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Jak administrować usługą Azure cache for Redis
W tym temacie opisano sposób wykonywania zadań administracyjnych, takich jak [Ponowne uruchamianie](#reboot) i [Planowanie aktualizacji](#schedule-updates) pamięci podręcznej platformy Azure dla wystąpień Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Ponownie uruchom komputer
Blok **ponowne uruchomienie** umożliwia ponowne uruchomienie jednego lub większej liczby węzłów pamięci podręcznej. Ta funkcja ponownego uruchamiania umożliwia testowanie aplikacji pod kątem odporności w przypadku awarii węzła pamięci podręcznej.

![Ponownie uruchom komputer](./media/cache-administration/redis-cache-administration-reboot.png)

Wybierz węzły do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie**.

![Ponownie uruchom komputer](./media/cache-administration/redis-cache-reboot.png)

Jeśli masz pamięć podręczną Premium z włączoną obsługą klastrowania, możesz wybrać fragmentów pamięci podręcznej, aby przeprowadzić ponowny rozruch.

![Ponownie uruchom komputer](./media/cache-administration/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić co najmniej jeden węzeł pamięci podręcznej, wybierz odpowiednie węzły, a następnie kliknij przycisk **Uruchom ponownie**. Jeśli masz pamięć podręczną Premium z włączoną obsługą klastrowania, wybierz żądaną fragmentów do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie**. Po kilku minutach wybrane węzły zostaną przełączone ponownie, a następnie ponownie w trybie online kilka minut później.

Wpływ na aplikacje klienckie różni się w zależności od tego, które węzły zostały ponownie rozruchowe.

* **Wzorzec** — po ponownym uruchomieniu węzła głównego usługa Azure cache for Redis przechodzi w tryb failover do węzła repliki i promuje go do serwera głównego. Podczas pracy w trybie failover może istnieć krótki interwał, w którym połączenia mogą kończyć się niepowodzeniem w pamięci podręcznej.
* **Podrzędny** — gdy węzeł podrzędny zostanie ponownie uruchomiony, zazwyczaj nie ma to wpływu na klientów pamięci podręcznej.
* **Zarówno główny, jak i podrzędny** — po ponownym uruchomieniu obu węzłów pamięci podręcznej wszystkie dane są tracone w pamięci podręcznej i połączenia z pamięcią podręczną kończą się niepowodzeniem, dopóki węzeł podstawowy nie powróci do trybu online. Jeśli skonfigurowano [trwałość danych](cache-how-to-premium-persistence.md), najnowsza kopia zapasowa zostanie przywrócona, gdy pamięć podręczna wróci do trybu online, ale wszystkie zapisy pamięci podręcznej, które wystąpiły po ostatniej kopii zapasowej, zostaną utracone.
* **Węzły pamięci podręcznej Premium z włączoną obsługą klastrowania** — po ponownym uruchomieniu co najmniej jednego węzła pamięci podręcznej Premium z włączoną obsługą klastrowania zachowanie dla wybranych węzłów jest takie samo jak w przypadku ponownego uruchomienia odpowiedniego węzła lub węzłów nieklastrowanej pamięci podręcznej.

> [!IMPORTANT]
> Dla wszystkich warstw cenowych jest teraz dostępny ponowny rozruch.
> 
> 

## <a name="reboot-faq"></a>Często zadawane pytania dotyczące ponownego uruchamiania
* [Który węzeł należy ponownie uruchomić w celu przetestowania mojej aplikacji?](#which-node-should-i-reboot-to-test-my-application)
* [Czy mogę ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klienckie?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Czy po ponownym uruchomieniu utracisz dane z mojej pamięci podręcznej?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Czy mogę uruchomić ponownie pamięć podręczną przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Jakie warstwy cenowe mogą korzystać z funkcji ponownego uruchamiania?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Który węzeł należy ponownie uruchomić w celu przetestowania mojej aplikacji?
Aby przetestować odporność aplikacji przed awarią podstawowego węzła pamięci podręcznej, należy ponownie uruchomić węzeł **główny** . Aby przetestować odporność aplikacji przed awarią węzła pomocniczego, należy ponownie uruchomić węzeł **podrzędny** . Aby przetestować odporność aplikacji przed całkowitą awarią pamięci podręcznej, należy ponownie uruchomić **oba** węzły.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Czy mogę ponownie uruchomić pamięć podręczną, aby wyczyścić połączenia klienckie?
Tak, po ponownym uruchomieniu pamięci podręcznej wszystkie połączenia klientów są usuwane. Ponowny rozruch może być przydatny w przypadku, gdy wszystkie połączenia klientów są używane z powodu błędu logiki lub usterki w aplikacji klienckiej. Każda warstwa cenowa ma inne [limity połączeń klientów](cache-configure.md#default-redis-server-configuration) dla różnych rozmiarów, a po osiągnięciu tych limitów nie są akceptowane żadne dalsze połączenia z klientami. Ponowne uruchamianie pamięci podręcznej umożliwia wyczyszczenie wszystkich połączeń klientów.

> [!IMPORTANT]
> W przypadku ponownego uruchomienia pamięci podręcznej w celu wyczyszczenia połączeń klientów StackExchange. Redis automatycznie ponownie nawiązuje połączenie, gdy węzeł Redis będzie w trybie online. Jeśli podstawowy problem nie zostanie rozwiązany, połączenia klienta mogą być nadal używane.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Czy po ponownym uruchomieniu utracisz dane z mojej pamięci podręcznej?
W przypadku ponownego uruchomienia zarówno węzłów głównych **, jak** i podrzędnych wszystkie dane w pamięci podręcznej (lub w tym fragmentu, jeśli używasz pamięci podręcznej Premium z włączoną obsługą klastrowania) mogą zostać utracone, ale nie jest to gwarantowane. Jeśli skonfigurowano [trwałość danych](cache-how-to-premium-persistence.md), najnowsza kopia zapasowa zostanie przywrócona, gdy pamięć podręczna wróci do trybu online, ale wszystkie zapisy pamięci podręcznej, które wystąpiły po wykonaniu kopii zapasowej, zostaną utracone.

W przypadku ponownego uruchomienia tylko jednego z węzłów dane nie są zwykle tracone, ale mogą być dostępne. Na przykład jeśli węzeł główny zostanie uruchomiony ponownie, a zapis pamięci podręcznej jest w toku, dane z zapisu w pamięci podręcznej są tracone. Innym scenariuszem utraty danych może być ponowny rozruch jednego węzła, a inny węzeł zostanie przeszedł w dół ze względu na awarię w tym samym czasie. Aby uzyskać więcej informacji na temat możliwych przyczyn utraty danych, zobacz [co się stało z danymi w usłudze Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Czy mogę uruchomić ponownie pamięć podręczną przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, aby uzyskać instrukcje dotyczące programu PowerShell, zobacz [Aby ponownie uruchomić pamięć podręczną platformy Azure dla Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Jakie warstwy cenowe mogą korzystać z funkcji ponownego uruchamiania?
Dla wszystkich warstw cenowych jest dostępny ponowny rozruch.

## <a name="schedule-updates"></a>Zaplanuj aktualizacje
Blok **harmonogram aktualizacji** umożliwia wyznaczenie okna obsługi dla wystąpienia pamięci podręcznej. Po określeniu okna obsługi wszystkie aktualizacje serwera Redis są wykonywane w tym oknie. 

> [!NOTE] 
> Okno obsługi ma zastosowanie tylko do aktualizacji serwera Redis, a nie do aktualizacji lub aktualizacji platformy Azure dla systemu operacyjnego maszyn wirtualnych, które obsługują pamięć podręczną.
> 
> 

![Zaplanuj aktualizacje](./media/cache-administration/redis-schedule-updates.png)

Aby określić okno obsługi, sprawdź wymagane dni i określ czas rozpoczęcia okna obsługi dla każdego dnia, a następnie kliknij przycisk **OK**. Należy pamiętać, że czas okna obsługi jest w formacie UTC. 

Ustawienie domyślne i minimalne okno obsługi aktualizacji to pięć godzin. Ta wartość nie może być konfigurowalna z Azure Portal, ale można ją skonfigurować w programie PowerShell przy użyciu parametru `MaintenanceWindow` polecenia cmdlet [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) . Aby uzyskać więcej informacji, zobacz mogę zarządzać zaplanowanymi aktualizacjami przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?


## <a name="schedule-updates-faq"></a>Zaplanuj często zadawane pytania dotyczące aktualizacji
* [Kiedy aktualizacje są wykonywane, jeśli nie używam funkcji Zaplanuj aktualizacje?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jakiego typu aktualizacje są wykonywane w oknie zaplanowanej konserwacji?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Czy mogę zarządzać zaplanowanymi aktualizacjami za pomocą programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Kiedy aktualizacje są wykonywane, jeśli nie używam funkcji Zaplanuj aktualizacje?
Jeśli nie określisz okna obsługi, aktualizacje można wykonać w dowolnym momencie.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jakiego typu aktualizacje są wykonywane w oknie zaplanowanej konserwacji?
W oknie zaplanowanej konserwacji są wykonywane tylko aktualizacje serwera Redis. Okno obsługi nie ma zastosowania do aktualizacji lub aktualizacji platformy Azure dla systemu operacyjnego maszyny wirtualnej.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Czy mogę zarządzać zaplanowanymi aktualizacjami za pomocą programu PowerShell, interfejsu wiersza polecenia lub innych narzędzi do zarządzania?
Tak, możesz zarządzać zaplanowanymi aktualizacjami przy użyciu następujących poleceń cmdlet programu PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z większą [pamięcią podręczną platformy Azure dla funkcji warstwy Premium Redis](cache-premium-tier-intro.md) .

