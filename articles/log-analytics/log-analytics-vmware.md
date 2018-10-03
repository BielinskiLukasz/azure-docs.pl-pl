---
title: Rozwiązanie do monitorowania programu VMware w usłudze Log Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak to rozwiązanie monitorowanie VMware mogą ułatwić zarządzanie dziennikami i monitorowanie hostów ESXi.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 6bd195b8be558cfcfda10a750fbfe91079c6b094
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043607"
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Rozwiązanie monitorowanie VMware (wersja zapoznawcza) w usłudze Log Analytics

![Symbol programu VMware](./media/log-analytics-vmware/vmware-symbol.png)

> [!NOTE]
> To rozwiązanie monitorowanie VMware jest przestarzała.  Klienci, którzy już zainstalowano rozwiązania można nadal z niego korzystać, ale monitorowanie VMware nie można dodać do żadnych nowych obszarów roboczych.

Rozwiązanie monitorowanie VMware w usłudze Log Analytics to rozwiązanie, które pomaga w utworzeniu scentralizowanego rejestrowania i monitorowania podejście do dużych dzienników oprogramowania VMware. W tym artykule opisano, jak można rozwiązać, przechwytywania i zarządzać hostów ESXi w obrębie jednej lokalizacji za pomocą rozwiązania. Dzięki rozwiązaniu można poznać szczegółowe dane dla wszystkich hostów ESXi w obrębie jednej lokalizacji. Widać, liczby najważniejszych zdarzeń, stanu i trendów maszyn wirtualnych i ESXi hostów, oferowana w ramach dzienniki hostów ESXi. Można rozwiązać, wyświetlania i przeszukiwania scentralizowanych dzienników hostów ESXi. Ponadto możesz utworzyć alerty w oparciu o zapytań funkcji przeszukiwania dzienników.

Rozwiązanie korzysta z funkcji natywnej syslog hosta ESXi w celu wypychania danych do docelowej maszyny Wirtualnej, która ma agenta pakietu OMS. Jednak to rozwiązanie nie zapisywać pliki do usługi syslog w ramach docelowej maszyny Wirtualnej. Agent pakietu OMS otwiera port 1514 i nasłuchuje tej. Po odebraniu danych, agenta pakietu OMS wypycha dane do usługi Log Analytics.

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

* Dodaj rozwiązanie monitorowanie VMware do subskrypcji przy użyciu procesu opisanego w [zainstalować rozwiązanie do zarządzania](../monitoring/monitoring-solutions.md#install-a-management-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Obsługiwane hosty VMware ESXi
vSphere hosta ESXi 5.5, 6.0 i 6.5

#### <a name="prepare-a-linux-server"></a>Przygotowanie na serwerze z systemem Linux
Utwórz system operacyjny Linux maszyny Wirtualnej, aby otrzymywać wszystkie dane usługi syslog hostów ESXi. [OMS Linux Agent](log-analytics-linux-agents.md) to punkt kolekcji wszystkie dane usługi syslog hosta ESXi. Do przekazywania dzienników na jednym serwerze z systemem Linux, jak w poniższym przykładzie, można użyć wielu hostach ESXi.  

   ![Przepływ usługi SYSLOG](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfiguruj zbieranie dzienników systemowych
1. Konfigurowanie przekazywania usługi syslog dla VSphere. Aby uzyskać szczegółowe informacje ułatwiające konfigurowanie przekazywania usługi syslog, zobacz [Konfigurowanie dziennika systemowego na ESXi 5.0 lub nowszym (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Przejdź do **konfiguracji hosta ESXi** > **oprogramowania** > **Zaawansowane ustawienia** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
1. W *Syslog.global.logHost* pola, Dodaj serwer systemu Linux i numer portu *1514*. Na przykład `tcp://hostname:1514` lub `tcp://123.456.789.101:1514`
1. Otwórz zaporę hosta ESXi, dla dziennika systemowego. **Konfiguracja hosta ESXi** > **oprogramowania** > **profil zabezpieczeń** > **zapory** , a następnie otwórz **Właściwości**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
1. Sprawdź vSphere konsoli, aby sprawdzić, czy ten syslog nie jest prawidłowo skonfigurowany. Upewnij się na hoście ESXI, ten port **1514** jest skonfigurowany.
1. Pobierz i zainstaluj agenta pakietu OMS dla systemu Linux na serwerze z systemem Linux. Aby uzyskać więcej informacji, zobacz [dokumentacji dla agenta pakietu OMS dla systemu Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Po zainstalowaniu agenta pakietu OMS dla systemu Linux, przejdź do katalogu /etc/opt/microsoft/omsagent/sysconf/omsagent.d i skopiuj plik vmware_esxi.conf do katalogu /etc/opt/microsoft/omsagent/conf/omsagent.d i zmiany właściciela/grupy i uprawnienia pliku. Na przykład:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Uruchom ponownie agenta pakietu OMS dla systemu Linux, uruchamiając `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Przetestuj łączność między serwerze z systemem Linux i hosta ESXi przy użyciu `nc` polecenie na hoście ESXi. Na przykład:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. W witrynie Azure portal, wykonaj wyszukiwanie w dzienniku `VMware_CL`. Log Analytics zbiera dane usługi syslog, zachowuje formatu syslog. W portalu, niektóre określonych pól są przechwytywane, takich jak *Hostname* i *ProcessName*.  

    ![type](./media/log-analytics-vmware/type.png)  

    Jeśli wyświetlanie wyników wyszukiwania dziennika są podobne do powyższej ilustracji, gotowe do użycia pulpit nawigacyjny rozwiązania monitorowanie VMware.  

## <a name="vmware-data-collection-details"></a>Szczegóły zbierania danych VMware
To rozwiązanie monitorowanie VMware zbiera różne dane metryk i dzienników wydajności z hostów ESXi przy użyciu agentów pakietu OMS dla systemu Linux, które mają włączone.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu zbierania danych.

| Platforma | Agent usługi OMS dla systemu Linux | Agenta programu SCOM | Azure Storage | SCOM wymagane? | Dane agenta programu SCOM wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |co 3 minuty |

Poniższa tabela wyświetlanie przykładów pól danych zbieranych przez rozwiązanie monitorowanie VMware:

| nazwa pola | description |
| --- | --- |
| Device_s |Urządzenia magazynujące programu VMware |
| ESXIFailure_s |typy błędów |
| EventTime_t |Godzina wystąpienia zdarzenia |
| HostName_s |Nazwa hosta ESXi |
| Operation_s |Tworzenie maszyny Wirtualnej lub Usuń maszynę Wirtualną |
| ProcessName_s |Nazwa zdarzenia |
| ResourceId_s |Nazwa hosta VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Funkcja Hyper-V |
| SCSIStatus_s |Stan VMware SCSI |
| SyslogMessage_s |Dane usługi SYSLOG |
| UserName_s |Użytkownik, który utworzeniu lub usunięciu maszyny Wirtualnej |
| VMName_s |Nazwa maszyny wirtualnej |
| Computer (Komputer) |komputer-host |
| TimeGenerated |czas wygenerowania danych |
| DataCenter_s |Centrum danych VMware |
| StorageLatency_s |Magazyn, opóźnienie (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Monitorowanie VMware — Omówienie rozwiązania
Na kafelku VMware pojawia się w obszarze roboczym usługi Log Analytics. Zapewnia ogólny widok wszystkich błędów. Po kliknięciu kafelka, przejdź do widoku pulpitu nawigacyjnego.

![kafelek](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Przejdź do widoku pulpitu nawigacyjnego
W **VMware** widoku pulpitu nawigacyjnego bloki są uporządkowane według:

* Liczba stanu błędu
* Najważniejsze hosta według liczb zdarzeń
* Liczby najważniejszych zdarzeń
* Działania maszyny wirtualnej
* Zdarzenia dotyczące dysków hostów ESXi

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Kliknij przycisk bloku, aby otworzyć okienko wyszukiwania usługi Log Analytics, która zawiera szczegółowe informacje specyficzne dla bloku.

W tym miejscu można edytować zapytania wyszukiwania, aby zmodyfikować go dla określonego elementu. Aby uzyskać więcej informacji na temat tworzenia wyszukiwań w dziennikach, zobacz [znaleźć dane przy użyciu przeszukiwania dzienników w usłudze Log Analytics](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Znajdź wydarzenia hosta ESXi
Jeden host ESXi generuje wiele dzienników, w oparciu o ich procesów. To rozwiązanie monitorowanie VMware centralizuje je i zawiera podsumowanie liczby zdarzeń. To scentralizowany widok pomaga zrozumieć, które hostów ESXi ma dużej liczby zdarzeń i jakie zdarzenia występują najczęściej w danym środowisku.

![event](./media/log-analytics-vmware/events.png)

Możesz przejść do dalszych klikając hosta ESXi lub typu zdarzenia.

Po kliknięciu nazwy hosta ESXi, możesz przeglądać informacje z tego hosta ESXi. Jeśli chcesz zawęzić wyniki z typem zdarzenia, należy dodać `“ProcessName_s=EVENT TYPE”` w zapytaniu wyszukiwania. Możesz wybrać **ProcessName** w filtrze wyszukiwania. Który zawęża informacje dla Ciebie.

![Przechodzenie do szczegółów](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Znajdź wysokiej działania maszyny Wirtualnej
Maszyny wirtualne można tworzyć i usunięty na żadnym hoście ESXi. Jest to przydatne dla administratora zidentyfikować ile maszyn wirtualnych, które tworzy hosta ESXi. Tego umożliwiłoby, ułatwia zrozumienie wydajności i planowania pojemności. Rejestrowanie informacji o zdarzeń działania maszyny Wirtualnej jest niezwykle istotne w przypadku, gdy zarządzania środowiskiem.

![Przechodzenie do szczegółów](./media/log-analytics-vmware/vmactivities1.png)

Jeśli chcesz wyświetlić dodatkowe dane tworzenia maszyny Wirtualnej hosta ESXi, kliknij nazwę hosta ESXi.

![Przechodzenie do szczegółów](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Typowe zapytania wyszukiwania
Rozwiązanie zawiera inne przydatne zapytania, które mogą pomóc Ci w zarządzaniu hostów ESXi, takie jak miejsce do magazynowania o wysokiej, opóźnienie magazynu i ścieżka błędu.

![— zapytania](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Zapisywanie zapytań
Zapisywanie zapytań wyszukiwania jest funkcją standard w usłudze Log Analytics i ułatwia przechowywanie żadnych zapytań, które znajdziesz przydatne. Po utworzeniu zapytania, które możesz się przydać, zapisz go, klikając **ulubione**. Zapisane zapytanie pozwala z łatwością wykorzystać ją później [Mój pulpit nawigacyjny](log-analytics-dashboards.md) strony, w którym można utworzyć własne niestandardowe pulpity nawigacyjne.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Tworzenie alertów na podstawie zapytania
Po utworzeniu zapytania, można użyć zapytań ostrzega w przypadku wystąpienia określonych zdarzeń. Zobacz [alertów w usłudze Log Analytics](log-analytics-alerts.md) informacji o tym, jak tworzyć alerty. Przykłady alertów zapytania i inne przykłady zapytań, zobacz [monitorowanie VMware przy użyciu usługi Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) wpis w blogu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Co muszę na ESXi hosta ustawienie? Jaki wpływ będzie miał na Moje bieżącego środowiska?
Rozwiązanie używa natywnego Syslog hosta ESXi, mechanizm przekazywania. Nie potrzebujesz żadnego dodatkowego oprogramowania firmy Microsoft na hoście ESXi w celu przechwytywania dzienników. Powinien on mały wpływ na istniejącym środowiskiem. Jednakże należy ustawić przekazywania usługi syslog, czyli funkcji ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Należy ponownie uruchomić mój hosta ESXi?
Nie. Ten proces nie wymaga ponownego uruchomienia komputera. Czasami vSphere, nie jest poprawnie aktualizowany syslog. W takim przypadku należy zalogować się na hoście ESXi i ponownie załaduj syslog. Nie trzeba ponownie, uruchom ponownie hosta, więc ten proces nie jest znaczący wpływ na środowisko.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Można zwiększyć lub zmniejszyć ilość danych dziennika wysyłane do usługi Log Analytics?
Tak, możesz to zrobić. Umożliwia ustawienie poziomie dzienników hostów ESXi w vSphere. Zbieranie dzienników opiera się na *informacje* poziom. Tak, jeśli chcesz Przeprowadź inspekcję maszyn wirtualnych, tworzenie lub usuwanie należy zachować *informacje* poziomu na Hostd. Aby uzyskać więcej informacji, zobacz [VMware wiedzy](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Dlaczego Hostd nie dostarcza dane do usługi Log Analytics? Moje ustawienia dziennika jest równa informacji.
Wystąpił błąd hosta ESXi sygnatury czasowej syslog. Aby uzyskać więcej informacji, zobacz [VMware wiedzy](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Po zastosowaniu obejścia Hostd powinny działać normalnie.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Czy można mieć wiele hostów ESXi przekazywania danych z serwera syslog z jedną maszyną wirtualną za pomocą omsagent?
Tak. Może mieć wiele hostów ESXi przekazywania z jedną maszyną wirtualną za pomocą omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Dlaczego nie widzisz dane trafiające do usługi Log Analytics?
Może istnieć kilka przyczyn:

* ESXi host nie jest poprawnie wypychanie danych do maszyny Wirtualnej z systemem omsagent. Aby przetestować, wykonaj następujące czynności:

  1. Aby upewnić się, zaloguj się na hoście ESXi przy użyciu protokołu ssh i uruchom następujące polecenie: `nc -z ipaddressofVM 1514`

      Jeśli to się nie powiedzie, vSphere ustawień konfiguracji zaawansowanych prawdopodobnie nieprawidłowy. Zobacz [skonfigurować zbieranie dzienników systemowych](#configure-syslog-collection) informacji o tym, jak skonfigurować hosta ESXi na potrzeby przekazywania usługi syslog.
  1. Jeśli łączność portu usługi syslog zakończy się pomyślnie, ale nadal nie widzisz żadnych danych, Załaduj ponownie syslog na hoście ESXi, za pomocą protokołu ssh wpisz następujące polecenie: ` esxcli system syslog reload`
* Maszyny Wirtualnej za pomocą agenta pakietu OMS nie jest poprawnie ustawiony. Aby to przetestować, wykonaj następujące czynności:

  1. Usługa log Analytics nasłuchuje na porcie 1514. Aby zweryfikować, że jest otwarty, uruchom następujące polecenie: `netstat -a | grep 1514`
  1. Powinien zostać wyświetlony portu `1514/tcp` Otwórz. Jeśli tego nie zrobisz, sprawdź, czy omsagent jest poprawnie zainstalowany. Jeśli nie widzisz informacje o porcie syslog port nie jest otwarty na maszynie Wirtualnej.

    a. Sprawdź, czy Agent pakietu OMS jest uruchomiony przy użyciu `ps -ef | grep oms`. Jeśli nie jest uruchomiona, należy uruchomić proces, za pomocą polecenia ` sudo /opt/microsoft/omsagent/bin/service_control start`

    b. Otwórz plik `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

    c. Sprawdź, czy prawidłowego użytkownika i ustawienia grupy jest prawidłowy, podobnie do: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Jeśli plik nie istnieje lub nie użytkownika i ustawienia grupy jest nieprawidłowy, akcje naprawcze, [przygotowania serwera z systemem Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Kolejne kroki
* Użyj [wyszukiwań w dziennikach](log-analytics-log-searches.md) w usłudze Log Analytics, aby wyświetlić szczegółowe VMware obsługi danych.
* [Tworzenie własnych pulpitów nawigacyjnych](log-analytics-dashboards.md) przedstawiający dane hosta VMware.
* [Tworzenie alertów](log-analytics-alerts.md) po wystąpieniu określonych zdarzeń hostów VMware.
