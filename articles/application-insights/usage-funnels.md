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
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 3d4d6ba7e185b1cc5fdcee1633a11e579d8194e8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413967"
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
2. Możesz wyeksportować lejku tak, aby [usługi Power BI](app-insights-export-power-bi.md).
3. Wybierz krok, aby zobaczyć więcej szczegółów po prawej stronie. 
4. Na wykresie Konwersja danych historycznych przedstawiono współczynniki konwersji w ciągu ostatnich 90 dni. 
5. Zrozumienie użytkownikom lepsze, uzyskując dostęp do narzędzia użytkowników. Filtry można użyć w każdym kroku. 

## <a name="next-steps"></a>Kolejne kroki
  * [Przegląd wykorzystania](app-insights-usage-overview.md)
  * [Użytkownicy, sesje i zdarzenia](app-insights-usage-segmentation.md)
  * [Przechowywanie](app-insights-usage-retention.md)
  * [Skoroszyty](app-insights-usage-workbooks.md)
  * [Dodawanie kontekstu użytkownika](app-insights-usage-send-user-context.md)
  * [Eksportowanie do usługi Power BI](app-insights-export-power-bi.md)

