---
title: Zdarzenia kształtu — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej o najlepszych rozwiązaniach i sposobach tworzenia kształtów zapytań w usłudze Azure Time Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650927"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Kształt zdarzeń za pomocą usługi Azure czas Series Insights w wersji zapoznawczej

W tym artykule opisano najlepsze rozwiązania dotyczące kształtów ładunków w formacie JSON na potrzeby pozyskiwania danych w Azure Time Series Insights i maksymalizowania wydajności zapytań w wersji zapoznawczej.

## <a name="best-practices"></a>Najlepsze praktyki

Najlepiej uważnie zastanowić się, jak wysyłać zdarzenia do środowiska Time Series Insights w wersji zapoznawczej. 

Ogólne najlepsze rozwiązania obejmują:

* jak najbardziej wydajny wysyłanie danych przez sieć.
* Store dane w sposób, który pomoże Ci agregować je bardziej odpowiednio do danego scenariusza.

W celu uzyskania najlepszej wydajności zapytań przestrzegaj następujących zasad:

* Nie wysyłaj niepotrzebnych właściwości. Time Series Insights w wersji zapoznawczej według użycia. Najlepiej przechowywać i przetwarzać tylko dane, które będą używane do wykonywania zapytań.
* Użyj pól wystąpień dla danych statycznych. To rozwiązanie pomaga uniknąć wysyłania danych statycznych przez sieć. Pola wystąpienia, składnik modelu szeregów czasowych, działają jak dane referencyjne w usłudze Time Series Insights, która jest ogólnie dostępna. Aby dowiedzieć się więcej o polach wystąpienia, Odczytaj [model szeregów czasowych](./time-series-insights-update-tsm.md).
* Udostępnianie właściwości wymiaru spośród dwóch lub większej liczby zdarzeń. Pomaga to bardziej wydajne wysyłanie danych przez sieć.
* Nie używaj tablicy głębokiego zagnieżdżenia. Wersja zapoznawcza Time Series Insights obsługuje do dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Czas Series Insights w wersji zapoznawczej spłaszcza tablic w wiadomości do wielu zdarzeń przy użyciu pary wartości właściwości.
* Jeśli tylko kilka istnieją środki dla wszystkich lub większości zdarzeń, zaleca się wysłać te miary jako osobne właściwości w ramach tego samego obiektu. Ich wysyłanie osobno zmniejsza liczbę zdarzeń i może poprawić wydajność zapytań, ponieważ wymaga przetworzenia mniejszych zdarzeń.

## <a name="column-flattening"></a>Spłaszczanie kolumn

W trakcie pozyskiwania ładunki zawierające obiekty zagnieżdżone zostaną spłaszczone, tak aby nazwa kolumny była pojedynczą wartością z delineator.

* Na przykład następujący zagnieżdżony kod JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Zmieni się: `data_flow` po spłaszczeniu.

> [!IMPORTANT]
> * Azure Time Series Insights w wersji zapoznawczej używa podkreśleń (`_`) do rozkreślenia kolumn.
> * Zanotuj różnicę z ogólnej dostępności, która używa kropek (`.`).

Poniżej przedstawiono bardziej złożone scenariusze.

#### <a name="example-1"></a>Przykład 1:

W poniższym scenariuszu znajdują się co najmniej dwa urządzenia, które wysyłają pomiary (sygnały): *szybkość przepływu*, *ciśnienie oleju*, *temperatura*i *wilgotność*.

Istnieje pojedynczy komunikat IoT Hub platformy Azure, w którym tablica zewnętrzna zawiera wspólną sekcję wspólnych wartości wymiaru (należy zwrócić uwagę na dwa wpisy urządzeń zawarte w komunikacie).

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**Wnioski**

* Przykładowy kod JSON ma zewnętrzną tablicę, która używa danych [wystąpienia szeregów czasowych](./time-series-insights-update-tsm.md#time-series-model-instances) , aby zwiększyć wydajność wiadomości. Mimo że wystąpienia szeregów czasowych metadanych urządzenia nie mogą ulec zmianie, często zapewnia przydatne właściwości analizy danych.

* KOD JSON łączy dwa lub więcej komunikatów (po jednym z poszczególnych urządzeń) w jeden ładunek, oszczędzając przepustowość w miarę upływu czasu.

* Pojedyncze punkty danych serii dla każdego urządzenia są łączone w jeden atrybut **serii** , co zmniejsza konieczność ciągłego przesyłania strumieniowego aktualizacji dla każdego urządzenia.

> [!TIP]
> Aby zmniejszyć liczbę komunikatów wymaganych do wysłania danych i zwiększyć wydajność telemetrii, należy rozważyć utworzenie wsadowe wspólnych wartości wymiarów i metadanych wystąpienia szeregów czasowych w jednym ładunku JSON.

#### <a name="time-series-instance"></a>Wystąpienia serii czasu 

Zapoznaj się z tym sposobem, jak używać [wystąpienia szeregów czasowych](./time-series-insights-update-tsm.md#time-series-model-instances) , aby bardziej optymalnie KSZTAŁTOWAĆ kod JSON. 

> [!NOTE]
> Poniższe [identyfikatory szeregów czasowych](./time-series-insights-update-how-to-id.md) to *deviceIds*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Czas Series Insights w wersji zapoznawczej łączy tabeli (po spłaszczanie) podczas przeszukiwania. Tabela zawiera dodatkowe kolumny, takie jak **Type**.

| deviceId  | Typ | L1 | PAMIĘCI PODRĘCZNEJ L2 | sygnatura czasowa | Częstotliwość series_Flow ft3/s | series_Engine z użyciem nacisku oleju |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SYMULATOR | System baterii | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SYMULATOR |   System baterii |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA WSPÓLNE | SYMULATOR |    System baterii |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  Powyższa tabela przedstawia widok zapytania w [Eksploratorze wersji zapoznawczej](./time-series-insights-update-explorer.md).

**Wnioski**

* W poprzednim przykładzie właściwości statyczne są przechowywane w Time Series Insights wersji zapoznawczej, aby zoptymalizować dane przesyłane przez sieć.
* Time Series Insights dane podglądu są przyłączone w czasie zapytania za pomocą identyfikatora szeregów czasowych zdefiniowanego w wystąpieniu.
* Używane są dwie warstwy zagnieżdżania. Ta liczba jest największa, która obsługuje Time Series Insights w wersji zapoznawczej. Koniecznie można uniknąć głęboko zagnieżdżonych tablic.
* Ponieważ istnieje kilka miar, są one wysyłane jako osobne właściwości w ramach tego samego obiektu. W przykładzie jest to unikatowymi kolumnami **interfejsu psi Series_Flow rate**, **series_Engine psi**oraz **series_Flow rate ft3/s** .

>[!IMPORTANT]
> Pola wystąpienia nie są przechowywane przy użyciu telemetrii. Są one przechowywane z metadanymi w modelu szeregów czasowych.

#### <a name="example-2"></a>Przykład 2:

Weź pod uwagę następujące dane JSON:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

W powyższym przykładzie właściwość spłaszczone `data["flow"]` zaprezentuje kolizję nazw z właściwością `data_flow`.

W takim przypadku *Najnowsza* wartość właściwości spowoduje zastąpienie wcześniejszej wartości. 

> [!TIP]
> Skontaktuj się z zespołem Time Series Insights, aby uzyskać pomoc.

> [!WARNING] 
> * W przypadkach, gdy zduplikowane właściwości są obecne w tym samym (pojedynczo) ładunku zdarzenia z powodu spłaszczania lub innego mechanizmu, Najnowsza > wartość właściwości jest przechowywana, przez zapisanie wszystkich poprzednich wartości.
> * Seria połączonych zdarzeń nie przesłoni siebie nawzajem.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z zaleceniami, Przeczytaj [Azure Time Series Insights wersja zapoznawcza zapytania](./time-series-insights-query-data-csharp.md). Dowiesz się więcej na temat składni zapytania dla [interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview) usługi Time Series Insights Preview na potrzeby dostępu do danych.

* Połącz najlepsze rozwiązania JSON z [modelem szeregów czasowych](./time-series-insights-update-how-to-tsm.md).
