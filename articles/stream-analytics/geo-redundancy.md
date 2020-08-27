---
title: Zapewnianie nadmiarowości geograficznej dla zadań Azure Stream Analytics
description: W tym artykule opisano sposób osiągnięcia nadmiarowości geograficznej Azure Stream Analytics zadań, a nie geograficznie trybu failover.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 44b0394773485f8054eddc94c7d7e9b91baebc6d
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951095"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Zapewnianie nadmiarowości geograficznej dla zadań Azure Stream Analytics

Azure Stream Analytics nie zapewnia automatycznej pracy w trybie geograficznym, ale można uzyskać nadmiarowość geograficzną przez wdrożenie identycznych Stream Analytics zadań w wielu regionach świadczenia usługi Azure. Każde zadanie nawiązuje połączenie z lokalnymi źródłami danych wejściowych i lokalnymi. Jest odpowiedzialna aplikacja do przesyłania danych wejściowych do dwóch regionalnych wejść i uzgadniania między dwoma regionami regionalnymi. Zadania Stream Analytics są dwoma osobnymi jednostkami.

Na poniższym diagramie przedstawiono przykładowe geograficznie nadmiarowe wdrożenie zadania Stream Analytics przy użyciu danych wejściowych centrum zdarzeń i danych wyjściowych usługi Azure Database.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="Diagram geograficznie nadmiarowych zadań usługi Stream Analytics":::

## <a name="primarysecondary-strategy"></a>Strategia podstawowa/pomocnicza

Aplikacja musi zarządzać wyjściową bazą danych regionu, która jest uważana za podstawową i która jest traktowana jako pomocnicza. W przypadku awarii regionu podstawowego aplikacja przełącza się do pomocniczej bazy danych i rozpocznie odczytywanie aktualizacji z tej bazy danych. Rzeczywisty mechanizm, który pozwala zminimalizować odczyty duplikatów, zależy od aplikacji.Można uprościć ten proces, pisząc dodatkowe informacje w danych wyjściowych. Na przykład można dodać sygnaturę czasową lub identyfikator sekwencji do każdego danych wyjściowych, aby pominąć zduplikowane wiersze w prostej operacji. Po przywróceniu regionu podstawowego przechwytuje on do pomocniczej bazy danych przy użyciu podobnego Mechanics.

Chociaż różne typy danych wejściowych i wyjściowych umożliwiają używanie różnych opcji replikacji geograficznej, zalecamy użycie wzorca przedstawionego w tym artykule w celu zapewnienia nadmiarowości geograficznej, ponieważ zapewnia ona elastyczność i kontrolę nad zarówno producentami zdarzeń, jak i konsumentami zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

* [Monitorowanie zadań Azure Stream Analytics i zarządzanie nimi za pomocą programu PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Debugowanie oparte na danych w Azure Stream Analytics](stream-analytics-job-diagram-with-metrics.md)