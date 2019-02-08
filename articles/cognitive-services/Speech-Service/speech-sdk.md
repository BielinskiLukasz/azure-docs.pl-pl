---
title: Temat usługi mowy SDK — usługi mowy
titleSuffix: Azure Cognitive Services
description: Mowy usługi Software Development Kit (SDK) daje aplikacji natywnej dostęp do funkcji usługi rozpoznawania mowy, ułatwia tworzenie oprogramowania. Ten artykuł zawiera dodatkowe szczegóły dotyczące zestawu SDK dla Windows, Linux i Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 749def4df4aa210583f778668a398419ec08a07d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861429"
---
# <a name="about-the-speech-service-sdk"></a>Temat zestawu SDK usługi mowy

Mowy usługi Software Development Kit (SDK) daje aplikacji natywnej dostęp do funkcji usługi rozpoznawania mowy, ułatwia tworzenie oprogramowania. Obecnie, zestaw SDK zapewnia dostęp do **zamiana mowy na tekst**, **tłumaczenia mowy**, i **rozpoznawanie intencji**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Pobierz zestaw SDK

### <a name="windows"></a>Windows

Dla Windows firma Microsoft obsługuje następujące języki:

* C#Platformy uniwersalnej systemu Windows i platformy .NET, C++: Można odwoływać się i używać najnowszej wersji naszych pakietu NuGet zestawu SDK rozpoznawania mowy. Ten pakiet zawiera biblioteki klienckie 32-bitowych i 64-bitowych i bibliotek zarządzanych (.NET). Zestaw SDK można zainstalować za pomocą pakietu NuGet w programie Visual Studio. Wyszukaj **Microsoft.CognitiveServices.Speech**.

* Java: Można odwołać się i używać najnowszej wersji naszych pakietu Maven zestaw SDK rozpoznawania mowy, który obsługuje tylko Windows x64. W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium i odwołania `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0` jako zależność.

### <a name="linux"></a>Linux

> [!NOTE]
> Obecnie obsługujemy tylko Ubuntu 16.04 i 18.04 na komputerze (x86 lub x64 dla programowania w języku C++ i x64 dla platformy .NET Core i Java).

Upewnij się, że masz wymagany kompilator i biblioteki zainstalowane, uruchamiając następujące polecenia powłoki:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: Można odwoływać się i używać najnowszej wersji naszych pakietu NuGet zestawu SDK rozpoznawania mowy. Aby odwołać się do zestawu SDK, należy dodać następujące odwołanie pakietu do projektu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.2.0" />
  ```

* Java: Można odwołać się i używać najnowszej wersji naszych pakietu Maven zestaw SDK rozpoznawania mowy. W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium i odwołania `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0` jako zależność.

* C++: Pobierz zestaw SDK jako [pakietu tar](https://aka.ms/csspeech/linuxbinary) i Rozpakowywanie plików w wybranym katalogu. W poniższej tabeli przedstawiono strukturę folderu zestawu SDK:

  |Ścieżka|Opis|
  |-|-|
  |`license.md`|Licencja|
  |`ThirdPartyNotices.md`|Uwagi dotyczące innych firm|
  |`include`|Pliki nagłówkowe dla języków C i C++|
  |`lib/x64`|X64 natywnej biblioteki do łączenia się z aplikacją|
  |`lib/x86`|X86 natywnej biblioteki do łączenia się z aplikacją|

  Aby utworzyć aplikację, skopiuj lub Przenieś wymagane pliki binarne (i biblioteki) do środowiska deweloperskiego. Włączyć je zgodnie z potrzebami w procesie kompilacji.

### <a name="android"></a>Android

Zestaw Java SDK dla systemu Android jest spakowany jako [AAR (biblioteka systemu Android)](https://developer.android.com/studio/projects/android-library), która obejmuje niezbędnych bibliotek i wymagane uprawnienia dla systemu Android. Znajduje się w repozytorium narzędzia Maven w `https://csspeechstorage.blob.core.windows.net/maven/` jako pakiet `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0`.

Korzystanie z pakietu z projektu w programie Android Studio, należy wprowadzić następujące zmiany:

* W pliku build.gradle na poziomie projektu, Dodaj następujący kod do `repository` sekcji:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* W pliku build.gradle poziom modułu, Dodaj następujący kod do `dependencies` sekcji:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.2.0'
  ```

Zestaw Java SDK jest również częścią [zestawu Speech Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
