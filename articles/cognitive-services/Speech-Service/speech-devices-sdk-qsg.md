---
title: Wprowadzenie do zestawu Speech Devices SDK — program usługi mowy
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dla klientów zaczynających się od zestawu Speech Devices SDK.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 5bd631bf31ba4e6a840c3e4e818062d322da0158
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727930"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Wprowadzenie do zestawu Speech Devices SDK

W tym artykule opisano sposób konfigurowania programistycznych PC i zestaw Speech urządzenia development kit dla tworzenia urządzenia z włączoną obsługą mowy za pomocą zestawu Speech Devices SDK. Następnie zbudujesz i wdrożysz przykładową aplikację na urządzeniu.

Kod źródłowy przykładowej aplikacji jest dołączone do zestawu Speech Devices SDK. Warto również [dostępne w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tworzenia przy użyciu zestawu Speech Devices SDK, Zbierz informacje i oprogramowania, które są potrzebne:

* Pobierz [deweloperski z ROOBO](http://ddk.roobo.com/). Zestawy są dostępne z konfiguracjami tablicy liniowego lub cykliczne mikrofonu. Wybierz poprawną konfigurację do własnych potrzeb.

    |Tworzenie konfiguracji zestawu|Lokalizacja osoby mówiącej|
    |-----------------------------|------------|
    |Cykliczne|Dowolnym kierunku z urządzenia|
    |Liniowe|Przed urządzenia|

* Pobierz najnowszą wersję zestawu Speech Devices SDK, który zawiera aplikację systemu Android próbki z [witryny pobierania zestawu Speech Devices SDK](https://shares.datatransfer.microsoft.com/). Wyodrębnij plik zip do folderu lokalnego, takich jak C:\SDSDK.

* Zainstaluj [programu Android Studio](https://developer.android.com/studio/) i [Vysor](http://vysor.io/download/) na komputerze.

* Pobierz [klucz subskrypcji usługi mowy](get-started.md). Można uzyskać bezpłatnej 30-dniowej wersji próbnej lub uzyskać klucz z pulpitu nawigacyjnego platformy Azure.

* Jeśli chcesz użyć funkcja rozpoznawania celu usługi rozpoznawania mowy, Subskrybuj [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) i [Pobierz klucz subskrypcji](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription).

    Możesz [Tworzenie prostego modelu usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) lub użyj przykładu model usługi LUIS, example.json usługi LUIS. Przykładowy model usługi LUIS jest dostępny z [witryny pobierania zestawu Speech Devices SDK](https://shares.datatransfer.microsoft.com/). Można przekazać swój model pliku JSON do [portal usługi LUIS](https://www.luis.ai/home), wybierz opcję **importowania Nowa aplikacja**, a następnie wybierz plik JSON.

## <a name="set-up-the-development-kit"></a>Skonfiguruj zestaw development kit

1. Zestaw development kit nawiązać połączenie z komputerem PC lub dostarczenia danych karty przy użyciu kabla USB wczesnych wysokiej jakości. Wskaźnik zielony zasilania, zostaną wyświetlone w górnym tablicy.

1. Zestaw development kit połączyć się z komputerem za pomocą drugiego mini kabla USB.

    ![Łączenie zestawu dla deweloperów](media/speech-devices-sdk/qsg-1.png)

1. Orientacja Twojego zestawu SDK dla dowolnego cykliczne lub liniowy konfiguracji.

    |Tworzenie konfiguracji zestawu|Orientacja|
    |-----------------------------|------------|
    |Cykliczne|Prawidłowa z mikrofon połączonego z limitu|
    |Liniowe|Na bok za pomocą mikrofon przodu (pokazane na poniższej ilustracji)|

    ![Orientacja kit liniowej dev](media/speech-devices-sdk/qsg-2.png)

1. Zainstaluj certyfikaty i plik tabeli wznawiania programu word (słowo kluczowe), a następnie ustaw uprawnienia urządzenia dźwiękowego. W oknie wiersza polecenia, wpisz następujące polecenia:

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Te polecenia korzystają z mostka debugowania systemu Android, `adb.exe`, który jest częścią instalacji programu Android Studio. To narzędzie znajduje się w C:\Users\[nazwa_użytkownika] \AppData\Local\Android\Sdk\platform narzędzia. Ten katalog można dodać do swojej ścieżki, aby był bardziej wygodne do wywołania `adb`. W przeciwnym razie należy określić pełną ścieżkę do instalacji adb.exe w każdego polecenia, który wywołuje `adb`.

    > [!TIP]
    > Wyciszenia mikrofonu i głośników, należy upewnić się, że pracujesz z mikrofon zestaw development kit Twojego komputera. W ten sposób przypadkowo nie spowoduje wyzwolenia z urządzenia z dźwięku z komputera.

1.  Rozpocznij Vysor na tym komputerze.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Urządzenia powinny zostać wyświetlone w obszarze **wybierz urządzenie**. Wybierz **widoku** przycisk obok urządzenia.

1.  Łączenie z siecią bezprzewodową, wybierając ikonę folderu, a następnie wybierz **ustawienia** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Jeśli Twoja firma ma zasady dotyczące podłączania urządzeń do sieci Wi-Fi systemu, należy uzyskać adres MAC, i skontaktuj się z działem IT o sposobie nawiązywania połączenia z sieci Wi-Fi firmy.
    >
    > Aby znaleźć adres MAC zestaw deweloperski, wybierz ikonę folderu plików na pulpicie zestawu dla deweloperów.
    >
    >  ![Folder plików Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Wybierz **ustawienia**. Wyszukaj "mac address", a następnie wybierz pozycję **adres Mac** > **zaawansowane WLAN**. Zanotuj adres MAC, który pojawia się w dolnej części okna dialogowego.
    >
    > ![Adres Vysor MAC](media/speech-devices-sdk/qsg-11.png)
    >
    > Niektóre firmy mogą mieć limit czasu na jak długo urządzenie może być podłączony do sieci Wi-Fi systemu. Może być konieczne rozszerzenie rejestracji zestawu dla deweloperów z sieci Wi-Fi systemu po określonej liczbie dni.
    >
    > Aby dołączyć osoby mówiącej do zestawu dla deweloperów, należy go połączyć z wierszem audio out. Należy wybrać dobrej jakości, 3.5 mm osoby mówiącej.
    >
    > ![Vysor audio](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>Uruchamianie przykładowej aplikacji

Aby uruchomić testy ROOBO i weryfikowanie konfiguracji development kit, tworzenie i instalowanie przykładowej aplikacji:

1.  Uruchom program Android Studio.

1.  Wybierz pozycję **Open an existing Android Studio project** (Otwórz istniejący projekt Android Studio).

    ![Android Studio — Otwórz istniejący projekt](media/speech-devices-sdk/qsg-5.png)

1.  Przejdź do C:\SDSDK\Android-Sample-Release\example. Wybierz **OK** otworzyć projektu w przykładzie.

1.  Dodaj klucz subskrypcji mowy do kodu źródłowego. Do wypróbowania rozpoznawanie intencji, również należy dodać swoje [usługi Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) klucz subskrypcji i aplikacji identyfikatora.

    Kluczy i informacje o aplikacji przejdź w następujących wierszach w pliku źródłowym MainActivity.java:

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app ID]"
    ```

1. Aktywujące domyślne (słowo kluczowe) jest "Computer". Można też spróbować jednego z innych podane wake słów, takich jak "Maszyna" lub "Asystent". Pliki zasobów dla tych słów alternatywne wznawiania znajdują się w zestawu Speech Devices SDK, na w tym folderze — słowo kluczowe. Na przykład C:\SDSDK\Android-Sample-Release\keyword\Computer zawiera pliki, używany do wznawiania wyraz "Komputer".

    Możesz również [utworzyć niestandardowe słowo aktywujące](speech-devices-sdk-create-kws.md).

    Aby zainstalować word wznawiania, których chcesz użyć:

    * Utwórz folder — słowo kluczowe w folderze danych na urządzeniu, uruchamiając następujące polecenia w oknie wiersza polecenia:

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Skopiuj pliki kws.table kws_k.fst i words_kw.txt do folderu \data\keyword urządzenia. Uruchom następujące polecenia w oknie wiersza polecenia. Jeśli utworzono [niestandardowe słowo aktywujące](speech-devices-sdk-create-kws.md), plik kws.table wygenerowane z sieci web znajduje się w tym samym katalogu co pliki kws.table, kws_k.fst i words_kw.txt. Niestandardowe słowo aktywujące, można użyć `adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword` polecenie, aby wypchnąć plik kws.table do zestawu dla deweloperów:

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```

    * Odwoływać się do tych plików w przykładowej aplikacji. Znajdź następujące wiersze w MainActivity.java. Upewnij się, że określone słowo kluczowe jest używasz i czy ścieżka prowadzi do `kws.table` pliku, który jest przypisany do urządzenia.

        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > We własnym kodzie można użyć pliku kws.table, aby utworzyć wystąpienie modelu — słowo kluczowe i uruchomić rozpoznawanie:
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Zaktualizuj następujące wiersze, które zawierają ustawienia geometrii, mikrofon tablicy:

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```
    W poniższej tabeli opisano dostępne wartości:

    |Zmienna|Znaczenie|Dostępne wartości|
    |--------|-------|----------------|
    |`DeviceGeometry`|Konfiguracji fizycznej mic|Aby uzyskać cykliczne zestaw deweloperski: `Circular6+1` |
    |||Aby uzyskać liniowej zestaw deweloperski: `Linear4`|
    |`SelectedGeometry`|Konfiguracja kontroli integralności uprawnień oprogramowania|Dla okręgu deweloperski, który używa mikrofonu wszystkich: `Circular6+1`|
    |||Aby uzyskać zestaw cykliczne deweloperów, która używa czterech mikrofonu: `Circular3+1`|
    |||Dla liniowych deweloperski, który używa mikrofonu wszystkich: `Linear4`|
    |||Dla zestawu SDK do dev liniowy, który używa dwóch mikrofonu: `Linear2`|


1.  Aby skompilować aplikację, na **Uruchom** menu, wybierz opcję **Uruchom "aplikację"**. **Wybierz cel wdrożenia** pojawi się okno dialogowe.

1. Wybierz urządzenie, a następnie wybierz **OK** wdrożyć aplikację na urządzeniu.

    ![Wybierz cel wdrożenia — okno dialogowe](media/speech-devices-sdk/qsg-7.png)

1.  Przykładowa aplikacja zestawu Speech Devices SDK rozpoczyna się i wyświetla następujące opcje:

    ![Opcje i przykładowa aplikacja przykład zestawu Speech Devices SDK](media/speech-devices-sdk/qsg-8.png)

1. Eksperyment!

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="certificate-failures"></a>Błędy certyfikatów

Jeśli błędy certyfikatów podczas korzystania z usługi rozpoznawania mowy, upewnij się, że urządzenie ma poprawną datę i godzinę:

1. Przejdź do **ustawienia**. W obszarze **systemu**, wybierz opcję **daty i godziny**.

    ![W obszarze Ustawienia wybierz datę i godzinę](media/speech-devices-sdk/qsg-12.png)

1. Zachowaj **automatycznej daty i godziny** wybraną opcją. W obszarze **wybierz strefę czasową**, wybierz strefę czasową.

    ![Wybierz opcje daty i strefy czasowej](media/speech-devices-sdk/qsg-13.png)

    Gdy pojawi się, że czas zestawu dla deweloperów odpowiada czas na komputerze z systemem, zestaw deweloperski jest połączony z Internetem.

    Aby uzyskać więcej informacji rozwoju, zobacz [Podręcznik programowania ROOBO](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

### <a name="audio"></a>Audio

ROOBO udostępnia narzędzia, która przechwytuje wszystkie audio w pamięci flash. Mogą pomóc rozwiązać problemy. Wersja narzędzia towarzyszy konfiguracjami development kit. Na [witryny ROOBO](http://ddk.roobo.com/), wybierz urządzenie, a następnie wybierz **narzędzia ROOBO** widocznego u dołu strony.
