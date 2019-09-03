---
title: Architektura replikacji platformy Azure do platformy Azure w Azure Site Recovery | Microsoft Docs
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas konfigurowania odzyskiwania po awarii między regionami platformy Azure dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: raynew
ms.openlocfilehash: d415f303976ae454cb99f07e8d6e15e338e24d7d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231455"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii z platformy Azure do platformy Azure


W tym artykule opisano architekturę, składniki i procesy używane podczas wdrażania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) . Po skonfigurowaniu odzyskiwania po awarii maszyny wirtualne platformy Azure są ciągle replikowane z programu do innego regionu docelowego. Jeśli wystąpi awaria, możesz przejść do trybu failover maszyn wirtualnych do regionu pomocniczego i uzyskać do nich dostęp. Po ponownym uruchomieniu wszystkiego można wrócić do trybu failover i kontynuować pracę w lokalizacji głównej.



## <a name="architectural-components"></a>Składniki architektury

Składniki związane z odzyskiwaniem po awarii dla maszyn wirtualnych platformy Azure zostały podsumowane w poniższej tabeli.

**Składnik** | **Wymagania**
--- | ---
**Maszyny wirtualne w regionie źródłowym** | Jedna z więcej maszyn wirtualnych platformy Azure w [obsługiwanym regionie źródłowym](azure-to-azure-support-matrix.md#region-support).<br/><br/> Na maszynach wirtualnych może działać dowolny [obsługiwany system operacyjny](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Źródłowy magazyn maszyny wirtualnej** | Maszynami wirtualnymi platformy Azure można zarządzać lub korzystać z dysków niezarządzanych na kontach magazynu.<br/><br/>[Poznaj informacje o](azure-to-azure-support-matrix.md#replicated-machines---storage) obsługiwanej usłudze Azure Storage.
**Źródłowe sieci VMNETWORK** | Maszyny wirtualne mogą znajdować się w jednej lub większej liczbie podsieci w sieci wirtualnej (VNet) w regionie źródłowym. [Dowiedz się więcej](azure-to-azure-support-matrix.md#replicated-machines---networking) o wymaganiach sieciowych.
**Konto magazynu pamięci podręcznej** | Potrzebujesz konta magazynu pamięci podręcznej w sieci źródłowej. Podczas replikacji zmiany maszyny wirtualnej są przechowywane w pamięci podręcznej przed wysłaniem do magazynu docelowego.  Konta magazynu pamięci podręcznej muszą być standardowe.<br/><br/> Użycie pamięci podręcznej zapewnia minimalny wpływ na aplikacje produkcyjne, które są uruchomione na maszynie wirtualnej.<br/><br/> [Dowiedz się więcej](azure-to-azure-support-matrix.md#cache-storage) o wymaganiach dotyczących magazynu pamięci podręcznej. 
**Zasoby docelowe** | Zasoby docelowe są używane podczas replikacji i w przypadku przejścia w tryb failover. Site Recovery można skonfigurować zasób docelowy domyślnie lub można je utworzyć lub dostosować.<br/><br/> W regionie docelowym Sprawdź, czy można tworzyć maszyny wirtualne i czy subskrypcja ma wystarczającą ilość zasobów, aby obsługiwać rozmiary maszyn wirtualnych, które będą potrzebne w regionie docelowym. 

![Replikacja źródłowa i docelowa](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Zasoby docelowe

Po włączeniu replikacji dla maszyny wirtualnej Site Recovery zapewnia opcję automatycznego tworzenia zasobów docelowych. 

**Zasób docelowy** | **Ustawienie domyślne**
--- | ---
**Subskrypcja docelowa** | Taka sama jak w przypadku subskrypcji źródłowej.
**Docelowa Grupa zasobów** | Grupa zasobów, do której maszyny wirtualne należą po przejściu w tryb failover.<br/><br/> Może ona znajdować się w dowolnym regionie świadczenia usługi Azure, z wyjątkiem regionu źródłowego.<br/><br/> Site Recovery tworzy nową grupę zasobów w regionie docelowym z sufiksem "ASR".<br/><br/>
**Docelowa sieć wirtualna** | Sieć wirtualna (VNet), w której znajdują się zreplikowane maszyny wirtualne, po przejściu w tryb failover. Tworzone jest mapowanie sieci między źródłową i docelową siecią wirtualną i odwrotnie.<br/><br/> Site Recovery utworzyć nową sieć wirtualną i podsieć z sufiksem "ASR".
**Docelowe konto magazynu** |  Jeśli maszyna wirtualna nie używa dysku zarządzanego, jest to konto magazynu, do którego są replikowane dane.<br/><br/> Site Recovery tworzy nowe konto magazynu w regionie docelowym w celu dublowania źródłowego konta magazynu.
**Dyski zarządzane repliki** | Jeśli maszyna wirtualna używa dysku zarządzanego, są to dyski zarządzane, do których są replikowane dane.<br/><br/> Site Recovery tworzy dyski zarządzane repliki w regionie magazynu w celu dublowania źródła.
**Docelowe zestawy dostępności** |  Zestaw dostępności, w którym replikowane maszyny wirtualne znajdują się po przejściu w tryb failover.<br/><br/> Site Recovery tworzy zestaw dostępności w regionie docelowym z sufiksem "ASR" dla maszyn wirtualnych, które znajdują się w zestawie dostępności w lokalizacji źródłowej. Jeśli zestaw dostępności istnieje, jest używany i nowy nie zostanie utworzony.
**Docelowe strefy dostępności** | Jeśli region docelowy obsługuje strefy dostępności, Site Recovery przypisuje ten sam numer strefy, który jest używany w regionie źródłowym.

### <a name="managing-target-resources"></a>Zarządzanie zasobami docelowymi

Zasobami docelowymi można zarządzać w następujący sposób:

- Ustawienia docelowe można modyfikować podczas włączania replikacji.
- Ustawienia docelowe można modyfikować po już działaniu replikacji. Wyjątek jest typem dostępności (pojedynczym wystąpieniem, zestawem lub strefą). Aby zmienić to ustawienie, należy wyłączyć replikację, zmodyfikować ustawienie, a następnie włączyć je ponownie.



## <a name="replication-policy"></a>Zasady replikacji 

Po włączeniu replikacji maszyny wirtualnej platformy Azure domyślnie Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi podsumowywanymi w tabeli.

**Ustawienie zasad** | **Szczegóły** | **Domyślne**
--- | --- | ---
**Przechowywanie punktów odzyskiwania** | Określa, jak długo Site Recovery zachowuje punkty odzyskiwania | 24 godz.
**Częstotliwość migawek spójnych na poziomie aplikacji** | Jak często Site Recovery pobiera migawkę spójną na poziomie aplikacji. | Co cztery godziny

### <a name="managing-replication-policies"></a>Zarządzanie zasadami replikacji

Domyślnymi ustawieniami zasad replikacji można zarządzać i modyfikować w następujący sposób:
- Ustawienia można modyfikować po włączeniu replikacji.
- W każdej chwili można utworzyć zasady replikacji, a następnie zastosować je po włączeniu replikacji.

### <a name="multi-vm-consistency"></a>Spójność między MASZYNami wirtualnymi

Jeśli chcesz, aby maszyny wirtualne były replikowane ze sobą i w trybie failover współużytkowane punkty odzyskiwania spójne z awarią i aplikacjami, możesz zebrać je razem w grupie replikacji. Spójność wielu maszyn wirtualnych wpływa na wydajność obciążeń i powinna być używana tylko w przypadku maszyn wirtualnych korzystających z obciążeń, które wymagają spójności na wszystkich komputerach. 



## <a name="snapshots-and-recovery-points"></a>Migawki i punkty odzyskiwania

Punkty odzyskiwania są tworzone na podstawie migawek dysków maszyny wirtualnej wykonanych w określonym punkcie w czasie. Po przełączeniu maszyny wirtualnej w tryb failover należy użyć punktu odzyskiwania, aby przywrócić maszynę wirtualną w lokalizacji docelowej.

W przypadku przełączenia w tryb failover zwykle chcemy upewnić się, że maszyna wirtualna nie ma uszkodzenia lub utracie danych, oraz że dane maszyny wirtualnej są spójne dla systemu operacyjnego i aplikacji uruchamianych na maszynie wirtualnej. Jest to zależne od typu wykonanych migawek.

Site Recovery wykonuje migawki w następujący sposób:

1. W przypadku wybrania dla nich częstotliwości w Site Recovery są domyślnie spójne migawki danych i migawki spójne z aplikacjami.
2. Punkty odzyskiwania są tworzone na podstawie migawek i przechowywane zgodnie z ustawieniami przechowywania w zasadach replikacji.

### <a name="consistency"></a>Spójność

W poniższej tabeli objaśniono różne typy spójności.

### <a name="crash-consistent"></a>Spójny na poziomie awarii

**Opis** | **Szczegóły** | **Zalecenie**
--- | --- | ---
Migawka spójna pod kątem awarii przechwytuje dane znajdujące się na dysku podczas tworzenia migawki. Nie zawiera żadnych elementów w pamięci.<br/><br/> Zawiera odpowiednik danych na dysku, które mogą być obecne, jeśli maszyna wirtualna uległa awarii lub przewód zasilający został pobrany z serwera na chwilę, gdy migawka została wykonana.<br/><br/> Spójna awaria nie gwarantuje spójności danych dla systemu operacyjnego lub aplikacji na maszynie wirtualnej. | Site Recovery domyślnie tworzy punkty odzyskiwania spójne z awarią co pięć minut. Nie można zmodyfikować tego ustawienia.<br/><br/>  | Obecnie większość aplikacji może odzyskać z punktów spójnych z awarią.<br/><br/> Punkty odzyskiwania spójne z awarią są zwykle wystarczające do replikacji systemów operacyjnych i aplikacji, takich jak serwery DHCP i serwery wydruku.

### <a name="app-consistent"></a>Spójna na poziomie aplikacji

**Opis** | **Szczegóły** | **Zalecenie**
--- | --- | ---
Punkty odzyskiwania spójne z aplikacjami są tworzone na podstawie migawek spójnych na poziomie aplikacji.<br/><br/> Migawka spójna na poziomie aplikacji zawiera wszystkie informacje w migawce spójnej na poziomie awarii oraz wszystkie dane w pamięci i transakcjach w toku. | Migawki spójne z aplikacjami używają Usługa kopiowania woluminów w tle (VSS):<br/><br/>   1) po zainicjowaniu migawki usługa VSS wykonuje na woluminie operację kopiowania na zapis (KROWy).<br/><br/>   2) przed wykonaniem KROWy usługa VSS informuje każdą aplikację na komputerze, że musi ona opróżnić dane rezydentne pamięci na dysk.<br/><br/>   3) usługa VSS umożliwia korzystanie z kopii zapasowej/odzyskiwania po awarii (w tym przypadku Site Recovery) w celu odczytania danych migawki i przejścia. | Migawki spójne z aplikacjami są wykonywane zgodnie z określoną częstotliwością. Ta częstotliwość powinna być zawsze mniejsza niż ustawiona dla zachowywania punktów odzyskiwania. Jeśli na przykład zachowasz punkty odzyskiwania przy użyciu domyślnego ustawienia przez 24 godziny, należy ustawić częstotliwość krótszą niż 24 godziny.<br/><br/>Są one bardziej skomplikowane i trwają dłużej niż w przypadku migawek spójnych na poziomie awarii.<br/><br/> Wpływają one na wydajność aplikacji uruchomionych na maszynie wirtualnej z włączoną obsługą replikacji. 

## <a name="replication-process"></a>Proces replikacji

Po włączeniu replikacji dla maszyny wirtualnej platformy Azure następuje:

1. Rozszerzenie usługi mobilności Site Recovery jest automatycznie instalowane na maszynie wirtualnej.
2. Rozszerzenie rejestruje maszynę wirtualną przy użyciu Site Recovery.
3. Replikacja ciągła rozpoczyna się dla maszyny wirtualnej.  Zapisy dysku są natychmiast transferowane do konta magazynu pamięci podręcznej w lokalizacji źródłowej.
4. Site Recovery przetwarza dane w pamięci podręcznej i wysyła je do docelowego konta magazynu lub do dysków zarządzanych repliki.
5. Po przetworzeniu danych punkty odzyskiwania spójne z awarią są generowane co pięć minut. Punkty odzyskiwania spójne z aplikacjami są generowane zgodnie z ustawieniem określonym w zasadach replikacji.

![Włączanie procesu replikacji, krok 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Proces replikacji**

## <a name="connectivity-requirements"></a>Wymagania dotyczące łączności

 Replikowanie maszyn wirtualnych platformy Azure wymaga łączności wychodzącej. Site Recovery nigdy nie potrzebuje łączności przychodzącej z maszyną wirtualną. 

### <a name="outbound-connectivity-urls"></a>Łączność wychodząca (URL)

Jeśli dostęp wychodzący dla maszyn wirtualnych jest kontrolowany za pomocą adresów URL, Zezwól na te adresy URL.

| **Adres URL** | **Szczegóły** |
| ------- | ----------- |
| *.blob.core.windows.net | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| login.microsoftonline.com | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| *.servicebus.windows.net | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Aby sterować łącznością wychodzącą dla maszyn wirtualnych przy użyciu adresów IP, Zezwól na te adresy.
Należy pamiętać, że szczegółowe informacje o wymaganiach dotyczących łączności sieciowej można znaleźć w temacie [Obsługa sieci — oficjalny dokument](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) 

#### <a name="source-region-rules"></a>Reguły regionu źródłowego

**Reguły** |  **Szczegóły** | **Tag usługi**
--- | --- | --- 
Zezwalaj na ruch wychodzący HTTPS: port 443 | Zezwalaj na zakresy, które odpowiadają kontom magazynu w regionie źródłowym | Chowan. \<nazwa regionu >.
Zezwalaj na ruch wychodzący HTTPS: port 443 | Zezwalaj na zakresy, które odpowiadają Azure Active Directory (Azure AD).<br/><br/> Jeśli w przyszłości zostaną dodane adresy usługi Azure AD, musisz utworzyć nowe reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).  | AzureActiveDirectory
Zezwalaj na ruch wychodzący HTTPS: port 443 | Zezwalaj na dostęp do [Site Recovery punktów końcowych](https://aka.ms/site-recovery-public-ips) , które odpowiadają lokalizacji docelowej. 

#### <a name="target-region-rules"></a>Reguły regionu docelowego

**Reguły** |  **Szczegóły** | **Tag usługi**
--- | --- | --- 
Zezwalaj na ruch wychodzący HTTPS: port 443 | Zezwalaj na zakresy, które odpowiadają kontom magazynu w regionie docelowym. | Chowan. \<nazwa regionu >.
Zezwalaj na ruch wychodzący HTTPS: port 443 | Zezwalaj na zakresy odpowiadające usłudze Azure AD.<br/><br/> Jeśli w przyszłości zostaną dodane adresy usługi Azure AD, musisz utworzyć nowe reguły sieciowej grupy zabezpieczeń.  | AzureActiveDirectory
Zezwalaj na ruch wychodzący HTTPS: port 443 | Zezwalaj na dostęp do [Site Recovery punktów końcowych](https://aka.ms/site-recovery-public-ips) odpowiadających lokalizacji źródłowej. 


#### <a name="control-access-with-nsg-rules"></a>Kontrola dostępu przy użyciu reguł sieciowej grupy zabezpieczeń

W przypadku kontrolowania łączności maszyn wirtualnych przez filtrowanie ruchu sieciowego do i z sieci lub podsieci platformy Azure przy użyciu [reguł sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview)należy zwrócić uwagę na następujące wymagania:

- Reguły sieciowej grupy zabezpieczeń dla źródłowego regionu platformy Azure powinny zezwalać na dostęp wychodzący dla ruchu związanego z replikacją.
- Zalecamy utworzenie reguł w środowisku testowym przed wprowadzeniem ich do środowiska produkcyjnego.
- Używaj [tagów usługi](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) zamiast zezwalać na poszczególne adresy IP.
    - Tagi usług reprezentują grupę prefiksów adresów IP zebranych razem, aby zminimalizować złożoność podczas tworzenia reguł zabezpieczeń.
    - Firma Microsoft automatycznie aktualizuje Tagi usług w czasie. 
 
Dowiedz się więcej o [łączności](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) wychodzącej dla Site Recovery i [kontrolowania łączności z usługą sieciowych grup zabezpieczeń](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Łączność dla spójności dotyczącej kilku maszyn wirtualnych

Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004.
- Upewnij się, że żadne urządzenie zapory nie blokuje wewnętrznej komunikacji między maszynami wirtualnymi za pośrednictwem portu 20004.
- Jeśli do grupy replikacji chcesz dodać maszyny wirtualne z systemem Linux, musisz ręcznie otworzyć port 20004 dla ruchu wychodzącego zgodnie ze wskazówkami dla konkretnej wersji systemu Linux.




## <a name="failover-process"></a>Proces trybu failover

Po zainicjowaniu trybu failover maszyny wirtualne są tworzone w docelowej grupie zasobów, docelowej sieci wirtualnej, podsieci docelowej i w docelowym zestawie dostępności. Podczas pracy w trybie failover można użyć dowolnego punktu odzyskiwania.

![Proces trybu failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Następne kroki

[Szybka replikacja](azure-to-azure-quickstart.md) maszyny wirtualnej platformy Azure do regionu pomocniczego.
