---
title: Rozwiązywanie problemów z Azure Files w systemie Windows | Microsoft Docs
description: Rozwiązywanie problemów z Azure Files w systemie Windows
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 17ecc80fee3b024c334b8d36533663f1f3cebe4d
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136909"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Rozwiązywanie problemów z Azure Files w systemie Windows

W tym artykule wymieniono typowe problemy związane z Microsoft Azure plikami w przypadku łączenia się z klientami systemu Windows. Zapewnia również możliwe przyczyny i rozwiązania tych problemów. Oprócz kroków opisanych w tym artykule, można również użyć [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) , aby upewnić się, że środowisko klienta systemu Windows ma odpowiednie wymagania wstępne. AzFileDiagnostics automatyzuje wykrywanie większości objawów wymienionych w tym artykule i ułatwia skonfigurowanie środowiska w celu uzyskania optymalnej wydajności. Te informacje można również znaleźć w obszarze [Rozwiązywanie problemów z udziałami Azure Files](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) , które udostępniają kroki ułatwiające rozwiązywanie problemów z połączeniem/mapowaniem/instalowaniem udziałów Azure Files.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Błąd 5 podczas instalowania udziału plików platformy Azure

Podczas próby zainstalowania udziału plików może zostać wyświetlony następujący błąd:

- Wystąpił błąd systemowy 5. Odmowa dostępu.

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyna 1: nieszyfrowany kanał komunikacyjny

Ze względów bezpieczeństwa połączenia z udziałami plików platformy Azure są blokowane, jeśli kanał komunikacyjny nie jest szyfrowany i jeśli próba połączenia nie pochodzi z tego samego centrum danych, w którym znajdują się udziały plików platformy Azure. Nieszyfrowane połączenia w tym samym centrum danych też mogą być blokowane, jeśli ustawienie [Wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest włączone na koncie magazynu. Szyfrowany kanał komunikacyjny jest udostępniany tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Systemy Windows 8, Windows Server 2012 i nowsze wersje negocjują żądania obejmujące protokół SMB 3.0, który obsługuje szyfrowanie.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

1. Nawiąż połączenie z klientem obsługującym szyfrowanie SMB (system Windows 8, Windows Server 2012 lub nowszy) lub Połącz się z maszyną wirtualną w tym samym centrum danych co konto usługi Azure Storage, które jest używane w udziale plików platformy Azure.
2. Upewnij się, że ustawienie [wymagany bezpieczny transfer](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) jest wyłączone na koncie magazynu, jeśli klient nie obsługuje szyfrowania SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 2: na koncie magazynu są włączone reguły sieci wirtualnej lub zapory 

Jeśli reguły sieci wirtualnej i zapory są skonfigurowane na koncie magazynu, dla ruchu sieciowego będzie następować odmowa dostępu, chyba że dostęp będzie dozwolony dla adresu IP klienta lub sieci wirtualnej.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Przyczyna 3: uprawnienia na poziomie udziału są nieprawidłowe w przypadku korzystania z uwierzytelniania opartego na tożsamościach

Jeśli użytkownicy uzyskują dostęp do udziału plików platformy Azure przy użyciu uwierzytelniania Active Directory (AD) lub Azure Active Directory Domain Services (Azure AD DS), dostęp do udziału plików zakończy się niepowodzeniem z powodu błędu "odmowa dostępu", jeśli uprawnienia na poziomie udziału są nieprawidłowe. 

### <a name="solution-for-cause-3"></a>Rozwiązanie dla przyczyny 3

Aby zaktualizować uprawnienia na poziomie udziału, zobacz [przypisywanie uprawnień dostępu do tożsamości](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Błąd 53, błąd 67 lub błąd 87 podczas instalowania lub odinstalowywania udziału plików platformy Azure

Podczas próby zainstalowania udziału plików z lokalnego lub innego centrum danych mogą pojawić się następujące błędy:

- Wystąpił błąd systemowy 53. Nie można znaleźć ścieżki sieciowej.
- Wystąpił błąd systemowy 67. Nie można odnaleźć nazwy sieci.
- Wystąpił błąd systemowy 87. Parametr jest nieprawidłowy.

### <a name="cause-1-port-445-is-blocked"></a>Przyczyna 1: Port 445 jest zablokowany

Błąd systemu 53 lub błąd systemu 67 może wystąpić, jeśli port 445 wychodzący ruch do Azure Files centrum danych jest zablokowany. Aby zobaczyć podsumowanie usługodawców internetowych, którzy nie zezwalają na dostęp z portu 445, przejdź do witryny [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby sprawdzić, czy zapora lub usługodawca internetowy blokuje port 445, użyj narzędzia [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) lub polecenia cmdlet `Test-NetConnection`. 

Aby użyć polecenia cmdlet `Test-NetConnection`, należy zainstalować moduł Azure PowerShell, aby uzyskać więcej informacji, zobacz [Install Azure PowerShell module](/powershell/azure/install-Az-ps) . Pamiętaj, aby zastąpić wyrażenia `<your-storage-account-name>` i `<your-resource-group-name>` nazwami odpowiednimi dla konta magazynu.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Jeśli połączenie zostało pomyślnie nawiązane, powinny pojawić się następujące dane wyjściowe:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> Powyższe polecenie zwraca bieżący adres IP konta magazynu. Nie ma żadnej gwarancji, że ten adres IP pozostanie niezmieniony. Może on ulec zmianie w dowolnym momencie. Nie umieszczaj tego adresu IP w żadnym kodzie skryptu ani konfiguracji zapory.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

#### <a name="solution-1---use-azure-file-sync"></a>Rozwiązanie 1 — Używanie Azure File Sync
Azure File Sync można przekształcić lokalny serwer systemu Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego protokołu, który jest dostępny w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, NFS i FTPS. Azure File Sync działa przez port 443 i może służyć jako obejście w celu uzyskania dostępu Azure Files z klientów, którzy mają zablokowany port 445. [Dowiedz się, jak skonfigurować Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Rozwiązanie 2 — Korzystanie z sieci VPN
Skonfigurowanie sieci VPN do określonego konta magazynu spowoduje, że ruch przejdzie przez bezpieczny tunel, a nie przez Internet. Postępuj zgodnie z [instrukcjami, aby skonfigurować sieć VPN](storage-files-configure-p2s-vpn-windows.md) , aby uzyskać dostęp do Azure Files z systemu Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Rozwiązanie 3 — odblokowywanie portu 445 przy pomocy usługodawcy internetowego/administratora IT
Skontaktuj się z działem IT lub usługodawcą internetowym, aby otworzyć port 445 wychodzące do [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Rozwiązanie 4 — Korzystanie z narzędzi opartych na interfejsie API REST, takich jak Eksplorator usługi Storage/PowerShell
Azure Files obsługuje również protokół REST oprócz protokołu SMB. Dostęp do REST działa przez port 443 (standardowy protokół TCP). Istnieją różne narzędzia, które są zapisywane przy użyciu interfejsu API REST, które umożliwiają rozbudowane środowisko użytkownika. [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) jest jednym z nich. [Pobierz i zainstaluj Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) i Połącz się ze swoim udziałem plików, Azure Files. Można również użyć [programu PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) , który również jest interfejsem API REST użytkownika.

### <a name="cause-2-ntlmv1-is-enabled"></a>Przyczyna 2: NTLMv1 jest włączona

Błąd systemu 53 lub błąd systemu 87 może wystąpić, jeśli na kliencie jest włączona komunikacja NTLMv1. Usługa Azure Files obsługuje tylko uwierzytelnianie NTLMv2. Włączenie komunikacji NTLMv1 powoduje, że klient jest mniej bezpieczny. Dlatego komunikacja jest blokowana dla usługi Azure Files. 

Aby ustalić, czy jest to przyczyną błędu, sprawdź, czy następujący podklucz rejestru jest ustawiony na wartość 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Aby uzyskać więcej informacji, zobacz temat [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) w witrynie TechNet.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przywróć wartość **LmCompatibilityLevel** do wartości domyślnej równej 3 w następującym podkluczu rejestru:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Błąd 1816 "za mało dostępnego przydziału, aby przetworzyć to polecenie" podczas kopiowania do udziału plików platformy Azure

### <a name="cause"></a>Przyczyna

Błąd 1816 występuje po osiągnięciu górnego limitu współbieżnych otwartych dojść, które są dozwolone dla pliku na komputerze, na którym jest instalowany udział plików.

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę współbieżnych dojść otwartych przez zamknięcie niektórych uchwytów, a następnie ponów próbę. Aby uzyskać więcej informacji, zobacz [Microsoft Azure Storage listy kontrolnej wydajności i skalowalności](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Aby wyświetlić otwarte uchwyty dla udziału plików, katalogu lub pliku, należy użyć polecenia cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) programu PowerShell.  

Aby zamknąć otwarte uchwyty dla udziału plików, katalogu lub pliku, należy użyć polecenia cmdlet programu PowerShell [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) .

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w programie AZ PowerShell module w wersji 2,4 lub nowszej. Aby zainstalować najnowszy moduł AZ PowerShell module, zobacz [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Błąd "Brak dostępu" podczas próby uzyskania dostępu do udziału plików platformy Azure lub usunięcie go  
W przypadku próby uzyskania dostępu do udziału plików platformy Azure w portalu lub usunięcia go z niego może zostać wyświetlony następujący błąd:

Brak dostępu  
Kod błędu: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 1: na koncie magazynu są włączone reguły sieci wirtualnej lub zapory

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Przyczyna 2: Twoje konto użytkownika nie ma dostępu do konta magazynu

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przejdź do konta magazynu, na którym znajduje się udział plików platformy Azure, kliknij pozycję **Kontrola dostępu (IAM)** i sprawdź, czy konto użytkownika ma dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [jak zabezpieczyć konto magazynu za pomocą Access Control opartego na rolach (RBAC)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Nie można usunąć pliku lub katalogu w udziale plików platformy Azure
Podczas próby usunięcia pliku może zostać wyświetlony następujący błąd:

Określony zasób jest oznaczony do usunięcia przez klienta SMB.

### <a name="cause"></a>Przyczyna
Ten problem występuje zwykle, gdy plik lub katalog ma otwarte dojście. 

### <a name="solution"></a>Rozwiązanie

Jeśli klienci SMB zamknęli wszystkie otwarte dojścia, a problem nadal wystąpi, wykonaj następujące czynności:

- Użyj polecenia cmdlet programu PowerShell [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) , aby wyświetlić otwarte dojścia.

- Użyj polecenia cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) programu PowerShell, aby zamknąć otwarte dojścia. 

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w programie AZ PowerShell module w wersji 2,4 lub nowszej. Aby zainstalować najnowszy moduł AZ PowerShell module, zobacz [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Kopiowanie plików do i z Azure Files w systemie Windows

Podczas próby przetransferowania plików do usługi plików platformy Azure może być widoczna niska wydajność.

- Jeśli nie masz wymaganego minimalnego rozmiaru operacji we/wy, zalecamy użycie 1 MiB jako rozmiaru we/wy w celu uzyskania optymalnej wydajności.
-   Jeśli znasz końcowy rozmiar pliku, który jest rozszerzany przy użyciu zapisu, a oprogramowanie nie ma problemów ze zgodnością, gdy niezapisany ogon w pliku zawiera zera, a następnie ustaw rozmiar pliku z góry, zamiast wprowadzać każdy zapis rozszerzający.
-   Użyj odpowiedniej metody copy:
    -   Użyj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) do dowolnego transferu między dwoma udziałami plików.
    -   Użyj [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) między udziałami plików na komputerze lokalnym.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Zagadnienia dotyczące Windows 8.1 lub systemu Windows Server 2012 R2

W przypadku klientów z systemem Windows 8.1 lub Windows Server 2012 R2 upewnij się, że zainstalowano poprawkę [KB3114025](https://support.microsoft.com/help/3114025) . Ta poprawka podnosi wydajność dojścia do tworzenia i zamykania.

Aby sprawdzić, czy poprawka została zainstalowana, można uruchomić następujący skrypt:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Jeśli poprawka jest zainstalowana, wyświetlane są następujące dane wyjściowe:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Obrazy systemu Windows Server 2012 R2 w witrynie Azure Marketplace mają domyślnie zainstalowane KB3114025 poprawek, począwszy od grudnia 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Brak folderu z literą dysku w "My Computer" lub "tym komputerze"

Jeśli udział plików platformy Azure jest mapowany jako administrator przy użyciu net use, prawdopodobnie brakuje udziału.

### <a name="cause"></a>Przyczyna

Domyślnie Eksplorator plików systemu Windows nie jest uruchomiony jako administrator. Jeśli uruchomisz polecenie net use z wiersza polecenia z uprawnieniami administracyjnymi, możesz mapować dysk sieciowy jako administrator. Ze względu na to, że mapowane dyski są skoncentrowane na użytkownikach, zalogowane konto użytkownika nie wyświetla dysków, jeśli są one zainstalowane przy użyciu innego konta użytkownika.

### <a name="solution"></a>Rozwiązanie
Zainstaluj udział z wiersza polecenia bez administratora. Alternatywnie możesz skorzystać z [tego tematu TechNet](https://technet.microsoft.com/library/ee844140.aspx) , aby skonfigurować wartość rejestru **EnableLinkedConnections** .

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Polecenie net use kończy się niepowodzeniem, jeśli konto magazynu zawiera ukośnik

### <a name="cause"></a>Przyczyna

Polecenie net use interpretuje ukośnik (/) jako opcję wiersza polecenia. Jeśli nazwa konta użytkownika zaczyna się od ukośnika, mapowanie dysku kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, można wykonać jedną z następujących czynności:

- Uruchom następujące polecenie programu PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  W pliku wsadowym można uruchomić polecenie w następujący sposób:

  `Echo new-smbMapping ... | powershell -command –`

- Umieść znaki podwójnego cudzysłowu otaczające klucz, aby obejść ten problem — chyba że ukośnik jest pierwszym znakiem. Jeśli tak jest, użyj trybu interaktywnego, a następnie wprowadź hasło oddzielnie lub ponownie wygeneruj klucze, aby uzyskać klucz, który nie zaczyna się od ukośnika.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikacja lub usługa nie może uzyskać dostępu do zainstalowanego dysku Azure Files

### <a name="cause"></a>Przyczyna

Dyski są instalowane na użytkownika. Jeśli aplikacja lub usługa jest uruchomiona przy użyciu innego konta użytkownika niż to, na którym zainstalowano dysk, aplikacja nie zobaczy tego dysku.

### <a name="solution"></a>Rozwiązanie

Użyj jednego z następujących rozwiązań:

-   Zainstaluj dysk z tego samego konta użytkownika, które zawiera aplikację. Możesz użyć narzędzia, takiego jak PsExec.
- Przekaż nazwę i klucz konta magazynu w parametrach nazwa użytkownika i hasło polecenia net use.
- Użyj polecenia cmdkey, aby dodać poświadczenia do Menedżera poświadczeń. Wykonaj to z wiersza polecenia w kontekście konta usługi za pośrednictwem interakcyjnego logowania lub przy użyciu `runas`.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapuj udział bezpośrednio bez użycia zmapowanej litery dysku. Niektóre aplikacje mogą nie ponownie połączyć się z literą dysku, więc użycie pełnej ścieżki UNC może być bardziej niezawodne. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Po wykonaniu tych instrukcji może zostać wyświetlony następujący komunikat o błędzie podczas uruchamiania polecenia net use dla konta usługi sieciowej/systemu: "błąd systemu 1312. Określona sesja logowania nie istnieje. Być może zostało już zakończone. " W takim przypadku upewnij się, że nazwa użytkownika, która jest przenoszona do usługi net use, zawiera informacje o domenie (na przykład: "[nazwa konta magazynu]. plik. Core. Windows. NET").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Błąd "Kopiowanie pliku do lokalizacji docelowej, która nie obsługuje szyfrowania"

Gdy plik jest kopiowany za pośrednictwem sieci, plik jest odszyfrowywany na komputerze źródłowym, przesyłany w postaci zwykłego tekstu i ponownie szyfrowany w miejscu docelowym. Jednak podczas próby skopiowania zaszyfrowanego pliku może zostać wyświetlony następujący błąd: "kopiujesz plik do lokalizacji docelowej, która nie obsługuje szyfrowania".

### <a name="cause"></a>Przyczyna
Ten problem może wystąpić, jeśli używasz system szyfrowania plików (EFS). Pliki szyfrowane przez funkcję BitLocker można kopiować do Azure Files. Jednak Azure Files nie obsługuje systemu plików NTFS.

### <a name="workaround"></a>Obejście
Aby skopiować plik za pośrednictwem sieci, należy go najpierw odszyfrować. Użyj jednej z poniższych metod:

- Użyj polecenia **copy/d** . Umożliwia zapisywanie zaszyfrowanych plików jako odszyfrowanych plików w miejscu docelowym.
- Ustaw następujący klucz rejestru:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ wartości = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Wartość = 1

Należy pamiętać, że ustawienie klucza rejestru ma wpływ na wszystkie operacje kopiowania wprowadzone do udziałów sieciowych.

## <a name="slow-enumeration-of-files-and-folders"></a>Wolne Wyliczenie plików i folderów

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli na komputerze klienckim nie ma wystarczającej ilości pamięci podręcznej dla dużych katalogów.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy dostosować wartość rejestru **DirectoryCacheEntrySizeMax** , aby zezwalała na buforowanie większych list katalogów na komputerze klienckim:

- Lokalizacja: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Wartość Mane: DirectoryCacheEntrySizeMax 
- Typ wartości: DWORD
 
 
Na przykład można ustawić 0x100000 i sprawdzić, czy wydajność staje się lepsza.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Błąd AadDsTenantNotFound podczas włączania uwierzytelniania Azure Active Directory Domain Service (AAD DS) dla Azure Files "nie można zlokalizować aktywnych dzierżawców z identyfikatorem dzierżawy AAD-dzierżawca"

### <a name="cause"></a>Przyczyna

AadDsTenantNotFound Wystąpił błąd podczas próby [włączenia uwierzytelniania Azure Active Directory Domain Services (Azure AD DS) na Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) na koncie magazynu, w którym [Usługa domeny usługi AAD (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) nie zostanie utworzona w dzierżawie w usłudze AAD skojarzonej subskrypcji.  

### <a name="solution"></a>Rozwiązanie

Włącz usługi AAD DS w dzierżawie AAD subskrypcji, w której wdrożono konto magazynu. Do utworzenia domeny zarządzanej wymagane są uprawnienia administratora dzierżawy usługi AAD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie ze wskazówkami krok po kroku, aby [włączyć Azure Active Directory Domain Services przy użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Wystąpił błąd "System Error 1359. Wystąpił błąd wewnętrzny "odebrany za pośrednictwem protokołu SMB do udziałów plików z włączonym uwierzytelnianiem Azure Active Directory Domain Service (AAD DS)

### <a name="cause"></a>Przyczyna

Wystąpił błąd "System Error 1359. Błąd wewnętrzny "występuje podczas próby nawiązania połączenia z udziałem plików z włączonym uwierzytelnianiem usługi AAD DS dla usługi AAD DS z nazwą DNS domeny rozpoczynającą się od znaku numerycznego. Jeśli na przykład nazwa DNS domeny usługi AAD DS to "1domain", ten błąd wystąpi podczas próby zainstalowania udziału plików przy użyciu poświadczeń usługi AAD. 

### <a name="solution"></a>Rozwiązanie

Obecnie można rozważyć ponowne wdrożenie usługi AAD DS przy użyciu nowej nazwy DNS domeny, która jest stosowana z poniższymi regułami:
- Nazwy nie mogą rozpoczynać się od znaku numerycznego.
- Nazwy muszą mieć długość od 3 do 63 znaków.

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.
