---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 64a6bf1fec61871d2787966a5b0af24d4f012032
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637624"
---
:::row:::
    :::column span="3":::
        Zestaw Java SDK dla systemu Android jest spakowany jako <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Biblioteka systemu Android <span class="docon docon-navigate-external x-hidden-focus"></span> ) </a>, który obejmuje niezbędne biblioteki i wymagane uprawnienia systemu Android. Jest ona hostowana w repozytorium Maven `https://csspeechstorage.blob.core.windows.net/maven/` jako pakiet `com.microsoft.cognitiveservices.speech:client-sdk:1.12.1` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Aby użyć pakietu z projektu Android Studio, wprowadź następujące zmiany:

1. W pliku *Build. Gradle* na poziomie projektu Dodaj następujące polecenie do `repository` sekcji:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. W pliku *Build. Gradle* na poziomie modułu Dodaj następujące polecenie do `dependencies` sekcji:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.12.1'
  ```

Zestaw Java SDK jest również częścią [zestawu Speech Devices SDK](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Zasoby dodatkowe

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Kod źródłowy przewodnika Szybki Start dla systemu Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Środowisko uruchomieniowe Java (JRE) — kod źródłowy szybkiego startu<span class="docon docon-navigate-external x-hidden-focus"></span></a>