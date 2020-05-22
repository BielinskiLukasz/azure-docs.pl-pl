---
title: Microsoft Azure Data Box — często zadawane pytania | Microsoft Docs — dane
description: Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure Data Box, rozwiązania w chmurze umożliwiającego transferowanie dużych ilości danych na platformę Azure, oraz odpowiedzi na te pytania.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: d1455bc65f175970bb741dab4a56aa0aac58fc59
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742993"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: często zadawane pytania

Rozwiązanie hybrydowe Microsoft Azure Data Box umożliwia wysyłanie terabajtów danych na platformę Azure w szybki, niedrogi i niezawodny sposób przy użyciu urządzenia do transferu. Często zadawane pytania zawierają pytania, które mogą się pojawić podczas korzystania z usługi Data Box w witrynie Azure Portal, oraz odpowiedzi na te pytania. 

Pytania i odpowiedzi są podzielone na następujące kategorie:

- Informacje o usłudze
- Zamawianie urządzenia
- Konfigurowanie i łączenie 
- Śledzenie stanu
- Kopiowanie danych 
- Dostawa urządzenia
- Weryfikowanie i przekazywanie danych 
- Pomoc techniczna dotycząca łańcucha nadzoru

## <a name="about-the-service"></a>Informacje o usłudze

### <a name="q-what-is-azure-data-box-service"></a>PYTANIE: Co to jest usługa Azure Data Box? 
A.  Usługa Azure Data Box jest przeznaczona do pozyskiwania danych w trybie offline. Ta usługa zarządza szeregiem produktów dla różnych pojemności magazynu, dostosowanych do transportu danych. 

### <a name="q-what-is-azure-data-box"></a>PYTANIE: Co to jest usługa Azure Data Box?
A. Azure Data Box umożliwia szybkie, niedrogie i bezpieczny transfer terabajtów danych do platformy Azure. Urządzenie Data Box można zamówić w witrynie Azure Portal. Firma Microsoft dostarcza urządzenie magazynujące o pojemności 80 TB za pośrednictwem regionalnego przewoźnika. 

Po otrzymaniu urządzenia możesz szybko je skonfigurować przy użyciu lokalnego internetowego interfejsu użytkownika. Skopiuj dane z serwerów na urządzenie, a następnie wyślij urządzenie z powrotem do centrum danych platformy Azure. W centrum danych platformy Azure Twoje dane są automatycznie przekazywane z urządzenia na platformę Azure. Cały proces można śledzić przez usługę Data Box w witrynie Azure Portal.

### <a name="q-when-should-i-use-data-box"></a>PYTANIE: Kiedy używać usługi Data Box?
A. Jeśli masz od 40 do 500 TB danych, które chcesz przetransferować na platformę Azure, korzystne będzie zastosowanie usługi Data Box. W przypadku rozmiarów danych < 40 TB Użyj Data Box Disk i dla rozmiarów danych > 500 TB, zarejestruj się, aby uzyskać [Data Box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>PYTANIE: Jaki jest koszt usługi Data Box?
A. Usługa Data Box jest dostępna za symboliczną opłatą przez 10 dni. Po wybraniu modelu produktu podczas tworzenia zamówienia w witrynie Azure Portal zostaną wyświetlone opłaty za urządzenie. Bezpłatna jest także przesyłka, natomiast zostaną naliczone opłaty za usługę Azure Storage. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Data Box](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>PYTANIE: Jaka jest maksymalna ilość danych, które mogę jednorazowo przetransferować za pomocą urządzenia Data Box?
A. Urządzenie Data Box ma 100 TB niesformatowanej pojemności i 80 TB pojemności do wykorzystania. Za pomocą urządzenia Data Box możesz przetransferować do 80 TB danych. Aby przetransferować większą ilość danych, musisz zamówić więcej urządzeń.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>PYTANIE: Jak sprawdzić, czy usługa Data Box jest dostępna w moim regionie? 
A.  Aby uzyskać informacje na temat krajów/regionów dostępnych urządzenie Data Box, przejdź do [obszaru dostępność regionu](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>PYTANIE: W jakich regionach mogę przechowywać dane, korzystając z usługi Data Box?
A. Urządzenie Data Box jest obsługiwana w przypadku wszystkich regionów w Stanach Zjednoczonych, Europa Zachodnia, Europa Północna, Francja, Zjednoczone Królestwo, Japonia, Australia i Kanada. Aby uzyskać więcej informacji, przejdź do [obszaru dostępność regionu](data-box-overview.md#region-availability).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>PYTANIE: Z kim należy się skontaktować, gdy pojawią się jakiekolwiek problemy z usługą Data Box?
A. Jeśli napotkasz jakiekolwiek problemy z usługą Data Box, [skontaktuj się z działem pomocy technicznej firmy Microsoft](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>PYTANIE: Moje urządzenie Data Box zostało zgubione. Czy jest opłata za utracone urządzenie?
A. Tak. Istnieje opłata za utracone lub uszkodzone urządzenie. Wysokość tej opłaty można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/storage/databox/) oraz w [warunkach użytkowania usługi dla tego produktu](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Zamawianie urządzenia

### <a name="q-how-do-i-get-data-box"></a>PYTANIE: Jak mogę otrzymać urządzenie Data Box? 
A.  Aby otrzymać urządzenie Azure Data Box, zaloguj się do witryny Azure Portal i utwórz zamówienie na urządzenie Data Box. Podaj informacje kontaktowe i szczegóły dotyczące powiadomień. Po złożeniu zamówienia, w zależności od dostępności, urządzenie Data Box zostanie dostarczone w ciągu 10 dni. Aby uzyskać więcej informacji, zobacz temat [Zamawianie urządzenia Data Box](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>PYTANIE: Nie udało się utworzyć zamówienia na urządzenie Data Box w witrynie Azure Portal. Co może być powodem?
A. Jeśli nie udało się utworzyć zamówienia na urządzenie Data Box, problem może być związany z typem subskrypcji lub dostępem. 

Sprawdź swoją subskrypcję. Urządzenie Data Box jest dostępne wyłącznie w dla ofert subskrypcji w ramach umowy Enterprise Agreement (EA) i programu Cloud Solution Provider (CSP). Jeśli Twoja subskrypcja nie kwalifikuje się do żadnego z powyższych typów, skontaktuj się z działem pomocy technicznej firmy Microsoft, aby ją zmienić.

Jeśli masz obsługiwany typ oferty subskrypcji, sprawdź poziom dostępu swojej subskrypcji. Musisz być współautorem lub właścicielem subskrypcji, aby utworzyć zamówienie.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>PYTANIE: Zamawiam kilka urządzeń Data Box. Nie mogę utworzyć kolejnego zamówienia. Co może być powodem?
A. Zezwalamy na maksymalnie pięć aktywnych zamówień na subskrypcję w granicy handlowej (kombinacja wybranego regionu i kraju). Jeśli chcesz zamówić dodatkowe urządzenie, skontaktuj się z działem pomocy technicznej firmy Microsoft, aby zwiększyć limit swojej subskrypcji.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>PYTANIE: Podczas próby utworzenia zamówienia otrzymuję powiadomienie, że usługa Data Box jest niedostępna. Co to oznacza?
A. Oznacza to, że usługa Data Box jest niedostępna dla kombinacji wybranego regionu i kraju. Zmiana tej kombinacji prawdopodobnie umożliwi skorzystanie z usługi Data Box. Aby uzyskać listę regionów, w których usługa jest dostępna, przejdź do tematu [Dostępność regionalna urządzeń Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>PYTANIE: Moje zamówienie na urządzenie Data Box zostało złożone kilka dni temu. Kiedy otrzymam urządzenie Data Box?
A. Po złożeniu zamówienia sprawdzamy, czy urządzenie dla zamówienia jest dostępne. Jeśli urządzenie jest dostępne, firma Microsoft dostarcza je w ciągu 10 dni. Możliwe, że będą występować pewne okresy dużego zapotrzebowania. W takiej sytuacji Twoje zamówienie zostanie umieszczone w kolejce, a w witrynie Azure Portal będzie można śledzić zmiany stanu. Jeśli zamówienie nie zostanie zrealizowane w ciągu 90 dni, zostanie automatycznie anulowane.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>PYTANIE: Urządzenie Data Box zostało zapełnione danymi i muszę zamówić kolejne. Czy można szybko złożyć zamówienie?
A. W takim przypadku możesz sklonować poprzednie zamówienie. Klonowanie tworzy takie samo zamówienie i możliwe jest edytowanie jego szczegółów bez konieczności wpisywania adresu, danych kontaktowych i danych dotyczących powiadomień.

## <a name="configure-and-connect"></a>Konfigurowanie i łączenie

### <a name="q-how-do-i-unlock-the-data-box"></a>PYTANIE: Jak mogę odblokować urządzenie Data Box? 
A.  W witrynie Azure Portal przejdź do zamówienia na urządzenie Data Box, a następnie wybierz pozycję **Szczegóły urządzenia**. Skopiuj hasło odblokowania. Użyj tego hasła, aby zalogować się do lokalnego internetowego interfejsu użytkownika na swoim urządzeniu Data Box. Aby uzyskać więcej informacji, zobacz temat [Samouczek: rozpakowywanie, podłączanie kabli, łączenie urządzenia Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>PYTANIE: Czy mogę użyć komputera-hosta z systemem Linux, aby nawiązać połączenie z urządzeniem Data Box i skopiować na nie dane?
A.  Tak. Urządzenie Data Box może być łączone z klientami NFS i SMB. Aby uzyskać więcej informacji, przejdź do listy [Obsługiwane systemy operacyjne](data-box-system-requirements.md) komputera-hosta.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>PYTANIE: Moje urządzenie Data Box zostało wysłane, ale teraz chcę anulować to zamówienie. Dlaczego przycisk Anuluj nie jest dostępny?
A.  Zamówienie można anulować tylko po złożeniu zamówienia na urządzenie Data Box i przed jego przetworzeniem. Po przetworzeniu zamówienia na urządzenie Data Box nie można go już anulować. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>PYTANIE: Czy mogę podłączyć urządzenie Data Box jednocześnie do wielu komputerów-hostów w celu transferu danych?
A. Tak. Urządzenie Data Box można podłączyć do wielu komputerów-hostów na potrzeby transferu danych i można równolegle wykonywać wiele zadań kopiowania. Aby uzyskać więcej informacji, zobacz temat [Samouczek: kopiowanie danych na urządzenie Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>PYTANIE: Czy mogę połączyć się obydwoma interfejsami 10 GbE na urządzeniu Data Box w celu przesyłania danych?
A. Tak. Można jednocześnie podłączyć obydwa interfejsy 10 GbE na urządzeniu Data Box w celu kopiowania danych. Aby uzyskać więcej informacji na temat sposobu kopiowania danych, przejdź do [samouczka: Kopiuj dane do Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>PYTANIE: Wskaźnik LED błędu systemu na przednim panelu operacyjnym świeci się. Co mam zrobić?
A. Jeśli wskaźnik LED błędu systemu świeci się, oznacza to, że system jest w złej kondycji. [Skontaktuj się pomoc techniczna firmy Microsoft](data-box-disk-contact-microsoft-support.md) w celu wykonania następnych kroków.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>PYTANIE: Nie mogę uzyskać dostępu do hasła odblokowania urządzenia Data Box w witrynie Azure Portal. Co może być powodem?
A. Jeśli nie możesz uzyskać dostępu do hasła odblokowania w witrynie Azure Portal, sprawdź uprawnienia w swojej subskrypcji i na koncie magazynu. Upewnij się, że masz uprawnienia współautora lub właściciela na poziomie grupy zasobów. W przeciwnym razie musisz mieć co najmniej uprawnienia roli Operator dla urządzenia Data Box, aby wyświetlić poświadczenia dostępu.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>PYTANIE: Czy na urządzeniu Data Box jest obsługiwana konfiguracja kanału portu? Jak wygląda sytuacja w przypadku wielościeżkowego we/wy (MPIO)?
A. Nie obsługujemy konfiguracji kanału portu, konfiguracji wielościeżkowego we/wy (MPIO) ani konfiguracji sieci vLAN na urządzeniu Data Box.

## <a name="track-status"></a>Śledzenie stanu

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>PYTANIE: Jak mogę śledzić stan urządzenia Data Box od momentu złożenia zamówienia do jego odesłania? 
A.  Stan zamówienia urządzenia Data Box możesz śledzić w witrynie Azure Portal. Podczas tworzenia zamówienia jest również wyświetlany monit o podanie adresu e-mail do powiadomień. Jeśli go podasz, będziesz otrzymywać powiadomienia e-mail o każdej zmianie stanu zamówienia. Więcej informacji na temat [konfigurowania wiadomości e-mail z powiadomieniami](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>PYTANIE: Jak mogę zwrócić urządzenie? 
A.  Firma Microsoft wyświetla etykietę wysyłkową na wyświetlaczu E-ink. Jeśli etykieta wysyłkowa nie pojawi się na ekranie E-ink, przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**. Pobierz i wydrukuj etykietę, umieść ją w przezroczystej plastikowej koszulce na urządzeniu i oddaj urządzenie w lokalizacji firmy przewozowej. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>PYTANIE: Przysłano mi wiadomość e-mail z powiadomieniem, że moje urządzenie dotarło do centrum danych platformy Azure. Jak mogę sprawdzić, czy przekazywanie danych jest w toku?
A. Możesz przejść do swojego zamówienia urządzenia Data Box w witrynie Azure Portal i przejść do sekcji **Przegląd**. Jeśli przekazywanie danych do platformy Azure zostało rozpoczęte, w okienku po prawej stronie zobaczysz postęp kopiowania. 

## <a name="migrate-data"></a>Migrowanie danych

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>PYTANIE: Jaki jest maksymalny rozmiar danych w przypadku urządzenia Data Box?  
A.  Urządzenie Data Box ma 80 TB pojemności magazynu do wykorzystania. W przypadku danych zajmujących od 40 TB do 80 TB możesz użyć pojedynczego urządzenia Data Box. W przypadku większych ilości danych, nieprzekraczających 500 TB, możesz zamówić wiele urządzeń Data Box. W przypadku danych przekraczających 500 TB zarejestruj się w usłudze Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>PYTANIE: Jakie są maksymalne rozmiary blokowych obiektów blob i stronicowych obiektów blob obsługiwane przez urządzenia Data Box? 
A.  Maksymalne rozmiary podlegają limitom usługi Azure Storage. Maksymalny rozmiar blokowego obiektu blob to około 4,768 TiB, a maksymalny rozmiar stronicowego obiektu blob to 8 TiB. Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage](../storage/blobs/scalability-targets.md).

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>PYTANIE: Skąd mam wiedzieć, że moje dane są bezpieczne podczas transportu? 
A. Istnieje wiele funkcji zabezpieczeń wdrożonych w celu zapewnienia bezpieczeństwa urządzenia Data Box w czasie transportu. Niektóre z nich obejmują plomby umożliwiające wykrycie manipulacji, wykrywanie ingerencji w sprzęt i oprogramowanie, hasło odblokowania urządzenia. Aby uzyskać więcej informacji, przejdź do tematu [Zabezpieczenia i ochrona danych w usłudze Azure Data Box](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>PYTANIE: Jak mogę skopiować dane na urządzenie Data Box? 
A.  Jeśli używasz klienta SMB, możesz skopiować dane na urządzenie za pomocą narzędzia kopiowania SMB, takiego jak Robocopy czy Diskboss, a nawet przeciągając i upuszczając pliki w Eksploratorze plików systemu Windows. 

Jeśli używasz klienta NFS, możesz użyć narzędzia [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) lub [Ultracopier](https://ultracopier.first-world.info/). 

Aby uzyskać więcej informacji, zobacz temat [Samouczek: kopiowanie danych na urządzenie Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>PYTANIE: Czy istnieją jakieś porady dotyczące przyspieszania kopiowania danych?
A.  Aby przyspieszyć proces kopiowania:

- Użyj wielu strumieni kopiowania danych. Na przykład w narzędziu Robocopy włącz opcję kopiowania wielowątkowego. Aby uzyskać więcej informacji na temat konkretnego polecenia, zobacz [Samouczek: kopiowanie danych na urządzenie Data Box i ich weryfikacja](data-box-deploy-copy-data.md).
- Użyj wielu sesji.
- Zamiast kopiować za pośrednictwem udziału sieciowego (gdzie szybkość sieci może narzucać ograniczenia), upewnij się, że dane znajdują się lokalnie na komputerze, do którego podłączono urządzenie Data Box.
- Wykonaj test porównawczy wydajności komputera używanego do kopiowania danych. Pobierz [narzędzie Bluestop FIO](https://ci.appveyor.com/project/axboe/fio) i za jego pomocą przeprowadź test porównawczy wydajności sprzętu serwera. Wybierz najnowszą kompilację x86 lub x64, wybierz kartę **artefakty** i Pobierz plik msi.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>PYTANIE: Czy mogę używać wielu kont magazynu z urządzeniem Data Box?
A.  Tak. Z urządzeniem Data Box obsługiwanych jest maksymalnie 10 kont magazynu ogólnego przeznaczenia, klasycznych lub usługi Blob Storage. Obsługiwane są obiekty blob zarówno w warstwie Gorąca, jak i Chłodna. 


## <a name="ship-device"></a>Dostawa urządzenia

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>PYTANIE: Urządzenie zostało dostarczone, ale wydaje się być uszkodzone. Co mam zrobić?
A. Jeśli urządzenie zostało dostarczone uszkodzone lub istnieje dowód manipulowania przy nim, nie należy go używać. [Skontaktuj się z działem pomocy technicznej firmy Microsoft](data-box-disk-contact-microsoft-support.md) i jak najszybciej zwróć urządzenie. Aby otrzymać urządzenie zastępcze, można także utworzyć nowe zamówienie na urządzenie Data Box. W tym przypadku opłata za urządzenie zastępcze nie zostanie naliczona.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>PYTANIE: Czy mogę wybrać moją urządzenie Data Box zamówienie samodzielnie? Czy mogę zwrócić urządzenie Data Box za pośrednictwem wybranego przez siebie operatora?
A. Tak. Firma Microsoft oferuje również samozarządzaną wysyłkę wyłącznie w US Gov regionie. Podczas umieszczania kolejności urządzenie Data Box można wybrać opcję samozarządzanej wysyłki. Aby wybrać urządzenie urządzenie Data Box, wykonaj następujące czynności:
    
1. Po złożeniu zamówienia zamówienie jest przetwarzane i urządzenie Data Box jest przygotowywane. Otrzymasz powiadomienie za pośrednictwem wiadomości e-mail, że zamówienie jest gotowe do odbioru. 
2. Gdy zamówienie będzie gotowe do pobrania, przejdź do zamówienia w Azure Portal i przejdź do bloku **Przegląd** . 
3. Zobaczysz powiadomienie z kodem w Azure Portal. Wyślij wiadomość e-mail do [zespołu operacji Azure Data Box](mailto:adbops@microsoft.com) i podaj kod. Zespół dostarczy lokalizację i zaplanował datę i godzinę pobrania. Po otrzymaniu powiadomienia e-mail należy zadzwonić do zespołu w ciągu 5 dni roboczych.

Po zakończeniu kopiowania danych wykonaj następujące kroki, aby zwrócić urządzenie:

1. Po zakończeniu kopiowania danych bez błędów Uruchom **przygotowanie do wysłania**. Po zakończeniu przygotowania otrzymasz kod w lokalnym interfejsie użytkownika sieci Web urządzenia. Skopiuj i Zapisz kod.
2. Wyłącz urządzenie i Usuń kable łączące.
3. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
4. Wyślij wiadomość e-mail do [zespołu operacji Azure Data Box](mailto:adbops@microsoft.com) i podaj kod, który został wcześniej zapisany. Udostępnimy informacje o tym, gdzie i kiedy należy odrzucić urządzenie.

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>PYTANIE: Czy moje urządzenie Data Box urządzenia będą przekraczać granice kraju/regionu podczas wysyłki?
A. Wszystkie urządzenia urządzenie Data Box są dostarczane z tego samego kraju/regionu co ich lokalizacja docelowa i nie będą przecinać żadnych granic międzynarodowych. Jedyny wyjątek dotyczy zamówień w Unii Europejskiej (UE), w których urządzenia mogą być dostarczane do i z dowolnego kraju/regionu UE. Dotyczy to zarówno urządzeń urządzenie Data Box, jak i Data Box Heavy.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>PYTANIE: W regionie Wschodnie stany USA zamówiłem urządzenie Data Box, ale otrzymałem urządzenie, które było dostarczane z lokalizacji w zachodnich stanach USA. Gdzie należy zwrócić urządzenie?
A. Firma Microsoft próbuje uzyskać urządzenie Data Box urządzenie tak szybko, jak to możliwe. Firma Microsoft ustala priorytety wysyłki od centrum danych znajdującego się najbliżej lokalizacji konta magazynu, ale wyśle Urządzenie z dowolnego centrum danych platformy Azure, które ma dostępny spis. Twoja urządzenie Data Box powinna zostać zwrócona do tej samej lokalizacji, z której zostało wysłane, tak jak jest wyświetlana w etykiecie wysyłkowej.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>PYTANIE: Etykieta wysyłki zwrotnej nie jest widoczna na wyświetlaczu E-ink. Co mam zrobić?
A. Jeśli na wyświetlaczu E-ink nie widać etykiety wysyłki zwrotnej, wykonaj następujące czynności:
- Usuń starą etykietę wysyłkową i wszelkie nalepki z poprzedniej wysyłki.
- Przejdź do swojego zamówienia w witrynie Azure Portal. Przejdź do pozycji **Przegląd** i wybierz pozycję **Pobierz etykietę wysyłkową**. Aby uzyskać więcej informacji, przejdź do sekcji [Pobierz etykietę wysyłkową](data-box-portal-admin.md#download-shipping-label).
- Wydrukuj etykietę wysyłkową i włóż ją do przezroczystej plastikowej koszulki dołączonej do urządzenia. 
- Upewnij się, że etykieta wysyłkowa jest dobrze widoczna. 

### <a name="q-how-is-my-data-protected-during-transit"></a>PYTANIE: Jak moje dane są chronione w transporcie? 
A.  W czasie transportu dane są chronione dzięki następującym cechom urządzenia Data Box.
 - Dyski urządzenia Data Box są zaszyfrowane za pomocą 256-bitowego szyfrowania AES. 
 - Urządzenie jest zablokowane i wymaga hasła odblokowania w celu wprowadzania danych i uzyskania do nich dostępu.
Aby uzyskać więcej informacji, zobacz temat [Funkcje zabezpieczeń urządzenia Data Box](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>PYTANIE: Przygotowywanie urządzenia do wysłania zostało zakończone, a urządzenie zostało wyłączone. Czy wciąż mogę dodać więcej danych do urządzenia Data Box?
A. Tak. Włączenie urządzenia i dodanie większej ilości danych jest możliwe. Po zakończeniu kopiowania danych należy ponownie uruchomić funkcję **Przygotuj do wysłania**.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>PYTANIE: Otrzymane urządzenie nie uruchamia się. Jak mogę je odesłać?
A. Jeśli urządzenie nie uruchamia się, przejdź do zamówienia w witrynie Azure Portal. Pobierz etykietę wysyłkową i dołącz ją do urządzenia. Aby uzyskać więcej informacji, przejdź do sekcji [Pobierz etykietę wysyłkową](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Weryfikowanie i przekazywanie

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>PYTANIE: Jak wkrótce mogę uzyskać dostęp do danych na platformie Azure po przeprowadzeniu urządzenie Data Box z powrotem? 
A.  Gdy stan zamówienia dla **kopii danych** zostanie wyświetlony jako **zakończony**, dostęp do danych powinien być od razu możliwy.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>PYTANIE: Gdzie znajdują się moje dane na platformie Azure po przekazaniu?
A.  Po skopiowaniu danych na urządzenie Data Box, w zależności od tego, czy dane są blokowymi obiektami blob, stronicowymi obiektami blob czy plikami usługi Azure Files, dane są przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>PYTANIE: Nazwy moich kontenerów nie są zgodne z wymaganiami dotyczącymi nazewnictwa platformy Azure. Czy przekazywanie moich danych na platformę Azure nie powiedzie się?
A.  Jeśli nazwy kontenerów zawierają wielkie litery, zostaną one automatycznie przekonwertowane na małe litery. Jeśli nazwy nie są zgodne w inny sposób (znaki specjalne, inne języki itd.), przekazywanie zakończy się niepowodzeniem. Aby uzyskać więcej informacji na temat najlepszych rozwiązań dotyczących nazewnictwa udziałów, kontenerów i plików, przejdź do tematów:
- [Nazywanie i przywoływanie udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Konwencje stronicowych i blokowych obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>PYTANIE: Jak mogę zweryfikować dane skopiowane na urządzenie Data Box?
A.  Dane są weryfikowane po zakończeniu kopiowania danych i uruchomieniu opcji **Przygotuj do wysłania**. Urządzenie Data Box generuje listę plików i sum kontrolnych dla danych w trakcie procesu weryfikacji. Listę plików można pobrać i porównać ją z plikami w danych źródłowych. Aby uzyskać więcej informacji, przejdź do tematu [Przygotowywanie do wysłania](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>PYTANIE: Co się stanie z moimi danymi po zwróceniu urządzenia Data Box?
A.  Po zakończeniu kopiowania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi NIST SP 800-88 Revision 1. Aby uzyskać więcej informacji, przejdź do tematu [Wymazywanie danych z urządzenia Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Raport z inspekcji

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>W jaki sposób usługa Azure Data Box pomaga klientom wspierać procedurę łańcucha nadzoru?
A.  Usługa Azure Data Box natywnie dostarcza raporty, których można użyć do dokumentacji łańcucha nadzoru. Dzienniki inspekcji i kopiowania są dostępne na koncie magazynu na platformie Azure, a [historię zamówień można pobrać](data-box-portal-admin.md#download-order-history) w witrynie Azure Portal po zakończeniu realizacji zamówienia.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Jaki typ raportowania jest dostępny do obsługi łańcucha nadzoru?
A.  Do obsługi łańcucha nadzoru dostępne są następujące typy raportowania:

- Logistyka transportu od firmy UPS.
- Rejestrowanie włączenia i dostęp do udziału użytkownika.
- Plik manifestu z 64-bitową cykliczną kontrolą nadmiarowości (CRC-64) lub suma kontrolna dla każdego pliku pozyskanego pomyślnie do urządzenia Data Box.
- Raportowanie plików, których nie można przekazać na konto usługi Azure Storage.
- Oczyszczanie urządzenia Data Box (zgodnie z normami NIST 800 88R1) po skopiowaniu danych na konto usługi Azure Storage.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Czy są dostępne dzienniki śledzenia przewoźnika (od firmy UPS)? 
A.  Dzienniki śledzenia przewoźnika są przechwytywane w historii zamówień urządzenia Data Box. Ten raport jest dostępny dla Ciebie po powrocie urządzenia do centrum danych platformy Azure i po wyczyszczeniu danych z dysków urządzenia. W razie potrzeby możesz także przejść bezpośrednio do witryny sieci Web operatora z numerem śledzenia zamówienia i uzyskać informacje o śledzeniu.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Czy mogę przetransportować urządzenie Data Box do centrum danych platformy Azure? 
A.  Nie. Obecnie centrum danych platformy Azure nie akceptuje dostarczania urządzeń Data Box od klientów lub od przewoźników innych niż UPS.


## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wymagania systemowe usługi Data Box](data-box-system-requirements.md).
- Poznaj [ograniczenia usługi Data Box](data-box-limits.md).
- Szybko wdróż usługę [Azure Data Box](data-box-quickstart-portal.md) w witrynie Azure Portal.
