---
title: Aktualizacja Azure stosu 1802 | Dokumentacja firmy Microsoft
description: "Więcej informacji na temat nowości w aktualizacji 1802 stosu Azure zintegrowanych systemów, znane problemy i pobierania aktualizacji."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: b5e52e30b99e1d59c2f8bde17b31b0f1cb3d6d70
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="azure-stack-1802-update"></a>Azure aktualizacji 1802 stosu

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ulepszenia i poprawki w pakiecie aktualizacji 1802 znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]        
> Ten pakiet aktualizacji jest tylko dla stosu Azure zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji Azure stosu Development Kit.

## <a name="build-reference"></a>Tworzenie odwołania    
Numer kompilacji aktualizacji 1802 stosu Azure jest **20180302.1**.  


## <a name="before-you-begin"></a>Przed rozpoczęciem    
> [!IMPORTANT]    
> Nie należy próbować tworzyć maszyn wirtualnych podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](/azure-stack-updates#plan-for-updates).

> [!IMPORTANT]    
> Jeśli używasz [SQL](/azure-stack-sql-resource-provider-deploy) lub [MySQL](/azure-stack-mysql-resource-provider-deploy) dostawców zasobów, konieczne jest uruchomienie wersji 1712 przed rozpoczęciem instalacji wersji stosu Azure 1802. Jeśli zainstalowano wersję stosu Azure 1802 dostawców są wersji 1711 lub starszym, nie można zaktualizować dostawców zasobów do nowszej wersji.



### <a name="prerequisites"></a>Wymagania wstępne
- Zainstaluj stosu Azure [zaktualizować 1712](azure-stack-update-1712.md) przed zastosowaniem aktualizacji 1802 stosu Azure.    

- Zainstaluj **AzS poprawkę — 20180222.2 kompilacji 1.0.180312.1-** przed zastosowaniem aktualizacji 1802 stosu Azure. Ta poprawka aktualizacji usługi Windows Defender i jest dostępna podczas pobierania aktualizacji dla stosu Azure.

  Aby zainstalować poprawkę, wykonaj normalnych procedur [instalowania aktualizacji dla stosu Azure](azure-stack-apply-updates.md). Nazwa aktualizacji jest wyświetlana jako **poprawkę AzS — 1.0.180312.1**i zawiera następujące pliki: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Po przekazaniu tych plików, aby konto magazynu i kontener, uruchom instalację na kafelku aktualizacji w portalu administracyjnym. 
  
  W odróżnieniu od aktualizacje stosu Azure po zainstalowaniu tej aktualizacji nie powoduje zmiany wersji Azure stosu.  Aby potwierdzić, ta aktualizacja jest zainstalowana, wyświetlić listę **zainstalowane aktualizacje**.
 


### <a name="post-update-steps"></a>Czynności po aktualizacji
*Nie istnieją żadne czynności po aktualizacji dla aktualizacji 1802.*


### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki
Ta aktualizacja obejmuje następujące ulepszenia i poprawki dla stosu Azure.

- **Dodano obsługę następujących wersji interfejsu API usługi Magazyn Azure**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Aby uzyskać więcej informacji, zobacz [usługi Azure Storage stosu: różnice i zagadnienia dotyczące](/azure/azure-stack/user/azure-stack-acs-differences).

- **Obsługa większej [blokowych obiektów blob](azure-stack-acs-differences.md)**:
    - Maksymalny dopuszczalny rozmiar bloku jest zwiększona z 4 MB na 100 MB.
    - Rozmiar maksymalny obiektu blob jest zwiększona z 195 GB do 4,75 TB.  

- **Infrastruktura kopii zapasowej** teraz zostanie wyświetlona w kafelku dostawców zasobów i alerty dotyczące tworzenia kopii zapasowej są włączone. Aby uzyskać więcej informacji o usłudze kopii zapasowej infrastruktury, zobacz [kopii zapasowej i odzyskiwanie danych dla stosu Azure w usłudze Kopia zapasowa infrastruktury](azure-stack-backup-infrastructure-backup.md).

- **Aktualizacja do *AzureStack testu* polecenia cmdlet** zwiększające diagnostyki dla magazynu. Aby uzyskać więcej informacji na temat tego polecenia cmdlet, zobacz [weryfikacji stosu Azure](azure-stack-diagnostic-test.md).

- **Ulepszenia opartej na rolach kontroli dostępu (RBAC)** — można teraz używać RBAC Aby oddelegować uprawnienia do grup uniwersalnych użytkownika po wdrożeniu stosu Azure z usługami AD FS. Aby dowiedzieć się więcej na temat RBAC, zobacz [Zarządzanie RBAC](azure-stack-manage-permissions.md).

- **Dodano obsługę wielu domen błędów**.  Aby uzyskać więcej informacji, zobacz [wysoka dostępność dla stosu Azure](azure-stack-key-features.md#high-availability-for-azure-stack).

- **Różne poprawki** wydajności, trwałości, zabezpieczeń i systemu operacyjnego, który jest używany przez stos platformy Azure.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji    
*Nie są znane problemy dotyczące instalacji aktualizacji 1802.*


### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)
Poniżej przedstawiono znane problemy występujące po instalacji w kompilacji **20180302.1**

#### <a name="portal"></a>Portal
- Możliwość [aby otworzyć nowe żądanie pomocy technicznej z listy rozwijanej](azure-stack-manage-portals.md#quick-access-to-help-and-support) z wewnątrz administrator portalu nie jest dostępna. Zamiast tego użyj następującego łącza:     
    - Stosu Azure zintegrowanych systemów, użyj https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Przyczyną tego problemu jest błąd podczas instalacji aktualizacji, które powoduje, że aktualizacja niepoprawnie zgłaszana jako powiodło się. Jeśli ten problem występuje, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

- Może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz koło zębate ikonę w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.

- Po wyświetleniu właściwości zasób lub grupa zasobów **Przenieś** przycisk jest niedostępny. To zachowanie jest oczekiwane. Przenoszenie zasobów lub grup zasobów między grupami zasobów lub subskrypcji nie jest obecnie obsługiwane.

- Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.

- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Aby uniknąć tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- Na pulpicie nawigacyjnym portalu administracyjnego kafelka aktualizacji nie są wyświetlane informacje o aktualizacjach. Aby rozwiązać ten problem, kliknij Kafelek, aby go odświeżyć.

-   W portalu administracyjnym, można napotkać alert krytyczny dotyczący składnika Microsoft.Update.Admin. Nazwa alertu, opis i korygowania wszystkich wyświetlania jako:  
    - *Błąd — szablon FaultType ResourceProviderTimeout Brak.*

    Można bezpiecznie zignorować ten alert. 

#### <a name="health-and-monitoring"></a>Monitorowania kondycji i
Nie są znane problemy po zaktualizowaniu do 1802.

#### <a name="marketplace"></a>Portal Marketplace
- Użytkowników może uzyskać administracyjnej elementy, takie jak plany i oferty i przeglądać pełnego portalu marketplace bez subskrypcji. Te elementy są niefunkcjonalne dla użytkowników.

#### <a name="compute"></a>Wystąpienia obliczeniowe
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- Stos Azure obsługuje, używając tylko dysków VHD typu stałego. Niektóre obrazy oferowane za pośrednictwem portalu marketplace na stosie Azure Użyj dynamicznych wirtualnych dysków twardych, ale te zostały usunięte. Zmiana rozmiaru maszyny wirtualnej (VM) z dysku dynamicznego do niego dołączony pozostawia maszyny Wirtualnej w stanie niepowodzenia.

  Aby zminimalizować ten problem, należy usunąć maszynę Wirtualną bez usuwania dysku maszyny Wirtualnej, obiektu blob dysku VHD na koncie magazynu. Następnie Konwertuj wirtualny dysk twardy z dysk dynamiczny na dysk stały, a następnie ponownie utwórz maszynę wirtualną.

- Po utworzeniu zestawem dostępności w portalu, przechodząc do **nowy** > **obliczeniowe** > **zestawu dostępności**, można tworzyć tylko z domeny błędów i Aktualizacja domeny 1 zestawu dostępności. Jako obejście, podczas tworzenia nowej maszyny wirtualnej, Utwórz dostępności przy użyciu programu PowerShell, interfejsu wiersza polecenia, lub z poziomu portalu.

- Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć do serii DS maszyny Wirtualnej. Serii DS maszyny wirtualne mogą obsługiwać jako wiele dysków z danymi jako konfiguracji platformy Azure.

- Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

-  Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>Networking
- Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy.

- Wewnętrzny równoważenia obciążenia (ILB) nieprawidłowo obsługuje adresów MAC dla maszyn wirtualnych zaplecza, co powoduje, że ILB na przerwanie przy użyciu wystąpień systemu Linux w sieci wewnętrznej.  ILB współpracuje prawidłowo z wystąpień systemu Windows w sieci wewnętrznej.

-   Funkcja przesyłania dalej protokołu IP jest widoczny w portalu, jednak włączenie przesyłania dalej protokołu IP nie ma wpływu. Ta funkcja nie jest jeszcze obsługiwana.

- Stos Azure obsługuje jeden *bramy sieci lokalnej* według adresu IP. Dotyczy to we wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalnej bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej przy użyciu tego samego adresu IP są zablokowane.

- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się do niestandardowego błąd serwera DNS. Zaktualizowano ustawienia nie są przenoszone do maszyn wirtualnych w tej sieci wirtualnej.

- Stos Azure nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, muszą być zdefiniowane w czasie wdrażania.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Obejście usługi aplikacji: Jeśli potrzebujesz pulpitu zdalnego do wystąpień kontrolera, zmodyfikować zasady zabezpieczeń w ramach grup zabezpieczeń sieci przy użyciu programu PowerShell.  Poniżej przedstawiono przykłady *Zezwalaj*, a następnie Przywróć konfigurację, aby *odmowy*:  
    
    - *Zezwalaj na:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Odmów:*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL
 - Przed kontynuowaniem przejrzyj ważne uwagi w [przed rozpoczęciem](#before-you-begin) pobliżu początku te informacje o wersji.
- Może potrwać maksymalnie jedną godzinę, zanim użytkownicy mogą tworzyć baz danych w ramach nowego wdrożenia bazy danych SQL lub MySQL.

- Tylko dostawcy zasobów jest obsługiwane tworzenie elementów na serwerach, że host SQL lub MySQL. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może spowodować niezgodne stanu.  


> [!NOTE]  
> Po aktualizacji do 1802 stosu Azure, można użyć dostawcy zasobów SQL i MySQL, które wcześniej wdrożona.  Firma Microsoft zaleca się, że po udostępnieniu nowej wersji aktualizacji SQL i MySQL. Podobnie jak stosu Azure sekwencyjnie Zastosuj aktualizacje do dostawców zasobów SQL i MySQL.  Na przykład jeśli używasz wersji 1710, najpierw zastosować wersji 1711, a następnie 1712, a następnie zaktualizuj do 1802.      
>   
> Instalowanie aktualizacji 1802 nie wpływa na użycie bieżącej bazy danych SQL lub MySQL dostawców zasobów przez użytkowników.
> Niezależnie od wersji dostawców zasobów, których używasz danych użytkowników w swoich baz danych nie jest dotknąć i pozostanie dostępny.    


#### <a name="app-service"></a>App Service
- Użytkownicy muszą zarejestrować dostawcy zasobów magazynu przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- Aby skalować w poziomie infrastruktury (pracowników, zarządzania, role frontonu), musi być programu PowerShell, zgodnie z opisem w informacjach o wersji dla obliczania.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Pobieranie narzędzia Azure stosu z usługi GitHub
- Korzystając z *webrequest wywołania* polecenia cmdlet programu PowerShell, aby pobrać stosu Azure narzędzi z serwisu Github, komunikat o błędzie:     
    -  *Wywołanie webrequest: żądanie zostało przerwane: nie można utworzyć bezpiecznego kanału SSL/TLS.*     

  Ten błąd występuje z powodu ostatnie amortyzacja Obsługa GitHub standardów kryptograficznych Tlsv1 i Tlsv1.1 (wartość domyślna dla środowiska PowerShell). Aby uzyskać więcej informacji, zobacz [powiadomienia usuwania słabe standardów kryptograficznych](https://githubengineering.com/crypto-removal-notice/).

  Aby rozwiązać ten problem, Dodaj `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` na początku skryptu, aby wymusić konsoli programu PowerShell, aby użyć TLSv1.2 podczas pobierania z repozytoriami GitHub.


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji 1802 stosu Azure z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Więcej informacji
Firma Microsoft udostępnia sposób monitorowania i wznowić aktualizacji za pomocą uprzywilejowanych punktu końcowego (program ten) zainstalowanych z 1710 aktualizacji.

- Zobacz [monitorowania aktualizacji w stosie Azure, korzystając z dokumentacji uprzywilejowanych punktu końcowego](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Zobacz także

- Omówienie zarządzania aktualizacjami w stosie Azure, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md).
- Aby uzyskać więcej informacji dotyczących sposobu stosowania aktualizacji za pomocą stosu Azure, zobacz [stosowanie aktualizacji w stosie Azure](azure-stack-apply-updates.md).
