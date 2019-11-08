---
title: Często zadawane pytania dotyczące Azure Files | Microsoft Docs
description: Znajdź odpowiedzi na często zadawane pytania dotyczące Azure Files.
author: roygara
ms.service: storage
ms.date: 07/30/2019
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 6526f27177b5fb8640deb5302d8cb3aa4acf1a97
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824275"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Często zadawane pytania dotyczące Azure Files
[Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego [protokołu bloku komunikatów serwera (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Udziały plików platformy Azure można instalować jednocześnie w chmurze lub lokalnych wdrożeniach systemów Windows, Linux i macOS. Możesz również buforować udziały plików platformy Azure na maszynach z systemem Windows Server, używając Azure File Sync, aby szybko uzyskać dostęp do miejsca, w którym są używane dane.

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Files funkcji i funkcji, w tym używania Azure File Sync z Azure Files. Jeśli nie widzisz odpowiedzi na pytanie, możesz skontaktować się z nami za pomocą następujących kanałów (w kolejności eskalacji):

1. Sekcja komentarzy w tym artykule.
2. [Forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Pomoc techniczna firmy Microsoft. Aby utworzyć nowe żądanie obsługi, w Azure Portal na karcie **Pomoc** wybierz przycisk **Pomoc i obsługa techniczna** , a następnie wybierz pozycję **nowe żądanie obsługi**.

## <a name="general"></a>Ogólne
* <a id="why-files-useful"></a>
  **jak jest Azure Files przydatne?**  
   Za pomocą Azure Files można tworzyć udziały plików w chmurze bez konieczności zarządzania obciążeniem serwera fizycznego, urządzenia ani urządzenia. Firma Microsoft monotonous Ci, w tym stosowanie aktualizacji systemu operacyjnego i zastępowaniem uszkodzonych dysków. Aby dowiedzieć się więcej na temat scenariuszy, które Azure Files mogą pomóc, zobacz [dlaczego Azure Files jest przydatna](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **jakie są różne sposoby uzyskiwania dostępu do plików w Azure Files?**  
    Udział plików na komputerze lokalnym można zainstalować przy użyciu protokołu SMB 3,0 lub użyć narzędzi takich jak [Eksplorator usługi Storage](https://storageexplorer.com/) , aby uzyskać dostęp do plików w udziale plików. Z poziomu aplikacji możesz użyć bibliotek klienta magazynu, interfejsów API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby uzyskać dostęp do plików w udziale plików platformy Azure.

* <a id="what-is-afs"></a>
  **co to jest Azure File Sync?**  
    Za pomocą Azure File Sync można scentralizować udziały plików w organizacji w Azure Files, utrzymując elastyczność, wydajność i zgodność lokalnego serwera plików. Azure File Sync przekształca komputery z systemem Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego protokołu, który jest dostępny w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, sieciowego systemu plików (NFS) i usługi protokół transferu plików (FTPS). Na całym świecie możesz mieć dowolną liczbę pamięci podręcznych.

* <a id="files-versus-blobs"></a>
  **Dlaczego mam korzystać z udziału plików platformy Azure, a nie usługi Azure Blob Storage?**  
    Azure Files i magazyn obiektów blob platformy Azure umożliwiają przechowywanie dużych ilości danych w chmurze, ale są one przydatne w nieco inny sposób. 
    
    Usługa Azure Blob Storage jest przydatna w przypadku dużych skalowalnych aplikacji w chmurze, które muszą przechowywać dane niestrukturalne. W celu zmaksymalizowania wydajności i skalowania usługa Azure Blob Storage jest prostszym abstrakcją pamięci masowej niż prawdziwy system plików. Dostęp do usługi Azure Blob Storage można uzyskać tylko za pośrednictwem bibliotek klienckich opartych na interfejsie REST (lub bezpośrednio za pośrednictwem protokołu REST).

    Azure Files to system plików. Azure Files ma wszystkie znane streszczenia plików i miłość od lat pracy z lokalnymi systemami operacyjnymi. Podobnie jak w przypadku usługi Azure Blob Storage, Azure Files oferuje Interfejs REST i biblioteki klienckie oparte na protokole REST. W przeciwieństwie do usługi Azure Blob Storage, Azure Files oferuje dostęp SMB do udziałów plików platformy Azure. Korzystając z protokołu SMB, można zainstalować udział plików platformy Azure bezpośrednio w systemie Windows, Linux lub macOS, lokalnie lub na maszynach wirtualnych w chmurze, bez konieczności pisania kodu lub dołączania specjalnych sterowników do systemu plików. Możesz również buforować udziały plików platformy Azure na lokalnych serwerach plików przy użyciu Azure File Sync, aby uzyskać szybki dostęp, a w pobliżu miejsca, w którym są używane dane. 
   
    Aby zapoznać się z bardziej szczegółowym opisem różnic między Azure Files i magazynem obiektów blob platformy Azure, zobacz temat decydowanie o tym, [Kiedy używać usługi Azure Blob Storage, Azure Files lub Azure disks](../common/storage-decide-blobs-files-disks.md). Aby dowiedzieć się więcej o usłudze Azure Blob Storage, zobacz [wprowadzenie do usługi BLOB Storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Dlaczego mam korzystać z udziału plików platformy Azure, a nie z usługi Azure disks?**  
    Dysk w usłudze Azure disks jest po prostu dyskiem. Aby uzyskać wartość z dysków platformy Azure, należy dołączyć dysk do maszyny wirtualnej działającej na platformie Azure. Dysków platformy Azure można używać dla wszystkich elementów, z których można korzystać na serwerze lokalnym. Można go użyć jako dysku systemu operacyjnego, jako obszaru wymiany dla systemu operacyjnego lub jako dedykowanego magazynu dla aplikacji. Interesujące użycie usługi Azure disks polega na utworzeniu serwera plików w chmurze, który będzie używany w tych samych miejscach, w których może być używany udział plików platformy Azure. Wdrożenie serwera plików na platformie Azure Virtual Machines jest wysoce wydajnym sposobem na uzyskanie magazynu plików na platformie Azure, gdy wymagane są opcje wdrażania, które nie są obecnie obsługiwane przez Azure Files (takie jak obsługa protokołu NFS lub Premium Storage). 

    Jednak uruchomienie serwera plików z dyskami platformy Azure jako magazynu zaplecza zwykle jest znacznie droższe niż korzystanie z udziału plików platformy Azure z kilku powodów. Po pierwsze, oprócz płacenia za magazyn na dyskach, należy również płacić za wydatki związane z uruchamianiem co najmniej jednej maszyny wirtualnej platformy Azure. Po drugie należy również zarządzać maszynami wirtualnymi używanymi do uruchamiania serwera plików. Na przykład użytkownik jest odpowiedzialny za uaktualnienia systemu operacyjnego. Na koniec w przypadku założenia, że dane mają być buforowane lokalnie, można skonfigurować technologie replikacji, takie jak rozproszony system plików Replication (DFSR), i zarządzać nimi.

    Jednym z metod uzyskiwania najlepszych Azure Files i serwera plików hostowanego w usłudze Azure Virtual Machines (oprócz używania dysków platformy Azure jako magazynu zaplecza) jest zainstalowanie Azure File Sync na serwerze plików hostowanym na maszynie wirtualnej w chmurze. Jeśli udział plików platformy Azure znajduje się w tym samym regionie, w którym znajduje się serwer plików, można włączyć obsługę warstw w chmurze i ustawić wartość procentową ilości wolnego miejsca na maksymalną (99%). Zapewnia to minimalne duplikowanie danych. Możesz również użyć dowolnych aplikacji do serwerów plików, takich jak aplikacje wymagające obsługi protokołu NFS.

    Aby uzyskać informacje dotyczące opcji konfigurowania serwera plików o wysokiej wydajności i wysokiej dostępności na platformie Azure, zobacz [wdrażanie klastrów gościa maszyny wirtualnej IaaS w Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Aby uzyskać bardziej szczegółowy opis różnic między Azure Files i dyskami platformy Azure, zobacz decydowanie o tym, [Kiedy używać usługi Azure Blob Storage, Azure Files lub Azure disks](../common/storage-decide-blobs-files-disks.md). Aby dowiedzieć się więcej o usłudze Azure disks, zobacz [Omówienie usługi azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **Jak mogę rozpocząć korzystanie z Azure Files?**  
   Wprowadzenie do Azure Files jest proste. Najpierw [Utwórz udział plików](storage-how-to-create-file-share.md), a następnie zainstaluj go w preferowanym systemie operacyjnym: 

  * [Instalowanie w systemie Windows](storage-how-to-use-files-windows.md)
  * [Instalowanie w systemie Linux](storage-how-to-use-files-linux.md)
  * [Zainstaluj w macOS](storage-how-to-use-files-mac.md)

    Aby zapoznać się z bardziej szczegółowym przewodnikiem wdrażania udziału plików platformy Azure w celu zastąpienia produkcyjnych udziałów plików w organizacji, zobacz [Planowanie wdrożenia Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **jakie opcje nadmiarowości magazynu są obsługiwane przez Azure Files?**  
    Obecnie Azure Files obsługuje Magazyn lokalnie nadmiarowy (LRS), magazyn strefowo nadmiarowy (ZRS), magazyn Geograficznie nadmiarowy (GRS) i geograficznie nadmiarowy magazyn (GZRS) (wersja zapoznawcza). Firma Microsoft planuje obsługę magazynu geograficznie nadmiarowego do odczytu (RA-GRS) w przyszłości, ale nie mamy już do udostępnienia osi czasu.

* <a id="tier-options"></a>
  **jakie warstwy magazynowania są obsługiwane w Azure Files?**  
    Azure Files obsługuje dwie warstwy magazynowania: Premium i Standard. Standardowe udziały plików są tworzone na kontach magazynu ogólnego przeznaczenia (GPv1 lub GPv2), a udziały plików w warstwie Premium są tworzone na kontach magazynu FileStorage. Dowiedz się więcej na temat tworzenia [standardowych udziałów plików](storage-how-to-create-file-share.md) i [udziałów plików w warstwie Premium](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > Nie można tworzyć udziałów plików platformy Azure z kont magazynu obiektów blob lub ogólnego przeznaczenia (GPv1 lub GPv2) w *warstwie Premium* . Standardowe udziały plików platformy Azure muszą zostać utworzone wyłącznie w ramach *standardowych* kont ogólnego przeznaczenia, a udziały plików platformy Azure w warstwie Premium muszą zostać utworzone tylko na kontach magazynu FileStorage. Konta magazynu ogólnego przeznaczenia (GPv1 i GPv2) są przeznaczone tylko dla stron sieci Web *w warstwie Premium* . 

* <a id="give-us-feedback"></a>
  **naprawdę chcę zobaczyć konkretną funkcję dodaną do Azure Files. Czy można je dodać?**  
    Zespół Azure Files ma na celu wysłuchanie wszelkich opinii na temat naszej usługi. Zagłosuj na żądania funkcji w witrynie [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Czekamy na poszukiwanie wielu nowych funkcji.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **jakie regiony są obsługiwane dla Azure File Sync?**  
    Listę dostępnych regionów można znaleźć w sekcji [dostępność regionu](storage-sync-files-planning.md#region-availability) w podręczniku planowania Azure File Sync. Ciągle będziemy dodawać obsługę dodatkowych regionów, w tym regionów niepublicznych.

* <a id="cross-domain-sync"></a>
  **można mieć przyłączone do domeny serwery i przyłączone do domeny w tej samej grupie synchronizacji?**  
    Tak. Grupa synchronizacji może zawierać punkty końcowe serwera, które mają różne członkostwa Active Directory, nawet jeśli nie są przyłączone do domeny. Mimo że ta konfiguracja technicznie działa, nie zalecamy jej jako typowej konfiguracji, ponieważ listy kontroli dostępu (ACL) zdefiniowane dla plików i folderów na jednym serwerze mogą nie być wymuszane przez inne serwery w grupie synchronizacji. Aby uzyskać najlepsze wyniki, zalecamy synchronizację między serwerami znajdującymi się w tym samym lesie Active Directory, między serwerami, które znajdują się w różnych Active Directory lasach, ale które ustanowiły relacje zaufania lub między serwerami, które nie należą do domeny. Zalecamy uniknięcie użycia kombinacji tych konfiguracji.

* <a id="afs-change-detection"></a>
  **utworzony plik bezpośrednio w udziale plików platformy Azure przy użyciu protokołu SMB lub w portalu. Jak długo trwa synchronizacja pliku z serwerami w grupie synchronizacji?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Jeśli ten sam plik zostanie zmieniony na dwóch serwerach w tym samym czasie, co się dzieje?**  
    Azure File Sync korzysta z prostej strategii rozwiązywania konfliktów: zachowujemy zmiany w plikach, które są zmieniane na dwóch serwerach w tym samym czasie. Ostatnio zapisywana zmiana zachowuje oryginalną nazwę pliku. Starszy plik zawiera maszynę "Źródło" i numer konfliktu dołączony do nazwy. Jest on zgodny z tą taksonomią: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\>\<EXT  

    Na przykład pierwszy konflikt CompanyReport. docx stanie się CompanyReport-CentralServer. docx, jeśli CentralServer jest miejsce, w którym wystąpił starszy zapis. Drugi konflikt zostałby nazwany CompanyReport-CentralServer-1. docx. Azure File Sync obsługuje pliki konfliktów 100 na plik. Po osiągnięciu maksymalnej liczby plików konfliktów plik nie zostanie zsynchronizowany, dopóki liczba plików konfliktów nie będzie mniejsza niż 100.

* <a id="afs-storage-redundancy"></a>
  **jest magazyn Geograficznie nadmiarowy obsługiwany dla Azure File Sync?**  
    Tak, Azure Files obsługuje Magazyn lokalnie nadmiarowy (LRS) i magazyn Geograficznie nadmiarowy (GRS). Jeśli zainicjujesz tryb failover konta magazynu między sparowanymi regionami na koncie skonfigurowanym dla usługi GRS, firma Microsoft zaleca traktowanie nowego regionu jako kopii zapasowej tylko danych. Azure File Sync nie rozpoczyna automatycznej synchronizacji z nowym regionem podstawowym. 

* <a id="sizeondisk-versus-size"></a>
  **Dlaczego rozmiar właściwości *dysku* dla pliku jest zgodny z właściwością *size* po użyciu Azure File Sync?**  
  Zobacz temat Obsługa [warstw w chmurze](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Jak mogę sprawdzić, czy plik został warstwowy?**  
  Zobacz temat Obsługa [warstw w chmurze](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Plik, którego chcę użyć, został warstwowy. Jak można przywołać plik na dysk, aby użyć go lokalnie?**  
  Zobacz temat Obsługa [warstw w chmurze](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Jak mogę Wymuś warstwowy plik lub katalog?**  
  Zobacz temat Obsługa [warstw w chmurze](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **jak jest interpretowane *wolne miejsce na woluminie* , gdy mam wiele punktów końcowych serwera na woluminie?**  
  Zobacz temat Obsługa [warstw w chmurze](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **które pliki lub foldery są automatycznie wykluczane przez Azure File Sync?**  
    Domyślnie Azure File Sync wyklucza następujące pliki:
  * plik Desktop. ini
  * kciuki. DB
  * ehThumbs. DB
  * \*~$.\*
  * \*. LACCDB
  * \*. tmp
  * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Następujące foldery są również Domyślnie wykluczone:

  * Informacje o woluminie \System
  * odzyskanie \$. OKREŚLONEJ
  * \SyncShareState

* <a id="afs-os-support"></a>
  **można używać Azure File Sync z systemem Windows Server 2008 R2, Linux lub na moim urządzeniu magazynującym podłączonym do sieci (nas)?**  
    Obecnie Azure File Sync obsługuje tylko systemy Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2. W tej chwili nie mamy żadnych innych planów, które możemy udostępnić, ale jesteśmy w stanie obsłużyć dodatkowe platformy na podstawie popytu klientów. Poinformuj nas o tym, co Azure Files platformę [UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) , które mają być obsługiwane przez firmę Microsoft.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Dlaczego pliki warstwowe istnieją poza przestrzenią nazw punktu końcowego serwera?**  
    Przed Azure File Sync agentem w wersji 3 Azure File Sync zablokował przenoszenie plików warstwowych poza punkt końcowy serwera, ale na tym samym woluminie, na którym znajduje się punkt końcowy serwera. Operacje kopiowania, przenoszenia plików niewarstwowych i przenoszenia warstwowych do innych woluminów nie były naruszone. Przyczyną tego zachowania było niejawne założenie, że Eksplorator plików i inne interfejsy API systemu Windows mają te operacje przenoszenia na tym samym woluminie (niemal) operacji zmiany nazwy. Oznacza to, że przeniesienie spowoduje, że Eksplorator plików lub inne metody przenoszenia (takie jak wiersz polecenia lub program PowerShell) nie odpowiada, podczas gdy Azure File Sync ponownie wywołuje dane z chmury. Począwszy od [Azure File Sync agenta w wersji 3.0.12.0](storage-files-release-notes.md#supported-versions), Azure File Sync umożliwi przeniesienie pliku warstwowego poza punkt końcowy serwera. Pozwalamy uniknąć negatywnych skutków wcześniej wymienionych przez umożliwienie, aby plik warstwowy istniał jako plik warstwowy spoza punktu końcowego serwera, a następnie ponownie wywołuje plik w tle. Oznacza to, że przenoszone na tym samym woluminie są chwilowe, a wszystkie prace do odwoływania pliku do dysku po zakończeniu przenoszenia. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **mam problem z Azure File Sync na serwerze (synchronizacja, Obsługa warstw w chmurze itp.). Czy należy usunąć i ponownie utworzyć mój punkt końcowy serwera?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **można przenieść usługę synchronizacji magazynu i/lub konto magazynu do innej grupy zasobów lub subskrypcji?**  
   Tak, usługa synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu jest przenoszone, musisz udzielić hybrydowej usłudze File Sync dostępu do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync nie obsługuje przeniesienia subskrypcji do innej dzierżawy usługi Azure AD.
    
* <a id="afs-ntfs-acls"></a>
  **Azure File Sync zachować listy ACL systemu plików NTFS na poziomie katalogu i pliku oraz dane przechowywane w Azure Files?**

    Listy ACL systemu plików NTFS przenoszone z lokalnych serwerów plików są utrwalane przez Azure File Sync jako metadane. Azure Files nie obsługuje uwierzytelniania przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do udziałów plików zarządzanych przez usługę Azure File Sync.
    
## <a name="security-authentication-and-access-control"></a>Zabezpieczenia, uwierzytelnianie i kontrola dostępu
* <a id="ad-support"></a>
**jest uwierzytelnianie oparte na tożsamościach i kontrola dostępu obsługiwane przez Azure Files?**  
    
    Tak, Azure Files obsługuje uwierzytelnianie oparte na tożsamościach i kontrolę dostępu przy użyciu usługi Azure AD Domain Service (Azure AD DS). Usługa Azure AD DS Authentication za pośrednictwem protokołu SMB dla Azure Files umożliwia maszynom wirtualnym z systemem Windows przyłączonych do domeny usługi Azure AD DS dostęp do udziałów, katalogów i plików przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie obsługi uwierzytelniania protokołu SMB w usługach Azure Files Azure Active Directory Domain Service (Azure AD DS)](storage-files-active-directory-overview.md). 

    Azure Files oferuje dwa dodatkowe sposoby zarządzania kontrolą dostępu:

    - Sygnatury dostępu współdzielonego (SAS) mogą być używane do generowania tokenów z określonymi uprawnieniami, które są prawidłowe przez określony przedział czasu. Na przykład można wygenerować token z dostępem tylko do odczytu do określonego pliku o 10-minutowym wygaśnięciu. Każda osoba, która posiada token, gdy token jest ważny, ma dostęp tylko do odczytu do tego pliku przez 10 minut. Klucze sygnatury dostępu współdzielonego są obsługiwane tylko za pośrednictwem interfejsu API REST lub bibliotek klienckich. Udział plików platformy Azure należy zainstalować za pośrednictwem protokołu SMB przy użyciu kluczy konta magazynu.

    - Azure File Sync zachowuje i replikuje wszystkie poufne listy kontroli dostępu lub listy DACL (niezależnie od tego, czy Active Directory lub lokalne) wszystkie punkty końcowe serwera, do których jest synchronizowana. Ponieważ system Windows Server może już być uwierzytelniany przy użyciu Active Directory, Azure File Sync jest efektywną opcją zatrzymania, dopóki nie zostanie wypełniona pełna obsługa uwierzytelniania opartego na Active Directory i obsługi list ACL.
    
    Aby uzyskać kompleksową reprezentację wszystkich protokołów obsługiwanych przez usługi Azure Storage, można przyjrzeć się do [autoryzacji dostępu do usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) . 

* <a id="ad-support-devices"></a>
**Azure Files uwierzytelnianie usługi azure AD DS obsługują dostęp do protokołu SMB przy użyciu poświadczeń usługi Azure AD z urządzeń przyłączonych do lub zarejestrowanych w usłudze Azure AD?**

    Nie, ten scenariusz nie jest obsługiwany.

* <a id="ad-support-rest-apis"></a>
istnieją **interfejsy API REST obsługujące pobieranie/Ustawianie/kopiowanie katalogów/plików list ACL systemu plików NTFS?**

    Obecnie nie obsługujemy interfejsów API REST w celu pobierania, ustawiania lub kopiowania list ACL systemu plików NTFS dla katalogów lub plików.

* <a id="ad-vm-subscription"></a>
**mogę uzyskać dostęp do Azure Files przy użyciu poświadczeń usługi Azure AD z maszyny wirtualnej w ramach innej subskrypcji?**

    Jeśli subskrypcja, w ramach której wdrożono udział plików, jest skojarzona z tą samą dzierżawą usługi Azure AD, co wdrożenie Azure AD Domain Services, do którego jest przyłączona maszyna wirtualna, można uzyskać dostęp do Azure Files przy użyciu tych samych poświadczeń usługi Azure AD. Ograniczenie jest nakładane nie na subskrypcję, ale w skojarzonej dzierżawie usługi Azure AD.    
    
* <a id="ad-support-subscription"></a>
**można włączyć Azure Files uwierzytelniania AD DS platformy Azure za pomocą dzierżawy usługi Azure AD innej niż główna dzierżawa, z którą jest skojarzony udział plików?**

    Nie, Azure Files obsługuje tylko integrację z usługą Azure AD DS z dzierżawą usługi Azure AD, która znajduje się w tej samej subskrypcji co udział plików. Tylko jedna subskrypcja może być skojarzona z dzierżawą usługi Azure AD.

* <a id="ad-linux-vms"></a>
**Azure Files usługi Azure AD DS do obsługi maszyn wirtualnych z systemem Linux?**

    Nie, uwierzytelnianie z maszyn wirtualnych systemu Linux nie jest obsługiwane.

* <a id="ad-aad-smb-afs"></a>
**można wykorzystać Azure Files uwierzytelniania AD DS platformy Azure dla udziałów plików zarządzanych przez Azure File Sync?**

    Nie, Azure Files nie obsługuje zachowywania list ACL systemu plików NTFS w udziałach plików zarządzanych przez Azure File Sync. Listy ACL plików przenoszone z lokalnych serwerów plików są utrwalane przez Azure File Sync. Wszystkie listy ACL systemu NTFS skonfigurowane natywnie dla Azure Files zostaną zastąpione przez usługę Azure File Sync. Ponadto Azure Files nie obsługuje uwierzytelniania przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do udziałów plików zarządzanych przez usługę Azure File Sync.

* <a id="encryption-at-rest"></a>
**Jak upewnić się, że mój udział plików platformy Azure jest szyfrowany w spoczynku?**  

    Tak. Aby uzyskać więcej informacji, zobacz [Azure szyfrowanie usługi Storage](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**jak zapewnić dostęp do określonego pliku przy użyciu przeglądarki sieci Web?**  

    Sygnatury dostępu współdzielonego umożliwiają generowanie tokenów z określonymi uprawnieniami, które są prawidłowe przez określony przedział czasu. Na przykład można wygenerować token, który zapewnia dostęp tylko do odczytu do określonego pliku, przez określony czas. Każdy posiadacz adresu URL może uzyskać dostęp do pliku bezpośrednio z dowolnej przeglądarki sieci Web, gdy token jest prawidłowy. Możesz łatwo wygenerować klucz sygnatury dostępu współdzielonego z interfejsu użytkownika, takiego jak Eksplorator usługi Storage.

* <a id="file-level-permissions"></a>
czy **można określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w ramach udziału?**  

    W przypadku zainstalowania udziału plików przy użyciu protokołu SMB nie ma uprawnień na poziomie folderów. Jeśli jednak utworzysz sygnaturę dostępu współdzielonego za pomocą interfejsu API REST lub bibliotek klienckich, możesz określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w udziale.

* <a id="ip-restrictions"></a>
**można zaimplementować ograniczenia adresów IP dla udziału plików platformy Azure?**  

    Tak. Dostęp do udziału plików platformy Azure można ograniczyć na poziomie konta magazynu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**jakie zasady zgodności danych są obsługiwane Azure Files?**  

   Azure Files uruchamiana na podstawie tej samej architektury magazynu, która jest używana w innych usługach magazynu w usłudze Azure Storage. Azure Files stosuje te same zasady zgodności danych, które są używane w innych usługach Azure Storage. Aby uzyskać więcej informacji na temat zgodności danych usługi Azure Storage, możesz zapoznać się z [ofertą zgodności z usługą Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings), a następnie przejść do [Centrum zaufania firmy Microsoft](https://microsoft.com/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Dostęp lokalny

* <a id="port-445-blocked"></a>
**mojego usługodawcy internetowego lub blokuje Port 445, który kończy się niepowodzeniem Azure Files instalacji. Co mam zrobić?**

    Można dowiedzieć się więcej [na temat różnych sposobów obejścia zablokowanego portu 445 w tym miejscu](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Azure Files zezwala tylko na połączenia przy użyciu protokołu SMB 3,0 (z obsługą szyfrowania) spoza regionu lub centrum danych. Protokół SMB 3,0 wprowadził wiele funkcji zabezpieczeń, w tym szyfrowanie kanałów, które są bardzo bezpieczne do użycia w Internecie. Możliwe, że port 445 został zablokowany ze względu na historyczne przyczyny luk w zabezpieczeniach, które znajdują się w mniejszych wersjach protokołu SMB. W idealnym przypadku port powinien być blokowany tylko dla ruchu SMB 1,0, a protokół SMB 1,0 powinien być wyłączony na wszystkich klientach.

* <a id="expressroute-not-required"></a>
**używać usługi Azure ExpressRoute do nawiązywania połączenia z Azure Files lub korzystania z Azure File Sync lokalnego?**  

    Nie. ExpressRoute nie jest wymagana w celu uzyskania dostępu do udziału plików platformy Azure. Jeśli instalujesz udział plików platformy Azure bezpośrednio lokalnie, wymagany jest tylko port 445 (ruch wychodzący TCP) otwarty na potrzeby dostępu do Internetu (jest to port wykorzystywany przez protokół SMB do komunikacji). Jeśli używasz Azure File Sync, wymagany jest tylko port 443 (ruch wychodzący TCP) dla dostępu HTTPS (brak wymaganego protokołu SMB). *Można* jednak używać ExpressRoute z jedną z tych opcji dostępu.

* <a id="mount-locally"></a>
**jak zainstalować udział plików platformy Azure na mojej maszynie lokalnej?**  

    Udział plików można zainstalować przy użyciu protokołu SMB, jeśli jest otwarty port 445 (ruch wychodzący TCP), a klient obsługuje protokół SMB 3,0 (na przykład jeśli używasz systemu Windows 10 lub Windows Server 2016). Jeśli port 445 jest blokowany przez zasady organizacji lub przez usługodawcę internetowego, możesz użyć Azure File Sync, aby uzyskać dostęp do udziału plików platformy Azure.

## <a name="backup"></a>Tworzenie kopii zapasowych
* <a id="backup-share"></a>
**Jak mogę utworzyć kopię zapasową udziału plików platformy Azure?**  
    Można użyć okresowych [migawek udziałów](storage-snapshots-files.md) do ochrony przed przypadkowym usunięciem. Można również użyć narzędzia do tworzenia kopii zapasowych AzCopy, Robocopy lub innych firm, które umożliwia utworzenie kopii zapasowej zainstalowanego udziału plików. Azure Backup oferuje kopię zapasową Azure Files. Dowiedz się więcej [na temat tworzenia kopii zapasowych udziałów plików platformy Azure przez Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Migawki udziałów

### <a name="share-snapshots-general"></a>Migawki udziałów: ogólne
* <a id="what-are-snaphots"></a>
**czym są migawki udziałów plików?**  
    Migawek udziałów plików platformy Azure można użyć do utworzenia wersji tylko do odczytu udziałów plików. Możesz również użyć Azure Files, aby skopiować wcześniejszą wersję zawartości z powrotem do tego samego udziału, do alternatywnej lokalizacji na platformie Azure lub lokalnie, aby uzyskać więcej modyfikacji. Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Omówienie udostępniania migawek](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**gdzie przechowywane są migawki udziałów?**  
    Migawki udziałów są przechowywane na tym samym koncie magazynu co udział plików.

* <a id="snapshot-consistency"></a>
**są udostępnianie migawek spójnych z aplikacjami?**  
    Nie, migawka udziałów nie jest spójna z aplikacją. Przed przeprowadzeniem migawki udziału użytkownik musi opróżnić zapisy z aplikacji do udziału.

* <a id="snapshot-limits"></a>
**są ograniczenia liczby migawek udziału, których można użyć?**  
    Tak. Azure Files może obsłużyć maksymalnie 200 migawek udziałów. Migawki udziałów nie są wliczane do limitu przydziału udziału, więc nie ma żadnego udziału na łączny obszar, który jest używany przez wszystkie migawki udziałów. Nadal obowiązują limity kont magazynu. W przypadku migawek udziałów 200 należy usunąć starsze migawki w celu utworzenia nowych migawek udziałów.

* <a id="snapshot-cost"></a>
**ile kosztuje udział migawek?**  
    Do migawki są stosowane standardowe transakcje i standardowe opłaty za magazyn. Migawki mają charakter przyrostowy. Podstawową migawką jest sam udział. Wszystkie kolejne migawki są przyrostowe i będą przechowywać różnice z poprzedniej migawki. Oznacza to, że zmiany różnicowe, które będą widoczne na rachunku, będą minimalne, jeśli obciążenie obciążeń jest minimalne. Informacje o cenach standardowych Azure Files można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/) . Dzisiaj, aby zobaczyć rozmiar używany przez migawkę udziału, można porównać rozliczane zdolności produkcyjne z zużywaną pojemnością. Pracujemy nad narzędziami, aby usprawnić raportowanie.

* <a id="ntfs-acls-snaphsots"></a>
**to listy ACL systemu plików NTFS dotyczące katalogów i plików utrwalonych w migawkach udziałów?**  
    Listy ACL NTFS dotyczące katalogów i plików są utrwalane w migawkach udziałów.

### <a name="create-share-snapshots"></a>Tworzenie migawek udziałów
* <a id="file-snaphsots"></a>
**można utworzyć migawkę udziału poszczególnych plików?**  
    Migawki udziałów są tworzone na poziomie udziału plików. Można przywrócić pojedyncze pliki z migawki udziału plików, ale nie można utworzyć migawek udziałów na poziomie pliku. Jeśli jednak masz migawkę udziału na poziomie udziału i chcesz wyświetlić migawki udziałów, w których określony plik został zmieniony, możesz to zrobić w **poprzednich wersjach** w udziale zainstalowanym w systemie Windows. 
    
    Jeśli potrzebujesz funkcji migawek plików, powiadom nas o [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**można utworzyć migawki udziałów dla zaszyfrowanego udziału plików?**  
    Możesz wykonać migawkę udziału udziałów plików platformy Azure z włączonym szyfrowaniem. Można przywrócić pliki z migawki udziału do zaszyfrowanego udziału plików. Jeśli udział jest szyfrowany, migawka udziału również jest zaszyfrowana.

* <a id="geo-redundant-snaphsots"></a>
**są geograficznie nadmiarowe migawki udostępnione?**  
    Migawki udziałów mają taką samą nadmiarowość jak udział plików platformy Azure, dla którego zostały wykonane. W przypadku wybrania magazynu geograficznie nadmiarowego dla Twojego konta migawka udziału również jest przechowywana w sposób nadmiarowy w sparowanym regionie.

### <a name="manage-share-snapshots"></a>Zarządzanie migawkami udziałów
* <a id="browse-snapshots-linux"></a>
mogę **przeglądać moje migawki udostępniania z systemu Linux?**  
    Za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć, wyświetlać, przeglądać i przywracać migawki udziałów w systemie Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**można skopiować migawki udziału na inne konto magazynu?**  
    Można skopiować pliki z migawek udziałów do innej lokalizacji, ale nie można skopiować samych migawek udziałów.

### <a name="restore-data-from-share-snapshots"></a>Przywracanie danych z migawek udziałów
* <a id="promote-share-snapshot"></a>
**można podwyższyć poziom migawki udziału do udziału podstawowego?**  
    Dane z migawki udziału można kopiować do innych miejsc docelowych. Nie można podwyższyć poziomu migawki udziału do udziału podstawowego.

* <a id="restore-snapshotted-file-to-other-share"></a>
**można przywrócić dane z migawki udostępniania na inne konto magazynu?**  
    Tak. Pliki z migawki udziału można skopiować do oryginalnej lokalizacji lub do alternatywnej lokalizacji, która zawiera to samo konto magazynu lub inne konto magazynu w tym samym regionie lub w różnych regionach. Możesz również kopiować pliki do lokalizacji lokalnej lub innej chmury.    
  
### <a name="clean-up-share-snapshots"></a>Czyszczenie migawek udziałów
* <a id="delete-share-keep-snapshots"></a>
mogę **usunąć mój udział, ale nie usunąć moich migawek udostępniania?**  
    Jeśli masz aktywne migawki udziałów w udziale, nie możesz usunąć Twojego udziału. Możesz użyć interfejsu API, aby usunąć migawki udziałów wraz z udziałem. Można również usunąć zarówno migawki udziału, jak i udział w Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**co się dzieje z migawkami udziału w przypadku usunięcia konta magazynu?**  
    Usunięcie konta magazynu spowoduje również usunięcie migawek udziałów.

## <a name="billing-and-pricing"></a>Rozliczenia i Cennik
* <a id="vm-file-share-network-traffic"></a>
**czy ruch sieciowy między maszyną wirtualną platformy Azure a udziałem plików platformy Azure jest liczony jako zewnętrzna przepustowość, która jest naliczana dla subskrypcji?**  
    Jeśli udział plików i maszyna wirtualna znajdują się w tym samym regionie świadczenia usługi Azure, nie ma dodatkowej opłaty za ruch między udziałem plików i maszyną wirtualną. Jeśli udział plików i maszyna wirtualna znajdują się w różnych regionach, ruch między nimi jest naliczany jako przepustowość zewnętrzna.

* <a id="share-snapshot-price"></a>
**ile kosztuje udział migawek?**  
     W trakcie okresu zapoznawczego nie ma opłat za pojemność migawki udziału. Obowiązują standardowe dane wyjściowe magazynu i koszty transakcji. Po uzyskaniu ogólnej dostępności subskrypcje będą obciążane opłatami za zasoby i transakcje na migawek udziałów.
     
     Migawki udziałów mają charakter przyrostowy. Migawką udziału podstawowego jest sam udział. Wszystkie kolejne migawki udziałów są przyrostowe i przechowują tylko różnice z poprzedniej migawki udziału. Opłaty są naliczane tylko za zmienioną zawartość. Jeśli masz udział z 100 GiB danych, ale tylko 5 GiB uległo zmianie od czasu ostatniej migawki udziału, migawka udziału wykorzystuje tylko 5 dodatkowych GiB, a opłaty są naliczane za 105 GiB. Aby uzyskać więcej informacji na temat transakcji i standardowych opłat wychodzących, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skalowanie i wydajność
* <a id="files-scale-limits"></a>
**jakie są limity skalowania Azure Files?**  
    Aby uzyskać informacje o skalowalności i wydajności dla Azure Files, zobacz [Azure Files cele dotyczące skalowalności i wydajności](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**jakie rozmiary są dostępne dla udziałów plików platformy Azure?**  
    Rozmiary udziałów plików platformy Azure (Premium i standard) można skalować w górę do 100 TiB. Zapoznaj się z sekcją dołączanie [do większej liczby udziałów plików (warstwa standardowa)](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) przewodnika planowania dla instrukcji dołączania do większych udziałów plików dla warstwy Standardowa.

* <a id="lfs-performance-impact"></a>
**rozszerza limit przydziału udziału plików na moje obciążenia lub Azure File Sync?**
    
    Nie. Zwiększenie przydziału nie będzie miało wpływu na obciążenia lub Azure File Sync.

* <a id="open-handles-quota"></a>
**ilu klientów może jednocześnie uzyskać dostęp do tego samego pliku?**    
    W pojedynczym pliku występuje limit przydziału liczby otwartych dojścia do 2 000. Jeśli masz otwarte dojścia do 2 000, zostanie wyświetlony komunikat o błędzie informujący o osiągnięciu limitu przydziału.

* <a id="zip-slow-performance"></a>
**moja wydajność jest niska, gdy rozpakuję pliki w Azure Files. Co mam zrobić?**  
    Aby przenieść dużą liczbę plików do Azure Files, zalecamy użycie AzCopy (dla systemu Windows; w wersji zapoznawczej dla systemów Linux i UNIX) lub Azure PowerShell. Te narzędzia zostały zoptymalizowane pod kątem transferu sieciowego.

* <a id="slow-perf-windows-81-2012r2"></a>
**Dlaczego moja wydajność jest niska po zainstalowaniu udziału plików platformy Azure w systemie Windows Server 2012 R2 lub Windows 8.1?**  
    Występuje znany problem podczas instalowania udziału plików platformy Azure w systemie Windows Server 2012 R2 i Windows 8.1. Problem został poprawiony w aktualizacji zbiorczej 2014 kwietnia Windows 8.1 i Windows Server 2012 R2. W celu uzyskania optymalnej wydajności upewnij się, że dla wszystkich wystąpień systemu Windows Server 2012 R2 i Windows 8.1 zastosowano tę poprawkę. (Zawsze należy odbierać poprawki systemu Windows za Windows Update.) Aby uzyskać więcej informacji, zapoznaj się z artykułem dotyczącym powolnej wydajności programu Microsoft Knowledge Base w [przypadku uzyskiwania dostępu do Azure Files z Windows 8.1 lub Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkcje i współdziałanie z innymi usługami
* <a id="cluster-witness"></a>
**można użyć mojego udziału plików platformy Azure jako *monitora udziału plików* dla mojego klastra trybu failover systemu Windows Server?**  
    Obecnie ta konfiguracja nie jest obsługiwana w przypadku udziału plików platformy Azure. Aby uzyskać więcej informacji na temat sposobu konfigurowania tego programu dla usługi Azure Blob Storage, zobacz [wdrażanie monitora chmury dla klastra trybu failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**można zainstalować udział plików platformy Azure w wystąpieniu kontenera platformy Azure?**  
    Tak, udziały plików platformy Azure są dobrym rozwiązaniem, gdy chcesz utrwalać informacje poza okresem istnienia wystąpienia kontenera. Aby uzyskać więcej informacji, zobacz [Instalowanie udziału plików platformy Azure przy użyciu usługi Azure Container Instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**czy istnieje operacja zmiany nazwy w interfejsie API REST?**  
    Nie w tej chwili.

* <a id="nested-shares"></a>
**można skonfigurować udziały zagnieżdżone? Innymi słowy, udział w udziale?**  
    Nie. Udział plików *jest* wirtualnym sterownikiem, który można zainstalować, więc udziały zagnieżdżone nie są obsługiwane.

* <a id="ibm-mq"></a>
**Jak mogę użyć Azure Files z IBM MQ?**  
    Firma IBM wydała dokument, który pomaga klientom firmy IBM MQ skonfigurować Azure Files z usługą firmy IBM. Aby uzyskać więcej informacji, zobacz [jak skonfigurować usługę zarządzania wieloma wystąpieniami programu IBM MQ z usługą Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Zobacz też
* [Rozwiązywanie problemów Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Rozwiązywanie problemów Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)
