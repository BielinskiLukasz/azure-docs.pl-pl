---
title: Oceń maszyny wirtualne funkcji Hyper-V do migracji na platformę Azure za pomocą Azure Migrate | Microsoft Docs
description: Opisuje sposób oceny lokalnych maszyn wirtualnych funkcji Hyper-V na potrzeby migracji na platformę Azure przy użyciu Azure Migrate.
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: f36b0fbae01b25e604222c76d41ac21c0a7ae5a9
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029028"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Ocenianie maszyn wirtualnych funkcji Hyper-V za pomocą oceny serwera Azure Migrate

W tym artykule przedstawiono sposób oceny lokalnych maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia do oceny serwera Azure Migrate.

[Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm.



Ten samouczek jest drugą częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne funkcji Hyper-V na platformę Azure. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj projekt Azure Migrate.
> * Skonfiguruj i Zarejestruj urządzenie Azure Migrate.
> * Rozpocznij ciągłe wykrywanie lokalnych maszyn wirtualnych.
> * Grupuj odnalezione maszyny wirtualne i Oceń grupę.
> * Przejrzyj ocenę.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami z instrukcjami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

- [Wykonaj](tutorial-prepare-hyper-v.md) pierwszy samouczek z tej serii. Jeśli tego nie zrobisz, instrukcje podane w tym samouczku nie będą działały.
- Oto co należy zrobić w pierwszym samouczku:
    - [Skonfiguruj uprawnienia platformy Azure](tutorial-prepare-hyper-v.md#prepare-azure) dla Azure Migrate.
    - [Przygotuj klastry Hyper-V](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) , hosty i maszyny wirtualne w celu oceny.
    - [Przygotuj się do wdrożenia](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) urządzenia Azure Migrate, służącego do odnajdywania i oceny maszyn wirtualnych funkcji Hyper-V.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu Azure Migrate

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W wynikach wyszukiwania wybierz pozycję **Azure Migrate**.
3. W obszarze **Omówienie**, **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Oceń i zmigruj serwery**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. W obszarze **Wprowadzenie** kliknij pozycję **Dodaj narzędzia**.
5. Na karcie **Migrowanie projektu** wybierz subskrypcję platformy Azure i Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu**Określ nazwę projektu i region, w którym chcesz utworzyć projekt.


    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

    W tych regionach można utworzyć projekt Azure Migrate.

    **Lokalizacja geograficzna** | **Region**
    --- | ---
    Azja  | Azja Południowo-Wschodnia
    Europa | Europa Północna lub Europa Zachodnia
    Zjednoczone Królestwo |  Południowe Zjednoczone Królestwo lub Zachodnie Zjednoczone Królestwo
    Stany Zjednoczone | Wschodnie stany USA, zachodnie stany USA 2 lub zachodnie stany USA

    - Region projektu służy tylko do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.
    - Podczas migrowania maszyn wirtualnych możesz wybrać inny region docelowy platformy Azure. Wszystkie regiony platformy Azure są obsługiwane w celu migracji.

7. Kliknij przycisk **Dalej**.
8. W **narzędziu Wybierz ocenę**wybierz pozycję **Azure Migrate: Ocena serwera** > **dalej**.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W obszarze **Przegląd i dodawanie narzędzi** przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.




## <a name="set-up-the-appliance-vm"></a>Konfigurowanie maszyny wirtualnej urządzenia

Azure Migrate oceny serwera uruchamia lekkie urządzenie maszyny wirtualnej funkcji Hyper-V.

- To urządzenie służy do odnajdywania maszyn wirtualnych i wysyłania metadanych maszyn wirtualnych i danych wydajności do Azure Migrate: Ocena serwera.
- Aby skonfigurować urządzenie:
    - Pobierz skompresowany wirtualny dysk twardy funkcji Hyper-V z Azure Portal.
    - Utwórz urządzenie i sprawdź, czy może nawiązać połączenie z oceną serwera Azure Migrate.
    - Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.

### <a name="download-the-vhd"></a>Pobierz dysk VHD

Pobierz szablon skompresowanego dysku VHD dla urządzenia.

1. W obszarze **cele migracji** > **serwery** > **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie maszyn** > **są zwirtualizowane maszyny?** kliknij przycisk **tak, z funkcją Hyper-V**.
3. Kliknij pozycję **Pobierz** , aby pobrać plik VHD.

    ![Pobierz maszynę wirtualną](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.

2. Uruchom następujące polecenie programu PowerShell, aby wygenerować skrót dla pliku ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Przykład użycia: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  W przypadku 2.19.07.30 w wersji urządzenia wygenerowany skrót powinien być zgodny z tymi ustawieniami.

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i Utwórz maszynę wirtualną.

1. Po pobraniu skompresowanego pliku VHD na hosta funkcji Hyper-V, na którym zostanie umieszczona maszyna wirtualna urządzenia, Wyodrębnij plik zip.
    - W wyodrębnionej lokalizacji plik rozpakuje się do folderu o nazwie **AzureMigrateAppliance_VersionNumber**.
    - Ten folder zawiera podfolder, nazywany również **AzureMigrateAppliance_VersionNumber**.
    - Ten podfolder zawiera trzy dalsze **migawki**podfolderów, **wirtualne dyski twarde**i **Virtual Machines**.

2. Otwórz Menedżera funkcji Hyper-V. W obszarze **Akcje**kliknij pozycję **Importuj maszynę wirtualną**.

    ![Wdrażanie wirtualnego dysku twardego](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Po **rozpoczęciu pracy**Kreatora importu maszyny wirtualnej > kliknij przycisk **dalej**.
3. W obszarze **lokalizowanie folderu**wybierz folder **Virtual Machines** . Następnie kliknij przycisk **Next** (Dalej).
1. W obszarze **Wybierz maszynę wirtualną**kliknij przycisk **dalej**.
2. W obszarze **Wybierz typ importu**kliknij pozycję **Kopiuj maszynę wirtualną (Utwórz nowy unikatowy identyfikator)** . Następnie kliknij przycisk **Next** (Dalej).
3. W obszarze **Wybierz lokalizację docelową**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W obszarze **foldery magazynu**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W obszarze **Wybierz sieć**Określ przełącznik wirtualny, który będzie używany przez maszynę wirtualną. Przełącznik wymaga połączenia z Internetem, aby wysyłać dane do platformy Azure.
6. W obszarze **Podsumowanie**przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **Virtual Machines**Uruchom maszynę wirtualną.


### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może połączyć się z [adresami URL platformy Azure](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

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

Jeśli używasz dysków VHD w systemie technologii, musisz włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. Wymaga to:

- Każdemu hostowi można umożliwić działanie jako delegata urządzenia. Jeśli samouczki zostały wykonane w powyższej części samouczka, podczas przygotowywania funkcji Hyper-V do oceny i migracji należy wykonać te instrukcje. Należy skonfigurować protokół CredSSP dla hostów [ręcznie](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)lub przez [uruchomienie skryptu](tutorial-prepare-hyper-v.md#prepare-with-a-script) , który to robi.
- Włącz delegowanie CredSSP, aby urządzenie Azure Migrate było działać jako klient, Delegowanie poświadczeń do hosta.

Włącz na urządzeniu w następujący sposób:

#### <a name="option-1"></a>Opcja 1

Na maszynie wirtualnej urządzenia Uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowymi nazwami hostów.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Przykład: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opcja 2

Alternatywnie należy to zrobić w Edytor lokalnych zasad grupy na urządzeniu:

1. W **zasadach komputera lokalnego** > **Konfiguracja komputera**, kliknij pozycję **Szablony administracyjne** > **system** > **Delegowanie poświadczeń**.
2. Kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń**i wybierz pozycję **włączone**.
3. W obszarze **Opcje**kliknij pozycję **Pokaż**, a następnie na liście Dodaj każdego hosta funkcji Hyper-V, który ma zostać odnajdowany, przy użyciu **usługi WSMAN/** jako prefiksu.
4. Następnie w obszarze **Delegowanie poświadczeń**kliknij dwukrotnie pozycję **Zezwól na delegowanie świeżych poświadczeń z uwierzytelnianiem serwera tylko NTLM**. Ponownie Dodaj każdy host funkcji Hyper-V, który ma zostać odnalezienie do listy, przy użyciu **usługi WSMAN/** jako prefiksu.

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

Spowoduje to uruchomienie odnajdywania. Dla każdego hosta pozostanie około 1,5 minut na obsługę metadanych odnalezionych serwerów w Azure Portal.

### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu odnajdywania możesz sprawdzić, czy maszyny wirtualne są widoczne w portalu.

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery** > **Azure Migrate: Strona Ocena serwera** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Istnieją dwa typy ocen, które można uruchomić za pomocą Azure Migrate oceny serwera.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparta na wydajności** | Oceny oparte na zebranych danych wydajności | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)** : w zależności od liczby operacji we/wy na sekundę i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie rozmiaru lokalnego maszyny wirtualnej<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu wybieranego do oceny.



### <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen.
2. W obszarze **serwery** > **Azure Migrate: Ocena serwera**, kliknij przycisk **Oceń**.

    ![Ocena](./media/tutorial-assess-hyper-v/assess.png)

3. W obszarze **ocenianie serwerów**Określ nazwę oceny.
4. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. W obszarze **Wybierz lub Utwórz grupę**wybierz pozycję **Utwórz nową** i określ nazwę grupy. Grupa zbiera co najmniej jedną maszynę wirtualną w celu oceny.
4. W obszarze **Dodawanie maszyn do grupy**Wybierz Maszyny wirtualne, które mają zostać dodane do grupy.
5. Kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę, i uruchom ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Po utworzeniu oceny Wyświetl ją w obszarze **serwery** > **Azure Migrate: Ocena serwera**.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.


## <a name="review-an-assessment"></a>Przegląd oceny

Ocena zawiera opis:

- **Gotowość platformy Azure**: czy maszyny wirtualne są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeniowe i magazynowe związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Oszacowanie kosztu miesięcznego magazynu**: szacowane koszty magazynu dyskowego po migracji.


### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W obszarze **cele migracji** >  **serwery** > **Azure Migrate: Ocena serwera**, kliknij przycisk **oceny**.
2. W obszarze **oceny**kliknij ocenę, aby go otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Przegląd gotowości platformy Azure

1. W obszarze **gotowość platformy Azure**Sprawdź, czy maszyny wirtualne są gotowe do migracji na platformę Azure.
2. Sprawdź stan maszyny wirtualnej:
    - **Gotowe do platformy Azure**: Azure Migrate zaleca rozmiar maszyny wirtualnej i oszacowania kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości ze względu na problemy z dostępnością danych.

2. Kliknij stan **gotowości platformy Azure** . Możesz wyświetlić szczegóły gotowości maszyn wirtualnych i przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure.

1. Zapoznaj się z miesięcznymi kosztami obliczeniowymi i magazynem. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie.

    - Oszacowania kosztów opierają się na zaleceń dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Oszacowanie kosztów służy do uruchamiania lokalnych maszyn wirtualnych jako maszyn wirtualnych IaaS. Ocena serwera Azure Migrate nie uwzględnia kosztów PaaS ani SaaS.

2. Możesz przejrzeć szacunkowe oszacowanie kosztów magazynowania. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Możesz przejść do szczegółów, aby wyświetlić szczegóły dla określonych maszyn wirtualnych.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Po uruchomieniu ocen opartych na wydajności, do oceny jest przypisywany rating zgodności.

![Ocena zaufania](./media/tutorial-assess-hyper-v/confidence-rating.png)

- Nadawana jest Ocena z 1 – gwiazdka (najmniejsza) do 5-gwiazdka (najwyższa).
- Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru zapewnianych przez ocenę.
- Ocena zaufania jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.

Klasyfikacje zaufania dla oceny są następujące.

**Dostępność punktu danych** | **Ocenę zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej](best-practices-assessment.md#best-practices-for-confidence-ratings) o najlepszych rozwiązaniach dotyczących klasyfikacji zaufania.





## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie urządzenia Azure Migrate
> * Tworzenie i przeglądanie oceny

Przejdź do trzeciego samouczka z serii, aby dowiedzieć się, jak migrować maszyny wirtualne funkcji Hyper-V do platformy Azure za pomocą migracji Azure Migrate Server.

> [!div class="nextstepaction"]
> [Migrowanie maszyn wirtualnych funkcji Hyper-V](./tutorial-migrate-hyper-v.md)
