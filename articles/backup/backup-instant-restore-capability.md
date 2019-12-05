---
title: Możliwość natychmiastowego przywrócenia platformy Azure
description: Funkcja błyskawicznego przywracania platformy Azure i często zadawane pytania dotyczące stosu kopii zapasowych maszyny wirtualnej, Menedżer zasobów model wdrażania
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7a962ff42125b262f6ec6673f75b0afd2954de24
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806375"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Uzyskiwanie ulepszonej wydajności tworzenia kopii zapasowych i przywracania dzięki funkcji Azure Backup natychmiastowego przywracania

> [!NOTE]
> Na podstawie informacji zwrotnych od użytkowników zmieniamy nazwy **stosu kopii zapasowych maszyny wirtualnej v2** na **natychmiastowe przywracanie** , aby zmniejszyć liczbę pomyłek przy użyciu funkcji Azure Stack.
> Wszyscy użytkownicy usługi Azure Backup zostali teraz uaktualnioni do **natychmiastowego przywracania**.

Nowy model do natychmiastowego przywracania zapewnia następujące udoskonalenia funkcji:

* Możliwość korzystania z migawek wykonanych w ramach zadania tworzenia kopii zapasowej, które jest dostępne do odzyskania bez oczekiwania na zakończenie transferu danych do magazynu. Skraca czas oczekiwania na skopiowanie migawek do magazynu przed wyzwoleniem przywracania.
* Skraca czas wykonywania kopii zapasowej i przywracania przez zachowywanie migawek lokalnie, domyślnie przez dwa dni. Domyślna wartość przechowywania migawek jest konfigurowalna dla dowolnej wartości z zakresu od 1 do 5 dni.
* Obsługuje rozmiary dysków do 32 TB. Nie zaleca się zmiany rozmiarów dysków Azure Backup.
* Program obsługuje dyski SSD w warstwie Standardowa razem z dyskami HDD w warstwie Standardowa i dyskami SSD w warstwie Premium.
* Możliwość korzystania z niezarządzanych maszyn wirtualnych (na dysku) podczas przywracania. Ta możliwość istnieje nawet wtedy, gdy maszyna wirtualna zawiera dyski rozproszone na kontach magazynu. Przyspiesza operacje przywracania w różnych konfiguracjach maszyn wirtualnych.
* W przypadku tworzenia kopii zapasowych maszyn wirtualnych korzystających z usługi Premium Storage z natychmiastowym przywróceniem zaleca się alokowanie *50%* wolnego miejsca w łącznym przydzielonym miejscu do magazynowania, które jest wymagane **tylko** dla pierwszej kopii zapasowej. Ilość wolnego miejsca na 50% nie jest wymagana w przypadku kopii zapasowych po wykonaniu pierwszej kopii zapasowej.

## <a name="whats-new-in-this-feature"></a>Co nowego w tej funkcji

Obecnie zadanie tworzenia kopii zapasowej składa się z dwóch faz:

1. Tworzenie migawki maszyny wirtualnej.
2. Przenoszenie migawki maszyny wirtualnej do magazynu usługi Azure Recovery Services.

Punkt odzyskiwania jest uznawany za tworzony dopiero po zakończeniu faz 1 i 2. W ramach tego uaktualnienia punkt odzyskiwania jest tworzony zaraz po zakończeniu migawki, a ten punkt odzyskiwania typu migawki może służyć do wykonywania przywracania przy użyciu tego samego przepływu przywracania. Ten punkt odzyskiwania można zidentyfikować w Azure Portal przy użyciu "migawek" jako typu punktu odzyskiwania, a po przeniesieniu migawki do magazynu typ punktu odzyskiwania zmieni się na "migawka i magazyn".

![Zadanie tworzenia kopii zapasowej w stosie kopii zapasowych maszyny wirtualnej Menedżer zasobów model wdrażania — magazyn i magazynu](./media/backup-azure-vms/instant-rp-flow.png)

Domyślnie migawki są przechowywane przez dwa dni. Ta funkcja umożliwia przywracanie z tych migawek w tym miejscu przez wycinanie czasów przywracania. Skraca czas wymagany do przekształcania i kopiowania danych z magazynu.

## <a name="feature-considerations"></a>Zagadnienia dotyczące funkcji

* Migawki są przechowywane wraz z dyskami w celu zwiększenia tworzenia punktów odzyskiwania i przyspieszenia operacji przywracania. W związku z tym zobaczysz koszty magazynu odpowiadające migawkom pobranym w tym okresie.
* Migawki przyrostowe są przechowywane jako stronicowe obiekty blob. Wszyscy użytkownicy korzystający z dysków niezarządzanych są obciążani za migawki przechowywane na koncie magazynu lokalnego. Ponieważ kolekcje punktów przywracania używane przez zarządzane kopie zapasowe maszyn wirtualnych używają migawek obiektów BLOB na podstawowym poziomie magazynu, w przypadku dysków zarządzanych zobaczysz koszty odpowiadające cenom migawek obiektów blob i są one przyrostowe.
* W przypadku kont magazynu w warstwie Premium migawki wykonywane na potrzeby natychmiastowych punktów odzyskiwania są wliczane do 10 TB przydzielonego miejsca.
* Można skonfigurować przechowywanie migawek na podstawie potrzeb przywracania. W zależności od wymagań można ustawić przechowywanie migawek na co najmniej jeden dzień w bloku zasad tworzenia kopii zapasowych, jak wyjaśniono poniżej. Ułatwi to oszczędność kosztów przechowywania migawek, jeśli nie jest często wykonywanych operacji przywracania.
* Po przeprowadzeniu uaktualnienia do natychmiastowego przywrócenia nie można wrócić.

>[!NOTE]
>Po tym uaktualnieniu natychmiastowego przywracania, czas przechowywania migawki dla wszystkich klientów (**w przypadku nowych i istniejących**) zostanie ustawiony na wartość domyślną dwa dni. Można jednak ustawić czas trwania zgodnie z wymaganiami do dowolnej wartości z zakresu od 1 do 5 dni.

## <a name="cost-impact"></a>Wpływ na koszty

Migawki przyrostowe są przechowywane na koncie magazynu maszyny wirtualnej, które jest używane do natychmiastowego odzyskiwania. Migawka przyrostowa oznacza, że miejsce zajęte przez migawkę jest równe zajętemu miejscu zajmowanym przez strony, które są zapisywane po utworzeniu migawki. Opłaty są naliczane za GB zajętego miejsca zajętego przez migawkę, a cena za GB jest taka sama jak na [stronie cennika](https://azure.microsoft.com/pricing/details/managed-disks/).

>[!NOTE]
> Czas przechowywania migawek jest ustalony na 5 dni w przypadku zasad tygodniowych.

## <a name="configure-snapshot-retention"></a>Konfiguruj przechowywanie migawek

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

W Azure Portal można zobaczyć pole dodane w bloku **zasady kopii zapasowych maszyny wirtualnej** w sekcji **natychmiastowe przywracanie** . Czas przechowywania migawki można zmienić w bloku **zasady kopii zapasowych maszyny wirtualnej** dla wszystkich maszyn wirtualnych skojarzonych z określonymi zasadami tworzenia kopii zapasowych.

![Możliwość natychmiastowego przywrócenia](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Używanie programu PowerShell

>[!NOTE]
> Za pomocą polecenia AZ PowerShell w wersji 1.6.0 lub nowszej można zaktualizować okres przechowywania migawki przywracania natychmiast w zasadach przy użyciu programu PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Domyślne przechowywanie migawek dla każdej zasady jest ustawione na dwa dni. Użytkownik może zmienić wartość na co najmniej 1 i maksymalnie pięć dni. W przypadku zasad cotygodniowych przechowywanie migawek jest stałe do pięciu dni.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Jakie są implikacje kosztu natychmiastowego przywrócenia?

Migawki są przechowywane wraz z dyskami w celu przyspieszenia operacji tworzenia i przywracania punktów odzyskiwania. W związku z tym będą widoczne koszty magazynu odpowiadające przechowywaniu migawek wybranym jako część zasad kopii zapasowych maszyny wirtualnej.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>W Premium Storage kontach czy migawki wykonywane na potrzeby natychmiastowego odzyskiwania zajmują limit 10 TB migawek?

Tak, w przypadku kont usługi Premium Storage migawki wykonywane na potrzeby natychmiastowego odzyskiwania zajmują 10 TB przydzieloną przestrzeń migawek.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Jak działa przechowywanie migawek w okresie pięciu dni?

Każdy dzień tworzenia nowej migawki obejmuje pięć pojedynczych migawek przyrostowych. Rozmiar migawki zależy od zmian danych, które są w większości przypadków około 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Czy migawka jest migawką przyrostową lub pełną migawką?

Migawki wykonywane w ramach funkcji natychmiastowego przywracania to migawki przyrostowe.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Jak można obliczyć przybliżony wzrost kosztów spowodowany natychmiastowym przywracaniem?

Jest to zależne od zmian w maszynie wirtualnej. W przypadku stałego stanu można przypuszczać zwiększenie kosztu = okres przechowywania migawek dzienny dla każdego kosztu magazynu maszyny wirtualnej za GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Jeśli typ odzyskiwania punktu przywracania to "migawka i magazyn" i wykonujesz operację przywracania, który typ odzyskiwania zostanie użyty?

Jeśli typ odzyskiwania to "migawka i magazyn", przywracanie zostanie automatycznie wykonane z lokalnej migawki, która będzie znacznie szybsza w porównaniu do przywracania wykonanego z magazynu.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Co się stanie w przypadku wybrania okresu przechowywania punktu przywracania (warstwa 2) mniejszego niż okres przechowywania migawki (Pomoc)?

Nowy model nie zezwala na usuwanie punktu przywracania (SVR), chyba że migawka (Pomoc) jest usuwana. Zalecamy przeprowadzenie planowania okresu przechowywania punktu przywracania (SVR) większego niż okres przechowywania migawki.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Dlaczego moja migawka jest istniejąca, nawet po upływie ustawionego okresu przechowywania w zasadach tworzenia kopii zapasowych?

Jeśli punkt odzyskiwania zawiera migawkę i jest to najnowsza dostępna jednostka UZALEŻNIONa, jest zachowywany do momentu utworzenia kolejnej kopii zapasowej. Jest to zgodne z zaprojektowanymi zasadami "wyrzucania elementów bezużytecznych" (GC), ponieważ w przypadku wystąpienia problemu z maszyną wirtualną wszystkie kopie zapasowe mogą być zawsze obecne w przypadku wystąpienia błędu. W normalnych scenariuszach RPS pliku są czyszczone w ciągu maksymalnie 24 godzin po ich wygaśnięciu.
