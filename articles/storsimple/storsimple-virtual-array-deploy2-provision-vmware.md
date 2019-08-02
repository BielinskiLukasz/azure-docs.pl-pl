---
title: Inicjowanie obsługi administracyjnej macierzy wirtualnej StorSimple w oprogramowaniu VMware | Microsoft Docs
description: Ten drugi samouczek w serii StorSimple Virtual Array Deployment obejmuje Inicjowanie obsługi urządzenia wirtualnego w oprogramowaniu VMware.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab5ad8acc5d0769a19a4022c55e0461e7ce42762
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516822"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Wdrażanie wirtualnej macierzy StorSimple — Inicjowanie obsługi administracyjnej w oprogramowaniu VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Przegląd

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

W tym samouczku opisano sposób aprowizacji i nawiązywania połączenia z tablicą wirtualną StorSimple w systemie hosta z systemem VMware ESXi 5,0, 5,5, 6,0 lub 6,5. Ten artykuł dotyczy wdrażania tablic wirtualnych StorSimple w Azure Portal i w chmurze Microsoft Azure Government.

Do aprowizowania urządzenia wirtualnego i nawiązania z nim połączenia wymagane są uprawnienia administratora. Aprowizacja i wstępna konfiguracja może zająć około 10 minut.

## <a name="provisioning-prerequisites"></a>Wymagania wstępne dotyczące aprowizacji
Wymagania wstępne dotyczące aprowizacji urządzenia wirtualnego w systemie hosta z systemem VMware ESXi 5,0, 5,5, 6,0 lub 6,5 są następujące.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Wykonano wszystkie kroki z sekcji [przygotowanie portalu do macierzy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Pobrano obraz urządzenia wirtualnego dla programu VMware z Azure Portal. Aby uzyskać więcej informacji, **Zobacz Krok 3. Pobierz obraz** urządzenia wirtualnego [Przygotuj Portal for StorSimple Virtual Array Guide](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Dla urządzenia wirtualnego StorSimple
Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

* Masz dostęp do systemu hosta z uruchomioną funkcją Hyper-V (2008 R2 lub nowszą), którego można użyć w celu udostępnienia urządzenia.
* System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurowanie macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 000 000 plików. Do obsługi 2-4 milionów plików potrzebna jest 16 GB pamięci RAM.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB dla danych systemu.

### <a name="for-the-network-in-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem upewnij się, że:

* Zawarto przegląd wymagań sieciowych w celu wdrożenia urządzenia wirtualnego StorSimple i skonfigurowania sieci centrum danych zgodnie z wymaganiami. 

## <a name="step-by-step-provisioning"></a>Inicjowanie obsługi krok po kroku
Aby zapewnić obsługę administracyjną urządzenia wirtualnego i nawiązać z nim połączenie, należy wykonać następujące czynności:

1. Upewnij się, że system hosta ma wystarczające zasoby do spełnienia minimalnych wymagań dotyczących urządzeń wirtualnych.
2. Inicjowanie obsługi administracyjnej urządzenia wirtualnego w funkcji hypervisor.
3. Uruchom urządzenie wirtualne i uzyskaj adres IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Upewnij się, że system hosta spełnia minimalne wymagania dotyczące urządzenia wirtualnego
Aby utworzyć urządzenie wirtualne, potrzebne są:

* Dostęp do systemu hosta z systemem VMware ESXi Server 5,0, 5,5, 6,0 lub 6,5.
* Klient VMware vSphere w systemie na potrzeby zarządzania hostem ESXi.

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurowanie macierzy wirtualnej jako serwera plików, 8 GB obsługuje mniej niż 2 000 000 plików. Do obsługi 2-4 milionów plików potrzebna jest 16 GB pamięci RAM.
  * Jeden interfejs sieciowy połączony z siecią umożliwiającą kierowanie ruchu do Internetu. Minimalna przepustowość internetowa powinna wynosić 5 MB/s, aby zapewnić optymalną pracę z urządzeniem.
  * Dysk wirtualny 500 GB dla danych.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: Aprowizowanie urządzenia wirtualnego w funkcji hypervisor
Wykonaj następujące czynności, aby aprowizować urządzenie wirtualne w funkcji hypervisor.

1. Skopiuj obraz urządzenia wirtualnego do swojego systemu. Ten obraz wirtualny został pobrany za pomocą Azure Portal.

   1. Upewnij się, że pobrano najnowszy plik obrazu. Jeśli wcześniej pobrano obraz, Pobierz go ponownie, aby upewnić się, że masz najnowszy obraz. Najnowszy obraz ma dwa pliki (zamiast jednego).
   2. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.

2. Zaloguj się na serwerze ESXi przy użyciu klienta vSphere. Musisz mieć uprawnienia administratora, aby utworzyć maszynę wirtualną.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. W kliencie vSphere w sekcji spis w okienku po lewej stronie wybierz serwer ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Przekaż dysk VMDK na serwer ESXi. Przejdź do karty **Konfiguracja** w okienku po prawej stronie. W obszarze **sprzęt**wybierz pozycję **Magazyn**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. W okienku po prawej stronie, w obszarze **Datastores** (Magazyny danych), wybierz magazyn danych, do którego chcesz przekazać dysk VMDK. Magazyn danych musi mieć wystarczającą ilość wolnego miejsca dla systemu operacyjnego i dysków z danymi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kliknij prawym przyciskiem myszy i wybierz polecenie **Browse Datastore** (Przeglądaj magazyn danych).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Zostanie wyświetlone okno **Datastore Browser** (Przeglądarka magazynu danych).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na pasku narzędzi kliknij ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonę, aby utworzyć nowy folder. Określ nazwę folderu i zanotuj ją. Ta nazwa folderu będzie używana później, podczas tworzenia maszyny wirtualnej (zalecane najlepsze rozwiązanie). Kliknij przycisk **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Nowy folder zostanie wyświetlony w okienku po lewej stronie okna **Datastore Browser** (Przeglądarka magazynu danych).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Kliknij ikonę ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) Przekaż i wybierz pozycję **Przekaż plik**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Wskaż pobrane pliki VMDK. Pliki są dwa. Wybierz plik do przekazania.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Kliknij przycisk **Open** (Otwórz). Rozpocznie się przekazywanie pliku VMDK do określonego magazynu danych. Przekazanie pliku może zająć kilka minut.
13. Po zakończeniu przekazywania plik będzie widoczny w magazynie danych w utworzonym folderze.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teraz przekaż drugi plik VMDK do tego samego magazynu danych.
14. Wróć do okna klienta vSphere. Po wybraniu serwera ESXi kliknij prawym przyciskiem myszy i wybierz pozycję **Nowa maszyna wirtualna**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Zostanie wyświetlone okno **Utwórz nową maszynę wirtualną** . Na stronie **Konfiguracja** wybierz opcję Niestandardowa. Kliknij przycisk **Dalej**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na stronie **Nazwa i lokalizacja** Określ nazwę swojej maszyny wirtualnej. Ta nazwa powinna być zgodna z nazwą folderu (zalecane najlepsze rozwiązanie) określoną wcześniej w kroku 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na stronie **Magazyn** wybierz magazyn danych, którego chcesz użyć do aprowizacji maszyny wirtualnej.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na stronie **wersja maszyny wirtualnej** wybierz pozycję **wersja maszyny wirtualnej: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na stronie **system operacyjny gościa** wybierz **system operacyjny gościa** jako **system Windows**. W polu **wersja**, z listy rozwijanej wybierz pozycję **Microsoft Windows Server 2012 (64-bit)** .

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na stronie **procesory** Dostosuj **liczbę gniazd wirtualnych** i **liczbę rdzeni na gniazdo wirtualne** , aby **łączna liczba rdzeni** wynosić 4 (lub więcej). Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na stronie **pamięć** Określ 8 GB pamięci RAM (lub więcej). Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na stronie **Sieć** Określ liczbę interfejsów sieciowych. Minimalnym wymaganiem jest jeden interfejs sieciowy.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na stronie **kontroler SCSI** Zaakceptuj domyślny **kontroler LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na stronie **Wybierz dysk** wybierz opcję **Użyj istniejącego dysku wirtualnego**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na stronie **Wybierz istniejący dysk** w obszarze **ścieżka pliku dysku**kliknij przycisk **Przeglądaj**. Spowoduje to otwarcie okna dialogowego **Przeglądaj magazyny** danych. Przejdź do lokalizacji, w której został przekazany dysk VMDK. W magazynie danych jest teraz widoczny tylko jeden plik, w ramach którego zostały scalone dwa pliki, które zostały wcześniej przekazane. Wybierz plik, a następnie kliknij przycisk **OK**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na stronie **Opcje zaawansowane** Zaakceptuj ustawienie domyślne i kliknij przycisk **dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na stronie **Ready to Complete** (Gotowe do ukończenia) przejrzyj wszystkie ustawienia skojarzone z nową maszyną wirtualną. **Przed ukończeniem Sprawdź ustawienia maszyny wirtualnej**. Kliknij pozycję **Kontynuuj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na stronie **właściwości Virtual Machines** na karcie **sprzęt** zlokalizuj sprzęt urządzenia. Wybierz pozycję **nowy dysk twardy**. Kliknij przycisk **Dodaj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zobaczysz okno **Dodaj sprzęt** . Na stronie **Typ urządzenia** w obszarze **Wybierz typ urządzenia, które chcesz dodać**wybierz pozycję **dysk twardy**, a następnie kliknij przycisk **dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na stronie **Wybierz dysk** wybierz opcję **Utwórz nowy dysk wirtualny**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na stronie **Tworzenie dysku** Zmień **rozmiar dysku** na 500 GB (lub więcej). Chociaż 500 GB jest minimalnym wymaganiem, zawsze można zainicjować obsługę większą liczbę dysków. Należy pamiętać, że nie można rozbudować lub zmniejszyć dysku po zainicjowaniu obsługi administracyjnej. Aby uzyskać więcej informacji na temat rozmiaru dysku do aprowizacji, zapoznaj się z sekcją ustalanie rozmiaru w [dokumencie Best Practices](storsimple-ova-best-practices.md). W obszarze **Inicjowanie obsługi dysku**wybierz pozycję **alokowanie elastyczne**. Kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na stronie **Opcje zaawansowane** Zaakceptuj wartość domyślną.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na stronie **gotowy do ukończenia** przejrzyj opcje dysku. Kliknij przycisk **Zakończ**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Wróć do strony właściwości maszyny wirtualnej. Do maszyny wirtualnej zostanie dodany nowy dysk twardy. Kliknij przycisk **Zakończ**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Po wybraniu maszyny wirtualnej w okienku po prawej stronie przejdź do karty **Podsumowanie** . Przejrzyj ustawienia dla swojej maszyny wirtualnej.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Maszyna wirtualna jest teraz aprowizowana. Następnym krokiem jest uruchomienie tej maszyny i uzyskanie adresu IP.

> [!NOTE]
> Firma Microsoft zaleca, aby nie instalować narzędzi VMware w macierzy wirtualnej (zgodnie z powyższym przydziałem). Zainstalowanie narzędzi VMware spowoduje powstanie nieobsługiwanej konfiguracji.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: Uruchamianie urządzenia wirtualnego i uzyskiwanie adresu IP
Wykonaj poniższe kroki, aby uruchomić urządzenie wirtualne i nawiązać z nim połączenie.

#### <a name="to-start-the-virtual-device"></a>Aby uruchomić urządzenie wirtualne
1. Uruchom urządzenie wirtualne. W vSphere Configuration Manager w lewym okienku wybierz urządzenie i kliknij prawym przyciskiem myszy, aby wyświetlić menu kontekstowe. Wybierz pozycję **Power** (Zasilanie), a następnie pozycję **Power on** (Włącz). Maszyna wirtualna powinna zostać włączona. Stan można wyświetlić w okienku **ostatnie zadania** w dolnej części klienta vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Ukończenie zadań instalacyjnych potrwa kilka minut. Po uruchomieniu urządzenia przejdź do karty **konsola** . Wyślij klawisze CTRL + ALT + DELETE, aby zalogować się na urządzeniu. Alternatywnie możesz wskazać kursor w oknie konsoli i nacisnąć klawisze CTRL + ALT + INSERT. Domyślny użytkownik to *StorSimpleAdmin* , a hasło domyślne to *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Zostanie wyświetlony monit informujący o konieczności zmiany hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Wprowadź hasło zawierające co najmniej 8 znaków. Hasło musi zawierać 3 z 4 tych wymagań: wielkie litery, małe litery, cyfry i znaki specjalne. Wprowadź ponownie hasło w celu potwierdzenia. Zostanie wyświetlone powiadomienie o zmianie hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po pomyślnym zmianie hasła urządzenie wirtualne może zostać ponownie rozruchowe. Poczekaj na zakończenie ponownego rozruchu. Konsola programu Windows PowerShell urządzenia może być wyświetlana wraz z paskiem postępu.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroki od 6 do 8 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska protokołu DHCP należy pominąć te kroki i przejść do kroku 9. Jeśli urządzenie zostanie uruchomione w środowisku innym niż DHCP, zobaczysz następujący ekran.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Następnie skonfiguruj sieć.
7. `Get-HcsIpAddress` Użyj polecenia, aby wyświetlić listę interfejsów sieciowych włączonych na urządzeniu wirtualnym. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Przykład przedstawiono poniżej:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Ten adres IP będzie używany do nawiązywania połączenia z interfejsem użytkownika sieci Web urządzenia wirtualnego i dokończ konfigurację lokalną i rejestrację.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Obowiązkowe Wykonaj ten krok tylko wtedy, gdy wdrażasz urządzenie w chmurze dla instytucji rządowych. Na urządzeniu zostanie teraz włączony tryb Stany Zjednoczone FIPS (Federal Information Processing Standard). Standard FIPS 140 definiuje algorytmy kryptograficzne zatwierdzone do użycia przez Federalne systemy komputerowe dla instytucji rządowych USA do ochrony poufnych danych.

    1. Aby włączyć tryb FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Po włączeniu trybu FIPS należy ponownie uruchomić urządzenie, aby zmiany kryptograficzne zaczęły obowiązywać.

       > [!NOTE]
       > Można włączyć lub wyłączyć tryb FIPS na urządzeniu. Przełączanie urządzenia do trybu FIPS i niezgodnego z trybem FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalnych wymagań dotyczących konfiguracji, w tekście baneru zostanie wyświetlony błąd (widoczny poniżej). Trzeba zmodyfikować konfigurację urządzenia tak, aby zapewnić zasoby spełniające minimalne wymagania. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Zapoznaj się z minimalnymi wymaganiami [dotyczącymi konfiguracji w kroku 1: Upewnij się, że system hosta spełnia minimalne wymagania dotyczące](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements)urządzenia wirtualnego.

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Jeśli wystąpi jakikolwiek inny błąd podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci Web, zapoznaj się z następującymi przepływami pracy:

* Uruchom testy diagnostyczne, aby [rozwiązać problemy z konfiguracją interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generuj pakiet dzienników i Wyświetl pliki dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie macierzy wirtualnej StorSimple jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie macierzy wirtualnej StorSimple jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
