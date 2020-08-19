---
title: Wdróż usługę przesyłania dalej dzienników, aby połączyć dane CEF z platformą Azure — wskaźnikiem rozwoju | Microsoft Docs
description: Dowiedz się, jak wdrożyć agenta, aby połączyć dane CEF z platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: a7d7c7b7236841835866ccb7786e7e4eab767c1f
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565591"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Krok 1. wdrażanie usługi przesyłania dalej dzienników


W tym kroku wyznaczysz i skonfigurujesz maszynę z systemem Linux, która będzie przekazywać dzienniki z rozwiązania zabezpieczeń do obszaru roboczego wskaźnikowego platformy Azure. Ta maszyna może być maszyną fizyczną lub wirtualną w środowisku lokalnym, maszyną wirtualną platformy Azure lub MASZYNą wirtualną w innej chmurze. Przy użyciu dostarczonego linku uruchomisz skrypt na wyznaczeniym komputerze, który wykonuje następujące zadania:
- Instaluje agenta Log Analytics dla systemu Linux (znanego również jako agent pakietu OMS) i konfiguruje go do następujących celów:
    - nasłuchiwanie komunikatów CEF z wbudowanego demona dziennika systemu Linux na porcie TCP 25226
    - bezpieczne wysyłanie komunikatów za pośrednictwem protokołu TLS do obszaru roboczego wskaźnikowego platformy Azure, w którym są analizowane i wzbogacane

- Konfiguruje wbudowane demona dziennika systemu Linux (rsyslog. d/dziennik systemowy) dla następujących celów:
    - nasłuchiwanie komunikatów dziennika systemowego z rozwiązań zabezpieczeń na porcie TCP 514
    - przekazywanie tylko komunikatów identyfikowanych jako CEF do agenta Log Analytics na hoście lokalnym przy użyciu portu TCP 25226
 
## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć podwyższony poziom uprawnień (sudo) na wyznaczeniu maszyny z systemem Linux.
- Na komputerze z systemem Linux musi być zainstalowany język Python.<br>Użyj `python -version` polecenia, aby sprawdzić.
- Komputer z systemem Linux nie może być połączony z żadnym obszarem roboczym platformy Azure przed zainstalowaniem agenta Log Analytics.

## <a name="run-the-deployment-script"></a>Uruchamianie skryptu wdrażania
 
1. W menu nawigacji wskaźnikowej platformy Azure kliknij pozycję **Łączniki danych**. Z listy łączników kliknij kafelek **Common Event format (CEF)** , a następnie przycisk **Otwórz stronę łącznika** w prawym dolnym rogu. 

1. W obszarze **1,2 Zainstaluj moduł CEF na komputerze z systemem Linux**, skopiuj link podany w obszarze **Uruchom następujący skrypt, aby zainstalować i zastosować moduł zbierający CEF**, lub poniższy tekst:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Gdy skrypt jest uruchomiony, upewnij się, że nie są wyświetlane żadne komunikaty o błędach lub ostrzeżeniach.

> [!NOTE]
> **Używanie tego samego komputera do przesyłania zarówno zwykłego dziennika systemowego *, jak i* komunikatów CEF**
>
> Jeśli planujesz używać tego komputera usługi przesyłania dalej dzienników do przesyłania dalej [komunikatów](connect-syslog.md) dziennika systemowego i CEF, a następnie w celu uniknięcia duplikowania zdarzeń do tabel dziennika systemowego i CommonSecurityLog:
>
> 1. Na każdym komputerze źródłowym, który wysyła dzienniki do usługi przesyłania dalej w formacie CEF, należy edytować plik konfiguracji dziennika systemowego w celu usunięcia obiektów używanych do wysyłania komunikatów CEF. W ten sposób obiekty, które są wysyłane w CEF, nie będą również wysyłane w dzienniku systemu. Szczegółowe instrukcje można znaleźć w temacie [Konfigurowanie dziennika systemowego w agencie systemu Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Na tych maszynach należy uruchomić następujące polecenie, aby wyłączyć synchronizację agenta z konfiguracją dziennika systemowego na platformie Azure. Dzięki temu zmiana konfiguracji wprowadzona w poprzednim kroku nie zostanie zastępować.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

Przejdź do [kroku 2: Skonfiguruj rozwiązanie zabezpieczeń do przesyłania dalej komunikatów CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Opisany skrypt wdrożenia

Poniżej znajduje się opis poleceń wykonywanych przez polecenie skryptu wdrażania.

Wybierz demona dziennika systemu, aby wyświetlić odpowiedni opis.

# <a name="rsyslog-daemon"></a>[Demon rsyslog](#tab/rsyslog)

1. **Pobieranie i Instalowanie agenta Log Analytics:**

    - Pobiera skrypt instalacyjny dla agenta systemu Linux Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instaluje agenta Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurowanie demona dziennika systemowego:**

    1. Otwiera port 514 dla komunikacji TCP przy użyciu pliku konfiguracji dziennika systemowego `/etc/rsyslog.conf` .

    1. Konfiguruje demona do przesyłania dalej komunikatów CEF do agenta Log Analytics na porcie TCP 25226, wstawiając specjalny plik konfiguracji `security-config-omsagent.conf` do katalogu demona dziennika systemu `/etc/rsyslog.d/` .

        Zawartość `security-config-omsagent.conf` pliku:

        ```console
        :rawmsg, regex, "CEF"|"ASA"
        *.* @@127.0.0.1:25226
        ```

1. **Ponowne uruchamianie demona dziennika systemu**

    `service rsyslog restart`

1. **Ustawianie konfiguracji agenta Log Analytics na nasłuchiwanie na porcie 25226 i przekazywanie komunikatów CEF do usługi Azure wskaźnikowego**

    1. Pobiera konfigurację z repozytorium usługi GitHub Agent Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Uruchamia ponownie agenta Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[Demon dziennika systemu](#tab/syslogng)

1. **Pobieranie i Instalowanie agenta Log Analytics:**

    - Pobiera skrypt instalacyjny dla agenta systemu Linux Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instaluje agenta Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurowanie demona dziennika systemowego:**

    1. Otwiera port 514 dla komunikacji TCP przy użyciu pliku konfiguracji dziennika systemowego `/etc/syslog-ng/syslog-ng.conf` .

    1. Konfiguruje demona do przesyłania dalej komunikatów CEF do agenta Log Analytics na porcie TCP 25226, wstawiając specjalny plik konfiguracji `security-config-omsagent.conf` do katalogu demona dziennika systemu `/etc/syslog-ng/conf.d/` .

        Zawartość `security-config-omsagent.conf` pliku:

        ```console
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Ponowne uruchamianie demona dziennika systemu**

    `service syslog-ng restart`

1. **Ustawianie konfiguracji agenta Log Analytics na nasłuchiwanie na porcie 25226 i przekazywanie komunikatów CEF do usługi Azure wskaźnikowego**

    1. Pobiera konfigurację z repozytorium usługi GitHub Agent Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Uruchamia ponownie agenta Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób wdrażania agenta Log Analytics w celu połączenia urządzeń CEF z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

