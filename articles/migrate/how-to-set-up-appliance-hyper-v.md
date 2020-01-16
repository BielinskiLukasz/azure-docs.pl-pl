---
title: Konfigurowanie urządzenia Azure Migrate dla funkcji Hyper-V
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate do oceniania i migrowania maszyn wirtualnych funkcji Hyper-V.
ms.topic: article
ms.date: 11/19/2019
ms.openlocfilehash: 0704adda314b94736b01fe114c3643ef8bd83753
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029092"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurowanie urządzenia dla maszyn wirtualnych funkcji Hyper-V

W tym artykule opisano sposób konfigurowania urządzenia Azure Migrate, jeśli oceniasz maszyny wirtualne funkcji Hyper-V za pomocą narzędzia do oceny Azure Migrate Server, lub migrujesz maszyny wirtualne VMware do platformy Azure za pomocą narzędzia migracji Azure Migrate Server.

Urządzenie maszyny wirtualnej funkcji Hyper-V jest lekkim urządzeniem używanym przez Azure Migrate oceny/migracji serwera, aby wykonać następujące czynności:

- Odnajdywanie lokalnych maszyn wirtualnych funkcji Hyper-V.
- Wysyłanie metadanych i danych wydajności dla odnalezionych maszyn wirtualnych w celu Azure Migrate oceny/migracji serwera.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu Azure Migrate.


## <a name="appliance-deployment-steps"></a>Kroki wdrażania urządzenia

Aby skonfigurować urządzenie:
- Pobierz skompresowany wirtualny dysk twardy funkcji Hyper-V z Azure Portal.
- Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
- Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

## <a name="download-the-vhd"></a>Pobierz dysk VHD

Pobierz szablon skompresowanego dysku VHD dla urządzenia.

1. W obszarze **cele migracji** > **serwery** > **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie maszyn** > **są zwirtualizowane maszyny?** kliknij przycisk **tak, z funkcją Hyper-V**.
3. Kliknij pozycję **Pobierz** , aby pobrać plik VHD.

    ![Pobierz maszynę wirtualną](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla wirtualnego dysku twardego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  W przypadku 2.19.11.12 w wersji urządzenia wygenerowany skrót powinien być zgodny z tymi ustawieniami.

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. Wyodrębnij plik skompresowanego dysku VHD do folderu na hoście funkcji Hyper-V, który będzie obsługiwał maszynę wirtualną urządzenia. Trzy foldery są wyodrębniane.
2. Otwórz Menedżera funkcji Hyper-V. W obszarze **Akcje**kliknij pozycję **Importuj maszynę wirtualną**.

    ![Wdrażanie wirtualnego dysku twardego](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Po **rozpoczęciu pracy**Kreatora importu maszyny wirtualnej > kliknij przycisk **dalej**.
3. W obszarze **lokalizowanie folderu**określ folder zawierający wyodrębniony wirtualny dysk twardy. Następnie kliknij przycisk **Next** (Dalej).
1. W obszarze **Wybierz maszynę wirtualną**kliknij przycisk **dalej**.
2. W obszarze **Wybierz typ importu**kliknij pozycję **Kopiuj maszynę wirtualną (Utwórz nowy unikatowy identyfikator)** . Następnie kliknij przycisk **Next** (Dalej).
3. W obszarze **Wybierz lokalizację docelową**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W obszarze **foldery magazynu**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W obszarze **Wybierz sieć**Określ przełącznik wirtualny, który będzie używany przez maszynę wirtualną. Przełącznik wymaga połączenia z Internetem, aby wysyłać dane do platformy Azure.
6. W obszarze **Podsumowanie**przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **Virtual Machines**Uruchom maszynę wirtualną.


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z [adresami URL platformy Azure](migrate-appliance.md#url-access).

## <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. W Menedżerze funkcji Hyper-V > **Virtual Machines**kliknij prawym przyciskiem myszy maszynę wirtualną > **Połącz**.
2. Podaj język, strefę czasową i hasło dla urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną wirtualną, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację na pulpicie urządzenia, klikając skrót do aplikacji.
1. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
    - **Łączność**: aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby funkcja odnajdywania maszyn wirtualnych działała prawidłowo.
    - **Instalowanie aktualizacji**: ocena serwera Azure Migrate sprawdza, czy na urządzeniu zainstalowano najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
2. Na nowej karcie Zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web.
4. Wybierz subskrypcję, w której został utworzony projekt Azure Migrate. Następnie wybierz projekt.
5. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
6. Kliknij pozycję **zarejestruj**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla wirtualnych dysków twardych SMB

Jeśli używasz dysków VHD w systemie technologii, musisz włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. W tym celu z urządzenia:

1. Na maszynie wirtualnej urządzenia Uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowymi nazwami hostów.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Alternatywnie należy to zrobić w Edytor lokalnych zasad grupy na urządzeniu:
    - W **zasadach komputera lokalnego** > **Konfiguracja komputera**, kliknij pozycję **Szablony administracyjne** > **system** > **Delegowanie poświadczeń**.
    - Kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń**i wybierz pozycję **włączone**.
    - W obszarze **Opcje**kliknij pozycję **Pokaż**, a następnie na liście Dodaj każdego hosta funkcji Hyper-V, który ma zostać odnajdowany, przy użyciu **usługi WSMAN/** jako prefiksu.
    - W obszarze **Delegowanie poświadczeń**kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń z uwierzytelnianiem serwera tylko NTLM**. Ponownie Dodaj każdy host funkcji Hyper-V, który ma zostać odnalezienie do listy, przy użyciu **usługi WSMAN/** jako prefiksu.

## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Połącz się z urządzeniem z hostami lub klastrami funkcji Hyper-V, a następnie Uruchom odnajdywanie maszyn wirtualnych.

1. W polu **Nazwa użytkownika** i **hasło**określ poświadczenia konta, które będą używane przez urządzenie do odnajdywania maszyn wirtualnych. Określ przyjazną nazwę dla poświadczeń, a następnie kliknij pozycję **Zapisz szczegóły**.
2. Kliknij pozycję **Dodaj hosta**i określ szczegóły hosta/klastra funkcji Hyper-V.
3. Kliknij przycisk **Weryfikuj**. Po walidacji wyświetlana jest liczba maszyn wirtualnych, które mogą zostać odnalezione na każdym hoście/klastrze.
    - Jeśli walidacja nie powiedzie się dla hosta, przejrzyj błąd, umieszczając kursor nad ikoną w kolumnie **stan** . Usuń problemy i ponownie sprawdź poprawność.
    - Aby usunąć hosty lub klastry, wybierz pozycję > **Usuń**.
    - Nie można usunąć określonego hosta z klastra. Można usunąć tylko cały klaster.
    - Można dodać klaster, nawet jeśli występują problemy z konkretnymi hostami w klastrze.
4. Po sprawdzeniu poprawności kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć proces odnajdywania.

Spowoduje to uruchomienie odnajdywania. Metadane wykrytych maszyn wirtualnych mogą pojawić się w Azure Portal około 15 minut.

## <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu odnajdywania możesz sprawdzić, czy maszyny wirtualne są widoczne w portalu.

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery** > **Azure Migrate: Strona Ocena serwera** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.


## <a name="next-steps"></a>Następne kroki

Wypróbuj [ocenę funkcji Hyper-V](tutorial-assess-hyper-v.md) dzięki ocenie serwera Azure Migrate.
