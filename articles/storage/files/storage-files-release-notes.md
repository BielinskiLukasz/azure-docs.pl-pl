---
title: Informacje o wersji agenta usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Informacje o wersji agenta usługi Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 2/12/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 2bbac21b9ac3e07cbb41ea8aa4cf93dcbd636d15
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181783"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Informacje o wersji agenta usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Instalacje systemów Windows Server są przekształcane w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

W tym artykule udostępniono informacje o obsługiwanych wersjach agenta usługi Azure File Sync.

## <a name="supported-versions"></a>Obsługiwane wersje
Następujące wersje są obsługiwane przez agenta usługi Azure File Sync:

| Punkt kontrolny | Numer wersji agenta | Data wydania | Stan |
|----|----------------------|--------------|------------------|
| W wersji 5 Release - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 lutego 2019 r. | Obsługiwane (zalecana wersja) |
| 2019 stycznia aktualizacji zbiorczej — [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 stycznia 2019 r. | Obsługiwane |
| Grudnia 2018 r aktualizacji zbiorczej — [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 grudnia 2018 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji z grudnia 2018 r. | 4.1.0.0 | 4 grudnia 2018 r. | Obsługiwane |
| Wydania wersji 4 | 4.0.1.0 | 13 listopada 2018 r. | Obsługiwane |
| Pakiet zbiorczy aktualizacji września 2018 r. | 3.3.0.0 | 24 września 2018 r. | Obsługiwane — wersja agenta wygaśnie w dniu 19 lipca 2019 r. |
| Pakiet zbiorczy aktualizacji z sierpnia 2018 | 3.2.0.0 | 15 sierpnia 2018 r. | Obsługiwane — wersja agenta wygaśnie w dniu 19 lipca 2019 r. |
| Ogólna dostępność | 3.1.0.0 | 19 lipca 2018 r. | Obsługiwane — wersja agenta wygaśnie w dniu 19 lipca 2019 r. |
| Wygasłego agentami | 1.1.0.0 - 3.0.13.0 | ND | Nie jest obsługiwana — wersje agentów wygasła w dniu 1 października 2018 r. |

### <a name="azure-file-sync-agent-update-policy"></a>Zasady aktualizacji agenta usługi Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-5020"></a>Wersja agenta 5.0.2.0
Poniższe informacje o wersji dotyczą wersji 5.0.2.0 agenta usługi Azure File Sync (wydanej 12 lutego 2019 r.).

### <a name="improvements-and-issues-that-are-fixed"></a>Ulepszenia i problemy, które zostały usunięte

- Obsługa chmury Azure Government
    - Dodaliśmy obsługę wersji zapoznawczej w chmurze Azure dla instytucji rządowych. Wymaga to subskrypcji wymienione biały i pobieraniu specjalne agenta od firmy Microsoft. Aby uzyskać dostęp do wersji zapoznawczej, Wyślij wiadomość e-mail bezpośrednio do nas [ AzureFiles@microsoft.com ](mailto:AzureFiles@microsoft.com).
- Obsługa deduplikacji danych
    - Funkcja deduplikacji danych teraz jest w pełni obsługiwany przy użyciu obsługi warstw włączone w systemie Windows Server 2016 i Windows Server 2019 w chmurze. Włączanie deduplikacji na woluminie z obsługi warstw włączone w chmurze pozwala bez inicjowania obsługi administracyjnej więcej miejsca w pamięci podręcznej więcej plików lokalnych.
- Obsługa transferu danych w trybie offline (np. za pomocą urządzenia Data Box)
    - Łatwo przeprowadzić migrację dużych ilości danych do usługi Azure File Sync za pomocą jakichkolwiek środków, możesz wybrać. Możesz nawet usługi migracji innych firm, narzędzia AzCopy i Azure Data Box. Nie należy używać ogromnych ilości przepustowości, aby uzyskać dane na platformę Azure, w przypadku urządzenia Data Box — po prostu poczty go istnieje! Aby dowiedzieć się więcej, zobacz [w trybie Offline Docs transferu danych](https://aka.ms/AFS/OfflineDataTransfer).
- Wydajność synchronizacji ulepszone
    - Klienci z wielu punkty końcowe serwera, w tym samym woluminie mogła wystąpić powolne synchronizacji wydajności we wcześniejszych wersjach. Usługa Azure File Sync tworzy migawkę usługi VSS tymczasowe raz dziennie na serwerze, aby synchronizować pliki, które mają otwarte dojścia. Synchronizuj teraz obsługuje wielu punkty końcowe serwera synchronizacji na woluminie podczas sesji synchronizacji usługi VSS jest aktywny. Już trwa oczekiwanie na VSS Synchronizuj sesji, aby wykonać, aby wznowić synchronizację na inne punkty końcowe serwera na woluminie.
- Udoskonalone monitorowanie w portalu
    - Wykresy zostały dodane w portalu usługi synchronizacji magazynu, aby wyświetlić:
        - Liczba plików synchronizowane
        - Rozmiar danych transferowanych
        - Liczba plików nie synchronizuje
        - Rozmiar danych przypomnieć
        - Stan łączności serwera
    - Aby dowiedzieć się więcej, zobacz [monitora usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Zwiększona skalowalność i niezawodność
    - Maksymalna liczba obiektów systemu plików (pliki i katalogi) w katalogu został zwiększony do 1 000 000. Limit poprzedniej została 200 000.
    - Synchronizacja podejmie próbę wznowienia transfer danych niż zainicjowaniu przerwanie transferu dużych plików 

### <a name="evaluation-tool"></a>Narzędzie oceny
Przed wdrożeniem usługi Azure File Sync, należy sprawdzić, czy jest on zgodny z systemu za pomocą narzędzia oceny usługi Azure File Sync. To narzędzie jest polecenie cmdlet programu Azure PowerShell, która sprawdza pod kątem potencjalnych problemów za pomocą systemu plików i zestaw danych, takimi jak nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. I instrukcje dotyczące instalacji, zobacz [uruchamiania narzędzia oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta przy użyciu uprawnień podniesionych uprawnień (Administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany tylko na 2019 r Server systemu Windows, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z obsługą pamięci dynamicznej, minimalna MiB 2048 pamięci należy skonfigurować maszynę Wirtualną.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punkty końcowe serwera znajduje się na woluminie, który zawiera katalog informacji (SVI) woluminu systemowego skompresowany. Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.
- Tryb FIPS nie jest obsługiwana i musi zostać wyłączona. 

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md).
- Osłon plików Menedżera zasobów serwera plików (FSRM) mogą powodować nieskończone błędy synchronizacji, gdy pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu sysprep na serwerze, na którym jest zainstalowany agent usługi Azure File Sync nie jest obsługiwana i może prowadzić do nieoczekiwanych wyników. Powinien być zainstalowany agent usługi Azure File Sync po wdrożenie obrazu serwera i zakończeniu miniinstalacji programu sysprep.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanych znaków. Zobacz [przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) listę nieobsługiwanych znaków.
- Pliki i katalogi, które kończą się kropką.
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe, stanie się niedostępna, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli przeszły 30 dni, ponieważ usunięto punkt końcowy serwera lub usunięto punkt końcowy w chmurze, pliki warstwowe, które nie zostały przypomnieć będzie bezużyteczny.
- Chmura obsługi warstw nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, należy wyłączyć obsługi warstw podczas tworzenia punktu końcowego serwera w chmurze.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania aplikacji w ramach lokalizacji punktu końcowego serwera lub systemu operacyjnego.
- Nazwa serwera w portalu nie są aktualizowane po zmianie nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Usługa Azure File Sync nie obsługuje bezpośrednio wprowadzania zmian do udziału plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw konieczne ich odnalezienie przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze, co 24 godziny. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pośrednictwem protokołu REST nie może zaktualizować SMB godzina ostatniej modyfikacji i nie będą widoczne jako zmiany przez sync.
- Usługa synchronizacji magazynu i/lub konta magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesiony, należy udzielić dostępu usługi hybrydowe File Sync do konta magazynu (zobacz [upewnij się, usługa Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji Azure innej dzierżawy usługi AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą narzędzia robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe pliku. Pozwoli to zagwarantować, że starsze pliki są rozmieszczone warstwowo wcześniej niż ostatnio używanych plików.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-4300"></a>Wersja agenta 4.3.0.0
Poniższe informacje o wersji dotyczą wersji 4.3.0.0 agenta usługi Azure File Sync wydanej 14 stycznia 2019 r. Te informacje są uzupełnieniem informacji o wersji 4.0.1.0 informacje o wersji.

Lista problemów rozwiązanych w tej wersji:  
- Pliki nie są organizowane w warstwy po uaktualnieniu agenta usługi Azure File Sync w wersji 4.x.
- AfsUpdater.exe jest teraz obsługiwany 2019 serwera systemu Windows.
- Ulepszenia niezawodności różne do celów synchronizacji. 

## <a name="agent-version-4200"></a>Wersja agenta 4.2.0.0
Poniższe informacje o wersji dotyczą wersji 4.2.0.0 agenta usługi Azure File Sync wydanej 10 grudnia 2018 r. Te informacje są uzupełnieniem informacji o wersji 4.0.1.0 informacje o wersji.

Lista problemów rozwiązanych w tej wersji:  
- Może wystąpić błąd zatrzymania 0x3B lub błąd zatrzymania 0x1E, po utworzeniu migawki VSS.  
- Przeciek pamięci może wystąpić, gdy w chmurze warstw jest włączona  

## <a name="agent-version-4100"></a>Wersja agenta 4.1.0.0
Poniższe informacje o wersji dotyczą wersji 4.1.0.0 agenta usługi Azure File Sync wydanej 4 grudnia 2018 r. Te informacje są uzupełnieniem informacji o wersji 4.0.1.0 informacje o wersji.

Lista problemów rozwiązanych w tej wersji:  
- Serwer może przestać odpowiadać z powodu przeciek pamięci Obsługa poziomów w chmurze.  
- Instalacja agenta zakończy się niepowodzeniem z powodu następującego błędu: Błąd 1921. Nie można zatrzymać usługi "Magazynu agenta synchronizacji" (FileSyncSvc).  Sprawdź, czy masz wystarczające uprawnienia do zatrzymywania usług systemowych.  
- Usługa agenta synchronizacji magazynu (FileSyncSvc) może ulec awarii, gdy użycie pamięci jest wysokie.  
- Ulepszenia niezawodności różne chmury, warstw i synchronizacji.

## <a name="agent-version-4010"></a>Wersja agenta 4.0.1.0
Poniższe informacje o wersji dotyczą wersji 4.0.1.0 agenta usługi Azure File Sync (wydanej 13 listopada 2018 r.).

### <a name="evaluation-tool"></a>Narzędzie oceny
Przed wdrożeniem usługi Azure File Sync, należy sprawdzić, czy jest on zgodny z systemu za pomocą narzędzia oceny usługi Azure File Sync. To narzędzie jest polecenie cmdlet programu Azure PowerShell, która sprawdza pod kątem potencjalnych problemów za pomocą systemu plików i zestaw danych, takimi jak nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. I instrukcje dotyczące instalacji, zobacz [uruchamiania narzędzia oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta przy użyciu uprawnień podniesionych uprawnień (Administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany tylko na 2019 r Server systemu Windows, Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GiB pamięci. Jeśli serwer jest uruchomiony na maszynie wirtualnej z obsługą pamięci dynamicznej, minimalna MiB 2048 pamięci należy skonfigurować maszynę Wirtualną.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punkty końcowe serwera znajduje się na woluminie, który zawiera katalog informacji (SVI) woluminu systemowego skompresowany. Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.
- Tryb FIPS nie jest obsługiwana i musi zostać wyłączona. 
- Może wystąpić błąd zatrzymania 0x3B lub błąd zatrzymania 0x1E, po utworzeniu migawki VSS.

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md).
- Osłon plików Menedżera zasobów serwera plików (FSRM) mogą powodować nieskończone błędy synchronizacji, gdy pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu sysprep na serwerze, na którym jest zainstalowany agent usługi Azure File Sync nie jest obsługiwana i może prowadzić do nieoczekiwanych wyników. Powinien być zainstalowany agent usługi Azure File Sync po wdrożenie obrazu serwera i zakończeniu miniinstalacji programu sysprep.
- Deduplikacja danych i obsługa warstw w chmurze nie są obsługiwane w tym samym woluminie.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Pliki z nieobsługiwanych znaków. Zobacz [przewodnik rozwiązywania problemów](storage-sync-files-troubleshoot.md#handling-unsupported-characters) listę nieobsługiwanych znaków.
- Pliki i katalogi, które kończą się kropką.
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe, stanie się niedostępna, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera. Aby przywrócić dostęp do plików, Utwórz ponownie punkt końcowy serwera. Jeśli przeszły 30 dni, ponieważ usunięto punkt końcowy serwera lub usunięto punkt końcowy w chmurze, pliki warstwowe, które nie zostały przypomnieć będzie bezużyteczny.
- Chmura obsługi warstw nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, należy wyłączyć obsługi warstw podczas tworzenia punktu końcowego serwera w chmurze.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania aplikacji w ramach lokalizacji punktu końcowego serwera lub systemu operacyjnego.
- Nazwa serwera w portalu nie są aktualizowane po zmianie nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Usługa Azure File Sync nie obsługuje bezpośrednio wprowadzania zmian do udziału plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw konieczne ich odnalezienie przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze, co 24 godziny. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pośrednictwem protokołu REST nie może zaktualizować SMB godzina ostatniej modyfikacji i nie będą widoczne jako zmiany przez sync.
- Usługa synchronizacji magazynu i/lub konta magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesiony, należy udzielić dostępu usługi hybrydowe File Sync do konta magazynu (zobacz [upewnij się, usługa Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji Azure innej dzierżawy usługi AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Na podstawie daty obsługi warstw w chmurze ustawienie zasad służy do określania plików, które mają być buforowane, jeśli są dostępne w określonej liczby dni. Aby dowiedzieć się więcej, zobacz [Przegląd obsługi warstw w chmurze](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas kopiowania plików za pomocą narzędzia robocopy, użyj opcji/MIR, aby zachować sygnatury czasowe pliku. Pozwoli to zagwarantować, że starsze pliki są rozmieszczone warstwowo wcześniej niż ostatnio używanych plików.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.

## <a name="agent-version-3300"></a>Wersja agenta 3.3.0.0
Poniższe informacje o wersji dotyczą wersji 3.3.0.0 agenta usługi Azure File Sync wydanej 24 września 2018 r. Te informacje są uzupełnieniem informacji o wersji 3.1.0.0 informacje o wersji.

Lista problemów rozwiązanych w tej wersji:
- Stan zarejestrowanego serwera to "Pojawia się w trybie offline" od usługi Azure File Sync, agent jest uaktualniony do wersji 3.1 lub 3.2.
- Usługa agenta synchronizacji (FileSyncSvc) magazynu kończy się niepowodzeniem ze względu na pliki, których długie ścieżki.
- Rejestracja serwera nie powiodła się z powodu błędu: Nie można załadować pliku lub zestawu Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Wersja agenta 3.2.0.0
Poniższe informacje o wersji dotyczą wersji 3.2.0.0 agenta usługi Azure File Sync wydanej 15 sierpnia 2018. Te informacje są uzupełnieniem informacji o wersji 3.1.0.0 informacje o wersji.

Ta wersja zawiera następujące poprawki:
- Synchronizacji kończy się niepowodzeniem błąd braku pamięci (0x8007000e) z powodu przeciek pamięci

## <a name="agent-version-3100"></a>Wersja agenta 3.1.0.0
Poniższe informacje o wersji dotyczą wersji 3.1.0.0 agenta usługi Azure File Sync (wydanej 19 lipca 2018 r.).

### <a name="evaluation-tool"></a>Narzędzie oceny
Przed wdrożeniem usługi Azure File Sync, należy sprawdzić, czy jest on zgodny z systemu za pomocą narzędzia oceny usługi Azure File Sync. To narzędzie jest polecenie cmdlet programu Azure PowerShell, która sprawdza pod kątem potencjalnych problemów za pomocą systemu plików i zestaw danych, takimi jak nieobsługiwane znaki lub nieobsługiwaną wersję systemu operacyjnego. I instrukcje dotyczące instalacji, zobacz [uruchamiania narzędzia oceny](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) w przewodniku planowania. 

### <a name="agent-installation-and-server-configuration"></a>Instalacja agenta i konfiguracja serwera
Aby uzyskać więcej informacji na temat sposobu instalowania i konfigurowania agenta usługi Azure File Sync z systemem Windows Server, zobacz [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).

- Musi być zainstalowany pakiet instalacyjny agenta przy użyciu uprawnień podniesionych uprawnień (Administrator).
- Agent nie jest obsługiwany w opcjach wdrażania systemu Windows Server Core lub Nano Server.
- Agent jest obsługiwany wyłącznie w systemach Windows Server 2016 i Windows Server 2012 R2.
- Agent wymaga co najmniej 2 GB pamięci fizycznej.
- Usługa agenta synchronizacji magazynu (FileSyncSvc) nie obsługuje punkty końcowe serwera znajduje się na woluminie, który zawiera katalog informacji (SVI) woluminu systemowego skompresowany. Ta konfiguracja będzie prowadzić do nieoczekiwanych wyników.
- Tryb FIPS nie jest obsługiwana i musi zostać wyłączona. 

### <a name="interoperability"></a>Współdziałanie
- Oprogramowanie antywirusowe, kopia zapasowa i inne aplikacje, które uzyskują dostęp do plików warstwowych, mogą spowodować niepożądane odwołania, chyba że przestrzegają one atrybutu offline i pomijają odczytywanie zawartości tych plików. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md).
- Nie używaj osłon plików Menedżera zasobów serwera plików ani innych osłon plików. Osłony plików mogą powodować nieskończone błędy synchronizacji, jeśli pliki są zablokowane z powodu osłony plików.
- Uruchamianie programu sysprep na serwerze, na którym jest zainstalowany agent usługi Azure File Sync nie jest obsługiwana i może prowadzić do nieoczekiwanych wyników. Rejestracja agenta instalacji i serwer powinien występować po wdrażanie obrazu serwera i zakończeniu miniinstalacji programu sysprep.
- Deduplikacja danych i obsługa warstw w chmurze nie są obsługiwane w tym samym woluminie.

### <a name="sync-limitations"></a>Ograniczenia synchronizacji
Następujące elementy nie są synchronizowane, ale reszta systemu nadal normalnie działa:
- Ścieżki dłuższe niż 2048 znaków.
- Część DACL (poufna lista kontroli dostępu) deskryptora zabezpieczeń, jeśli jest większa niż 2 KB. Ten problem występuje tylko wtedy, gdy masz więcej niż około 40 wpisów kontroli dostępu (ACE) dla jednego elementu.
- Część SACL (systemowa lista kontroli dostępu) deskryptora zabezpieczeń, która jest używana do przeprowadzania inspekcji.
- Atrybuty rozszerzone.
- Alternatywne strumienie danych.
- Punkty ponownej analizy.
- Twarde linki.
- Kompresja (jeśli jest ustawiona w pliku serwera) nie jest zachowywana, gdy zmiany z innych punktów końcowych są synchronizowane z tym plikiem.
- Każdy plik zaszyfrowany za pomocą systemu szyfrowania plików (lub innego szyfrowania trybu użytkownika), który uniemożliwia usłudze odczytywanie danych.

    > [!Note]  
    > Usługa Azure File Sync zawsze szyfruje dane podczas przesyłania. Dane magazynowane na platformie Azure są zawsze zaszyfrowane.
 
### <a name="server-endpoint"></a>Punkt końcowy serwera
- Punkt końcowy serwera można tworzyć tylko na woluminie NTFS. Systemy plików ReFS, FAT, FAT32 i inne nie są obecnie obsługiwane przez usługę Azure File Sync.
- Pliki warstwowe będzie ono używane, jeśli pliki nie zostaną odwołane przed usunięciem punktu końcowego serwera.
- Chmura obsługi warstw nie jest obsługiwana na woluminie systemowym. Aby utworzyć punkt końcowy serwera na woluminie systemowym, należy wyłączyć obsługi warstw podczas tworzenia punktu końcowego serwera w chmurze.
- Klaster trybu failover jest obsługiwany tylko z dyskami klastrowanymi, ale nie z udostępnionymi woluminami klastra (CSV).
- Punktu końcowego serwera nie można zagnieżdżać. Może on współistnieć na tym samym woluminie równolegle z innym punktem końcowym.
- Nie należy przechowywać pliku stronicowania systemu operacyjnego lub aplikacji w ramach punktu końcowego serwera.
- Nazwa serwera w portalu nie są aktualizowane po zmianie nazwy serwera.

### <a name="cloud-endpoint"></a>Punkt końcowy w chmurze
- Usługa Azure File Sync nie obsługuje bezpośrednio wprowadzania zmian do udziału plików platformy Azure. Jednak wszelkie zmiany wprowadzone w udziale plików platformy Azure najpierw konieczne ich odnalezienie przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze, co 24 godziny. Ponadto zmiany wprowadzone do udziału plików platformy Azure za pośrednictwem protokołu REST nie może zaktualizować SMB godzina ostatniej modyfikacji i nie będą widoczne jako zmiany przez sync.
- Usługa synchronizacji magazynu i/lub konta magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesiony, należy udzielić dostępu usługi hybrydowe File Sync do konta magazynu (zobacz [upewnij się, usługa Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji Azure innej dzierżawy usługi AD.

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
- Jeśli plik warstwowy jest kopiowany do innej lokalizacji za pomocą rozszerzenia Robocopy, wynikowy plik nie będzie obsługiwany w warstwie. Atrybut offline może być ustawiony, ponieważ rozszerzenie Robocopy niepoprawnie dołącza ten atrybut podczas operacji kopiowania.
- Podczas przeglądania właściwości plików z klienta SMB atrybut offline może wyglądać na ustawiony niepoprawnie z powodu buforowania plików metadanych przez SMB.
