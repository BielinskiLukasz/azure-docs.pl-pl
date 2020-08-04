---
title: Tworzenie woluminu Dual-Protocol (NFSv3 i SMB) dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób tworzenia woluminu korzystającego z dwóch protokołów NFSv3 i SMB z obsługą mapowania użytkowników LDAP.
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
ms.topic: how-to
ms.date: 07/28/2020
ms.author: b-juche
ms.openlocfilehash: 61e8c56e75e82bc28ddb2abf231d9a5e919691b0
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535489"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Tworzenie woluminu Dual-Protocol (NFSv3 i SMB) dla Azure NetApp Files

Azure NetApp Files obsługuje tworzenie woluminów przy użyciu systemu plików NFS (NFSv3 i NFSv 4.1), SMBv3 lub Dual Protocol. W tym artykule opisano sposób tworzenia woluminu korzystającego z dwóch protokołów NFSv3 i SMB z obsługą mapowania użytkowników LDAP.  


## <a name="before-you-begin"></a>Przed rozpoczęciem 

* Potrzebujesz skonfigurowanej puli pojemności.  
    Zobacz [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md).   
* Podsieć musi być delegowana do usługi Azure NetApp Files.  
    Zobacz [delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

* Upewnij się, że spełniasz [wymagania dotyczące Active Directory połączeń](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Upewnij się, że klient NFS jest aktualny i uruchomiono najnowsze aktualizacje dla systemu operacyjnego.

## <a name="create-a-dual-protocol-volume"></a>Tworzenie dwuprotokołowego woluminu

1.  Kliknij blok **woluminy** w bloku pule pojemności. Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  W oknie Tworzenie woluminu kliknij pozycję **Utwórz**, a następnie podaj informacje dotyczące następujących pól na karcie podstawowe:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w ramach każdej puli pojemności. Musi zawierać co najmniej trzy znaki. Można użyć dowolnych znaków alfanumerycznych.   

        Nie można użyć `default` jako nazwy woluminu.

    * **Pula pojemności**  
        Określ pulę pojemności, w której ma zostać utworzony wolumin.

    * **limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Możesz również uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Jeśli chcesz zastosować istniejące zasady migawek do woluminu, kliknij pozycję **Pokaż sekcję zaawansowaną** , aby ją rozwinąć, a następnie wybierz pozycję Zasady migawek w menu rozwijanym. 

        Aby uzyskać informacje na temat tworzenia zasad migawek, zobacz [Zarządzanie zasadami migawek](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Pokaż zaznaczenie zaawansowane](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Kliknij pozycję **Protokół**, a następnie wykonaj następujące czynności:  
    * Wybierz pozycję **Dual-Protocol (NFSv3 i SMB)** jako typ protokołu dla woluminu.   

    * Z listy rozwijanej wybierz połączenie **Active Directory** .  
    Używany Active Directory musi mieć certyfikat głównego urzędu certyfikacji serwera. 

    * Określ **ścieżkę woluminu** dla woluminu.   
    Ta ścieżka woluminu jest nazwą udostępnionego woluminu. Nazwa musi zaczynać się od litery i musi być unikatowa w ramach każdej subskrypcji i każdego regionu.  

    * Określ **styl zabezpieczeń** , który ma być używany: NTFS (domyślnie) lub UNIX.

    * Opcjonalnie [Skonfiguruj zasady eksportowania dla tego woluminu](azure-netapp-files-configure-export-policy.md).

    ![Określanie podwójnego protokołu](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły woluminu. Następnie kliknij przycisk **Utwórz** , aby utworzyć wolumin.

    Utworzony wolumin zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Przekaż Active Directory publiczny certyfikat główny urzędu certyfikacji  

1.  Aby zainstalować i skonfigurować urząd certyfikacji, należy postępować zgodnie z instrukcjami [instalacji](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) . 

2.  Postępuj zgodnie [z wyświetlaniem certyfikatów z przystawką programu MMC](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) , aby użyć przystawki programu MMC i narzędzia Menedżer certyfikatów.  
    Użyj przystawki Menedżer certyfikatów, aby zlokalizować certyfikat główny lub wystawiający dla urządzenia lokalnego. Polecenia przystawki Zarządzanie certyfikatami należy uruchamiać z jednego z następujących ustawień:  
    * Klient oparty na systemie Windows, który przyłączył się do domeny i ma zainstalowany certyfikat główny 
    * Inny komputer w domenie zawierający certyfikat główny  

3. Wyeksportuj certyfikat główny.  
    Upewnij się, że certyfikat został wyeksportowany w formacie X. 509 z kodowaniem Base-64 (. CER): 

    ![Kreator eksportu certyfikatów](../media/azure-netapp-files/certificate-export-wizard.png)

4. Przejdź do konta NetApp dla dwuprotokołowego woluminu, kliknij przycisk **Active Directory połączenia**i przekaż certyfikat głównego urzędu certyfikacji przy użyciu okna **Active Directory sprzężenia** :  

    ![Certyfikat głównego urzędu certyfikacji serwera](../media/azure-netapp-files/server-root-ca-certificate.png)

    Upewnij się, że nazwa urzędu certyfikacji może zostać rozpoznana przez system DNS. Ta nazwa to pole "wystawione przez" lub "wystawca" certyfikatu:  

    ![Informacje o certyfikacie](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>Zarządzanie atrybutami LDAP POSIX

Można zarządzać atrybutami POSIX, takimi jak UID, katalogiem macierzystym i innymi wartościami, za pomocą przystawki MMC Użytkownicy i komputery Active Directory.  Poniższy przykład pokazuje Edytor atrybutów Active Directory:  

![Edytor atrybutów Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 


## <a name="configure-the-nfs-client"></a>Konfigurowanie klienta NFS 

Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie klienta NFS dla Azure NetApp Files](configure-nfs-clients.md) , aby skonfigurować klienta systemu plików NFS.  

## <a name="next-steps"></a>Następne kroki  

* [Dwa często zadawane pytania dotyczące protokołu](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [Konfigurowanie klienta NFS dla Azure NetApp Files](configure-nfs-clients.md) 
