---
title: Omówienie przechowywania danych w środowisku usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano dwa ustawienia, które kontrolują przechowywanie danych w środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 204a9d64d14fe190cb0de73c964bb95b4b9b475f
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628770"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Omówienie przechowywania danych w usłudze Time Series Insights
W tym artykule opisano dwa ustawienia, które mają wpływ na przechowywanie danych w środowisku usługi Time Series Insights (TSI).

Każde środowisko usługi TSI ma ustawienie sterujące **czas przechowywania danych**. Wartość rozciąga się od 1 do 400 dni. Dane zostaną usunięte, oparte na środowisku pojemności lub przechowywania czas magazynowania (1 – 400), osiągnięta jako pierwsza.

Każde środowisko usługi TSI ma dodatkowe ustawienie **zachowanie przekroczony limit magazynu**. To ustawienie kontroluje zachowanie transferu danych przychodzących i czyszczenie, gdy zostanie osiągnięta maksymalna wydajność środowiska. Istnieją dwa zachowania do wyboru:
- **Usuwanie starych danych** (ustawienie domyślne)  
- **Wstrzymaj transferu danych przychodzących**

> [!NOTE]
> Domyślnie podczas tworzenia nowego środowiska, przechowywania jest skonfigurowany do **wyczyścić stare dane**. To ustawienie może być przełączane w razie potrzeby po przy użyciu witryny Azure portal w czasie jego tworzenia **Konfiguruj** strony środowiska TSI.

Informacje na temat przechowywania zachowania przełączania można przejrzeć [konfigurowania przechowywania w usłudze Time Series Insights](time-series-insights-how-to-configure-retention.md).

Porównaj zachowania w zakresie przechowywania danych:

## <a name="purge-old-data"></a>Usuwanie starych danych
- To zachowanie jest zachowaniem domyślnym dla środowisk usługi TSI i wystawach wykazywał tego samego środowiska usługi TSI zachowanie, ponieważ uruchomiona w publicznej wersji zapoznawczej.  
- To zachowanie jest preferowane w przypadku, gdy użytkownicy chcą zobaczyć zawsze ich *najbardziej aktualne dane* w swoim środowisku TSI. 
- To zachowanie *Przeczyszcza* danych środowiska po osiągnięciu limitu (czas przechowywania, rozmiar lub count, osiągnięta jako pierwsza). Przechowywanie jest domyślnie do 30 dni. 
- Najstarszy pozyskiwanych danych są przeczyszczane pierwszy (podejście FIFO).

### <a name="example-1"></a>Przykład 1:
Należy wziąć pod uwagę przykładowe środowisko z zachowaniem przechowywania **nadal transferu danych przychodzących i czyszczenie starych danych**: W tym przykładzie **czas przechowywania danych** jest ustawiona na 400 dni. **Pojemność** jest ustawiona na jednostki S1, co zawiera 30 GB łączna pojemność.   Załóżmy, że dla ruchu przychodzącego dane są gromadzone do 500 MB dziennie średnio. To środowisko można zachować tylko 60 dni ważności danych, biorąc pod uwagę współczynnik danych przychodzących, ponieważ osiągnięto maksymalną pojemność w ciągu 60 dni. Dane przychodzące są gromadzone jako: 500 MB każdy dzień x 60 dni = 30 GB. 

W tym przykładzie dnia 61st środowiska pokazuje zawierać najświeższe dane, ale Przeczyszcza najstarsze dane starsze niż 60 dni. Przeczyszczanie sprawia, że miejsce dla nowych danych przesyłania strumieniowego, tak aby nowe dane mogą w dalszym ciągu być eksplorowana. 

Jeśli użytkownik zamierza przechowywać dane dłużej, można zwiększyć rozmiar środowiska, dodając usługę o kolejne jednostki lub wypchnąć mniejszej ilości danych.  

### <a name="example-2"></a>Przykład 2:
Należy wziąć pod uwagę środowisko skonfigurowane również zachowanie przechowywania **nadal transferu danych przychodzących i czyszczenie starych danych**. W tym przykładzie **czas przechowywania danych** jest ustawiona na niższą wartość 180 dni. **Pojemność** jest ustawiona na jednostki S1, co zawiera 30 GB łączna pojemność. Aby można było przechowywać dane przez pełne 180 dni, dzienny ruch przychodzący nie może przekraczać 0.166 GB (166 MB) na dzień.  

Zawsze, gdy to środowisko dzienny transfer przychodzący przekroczy 0.166 GB dziennie, dane nie mogą być przechowywane przez 180 dni, ponieważ pobiera przeczyścić dane. Należy wziąć pod uwagę tego samego środowiska zajęty horyzoncie czasowym. Załóżmy, że szybkość transferu danych przychodzących środowiska może wzrosnąć do średniej 0.189 GB dziennie. W tym okresie zajęty 158 dni danych zostaną zachowane (30GB/0.189 = 158,73 dni przechowywania). Ten czas jest mniejszy niż przedział czasu przechowywania żądanych danych.

## <a name="pause-ingress"></a>Wstrzymaj transferu danych przychodzących
- To zachowanie jest przeznaczony do upewnij się, że dane nie są przeczyszczane, jeśli osiągnięciu limitów rozmiaru i liczby przed ich okresu przechowywania.  
- Takie zachowanie odznacza się dodatkowy czas dla użytkowników zwiększyć jej pojemność, ich środowiska, zanim dane są przeczyszczane ze względu na naruszenie okres przechowywania danych
- To zachowanie pomaga chronić przed utratą danych, ale tworzy szansy sprzedaży związane z utratą najnowszych danych, jeśli ruch przychodzący jest wstrzymana, po upływie okresu przechowywania źródła zdarzeń.
- Jednak po osiągnięciu maksymalnej pojemności środowiska środowiska wstrzymuje transferu danych przychodzących, dopóki nie występują dodatkowe akcje: 
   - Możesz zwiększyć maksymalną pojemność środowiska. Aby uzyskać więcej informacji, zobacz [sposób skalowania środowiska usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md) można dodać kolejne jednostki skalowania.
   - Okres przechowywania danych, a dane są przeczyszczane możliwe. Dzięki temu środowisko poniżej maksymalnej pojemności.

### <a name="example-3"></a>Przykład 3:
Należy wziąć pod uwagę środowisko, z zachowaniem przechowywania skonfigurowane do **wstrzymać ruch przychodzący**. W tym przykładzie **okres przechowywania danych** jest skonfigurowany do 60 dni. **Pojemność** jest równa 3 jednostki S1. Załóżmy, że w tym środowisku obowiązują transferu danych przychodzących, 2GB danych dziennie. W tym środowisku ruch przychodzący jest wstrzymany, po osiągnięciu maksymalnej pojemności. W tym czasie środowiska pokazuje tego samego zestawu danych, dopóki wznawia transferu danych przychodzących lub do momentu "continue przychodzące" jest włączona (który będzie przeczyścić starszych danych, aby zwolnić miejsce dla nowych danych). 

Po wznowieniu działania transferu danych przychodzących:
- Dane przepływają w kolejności, w której zostało przesłane przez źródło zdarzenia
- Zdarzenia są indeksowane w oparciu o ich sygnatury czasowej, chyba że przekroczono zasady przechowywania w źródle zdarzenia. Aby uzyskać więcej informacji na temat konfiguracji przechowywania źródła zdarzeń [Event Hubs często zadawane pytania](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Należy ustawić alerty, aby powiadomić w celu uniknięcia ruch przychodzący jest wstrzymane. Możliwa jest utrata danych, ponieważ domyślny okres przechowywania będzie 1 dzień dla źródeł zdarzeń platformy Azure. W związku z tym gdy ruch przychodzący jest wstrzymany, prawdopodobnie zostaną utracone najnowsze dane, chyba że zostaną podjęte dodatkowe działania. Należy zwiększyć pojemność, lub zachowanie, aby przełączyć **wyczyścić stare dane** w celu uniknięcia potencjalnych utraty danych.

W których to dotyczy usługi Event Hubs, należy wziąć pod uwagę dostosowywanie **okres przechowywania wiadomości** właściwość, aby zminimalizować ryzyko utraty danych, gdy wystąpi Wstrzymaj transferu danych przychodzących usługi Time Series Insights.

![Przechowywanie komunikatów Centrum zdarzeń.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Brak właściwości skonfigurowanie źródła zdarzeń (timeStampPropertyName) TSI wartość domyślna to sygnatura czasowa odebrania w Centrum zdarzeń jako osi x. Jeśli timeStampPropertyName jest skonfigurowany jako coś innego, środowiska szuka skonfigurowanego timeStampPropertyName w pakiecie danych podczas zdarzenia są analizowane. 

Jeśli chcesz skalować środowiska do uwzględnienia dodatkowej pojemności lub zwiększyć okres przechowywania, zobacz [sposób skalowania środowiska usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md) Aby uzyskać więcej informacji.  

## <a name="next-steps"></a>Kolejne kroki
Informacje na temat przechowywania zachowania przełączania można przejrzeć [konfigurowania przechowywania w usłudze Time Series Insights](time-series-insights-how-to-configure-retention.md).
