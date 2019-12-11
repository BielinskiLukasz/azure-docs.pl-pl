---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/06/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: be46a0dda24dc990e895a3e983c730337f9cf31d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981279"
---
### <a name="general-availability-and-preview-comparison"></a>Ogólna dostępność i porównanie wersji zapoznawczej

Poniższa tabela zawiera podsumowanie kilku najważniejszych różnic między ogólnie dostępnymi Azure Time Series Insights a wystąpieniami wersji zapoznawczej.

| | Ogólna dostępność | Wersja zapoznawcza |
| --- | --- | ---|
| Obywatel pierwszej klasy | Zorientowane na zdarzenia | Zorientowane na serie czasowe |
| Powód semantyczny | Niskiego poziomu (dane referencyjne) | Wysoki poziom (modele) |
| Contextualization danych | Na poziomie innym niż urządzenie | Urządzenie i nie na poziomie urządzenia |
| Magazyn logiki obliczeniowej | Nie | Przechowywane w modelu typu zmienne części |
| Magazyn i kontrola dostępu | Nie | Włączone za pośrednictwem modelu |
| Agregacje/próbkowanie | Nie | Ważone zdarzenie i ważone czasowo |
| Odtwarzanie sygnału | Nie | Interpolacji |
| Produkcja pochodnych szeregów czasowych | Nie | Tak, scalenia i sprzężenia |
| Elastyczność języka | Bez możliwości tworzenia | Składanie |
| Język wyrażeń | Ciąg predykatu | Wyrażenia szeregów czasowych (ciągi predykatów, wartości, wyrażenia i funkcje) |

### <a name="property-limits"></a>Limity właściwości

Limity właściwości Time Series Insights wzrosły do 1 000 z maksymalną ilością 800 w przypadku. Podane właściwości zdarzenia mają odpowiednie kolumny JSON, CSV i wykresu, które można wyświetlić w [eksploratorze Time Series Insights w wersji zapoznawczej](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| JSZ | Właściwości maksymalne |
| --- | --- |
| Podgląd PAYG | Właściwości 1 000 (kolumny) |
| GA S1 | Właściwości 600 (kolumny) |
| GA S2 | Właściwości 800 (kolumny) |

### <a name="event-sources"></a>Źródła zdarzeń

Obsługiwane są maksymalnie dwa źródła zdarzeń na wystąpienie. 

* Dowiedz się, jak [dodać Źródło centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Skonfiguruj [Źródło Centrum IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limity interfejsu API

Limity interfejsu API REST dla programu Time Series Insights Preview są określone w [dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits).