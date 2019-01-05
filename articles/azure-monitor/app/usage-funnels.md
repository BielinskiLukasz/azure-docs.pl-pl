---
title: Usługi Azure Application Insights Lejki
description: Dowiedz się, jak można użyć Lejki, aby dowiedzieć się, jak klienci wchodzą w interakcje z Twoją aplikacją.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/17/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: cc2a21999bdbaef956c0cc6424bb46a93072a6cd
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54049983"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Odkryj, jak klienci korzystają z aplikacji za pomocą Application Insights Lejki

Omówienie obsługi klientów ma priorytetowe znaczenie dla Twojej firmy. Jeśli aplikacja obejmuje wiele etapów, musisz wiedzieć, czy postęp większość klientów przez cały proces, czy są one zakończenie procesu w pewnym momencie. Postęp przez szereg czynności w aplikacji sieci web jest znany jako *lejka*. Możesz używać usługi Azure Application Insights Lejki w celu uzyskania szczegółowych informacji o użytkowników i monitorowania współczynników konwersji krok po kroku. 

## <a name="create-your-funnel"></a>Utwórz lejku
Przed utworzeniem lejku, należy podjąć decyzję w kwestii, które chcesz odpowiedzieć. Na przykład możesz chcieć wiedzieć, ile wyświetlania strony głównej wyświetlanie profilu klienta i tworzenia biletu. W tym przykładzie właściciele firmy Fabrikam Fiber chcieli wiedzieć odsetek klientów, którzy pomyślnie utworzyć zgłoszenie do klienta.

Poniżej przedstawiono kroki, które mogą wykonać w celu utworzenia ich lejka.

1. W narzędziu Application Insights Lejki wybierz **New**.
1. Z **zakres czasu** menu rozwijanego wybierz opcję **ostatnich 90 dni**. Wybierz opcję **Moje Lejki** lub **udostępnione Lejki**.
1. Z **kroku 1** listy rozwijanej wybierz **indeksu**. 
1. Z **kroku 2** listy wybierz **klienta**.
1. Z **kroku 3** listy wybierz **Utwórz**.
1. Dodawanie nazwy do lejka, a następnie wybierz pozycję **Zapisz**.

Poniższy zrzut ekranu przedstawia przykład tego rodzaju danych narzędzia Lejki generuje. Właściciele Fabrikam zobaczyć, że w ciągu ostatnich 90 dni, procent 54.3 swoich klientów, którzy odwiedzone strony głównej utworzyć zgłoszenie do klienta. Można również wyświetlić 2,700 klientom pochodzi ze strony głównej do indeksu. Może to wskazywać problem odświeżania.


![Zrzut ekranu Lejki narzędzia z danymi](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funkcje Lejki
Poprzedni zrzut ekranu zawiera pięć obszarów. Są to funkcje lejki. Poniżej opisano bardziej każdy odpowiadający mu obszar na zrzucie ekranu:
1. Jeśli w aplikacji są próbkowane tak, zostanie wyświetlony transparent pobierania próbek. Wybranie transparent Otwiera okienko kontekstu, wyjaśniające, jak wyłączyć próbkowania. 
2. Możesz wyeksportować lejku tak, aby [usługi Power BI](../../application-insights/app-insights-export-power-bi.md).
3. Wybierz krok, aby zobaczyć więcej szczegółów po prawej stronie. 
4. Na wykresie Konwersja danych historycznych przedstawiono współczynniki konwersji w ciągu ostatnich 90 dni. 
5. Zrozumienie użytkownikom lepsze, uzyskując dostęp do narzędzia użytkowników. Filtry można użyć w każdym kroku. 

## <a name="next-steps"></a>Kolejne kroki
  * [Przegląd wykorzystania](usage-overview.md)
  * [Użytkownicy, sesje i zdarzenia](usage-segmentation.md)
  * [Przechowywanie](usage-retention.md)
  * [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
  * [Dodawanie kontekstu użytkownika](usage-send-user-context.md)
  * [Eksportowanie do usługi Power BI](../../application-insights/app-insights-export-power-bi.md)

