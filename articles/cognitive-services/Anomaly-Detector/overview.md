---
title: Co to jest interfejs API narzędzia do wykrywania anomalii?
titleSuffix: Azure Cognitive Services
description: Użyj zaawansowanych algorytmów interfejsu API wykrywania anomalii, aby identyfikować anomalie w danych szeregów czasowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 3bd60ff1e732940bbb13c2e224084cf7e331266b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934877"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co to jest interfejs API narzędzia do wykrywania anomalii?

Interfejs API wykrywania anomalii umożliwia monitorowanie i wykrywanie anomalii w danych szeregów czasowych przy użyciu uczenia maszynowego. Interfejs API wykrywania anomalii dostosowuje się przez automatyczne identyfikowanie i stosowanie modeli najlepiej dopasowane do danych, niezależnie od wielkości branżowej, scenariusza lub ilości danych. Korzystając z danych szeregów czasowych, interfejs API określa granice wykrywania anomalii, oczekiwane wartości i które punkty danych są anomaliami.

![Wykrywanie zmian wzorca w żądaniach obsługi](./media/anomaly_detection2.png)

Korzystanie z detektora anomalii nie wymaga wcześniejszego doświadczenia w uczeniu maszynowym, a interfejs API RESTful umożliwia łatwą integrację usługi z aplikacjami i procesami.

## <a name="features"></a>Funkcje

Dzięki detektorowi anomalii można automatycznie wykrywać anomalie w danych szeregów czasowych lub w miarę ich występowania w czasie rzeczywistym. 

|Cecha  |Opis  |
|---------|---------|
|Wykrywaj anomalie w czasie rzeczywistym. | Wykrywaj anomalie w danych przesyłanych strumieniowo, używając wcześniej zaobserwowanych punktów danych w celu ustalenia, czy Najnowsza z nich jest nieaktualna. Ta operacja generuje model przy użyciu wysyłanych punktów danych i określa, czy punkt docelowy jest anomalią. Wywołując interfejs API z każdym wygenerowanym nowym punktem danych, można monitorować dane w miarę ich tworzenia. |
|Wykrywaj anomalie w zestawie danych jako Partia zadań. | Skorzystaj z szeregów czasowych, aby wykryć ewentualne anomalie, które mogą istnieć w danych. Ta operacja generuje model przy użyciu wszystkich danych szeregów czasowych, przy czym każdy punkt jest analizowany z tym samym modelem.         |
| Uzyskaj dodatkowe informacje na temat danych. | Uzyskaj przydatne szczegóły dotyczące danych i wszelkich obserwowanych anomalii, w tym oczekiwanych wartości, granic anomalii i pozycji. |
| Dostosuj granice wykrywania anomalii. | Interfejs API wykrywania anomalii automatycznie tworzy granice na potrzeby wykrywania anomalii. Dostosuj te granice, aby zwiększyć lub zmniejszyć czułość interfejsu API na anomalie danych i lepiej dopasować dane. |

## <a name="demo"></a>Pokaz

Zapoznaj się z tą [interaktywną prezentacją](https://aka.ms/adDemo) , aby dowiedzieć się, jak działa detektor anomalii.
Aby uruchomić demonstrację, należy utworzyć zasób wykrywania anomalii i uzyskać klucz interfejsu API i punkt końcowy.

## <a name="notebook"></a>Notes

Aby dowiedzieć się, jak wywołać interfejs API wykrywania anomalii, Wypróbuj ten [Notes platformy Azure](https://aka.ms/adNotebook). Ta Jupyter Notebook hostowana w sieci Web pokazuje, jak wysłać żądanie interfejsu API i wizualizować wynik.

Aby uruchomić Notes, wykonaj następujące czynności:

1. Pobierz prawidłowy klucz subskrypcji interfejsu API wykrywania anomalii i punkt końcowy interfejsu API. W poniższej sekcji znajdują się instrukcje dotyczące rejestrowania się.
1. Zaloguj się, a następnie kliknij pozycję Klonuj w prawym górnym rogu.
1. Usuń zaznaczenie opcji "publiczny" w oknie dialogowym przed ukończeniem operacji klonowania, w przeciwnym razie Notes, w tym wszystkie klucze subskrypcji, będzie publiczny.
1. Kliknij pozycję **Uruchom przy bezpłatnej obliczeń**
1. Wybierz jeden z notesów.
1. Dodaj do `subscription_key` zmiennej prawidłowy klucz subskrypcji interfejsu API wykrywania anomalii. 
1. `endpoint` Zmień zmienną na punkt końcowy. Na przykład: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na górnym pasku menu kliknij **komórkę**, a następnie **Uruchom wszystkie**.

## <a name="workflow"></a>Przepływ pracy

Interfejs API wykrywania anomalii to usługa sieci Web RESTful, ułatwiająca wywoływanie z dowolnego języka programowania, który może wykonywać żądania HTTP i analizować dane JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Po zarejestrowaniu:

1. Zrób dane szeregów czasowych i Przekształć je w prawidłowy format JSON. Podczas przygotowywania danych należy stosować [najlepsze rozwiązania](concepts/anomaly-detection-best-practices.md) w celu uzyskania najlepszych wyników.
1. Wyślij żądanie do interfejsu API wykrywania anomalii z danymi.
1. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="algorithms"></a>Algorytmy

* Zapoznaj się z tym blogiem technicznym dotyczącym [interfejsu API wykrywania anomalii platformy Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162) na temat algorytmów pod okapem.
* Zapoznaj się z tym papierem [usługi wykrywania anomalii w szeregach czasowych w firmie Microsoft](https://arxiv.org/abs/1906.03821) (zaakceptowanej przez KDD 2019) dla algorytmów SR-CNN opracowanych przez firmę Microsoft.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Dołącz do społeczności wykrywania anomalii

* Dołącz do [grupy doradcy wykrywania anomalii w usłudze Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Zobacz wybraną [zawartość wygenerowaną przez użytkownika](user-generated-content.md)

## <a name="next-steps"></a>Następne kroki

* [Szybki start: Wykrywaj anomalie w danych szeregów czasowych przy użyciu interfejsu API REST usługi wykrywania anomalii](quickstarts/detect-data-anomalies-csharp.md)
* [Demonstracja online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) interfejsu API wykrywania anomalii
* [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) usługi wykrywania anomalii
