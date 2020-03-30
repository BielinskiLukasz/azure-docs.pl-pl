---
title: Aprowizacja storsimple tablicy wirtualnej w VMware
description: Ten drugi samouczek w storsimple virtual array wdrażania serii obejmuje inicjowanie obsługi administracyjnej urządzenia wirtualnego w VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76272094"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Wdrażanie tablicy wirtualnej StorSimple — aprowizowanie w VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

W tym samouczku opisano sposób aprowizowania i łączenia się z tablicą wirtualną StorSimple w systemie hosta z systemem VMware ESXi 5.0, 5.5, 6.0 lub 6.5. Ten artykuł dotyczy wdrażania tablic wirtualnych StorSimple w witrynie Azure portal i chmurze microsoft azure dla instytucji rządowych.

Do aprowizowania urządzenia wirtualnego i nawiązania z nim połączenia wymagane są uprawnienia administratora. Aprowizacja i wstępna konfiguracja może zająć około 10 minut.

## <a name="provisioning-prerequisites"></a>Wymagania wstępne inicjowania obsługi administracyjnej
Wymagania wstępne dotyczące aprowizowania urządzenia wirtualnego w systemie hosta z systemem VMware ESXi 5.0, 5.5, 6.0 lub 6.5 są następujące.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Wykonano wszystkie kroki opisane w trybie [Przygotowywanie portalu dla tablicy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Obraz urządzenia wirtualnego dla VMware został pobrany z witryny Azure portal. Aby uzyskać więcej informacji, zobacz **Krok 3: Pobieranie obrazu urządzenia wirtualnego** [przewodnika "Przygotowywanie portalu dla tablicy wirtualnej StorSimple ".](storsimple-virtual-array-deploy1-portal-prep.md)

### <a name="for-the-storsimple-virtual-device"></a>Dla urządzenia wirtualnego StorSimple
Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

* Masz dostęp do systemu hosta z systemem Hyper-V (2008 R2 lub nowszym), który może służyć do aprowizowania urządzenia.
* System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować tablicę wirtualną jako serwer plików, 8 GB obsługuje mniej niż 2 miliony plików. Potrzebujesz 16 GB pamięci RAM do obsługi 2 - 4 milionów plików.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny o przekątną 500 GB do danych systemowych.

### <a name="for-the-network-in-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem upewnij się, że:

* Przejrzano wymagania sieciowe, aby wdrożyć urządzenie wirtualne StorSimple i skonfigurowano sieć centrum danych zgodnie z wymaganiami. 

## <a name="step-by-step-provisioning"></a>Inicjowanie administracyjne krok po kroku
Aby aprowizować urządzenie wirtualne i połączyć się z nią, należy wykonać następujące czynności:

1. Upewnij się, że system hosta ma wystarczające zasoby, aby spełnić minimalne wymagania urządzenia wirtualnego.
2. Aprowizuj urządzenie wirtualne w hipernadzorcy.
3. Uruchom urządzenie wirtualne i uzyskaj adres IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Upewnij się, że system hosta spełnia minimalne wymagania dotyczące urządzeń wirtualnych
Aby utworzyć urządzenie wirtualne, potrzebne będą:

* Dostęp do systemu hosta z systemem VMware ESXi Server 5.0, 5.5, 6.0 lub 6.5.
* Klient VMware vSphere w systemie na potrzeby zarządzania hostem ESXi.

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurować tablicę wirtualną jako serwer plików, 8 GB obsługuje mniej niż 2 miliony plików. Potrzebujesz 16 GB pamięci RAM do obsługi 2 - 4 milionów plików.
  * Jeden interfejs sieciowy połączony z siecią umożliwiającą kierowanie ruchu do Internetu. Minimalna przepustowość Internetu powinna wynosić 5 Mb/s, aby umożliwić optymalną pracę urządzenia.
  * Dysk wirtualny o przekątną 500 GB dla danych.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: Aprowizuj urządzenie wirtualne w funkcji hypervisor
Wykonaj następujące czynności, aby aprowizować urządzenie wirtualne w funkcji hypervisor.

1. Skopiuj obraz urządzenia wirtualnego do swojego systemu. Ten obraz wirtualny został pobrany za pośrednictwem witryny Azure portal.

   1. Upewnij się, że pobrano najnowszy plik obrazu. Jeśli obraz został pobrany wcześniej, pobierz go ponownie, aby upewnić się, że masz najnowszy obraz. Najnowszy obraz ma dwa pliki (zamiast jednego).
   2. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.

2. Zaloguj się na serwerze ESXi przy użyciu klienta vSphere. Musisz mieć uprawnienia administratora, aby utworzyć maszynę wirtualną.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. W kliencie vSphere w sekcji zapasów w lewym okienku wybierz serwer ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Przekaż dysk VMDK na serwer ESXi. Przejdź do karty **Konfiguracja** w prawym okienku. W obszarze **Sprzęt**wybierz pozycję **Magazyn**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. W okienku po prawej stronie, w obszarze **Datastores** (Magazyny danych), wybierz magazyn danych, do którego chcesz przekazać dysk VMDK. Magazyn danych musi mieć wystarczającą ilość wolnego miejsca dla systemu operacyjnego i dysków z danymi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Kliknij prawym przyciskiem myszy i wybierz polecenie **Browse Datastore** (Przeglądaj magazyn danych).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Zostanie wyświetlone okno **Datastore Browser** (Przeglądarka magazynu danych).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na pasku narzędzi ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) kliknij ikonę, aby utworzyć nowy folder. Określ nazwę folderu i zanotuj ją. Ta nazwa folderu będzie używana później, podczas tworzenia maszyny wirtualnej (zalecane najlepsze rozwiązanie). Kliknij przycisk **OK**.

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
14. Wróć do okna klienta vSphere. Po wybraniu serwera ESXi kliknij prawym przyciskiem myszy i wybierz polecenie **Nowa maszyna wirtualna**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Zostanie wyświetlone okno **Utwórz nową maszynę wirtualną.** Na stronie **Konfiguracja** wybierz opcję **Niestandardowa.** Kliknij przycisk **alej**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na stronie **Nazwa i lokalizacja** określ nazwę maszyny wirtualnej. Ta nazwa powinna być zgodna z nazwą folderu (zalecaną najlepszą praktyką) określoną wcześniej w kroku 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na stronie **Magazyn** wybierz magazyn danych, którego chcesz użyć do aprowizowania maszyny wirtualnej.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na stronie **Wersja maszyny wirtualnej** wybierz pozycję **Wersja maszyny wirtualnej: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na stronie **System operacyjny gość** wybierz system operacyjny dla **gościa** jako **System Windows**. W przypadku **wersji**z listy rozwijanej wybierz pozycję **Microsoft Windows Server 2012 (64-bitowy)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na stronie **procesorów** dostosuj **liczbę gniazd wirtualnych** i **liczbę rdzeni na gniazdo wirtualne,** tak aby **całkowita liczba rdzeni** wynosi 4 (lub więcej). Kliknij przycisk **alej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na stronie **Pamięć** określ 8 GB (lub więcej) pamięci RAM. Kliknij przycisk **alej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na stronie **Sieć** określ liczbę interfejsów sieciowych. Minimalnym wymaganiem jest jeden interfejs sieciowy.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na stronie **Kontroler SCSI** zaakceptuj domyślny **kontroler LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na stronie **Wybierz dysk** wybierz pozycję **Użyj istniejącego dysku wirtualnego**. Kliknij przycisk **alej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na stronie **Wybieranie istniejącego dysku** w obszarze **Ścieżka pliku dysku**kliknij pozycję **Przeglądaj**. Spowoduje to otwarcie okna dialogowego **Przeglądaj magazyny danych.** Przejdź do lokalizacji, w której został przekazany VMDK. Teraz widzisz tylko jeden plik w magazynie danych, ponieważ dwa pliki, które zostały pierwotnie przekazane, zostały scalone. Zaznacz plik i kliknij przycisk **OK**. Kliknij przycisk **alej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na stronie **Opcje zaawansowane** zaakceptuj wartość domyślną i kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na stronie **Ready to Complete** (Gotowe do ukończenia) przejrzyj wszystkie ustawienia skojarzone z nową maszyną wirtualną. Zaznacz **pozycję Edytuj ustawienia maszyny wirtualnej przed zakończeniem**. Kliknij przycisk **Kontynuuj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na stronie **Właściwości maszyn wirtualnych** na karcie **Sprzęt** znajdź sprzęt urządzenia. Wybierz **pozycję Nowy dysk twardy**. Kliknij przycisk **Dodaj**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zostanie wyświetlene okno **Dodaj sprzęt.** Na stronie **Typ urządzenia** w obszarze Wybierz **typ urządzenia, które chcesz dodać**, wybierz pozycję Dysk **twardy**i kliknij przycisk **Dalej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na stronie **Wybierz dysk** wybierz pozycję **Utwórz nowy dysk wirtualny**. Kliknij przycisk **alej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na stronie **Tworzenie dysku** zmień **rozmiar dysku** na 500 GB (lub więcej). Podczas gdy 500 GB jest minimalnym wymaganiem, zawsze można aprowizować większy dysk. Należy zauważyć, że nie można rozwinąć ani zmniejszyć dysku po zainicjowaniu obsługi administracyjnej. Aby uzyskać więcej informacji na temat rozmiaru dysku do zainicjowania, zapoznaj się z sekcją zmiany rozmiaru w [dokumencie najlepszych praktyk](storsimple-ova-best-practices.md). W obszarze **Inicjowanie obsługi administracyjnej dysku**wybierz pozycję **Thin Provision**. Kliknij przycisk **alej**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na stronie **Opcje zaawansowane** zaakceptuj wartość domyślną.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na stronie **Gotowe do ukończenia** przejrzyj opcje dysku. Kliknij przycisk **Zakończ**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Powrót do strony Właściwości maszyny wirtualnej. Nowy dysk twardy zostanie dodany do maszyny wirtualnej. Kliknij przycisk **Zakończ**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Po wybraniu maszyny wirtualnej w prawym okienku przejdź do karty **Podsumowanie.**

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Maszyna wirtualna jest teraz aprowizowana. Następnym krokiem jest uruchomienie tej maszyny i uzyskanie adresu IP.

> [!NOTE]
> Zaleca się, aby nie instalować narzędzi VMware na macierzy wirtualnej (zgodnie z powyższymi zaleceniami). Zainstalowanie narzędzi VMware spowoduje powstanie nieobsługiwanej konfiguracji.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: Uruchom urządzenie wirtualne i uzyskaj adres IP
Wykonaj poniższe kroki, aby uruchomić urządzenie wirtualne i nawiązać z nim połączenie.

#### <a name="to-start-the-virtual-device"></a>Aby uruchomić urządzenie wirtualne
1. Uruchom urządzenie wirtualne. W menedżerze konfiguracji vSphere w lewym okienku wybierz urządzenie i kliknij prawym przyciskiem myszy, aby przywołać menu kontekstowe. Wybierz pozycję **Power** (Zasilanie), a następnie pozycję **Power on** (Włącz). Maszyna wirtualna powinna zostać włączona. Stan można wyświetlić w dolnym okienku **Ostatnie zadania** klienta vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Wykonanie zadań konfiguracyjnych zajmie kilka minut. Po uruchomieniu urządzenia przejdź do karty **Konsola.** Alternatywnie można wycelować kursor w oknie konsoli i nacisnąć klawisze Ctrl+Alt+Insert. Domyślnym użytkownikiem jest *StorSimpleAdmin,* a domyślnym hasłem jest *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa przy pierwszym logowaniu. Zostanie wyświetlony monit informujący o konieczności zmiany hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Wprowadź hasło zawierające co najmniej 8 znaków. Hasło musi zawierać 3 z 4 z tych wymagań: wielkie litery, małe litery, znaki liczbowe i specjalne. Wprowadź ponownie hasło w celu potwierdzenia. Zostaniesz powiadomiony o zmianie hasła.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po pomyślnej zmianie hasła urządzenie wirtualne może ponownie uruchomić komputer. Poczekaj na zakończenie ponownego uruchomienia. Konsola programu Windows PowerShell urządzenia może być wyświetlana wraz z paskiem postępu.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroki od 6 do 8 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska protokołu DHCP należy pominąć te kroki i przejść do kroku 9. Jeśli urządzenie uruchomiliśmy w środowisku innych niż DHCP, zostanie wyświetlony następujący ekran.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Następnie skonfiguruj sieć.
7. Użyj `Get-HcsIpAddress` polecenia, aby wyświetlić listę interfejsów sieciowych włączonych na urządzeniu wirtualnym. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Przykład przedstawiono poniżej:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Ten adres IP będzie używany do łączenia się z internetowym interfejsem użytkownika urządzenia wirtualnego i ukończenia lokalnej konfiguracji i rejestracji.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Opcjonalnie) Ten krok należy wykonać tylko w przypadku wdrażania urządzenia w chmurze instytucji rządowych. Teraz włączysz tryb FIPS (United States Federal Information Processing Standard) na swoim urządzeniu. Standard FIPS 140 definiuje algorytmy kryptograficzne zatwierdzone do użytku przez amerykańskie systemy komputerowe rządu federalnego w celu ochrony poufnych danych.

    1. Aby włączyć tryb FIPS, uruchom następujące polecenie cmdlet:

        `Enable-HcsFIPSMode`
    2. Uruchom ponownie urządzenie po włączeniu trybu FIPS, aby umożliwić zajście w stan działania.

       > [!NOTE]
       > W urządzeniu można włączyć lub wyłączyć tryb FIPS. Przemiennie urządzenia między trybem FIPS i nie-FIPS nie jest obsługiwane.
       >
       >

Jeśli urządzenie nie spełnia minimalnych wymagań dotyczących konfiguracji, w tekście baneru zostanie wyświetlony błąd (widoczny poniżej). Trzeba zmodyfikować konfigurację urządzenia tak, aby zapewnić zasoby spełniające minimalne wymagania. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Minimalne wymagania konfiguracji opisano w sekcji [Krok 1. Sprawdzanie, czy system hosta spełnia minimalne wymagania dotyczące urządzenia wirtualnego](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Jeśli podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci Web napotkasz inny błąd, zapoznaj się z następującymi przepływami pracy:

* Uruchom testy diagnostyczne w celu [rozwiązywania problemów z konfiguracją interfejsu użytkownika sieci](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)Web .
* [Generowanie pakietu dziennika i wyświetlania plików dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie tablicy wirtualnej StorSimple jako serwera plików](storsimple-virtual-array-deploy3-fs-setup.md)
* [Konfigurowanie tablicy wirtualnej StorSimple jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
