---
title: Często zadawane pytania dotyczące Azure NetApp Files | Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: b-juche
ms.openlocfilehash: aaa7e5e65ced2a9899bef5a811ee74be42a8548f
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048811"
---
# <a name="faqs-about-azure-netapp-files"></a>Często zadawane pytania dotyczące Azure NetApp Files

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure NetApp Files. 

## <a name="networking-faqs"></a>Często zadawane pytania dotyczące sieci

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Czy ścieżka danych systemu plików NFS przechodzi przez Internet?  

Nie. Ścieżka danych NFS nie przechodzi przez Internet. Azure NetApp Files to usługa Azure Native, która jest wdrażana w usłudze Azure Virtual Network (VNet), gdzie usługa jest dostępna. Azure NetApp Files używa delegowanej podsieci i udostępnia interfejs sieciowy bezpośrednio w sieci wirtualnej. 

Aby uzyskać szczegółowe informacje, zobacz [wytyczne dotyczące planowania sieci Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Czy mogę połączyć sieć wirtualną, która została już utworzona, do usługi Azure NetApp Files?

Tak, możesz połączyć sieci wirtualnych utworzone w usłudze. 

Aby uzyskać szczegółowe informacje, zobacz [wytyczne dotyczące planowania sieci Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) .  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Czy mogę zainstalować wolumin systemu plików NFS Azure NetApp Files przy użyciu nazwy FQDN DNS?

Tak, możesz w przypadku utworzenia wymaganych wpisów DNS. Azure NetApp Files dostarcza adres IP usługi dla woluminu aprowizacji. 

> [!NOTE] 
> W razie potrzeby Azure NetApp Files mogą wdrożyć dodatkowe adresy IP dla usługi.  Może być konieczne okresowe aktualizowanie wpisów DNS.

## <a name="security-faqs"></a>Często zadawane pytania dotyczące zabezpieczeń

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Czy ruch sieciowy między maszyną wirtualną platformy Azure a magazynem jest szyfrowany?

Ruch danych (ruch z klienta z NFSv3, NFSv 4.1 lub SMBv3 do woluminów Azure NetApp Files) nie jest szyfrowany. Jednak ruch z maszyny wirtualnej platformy Azure (z systemem plików NFS lub klienta SMB) do Azure NetApp Files jest tak bezpieczny jak każdy inny ruch z maszyny wirtualnej na maszynę wirtualną. Ten ruch jest lokalny dla sieci centrów danych platformy Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Czy magazyn może być szyfrowany w spoczynku?

Wszystkie woluminy Azure NetApp Files są szyfrowane przy użyciu standardu FIPS 140-2. Wszystkie klucze są zarządzane przez usługę Azure NetApp Files. 

### <a name="how-are-encryption-keys-managed"></a>Jak zarządzane są klucze szyfrowania? 

Zarządzanie kluczami dla Azure NetApp Files jest obsługiwane przez usługę. Dla każdego woluminu jest generowany unikatowy klucz szyfrowania danych XTS-AES-256. Hierarchia kluczy szyfrowania służy do szyfrowania i ochrony wszystkich kluczy woluminów. Te klucze szyfrowania nigdy nie są wyświetlane ani raportowane w nieszyfrowanym formacie. Klucze szyfrowania są usuwane natychmiast po usunięciu woluminu.

Obecnie klucze zarządzane przez użytkownika (nie należy wprowadzać własnych kluczy) nie są obsługiwane.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Czy można skonfigurować zasady eksportowania systemu plików NFS, aby kontrolować dostęp do celu instalacji usługi Azure NetApp Files Service?


Tak, można skonfigurować maksymalnie pięć reguł w ramach jednej zasady eksportowania NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Czy Azure NetApp Files obsługiwać sieciowe grupy zabezpieczeń?

Nie, obecnie nie można zastosować grup zabezpieczeń sieci do delegowanej podsieci Azure NetApp Files lub interfejsów sieciowych utworzonych przez usługę.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Czy mogę używać usługi Azure IAM z Azure NetApp Files?

Tak, Azure NetApp Files obsługuje funkcje RBAC w usłudze Azure IAM.

## <a name="performance-faqs"></a>Często zadawane pytania dotyczące wydajności

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Co mam zrobić, aby zoptymalizować lub dostroić wydajność Azure NetApp Files?

Zgodnie z wymaganiami dotyczącymi wydajności można wykonać następujące działania: 
- Upewnij się, że rozmiar maszyny wirtualnej jest odpowiedni.
- Włącz przyspieszone sieci dla maszyny wirtualnej.
- Wybierz żądany poziom usług i rozmiar puli pojemności.
- Utwórz wolumin z żądanym rozmiarem przydziału dla pojemności i wydajności.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Jak mogę skonwertować poziomy usługi Azure NetApp Files na operacje we/wy na sekundę?

Możesz skonwertować MB/s na operacje we/wy na sekundę, korzystając z następującej formuły:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Jak mogę zmienić poziomu usługi woluminu?

Zmiana poziomu usługi woluminu nie jest obecnie obsługiwana.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Jak mogę monitorować Azure NetApp Files wydajności?

Azure NetApp Files udostępnia metryki wydajności woluminu. Można również użyć Azure Monitor do monitorowania metryk użycia dla Azure NetApp Files.  Aby uzyskać listę metryk wydajności dla Azure NetApp Files, zobacz [metryki dla Azure NetApp Files](azure-netapp-files-metrics.md) .

## <a name="nfs-faqs"></a>Często zadawane pytania dotyczące NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Chcę, aby wolumin został zainstalowany automatycznie podczas uruchamiania lub ponownego uruchamiania maszyny wirtualnej platformy Azure.  Jak mogę skonfigurować mój Host dla trwałych woluminów NFS?

Aby wolumin systemu plików NFS został automatycznie zainstalowany podczas uruchamiania lub ponownego uruchamiania maszyny wirtualnej, Dodaj wpis do pliku `/etc/fstab` na hoście. 

Aby uzyskać szczegółowe informacje [, zobacz Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) .  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Dlaczego polecenie DF na kliencie NFS nie pokazuje rozmiaru woluminu, który został zainicjowany?

Rozmiar woluminu raportowany w DF jest maksymalnym rozmiarem, do którego można zwiększyć wolumin Azure NetApp Files. Rozmiar woluminu Azure NetApp Files w DF polecenia nie odzwierciedla przydziału ani rozmiaru woluminu.  Azure NetApp Files rozmiaru woluminu lub przydziału można uzyskać za pomocą Azure Portal lub interfejsu API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Jakie wersje systemu plików NFS obsługuje Azure NetApp Files?

Azure NetApp Files obsługuje NFSv3 i NFSv 4.1. Wolumin można utworzyć przy użyciu dowolnej wersji systemu plików NFS. 

> [!IMPORTANT] 
> Dostęp do funkcji NFSv 4.1 wymaga listy dozwolonych.  Aby zażądać listy dozwolonych, Prześlij żądanie do <anffeedback@microsoft.com>. 


### <a name="how-do-i-enable-root-squashing"></a>Jak mogę włączyć zgniatanie głównego?

Główny zgniatanie nie jest obecnie obsługiwany.

## <a name="smb-faqs"></a>Funkcja SMB — często zadawane pytania

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Czy Active Directory jest wymagane połączenie z dostępem do protokołu SMB? 

Tak, przed wdrożeniem woluminu SMB należy utworzyć połączenie Active Directory. Określone kontrolery domeny muszą być dostępne przez delegowaną podsieć Azure NetApp Files w celu pomyślnego nawiązania połączenia.  Aby uzyskać szczegółowe informacje, zobacz [Tworzenie woluminu SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) . 

### <a name="how-many-active-directory-connections-are-supported"></a>Ile połączeń Active Directory są obsługiwane?

Azure NetApp Files nie obsługuje wielu połączeń Active Directory (AD) w jednym *regionie*, nawet jeśli połączenia usługi AD znajdują się na różnych kontach NetApp. Można jednak korzystać z wielu połączeń usługi AD w ramach jednej *subskrypcji*, o ile połączenia usługi AD znajdują się w różnych regionach. Jeśli potrzebujesz wielu połączeń usługi AD w jednym regionie, możesz to zrobić za pomocą osobnych subskrypcji. 

Skonfigurowano połączenie usługi AD na konto NetApp; połączenie z usługą AD jest widoczne tylko za pomocą konta NetApp, które zostało utworzone w programie.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Czy Azure NetApp Files obsługiwać Azure Active Directory? 

Obsługiwane są zarówno [usługi domenowe Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) , jak i [Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Istniejących Active Directory kontrolerów domeny można użyć z Azure NetApp Files. Kontrolery domeny mogą znajdować się na platformie Azure jako maszyny wirtualne lub lokalnie za pośrednictwem ExpressRoute lub sieci VPN S2S. W tej chwili Azure NetApp Files nie obsługuje funkcji AD Join dla [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) .

Jeśli używasz Azure NetApp Files z Azure Active Directory Domain Services, ścieżka jednostki organizacyjnej jest `OU=AADDC Computers` podczas konfigurowania Active Directory dla konta NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Jakie wersje systemu Windows Server Active Directory są obsługiwane?

Azure NetApp Files obsługuje wersje Active Directory Domain Services systemu Windows Server 2008r2SP1-2019.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Dlaczego dostęp do dostępnego miejsca na kliencie SMB nie jest wyświetlany?

Rozmiar woluminu zgłoszonego przez klienta SMB to maksymalny rozmiar, do którego można zwiększyć wolumin Azure NetApp Files. Rozmiar woluminu Azure NetApp Files, jak pokazano na kliencie SMB nie jest odzwierciedleniem przydziału lub rozmiaru woluminu. Azure NetApp Files rozmiaru woluminu lub przydziału można uzyskać za pomocą Azure Portal lub interfejsu API.

## <a name="capacity-management-faqs"></a>Często zadawane pytania dotyczące zarządzania pojemnością

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Jak mogę monitorować użycie puli pojemności i woluminu Azure NetApp Files? 

Azure NetApp Files zapewnia pulę pojemności i metryki użycia woluminu. Za pomocą Azure Monitor można także monitorować użycie Azure NetApp Files. Aby uzyskać szczegółowe informacje, zobacz [metryki dla Azure NetApp Files](azure-netapp-files-metrics.md) . 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Czy mogę zarządzać Azure NetApp Files przez Eksplorator usługi Azure Storage?

Nie. Azure NetApp Files nie jest obsługiwana przez Eksplorator usługi Azure Storage.

## <a name="data-migration-and-protection-faqs"></a>Często zadawane pytania dotyczące migracji i ochrony danych

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Jak mogę zmigrować dane do Azure NetApp Files?
Azure NetApp Files udostępnia woluminy NFS i SMB.  Do migracji danych do usługi można użyć dowolnego narzędzia do kopiowania opartego na plikach. 

NetApp oferuje rozwiązanie oparte na SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Rozwiązanie umożliwia replikowanie danych NFS lub SMB do Azure NetApp Files eksportu NFS lub udziałów SMB. 

Możesz również użyć szerokiej tablicy bezpłatnych narzędzi do kopiowania danych. W przypadku systemu plików NFS można używać narzędzi obciążeń, takich jak [rsync](https://rsync.samba.org/examples.html) , do kopiowania i synchronizowania danych źródłowych na wolumin Azure NetApp Files. W przypadku protokołu SMB można używać obciążeń [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) w taki sam sposób.  Te narzędzia mogą również replikować uprawnienia do pliku lub folderu. 

Wymagania dotyczące migracji danych z lokalnego do Azure NetApp Files są następujące: 

- Upewnij się, że Azure NetApp Files jest dostępna w docelowym regionie platformy Azure.
- Sprawdź poprawność łączności sieciowej między źródłem i Azure NetApp Files adresem IP woluminu docelowego. Transfer danych między środowiskiem lokalnym a usługą Azure NetApp Files jest obsługiwany w porównaniu z ExpressRoute.
- Utwórz docelowy wolumin Azure NetApp Files.
- Prześlij dane źródłowe do woluminu docelowego za pomocą preferowanego narzędzia do kopiowania plików.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Jak mogę utworzyć kopię woluminu Azure NetApp Files w innym regionie świadczenia usługi Azure?
    
Azure NetApp Files udostępnia woluminy NFS i SMB.  Wszystkie narzędzia do kopiowania na podstawie plików mogą służyć do replikowania danych między regionami platformy Azure. 

Usługa NetApp oferuje rozwiązanie oparte na SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Rozwiązanie umożliwia replikowanie danych NFS lub SMB do Azure NetApp Files eksportu NFS lub udziałów SMB. 

Możesz również użyć szerokiej tablicy bezpłatnych narzędzi do kopiowania danych. W przypadku systemu plików NFS można używać narzędzi obciążeń, takich jak [rsync](https://rsync.samba.org/examples.html) , do kopiowania i synchronizowania danych źródłowych na wolumin Azure NetApp Files. W przypadku protokołu SMB można używać obciążeń [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) w taki sam sposób.  Te narzędzia mogą również replikować uprawnienia do pliku lub folderu. 

Wymagania dotyczące replikowania woluminu Azure NetApp Files do innego regionu platformy Azure są następujące: 
- Upewnij się, że Azure NetApp Files jest dostępna w docelowym regionie platformy Azure.
- Sprawdź poprawność łączności sieciowej między sieci wirtualnych w każdym regionie. Obecnie globalna komunikacja równorzędna między sieci wirtualnych nie jest obsługiwana.  Połączenie między usługą sieci wirtualnych można ustanowić, łącząc się z obwodem usługi ExpressRoute lub przy użyciu połączenia sieci VPN S2S. 
- Utwórz docelowy wolumin Azure NetApp Files.
- Prześlij dane źródłowe do woluminu docelowego za pomocą preferowanego narzędzia do kopiowania plików.

### <a name="is-migration-with-azure-data-box-supported"></a>Czy migracja z Azure Data Box jest obsługiwana?

Nie. Azure Data Box obecnie nie obsługuje Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Czy migracja z usługą Azure Import/Export jest obsługiwana?

Nie. Usługa Azure Import/Export nie obsługuje obecnie Azure NetApp Files.

## <a name="next-steps"></a>Następne kroki  

- [Microsoft Azure ExpressRoute często zadawane pytania](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure Virtual Network często zadawane pytania](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Jak utworzyć żądanie pomocy technicznej dla platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Często zadawane pytania dotyczące wydajności protokołu SMB dla Azure NetApp Files](azure-netapp-files-smb-performance.md)
