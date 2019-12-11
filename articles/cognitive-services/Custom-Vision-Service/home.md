---
title: Co to jest usługa Custom Vision?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą usługi Custom Vision kompilować klasyfikatory obrazów niestandardowych w chmurze platformy Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 829921ae5600873fdc8f2946ae3ff355bffb06a8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978598"
---
# <a name="what-is-custom-vision"></a>Co to jest usługa Custom Vision?

Usługa Custom Vision to usługa poznawcza umożliwiająca tworzenie, wdrażanie i ulepszanie własnych klasyfikatorów obrazów. Klasyfikator obrazów to usługa sztucznej Inteligencji, która stosuje etykiety (reprezentujące _klasy_) do obrazów zgodnie z ich cechami wizualnymi. W odróżnieniu od usługi [przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) usługa Custom Vision umożliwia określenie etykiet do stosowania.

## <a name="what-it-does"></a>Wyniki działania

Usługa Custom Vision stosuje etykiety do obrazów przy użyciu algorytmu uczenia maszynowego. Jako deweloper musisz przesłać grupy obrazów, które zawierają określone cechy lub ich nie posiadają. Etykiety obrazów należy określić samodzielnie podczas przesyłania. Następnie algorytm uczy się na podstawie tych danych i oblicza swoją dokładność, wykonując na sobie test w oparciu o te same obrazy. Po zakończeniu trenowania algorytmu można go przetestować, nauczyć ponownie lub ewentualnie użyć do klasyfikowania nowych obrazów zgodnie z potrzebami aplikacji. Możesz także wyeksportować sam model do użytku w trybie offline.

### <a name="classification-and-object-detection"></a>Klasyfikacja i wykrywanie obiektów

Funkcje usługi Custom Vision można podzielić na dwie kategorie. **Klasyfikacja obrazów** stosuje jedną lub więcej etykiet do obrazu. **Wykrywanie obiektów** jest podobne, ale zwraca także współrzędne na obrazie, których dotyczą zastosowane etykiety.

### <a name="optimization"></a>Optymalizacja

Usługa Custom Vision została zoptymalizowana pod kątem szybkiego rozpoznawania głównych różnic między obrazami, dlatego można rozpocząć prototypowanie modelu przy użyciu niewielkiej ilości danych. 50 obrazów na etykietę to zazwyczaj dobry początek. Jednak usługa nie jest optymalna do wykrywania delikatnych różnic w obrazach (na przykład w przypadku wykrywania drobnych pęknięć lub zwiększania wcięcia w scenariuszach zapewniania jakości).

Ponadto możesz wybrać spośród kilku różnych typów algorytmu usługi Custom Vision, które są zoptymalizowane pod kątem obrazów z określonymi materiałami &mdash;, na przykład elementami krajobrazu lub produktami sprzedaży detalicznej. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący [tworzenia klasyfikatora](getting-started-build-a-classifier.md) .

## <a name="what-it-includes"></a>Co zawiera

Usługa Custom Vision jest dostępna jako zestaw natywnych zestawów SDK oraz za pośrednictwem interfejsu opartego na Internecie na [stronie głównej usługi Custom Vision](https://customvision.ai/). Możesz tworzyć, testować i trenować model za pośrednictwem jednego z tych dwóch interfejsów lub obu jednocześnie.

![Strona główna usługi Custom Vision w oknie przeglądarki Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Custom Vision powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z przewodnikiem [Tworzenie klasyfikatora](getting-started-build-a-classifier.md), aby rozpocząć korzystanie z usługi Custom Vision w Internecie, lub ukończ [Samouczek klasyfikacji obrazów](csharp-tutorial.md), aby zaimplementować podstawowy scenariusz w kodzie.
