---
title: Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia włączanie i konfigurowanie metrykę diagnostyki rozszerzonej na maszynach wirtualnych platformy Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 9c09f473f5d8b399c4fb1b1ad449c783c797f4dd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968513"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure

Rozwiązanie Cloudyn korzysta metryki danych platformy Azure z maszynami wirtualnymi portalu Azure, aby pokazać, że szczegółowe informacje na temat ich zasobów. Dane metryk, nazywany również liczników wydajności, jest używany przez firmę Cloudyn do generowania raportów. Jednak Cloudyn nie automatycznie zbierać wszystkie dane metryk platformy Azure z maszyny wirtualnej gościa, należy włączyć zbieranie metryk. Ten artykuł ułatwia włączanie i konfigurowanie diagnostyki dodatkowych metryk dla maszyn wirtualnych platformy Azure.

Po włączeniu zbierania metryk, możesz wykonywać następujące czynności:

- Wiadomo, kiedy maszyny wirtualne zbliżają się ich pamięci, dysku i limity procesora CPU.
- Wykrywanie trendów użycia i anomalii.
- Kontrolować ponoszone koszty według rozmiaru, od użycia.
- Uzyskaj koszt skuteczne, ustalanie rozmiaru zaleceń optymalizacji Cloudyn.

Na przykład możesz chcieć Monitoruj procent użycia procesora CPU i procent pamięci w maszynach wirtualnych platformy Azure. Metryki maszyny Wirtualnej platformy Azure odpowiadają _[Host] procentowe użycie procesora CPU_ i _procent pamięci [gościa]_.

> [!NOTE]
> Zbieranie rozszerzonych danych metryk jest obsługiwany tylko przy użyciu funkcji platformy Azure monitorowania na poziomie gościa. Cloudyn nie jest zgodny z rozszerzenia maszyny Wirtualnej programu Log Analytics.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Sprawdź, czy metryki są włączone na maszynach wirtualnych

1. Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.
2. W obszarze **maszyn wirtualnych**, wybierz maszynę Wirtualną i w obszarze **monitorowanie**, wybierz opcję **metryki**. Jest wyświetlana lista dostępnych metryk.
3. Wybierz niektóre metryki, a wykres przedstawia dane dla nich.  
    ![Przykładowe metryki — host procentowe użycie procesora CPU](./media/azure-vm-extended-metrics/metric01.png)

W powyższym przykładzie ograniczony zestaw standardowych metryk są dostępne dla hostów, ale metryk pamięci nie są. Metryki pamięci są częścią pakietu rozszerzonych metryk. Należy wykonać dodatkowe kroki w celu włączenia rozszerzonych metryk. Poniższe informacje przeprowadzi Cię przez włączenie ich.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Włącz rozszerzone metryki w witrynie Azure portal

Metryki standardowe są metryki komputera hosta. _[Host] procentowe użycie procesora CPU_ metryka jest jednym z przykładów. Dostępne są również podstawowe metryki dla maszyn wirtualnych gościa i są również nazywane rozszerzonych metryk. Przykłady rozszerzonych metryk _procent pamięci [gościa]_ i _dostępnej pamięci [gościa]_.

Włączanie rozszerzonych metryk jest bardzo proste. Dla każdej maszyny Wirtualnej Włącz monitorowanie na poziomie gościa. Po włączeniu monitorowania na poziomie gościa agent diagnostyki platformy Azure jest zainstalowany na maszynie Wirtualnej. Następujący proces jest taki sam dla maszyn wirtualnych klasycznych i regularnego i taki sam dla Windows i maszyn wirtualnych systemu Linux.

Należy pamiętać, że zarówno platformy Azure, jak i Linux monitorowania na poziomie gościa wymagają konta magazynu. Po włączeniu monitorowania poziomie gościa, jeśli nie wybierzesz istniejące konto magazynu, a następnie jedną zostanie utworzony.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Włącz monitorowanie na poziomie dla gości na istniejących maszynach wirtualnych

1. W **maszyn wirtualnych**, Wyświetl listę maszyn wirtualnych, a następnie wybierz Maszynę wirtualną.
2. W obszarze **monitorowanie**, wybierz opcję **metryki**.
3. Kliknij przycisk **ustawień diagnostycznych**.
4. Na stronie ustawienia diagnostyki kliknij **Włącz monitorowanie na poziomie gościa**.  
    ![Włącz monitorowanie na poziomie gościa](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Po kilku minutach agenta funkcji Diagnostyka Azure jest zainstalowany na maszynie Wirtualnej. Odśwież stronę i listy dostępnych metryk jest aktualizowany za pomocą metryk gościa.  
    ![Rozszerzonych metryk](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Aby włączyć monitorowanie poziomie gościa, na nowych maszynach wirtualnych

Podczas tworzenia nowych maszyn wirtualnych, upewnij się, że wybrano **Diagnostyka systemu operacyjnego gościa**.

![Włącz diagnostykę systemu operacyjnego gościa](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Poświadczenia usługi Resource Manager

Po włączeniu rozszerzonych metryk, upewnij się, że Cloudyn ma dostęp do Twojego [poświadczenia usługi Resource Manager](activate-subs-accounts.md). Poświadczenia są wymagane do rozwiązania Cloudyn do zbierania i przedstawia dane wydajności dla maszyn wirtualnych. Są również używane do tworzenia zaleceń dotyczących optymalizacji kosztów. Cloudyn musi mieć co najmniej trzy dni danych dotyczących wydajności z wystąpienia, aby ustalić, czy jest kandydatem do zalecenia redukcję zatrudnienia.

## <a name="enable-vm-metrics-with-a-script"></a>Włącz metryki maszyny Wirtualnej przy użyciu skryptu

Można włączyć metryki maszyny Wirtualnej za pomocą skryptów programu Azure PowerShell. W przypadku wielu maszyn wirtualnych, które chcesz włączyć metryki na można użyć skryptu, aby zautomatyzować proces. Przykładowe skrypty znajdują się w witrynie GitHub pod [usługi Azure Diagnostics Włącz](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Zobacz metryki wydajności platformy Azure

Aby wyświetlić metryki wydajności swoich wystąpień usługi Azure w portalu Cloudyn, przejdź do **zasoby** > **obliczenia** > **wystąpienia Eksploratora**. Na liście wystąpień maszyn wirtualnych Rozwiń wystąpienie, a następnie rozwiń zasobów, aby wyświetlić szczegóły.

![Eksplorator wystąpienia](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie został jeszcze włączony dostęp do interfejsu API usługi Azure Resource Manager dla konta, przejdź do [Activate Azure subskrypcje i konta](activate-subs-accounts.md).
