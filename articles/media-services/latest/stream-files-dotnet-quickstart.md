---
title: Przesyłanie strumieniowe plików wideo za pomocą usługi Azure Media Services — .NET | Microsoft Docs
description: Wykonaj kroki tego przewodnika Szybki start, aby utworzyć nowe konto usługi Azure Media Services, zakodować plik i przesłać go strumieniowo do usługi Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 3a50d78645630e499b11f012da122b12b026ae6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466871"
---
# <a name="quickstart-stream-video-files---net"></a>Szybki start: Przesyłanie strumieniowe plików wideo — .NET

Ten przewodnik Szybki start pokazuje, jak łatwo jest zakodować i rozpocząć strumieniowe przesyłanie wideo do różnych przeglądarek i urządzeń za pomocą usługi Azure Media Services. Zawartość wejściową można określić przy użyciu adresów URL HTTP, adresów URL SAS lub ścieżek do plików znajdujących się w magazynie obiektów blob platformy Azure.
W przykładzie w tym temacie kodowana jest zawartość, która jest udostępniana za pośrednictwem adresu URL protokołu HTTPS. Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS.

Na końcu tego przewodnika Szybki start będziesz umieć przesyłać strumieniowo filmy wideo.  

![Odtwarzanie wideo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz zainstalowanego programu Visual Studio, możesz pobrać program [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).<br/>Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.
- Postępuj zgodnie z instrukcjami zawartymi w temacie [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Uzyskiwanie dostępu do interfejsu API usług Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure) i zapisz poświadczenia. Będą one potrzebne w celu uzyskania dostępu do interfejsu API.

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładu

Sklonuj repozytorium GitHub zawierające przykład przesyłania strumieniowego platformy .NET na swoją maszynę za pomocą następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Przykład znajduje się w folderze [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Otwórz plik [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) z pobranego projektu. Zastąp wartości przy użyciu poświadczeń uzyskanych w sekcji z opisem [uzyskiwania dostępu do interfejsów API](access-api-cli-how-to.md).

W przykładzie są wykonywane następujące akcje:

1. Tworzenie **przekształcenia** (najpierw następuje sprawdzenie, czy podane przekształcenie istnieje). 
2. Tworzenie **zasobu** danych wyjściowych, który zostanie użyty jako dane wyjściowe **zadania** kodowania.
3. Tworzenie danych wejściowych **zadania** opartych na adresie URL protokołu HTTPS.
4. Przesłanie **zadania** kodowania przy użyciu danych przychodzących i wychodzących, które zostały utworzone wcześniej.
5. Sprawdzenie stanu zadania.
6. Tworzy **lokalizator przesyłania strumieniowego**.
7. Utworzenie adresów URL przesyłania strumieniowego.

Objaśnienia działania poszczególnych funkcji w przykładzie znajdziesz po sprawdzeniu kodu i przyjrzeniu się komentarzom w [tym pliku źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Po uruchomieniu aplikacji zostaną wyświetlone adresy URL, których można użyć do odtwarzania wideo za pomocą różnych protokołów. 

1. Naciśnij klawisze Ctrl+F5, aby uruchomić aplikację *EncodeAndStreamFiles*.
2. Wybierz protokół **HLS** firmy Apple — kończy się ciągiem *manifest(format=m3u8-aapl)*— i skopiuj adres URL przesyłania strumieniowego z konsoli.

![Dane wyjściowe](./media/stream-files-tutorial-with-api/output.png)

W przykładowym [kodzie źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) możesz zobaczyć, jak jest zbudowany adres URL. Aby go utworzyć, musisz połączyć nazwę hosta punktu końcowego przesyłania strumieniowego i ścieżkę lokalizatora przesyłania strumieniowego.  

## <a name="test-with-azure-media-player"></a>Testowanie przy użyciu usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę i przejdź pod adres [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. W polu **Adres URL** wklej jedną z wartości adresu URL przesyłania strumieniowego otrzymanych po uruchomieniu aplikacji. 
 
     Adres URL możesz wkleić w formacie HLS, Dash, lub Smooth, a usługa Azure Media Player przełączy się na odpowiedni protokół przesyłania strumieniowego w celu automatycznego odtworzenia na urządzeniu.
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby z grupy zasobów, w tym konto usługi Media Services i magazynu utworzone w ramach tego przewodnika Szybki start, usuń grupę zasobów.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Analizowanie kodu

Objaśnienia działania poszczególnych funkcji w przykładzie znajdziesz po sprawdzeniu kodu i przyjrzeniu się komentarzom w [tym pliku źródłowym](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

Samouczek [przekazywania, kodowania i strumieniowego przesyłania plików](stream-files-tutorial-with-api.md) zawiera bardziej zaawansowany przykład przesyłania strumieniowego wraz ze szczegółowymi objaśnieniami. 

### <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Wielowątkowość

Zestawy SDK usługi Azure Media Services 3 nie są bezpieczne wątkowo. Podczas pracy z aplikacją wielowątkową należy wygenerować nowy obiekt AzureMediaServicesClient dla każdego wątku.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md)
