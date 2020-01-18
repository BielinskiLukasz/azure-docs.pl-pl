---
title: 'Szybki Start: Tworzenie projektu wykrywania obiektów przy użyciu zestawu SDK C# dla Custom Vision'
titleSuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, wyszkol projekt i wykrywaj obiekty przy użyciu zestawu SDK dla platformy .NET i języka C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: c6aaf69ba3ed682a00a203079b024a47121334e3
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170065"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Szybki start: tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla platformy .NET

W tym artykule pokazano, jak rozpocząć pracę z zestawem SDK Custom Vision C# w programie w celu utworzenia modelu wykrywania obiektów. Po jego utworzeniu możesz dodać oznaczone regiony, przesłać obrazy, wyszkolić projekt, uzyskać adres URL domyślnego punktu końcowego przewidywania i użyć tego punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji platformy .NET. 

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobieranie zestawu Custom Vision SDK i przykładowego kodu

W celu napisania aplikacji .NET używającej usługi Custom Vision będą potrzebne pakiety NuGet usługi Custom Vision. Te pakiety są zawarte w przykładowym projekcie, który zostanie pobrany, ale możesz uzyskać do nich dostęp osobno.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Sklonuj lub pobierz projekt [Cognitive Services .NET Samples (Przykłady dotyczące platformy .NET dla usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Przejdź do folderu **CustomVision/ObjectDetection** i otwórz plik _ObjectDetection.csproj_ w programie Visual Studio.

Ten projekt programu Visual Studio tworzy nowy projekt Custom Vision o nazwie __My New Project__, który będzie dostępny za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie przekazuje obrazy do szkolenia i testowania modelu wykrywania obiektów. W tym projekcie model jest szkolony do wykrywania widelców i nożyczek na obrazach.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Zrozumienie kodu

Otwórz plik _Program.cs_ i przejrzyj kod. [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla kluczy szkoleń i prognoz o nazwach odpowiednio `CUSTOM_VISION_TRAINING_KEY` i `CUSTOM_VISION_PREDICTION_KEY`. Skrypt będzie wyglądał na te zmienne.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Ponadto Pobierz adres URL punktu końcowego ze strony Ustawienia w witrynie sieci Web Custom Vision. Zapisz ją w zmiennej środowiskowej o nazwie `CUSTOM_VISION_ENDPOINT`. Skrypt zapisuje odwołanie do niego w katalogu głównym klasy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Tworzenie nowego projektu Custom Vision Service

Następny fragment kodu tworzy projekt wykrywania obiektów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku tworzenia portalu sieci Web dla [czujnika](get-started-build-detector.md) ), zobacz metodę " [setproject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) ".  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Oznaczając tagami obrazy w projektach wykrywania obiektów, należy określić region każdego otagowanego obiektu za pomocą znormalizowanych współrzędnych. Następujący kod kojarzy poszczególne przykładowe obrazy z ich otagowanymi regionami.

> [!NOTE]
> Jeśli nie masz narzędzia do klikania i przeciągania, aby oznaczyć współrzędne regionów, możesz użyć internetowego interfejsu użytkownika w [Customvision.AI](https://www.customvision.ai/). W tym przykładzie współrzędne zostały już podane.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Następnie to mapowanie skojarzeń jest używane, aby przekazać każdy przykładowy obraz z jego współrzędnymi regionu. Można przekazać do 64 obrazów w pojedynczej partii.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

W tym momencie zostały przekazane wszystkie obrazy próbek i otagowane każde z nich (**rozwidlenie** lub **nożyczki**) za pomocą skojarzonego prostokąta pikseli.

### <a name="train-the-project"></a>Szkolenie projektu

Ten kod tworzy pierwszą iterację szkolenia w projekcie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Tworzenie punktu końcowego przewidywania

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Używanie punktu końcowego przewidywania

Ta część skryptu ładuje obraz testowy, wysyła zapytanie do punktu końcowego modelu oraz przekazuje dane przewidywania do konsoli.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po uruchomieniu aplikacja powinna otworzyć okno konsoli i zapisać w nim następujące dane wyjściowe:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Możesz następnie sprawdzić, czy obraz testowy (znajdujący się w folderze **Images/Test/** ) został odpowiednio otagowany i czy region wykrywania jest poprawny. W tym momencie możesz nacisnąć dowolny klawisz, aby zakończyć działanie aplikacji.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak wykonać każdy krok procesu wykrywania obiektów w kodzie. Ten przykład wykonuje pojedynczą iterację szkoleniową, ale często należy przeprowadzić uczenie i testowanie modelu wiele razy, aby zwiększyć jego dokładność. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
