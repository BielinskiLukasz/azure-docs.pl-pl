---
title: 'Szybki start: generowanie miniatury — REST, cURL'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu biblioteki cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 350bc95e08aa994e4cb70db6bf1f08d53bbec5a3
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177300"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Szybki Start: generowanie miniatury przy użyciu interfejsu API REST przetwarzanie obrazów i zwinięcie

W tym przewodniku szybki start utworzysz miniaturę obrazu przy użyciu interfejsu API REST przetwarzanie obrazów. Należy określić wymaganą wysokość i szerokość, która może się różnić w zależności od aspektu obrazu wejściowego. Przetwarzanie obrazów korzysta z inteligentnego przycinania, aby inteligentnie identyfikować obszar zainteresowania i generować współrzędne przycinania wokół tego regionu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć bibliotekę [cURL](https://curl.haxx.se/windows).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz wersji próbnej z usługi [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować przetwarzanie obrazów i uzyskać klucz.

## <a name="get-thumbnail-request"></a>Żądanie Get Thumbnail

[Metoda Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) umożliwia wygenerowanie miniatury obrazu.

Aby uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujący kod do edytora.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Zastąp ciąg `<File>` ścieżką i nazwę pliku, aby zapisać miniaturę.
1. Zmień adres URL żądania (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.1`), aby użyć lokalizacji, w której uzyskano klucze subskrypcji, jeśli jest to konieczne.
1. Opcjonalnie możesz zmienić obraz (`{\"url\":\"...`) do analizy.
1. Otwórz okno polecenia na komputerze z zainstalowanym programem cURL.
1. Wklej kod w oknie, a następnie uruchom polecenie.

>[!NOTE]
>Lokalizacja użyta w wywołaniu REST i lokalizacja użyta do otrzymania kluczy subskrypcji muszą być takie same. Jeśli na przykład uzyskano klucze subskrypcji z lokalizacji westus, zastąp ciąg „westcentralus” w poniższym adresie URL ciągiem „westus”.

## <a name="create-and-run-the-sample-command"></a>Tworzenie i uruchamianie przykładowego polecenia

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujące polecenie do edytora tekstów.
1. W razie potrzeby wprowadź w poleceniu następujące zmiany:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
    1. Zastąp wartość `<thumbnailFile>` ścieżką i nazwą pliku, w którym chcesz zapisać miniaturę.
    1. Zastąp pierwszą część adresu URL żądania (`westcentralus`) tekstem w adresie URL własnego punktu końcowego.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcjonalnie zmień adres URL obrazu w treści żądania (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adres URL innego obrazu do analizy, z którego chcesz wygenerować miniaturę.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstów w oknie wiersza polecenia, a następnie uruchom polecenie.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź powoduje zapisanie obrazu miniatury w pliku określonym w `<thumbnailFile>`. Jeśli żądanie zakończy się niepowodzeniem, w odpowiedzi zostanie wyświetlony kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak. Jeśli żądanie powiodło się, ale utworzona Miniatura nie jest prawidłowym plikiem obrazu, może to oznaczać, że klucz subskrypcji jest nieprawidłowy.

## <a name="next-steps"></a>Następne kroki

Eksploruj interfejs API przetwarzania obrazów w celu analizowania obrazu, wykrywania osobistości i punktów orientacyjnych, tworzenia miniatury oraz wyodrębniania tekstu napisanego i odręcznego. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
