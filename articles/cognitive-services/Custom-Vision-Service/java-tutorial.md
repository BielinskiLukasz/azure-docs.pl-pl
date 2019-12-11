---
title: 'Szybki start: tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Java'
titleSuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, naucz projekt i wykonaj przewidywanie przy użyciu zestawu Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 9e4ac74df269f2e3db2431a3f01490a5769c9aca
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970248"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Szybki start: tworzenie projektu klasyfikacji obrazów przy użyciu zestawu Custom Vision SDK dla języka Java

W tym artykule pokazano, jak rozpocząć pracę z zestawem SDK języka Java Custom Vision, aby skompilować model klasyfikacji obrazów. Po jej utworzeniu możesz dodać tagi, przesłać obrazy, wyszkolić projekt, uzyskać adres URL punktu końcowego domyślnego przewidywania projektu i użyć punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji języka Java. Jeśli chcesz przejść przez proces tworzenia i używania modelu klasyfikacji _bez_ kodu, zobacz zamiast tego [wskazówki dotyczące przeglądarki](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Wybrane środowisko IDE Java
- Zainstalowany zestaw [JDK 7 lub 8](https://aka.ms/azure-jdks).
- Zainstalowane narzędzie Maven
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobieranie zestawu Custom Vision SDK i przykładowego kodu

Do napisania aplikacji języka Java używającej usługi Custom Vision potrzebne są pakiety Maven usługi Custom Vision. Te pakiety są zawarte w przykładowym projekcie, który zostanie pobrany, ale możesz uzyskać do nich dostęp osobno.

Pakiet SDK Custom Vision można zainstalować z centralnego repozytorium Maven:

- [Pakiet SDK do szkolenia](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Pakiet SDK do przewidywania](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Sklonuj lub pobierz projekt [Cognitive Services Java SDK Samples (Przykłady dotyczące zestawu Cognitive Services Java SDK)](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Przejdź do folderu **Vision/CustomVision/** .

Ten projekt Java za pomocą usługi Custom Vision tworzy nowy projekt klasyfikacji obrazów o nazwie __Sample Java Project__, do którego można uzyskać dostęp za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. W tym projekcie klasyfikator jest używany do ustalania, czy drzewo jest __choiną__ (Hemlock), czy __wiśnią japońską__ (Japanese Cherry).

[!INCLUDE [get-keys](includes/get-keys.md)]

Program jest skonfigurowany do przechowywania danych kluczy jako zmiennych środowiskowych. Ustaw te zmienne, przechodząc do folderu **Vision/CustomVision** w programie PowerShell. Następnie wprowadź poniższe polecenia:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Zrozumienie kodu

Załaduj projekt `Vision/CustomVision` w środowisku IDE Java i otwórz plik _CustomVisionSamples.java_. Znajdź metodę **runSample** i Dodaj komentarz do wywołania metody **ObjectDetection_Sample** ,&mdash;ta metoda wykonuje scenariusz wykrywania obiektów, który nie jest uwzględniony w tym przewodniku. Metoda **ImageClassification_Sample** implementuje podstawowe funkcje tego przykładu. Przejdź do jej definicji i przejrzyj kod.

### <a name="create-a-custom-vision-service-project"></a>Tworzenie projektu Custom Vision Service

Ten pierwszy fragment kodu tworzy projekt klasyfikacji obrazów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Zobacz przeciążania metod tworzenia [projektu](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) , aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku po portalu sieci Web [klasyfikatora](getting-started-build-a-classifier.md) ).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Tworzenie tagów w projekcie

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Przykładowe obrazy znajdują się w folderze **src/main/resources** projektu. Są one odczytywane z tego miejsca i przekazywane do usługi wraz z odpowiednimi tagami.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

W poprzednim fragmencie kodu są używane dwie funkcje pomocnika, które pobierają obrazy jako strumienie zasobów i przekazują je do usługi (w jednej partii można przekazać do 64 obrazów).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>Uczenie klasyfikatora i publikowanie

Ten kod tworzy pierwszą iterację w projekcie, a następnie publikuje tę iterację w punkcie końcowym przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>Używanie punktu końcowego przewidywania

Punkt końcowy przewidywania, reprezentowany tutaj przez obiekt `predictor`, jest odwołaniem umożliwiającym przesyłanie obrazu do bieżącego modelu i uzyskiwanie przewidywania klasyfikacji. W tym przykładzie element `predictor` jest zdefiniowany w innym miejscu przy użyciu zmiennej środowiskowej klucza przewidywania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby skompilować i uruchomić rozwiązanie przy użyciu narzędzia Maven, uruchom następujące polecenie w katalogu projektu w programie PowerShell:

```powershell
mvn compile exec:java
```

Dane wyjściowe aplikacji w konsoli powinny być podobne do poniższego tekstu:

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Następnie możesz sprawdzić, czy przewidywanie dla obrazu testowego (kilka ostatnich wierszy danych wyjściowych) jest poprawne.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak wykonać każdy krok procesu klasyfikacji obrazów przy użyciu kodu. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
