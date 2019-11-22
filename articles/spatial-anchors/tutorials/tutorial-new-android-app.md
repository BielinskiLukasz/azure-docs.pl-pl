---
title: 'Samouczek: Tworzenie nowej aplikacji dla systemu Android'
description: W tym samouczku dowiesz się, jak utworzyć nową aplikację dla systemu Android przy użyciu kotwic przestrzennych platformy Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: a1c19523508f434c114df884824d1595376bac21
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276924"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Samouczek: instrukcje krok po kroku dotyczące tworzenia nowej aplikacji dla systemu Android przy użyciu kotwic przestrzennych platformy Azure

W tym samouczku pokazano, jak utworzyć nową aplikację systemu Android, która integruje funkcjonalność ARCore z zakotwiczeniami przestrzennymi platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

- Maszyna z systemem Windows lub macOS z <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
- Urządzenie z systemem Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">pracujące w trybie dewelopera</a> i <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">zgodne z platformą ARCore</a>.

## <a name="getting-started"></a>Wprowadzenie

Uruchom program Android Studio. W oknie **Witamy w Android Studio** kliknij pozycję **rozpocznij nowy projekt Android Studio**. Lub, jeśli masz już otwarty projekt, wybierz pozycję **plik**->**Nowy projekt**.

W oknie **Tworzenie nowego projektu** w obszarze **telefon i tablet** wybierz pozycję **puste działanie**, a następnie kliknij przycisk **dalej**. Następnie w obszarze **minimalny poziom interfejsu API**wybierz pozycję `API 26: Android 8.0 (Oreo)`i upewnij się, że **Język** jest ustawiony na `Java`. Możesz chcieć zmienić nazwę projektu & lokalizacji i nazwę pakietu. Pozostaw inne opcje. Kliknij przycisk **Zakończ**. Zostanie uruchomiony **Instalator składnika** . Po zakończeniu kliknij przycisk **Zakończ**. Po zakończeniu niektórych operacji Android Studio otworzy środowisko IDE.

## <a name="trying-it-out"></a>Trwa próba

Aby przetestować nową aplikację, podłącz urządzenie z obsługą dewelopera do komputera deweloperskiego przy użyciu kabla USB. Kliknij przycisk **uruchom**->**Uruchom polecenie "App"** . W oknie **Wybieranie celu wdrożenia** wybierz urządzenie, a następnie kliknij przycisk **OK**. Android Studio instaluje aplikację na podłączonym urządzeniu i uruchamia ją. Powinien być teraz widoczny "Hello world!" wyświetlane w aplikacji uruchomionej na urządzeniu. Kliknij przycisk **Uruchom** ,->**zatrzymać "App"** .

## <a name="integrating-_arcore_"></a>Integrowanie _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> to platforma firmy Google do tworzenia środowisk o rzeczywistości rozszerzonej, dzięki czemu urządzenie może śledzić swoją pozycję w miarę ich przenoszenia i tworzy własne zrozumienie świata rzeczywistego.

Zmodyfikuj `app\manifests\AndroidManifest.xml`, aby uwzględnić następujące wpisy w głównym węźle `<manifest>`. Ten fragment kodu wykonuje kilka czynności:

- Umożliwi aplikacji dostęp do aparatu urządzenia.
- Zapewnia również, że aplikacja będzie widoczna tylko w Sklep Google Play na urządzeniach, które obsługują ARCore.
- Skonfiguruje Sklep Google Play, aby pobierać i instalować ARCore, jeśli nie jest jeszcze zainstalowana, gdy aplikacja zostanie zainstalowana.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Zmodyfikuj `Gradle Scripts\build.gradle (Module: app)` tak, aby zawierał następujący wpis. Ten kod zapewni, że aplikacja jest przeznaczona dla ARCore w wersji 1,8. Po tej zmianie może zostać wyświetlone powiadomienie z programu Gradle z prośbą o zsynchronizowanie: kliknij pozycję **Synchronizuj teraz**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integrowanie _Sceneform_

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_</a> ułatwia renderowanie realistycznych scen 3W w aplikacjach rzeczywistości o rozszerzeniu, bez konieczności uczenia się OpenGL.

Zmodyfikuj `Gradle Scripts\build.gradle (Module: app)`, aby uwzględnić następujące wpisy. Ten kod umożliwi aplikacji korzystanie z konstrukcji językowych języka Java 8, co `Sceneform` wymaga. Zapewni również, że aplikacja będzie ukierunkowana na `Sceneform` wersji 1,8, ponieważ powinna być zgodna z wersją ARCore używaną przez aplikację. Po tej zmianie może zostać wyświetlone powiadomienie z programu Gradle z prośbą o zsynchronizowanie: kliknij pozycję **Synchronizuj teraz**.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Otwórz `app\res\layout\activity_main.xml`i Zastąp istniejący element Hello WOLRD `<TextView>` następującym elementem ArFragment. Ten kod spowoduje wyświetlenie kanału informacyjnego aparatu na ekranie umożliwiającym ARCore śledzenia położenia urządzenia w trakcie jego przenoszenia.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

Ponownie [Wdróż](#trying-it-out) aplikację na urządzeniu, aby zweryfikować ją jeszcze raz. Tym razem należy zażądać uprawnień do aparatu. Po zatwierdzeniu na ekranie powinno być widoczne renderowanie kanału informacyjnego aparatu.

## <a name="place-an-object-in-the-real-world"></a>Umieść obiekt w świecie rzeczywistym

Utwórzmy & umieścić obiekt przy użyciu aplikacji. Najpierw Dodaj następujące Importy do `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Następnie Dodaj następujące zmienne członkowskie do klasy `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Następnie Dodaj następujący kod do metody `onCreate()` `app\java\<PackageName>\MainActivity`. Ten kod spowoduje Podłączenie odbiornika o nazwie `handleTap()`, który będzie wykrywać, kiedy użytkownik naciśnie ekran na urządzeniu. Jeśli naciśnięcie ma być na świecie rzeczywistym, który został już rozpoznany przez śledzenie ARCore, zostanie uruchomiony odbiornik.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Na koniec Dodaj następującą metodę `handleTap()`, która spowoduje powiązanie wszystkiego ze sobą. Spowoduje to utworzenie kuli i umieszczenie jej w lokalizacji, w której zostanie umieszczona. Sfera będzie początkowo czarna, ponieważ `this.recommendedSessionProgress` jest teraz ustawiona na zero. Ta wartość zostanie później zmieniona na.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

Ponownie [Wdróż](#trying-it-out) aplikację na urządzeniu, aby zweryfikować ją jeszcze raz. Tym razem można poruszać się po urządzeniu, aby ARCore rozpoczęcie rozpoznawania środowiska. Następnie naciśnij ekran, aby utworzyć & umieścić czarny sferę na wybranej powierzchni.

## <a name="attach-a-local-azure-spatial-anchor"></a>Dołącz lokalną kotwicę przestrzenną platformy Azure

Zmodyfikuj `Gradle Scripts\build.gradle (Module: app)` tak, aby zawierał następujący wpis. Ten kod zapewni, że aplikacja będzie ukierunkowana na kotwice przestrzenne platformy Azure w wersji 1.3.0. Wspomniane odwołanie odwołujące się do wszystkich najnowszych wersji zakotwiczenia przestrzennego platformy Azure powinno funkcjonować.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.3.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.3.0]"
    ...
}
```

Kliknij prawym przyciskiem myszy pozycję `app\java\<PackageName>`->**nową** **klasę->Java**. Ustaw **nazwę** na _mojapierwszaaplikacja_i **Superklasa** na system _Android. app. Application_. Pozostaw inne opcje. Kliknij przycisk **OK**. Zostanie utworzony plik o nazwie `MyFirstApp.java`. Dodaj do niego następujący import:

```java
import com.microsoft.CloudServices;
```

Następnie Dodaj następujący kod do nowej klasy `MyFirstApp`, co zapewni zainicjowanie kotwice przestrzenne platformy Azure przy użyciu kontekstu aplikacji.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Teraz zmodyfikuj `app\manifests\AndroidManifest.xml` tak, aby zawierał następujący wpis w głównym węźle `<application>`. Ten kod spowoduje podłączenie klasy aplikacji utworzonej w aplikacji.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Wróć do `app\java\<PackageName>\MainActivity`, Dodaj do niego następujące Importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Następnie Dodaj następujące zmienne członkowskie do klasy `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Następnie Dodajmy następującą metodę `initializeSession()` wewnątrz klasy `mainActivity`. Po wywołaniu zagwarantuje, że sesja kotwic Azure przestrzenny zostanie utworzona i poprawnie zainicjowana podczas uruchamiania aplikacji.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Teraz przejdźmy do metody `onCreate()` `initializeSession()`. Ponadto zagwarantujemy, że ramki z kanału informacyjnego aparatu są wysyłane do zestawu Azure przestrzenny Kotwics do przetwarzania.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Na koniec Dodaj następujący kod do metody `handleTap()`. Dołączy lokalną kotwicę platformy Azure do czarnej sfery, która jest umieszczana w świecie rzeczywistym.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

Ponownie [Wdróż](#trying-it-out) aplikację jeszcze raz. Poruszaj się po urządzeniu, naciśnij ekran i umieść czarną sferę. Tym razem kod będzie tworzyć i dołączać lokalną kotwicę do usługi Azure przestrzenny do sfery.

Przed przeprowadzeniem dalszych dalszych czynności należy utworzyć identyfikator i klucz konta zakotwiczeń przestrzennych platformy Azure, jeśli jeszcze tego nie zrobiono. Aby je uzyskać, postępuj zgodnie z poniższą sekcją.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Przekaż lokalne zakotwiczenie do chmury

Po określeniu identyfikatora i klucza konta zakotwiczeń przestrzennych platformy Azure możemy wrócić do `app\java\<PackageName>\MainActivity`, dodając do niego następujące Importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Następnie Dodaj następujące zmienne członkowskie do klasy `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Teraz Dodaj następujący kod do metody `initializeSession()`. Najpierw ten kod zezwoli aplikacji na monitorowanie postępu, jaki zestaw SDK kotwice przestrzenne platformy Azure tworzy w miarę zbierania ramek z kanału informacyjnego aparatu. W miarę jak kolor sfery zacznie się zmieniać od oryginalnego koloru czarnego na szary. Następnie nastąpi obrócenie bieli po zebraniu wystarczającej liczby klatek, aby przesłać zakotwiczenie do chmury. Następnie ten kod zapewni poświadczenia potrzebne do komunikowania się z zapleczem chmury. Tutaj można skonfigurować aplikację do używania identyfikatora i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobów kotwic przestrzennych](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Następnie Dodaj następującą metodę `uploadCloudAnchorAsync()` wewnątrz klasy `mainActivity`. Po wywołaniu ta metoda asynchronicznie zaczeka, aż do momentu zebrania wystarczającej liczby klatek z Twojego urządzenia. Tak szybko, jak to nastąpi, zmieni kolor sfery na żółty, a następnie rozpocznie się przekazywanie lokalnego zakotwiczenia przestrzennego platformy Azure do chmury. Po zakończeniu przekazywania kod zwróci identyfikator zakotwiczenia.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Na koniec przychodźmy wszystko razem. W metodzie `handleTap()` Dodaj następujący kod. Spowoduje to wywołanie metody `uploadCloudAnchorAsync()` zaraz po utworzeniu sfery. Po powrocie metody kod poniżej wykona jedną aktualizację ostateczną do sfery, zmieniając jej kolor na niebieską.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

Ponownie [Wdróż](#trying-it-out) aplikację jeszcze raz. Poruszaj się po urządzeniu, naciśnij ekran i umieść swoją sferę. Tym razem, sfera zmieni kolor z czarnej na biały, ponieważ klatki kamer są zbierane. Gdy mamy wystarczającą liczbę ramek, sfera zmieni się na żółtą i rozpocznie się przekazywanie do chmury. Po zakończeniu przekazywania sfera zmieni kolor na niebieski. Opcjonalnie można również użyć okna `Logcat` wewnątrz Android Studio do monitorowania komunikatów dziennika wysyłanych przez aplikację. Na przykład postęp sesji podczas przechwytywania ramki oraz identyfikator zakotwiczony zwracany przez chmurę po zakończeniu przekazywania.

## <a name="locate-your-cloud-spatial-anchor"></a>Znajdź kotwicę przestrzenną w chmurze

Jedno zakotwiczenie zostanie przekazane do chmury. wszystko jest gotowe do ponownego zlokalizowania. Najpierw Dodajmy następujące Importy do kodu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Następnie Dodajmy Poniższy kod do metody `handleTap()`. Ten kod będzie:

- Usuń istniejącą niebieską sferę z ekranu.
- Zainicjuj ponownie sesję zakotwiczeń przestrzennych platformy Azure. Ta akcja zapewni, że zakotwiczenie, które zamierzamy zlokalizować, pochodzi z chmury, a nie do lokalnej kotwicy.
- Wydaj zapytanie dla zakotwiczenia przekazanego do chmury.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Teraz przechwytuje kod, który zostanie wywołany, gdy zakotwiczenie zostanie umieszczone w zapytaniu. Wewnątrz metody `initializeSession()` Dodaj następujący kod. Ten fragment kodu zostanie utworzony, & umieścić zieloną sferę, gdy zostanie umieszczona kotwica w chmurze. Spowoduje to również ponowne włączenie ekranu, aby można było wielokrotnie powtórzyć cały scenariusz: Utwórz kolejną kotwicę lokalną, przekaż ją i Znajdź ponownie.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Gotowe. Ponownie [Wdróż](#trying-it-out) aplikację po raz ostatni, aby wypróbować cały scenariusz. Poruszaj się po urządzeniu i umieść swoją czarną sferę. Następnie kontynuuj przeniesienie urządzenia do przechwytywania klatek kamer, dopóki sfera nie zmieni się na żółty. Twoje lokalne zakotwiczenie zostanie przekazane, a SFERA zmieni kolor na niebiesko. Na koniec naciśnij swój ekran jeszcze raz, aby lokalne zakotwiczenie zostało usunięte, a następnie będziemy wysyłać zapytania o jego odpowiednik w chmurze. Kontynuuj przenoszenie urządzenia do momentu, gdy zakotwiczenie chmury nie zostanie umieszczone. Zielona kula powinna pojawić się w poprawnej lokalizacji i można wypłukać & powtórzyć cały scenariusz ponownie.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
