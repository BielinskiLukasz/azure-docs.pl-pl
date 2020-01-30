---
title: Śledzenie zmian za pomocą Azure Automation
description: Change Tracking rozwiązanie pomaga identyfikować zmiany oprogramowania i usług systemu Windows, które występują w danym środowisku.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 83babd65fdf22ab40b0137d93a1cbe7f1fd7ff04
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844806"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Śledź zmiany w środowisku przy użyciu rozwiązania Change Tracking

W tym artykule opisano sposób korzystania z rozwiązania Change Tracking w celu łatwego identyfikowania zmian w środowisku programu. Rozwiązanie śledzi następujące zmiany konfiguracji, aby ułatwić identyfikowanie problemów operacyjnych:

- Oprogramowanie systemu Windows
- Oprogramowanie systemu Linux (pakiety)

    >[!NOTE]
    >Change Tracking śledzi tylko oprogramowanie, które jest zarządzane za pomocą Menedżera pakietów dystrybucji.

- Pliki systemów Windows i Linux
- Klucze rejestru systemu Windows
- Usługi systemu Windows
- Demony systemu Linux

Zmiany w zainstalowanym oprogramowaniu, usługach systemu Windows, rejestrze systemu Windows i na monitorowanych serwerach są wysyłane do usługi Azure Monitor w chmurze w celu przetworzenia. Logika jest stosowana do odebranych danych, a usługa w chmurze rejestruje dane. Korzystając z informacji na pulpicie nawigacyjnym Change Tracking, można łatwo zobaczyć zmiany wprowadzone w infrastrukturze serwera.

> [!NOTE]
> Azure Automation Change Tracking śledzi zmiany w maszynach wirtualnych. Aby śledzić zmiany właściwości Azure Resource Manager, zobacz [historię zmian](../governance/resource-graph/how-to/get-resource-changes.md)grafu zasobów platformy Azure.

## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows

Windows agent oficjalnie obsługuje następujące wersje systemu operacyjnego Windows:

* Windows Server 2008 R2 lub nowszy

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Następujące dystrybucje systemu Linux są oficjalnie obsługiwane. Jednak Agent systemu Linux może być również uruchamiany na innych dystrybucjach, których nie ma na liście. O ile nie zaznaczono inaczej, wszystkie wersje pomocnicze są obsługiwane dla każdej wymienionej wersji głównej.

### <a name="64-bit"></a>64 — bit

* CentOS 6 i 7
* Amazon Linux 2017,09
* Oracle Linux 6 i 7
* Red Hat Enterprise Linux Server 6 i 7
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS i 18,04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 — bit

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 i 9
* Ubuntu Linux 14,04 LTS i 16,04 LTS

## <a name="onboard"></a>Włączanie Change Tracking i spisu

Aby rozpocząć śledzenie zmian, należy włączyć rozwiązanie Change Tracking i spisu. Istnieje wiele sposobów dołączania maszyn do Change Tracking i spisu. Poniżej przedstawiono zalecane i obsługiwane sposoby dołączania rozwiązania.

* [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
* [Przeglądanie wielu maszyn](automation-onboard-solutions-from-browse.md)
* [Na koncie usługi Automation](automation-onboard-solutions-from-automation-account.md)
* [Z elementem Runbook Azure Automation](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Konfigurowanie Change Tracking i spisu

Aby dowiedzieć się, jak dołączyć komputery do rozwiązania, odwiedź: dołączanie [rozwiązań automatyzacji](automation-onboard-solutions-from-automation-account.md). Po dołączeniu maszyny przy użyciu rozwiązania Change Tracking i spisu można skonfigurować elementy do śledzenia. Po włączeniu nowego pliku lub klucza rejestru do śledzenia jest on dostępny dla Change Tracking i spisu.

Do śledzenia zmian w plikach w systemach Windows i Linux używane są skróty MD5 plików. Te skróty są następnie używane do wykrywania, czy wprowadzono zmiany od ostatniego spisu.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorowanie integralności plików w Azure Security Center

Azure Security Center dodaliśmy monitorowanie integralności plików (FIM) wbudowane w usłudze Azure Change Tracking. Chociaż program FIM monitoruje tylko pliki i rejestry, pełne rozwiązanie Change Tracking obejmuje również:

- Zmiany oprogramowania
- Usługi systemu Windows
- Demony systemu Linux

Jeśli włączono już program FIM i chcesz wypróbować pełne rozwiązanie Change Tracking, musisz wykonać następujące czynności. Ustawienia nie są usuwane przez ten proces.

> [!NOTE]
> Włączenie pełnego rozwiązania Change Tracking może spowodować naliczenie dodatkowych opłat, aby uzyskać więcej informacji, zobacz [Cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

1. Usuń rozwiązanie monitorowania, przechodząc do obszaru roboczego i lokalizowanie go na [liście zainstalowanych rozwiązań monitorowania](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania, a następnie kliknij pozycję Usuń, zgodnie z opisem w temacie [usuwanie rozwiązania monitorowania](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Ponownie włącz rozwiązanie, przechodząc do konta usługi Automation i wybierając pozycję **Change Tracking** z menu zasób w obszarze **Zarządzanie konfiguracją**.
4. Potwierdź szczegóły ustawienia obszaru roboczego, a następnie kliknij pozycję **Włącz**.

### <a name="configure-linux-files-to-track"></a>Konfigurowanie plików systemu Linux do śledzenia

Wykonaj następujące kroki, aby skonfigurować śledzenie plików na komputerach z systemem Linux:

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie **Change Tracking** wybierz pozycję **pliki systemu Linux**, a następnie kliknij pozycję **+ Dodaj** , aby dodać nowy plik do śledzenia.
3. Na stronie **Dodawanie pliku systemu Linux dla Change Tracking**wprowadź informacje dotyczące pliku lub katalogu, który ma być śledzony, i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy to ustawienie jest stosowane.        |
|Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
|Grupa     | Nazwa grupy do logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzenia pliku. Na przykład: "/etc/*. conf"       |
|Typ ścieżki     | Typ elementu, który ma być śledzony, możliwe wartości to plik i katalog.        |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Użyj polecenia Sudo     | To ustawienie określa, czy podczas sprawdzania elementu jest używane polecenie sudo.         |
|Linki     | To ustawienie określa, w jaki sposób są obsługiwane linki symboliczne podczas przechodzenia między katalogami.<br> **Ignoruj** — ignoruje linki symboliczne i nie uwzględnia plików/katalogów, do których się odwołuje.<br>**Obserwuj poniższe** linki symboliczne podczas rekursji, a także zawiera pliki/katalogi, do których się odwołuje.<br>**Zarządzanie** — następuje po linków symbolicznych i umożliwia zmianę zwracanej zawartości.     |
|Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: **True** lub **False**.|

> [!NOTE]
> Opcja linków „Zarządzaj” nie jest zalecana. Pobieranie zawartości plików nie jest obsługiwane.

### <a name="configure-windows-files-to-track"></a>Konfigurowanie plików systemu Windows do śledzenia

Wykonaj następujące kroki, aby skonfigurować śledzenie plików na komputerach z systemem Windows:

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie **Change Tracking** wybierz pozycję **pliki systemu Windows**, a następnie kliknij pozycję **+ Dodaj** , aby dodać nowy plik do śledzenia.
3. Na stronie **Dodawanie pliku systemu Windows dla Change Tracking**wprowadź informacje dotyczące pliku do śledzenia i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy to ustawienie jest stosowane.        |
|Nazwa elementu     | Przyjazna nazwa pliku do śledzenia.        |
|Grupa     | Nazwa grupy do logicznego grupowania plików.        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „c:\temp\\\*.txt”<br>Możesz użyć również zmiennych środowiskowych, takich jak „%winDir%\System32\\\*.*”       |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Przekaż zawartość pliku dla wszystkich ustawień| Włącza lub wyłącza przekazywanie zawartości pliku dla śledzonych zmian. Dostępne opcje: **True** lub **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Ustawienia wieloznaczne, rekursji i środowiska

Rekursja umożliwia określenie symboli wieloznacznych, aby uprościć śledzenie dla katalogów i zmiennych środowiskowych, aby umożliwić śledzenie plików w środowiskach z wieloma lub dynamicznymi nazwami dysków. Na poniższej liście przedstawiono typowe informacje, które należy znać podczas konfigurowania rekursji:

* Do śledzenia wielu plików wymagane są symbole wieloznaczne
* Jeśli używasz symboli wieloznacznych, mogą one być używane tylko w ostatnim segmencie ścieżki. (na przykład `c:\folder\*file*` lub `/etc/*.conf`)
* Jeśli zmienna środowiskowa ma nieprawidłową ścieżkę, walidacja powiedzie się, ale ta ścieżka zakończy się niepowodzeniem po uruchomieniu spisu.
* Należy unikać stosowania ścieżek ogólnych, takich jak `c:\*.*` podczas ustawiania ścieżki, ponieważ spowoduje to zbyt wiele przepływających folderów.

## <a name="configure-file-content-tracking"></a>Konfiguruj śledzenie zawartości plików

Zawartość pliku z zawartością pliku Change Tracking można wyświetlić przed zmianą i po niej. Jest on dostępny dla plików systemu Windows i Linux, dla każdej zmiany w pliku, zawartość pliku jest przechowywana na koncie magazynu, a plik jest wyświetlany przed zmianą, wbudowaną lub obok siebie. Aby dowiedzieć się więcej, zobacz [Wyświetlanie zawartości śledzonego pliku](change-tracking-file-contents.md).

![Wyświetlanie zmian w pliku](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Konfigurowanie kluczy rejestru systemu Windows do śledzenia

Wykonaj następujące kroki, aby skonfigurować śledzenie kluczy rejestru na komputerach z systemem Windows:

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. Kliknij pozycję **Edytuj ustawienia** (symbol koła zębatego).
2. Na stronie **Change Tracking** wybierz pozycję **Rejestr systemu Windows**, a następnie kliknij pozycję **+ Dodaj** , aby dodać nowy klucz rejestru do śledzenia.
3. W polu **Dodaj rejestr systemu Windows dla Change Tracking**wprowadź informacje dotyczące klucza do śledzenia i kliknij przycisk **Zapisz**.

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy to ustawienie jest stosowane.        |
|Nazwa elementu     | Przyjazna nazwa klucza rejestru do śledzenia.        |
|Grupa     | Nazwa grupy do logicznego grupowania kluczy rejestru.        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzenia klucza rejestru. Na przykład: "HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Start"      |

## <a name="limitations"></a>Ograniczenia

Rozwiązanie Change Tracking nie obsługuje obecnie następujących elementów:

* Rekursja śledzenia rejestru systemu Windows
* Systemy plików sieciowych
* Różne metody instalacji nie są śledzone
* pliki *. exe nie są śledzone dla systemu Windows

Inne ograniczenia:

* Kolumny **Maksymalny rozmiar pliku** i wartości są nieużywane w bieżącej implementacji.
* Jeśli zbierasz więcej niż 2500 plików w cyklu zbierania danych o 30 minutach, wydajność rozwiązania może być obniżona.
* Gdy ruch sieciowy jest wysoki, wyświetlanie rekordów może potrwać do 6 godzin.
* Jeśli zmodyfikujesz konfigurację podczas zamykania komputera, komputer może opublikować zmiany, które należą do poprzedniej konfiguracji.

## <a name="known-issues"></a>Znane problemy

Rozwiązanie Change Tracking ma obecnie następujące problemy:

* Aktualizacje poprawek nie są zbierane na maszynach podstawowych RS3 systemu Windows Server 2016.
* Demony systemu Linux może wskazywać zmieniony stan nawet wtedy, gdy nie wprowadzono żadnych zmian. Jest to spowodowane sposobem przechwytywania pola `SvcRunLevels`.

## <a name="change-tracking-data-collection-details"></a>Szczegóły zbierania danych Change Tracking

W poniższej tabeli przedstawiono częstotliwość zbierania danych dla typów zmian. Dla każdego typu migawka danych bieżącego stanu jest również odświeżana co najmniej co 24 godziny:

| **Zmień typ** | **Częstotliwość** |
| --- | --- |
| Rejestr systemu Windows | 50 minut |
| Plik systemu Windows | 30 minut |
| Plik systemu Linux | 15 minut |
| Usługi systemu Windows | 10 sekund do 30 minut</br> Wartość domyślna: 30 minut |
| Demony systemu Linux | 5 minut |
| Oprogramowanie systemu Windows | 30 minut |
| Oprogramowanie systemu Linux | 5 minut |

W poniższej tabeli przedstawiono limity śledzonych elementów na maszynę dla Change Tracking.

| **Zasób** | **Limit**| **Uwagi** |
|---|---|---|
|Plik|500||
|Rejestr|250||
|Oprogramowanie systemu Windows|250|Nie zawiera poprawek oprogramowania|
|Pakiety systemu Linux|1250||
|Usługi|250||
|Demon|250||

Średnie użycie danych Log Analytics dla maszyny przy użyciu Change Tracking i spisu jest około 40MB miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie w zależności od używanego środowiska. Zalecamy monitorowanie środowiska, aby zobaczyć dokładne użycie.

### <a name="windows-service-tracking"></a>Śledzenie usług systemu Windows

Domyślna częstotliwość zbierania dla usług systemu Windows to 30 minut. Aby skonfigurować częstotliwość, przejdź do **Change Tracking**. W obszarze **Edytuj ustawienia** na karcie **usługi systemu Windows** znajduje się suwak pozwalający zmienić częstotliwość zbierania dla usług systemu Windows od razu o 10 sekund do 30 minut. Przesuń suwak do żądanych częstotliwości i automatycznie go zapisuje.

![Suwak usług systemu Windows](./media/change-tracking/windowservices.png)

Agent śledzi jedynie zmiany, co optymalizuje wydajność agenta. Ustawienie wysokiego progu może pominąć zmiany, jeśli usługa została przywrócona do stanu pierwotnego. Ustawienie częstotliwości na mniejszą wartość umożliwia przechwytywanie zmian, które mogą zostać pominięte w przeciwnym razie.

> [!NOTE]
> Gdy Agent może śledzić zmiany w dół do 10-sekundowego interwału, dane będą nadal wyświetlane w portalu. Zmiany w czasie wyświetlania w portalu są nadal śledzone i rejestrowane.

### <a name="registry-key-change-tracking"></a>Śledzenie zmian klucza rejestru

Celem monitorowania zmian w kluczach rejestru jest wskazanie punktów rozszerzalności, w których można aktywować kod innej firmy i złośliwe oprogramowanie. Na poniższej liście przedstawiono listę wstępnie skonfigurowanych kluczy rejestru. Te klucze są skonfigurowane, ale nie są włączone. Aby śledzić te klucze rejestru, należy włączyć każdą z nich.

> [!div class="mx-tdBreakAll"]
> |Klucz rejestru | Przeznaczenie |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje typowe wpisy Autostart, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w procesie przy użyciu programu Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoruje skrypty uruchamiane podczas uruchamiania.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoruje skrypty uruchamiane przy zamykaniu.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitoruje klucze, które są ładowane przed zalogowaniem się użytkownika do konta systemu Windows. Ten klucz jest używany w przypadku programów 32-bitowych uruchomionych na komputerach z systemem 64-bitowym.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoruje zmiany w ustawieniach aplikacji.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoruje typowe wpisy Autostart, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w procesie przy użyciu programu Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoruje typowe wpisy Autostart, które są podłączane bezpośrednio do Eksploratora Windows i zwykle są uruchamiane w procesie przy użyciu programu Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitory dla rejestracji obsługi nakładki ikon.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitoruje rejestrację procedury obsługi nakładki ikon dla programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitoruje nowe wtyczki obiektów pomocników przeglądarki dla programu Internet Explorer. Służy do uzyskiwania dostępu do Document Object Model (DOM) bieżącej strony i kontrolowania nawigacji.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitoruje nowe wtyczki obiektów pomocników przeglądarki dla programu Internet Explorer. Służy do uzyskiwania dostępu do Document Object Model (DOM) bieżącej strony i kontrolowania nawigacji w przypadku programów 32-bitowych uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitoruje nowe rozszerzenia programu Internet Explorer, takie jak niestandardowe menu narzędzi i niestandardowe przyciski paska narzędzi.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitoruje nowe rozszerzenia programu Internet Explorer, takie jak niestandardowe menu narzędzi i niestandardowe przyciski paska narzędzi dla 32-bitowych programów uruchomionych na komputerach 64-bitowych.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32-bitowe sterowniki skojarzone z wavemapper, wave1 i Wave2, msacm. imaadpcm,. msadpcm,. msgsm610 i vidc. Podobne do sekcji [Drivers] w systemie. Plik INI.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoruje 32-bitowe sterowniki skojarzone z wavemapper, wave1 i Wave2, msacm. imaadpcm,. msadpcm,. msgsm610 i vidc dla programów 32-bitowych uruchomionych na komputerach 64-bitowych. Podobne do sekcji [Drivers] w systemie. Plik INI.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoruje listę znanych lub powszechnie używanych systemowych bibliotek DLL; Ten system uniemożliwia osobom wykorzystywanie słabych uprawnień katalogu aplikacji przez porzucanie w systemie plików DLL systemu koń trojański.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoruje listę pakietów, w których można odbierać powiadomienia o zdarzeniach z usługi Winlogon, model obsługi logowania interaktywnego dla systemu operacyjnego Windows.

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Poniższe adresy są wymagane dla Change Tracking. Komunikacja z tymi adresami odbywa się za pośrednictwem portu 443.

|Azure Public  |Platforma Azure dla instytucji rządowych  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

## <a name="use-change-tracking"></a>Użyj Change Tracking

Po włączeniu rozwiązania można wyświetlić podsumowanie zmian monitorowanych komputerów, wybierając **Change Tracking** w obszarze **Zarządzanie konfiguracją** na koncie usługi Automation.

Możesz wyświetlić zmiany na komputerach, a następnie przejść do szczegółów poszczególnych zdarzeń. Lista rozwijana jest dostępna w górnej części wykresu, aby ograniczyć wykres i szczegółowe informacje na podstawie typu zmiany i przedziałów czasu. Możesz również kliknąć i przeciągnąć na wykres, aby wybrać niestandardowy zakres czasu. **Typ zmiany** będzie jednym z następujących **zdarzeń**, **demonów**, **plików**, **rejestru**, **oprogramowania**, **usług systemu Windows**. Kategoria pokazuje typ zmiany i można **dodawać**, **modyfikować**lub **usuwać**.

![obraz pulpitu nawigacyjnego Change Tracking](./media/change-tracking/change-tracking-dash01.png)

Kliknięcie zmiany lub zdarzenia powoduje wyświetlenie szczegółowych informacji o tej zmianie. Jak widać w przykładzie, typ uruchamiania usługi został zmieniony z ręczny na automatycznie.

![obraz szczegółów śledzenia zmian](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Wyszukaj dzienniki

Oprócz szczegółów dostępnych w portalu wyszukiwania można wykonywać w odniesieniu do dzienników. Na otwartej stronie **Change Tracking** kliknij pozycję **log Analytics**, spowoduje to otwarcie strony **dzienniki** .

### <a name="sample-queries"></a>Przykładowe zapytania

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów zmian zebranych przez to rozwiązanie:

|Zapytanie  |Opis  |
|---------|---------|
|ConfigurationData<br>&#124;gdzie ConfigDataType = = "WindowsServices" i SvcStartupType = = "Auto"<br>&#124;gdzie SvcState = = "zatrzymana"<br>&#124;Podsumuj arg_max (TimeGenerated, *) według oprogramowania, komputera         | Pokazuje najnowsze rekordy spisu dla usług systemu Windows, które zostały ustawione na wartość automatycznie, ale zostały zgłoszone jako zatrzymane<br>Wyniki są ograniczone do najnowszego rekordu dla tego oprogramowania i komputera      |
|Zmianakonfiguracji<br>&#124;gdzie ConfigChangeType = = "oprogramowanie" i ChangeCategory = = "usunięte"<br>&#124;Order by TimeGenerated DESC|Pokazuje rekordy zmian dla usuniętego oprogramowania|

## <a name="alert-on-changes"></a>Alert dotyczący zmian

Kluczową możliwością Change Tracking i spisu jest możliwość tworzenia alertów dotyczących stanu konfiguracji i wszelkich zmian stanu konfiguracji środowiska hybrydowego.

W poniższym przykładzie zrzut ekranu pokazuje, że plik `C:\windows\system32\drivers\etc\hosts` został zmodyfikowany na komputerze. Ten plik jest ważny, ponieważ plik hosts jest używany przez system Windows do rozpoznawania nazw hostów na adresy IP i ma pierwszeństwo przed nawet usługą DNS, co może spowodować problemy z łącznością lub przekierowanie ruchu do złośliwych lub w inny sposób niebezpiecznych witryn sieci Web.

![Wykres przedstawiający zmianę pliku hosts](./media/change-tracking/changes.png)

Aby przeanalizować tę zmianę, przejdź do obszaru wyszukiwanie w dzienniku od kliknięcia **log Analytics**. W przeszukiwaniu dzienników Wyszukaj zmiany zawartości w pliku hosts przy użyciu kwerendy `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. To zapytanie szuka zmian, które zawierały zmiany zawartości plików dla plików, których w pełni kwalifikowana ścieżka zawiera wyraz "hosty". Możesz również poszukać określonego pliku, zmieniając część ścieżki na jej w pełni kwalifikowaną formę (na przykład `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Po powrocie zapytania do żądanych wyników kliknij przycisk **Nowa reguła alertu** w środowisku wyszukiwania dzienników, aby otworzyć stronę tworzenie alertów. Możesz również przejść do tego środowiska za pomocą **Azure monitor** w Azure Portal. W środowisku tworzenia alertów Sprawdź ponownie zapytanie i zmodyfikuj logikę alertów. W takim przypadku alert ma być wyzwalany, jeśli istnieje nawet jedna zmiana została wykryta między wszystkimi maszynami w środowisku.

![Obraz przedstawiający zapytanie o zmianę do śledzenia zmian w pliku hosts](./media/change-tracking/change-query.png)

Po ustawieniu logiki warunku Przypisz grupy akcji, aby wykonać akcje w odpowiedzi na wyzwolony alert. W takim przypadku skonfigurowano wiadomości e-mail do wysłania oraz bilet narzędzia ITSM do utworzenia.  Można również wykonywać wiele innych przydatnych czynności, takich jak wyzwalanie funkcji platformy Azure, elementu Runbook usługi Automation, elementu webhook lub aplikacji logiki.

![Obraz konfigurujący grupę akcji do alertu dotyczącego zmiany](./media/change-tracking/action-groups.png)

Po ustawieniu wszystkich parametrów i logiki można zastosować alert do środowiska.

### <a name="alert-suggestions"></a>Sugestie dotyczące alertu

Alerty dotyczące zmian w pliku Hosts to jedna dobra usługa alertów dotyczących danych Change Tracking lub spisu, ale istnieje wiele większej liczby scenariuszy związanych z alertami, w tym przypadki zdefiniowane razem z ich przykładowymi zapytaniami w sekcji poniżej.

|Zapytanie  |Opis  |
|---------|---------|
|Zmianakonfiguracji <br>&#124;gdzie ConfigChangeType = = "Files" i FileSystemPath zawiera "c:\\Windows\\system32\\sterowniki\\"|Przydatne do śledzenia zmian plików krytycznych dla systemu|
|Zmianakonfiguracji <br>&#124;gdzie FieldsChanged zawiera "FileContentChecksum" i FileSystemPath = = "c:\\Windows\\system32\\sterowniki\\etc\\hosts"|Przydatne do śledzenia modyfikacji plików konfiguracji kluczy|
|Zmianakonfiguracji <br>&#124;gdzie ConfigChangeType = = "WindowsServices" i SvcName zawiera "W3SVC" i SvcState = = "zatrzymana"|Przydatne do śledzenia zmian krytycznych usług systemu|
|Zmianakonfiguracji <br>&#124;gdzie ConfigChangeType = = "demony" i SvcName zawierają "SSH" i SvcState! = "uruchomiona"|Przydatne do śledzenia zmian krytycznych usług systemu|
|Zmianakonfiguracji <br>&#124;gdzie ConfigChangeType = = "oprogramowanie" i ChangeCategory = = "dodane"|Przydatne w środowiskach, które wymagają blokowania konfiguracji oprogramowania|
|ConfigurationData <br>&#124;gdzie Softwarename zawiera "Monitoring Agent" i CurrentVersion! = "8.0.11081.0"|Przydatne do wyświetlania maszyn, na których zainstalowano nieaktualną lub niezgodną wersję oprogramowania. Raport informuje o ostatnim raportowanym stanie konfiguracji, a nie o zmianach.|
|Zmianakonfiguracji <br>&#124;gdzie RegistryKey = = @ "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Przydatne do śledzenia zmian kluczowych kluczy antywirusowych|
|Zmianakonfiguracji <br>&#124;gdzie RegistryKey zawiera @ "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Przydatne do śledzenia zmian w ustawieniach zapory|

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkiem dotyczącym Change Tracking, aby dowiedzieć się więcej o korzystaniu z rozwiązania:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów ze zmianami w środowisku](automation-tutorial-troubleshoot-changes.md)

* Użyj [wyszukiwania w dzienniku Azure monitor](../log-analytics/log-analytics-log-searches.md) , aby wyświetlić szczegółowe dane śledzenia zmian.
