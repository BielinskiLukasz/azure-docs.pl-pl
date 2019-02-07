---
title: Samouczek — ograniczyć koszty platformy Azure z zaleceniami dotyczącymi optymalizacji | Dokumentacja firmy Microsoft
description: Ten samouczek ułatwia obniżenie kosztów platformy Azure, gdy działa zgodnie z zaleceniami optymalizacji.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 2255f14d026d406dc41802c321d0a673ee2f2f23
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766864"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Samouczek: Optymalizowanie kosztów od zalecanych

Usługa Azure Cost Management działa dzięki usłudze Azure Advisor, aby przedstawić zalecenia w zakresie optymalizacji kosztów. Usługa Azure Advisor ułatwia optymalizacji i zwiększyć wydajność, określając bezczynności i niedostatecznie używanych zasobów. W tym samouczku przedstawiono przykład gdzie identyfikowanie niedostatecznie używanych zasobów platformy Azure, a następnie podjęcia działania w celu ograniczenia kosztów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie kosztów optymalizacji zalecenia, aby wyświetlić potencjalnych przypadków niewydajnego użycia
> * Zajmującym się zalecenie, aby zmienić rozmiar maszyny wirtualnej do bardziej ekonomiczna opcja
> * Sprawdź akcji, aby upewnić się, że maszyna wirtualna został pomyślnie zmieniony

## <a name="prerequisites"></a>Wymagania wstępne
Zalecenia są dostępne dla różnych typów konta platformy Azure, w tym [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) klientów. Aby wyświetlić pełną listę typów obsługiwanych kont, zobacz [danych rozwiązania Cost Management zrozumieć](understand-cost-mgt-data.md). Aby wyświetlać dane kosztów, musisz mieć co najmniej prawa dostępu do odczytu co najmniej jednego z poniższych zakresów.

- Subskrypcja
- Grupa zasobów

Konieczne jest posiadanie aktywnej maszyny wirtualne z co najmniej 14 dni aktywności.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Wyświetl zalecenia dotyczące optymalizacji kosztów

W witrynie Azure Portal kliknij pozycję **Zarządzanie kosztami i rozliczenia** na liście usług. Następnie na liście w obszarze **Cost Management**, wybierz opcję **zalecenia usługi Advisor**. Klasyfikator koszt zalecenia są wyświetlane.

![Klasyfikator zarządzania zalecenia dotyczące kosztów wyświetlane w witrynie Azure portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Lista zaleceń identyfikuje przypadków niewydajnego użycia lub pokazuje, że zalecenia zakupu, które mogą pomóc Ci zaoszczędzić dodatkowe. Łączne **potencjalne oszczędności roczne** zawiera łączną kwotę, który potrafi zapisać, zamknąć lub cofnięcie przydziału wszystkich maszyn wirtualnych, które spełniają reguły rekomendacji. Jeśli nie chcesz je wyłączyć, należy rozważyć zmianę rozmiaru ich do mniej kosztowne jednostki SKU maszyny Wirtualnej.

**Wpływ** kategorii, wraz z **potencjalne oszczędności roczne**, pomagają identyfikować zaleceń, które potencjalnie można zapisać w miarę możliwości. Zalecenia o dużym znaczeniu są [Kup zarezerwowane wystąpienia maszyn wirtualnych, aby zaoszczędzić pieniądze w porównaniu z kosztami płatności zgodnie z rzeczywistym](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) i [maszyny wirtualnej Optymalizuj wydatki przez zmianą rozmiaru lub zamykanie wystąpienia niedostatecznie używanych](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances). Średni wpływ zalecenia są [obniżenie kosztów przez wyeliminowanie niezaznaczone elastycznie obwodów usługi ExpressRoute](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits) i [obniżenie kosztów przez usunięcie lub ponowne konfigurowanie bezczynne bramy sieci wirtualnej](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways).

## <a name="act-on-a-recommendation"></a>Działają zgodnie z zaleceniem

Usługa Azure Advisor monitoruje wykorzystanie maszyn wirtualnych przez 14 dni, a następnie identyfikuje niedostatecznie używanych maszyn wirtualnych. Maszyny wirtualne, których użycie procesora CPU osiągnie wartość 5% lub mniej, a użycie sieci to 7 MB lub mniej przez cztery lub więcej dni są traktowane jako niskie użycie maszyn wirtualnych.

5% lub mniej ustawienie wykorzystanie procesora CPU jest ustawieniem domyślnym, ale można dostosować ustawienia. Aby uzyskać więcej informacji na temat dostosowywania ustawienie zobacz [skonfigurować średni reguły wykorzystanie procesora CPU lub zaleceń dotyczących maszyny wirtualnej o niskim użyciu](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Chociaż w niektórych scenariuszach może spowodować niewielkie wykorzystanie zgodnie z projektem, często można oszczędzić pieniądze, zmieniając rozmiar maszyn wirtualnych do rozmiarów mniej kosztowne. Rzeczywiste oszczędności mogą się różnić, jeśli akcja zmiany rozmiaru. Przejdźmy teraz przez przykład zmiany rozmiaru maszyny wirtualnej.

Na liście zalecenia, kliknij **odpowiedniego rozmiaru lub zamknięcia systemu skutkowało niewystarczającym wykorzystaniem maszyn wirtualnych** zalecenia. Na liście kandydatów maszyny wirtualnej wybierz maszynę wirtualną, aby zmienić rozmiar, a następnie kliknij maszynę wirtualną. Szczegóły maszyny wirtualnej są wyświetlane, dzięki czemu można sprawdzić metryki wykorzystania. **Potencjalne oszczędności roczne** wartość to, co można zapisać przypadku zamknięcie lub Usuń maszynę Wirtualną. Zmiana rozmiaru maszyny Wirtualnej będzie prawdopodobnie zaoszczędzić pieniądze, ale nie zapisuje cała kwota potencjalne oszczędności roczne.

![Przykład szczegóły zalecenia](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Szczegóły maszyny Wirtualnej Sprawdź wykorzystanie maszyny wirtualnej, aby potwierdzić, że jest kandydat odpowiedniego rozmiaru.

![Maszyna wirtualna w poniższym przykładzie omówiono przedstawiający wykorzystanie historycznych](./media/tutorial-acm-opt-recommendations/vm-details.png)

Należy pamiętać, rozmiar bieżącej maszyny wirtualnej. Po upewnieniu się, że powinno zmienić rozmiar maszyny wirtualnej, należy zamknąć szczegóły maszyny Wirtualnej tak, aby wyświetlić listę maszyn wirtualnych.

Z listy kandydatów do zamknięty lub zmienianie jej rozmiaru wybierz **rozmiaru maszyny wirtualnej**.
![Przykład zalecenia z możliwością zmiany rozmiaru maszyny wirtualnej](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Następnie zobaczysz listę opcji dostępnych zmiany rozmiaru. Wybierz ten, który zapewni najlepszą wydajność i efektywność kosztową dla danego scenariusza. W poniższym przykładzie wybrana opcja zmienia rozmiar z **DS14\_V2** do **DS13\_V2**. Z zaleceniem zapisuje 551.30 $/ miesiąc lub 6,615.60 $/ rok.

![Przykład listę dostępnych rozmiarów maszyn wirtualnych, w którym można wybrać rozmiar](./media/tutorial-acm-opt-recommendations/choose-size.png)

Po wybraniu odpowiedniego rozmiaru, kliknij przycisk **wybierz** rozpoczął akcji zmiany rozmiaru.

Zmiana rozmiaru wymaga aktywnie uruchomionej maszyny wirtualnej, aby ponownie uruchomić. Jeśli maszyna wirtualna znajduje się w środowisku produkcyjnym, zaleca się uruchomienie operacji zmiany rozmiaru po godzinach pracy. Planowanie ponownego uruchomienia, można zmniejszyć spowodowany przez niedostępność chwilowo przerw w działaniu.

## <a name="verify-the-action"></a>Sprawdź akcji

Podczas zmiany rozmiaru maszyny Wirtualnej zakończy się pomyślnie, wyświetlane jest powiadomienie dotyczące urządzenia Azure.

![Pomyślnie zmieniono rozmiar maszyny wirtualnej powiadomień](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie kosztów optymalizacji zalecenia, aby wyświetlić potencjalnych przypadków niewydajnego użycia
> * Zajmującym się zalecenie, aby zmienić rozmiar maszyny wirtualnej do bardziej ekonomiczna opcja
> * Sprawdź akcji, aby upewnić się, że maszyna wirtualna został pomyślnie zmieniony

Jeśli nie zostały już przeczytane Cost Management najlepsze rozwiązania dotyczące, zawiera ogólne wskazówki i zasady, które należy wziąć pod uwagę, aby pomóc w zarządzaniu kosztami.

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania Cost Management](cost-mgt-best-practices.md)
