---
title: 'Szybki start: tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla języka C#'
titlesuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, wyszkol projekt i wykrywaj obiekty przy użyciu zestawu SDK dla platformy .NET i języka C#.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: 926e9feaa5061c84ce8de6d828da820e133700ce
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278864"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Szybki start: tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla platformy .NET

Ten artykuł zawiera informacje i przykładowy kod, dzięki którym można łatwiej rozpocząć tworzenie modeli wykrywania obiektów za pomocą zestawu Custom Vision SDK i języka C#. Po jego utworzeniu możesz dodać oznaczone regiony, przesłać obrazy, wyszkolić projekt, uzyskać adres URL domyślnego punktu końcowego przewidywania i użyć tego punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji platformy .NET. 

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobieranie zestawu Custom Vision SDK i przykładowego kodu
W celu napisania aplikacji .NET używającej usługi Custom Vision będą potrzebne pakiety NuGet usługi Custom Vision. Są one zawarte w przykładowym projekcie, który pobierzesz, ale możesz osobno uzyskać do nich dostęp w tym miejscu.

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Sklonuj lub pobierz projekt [Cognitive Services .NET Samples](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) (Przykłady dotyczące platformy .NET dla usług Cognitive Services). Przejdź do folderu **CustomVision/ObjectDetection** i otwórz plik ObjectDetection.csproj_ w programie Visual Studio.

Ten projekt programu Visual Studio tworzy nowy projekt Custom Vision o nazwie __My New Project__, który będzie dostępny za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie przekazuje obrazy do szkolenia i testowania modelu wykrywania obiektów. W tym projekcie model jest szkolony do wykrywania widelców i nożyczek na obrazach.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Zrozumienie kodu

Otwórz plik _Program.cs_ i przejrzyj kod. Wstaw klucze subskrypcji w odpowiednich definicjach w metodzie **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

### <a name="create-a-new-custom-vision-service-project"></a>Tworzenie nowego projektu Custom Vision Service

Następny fragment kodu tworzy projekt wykrywania obiektów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Oznaczając tagami obrazy w projektach wykrywania obiektów, należy określić region każdego otagowanego obiektu za pomocą znormalizowanych współrzędnych. Następujący kod kojarzy poszczególne przykładowe obrazy z ich otagowanymi regionami.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

Następnie to mapowanie skojarzeń jest używane, aby przekazać każdy przykładowy obraz z jego współrzędnymi regionu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

W tym momencie wszystkie przykładowe obrazy zostały przekazane, a każdy z nich ma tag (**widelec** lub **nożyczki**) i skojarzony prostokąt pikseli dla tego tagu.

### <a name="train-the-project"></a>Szkolenie projektu

Ten kod tworzy pierwszą iterację szkolenia w projekcie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="set-the-current-iteration-as-default"></a>Ustawianie bieżącej iteracji jako domyślnej

Ten kod oznacza bieżącą iterację jako domyślną. Iteracja domyślna odzwierciedla wersję modelu, która będzie odpowiadać na żądania przewidywania. Należy ją zaktualizować przy każdym ponownym szkoleniu modelu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=119-124)]

### <a name="create-a-prediction-endpoint"></a>Tworzenie punktu końcowego przewidywania

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=126-131)]

### <a name="use-the-prediction-endpoint"></a>Używanie punktu końcowego przewidywania

Ta część skryptu ładuje obraz testowy, wysyła zapytanie do punktu końcowego modelu oraz przekazuje dane przewidywania do konsoli.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=133-145)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po uruchomieniu aplikacja powinna otworzyć okno konsoli i zapisać w nim następujące dane wyjściowe:

```
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```
Możesz następnie sprawdzić, czy obraz testowy (znajdujący się w folderze **Images/Test/**) został odpowiednio otagowany i czy region wykrywania jest poprawny. W tym momencie możesz nacisnąć dowolny klawisz, aby zakończyć działanie aplikacji.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak można zrealizować w kodzie poszczególne kroki procesu wykrywania obiektów. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)