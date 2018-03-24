---
title: Uaktualnij do stosu kopii zapasowej maszyny Wirtualnej platformy Azure w wersji 2 | Dokumentacja firmy Microsoft
description: Uaktualnij proces i często zadawane pytania dla stos kopii zapasowej maszyny Wirtualnej w wersji 2
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 6d214072bccb8b2b42828ee003dcf349985b4f43
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>Uaktualnianie do stosu kopii zapasowej maszyny wirtualnej w wersji 2
Uaktualnienie kopii zapasowej stosu V2 maszyny wirtualnej (VM) zapewnia następujące udoskonalenia:
* Zdolność do wyświetlania migawka wykonana w ramach zadania tworzenia kopii zapasowej był dostępny do odzyskiwania bez oczekiwania na zakończenie transferu danych.
Zmniejszy jego czas oczekiwania na migawki mają być kopiowane do magazynu, aby mogło nastąpić wyzwolenie przywracania. Ponadto wyeliminuje to wymagania dodatkowe miejsce do magazynowania do wykonywania kopii zapasowych maszyn wirtualnych premium z wyjątkiem pierwszej kopii zapasowej.  

* Skrócenie czasu kopii zapasowej i przywracania zachowując migawki lokalnie przez 7 dni. 

* Obsługa dysku rozmiarów do 4 TB.  

* Możliwość używania konta magazynu oryginalne (nawet wtedy, gdy maszyna wirtualna ma dysków, które są rozproszone na kontach magazynu) podczas wykonywania przywracania niezarządzane maszyny wirtualnej. Spowoduje to przywrócenie szybciej w wielu różnych konfiguracji maszyny Wirtualnej. 
    > [!NOTE] 
    > To nie jest taka sama jak zastępowanie oryginalna maszyna wirtualna. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>Co to jest zmiana w nowego stosu?
Już dziś zadanie tworzenia kopii zapasowej składa się z dwóch faz:
1.  Utworzenia migawki maszyny Wirtualnej. 
2.  Przenoszenie migawki maszyny Wirtualnej do magazynu kopii zapasowej Azure. 

Punkt odzyskiwania jest uważany za tworzone dopiero po zakończeniu fazy 1 i 2. W ramach nowego stosu punkt odzyskiwania jest tworzony natychmiast po ukończeniu tworzenia migawki. Można przywrócić z tego punktu przywracania przy użyciu tego samego przepływu przywracania. Można zidentyfikować tego punktu odzyskiwania w portalu Azure za pomocą "migawki" jako typu punktu odzyskiwania. Po migawki są przesyłane do magazynu, typ punktu odzyskiwania zmieni się na "Migawki i Magazyn". 

![Zadanie tworzenia kopii zapasowej w stos kopii zapasowej maszyny Wirtualnej w wersji 2](./media/backup-azure-vms/instant-rp-flow.jpg) 

Domyślnie migawki zostaną zachowane na siedem dni. Dzięki temu przywracania można wykonać szybciej z migawek skracając czas potrzebny do skopiowania danych z magazynu do konta magazynu klienta. 

## <a name="considerations-before-upgrade"></a>Zagadnienia dotyczące przed uaktualnieniem
* To uaktualnienie jednokierunkową stos kopii zapasowej maszyny Wirtualnej. Tak wszystkie kopie zapasowe w przyszłości zostaną umieszczone w tym przepływie. Ponieważ **jest włączona na poziomie subskrypcji, wszystkie maszyny wirtualne przechodzą na ten przepływ**. Wszystkie nowe informacje będą funkcji będą oparte na tym samym stosie. Możliwość kontrolowania, które to na poziomie zasad pochodzi w przyszłych wersjach. 
* Dla maszyn wirtualnych z dysków w warstwie premium, podczas pierwszej kopii zapasowej upewnij się, miejsca do magazynowania odpowiednikiem rozmiar maszyny wirtualnej jest dostępna w ramach konta magazynu, aż do zakończenia pierwszej kopii zapasowej. 
* Ponieważ migawki są przechowywane lokalnie do zwiększania Tworzenie punktu odzyskiwania, a także aby przyspieszyć przywracania, zobaczysz kosztów magazynowania odpowiadający migawek w ciągu siedmiu dni.
* Jeśli przeprowadzasz przywracania z migawki punktu odzyskiwania dla maszyny Wirtualnej — wersja Premium, zobaczysz lokalizacji magazyn tymczasowy używany, gdy maszyna wirtualna jest tworzony jako część przywracania. 

## <a name="how-to-upgrade"></a>Jak uaktualnić?
### <a name="the-azure-portal"></a>Portalu Azure
Jeśli używasz przy użyciu portalu Azure zostanie wyświetlone powiadomienie na pulpicie nawigacyjnym magazynu powiązane do obsługi dużych dysków i kopii zapasowej i przywracania poprawa prędkości.

![Zadanie tworzenia kopii zapasowej w stos kopii zapasowej maszyny Wirtualnej w wersji 2](./media/backup-azure-vms/instant-rp-banner.png) 

Aby otworzyć ekran uaktualniania do nowego stosu, wybierz banerze. 

![Zadanie tworzenia kopii zapasowej w stos kopii zapasowej maszyny Wirtualnej w wersji 2](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Wykonaj następujące polecenia cmdlet z terminala PowerShell z podwyższonym poziomem uprawnień:
1.  Zaloguj się do konta platformy Azure. 

```
PS C:> Login-AzureRmAccount
```

2.  Wybierz subskrypcję, który chcesz zarejestrować podglądu:

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  Zarejestruj tę subskrypcję w prywatnej wersji zapoznawczej:

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>Sprawdź, czy uaktualnienie zostało ukończone
W terminalu programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące polecenie cmdlet:

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

Informacja zarejestrowane, subskrypcja jest uaktualniany do stos kopii zapasowej maszyny Wirtualnej w wersji 2. 



