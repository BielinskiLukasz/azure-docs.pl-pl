---
title: "Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych VMware funkcji Hyper V (bez użycia programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs"
description: "Dowiedz się, jak skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V (bez użycia programu VMM) na platformie Azure przy użyciu usługi Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/14/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b946964c162f47a283c37c6eae7e7152e27b6033
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurowanie odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper-V na platformie Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

Ten samouczek przedstawia sposób konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych funkcji Hyper V na platformie Azure. Ten samouczek jest istotny dla maszyn wirtualnych funkcji Hyper V, które nie są zarządzane przy użyciu programu System Center Virtual Machine Manager (VMM). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybieranie źródła i celu replikacji.
> * Konfigurowanie środowiska źródłowego replikacji wraz z lokalnymi składnikami usługi Site Recovery oraz środowiska docelowego replikacji.
> * Tworzenie zasad replikacji.
> * Włączanie replikacji maszyny wirtualnej.

Jest to trzeci samouczek z tej serii. Założono w nim, że zostały już wykonane zadania z poprzednich samouczków:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. [Przygotowywanie lokalnej funkcji Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Przed rozpoczęciem warto [zapoznać się z architekturą](concepts-hyper-v-to-azure-architecture.md) tego scenariusza odzyskiwania po awarii.

## <a name="select-a-replication-goal"></a>Wybieranie celu replikacji


1. W obszarze **Wszystkie usługi** > **Magazyny usługi Recovery Services** kliknij nazwę magazynu przygotowanego w poprzednim samouczku: **ContosoVMVault**.
2. W obszarze **Wprowadzenie** kliknij pozycję **Site Recovery**. Następnie kliknij pozycję **Przygotowywanie infrastruktury**
3. W obszarze **Cel ochrony** > **Gdzie znajdują się maszyny** wybierz pozycję **Lokalne**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane?**, wybierz pozycję **Nie**. Następnie kliknij przycisk **OK**.

    ![Cel replikacji](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

Aby skonfigurować środowisko źródłowe, należy dodać hosty funkcji Hyper-V do lokacji usługi Hyper-V, pobrać i zainstalować dostawcę usługi Azure Site Recovery oraz agenta usługi Azure Recovery Services i zarejestrować lokację funkcji Hyper-V w magazynie. 

1. W obszarze **Przygotowanie infrastruktury**, kliknij przycisk **Źródło**.
2. Kliknij przycisk **+ Lokacja funkcji Hyper-V** i wprowadź nazwę lokacji utworzonej w poprzednim samouczku: **ContosoHyperVSite**.
3. Kliknij przycisk **+ Serwer funkcji Hyper-V**.
4. Pobierz plik instalatora dostawcy.
5. Pobierz klucz rejestracji magazynu. Będzie potrzebny podczas instalacji dostawcy. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Pobieranie dostawcy](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalowanie dostawcy

Uruchom plik instalatora dostawcy (AzureSiteRecoveryProvider.exe) na każdym hoście funkcji Hyper-V dodanym do lokacji **ContosoHyperVSite**. Instalator zainstaluje dostawcę usługi Azure Site Recovery i agenta usług Recovery Services na każdym hoście usługi Hyper-V.

1. W Kreatorze instalacji dostawcy usługi Azure Site Recovery w obszarze **Microsoft Update** wyraź zgodę na używanie usługi Microsoft Update do sprawdzania aktualizacji dostawcy.
2. W obszarze **Instalacja** zaakceptuj domyślną lokalizację instalacji dostawcy i agenta, a następnie kliknij przycisk **Zainstaluj**.
3. Po zakończeniu instalacji w Kreatorze rejestracji usługi Microsoft Azure Site Recovery w obszarze **Ustawienia magazynu** kliknij przycisk **Przeglądaj**, a następnie w oknie **Plik klucza** wybierz pobrany plik klucza magazynu. 
4. Określ subskrypcję usługi Azure Site Recovery, nazwę magazynu (**ContosoVMVault**) oraz lokację funkcji Hyper-V (**ContosoHyperVSite**), do której należy serwer funkcji Hyper-V.
5. W obszarze **Ustawienia serwera proxy** wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
6. W obszarze**Rejestracja** po zarejestrowaniu serwera w magazynie kliknij przycisk **Zakończ**.

Metadane z serwera funkcji Hyper-V zostaną pobrane przez usługę Azure Site Recovery, a serwer zostanie wyświetlony w obszarze **Infrastruktura usługi Site Recovery** > **Hosty funkcji Hyper-V**. Może to potrwać maksymalnie 30 minut.


## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe. 

1. Kliknij pozycję **Przygotowanie infrastruktury** > **Cel**.
2. Wybierz subskrypcję i grupę zasobów **ContosoRG**, w której zostaną utworzone maszyny wirtualne platformy Azure po włączeniu trybu failover.
3. Wybierz model wdrażania usługi **Resource Manager**.

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.


## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Kliknij kolejno pozycje **Przygotowanie infrastruktury** > **Ustawienia replikacji** > **+ Utwórz i skojarz**.
2. W obszarze **Tworzenie i kojarzenie zasad** podaj nazwę zasad: **ContosoReplicationPolicy**.
3. Pozostaw ustawienia domyślne i kliknij przycisk **OK**.
    - Ustawienie **Częstotliwość kopiowania** wskazuje, że dane różnicowe (po replikacji początkowej) będą replikowane co pięć minut.
    - Ustawienie **Przechowywanie punktów odzyskiwania** wskazuje, że okna przechowywania dla każdego punktu odzyskiwania będą wynosiły dwie godziny.
    - Ustawienie **Częstotliwość migawek na poziomie aplikacji** wskazuje, że punkty odzyskiwana zawierające migawki na poziomie aplikacji będą tworzone co godzinę.
    - Ustawienie **Czas rozpoczęcia replikacji początkowej** wskazuje, że replikacja początkowa rozpocznie się natychmiast.
4. Po utworzeniu zasad kliknij przycisk **OK**. Po utworzeniu nowe zasady zostaną automatycznie skojarzone z określoną lokacją funkcji Hyper-V (**ContosoHyperVSite**)

    ![Zasady replikacji](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Włączanie replikacji


1. W obszarze **Replikowanie aplikacji** kliknij przycisk **Źródło**. 
2. W obszarze **Źródło** wybierz lokację **ContosoHyperVSite**. Następnie kliknij przycisk **OK**.
3. W obszarze **Cel** sprawdź, czy platforma Azure została wybrana jako środowisko docelowe oraz czy wskazano właściwą subskrypcję magazynu i model wdrożenia **Resource Manager**.
4. Wybierz konto magazynu **contosovmsacct1910171607** utworzone w poprzednim samouczku do replikowania danych, a następnie wybierz sieć **ContosoASRnet**, w której zostaną umieszczone maszyny wirtualne platformy Azure po przejściu do trybu failover.
5. W obszarze **Maszyny wirtualne** > **Wybierz** wybierz maszynę wirtualną, którą chcesz replikować. Następnie kliknij przycisk **OK**.

 Możesz śledzić postępy akcji **Włącz ochronę** w obszarze **Zadania** > **Zadania usługi Site Recovery**. Po zakończeniu zadania **Finalizuj ochronę** replikacja początkowa zostanie zakończona, a maszyna wirtualna będzie gotowa do pracy w trybie failover.

## <a name="next-steps"></a>Następne kroki
[Uruchamianie próbnego odzyskiwania po awarii](tutorial-dr-drill-azure.md)
