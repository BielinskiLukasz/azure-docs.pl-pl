---
title: Konsoli szeregowej maszyny wirtualnej platformy Azure dla systemu Linux | Dokumentacja firmy Microsoft
description: Dwukierunkowa konsoli szeregowej maszyn wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: a3d59d0e7575721dbb719944f27fd673ba41f469
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963822"
---
# <a name="virtual-machine-serial-console-for-linux"></a>Konsola szeregowa maszyny wirtualnej dla systemu Linux

Konsoli szeregowej maszyny wirtualnej (VM) w witrynie Azure portal zapewnia dostęp do konsoli usługi oparte na tekście dla maszyn wirtualnych systemu Linux. To połączenie szeregowe łączy do portu szeregowego COM1 maszyny wirtualnej, zapewniając dostęp do niego niezależnie od stanu sieci lub systemu operacyjnego maszyny wirtualnej. Dostęp do konsoli szeregowej maszyny wirtualnej jest możliwe tylko przy użyciu witryny Azure portal. Jest ona dozwolona tylko dla tych użytkowników, którzy mają dostęp do rolę Współautor maszyny wirtualnej lub nowszego do maszyny wirtualnej. 

Konsola szeregowa dokumentację dotyczącą maszyn wirtualnych Windows, zobacz [Konsola szeregowa maszyny wirtualnej dla Windows](../windows/serial-console.md).

> [!NOTE] 
> Konsoli szeregowej dla maszyn wirtualnych jest ogólnie dostępna w regionach platformy Azure na świecie. Nie jest jeszcze dostępne w Azure dla instytucji rządowych lub chmury chińskiej wersji platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne 

- Maszyna wirtualna, w którym uzyskujesz dostęp do konsoli szeregowej, należy użyć modelu wdrażania usługi resource management. W przypadku wdrożeń klasycznych nie są obsługiwane. 

- Maszyna wirtualna, w którym uzyskujesz dostęp do konsoli szeregowej muszą mieć [diagnostykę rozruchu](boot-diagnostics.md) włączone. 

    ![Ustawienia diagnostyki rozruchu](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Konta, które używa konsoli szeregowej muszą mieć [rola Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) dla maszyny Wirtualnej i [diagnostykę rozruchu](boot-diagnostics.md) konta magazynu: 

    - Maszyna wirtualna, w którym uzyskujesz dostęp do konsoli szeregowej, musisz mieć konto opartego na hasłach. Możesz je utworzyć za pomocą [Resetuj hasło](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkcji rozszerzenia dostępu do maszyny Wirtualnej. Wybierz **Resetuj hasło** z **pomoc techniczna i rozwiązywanie problemów z** sekcji. 

    - Ustawienia specyficzne dla dystrybucji systemu Linux, zobacz [konsoli szeregowej dostępności dystrybucji systemu Linux](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsoli szeregowej dla maszyn wirtualnych jest dostępna tylko za pośrednictwem witryny Azure portal:

  1. Otwórz [portal Azure](https://portal.azure.com).

  1. W menu po lewej stronie wybierz **maszyn wirtualnych**.

  1. Wybierz maszynę Wirtualną na liście. Zostanie otwarta strona Przegląd dla maszyny Wirtualnej.

  1. Przewiń w dół do **pomoc techniczna i rozwiązywanie problemów z** i wybierz pozycję **konsoli szeregowej**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

     ![Okna konsoli szeregowej systemu Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Konsoli szeregowej wymaga użytkownika lokalnego z skonfigurowanym hasłem. Maszyny wirtualne skonfigurowane tylko przy użyciu klucza publicznego SSH nie będzie mógł zalogować się do konsoli szeregowej. Aby utworzyć użytkownika lokalnego przy użyciu hasła, użyj [rozszerzenie VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), który jest dostępny w portalu, wybierając **Resetuj hasło** w witrynie Azure portal i Utwórz użytkownika lokalnego przy użyciu hasła.
> Możesz także zresetować hasło administratora na swoim koncie przez [rozruchu w trybie jednego użytkownika przy użyciu programu GRUB](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Konsola szeregowa dostępności dystrybucji systemu Linux
Na konsoli szeregowej działała poprawnie system operacyjny gościa, należy określić do odczytywania i zapisywania komunikatów konsoli do portu szeregowego. Większość [dystrybucje zalecane dla systemu Linux platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) zostały skonfigurowane domyślnie konsoli szeregowej. Wybieranie **konsoli szeregowej** w **pomoc techniczna i rozwiązywanie problemów z** części witryny Azure portal zapewnia dostęp do konsoli szeregowej. 

Dystrybucja      | Dostęp do konsoli szeregowej
:-----------|:---------------------
Red Hat Enterprise Linux    | Domyślnie dostęp do konsoli szeregowej. 
CentOS      | Domyślnie dostęp do konsoli szeregowej. 
Ubuntu      | Domyślnie dostęp do konsoli szeregowej.
CoreOS      | Domyślnie dostęp do konsoli szeregowej.
SUSE        | Nowsze obrazy w systemie SLES dostępne na platformie Azure mają dostęp do konsoli szeregowej, domyślnie włączone. Jeśli używasz starszych wersji w systemie SLES (10 lub starszy) na platformie Azure, zobacz [artykuł bazy wiedzy](https://www.novell.com/support/kb/doc.php?id=3456486) umożliwiające konsoli szeregowej. 
Oracle Linux        | Domyślnie dostęp do konsoli szeregowej.
Niestandardowych obrazów systemu Linux     | Aby włączyć konsoli szeregowej niestandardowego obrazu maszyny Wirtualnej systemu Linux, Włącz dostęp do konsoli w pliku */etc/inittab* systemem terminalu `ttyS0`. Na przykład: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Aby uzyskać więcej informacji na temat prawidłowo tworzenia obrazów niestandardowych, zobacz [tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure](https://aka.ms/createuploadvhd). Jeśli tworzysz niestandardowy jądra, należy rozważyć włączenie tych flag jądra: `CONFIG_SERIAL_8250=y` i `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Plik konfiguracji znajduje się w */boot/* ścieżki.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Typowe scenariusze dotyczące uzyskiwania dostępu do konsoli szeregowej 
Scenariusz          | Akcje w konsoli szeregowej                
:------------------|:-----------------------------------------
Uszkodzony *FSTAB* pliku | Naciśnij klawisz **Enter** klawisz, aby kontynuować, a następnie użyj edytora tekstów, aby naprawić *FSTAB* pliku. Konieczne może być w trybie jednego użytkownika, aby to zrobić. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) i [Użyj konsoli szeregowej, dostęp do programu GRUB i trybie jednego użytkownika](serial-console-grub-single-user-mode.md).
Reguły zapory niepoprawne | Dostęp do konsoli szeregowej i naprawić iptables. 
System plików uszkodzenie/wyboru | Dostęp do konsoli szeregowej i odzyskiwanie systemu plików. 
Problemy z konfiguracją protokołu RDP/SSH | Dostęp do konsoli szeregowej i zmienić ustawienia. 
Blokowanie sieci w systemie| W witrynie Azure portal do zarządzania systemem, należy uzyskać dostęp do konsoli szeregowej. 
Interakcja z programu inicjującego | Program GRUB dostęp z konsoli szeregowej. Aby uzyskać więcej informacji, zobacz [Użyj konsoli szeregowej, dostęp do programu GRUB i trybie jednego użytkownika](serial-console-grub-single-user-mode.md). 

## <a name="disable-the-serial-console"></a>Wyłącz konsoli szeregowej
Domyślnie wszystkie subskrypcje mają dostęp do konsoli szeregowej włączone dla wszystkich maszyn wirtualnych. Można wyłączyć konsoli szeregowej na poziomie subskrypcji lub na poziomie maszyny Wirtualnej.

> [!NOTE] 
> Aby włączyć lub wyłączyć konsoli szeregowej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Uprawnienia te obejmują role administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

### <a name="subscription-level-disable"></a>Wyłącz poziom subskrypcji
Można wyłączyć dla całej subskrypcji, za pośrednictwem konsoli szeregowej [Wyłącz konsoli wywołania interfejsu API REST](https://docs.microsoft.com/rest/api/serialconsole/console/console_disableconsole). Możesz użyć **wypróbuj** funkcji dostępnych na tej stronie dokumentacji interfejsu API, wyłączyć lub włączyć konsoli szeregowej dla subskrypcji. Wprowadź swój identyfikator subskrypcji dla **subscriptionId**, wprowadź **domyślne** dla **domyślne**, a następnie wybierz pozycję **Uruchom**. Poleceń interfejsu wiersza polecenia platformy Azure nie są jeszcze dostępne.

![Wypróbuj interfejs API REST](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Alternatywnie można następujący zestaw poleceń powłoki bash w usłudze Cloud Shell wyłączanie, włączanie i wyświetlanie wyłączone stanu konsoli szeregowej subskrypcji: 

* Aby wyświetlić stan wyłączenia konsoli szeregowej dla subskrypcji:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Aby wyłączyć konsoli szeregowej subskrypcji:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Aby włączyć konsoli szeregowej subskrypcji:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Wyłącz poziomie maszyny Wirtualnej
Konsoli szeregowej można wyłączyć dla określonej maszyny Wirtualnej przez wyłączenie ustawienia diagnostyki rozruchu dla tej maszyny Wirtualnej. Wyłącz funkcję diagnostyki rozruchu z witryny Azure portal, aby wyłączyć konsoli szeregowej maszyny wirtualnej.

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowej 

### <a name="access-security"></a>Zabezpieczenia dostępu 
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają dostęp do roli z [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub nowszej, aby maszyna wirtualna. Jeśli Twoja dzierżawa usługi Azure Active Directory wymaga uwierzytelniania wieloskładnikowego (MFA), a następnie dostęp do konsoli szeregowej będą także potrzebować MFA, ponieważ dostęp do konsoli szeregowej za pośrednictwem [witryny Azure portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane, które są wysyłane w obie strony są szyfrowane w sieci.

### <a name="audit-logs"></a>Dzienniki inspekcji
Dostęp do konsoli szeregowej jest aktualnie zalogowany [diagnostykę rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) dzienniki maszyny wirtualnej. Dostęp do tych dzienników są własnością i kontrolowane przez administratora maszyny wirtualnej platformy Azure.  

>[!CAUTION] 
Żadne hasła dostępu do konsoli są rejestrowane. Jednak jeśli polecenia uruchamiane w ramach konsoli zawierają lub danych wyjściowych haseł, kluczy tajnych, nazwy użytkowników lub jakąkolwiek inną formę identyfikowalne dane osobowe (PII), te będą zapisywane do dzienników diagnostyki rozruchu maszyny Wirtualnej. One będą zapisywane wraz z wszystkich innych widocznych tekstu, jako część wykonania wstecz przewijania konsoli szeregowej funkcji. Te dzienniki są cykliczne i tylko osoby z uprawnieniami do odczytu do konta magazynu diagnostyki mieli do nich dostęp. Jednak zaleca się następujące najlepsze rozwiązanie polegające na przy użyciu pulpitu zdalnego dla wszystkich elementów, które mogą obejmować wpisów tajnych i/lub dane osobowe. 

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsoli szeregowej i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie odłączony, a drugi użytkownik nawiązał połączenie z tą samą sesją.

>[!CAUTION] 
Oznacza to, że użytkownik, który jest odłączony nie będzie można wylogować. Możliwość wymuszenia wylogowania po rozłączenia (przy użyciu SIGHUP lub podobny mechanizm) nadal działa w planach. Dla Windows jest automatyczne włączono w specjalnych administracyjne Console (Konsola SAC); limit czasu Jednak dla systemu Linux, można skonfigurować ustawienie limitu czasu w terminalu. Aby to zrobić, Dodaj `export TMOUT=600` w swojej *.bash_profile* lub *podstawową* pliku dla użytkowników, używasz do logowania do konsoli. To ustawienie przekroczy limit czasu sesji, po upływie 10 minut.

## <a name="accessibility"></a>Ułatwienia dostępu
Dostępność jest kluczowym dla konsoli szeregowej platformy Azure. W tym celu upewniliśmy się, że konsoli szeregowej jest w pełni dostępna.

### <a name="keyboard-navigation"></a>Nawigowanie przy użyciu klawiatury
Użyj **kartę** kluczowe na klawiaturze, aby przejść w interfejsie konsoli szeregowej w witrynie Azure portal. Twoja lokalizacja zostanie wyróżniony na ekranie. Aby opuścić fokusu okna konsoli szeregowej, naciśnij klawisz **Ctrl**+**F6** na klawiaturze.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Użyj konsoli szeregowej przy użyciu czytnika zawartości ekranu
Konsoli szeregowej ma wbudowaną obsługę czytników zawartości ekranu. Przemieszczać się przy użyciu czytnika zawartości ekranu włączone umożliwi tekst alternatywny dla aktualnie wybranego przycisku zostanie odczytany na głos przez czytnik zawartości ekranu.

## <a name="errors"></a>Błędy
Ponieważ większość błędów przejściowych, ponawianie próby połączenia można często je naprawić. W poniższej tabeli przedstawiono listę błędy i środki zaradcze.

Błąd                            |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu dla  *&lt;VMNAME&gt;*. Aby korzystać z konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostykę rozruchu](boot-diagnostics.md) włączone. 
Maszyna wirtualna jest w stanie zatrzymania, przydział zostanie cofnięty. Uruchom maszynę Wirtualną i spróbuj ponownie nawiązać połączenie konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym uzyskać dostęp do konsoli szeregowej.
Nie masz wymaganych uprawnień do tej maszyny Wirtualnej za pomocą konsoli szeregowej. Upewnij się, że co najmniej uprawnienia roli Współautor maszyny wirtualnej.| Dostęp do konsoli szeregowej wymaga pewnych uprawnień. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu  *&lt;STORAGEACCOUNTNAME&gt;*. Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga pewnych uprawnień. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).
Gniazda sieci Web został zamknięty lub nie można otworzyć. | Może być konieczne do listy dozwolonych `*.console.azure.com`. Bardziej szczegółowe, ale dłużej podejście jest do listy dozwolonych adresów [zakresów adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są stosunkowo regularnie zmieniane.
Odpowiedź "Dostęp zabroniony" napotkano podczas uzyskiwania dostępu do konta magazynu diagnostyki rozruchu dla tej maszyny Wirtualnej. | Upewnij się, że tej diagnostyki rozruchu nie ma zapory konta. Konto magazynu diagnostyki rozruchu dostępny jest niezbędne do konsoli szeregowej funkcjonowania.

## <a name="known-issues"></a>Znane problemy 
Mamy świadomość problemy z konsoli szeregowej. Poniżej przedstawiono listę tych problemów oraz kroki dotyczące ograniczania ryzyka.

Problem                           |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Naciśnięcie klawisza **Enter** po transparent połączenia nie powoduje, że monit logowania do wyświetlenia. | Aby uzyskać więcej informacji, zobacz [Hitting wprowadź, nic nie robi](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Ten problem może wystąpić, jeśli używasz niestandardowej maszyny Wirtualnej, urządzenia ze wzmocnionymi zabezpieczeniami lub konfiguracji programu GRUB, powodujący systemu Linux nie można prawidłowo nawiązać portu szeregowego.
Tekst konsoli szeregowej wymaga tylko część rozmiaru ekranu (często po nim za pomocą edytora tekstów). | Negocjowanie o rozmiar okna nie obsługują konsoli szeregowej ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), co oznacza, że nie będzie brak sygnału SIGWINCH wysłanych do zaktualizowania rozmiaru ekranu i maszyna wirtualna będzie miała żadnej znajomości rozmiar usługi terminalowe. Zainstaluj xterm lub podobnej użyteczności, aby zapewnić Ci `resize` polecenia, a następnie uruchom `resize`.
Wklejanie ciągów długich nie działa. | Konsoli szeregowej ogranicza długość ciągów w terminalu, aby 2048 znaków, aby zapobiec przeciążeniu przepustowość portu szeregowego.


## <a name="frequently-asked-questions"></a>Często zadawane pytania 

**PYTANIA I ODPOWIEDZI. Jak wysłać opinię?**

A. Przekazać opinię, tworząc problem w usłudze GitHub w https://aka.ms/serialconsolefeedback. Można również (mniej preferowany), możesz wysłać opinię, za pośrednictwem azserialhelp@microsoft.com lub w maszynie wirtualnej kategorii http://feedback.azure.com.

**PYTANIA I ODPOWIEDZI. Konsoli szeregowej obsługuje kopiowania/wklejania?**

A. Tak. Użyj **Ctrl**+**Shift**+**C** i **Ctrl**+**Shift** + **V** do kopiowania i wklejania w terminalu.

**PYTANIA I ODPOWIEDZI. Czy można użyć konsoli szeregowej, zamiast połączenia SSH?**

A. Podczas tego użycia może wydawać się to technicznie możliwe, konsoli szeregowej ma służyć przede wszystkim podczas rozwiązywania problemów w sytuacjach, w których łączność za pośrednictwem protokołu SSH nie jest możliwe. Zaleca się przy użyciu konsoli szeregowej jako zamiennika SSH z następujących powodów:

- Konsoli szeregowej nie musi tak dużej ilości przepustowości jako protokołu SSH. Ponieważ jest ona połączenie tylko do tekstu, więcej interakcje ciężkich graficznego interfejsu użytkownika są trudne.
- Konsola szeregowa jest obecnie możliwe tylko przy użyciu nazwy użytkownika i hasła. Ponieważ klucze SSH są znacznie bezpieczniejsze niż kombinacji nazwy użytkownika/hasła, z punktu widzenia zabezpieczeń logowania zalecamy SSH za pośrednictwem konsoli szeregowej.

**PYTANIA I ODPOWIEDZI. Kto może włączyć lub wyłączyć konsoli szeregowej dla mojej subskrypcji?**

A. Aby włączyć lub wyłączyć konsoli szeregowej na poziomie całej subskrypcji, musisz mieć uprawnienia do zapisu do subskrypcji. Role, które mają uprawnienia do zapisu obejmują role administratora lub właściciela. Role niestandardowe może również mieć uprawnienia do zapisu.

**PYTANIA I ODPOWIEDZI. Kto ma dostęp do konsoli szeregowej dla mojej maszyny Wirtualnej?**

A. Konieczne jest posiadanie rola Współautor maszyny wirtualnej lub nowszej, aby uzyskać dostęp do konsoli szeregowej maszyny Wirtualnej maszyny Wirtualnej. 

**PYTANIA I ODPOWIEDZI. Moje konsoli szeregowej nie są wyświetlane wszystko, co należy zrobić?**

A. Obraz jest prawdopodobnie nieprawidłowo skonfigurowane, aby uzyskać dostęp do konsoli szeregowej. Aby uzyskać informacji o konfigurowaniu swój obraz, aby umożliwić konsoli szeregowej, zobacz [konsoli szeregowej dostępności dystrybucji systemu Linux](#serial-console-linux-distribution-availability).

**PYTANIA I ODPOWIEDZI. Konsoli szeregowej jest dostępna dla zestawów skalowania maszyn wirtualnych?**

A. W tej chwili dostęp do konsoli szeregowej dla wystąpień zestawu skalowania maszyn wirtualnych nie jest obsługiwane.

**PYTANIA I ODPOWIEDZI. Jeśli Moja maszyna wirtualna skonfigurować przy użyciu tylko uwierzytelnianie klucza SSH, można nadal mogę korzystać konsoli szeregowej nawiązywania połączenia z maszyną Wirtualną?**

A. Tak. Ponieważ konsoli szeregowej nie wymaga kluczy SSH, wystarczy ustawić kombinację nazwy użytkownika i hasła. Możesz to zrobić, wybierając **Resetuj hasło** w witrynie Azure portal i za pomocą tych poświadczeń do logowania do konsoli szeregowej.

## <a name="next-steps"></a>Kolejne kroki
* Użyj konsoli szeregowej do [dostępu CHODNIKÓW i trybie jednego użytkownika](serial-console-grub-single-user-mode.md).
* Użyj konsoli szeregowej dla [wywołania NMI i SysRq](serial-console-nmi-sysrq.md).
* Dowiedz się, jak korzystać z konsoli szeregowej do [Włącz CHODNIKÓW w różnych dystrybucjach](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* Jest również dostępny dla konsoli szeregowej [maszyn wirtualnych Windows](../windows/serial-console.md).
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md).

