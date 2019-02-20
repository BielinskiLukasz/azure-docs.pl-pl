---
title: Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services z usługą Azure Backup
description: W tym artykule opisano sposób tworzenia kopii zapasowej maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services z usługą Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 0f522897f3d3b3261045f1c14387af53ebf4ad9d
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429591"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure w magazynie usługi Recovery Services

W tym artykule opisano sposób tworzenia kopii zapasowej dla maszyny Wirtualnej platformy Azure przy użyciu [kopia zapasowa Azure](backup-overview.md) przez wdrożenie i opcja włączania kopii zapasowych w magazynie usługi Recovery Services. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Sprawdź obsługiwane scenariusze i wymagania wstępne.
> * Przygotowywanie maszyn wirtualnych platformy Azure. Zainstaluj agenta maszyny Wirtualnej platformy Azure, jeśli to konieczne i Sprawdź dostęp ruchu wychodzącego dla maszyn wirtualnych.
> * Utwórz magazyn.
> * Konfigurowanie magazynu dla magazynu
> * Odnajdywanie maszyn wirtualnych, skonfiguruj ustawienia kopii zapasowych i zasady.
> * Włącz wykonywanie kopii zapasowej maszyn wirtualnych platformy Azure


> [!NOTE]
   > W tym artykule opisano sposób tworzenia kopii zapasowej maszyn wirtualnych platformy Azure, konfigurowania magazynu i wybierając pozycję maszyny wirtualne, aby utworzyć kopię zapasową. Jest to przydatne, jeśli chcesz utworzyć kopię zapasową wielu maszyn wirtualnych. Możesz również [kopii zapasowej maszyny Wirtualnej platformy Azure](backup-azure-vms-first-look-arm.md) bezpośrednio z poziomu ustawień maszyny Wirtualnej.

## <a name="before-you-start"></a>Przed rozpoczęciem

Usługa Azure Backup tworzy kopie zapasowe maszyn wirtualnych platformy Azure, instalowanie rozszerzenia na maszynie agenta maszyny Wirtualnej platformy Azure.

1. [Przegląd](backup-architecture.md#architecture-direct-backup-of-azure-vms) architektura kopii zapasowych maszyn wirtualnych platformy Azure.
[Dowiedz się więcej o](backup-azure-vms-introduction.md) kopii zapasowych maszyn wirtualnych platformy Azure, a rozszerzenie kopii zapasowej.
2. [Przejrzyj macierz obsługi](backup-support-matrix-iaas.md) do utworzenia kopii zapasowej maszyny Wirtualnej platformy Azure.
3. Przygotowywanie maszyn wirtualnych platformy Azure. Zainstaluj agenta maszyny Wirtualnej, jeśli nie jest on zainstalowany i Sprawdź dostęp ruchu wychodzącego dla maszyn wirtualnych, aby utworzyć kopię zapasową.


## <a name="prepare-azure-vms"></a>Przygotowywanie maszyn wirtualnych platformy Azure

Zainstaluj agenta maszyny Wirtualnej, jeśli to konieczne i Sprawdź dostęp ruchu wychodzącego z maszyn wirtualnych.

### <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny Wirtualnej 
Jeśli to konieczne, zainstaluj agenta w następujący sposób.

**VM** | **Szczegóły**
--- | ---
**Maszyny wirtualne z systemem Windows** | [Pobierz i zainstaluj](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) pliku MSI agenta. Zainstaluj z uprawnieniami administratora na komputerze.<br/><br/> Aby zweryfikować instalację, w *C:\WindowsAzure\Packages* na maszynie Wirtualnej, kliknij prawym przyciskiem myszy WaAppAgent.exe > **właściwości**, > **szczegóły** kartę. **Wersja produktu** powinien znajdować się wartość 2.6.1198.718 lub wyższa.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, żadne operacje tworzenia kopii zapasowej są uruchomione, i [ponownie zainstalować agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Maszyny wirtualne z systemem Linux** | Instalację przy użyciu RPM lub DEB pakietu z repozytorium pakietów w Twojej dystrybucji jest preferowaną metodą instalacji i uaktualniania agenta systemu Linux dla platformy Azure. Wszystkie [zatwierdzonego dla dostawców dystrybucji](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Zintegruj pakiet Azure Linux agent repozytoriów i obrazów. Agent jest dostępna w [GitHub](https://github.com/Azure/WALinuxAgent), ale nie jest zalecane instalowanie z tego miejsca.<br/><br/> Jeśli aktualizujesz agenta, upewnij się, żadna operacja tworzenia kopii zapasowej jest uruchomiony i aktualizowanie plików binarnych. 


### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Zapasowy numer wewnętrzny, uruchomione na maszynie Wirtualnej musi mieć dostęp ruchu wychodzącego do platformy Azure z publicznymi adresami IP.

- Brak dostępu do jawnego sieciowego ruchu wychodzącego jest wymagane dla maszyny Wirtualnej platformy Azure do komunikowania się z usługą Azure Backup.
- Jednak niektóre starsze maszyny wirtualne mogą występować problemy i zakończyć się niepowodzeniem z powodu błędu **ExtensionSnapshotFailedNoNetwork** podczas próby połączenia. W tym przypadku użyj jednej z następujących opcji, aby zapasowy numer wewnętrzny może komunikować się do platformy Azure publicznych adresów IP dla ruchu kopii zapasowej.

   **Opcja** | **Akcja** ** | **Zalety** | **Wady**
   --- | --- | --- | ---
   **Skonfiguruj reguły sieciowej grupy zabezpieczeń** | Zezwalaj na [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/>  Można dodać regułę, która zezwala na dostęp do usługi Azure Backup przy użyciu [tag usługi](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure), a nie indywidualnie umożliwiając i zarządzanie nimi każdego zakresu adresów. [Dowiedz się więcej](../virtual-network/security-overview.md#service-tags) o tagi usługi. | Brak dodatkowych kosztów. Łatwo zarządzać za pomocą tagów usługi
   **Wdrażanie serwera proxy** | Wdrażanie serwera proxy HTTP dla routingu ruchu. | Zapewnia dostęp do całej platformy Azure i nie tylko magazyn. Ścisła kontrola nad adresy URL magazynu jest dozwolone.<br/><br/> Pojedynczy punkt internet access dla maszyn wirtualnych.<br/><br/> Dodatkowe koszty dla serwera proxy.<br/><br/> 
   **Konfigurowanie zapory platformy Azure** | Zezwalaj na ruch przez zaporę platformy Azure na maszynie Wirtualnej za pomocą tagu w pełni kwalifikowaną nazwę domeny dla usługi Azure Backup.|  Łatwa w użyciu, jeśli masz zapory usługi Azure w podsieci sieci wirtualnej | Nie można utworzyć własne tagi nazwy FQDN lub zmodyfikować nazwy FQDN w tagu.<br/><br/> Jeśli używasz usługi Azure Managed Disks, może być konieczne otwarcie dodatkowych portów (na porcie 8443) na zaporach.

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Skonfiguruj regułę sieciowej grupy zabezpieczeń, aby zezwolić na dostęp ruchu wychodzącego do usługi Azure

Jeśli maszyna wirtualna platformy Azure ma dostęp do zarządzanych przez sieciową grupę zabezpieczeń, należy zezwolić na dostęp ruchu wychodzącego w magazynie kopii zapasowej do wymaganych zakresów adresów i portów.

1. Na maszynie wirtualnej > **sieć**, kliknij przycisk **Dodaj regułę portu wyjściowego**.
2. W **Dodawanie reguły zabezpieczeń dla ruchu wychodzącego**, kliknij przycisk **zaawansowane**.
3. W **źródła**, wybierz opcję **VirtualNetwork**.
4. W **zakresów portów źródłowych**, wpisz znak gwiazdki (*), aby zezwolić na dostęp ruchu wychodzącego z dowolnego portu.
5. W **docelowy**, wybierz opcję **Tag usługi**. Wybierz z listy, **Storage.region**. Region to region, w którym magazyn i maszyny wirtualne, aby utworzyć kopię zapasową, znajdują się.
6. W **docelowe zakresy portów**, wybierz numer portu.
    - Niezarządzane maszyny Wirtualnej przy użyciu niezaszyfrowanego konta magazynu: 80
    - Niezarządzanej maszyny Wirtualnej za pomocą zaszyfrowanego konta magazynu: 443 (ustawienie domyślne)
    - Zarządzanych maszyn wirtualnych: 8443.
7. W **protokołu**, wybierz opcję **TCP**.
8. W **priorytet**, określ wartość priorytetu z mniej niż wyżej reguł odmowy. W przypadku regułę odmawiającą dostępu nową Zezwalaj na reguła musi być wyższa. Na przykład, jeśli masz **Deny_All** reguły priorytetem 1000, nową regułę, musi być ustawione na mniej niż 1000.
9. Podaj nazwę i opis reguły, a następnie kliknij przycisk **OK**.

Reguły sieciowej grupy zabezpieczeń można zastosować do wielu maszyn wirtualnych, aby zezwolić na dostęp ruchu wychodzącego.

W tym wideo przeprowadzi Cię przez proces.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>Kierowanie ruchu kopii zapasowych za pośrednictwem serwera proxy

Można kierować ruchem kopii zapasowych za pośrednictwem serwera proxy, a następnie udzielić dostępu do serwera proxy do wymaganych zakresów platformy Azure.
Należy skonfigurować serwer proxy maszyny Wirtualnej, aby umożliwić następujące czynności:

- Maszyna wirtualna platformy Azure należy kierować cały ruch HTTP do publicznej sieci internet za pośrednictwem serwera proxy.
- Serwer proxy powinien zezwalać na ruch przychodzący z maszyn wirtualnych w odpowiednich sieci wirtualnych (VNet).
- Sieciowa grupa zabezpieczeń **blokady NF** musi regułę zezwalającą na ruch wychodzący z Internetu z maszyną Wirtualną serwera proxy.

##### <a name="set-up-the-proxy"></a>Konfigurowanie serwera proxy
Jeśli nie masz konta serwera proxy systemu skonfigurowane w następujący sposób:

1. Pobierz [PsExec](https://technet.microsoft.com/sysinternals/bb897553).

2. Uruchom **PsExec.exe -i -s cmd.exe** do uruchamiania wiersza polecenia przy użyciu konta system.
3. Uruchom przeglądarkę w kontekście systemowym. Na przykład: **ProgramFiles%\Internet Explorer\iexplore.exe** programu Internet Explorer.  
4. Zdefiniuj ustawienia serwera proxy.
    - Na maszynach z systemem Linux:
        - Dodaj następujący wiersz do **/etc/środowisko** pliku:
            - **że =http://proxy IP adres: port serwera proxy**
        - Dodaj następujące wiersze do **/etc/waagent.conf** pliku:
            - **Adres IP HttpProxy.Host=proxy**
            - **HttpProxy.Port=proxy port**
    - Na komputerach Windows, w ustawieniach przeglądarki należy określić, że powinien być używany serwer proxy. Jeśli obecnie używasz serwera proxy na koncie użytkownika, umożliwia ten skrypt należy zastosować na poziomie konta system.
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

##### <a name="allow-incoming-connections-on-the-proxy"></a>Zezwalaj na połączenia przychodzące na serwerze proxy

Zezwalaj na połączenia przychodzące w ustawień serwera proxy.

- Na przykład otworzyć **Zapora Windows z zabezpieczeniami zaawansowanymi**.
    - Kliknij prawym przyciskiem myszy **reguły dla ruchu przychodzącego** > **nową regułę**.
    - W **typ reguły** wybierz **niestandardowe** > **dalej**.
    - W **Program**, wybierz opcję **wszystkie programy** > **dalej**.
    - W **protokoły i porty** Ustaw typ **TCP**, **portów lokalnych** do **określonych portów**, i **port zdalny**do **wszystkie porty**.
    - Zakończ pracę kreatora, a następnie określ nazwę dla tej reguły.

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Dodaj regułę wyjątku do sieciowej grupy zabezpieczeń dla serwera proxy

W sieciowej grupie zabezpieczeń **blokady NF**, zezwalają na ruch z dowolnego portu na 10.0.0.5 do dowolnego adresu internetowego na porcie 80 (HTTP) lub 443 (HTTPS).

- Poniższy skrypt programu PowerShell zawiera przykładowy zezwolenia na ruch.
- Zamiast zezwalać na ruch wychodzący do wszystkich publicznych adresów internetowych, można określić zakres adresów IP (-DestinationPortRange), lub użyj storage.region tag usługi.   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>Zezwalaj na dostęp przez zaporę za pomocą tagu w pełni kwalifikowaną nazwę domeny

Możesz skonfigurować zaporę usługi Azure, aby zezwolić na dostęp ruchu wychodzącego ruchu sieciowego do usługi Azure Backup.

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) wdrożenie zapory usługi Azure.
- [Przeczytaj o](https://docs.microsoft.com/azure/firewall/fqdn-tags) tagów w pełni kwalifikowaną nazwę domeny.

## <a name="create-a-vault"></a>Tworzenie magazynu

Magazyn przechowuje kopie zapasowe i punkty odzyskiwania utworzone wraz z upływem czasu i przechowuje zasady tworzenia kopii zapasowych, skojarzonych kopii zapasowych maszyn. Utwórz magazyn w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Na **Centrum** menu, wybierz opcję **Przeglądaj**i wpisz **usługi Recovery Services**. Wybierz **Magazyny usługi Recovery Services**.

    ![Wpisując w polu i wybierając polecenie "Magazynów usługi Recovery Services", w wynikach](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. Na **Magazyny usługi Recovery Services** menu, wybierz opcję **Dodaj**.

    ![Tworzenie magazynu Usług odzyskiwania — krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    ![Okienko "Magazyny usługi recovery Services"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. W **Magazyny usługi Recovery Services** >  **nazwa**, wprowadź przyjazną nazwę identyfikującą magazyn.
    - Nazwa musi być unikalna w tej subskrypcji platformy Azure.
    - Może on zawierać 2 do 50 znaków.
    - Musi zaczynać się literą i może zawierać tylko litery, cyfry i łączniki.
5. Wybierz **subskrypcji** Aby wyświetlić listę dostępnych subskrypcji. Jeśli nie masz pewności, której subskrypcji użyć, użyj wartości domyślnej (lub sugerowane) subskrypcji. Istnieje wiele opcji tylko wtedy, gdy pracy lub nauki jest skojarzony z wieloma subskrypcjami platformy Azure.
6. Wybierz **grupy zasobów** Aby wyświetlić listę dostępnych grup zasobów lub wybierz **New** do tworzenia nowej grupy zasobów. [Dowiedz się więcej](../azure-resource-manager/resource-group-overview.md) temat grup zasobów.
7. Wybierz **lokalizacji** do wybierz region geograficzny magazynu. Magazyn *musi* należeć do tego samego regionu maszyn wirtualnych, które chcesz utworzyć kopię zapasową.
8. Wybierz pozycję **Utwórz**.
    - Może upłynąć trochę czasu utworzenie magazynu.
    - Monitoruj powiadomienia o stanie wyświetlane w obszarze prawym górnym rogu portalu.
    ![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Po utworzeniu magazynu pojawi się na liście magazynów usługi Recovery Services. Jeśli nie widzisz swojego magazynu wybierz **Odśwież**.

## <a name="set-up-storage-replication"></a>Konfigurowanie replikacji magazynu

Domyślnie Magazyn jest [magazyn geograficznie nadmiarowy (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). Zalecamy użycie konta GRS dla Twoja podstawowa kopia zapasowa, ale można użyć[magazyn lokalnie nadmiarowy](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) tańszych opcji.

Zmodyfikuj replikacja magazynu w następujący sposób:

1. W magazynie > **infrastruktura zapasowa**, kliknij przycisk **konfiguracji kopii zapasowej**

   ![Lista magazynów kopii zapasowych](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. W **konfiguracji kopii zapasowej**, zmodyfikuj metodę nadmiarowości magazynu jako wymagane i wybierz **Zapisz**.


## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad tworzenia kopii zapasowej

Odnajdywanie maszyn wirtualnych w ramach subskrypcji i skonfiguruj kopię zapasową.

1. W magazynie > **Przegląd**, kliknij przycisk **+ kopia zapasowa**

   ![Przycisk tworzenia kopii zapasowej](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **Kopii zapasowej** i **cel kopii zapasowej** otwieranie okienka.

2. W **cel kopii zapasowej**> **gdzie jest uruchomione Twoje obciążenie?**, wybierz opcję **Azure**. W **jakich elementów chcesz utworzyć kopię zapasową?**, wybierz opcję **maszyny wirtualnej** >  **OK**. Rejestruje to rozszerzenie maszyny Wirtualnej w magazynie.

   ![Kopia zapasowa i cel kopii zapasowej okienka](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   W tym kroku rejestruje rozszerzenie maszyny Wirtualnej w magazynie. **Cel kopii zapasowej** okienko zostanie zamknięte, a **zasady tworzenia kopii zapasowej** zostanie otwarte okienko.

3. W **zasady tworzenia kopii zapasowej**, wybierz zasady, które chcesz skojarzyć z magazynem. Następnie kliknij przycisk **OK**.
    - Szczegóły domyślnych zasad znajdują się w menu rozwijanym.
    - Kliknij przycisk **Utwórz nowy** tworzenia zasad. [Dowiedz się więcej](backup-azure-arm-vms-prepare.md#configure-a-backup-policy) o definiowaniu zasad.
    

    !["Kopia zapasowa" i "Zasady kopii zapasowych" okienek](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. W **wybierz maszyny wirtualne** okienku wybierz maszyny wirtualne, korzystających z określonych zasadach kopii zapasowych > **OK**.

    - Wybranej maszyny Wirtualnej jest weryfikowana.
    - Można wybrać tylko maszyny wirtualne w tym samym regionie co magazyn. Maszyny wirtualne mogą być tylko kopii zapasowej w jednym magazynie.

   ![Okienko "Wybierz maszyny wirtualne"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. W **kopii zapasowej**, wybierz opcję **Włącz kopię zapasową**.

   - Wdraża zasady dla magazynu i maszyn wirtualnych i instaluje rozszerzenie kopii zapasowej na agencie maszyn wirtualnych uruchomionych na maszynie Wirtualnej platformy Azure.
   - Ten krok nie utworzyć początkowy punkt odzyskiwania dla maszyny Wirtualnej.

   ![Przycisk "Włącz kopię zapasową"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po włączeniu kopii zapasowej:

- Tworzenie początkowej kopii zapasowej kopii zapasowej jest uruchamiane zgodnie z harmonogramem kopii zapasowej.
- Usługa Backup instaluje rozszerzenie kopii zapasowej, czy maszyna wirtualna jest uruchomiona.
    - Uruchomiona maszyna wirtualna zapewnia największe prawdopodobieństwo uzyskania punktu odzyskiwania spójnego z aplikacją.
    -  Jednak maszyna wirtualna jest kopii zapasowej, nawet jeśli jest ona wyłączona i nie można zainstalować rozszerzenia. Jest to nazywane *maszyny Wirtualnej w trybie offline*. W takim przypadku punkt odzyskiwania będzie *awaryjnie spójny*.
    Należy pamiętać, że usługi Azure Backup nie obsługuje zegara automatyczne dostosowanie zmian letniego dla kopii zapasowych maszyn wirtualnych platformy Azure. Modyfikowanie zasad tworzenia kopii zapasowej ręcznego zgodnie z potrzebami.
  
 ## <a name="run-the-initial-backup"></a>Uruchom tworzenie początkowej kopii zapasowej

Początkowa kopia zapasowa zostanie uruchomione zgodnie z harmonogramem, chyba że zostanie ręcznie uruchomiony natychmiast. Uruchom ją ręcznie w następujący sposób:

1. W menu magazynu kliknij **kopii zapasowych elementów**.
2. W **elementy kopii zapasowej** kliknij **maszyny wirtualnej platformy Azure**.
3. W **elementy kopii zapasowej** listy, kliknij przycisk z wielokropkiem **...** .
4. Kliknij przycisk **Utwórz teraz kopię zapasową**.
5. W **Utwórz teraz kopię zapasową**, wybierz ostatni dzień, który ma być przechowywana punkt odzyskiwania przy użyciu kontrolki kalendarza > **OK**.
6. Monitoruj powiadomienia z portalu. Możesz monitorować postęp zadania na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > **w toku**. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.



## <a name="next-steps"></a>Kolejne kroki

- Rozwiązanie wszelkich problemów, które występują w [agentów maszyny Wirtualnej platformy Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) lub [kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-troubleshoot.md).
- [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-first-look-arm.md)
