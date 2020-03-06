---
title: Pobieranie zestawu Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Usługa mowy współpracuje z wieloma urządzeniami i źródłami audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. W tym artykule dowiesz się, jak uzyskać dostęp do zestawu SDK urządzeń mowy i rozpocząć tworzenie aplikacji.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f8c1500dbbd9135a850e145199de8fea68cc4630
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388287"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Pobieranie usług Cognitive Services Speech Devices SDK

Zestaw SDK urządzeń mowy jest wstępnie dostroją biblioteką opracowaną w celu pracy z utworzonymi zestawami deweloperskimi i różnymi konfiguracjami macierzy mikrofonów.

## <a name="choose-a-development-kit"></a>Wybierz zestaw deweloperski

|Urządzenia|Określając|Opis|Scenariusze|
|--|--|--|--|
|[Roobo Smart audio dev Kit](https://ddk.roobo.com)<br>[Konfiguracja](speech-devices-sdk-roobo-v1.md) / [przewodnik Szybki Start](speech-devices-sdk-android-quickstart.md)![roobo Smart audio dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7, tablica MIC, w usłudze ARM, Sieć Wi-Fi, wyjście z operacji we/wy. <br>[Android](speech-devices-sdk-android-quickstart.md)|Pierwszy zestaw SDK usługi Speech Devices do adaptacji zestawu SDK oprogramowania Microsoft MIC Array i Front Processing|Transkrypcja konwersacji, inteligentny głośnik, Agent głosowy, wearable|
|[Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Konfiguracja](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [przewodnika Szybki Start](speech-devices-sdk-windows-quickstart.md)![Azure urządzenia Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 aparaty RGB i głębokości macierzy mikrofonów. <br>[System Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Zestaw deweloperów z zaawansowanymi czujnikami sztucznej analizy (AI) do tworzenia zaawansowanych modeli przetwarzania i przetwarzania mowy. Łączy on najlepszą w swojej klasie macierzową tablicę i głębokości, z kamerą wideo i czujnikiem orientacji — wszystko to w jednym niewielkim urządzeniu z wieloma trybami, opcjami i zestawami SDK do obsługi różnych typów obliczeniowych.|Transkrypcja konwersacji, robotica, inteligentne Kompilowanie|
|Roobo Smart audio dev Kit 2<br>[Konfiguracja](speech-devices-sdk-roobo-v2.md)<br>![roobo Smart audio dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 z tablicą MIC, ARM SOC, Wi-Fi, Bluetooth, we/wy. <br>Linux|Zestaw SDK urządzeń z informacjami o drugiej generacji, który oferuje alternatywny system operacyjny i więcej funkcji w ramach ekonomicznych projektów referencyjnych.|Transkrypcja konwersacji, inteligentny głośnik, Agent głosowy, wearable|
|URbetter T11![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7\. tablica mikrofonu, ARM SOC, Wi-Fi, Ethernet, HDMI, Kamera USB. <br>Linux|Zestaw SDK urządzeń typu "Speech", który dostosowuje macierz Microsoft MIC i obsługuje rozszerzone we/wy, takie jak HDMI/Ethernet i więcej urządzeń peryferyjnych USB|Transkrypcja konwersacji, edukacja, szpital, roboty, OTT, Agent głosowy, dysk przez|

## <a name="download-the-speech-devices-sdk"></a>Pobierz zestaw Speech Devices SDK

Pobierz [zestaw SDK urządzeń mowy](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zestawu SDK urządzeń mowy](https://aka.ms/sdsdk-quickstart)
