---
title: Informacje o kopii zapasowej maszyny wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak usługa Azure Backup wykonuje kopie zapasowe maszyn wirtualnych platformy Azure oraz jak postępować zgodnie z najlepszymi rozwiązaniami.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: e22c4c24e83be0f89b306eed0eb1d80bdd9387e1
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747201"
---
# <a name="an-overview-of-azure-vm-backup"></a>Omówienie kopii zapasowej maszyny wirtualnej platformy Azure

W tym artykule opisano, jak [usługa Azure Backup](backup-introduction-to-azure-backup.md) wykonuje kopie zapasowe maszyn wirtualnych platformy Azure.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

Oto jak Azure Backup wykonuje kopię zapasową maszyn wirtualnych platformy Azure:

1. W przypadku maszyn wirtualnych platformy Azure wybranych do utworzenia kopii zapasowej Azure Backup uruchamia zadanie tworzenia kopii zapasowej zgodnie z określonym harmonogramem tworzenia kopii zapasowych.
1. Podczas pierwszej kopii zapasowej, rozszerzenie kopii zapasowej jest instalowane na maszynie wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych z systemem Windows jest zainstalowane rozszerzenie _VMSnapshot_ .
    - W przypadku maszyn wirtualnych z systemem Linux jest zainstalowane rozszerzenie _VMSnapshotLinux_ .
1. W przypadku maszyn wirtualnych z systemem Windows, które są uruchomione, współpracują z systemem Windows Usługa kopiowania woluminów w tle (VSS) w celu utworzenia migawki maszyny wirtualnej spójnej na poziomie aplikacji.
    - Domyślnie kopia zapasowa pobiera pełne kopie zapasowe usługi VSS.
    - Jeśli kopia zapasowa nie może pobrać migawki spójnej na poziomie aplikacji, zostanie przeprowadzona spójna z plikiem migawka magazynu (ponieważ nie ma żadnych zapisów aplikacji podczas zatrzymania maszyny wirtualnej).
1. W przypadku maszyn wirtualnych z systemem Linux kopia zapasowa pobiera kopię zapasową spójną na poziomie plików. W przypadku migawek spójnych z aplikacjami należy ręcznie dostosować skrypty poprzedzające i końcowe.
1. Gdy kopia zapasowa pobiera migawkę, przesyła dane do magazynu.
    - Kopia zapasowa jest zoptymalizowana przez utworzenie kopii zapasowej każdego dysku maszyny wirtualnej równolegle.
    - Dla każdego dysku, którego kopia zapasowa jest tworzona, Azure Backup odczytuje bloki na dysku i identyfikuje i transferuje tylko te bloki danych, które uległy zmianie (różnica) od czasu utworzenia poprzedniej kopii zapasowej.
    - Dane migawki mogą nie być od razu kopiowane do magazynu. W godzinach szczytu może upłynąć kilka godzin. Łączny czas wykonywania kopii zapasowej maszyny wirtualnej będzie krótszy niż 24 godziny dla codziennych zasad tworzenia kopii zapasowych.
1. Zmiany wprowadzone do maszyny wirtualnej z systemem Windows po włączeniu Azure Backup są następujące:
    - Pakiet redystrybucyjny Microsoft Visual C++ 2013 (x64) — 12.0.40660 jest zainstalowany na maszynie wirtualnej
    - Typ uruchamiania usługi kopiowania woluminów w tle (VSS) zmieniony na automatyczny przy użyciu instrukcji Manual
    - Usługa IaaSVmProvider systemu Windows jest dodawana

1. Po zakończeniu transferu danych migawka zostanie usunięta i zostanie utworzony punkt odzyskiwania.

![Architektura kopii zapasowej maszyny wirtualnej platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Szyfrowanie kopii zapasowych maszyny wirtualnej platformy Azure

Podczas tworzenia kopii zapasowych maszyn wirtualnych platformy Azure z Azure Backup maszyny wirtualne są szyfrowane przy użyciu szyfrowanie usługi Storage (SSE). Azure Backup może również tworzyć kopie zapasowe maszyn wirtualnych platformy Azure, które są szyfrowane przy użyciu Azure Disk Encryption.

**Szyfrowanie** | **Szczegóły** | **Pomoc techniczna**
--- | --- | ---
**Usługa Azure Disk Encryption** | Azure Disk Encryption szyfruje zarówno dyski systemu operacyjnego, jak i danych dla maszyn wirtualnych platformy Azure.<br/><br/> Azure Disk Encryption integruje się z kluczami szyfrowania funkcji BitLocker (BEKs), które są chronione w magazynie kluczy jako wpisy tajne. Azure Disk Encryption integruje się również z kluczami szyfrowania klucza Azure Key Vault (KEKs). | Azure Backup obsługuje tworzenie kopii zapasowych zarządzanych i niezarządzanych maszyn wirtualnych platformy Azure szyfrowanych tylko za pomocą BEKs lub z BEKs razem z KEKs.<br/><br/> BEKs i KEKs są archiwizowane i szyfrowane.<br/><br/> Ponieważ kopie zapasowe KEKs i BEKs są tworzone, użytkownicy z niezbędnymi uprawnieniami mogą przywrócić klucze i wpisy tajne z powrotem do magazynu kluczy, jeśli jest to konieczne. Ci użytkownicy mogą również odzyskać zaszyfrowaną maszynę wirtualną.<br/><br/> Zaszyfrowane klucze i wpisy tajne nie mogą być odczytywane przez nieautoryzowanych użytkowników lub platformę Azure.
**SSE** | W przypadku używania instrukcji SSE usługa Azure Storage zapewnia szyfrowanie w spoczynku przez automatyczne szyfrowanie danych przed ich zapisaniem. Usługa Azure Storage odszyfrowuje również dane przed ich pobraniem. | Azure Backup używa instrukcji SSE do szyfrowania w czasie spoczynku maszyn wirtualnych platformy Azure.

W przypadku zarządzanych i niezarządzanych maszyn wirtualnych platformy Azure usługa Backup obsługuje zarówno maszyny wirtualne zaszyfrowane za pomocą BEKs, jak i maszyny wirtualne zaszyfrowane za pomocą usługi BEKs oraz KEKs.

Kopie zapasowe BEKs (Secret) i KEKs (klucze) są szyfrowane. Mogą być odczytywane i używane tylko wtedy, gdy są przywracane do magazynu kluczy przez autoryzowanych użytkowników. Żaden z nieautoryzowanych użytkowników lub platforma Azure mogą odczytywać lub używać kopii zapasowych kluczy lub wpisów tajnych.

BEKs również kopie zapasowe. Dlatego jeśli BEKs zostaną utracone, autoryzowani użytkownicy mogą przywrócić BEKs do magazynu kluczy i odzyskać zaszyfrowane maszyny wirtualne. Tylko użytkownicy z wymaganym poziomem uprawnień mogą tworzyć kopie zapasowe zaszyfrowanych maszyn wirtualnych lub kluczy i wpisów tajnych oraz przywracać je.

## <a name="snapshot-creation"></a>Tworzenie migawki

Azure Backup wykonuje migawki zgodnie z harmonogramem tworzenia kopii zapasowych.

- **Maszyny wirtualne z systemem Windows:** W przypadku maszyn wirtualnych z systemem Windows usługa tworzenia kopii zapasowych koordynuje się z usługą VSS w celu utworzenia migawki dysków maszyn wirtualnych spójnej na poziomie aplikacji.

  - Domyślnie usługa Azure Backup wykonuje pełne kopie zapasowe w usłudze VSS. [Dowiedz się więcej](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Aby zmienić to ustawienie, tak aby Azure Backup pobiera kopie zapasowe w usłudze VSS, należy ustawić następujący klucz rejestru w wierszu polecenia:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent"/v USEVSSCOPYBACKUP/t REG_SZ/d TRUE/f**

- **Maszyny wirtualne z systemem Linux:** Aby tworzyć migawki maszyn wirtualnych z systemem Linux spójne z aplikacjami, należy użyć skryptów przedskryptowych i skryptów po skrypcie dla systemu Linux do pisania własnych skryptów niestandardowych w celu zapewnienia spójności.

  - Azure Backup wywołuje tylko skrypty poprzedzające lub gotowe.
  - Jeśli skrypty wstępne i po wykonaniu zostały wykonane pomyślnie, Azure Backup oznacza punkt odzyskiwania jako spójny dla aplikacji. Jeśli jednak używasz skryptów niestandardowych, jesteś w końcu odpowiedzialny za spójność aplikacji.
  - [Dowiedz się więcej](backup-azure-linux-app-consistent.md) o konfigurowaniu skryptów.

### <a name="snapshot-consistency"></a>Spójność migawek

W poniższej tabeli objaśniono różne typy spójności migawek:

**Zdjęcie** | **Szczegóły** | **Przywrócenia** | **Zagadnienie**
--- | --- | --- | ---
**Spójna na poziomie aplikacji** | Kopie zapasowe spójne z aplikacjami przechwytują zawartość pamięci i oczekujące operacje we/wy. Migawki spójne z aplikacjami używają składnika zapisywania usługi VSS (lub skryptów pre/post dla systemu Linux), aby zapewnić spójność danych aplikacji przed wystąpieniem kopii zapasowej. | Podczas odzyskiwania maszyny wirtualnej za pomocą migawki spójnej na poziomie aplikacji maszyna wirtualna jest uruchamiana. Nie występują uszkodzenia ani utrata danych. Aplikacje są uruchamiane w spójnym stanie. | System Windows: wszystkie składniki zapisywania usługi VSS zostały pomyślnie zakończone<br/><br/> Linux: skrypty poprzedzające i końcowe zostały skonfigurowane i zakończyły się powodzeniem
**Spójny system plików** | Spójne kopie zapasowe systemu plików zapewniają spójność, pobierając migawkę wszystkich plików w tym samym czasie.<br/><br/> | Podczas odzyskiwania maszyny wirtualnej za pomocą migawki spójnej z systemem plików, maszyna wirtualna jest uruchamiana. Nie występują uszkodzenia ani utrata danych. Aplikacje muszą implementować własny mechanizm naprawy, aby upewnić się, że przywrócone dane są spójne. | System Windows: niepowodzenie niektórych składników zapisywania usługi VSS <br/><br/> Linux: wartość domyślna (Jeśli skrypty pre/post nie są skonfigurowane lub zakończyły się niepowodzeniem)
**Spójny na poziomie awarii** | Migawki spójne z awarią są zwykle wykonywane, gdy maszyna wirtualna platformy Azure jest zamykana w momencie tworzenia kopii zapasowej. Przechwytywane i tworzone są kopie zapasowe tylko danych istniejących na dysku w momencie tworzenia kopii zapasowej.<br/><br/> Punkt odzyskiwania spójny na poziomie awarii nie gwarantuje spójności danych dla systemu operacyjnego lub aplikacji. | Mimo że nie ma żadnych gwarancji, maszyna wirtualna zazwyczaj wykonuje rozruch, a następnie uruchamia sprawdzenie dysku, aby naprawić błędy uszkodzeń. Wszelkie operacje dotyczące danych w pamięci lub zapisu, które nie zostały przekazane do dysku przed utratą awarii. Aplikacje implementują własne weryfikacje danych. Na przykład aplikacja bazy danych może użyć dziennika transakcji do weryfikacji. Jeśli w dzienniku transakcji znajdują się wpisy, które nie znajdują się w bazie danych, oprogramowanie bazy danych przenosi transakcje z powrotem do momentu spójności danych. | Maszyna wirtualna jest w stanie zamykania

## <a name="backup-and-restore-considerations"></a>Uwagi dotyczące tworzenia kopii zapasowych i przywracania

**Zagadnienie** | **Szczegóły**
--- | ---
**3,5** | Tworzenie kopii zapasowych dysków maszyny wirtualnej jest równoległe. Na przykład jeśli maszyna wirtualna ma cztery dyski, usługa tworzenia kopii zapasowych próbuje wykonać kopię zapasową wszystkich czterech dysków równolegle. Kopia zapasowa jest przyrostowa (dotyczy tylko zmienionych danych).
**Harmonogram** |  Aby zmniejszyć ruch kopii zapasowych, wykonaj kopię zapasową różnych maszyn wirtualnych w różnych porach dnia i upewnij się, że czasy nie nakładają się na siebie. Tworzenie kopii zapasowych maszyn wirtualnych w tym samym czasie powoduje wyczyszczenie ruchu.
**Przygotowywanie kopii zapasowych** | Należy pamiętać o czasie potrzebnym do przygotowania kopii zapasowej. Czas przygotowania obejmuje zainstalowanie lub zaktualizowanie rozszerzenia kopii zapasowej oraz wyzwolenie migawki zgodnie z harmonogramem tworzenia kopii zapasowych.
**Transfer danych** | Należy wziąć pod uwagę czas wymagany do Azure Backup identyfikować przyrostowe zmiany z poprzedniej kopii zapasowej.<br/><br/> W przyrostowej kopii zapasowej Azure Backup określa zmiany, obliczając sumę kontrolną bloku. Jeśli blok zostanie zmieniony, zostanie oznaczony do przeniesienia do magazynu. Usługa analizuje zidentyfikowane bloki i próbuje bardziej zminimalizować ilość danych do przesłania. Po dokonaniu oceny wszystkich zmienionych bloków Azure Backup przenosi zmiany do magazynu.<br/><br/> Podczas tworzenia migawki i kopiowania jej do magazynu może wystąpić opóźnienie.<br/><br/> W godzinach szczytu przetwarzanie kopii zapasowych może trwać do ośmiu godzin. Czas wykonywania kopii zapasowej maszyny wirtualnej będzie krótszy niż 24 godziny na codzienne wykonywanie kopii zapasowej.
**Początkowa kopia zapasowa** | Mimo że łączny czas wykonywania kopii zapasowych przyrostowych jest krótszy niż 24 godziny, ale może to nie być przypadek dla pierwszej kopii zapasowej. Czas wymagany do początkowej kopii zapasowej będzie zależeć od rozmiaru danych i czasu przetwarzania kopii zapasowej.
**Przywróć kolejkę** | Azure Backup przetwarza zadania przywracania z wielu kont magazynu w tym samym czasie i umieszcza żądania przywracania w kolejce.
**Przywróć kopię** | Podczas przywracania dane są kopiowane z magazynu do konta magazynu.<br/><br/> Łączny czas przywracania zależy od operacji we/wy na sekundę (IOPS) i przepływności konta magazynu.<br/><br/> Aby skrócić czas kopiowania, wybierz konto magazynu, które nie jest załadowane z innymi zapisami i odczytami aplikacji.

### <a name="backup-performance"></a>Wydajność kopii zapasowej

Te typowe scenariusze mogą mieć wpływ na łączny czas wykonywania kopii zapasowej:

- **Dodawanie nowego dysku do chronionej maszyny wirtualnej platformy Azure:** Jeśli maszyna wirtualna jest w trakcie przyrostowej kopii zapasowej i zostanie dodany nowy dysk, zwiększy się czas tworzenia kopii zapasowej. Łączny czas wykonywania kopii zapasowej może trwać ponad 24 godziny z powodu początkowej replikacji nowego dysku wraz z replikacją różnicową istniejących dysków.
- **Pofragmentowane dyski:** Operacje tworzenia kopii zapasowych są szybsze, gdy zmiany dysku są ciągłe. Jeśli zmiany zostaną rozłożone i pofragmentowane na dysku, kopia zapasowa będzie wolniejsza.
- Zmiany **dysku:** W przypadku dysków chronionych, które przechodzą przyrostową kopię zapasową, można wykonać codzienne zmiany o ponad 200 GB, a wykonywanie kopii zapasowej może trwać długo (więcej niż osiem godzin).
- **Wersje kopii zapasowej:** Najnowsza wersja kopii zapasowej (znana jako wersja natychmiastowego przywracania) używa bardziej zoptymalizowanego procesu niż porównanie sum kontrolnych w celu identyfikowania zmian. Ale jeśli używasz natychmiastowego przywracania i usunięto migawkę kopii zapasowej, kopia zapasowa przełączy się do porównania sum kontrolnych. W takim przypadku operacja tworzenia kopii zapasowej będzie przekroczyć 24 godziny (lub zakończyć się niepowodzeniem).

## <a name="best-practices"></a>Najlepsze praktyki

Podczas konfigurowania kopii zapasowych maszyn wirtualnych Sugerujemy następujące rozwiązania:

- Zmodyfikuj domyślne czasy harmonogramu, które są ustawiane w ramach zasad. Na przykład jeśli domyślny czas w zasadach wynosi 12:00, Zwiększ czas trwania o kilka minut, aby zasoby były optymalnie używane.
- Jeśli przywracasz maszyny wirtualne z jednego magazynu, zdecydowanie zalecamy użycie różnych [kont magazynu ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) , aby upewnić się, że docelowe konto magazynu nie zostanie ograniczone. Na przykład każda maszyna wirtualna musi mieć inne konto magazynu. Na przykład jeśli zostaną przywrócone 10 maszyn wirtualnych, użyj 10 różnych kont magazynu.
- W przypadku tworzenia kopii zapasowych maszyn wirtualnych korzystających z usługi Premium Storage z natychmiastowym przywróceniem zaleca się alokowanie *50%* wolnego miejsca w łącznym przydzielonym miejscu do magazynowania, które jest wymagane **tylko** dla pierwszej kopii zapasowej. Ilość wolnego miejsca na 50% nie jest wymagana w przypadku kopii zapasowych po wykonaniu pierwszej kopii zapasowej
- Przywracanie z warstwy magazynowania ogólnego przeznaczenia V1 (migawka) zostanie ukończone w ciągu kilku minut, ponieważ migawka znajduje się na tym samym koncie magazynu. Przywrócenie z warstwy magazynu ogólnego przeznaczenia w wersji 2 (magazyn) może zająć kilka godzin. W przypadkach, gdy dane są dostępne w magazynie ogólnego przeznaczenia w wersji 1, zalecamy użycie funkcji [natychmiastowego przywracania](backup-instant-restore-capability.md) , aby przyspieszyć przywracanie. (Jeśli dane muszą zostać przywrócone z magazynu, zajmie więcej czasu).
- Limit liczby dysków na konto magazynu jest określany względem tego, w jakim stopniu uzyskuje się dostęp do dysków przez aplikacje działające na maszynie wirtualnej infrastruktura jako usługa (IaaS). Ogólnie rzecz biorąc, jeśli na jednym koncie magazynu znajdują się od 5 do 10 dysków lub więcej, należy zrównoważyć obciążenie przez przeniesienie niektórych dysków do oddzielnych kont magazynu.

## <a name="backup-costs"></a>Koszty kopii zapasowych

Maszyny wirtualne platformy Azure z kopiami zapasowymi Azure Backup podlegają [Azure Backup cenniku](https://azure.microsoft.com/pricing/details/backup/).

Rozliczanie nie rozpocznie się, dopóki nie zostanie ukończona pierwsza kopia zapasowa. W tym momencie rozpocznie się rozliczanie zarówno dla magazynu, jak i chronionych maszyn wirtualnych. Rozliczanie jest kontynuowane, o ile wszystkie dane kopii zapasowej maszyny wirtualnej są przechowywane w magazynie. Jeśli zatrzymasz ochronę dla maszyny wirtualnej, ale dane kopii zapasowej dla maszyny wirtualnej istnieją w magazynie, rozliczenia będą kontynuowane.

Rozliczanie dla określonej maszyny wirtualnej zatrzymuje się tylko wtedy, gdy ochrona została zatrzymana i wszystkie dane kopii zapasowej zostaną usunięte. Po zatrzymaniu ochrony, gdy nie ma aktywnych zadań kopii zapasowej, rozmiar ostatniej pomyślnej kopii zapasowej maszyny wirtualnej to rozmiar chronionego wystąpienia użyty dla rachunku miesięcznego.

Obliczanie rozmiaru chronionego wystąpienia jest zależne od *rzeczywistego* rozmiaru maszyny wirtualnej. Rozmiar maszyny wirtualnej to suma wszystkich danych w maszynie wirtualnej, z wyłączeniem magazynu tymczasowego. Cennik jest oparty na rzeczywistych danych przechowywanych na dyskach danych, a nie na maksymalnym obsługiwanym rozmiarze dla każdego dysku danych dołączonego do maszyny wirtualnej.

Podobnie opłata za magazyn kopii zapasowych zależy od ilości danych przechowywanych w Azure Backup, która jest sumą rzeczywistych danych w poszczególnych punktach odzyskiwania.

Na przykład należy utworzyć maszynę wirtualną o rozmiarze a2, która ma dwa dodatkowe dyski z danymi o maksymalnym rozmiarze wynoszącym 4 TB. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

**3,5** | **Maksymalny rozmiar** | **Rzeczywiste dane obecne**
--- | --- | ---
Dysk systemu operacyjnego | 4095 GB | 17 GB
Dysk lokalny/tymczasowy | 135 GB | 5 GB (nie uwzględniono w kopii zapasowej)
Dysk danych 1 | 4095 GB | 30 GB
Dysk danych 2 | 4095 GB | 0 GB

Rzeczywistą wielkością maszyny wirtualnej w tym przypadku jest 17 GB + 30 GB + 0 GB = 47 GB. Ten rozmiar chronionego wystąpienia (47 GB) stanowi podstawę dla rachunku miesięcznego. Wraz ze wzrostem ilości danych w maszynie wirtualnej rozmiar chronionego wystąpienia używany do zmiany rozliczeń jest zgodny.

<a name="limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb"></a>

## <a name="public-preview-backup-of-vm-with-disk-sizes-up-to-30-tb"></a>Publiczna wersja zapoznawcza: Tworzenie kopii zapasowej maszyny wirtualnej z rozmiarem dysku do 30 TB

Azure Backup teraz obsługuje publiczną wersję zapoznawczą większych i bardziej zaawansowanych [Managed disks platformy Azure](https://azure.microsoft.com/blog/larger-more-powerful-managed-disks-for-azure-virtual-machines/) o rozmiarze do 30 TB. Ta wersja zapoznawcza zapewnia obsługę na poziomie produkcyjnym zarządzanych maszyn wirtualnych.

Kopie zapasowe maszyn wirtualnych z każdym rozmiarem dysku do 30 TB i maksymalnie 256 TB połączone dla wszystkich dysków w maszynie wirtualnej powinny bezproblemowo współpracować bez wpływu na istniejące kopie zapasowe. Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika, aby można było uruchamiać kopie zapasowe dysków o dużych rozmiarach, jeśli maszyna wirtualna została już skonfigurowana przy użyciu Azure Backup.

Należy utworzyć kopię zapasową wszystkich Virtual Machines Azure z dużymi dyskami, na których skonfigurowano kopię zapasową.

## <a name="next-steps"></a>Następne kroki

Teraz [Przygotuj się do utworzenia kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-arm-vms-prepare.md).
