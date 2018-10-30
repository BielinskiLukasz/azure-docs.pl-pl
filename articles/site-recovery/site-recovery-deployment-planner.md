---
title: Planista wdrażania usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure — informacje | Dokumentacja firmy Microsoft
description: Więcej informacji na temat planista wdrażania usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: nisoneji
ms.openlocfilehash: 5aade5a2ad0b0f51c5bd7f53ed0ee191950aa7c4
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213325"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Planista wdrażania usługi Azure Site Recovery, programu VMware do platformy Azure — informacje
Ten artykuł to podręcznik użytkownika planisty wdrażania usługi Azure Site Recovery dla wdrożeń produkcyjnych oprogramowania VMware na platformie Azure.

## <a name="overview"></a>Przegląd

Zanim zaczniesz chronić maszyny wirtualne oprogramowania VMware za pomocą usługi Azure Site Recovery, przydziel odpowiednią przepustowość zgodnie z częstotliwością dziennych zmian danych, aby osiągnąć założony cel punktu odzyskiwania. Pamiętaj o lokalnym wdrożeniu odpowiedniej liczby serwerów konfiguracji i serwerów przetwarzania.

Musisz także utworzyć właściwą liczbę kont magazynów platformy Azure odpowiedniego typu. Możesz utworzyć konta magazynu w warstwie Standardowa lub Premium, biorąc pod uwagę wzrost w zakresie źródłowych serwerów produkcyjnych z powodu zwiększania użycia w czasie. Wybierz typ magazynu dla maszyny wirtualnej na podstawie charakterystyk obciążenia, np. operacji we/wy odczytu/zapisu na sekundę (IOPS) lub współczynnika zmian danych, oraz limitów usługi Site Recovery.

 Planista wdrażania usługi Site Recovery to narzędzie wiersza polecenia na potrzeby scenariuszy odzyskiwania po awarii z funkcji Hyper-V do platformy Azure i z oprogramowania VMware do platformy Azure. To narzędzie pozwala zdalnie profilować maszyny wirtualne oprogramowania VMware (bez żadnego wpływu na środowisko produkcyjne), aby poznać wymagania dotyczące przepustowości i magazynu dla udanej replikacji oraz testu pracy w trybie failover. Narzędzie możesz uruchomić bez instalowania składników usługi Site Recovery w środowisku lokalnym. W celu uzyskania dokładnych wyników osiągniętej przepływności uruchom planistę na serwerze z systemem Windows Server spełniającym minimalne wymagania dotyczące serwera konfiguracji usługi Site Recovery, który trzeba będzie wdrożyć na początku procesu wdrażania w środowisku produkcyjnym.

Narzędzie udostępnia następujące szczegóły:

**Ocena zgodności**

* Ocena uprawnień maszyny wirtualnej na podstawie liczby dysków, rozmiaru dysku, liczby operacji we/wy na sekundę, współczynnika zmian, typu rozruchu (EFI/BIOS) i wersji systemu operacyjnego

**Zapotrzebowanie na przepustowość sieci w porównaniu z oceną celu punktu odzyskiwania**

* Szacowana przepustowość sieci wymagana na potrzeby replikacji przyrostowej
* Przepływność, którą usługa Site Recovery może uzyskać między środowiskiem lokalnym i platformą Azure
* Liczba maszyn wirtualnych do przetworzenia wsadowego na podstawie szacowanej przepustowości wymagana do ukończenia replikacji początkowej w określonym czasie
* Cel punktu odzyskiwania, który można osiągnąć przy danej przepustowości
* Wpływ na wymagany cel punktu odzyskiwania w przypadku zapewnienia mniejszej przepustowości

**Wymagania dotyczące infrastruktury platformy Azure**

* Wymagania dotyczące typu magazynu (konta w warstwie Standardowa lub Premium) dla poszczególnych maszyn wirtualnych
* Łączna liczba kont magazynu w warstwie Standardowa i Premium do skonfigurowania na potrzeby replikacji
* Propozycje nazw kont magazynu oparte na wskazówkach usługi Storage
* Rozmieszczanie konta magazynu dla wszystkich maszyn wirtualnych
* Liczba rdzeni platformy Azure do skonfigurowania przed rozpoczęciem pracy w trybie failover lub testu pracy w trybie failover w ramach subskrypcji
* Zalecany rozmiar poszczególnych maszyn wirtualnych platformy Azure

**Wymagania dotyczące infrastruktury lokalnej**
* Wymagana liczba serwerów konfiguracji i serwerów przetwarzania do wdrożenia lokalnie

**Szacowany koszt odzyskiwania po awarii na platformie Azure**
* Szacowany łączny koszt odzyskiwania po awarii do platformy Azure: koszt obliczeń, magazynu, sieci i licencji usługi Site Recovery
* Szczegółowa analiza kosztów dla maszyny wirtualnej


>[!IMPORTANT]
>
>Ponieważ obciążenie będzie prawdopodobnie zwiększać się wraz z upływem czasu, wszystkie poprzednie obliczenia narzędzia są wykonywane przy założeniu 30-procentowego współczynnika wzrostu wartości charakterystyk obciążenia. W obliczeniach jest też używana wartość 95. percentyla wszystkich metryk profilowania, takich jak liczba operacji we/wy odczytu/zapisu na sekundę i współczynnik zmian danych. Zarówno współczynnik wzrostu, jak i obliczenie procentowe można konfigurować. Więcej informacji na temat współczynnika wzrostu można znaleźć w sekcji „Zagadnienia związane ze współczynnikiem wzrostu”. Więcej informacji na temat wartości percentyla można znaleźć w sekcji „Wartość percentyla użyta w obliczeniach”.
>

## <a name="support-matrix"></a>Tabela obsługi

| | **Z programu VMware do platformy Azure** |**Z funkcji Hyper-V do platformy Azure**|**Z platformy Azure do platformy Azure**|**Z funkcji Hyper-V do lokacji dodatkowej**|**Z oprogramowania VMware do lokacji dodatkowej**
--|--|--|--|--|--
Obsługiwane scenariusze |Yes|Yes|Nie|Tak*|Nie
Obsługiwana wersja | vCenter 6.5, 6.0 lub 5.5| Windows Server 2016, Windows Server 2012 R2 | Nie dotyczy |Windows Server 2016, Windows Server 2012 R2|Nie dotyczy
Obsługiwana konfiguracja|vCenter, ESXi| Klaster funkcji Hyper-V, host funkcji Hyper-V|Nie dotyczy|Klaster funkcji Hyper-V, host funkcji Hyper-V|Nie dotyczy|
Liczba serwerów, które mogą być profilowane, na uruchomione wystąpienie planisty wdrażania usługi Site Recovery |Jeden (w tym samym czasie można profilować maszyny wirtualne należące do jednego serwera vCenter lub jednego serwera ESXi)|Wiele (w tym samym czasie można profilować maszyny wirtualne należące do wielu hostów lub klastrów hostów)| Nie dotyczy |Wiele (w tym samym czasie można profilować maszyny wirtualne należące do wielu hostów lub klastrów hostów)| Nie dotyczy

* Narzędzie jest przeznaczone głównie dla scenariuszy odzyskiwania po awarii z funkcji Hyper-V do platformy Azure. W przypadku odzyskiwania po awarii z funkcji Hyper-V do lokacji dodatkowej można używać go tylko do sprawdzania zaleceń po stronie źródła, takich jak wymagana przepustowość, wymagana ilość wolnego miejsca na każdym serwerze źródłowym funkcji Hyper-V oraz wartości dzielenia na partie replikacji początkowej i definicje partii. Zignoruj zalecenia i koszty dotyczące platformy Azure z raportu. Ponadto nie można używać operacji uzyskiwania informacji o przepływności w scenariuszu odzyskiwania po awarii z funkcji Hyper-V do lokacji dodatkowej.

## <a name="prerequisites"></a>Wymagania wstępne
Narzędzie obejmuje dwa główne etapy — profilowanie i generowanie raportu. Jest też dostępny trzeci etap umożliwiający obliczanie tylko przepływności. Wymagania dotyczące serwera, z którego inicjowane jest profilowanie i pomiar przepływności, zostały przedstawione w poniższej tabeli.

| Wymaganie dotyczące serwera | Opis|
|---|---|
|Profilowanie i pomiar przepływności| <ul><li>System operacyjny: Windows Server 2016 lub Windows Server 2012 R2<br>(w idealnej sytuacji spełniający co najmniej [zalecenia dotyczące rozmiaru serwera konfiguracji](https://aka.ms/asr-v2a-on-prem-components))</li><li>Konfiguracja maszyny: 8 wirtualnych procesorów CPU, 16 GB pamięci RAM, dysk twardy o rozmiarze 300 GB</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Pakiet redystrybucyjny Visual C++ dla Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Dostęp do platformy Azure przez Internet z tego serwera</li><li>Konto magazynu Azure</li><li>Dostęp administratora na serwerze</li><li>Minimalnie 100 GB wolnego miejsca na dysku (przy założeniu 1000 maszyn wirtualnych z średnio trzema dyskami na każdej z nich i profilowanych przez 30 dni)</li><li>Ustawienia poziomu statystyk programu VMware vCenter może być poziomu 1 lub nowszym</li><li>Zezwalaj na port vCenter (domyślnie 443): planista wdrażania usługi Site Recovery używa tego portu, aby nawiązać połączenie z serwerem vCenter/hostem ESXi</ul></ul>|
| Generowanie raportu | Komputer Windows lub Windows Server z programem Excel 2013 lub nowszym.<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Pakiet redystrybucyjny Visual C++ dla Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>[Oprogramowania VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli) jest wymagany tylko wtedy, gdy przekazujesz — opcja użytkownika w polecenie generowania raportu, aby pobrać najnowsze informacje o konfiguracji maszyn wirtualnych maszyn wirtualnych. Planista Depolyment łączy z programem vCenter server. Zezwalaj na port portu (domyślnie 443), aby połączyć się z serwerem vCenter vCenter.</li>|
| Uprawnienia użytkowników | Uprawnienia tylko do odczytu dla konta użytkownika używanego do uzyskiwania dostępu do serwera VMware vCenter/hosta VMware vSphere ESXi podczas profilowania |

> [!NOTE]
>
>Narzędzie może profilować tylko maszyny wirtualne z dyskami VMDK i RDM. Nie pozwala ono profilować maszyn wirtualnych z dyskami iSCSI ani NFS. Usługa Site Recovery obsługuje dyski iSCSI i NFS w przypadku serwerów VMware. Ponieważ jednak planista wdrażania nie znajduje się na gościu i profiluje tylko przy użyciu liczników wydajności programu vCenter, narzędzie nie ma wglądu w te typy dysków.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Pobieranie i wyodrębnianie narzędzia planisty wdrażania
1. Pobierz najnowszą wersję [planisty wdrożenia usługi Site Recovery](https://aka.ms/asr-deployment-planner).
Narzędzie jest spakowane w folderze ZIP. Bieżąca wersja narzędzia obsługuje tylko replikację z oprogramowania VMware na platformę Azure.

2. Skopiuj folder ZIP na serwer z systemem Windows, z którego chcesz uruchomić narzędzie.
Narzędzie można uruchomić z systemu Windows Server 2012 R2, jeśli serwer ma dostęp do sieci umożliwiający połączenie z serwerem vCenter/hostem vSphere ESXi przechowującym maszyny wirtualne do profilowania. Zalecamy jednak uruchamianie narzędzia na serwerze, którego konfiguracja sprzętowa spełnia [wytyczne dotyczące określania rozmiaru serwera konfiguracji](https://aka.ms/asr-v2a-on-prem-components). Jeśli wdrożono już lokalnie składniki usługi Site Recovery, uruchom narzędzie na serwerze konfiguracji.

    Na serwerze, na którym uruchamiasz narzędzie, zalecamy korzystanie z takiej samej konfiguracji sprzętu jak serwer konfiguracji (z wbudowanym serwerem przetwarzania). Dzięki takiej konfiguracji masz pewność, że osiągnięta przepływność zgłaszana przez narzędzie jest zgodna z rzeczywistą przepływnością, którą usługa Site Recovery może osiągnąć podczas replikacji. Obliczanie przepływności zależy od dostępnej przepustowości sieci na serwerze i konfiguracji sprzętu (np. procesora i magazynu) serwera. Jeśli uruchamiasz narzędzie z innego serwera, obliczana jest przepływność z tego serwera na platformę Azure. Ponadto ponieważ konfiguracja sprzętu serwera może różnić się od konfiguracji serwera konfiguracji, dane osiągniętej przepływności zgłaszanej przez narzędzie mogą być niedokładne.

3. Wyodrębnij folder ZIP.
Folder zawiera wiele plików i podfolderów. Plik wykonywalny nosi nazwę ASRDeploymentPlanner.exe i znajduje się w folderze nadrzędnym.

    Przykład: skopiuj plik zip na dysk E:\ i wyodrębnij go.
    E:\ASR Deployment Planner_v2.2.zip

    E:\ASR Deployment Planner_v2.2\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>Aktualizowanie planisty wdrażania do najnowszej wersji
Jeśli masz wcześniejszą wersję planisty wdrażania, wykonaj jedną z następujących czynności:
 * Jeśli najnowsza wersja nie zawiera poprawki profilowania i profilowanie jest już w toku w bieżącej wersji planisty, kontynuuj profilowanie.
 * Jeśli najnowsza wersja zawiera poprawkę profilowania, zalecamy zatrzymanie profilowania w bieżącej wersji, a następnie jego ponowne uruchomienie w nowej wersji.


 >[!NOTE]
 >
 >W przypadku uruchamiania profilowania w nowej wersji przekaż taką samą ścieżkę katalogu wyjściowego, aby narzędzie dołączało dane profilu do istniejących plików. Do wygenerowania raportu zostanie użyty kompletny zestaw profilowanych danych. Jeśli przekażesz inny katalog danych wyjściowych, zostaną utworzone nowe pliki, a stare profilowane dane nie będą używane podczas generowania raportu.
 >
 >Każda nowa wersja planisty wdrażania jest aktualizacją zbiorczą pliku ZIP. Nie musisz kopiować najnowszych plików do poprzedniego folderu. Można utworzyć nowy folder i użyć go.


## <a name="version-history"></a>Historia wersji
Najnowszą wersją narzędzia Planista wdrażania usługi Site Recovery jest wersja 2.2.
Informacje na temat poprawek, które zostały dodane w poszczególnych aktualizacjach, możesz znaleźć na stronie [Historia wersji narzędzia Planista wdrażania usługi Site Recovery](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx).

## <a name="next-steps"></a>Kolejne kroki
[Uruchamianie planisty wdrażania usługi Site Recovery](site-recovery-vmware-deployment-planner-run.md)
