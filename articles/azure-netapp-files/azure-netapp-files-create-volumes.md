---
title: Tworzenie woluminu NFS dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób tworzenia woluminu NFS dla Azure NetApp Files.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274088"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Tworzenie woluminu NFS dla usługi Azure NetApp Files

Azure NetApp Files obsługuje woluminy NFS (NFSv3 i NFSv 4.1) i SMBv3. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. W tym artykule pokazano, jak utworzyć wolumin systemu plików NFS. Jeśli chcesz utworzyć wolumin SMB, zobacz [Tworzenie woluminu SMB dla Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz skonfigurowanej puli pojemności.   
[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)   
Podsieć musi być delegowana do usługi Azure NetApp Files.  
[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Zagadnienia do rozważenia 

* Wybór wersji systemu plików NFS do użycia  
  NFSv3 może obsługiwać wiele różnych przypadków użycia i jest często wdrażany w większości aplikacji dla przedsiębiorstw. Należy sprawdzić poprawność wersji (NFSv3 lub NFSv 4.1) wymaganej przez aplikację i utworzyć wolumin przy użyciu odpowiedniej wersji. Jeśli na przykład używasz platformy [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave), zalecane jest blokowanie plików z nfsv 4.1 w NFSv3. 

* Zabezpieczenia  
  Obsługa bitów trybu systemu UNIX (odczyt, zapis i wykonywanie) jest dostępna dla NFSv3 i NFSv 4.1. Aby można było zainstalować woluminy NFS, na kliencie NFS jest wymagany dostęp na poziomie głównym.

* Lokalna użytkownicy/Grupa i obsługa protokołu LDAP dla NFSv 4.1  
  Obecnie NFSv 4.1 obsługuje tylko dostęp do woluminów głównych. Zobacz [Konfigurowanie domyślnej domeny nfsv 4.1 dla Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Najlepsze rozwiązania

* Należy upewnić się, że używasz odpowiednich instrukcji instalacji dla woluminu.  Zobacz [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Klient systemu plików NFS powinien znajdować się w tej samej sieci wirtualnej lub komunikacji równorzędnej co Azure NetApp Files woluminie. Połączenie spoza sieci wirtualnej jest obsługiwane; jednak spowoduje to wprowadzenie dodatkowych opóźnień i zmniejszenie ogólnej wydajności.

* Należy upewnić się, że klient NFS jest aktualny i uruchomiono najnowsze aktualizacje dla systemu operacyjnego.

## <a name="create-an-nfs-volume"></a>Tworzenie woluminu NFS

1.  Kliknij blok **woluminy** w bloku pule pojemności. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Zostanie wyświetlone okno Tworzenie woluminu.

3.  W oknie Tworzenie woluminu kliknij pozycję **Utwórz** i podaj informacje dla następujących pól:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w ramach każdej puli pojemności. Musi zawierać co najmniej trzy znaki. Można użyć dowolnych znaków alfanumerycznych.   

        Nie można użyć `default` jako nazwy woluminu.

    * **Pula pojemności**  
        Określ pulę pojemności, w której ma zostać utworzony wolumin.

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Możesz również uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kliknij pozycję **Protokół**, a następnie wykonaj następujące czynności:  
    * Wybierz system **plików NFS** jako typ protokołu dla woluminu.   
    * Określ **ścieżkę pliku** , która zostanie użyta do utworzenia ścieżki eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.

        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków. 

        Ścieżka pliku musi być unikatowa w ramach każdej subskrypcji i każdego regionu. 

    * Wybierz wersję systemu plików NFS (**NFSv3** lub **nfsv 4.1**) dla woluminu.  
    * Opcjonalnie [Skonfiguruj zasady eksportowania dla woluminu systemu plików NFS](azure-netapp-files-configure-export-policy.md).

    ![Określanie protokołu NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły woluminu.  Następnie kliknij przycisk **Utwórz** , aby utworzyć wolumin systemu plików NFS.

    Utworzony wolumin zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.


## <a name="next-steps"></a>Następne kroki  

* [Skonfiguruj domyślną domenę NFSv 4.1 dla Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurowanie zasad eksportu dla woluminu NFS](azure-netapp-files-configure-export-policy.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
