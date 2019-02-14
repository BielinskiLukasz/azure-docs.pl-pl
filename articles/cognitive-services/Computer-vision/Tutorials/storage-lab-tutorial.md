---
title: 'Samouczek: generowanie metadanych dla obrazów usługi Azure Storage'
titleSuffix: Azure Cognitive Services
description: W tym samouczku dowiesz się, jak zintegrować usługę przetwarzania obrazów platformy Azure z aplikacją internetową, aby generować metadane dla obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: pafarley
ms.openlocfilehash: 650696d1eb3979447bffa7312e91b4fe1a57652c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865016"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Samouczek: generowanie metadanych obrazów w usłudze Azure Storage przy użyciu przetwarzania obrazów

W tym samouczku dowiesz się, jak zintegrować usługę przetwarzania obrazów platformy Azure z aplikacją internetową, aby generować metadane dla przekazanych obrazów. Pełny przewodnik dotyczący aplikacji można znaleźć w [laboratorium usług Azure Storage i Cognitive Services](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) na platformie GitHub — ten samouczek dotyczy ćwiczenia 5 z tego laboratorium. Możesz utworzyć całą aplikację, wykonując każdy krok od początku do końca — jeśli jednak chcesz tylko dowiedzieć się, jak zintegrować przetwarzanie obrazów z istniejącą aplikacją internetową, przeczytaj ten artykuł.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasobu przetwarzania obrazów na platformie Azure
> * Wykonywanie analizy obrazu na obrazach usługi Azure Storage
> * Dołączanie metadanych do obrazów usługi Azure Storage
> * Sprawdzanie metadanych obrazów za pomocą Eksploratora usługi Azure Storage

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 

## <a name="prerequisites"></a>Wymagania wstępne

- Program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) lub nowszy z zainstalowanymi pakietami roboczymi „Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych” oraz „Tworzenie aplikacji na platformie Azure”.
- Konto usługi Azure Storage z kontenerem obiektów blob przydzielonym dla obrazów (wykonaj [ćwiczenie 1 z laboratorium usługi Azure Storage](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1), jeśli potrzebujesz pomocy dotyczącej tego kroku).
- Narzędzie Eksplorator usługi Azure Storage (wykonaj [ćwiczenie 2 z laboratorium usługi Azure Storage](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2), jeśli potrzebujesz pomocy dotyczącej tego kroku).
- Aplikacja internetowa platformy ASP.NET z dostępem do usługi Azure Storage (wykonaj [ćwiczenie 3 z laboratorium usługi Azure Storage](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3), aby szybko utworzyć taką aplikację).

## <a name="create-a-computer-vision-resource"></a>Tworzenie zasobu przetwarzania obrazów

Musisz utworzyć zasób przetwarzania obrazów dla swojego konta platformy Azure. Ten zasób zarządza Twoim dostępem do usługi przetwarzania obrazów platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com) i kliknij pozycję **Utwórz zasób**, a następnie pozycje **SI oraz usługa Machine Learning** i **Przetwarzanie obrazów**.

    ![Tworzenie nowej subskrypcji interfejsu API przetwarzania obrazów](../Images/new-vision-api.png)

1. W oknie dialogowym wprowadź ciąg „vision-api-key” w polu **Nazwa** i wybierz opcję **F0** w polu **Warstwa cenowa**. W obszarze **Lokalizacja** wybierz taką samą opcję, jak podczas konfigurowania konta usługi Azure Storage. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej** i także wybierz tę samą grupę zasobów. Zaznacz pole wyboru **Potwierdzam**, a następnie kliknij przycisk **Utwórz**.

    ![Subskrybowanie interfejsu API przetwarzania obrazów](../Images/create-vision-api.png)

1. Wróć do menu grupy zasobów i kliknij przycisk subskrypcji interfejsu API przetwarzania obrazów, która została właśnie utworzona. Skopiuj adres URL z obszaru **Punkt końcowy** do miejsca, z którego będzie go można za chwilę łatwo uzyskać. Następnie kliknij pozycję **Pokaż klucze dostępu**.

    ![Wyświetlanie kluczy dostępu](../Images/copy-vision-endpoint.png)

1. W następnym oknie skopiuj wartość **KLUCZ 1** do schowka.

    ![Kopiowanie klucza dostępu](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Dodawanie poświadczeń przetwarzania obrazów

Następnie dodasz do aplikacji wymagane poświadczenia, aby mogła uzyskiwać dostęp do zasobów przetwarzania obrazów.

Otwórz aplikację internetową platformy ASP.NET w programie Visual Studio i przejdź do pliku **Web.config** w folderze głównym projektu. Dodaj następujące instrukcje do sekcji `<appSettings>` w pliku, zastępując ciąg `VISION_KEY` kluczem skopiowanym w poprzednim kroku, a ciąg `VISION_ENDPOINT` adresem URL zapisanym w jeszcze wcześniejszym kroku.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Następnie w Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i użyj polecenia **Zarządzaj pakietami NuGet**, aby zainstalować pakiet **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Ten pakiet zawiera typy potrzebne do wywołania interfejsu API przetwarzania obrazów.

## <a name="add-metadata-generation-code"></a>Dodawanie kodu generowania metadanych

Następnie dodasz kod, który faktycznie wykorzystuje usługę przetwarzania obrazów do tworzenia metadanych dla obrazów. Te kroki dotyczą aplikacji platformy ASP.NET z laboratorium, ale możesz dostosować je do własnej aplikacji. W tym momencie ważne jest, że masz aplikację internetową platformy ASP.NET, która potrafi przekazywać obrazy do kontenera usługi Azure Storage, odczytywać obrazy z tego kontenera i wyświetlać je w widoku. Jeśli masz jakiekolwiek wątpliwości, najlepiej wykonaj [ćwiczenie 3 z laboratorium usługi Azure Storage](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Otwórz plik *HomeController.cs* w folderze **Kontrolery** projektu i dodaj następujące instrukcje `using` w górnej części pliku:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Następnie przejdź do metody **Upload**. Ta metoda konwertuje obrazy i przekazuje je do magazynu obiektów blob. Dodaj następujący kod bezpośrednio za blokiem, który rozpoczyna się od ciągu `// Generate a thumbnail` (lub na końcu procesu image-blob-creation). Ten kod pobiera obiekt blob zawierający obraz (`photo`) i używa usługi przetwarzania obrazów, aby wygenerować opis tego obrazu. Interfejs API przetwarzania obrazów generuje także listę słów kluczowych dla obrazu. Wygenerowany opis i słowa kluczowe są przechowywane w metadanych obiektu blob, aby można było je później pobrać.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Następnie przejdź do metody **Index** w tym samym pliku. Ta metoda numeruje przechowywane obiekty blob obrazów w docelowym kontenerze obiektów blob (jako wystąpienia **IListBlobItem**) i przekazuje je do widoku aplikacji. Zastąp blok `foreach` w tej metodzie następującym kodem. Ten kod wywołuje funkcję **CloudBlockBlob.FetchAttributes**, aby pobrać metadane dołączone do każdego obiektu blob. Wyodrębnia z metadanych opis wygenerowany komputerowo (`caption`) i dodaje go do obiektu **BlobInfo**, który jest przekazywany do widoku.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Testowanie aplikacji

Zapisz wprowadzone zmiany w programie Visual Studio i naciśnij klawisze **Ctrl+F5**, aby uruchomić aplikację w przeglądarce. Użyj aplikacji, aby przekazać kilka obrazów z folderu „photos” w zasobach laboratorium lub z własnego folderu. Kiedy umieścisz kursor na jednym z obrazów w widoku, powinno zostać wyświetlone okno etykietki narzędzia z wygenerowanym komputerowo podpisem obrazu.

![Podpis wygenerowany przez komputer](../Images/thumbnail-with-tooltip.png)

Aby wyświetlić wszystkie dołączone metadane, użyj Eksploratora usługi Azure Storage w celu wyświetlenia kontenera magazynu, którego używasz na potrzeby obrazów. Kliknij prawym przyciskiem myszy dowolny obiekt blob w kontenerze i wybierz pozycję **Właściwości**. W oknie dialogowym zobaczysz listę par klucz-wartość. Wygenerowany komputerowo opis obrazu jest przechowywany w elemencie „Caption”, a słowa kluczowe wyszukiwania są przechowywane w elementach „Tag0”, „Tag1” i tak dalej. Gdy skończysz, kliknij przycisk **Anuluj**, aby zamknąć okno dialogowe.

![Metadane obiektu blob](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować pracę nad swoją aplikacją internetową, zobacz sekcję [Następne kroki](#next-steps). Jeśli nie chcesz dalej używać tej aplikacji, usuń wszystkie zasoby specyficzne dla aplikacji. Aby to zrobić, możesz po prostu usunąć grupę zasobów, która zawiera Twoją subskrypcję usługi Azure Storage i zasób usługi przetwarzania obrazów. Spowoduje to usunięcie konta magazynu, przekazanych do niego obiektów blob i zasobu usługi App Service wymaganego do nawiązywania połączenia z aplikacją internetową platformy ASP.NET. 

Aby usunąć grupę zasobów, otwórz blok **Grupy zasobów** w portalu, przejdź do grupy zasobów używanej na potrzeby tego projektu, a następnie kliknij pozycję **Usuń grupę zasobów** u góry widoku. Zostanie wyświetlony monit o podanie nazwy grupy zasobów w celu potwierdzenia, że chcesz ją usunąć, ponieważ po usunięciu grupy zasobów nie można jej odzyskać.

## <a name="next-steps"></a>Następne kroki

W tym samouczku usługa przetwarzania obrazów platformy Azure została zintegrowana z istniejącą aplikacją internetową w celu automatycznego generowania podpisów i słów kluczowych dla obrazów obiektów blob podczas ich przekazywania. Następnie zapoznaj się z ćwiczeniem 6 w laboratorium usługi Azure Storage, aby dowiedzieć się, jak dodać funkcję wyszukiwania do aplikacji internetowej. Ta funkcja wykorzystuje słowa kluczowe, które generuje usługa przetwarzania obrazów.

> [!div class="nextstepaction"]
> [Dodawanie funkcji wyszukiwania do aplikacji](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
