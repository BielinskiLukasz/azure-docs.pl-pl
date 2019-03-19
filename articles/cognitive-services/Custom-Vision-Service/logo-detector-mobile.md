---
title: 'Samouczek: używanie detektora niestandardowego logo do rozpoznawania usług platformy Azure — Custom Vision'
titlesuffix: Azure Cognitive Services
description: W tym samouczku zostanie przedstawiona przykładowa aplikacja, która używa usługi Azure Custom Vision jako części scenariusza wykrywania logo. Dowiedz się, jak usługa Custom Vision wraz z innymi składnikami jest używana do dostarczenia całościowej aplikacji.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 259787a90b61b171f391dc02276214f17a57d0d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838820"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Samouczek: rozpoznawanie logo usług platformy Azure na obrazach z kamery

W tym samouczku poznasz przykładową aplikację, która używa usługi Azure Custom Vision jako części większego scenariusza. Aplikacja sztucznej inteligencji aprowizacji wizualnej, aplikacja Xamarin.Forms dla platform mobilnych, analizuje obrazy logo usług platformy Azure z kamery, a następnie wdraża rzeczywiste usługi na koncie użytkownika platformy Azure. Tutaj dowiesz się, jak używa ona usługi Custom Vision we współpracy z innymi składnikami w celu dostarczenia przydatnej całościowej aplikacji. Uruchom całą aplikację dla siebie lub po prostu wykonaj część instalacji usługi Custom Vision i zbadaj, jak aplikacja z niej korzysta.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie wykrywacza niestandardowego obiektu w celu rozpoznawania logo usług platformy Azure.
> - Łączenie Twojej aplikacji z usługami Azure Computer Vision i Custom Vision.
> - Tworzenie konta jednostki usługi platformy Azure w celu wdrożenia usług platformy Azure z poziomu aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). 

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Obciążenia na platformie Xamarin dla programu Visual Studio (zobacz [Instalacja platformy Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Emulator systemu iOS lub Android dla programu Visual Studio
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (opcjonalnie)

## <a name="get-the-source-code"></a>Uzyskiwanie kodu źródłowego

Jeśli chcesz korzystać z dostarczonej aplikacji internetowej, sklonuj lub pobierz kod źródłowy aplikacji z repozytorium [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) w usłudze GitHub. Otwórz plik *Source/VisualProvision.sln* w programie Visual Studio. Później będziesz edytować niektóre pliki projektu, aby było możliwe uruchomienie aplikacji.

## <a name="create-an-object-detector"></a>Tworzenie detektora obiektu

Zaloguj się do [witryny internetowej usługi Custom Vision](https://customvision.ai/) i utwórz nowy projekt. Określ projekt wykrywania obiektów i użyj domeny Logo. Umożliwi to usłudze użycie algorytmu zoptymalizowanego pod kątem wykrywania logo. 

![Okno nowego projektu w witrynie internetowej usługi Custom Vision w przeglądarce Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Przekazywanie i tagowanie obrazów

Następnie wytrenuj algorytm wykrywania logo, przekazując obrazy logo usług platformy Azure i tagując je ręcznie. Repozytorium AIVisualProvision zawiera zbiór obrazów do szkolenia, którego można użyć. W witrynie internetowej naciśnij przycisk **Add images (Dodaj obrazy)** znajdujący się na karcie **Training Images (Obrazy treningowe)**. Następnie przejdź do folderu **Documents/Images/Training_DataSet** repozytorium. Konieczne będzie ręczne tagowanie logo na każdym obrazie, jeśli więc tylko testujesz ten projekt, możesz chcieć przekazać tylko podzbiór obrazów. Przekaż co najmniej 15 wystąpień każdego tagu, który ma być używany.

Po przekazaniu obrazów treningowych wybierz pierwszy z nich na ekranie. Spowoduje to wyświetlenie okna tagowania. Narysuj pola i przypisz tagi do każdego logo na każdym obrazie. 

![Tagowanie logo w witrynie internetowej usługi Custom Vision](media/azure-logo-tutorial/tag-logos.png)

Aplikacja jest skonfigurowana do pracy z określonymi ciągami tagów. Definicje znajdziesz w pliku *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Po otagowaniu obrazu przejdź w prawo, aby otagować następny. Po zakończeniu zamknij okno tagowania.

## <a name="train-the-object-detector"></a>Szkolenie detektora obiektów

W okienku po lewej stronie ustaw przełącznik **Tags (Tagi)** w pozycji **Tagged (Otagowane)**, aby wyświetlić swoje obrazy. Następnie kliknij zielony przycisk w górnej części strony, aby wytrenować model. Spowoduje to nauczenie algorytmu rozpoznawania tych samych tagów na nowych obrazach. Przetestuje to również model na niektórych Twoich istniejących obrazach w celu wygenerowania ocen dokładności.

![Witryna internetowa usługi Custom Vision na karcie Training Images (Obrazy szkoleniowe). Na tym zrzucie ekranu oznaczono przycisk Train (Trenuj)](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Pobieranie adresu URL prognozy

Po wytrenowaniu modelu można już zintegrować go z Twoją aplikacją. Aby to zrobić, musisz pobrać adres URL punktu końcowego (adres modelu, do którego aplikacja będzie wysyłać zapytania) i klucz prognozowania (aby udzielić aplikacji dostępu do żądań prognozowania). Na karcie **Performance (Wydajność)** naciśnij przycisk **Prediction URL (Adres URL przewidywania)** u góry strony.

![Witryna internetowa usługi Custom Vision przedstawiająca okno interfejsu API przewidywania, które wyświetla adres URL i klucz interfejsu API](media/azure-logo-tutorial/cusvis-endpoint.png)

Skopiuj adres URL pliku obrazu i wartość **Prediction-Key** do odpowiednich pól w pliku *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Sprawdzenie użycia usługi Custom Vision

Otwórz plik *Source/VisualProvision/Services/Recognition/CustomVisionService.cs*, aby zobaczyć, jak aplikacja używa Twojego klucza usługi Custom Vision i adresu URL punktu końcowego. Metoda **PredictImageContentsAsync** przyjmuje strumień bajtów pliku obrazu wraz z tokenem anulowania (do asynchronicznego zarządzania zadaniami), wywołuje interfejs API prognozowania usługi Custom Vision i zwraca wynik prognozowania. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Ten wynik przybiera postać wystąpienia **PredictionResult**, które samo zawiera listę wystąpień **prognozy**. **Prognoza** zawiera wykryty tag i lokalizację otaczającego go pola na obrazie.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Aby dowiedzieć się więcej na temat sposobu obsługi tych danych w aplikacji, zacznij od metody **GetResourcesAsync**. Ta metoda jest zdefiniowana w pliku *Source/VisualProvision/Services/Recognition/RecognitionService.cs*.  

## <a name="add-computer-vision"></a>Dodawanie usługi Computer Vision

Część samouczka dotycząca usługi Custom Vision jest zakończona. Jeśli chcesz uruchomić aplikację, musisz zintegrować również usługę Computer Vision. Aplikacja używa funkcji rozpoznawania tekstu usługi Computer Vision, aby uzupełnić proces wykrywania logo. Logo platformy Azure może być rozpoznawane na podstawie jego wyglądu *lub* tekstu wydrukowanego w jego pobliżu. W przeciwieństwie do modeli usługi Custom Vision, usługa Computer Vision jest wstępnie wytrenowana pod kątem wykonywania pewnych operacji na obrazach lub wideo.

Zasubskrybuj usługę Computer Vision, aby uzyskać klucz i adres URL punktu końcowego. Aby uzyskać pomoc dotyczącą tego kroku, zobacz [Jak uzyskać klucze subskrypcji](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![Usługa Computer Vision w witrynie Azure Portal z wybranym menu Szybki start. Link do kluczy i adres URL punktu końcowego interfejsu API są zaznaczone](media/azure-logo-tutorial/comvis-keys.png)

Następnie otwórz plik *Source\VisualProvision\AppSettings.cs* i wpisz poprawne wartości do zmiennych `ComputerVisionEndpoint` i `ComputerVisionKey`.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aplikacja wymaga konta nazwy głównej usługi platformy Azure do wdrażania usług w Twojej subskrypcji platformy Azure. Nazwa główna usługi służy do delegowania określonych uprawnień do aplikacji za pomocą kontroli dostępu opartej na rolach. Aby dowiedzieć się więcej, zobacz [przewodnik po jednostkach usług](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Jednostkę usługi możesz utworzyć za pomocą usługi Azure Cloud Shell albo interfejsu wiersza polecenia platformy Azure (jak pokazano tutaj). Aby rozpocząć, zaloguj się i wybierz subskrypcję, której chcesz użyć.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Następnie utwórz swoją jednostkę usługi. (Wykonanie tego procesu może potrwać jakiś czas).

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Po pomyślnym zakończeniu powinny pojawić się następujące dane wyjściowe JSON zawierające niezbędne poświadczenia.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Zanotuj wartości `clientId` i `tenantId`. Dodaj je do odpowiednich pól w pliku *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Uruchamianie aplikacji

W tym momencie został przez Ciebie udzielony aplikacji dostęp do:

- wytrenowanego modelu usługi Custom Vision
- usługi Computer Vision
- konta jednostki usługi

Wykonaj następujące kroki, aby uruchomić aplikację:

1. W Eksploratorze rozwiązań programu Visual Studio wybierz projekt **VisualProvision.Android** lub projekt **VisualProvision.iOS**. Z menu rozwijanego na głównym pasku narzędzi wybierz odpowiedni emulator lub podłączone urządzenie przenośne. Następnie uruchom aplikację.

    > [!NOTE]
    > Aby uruchomić emulator systemu iOS, musisz mieć urządzenie z systemem MacOS.

1. Na pierwszym ekranie wprowadź identyfikator klienta jednostki usługi, identyfikator dzierżawy i hasło. Naciśnij przycisk **Login (Zaloguj się)**.

    > [!NOTE]
    > W niektórych emulatorach przycisk **Login (Zaloguj się)** może nie być uaktywniony na tym etapie. W takim przypadku zatrzymaj aplikację, otwórz plik *Source/VisualProvision/Pages/LoginPage.xaml*, znajdź element `Button` oznaczony jako **LOGIN BUTTON (PRZYCISK LOGOWANIA)**, usuń następujący wiersz, po czym ponownie uruchom aplikację.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Ekran aplikacji przedstawiający pola na poświadczenia jednostki usługi](media/azure-logo-tutorial/app-credentials.png)

1. Na następnym ekranie wybierz z listy rozwijanej swoją subskrypcję platformy Azure. (To menu powinno zawierać wszystkie subskrypcje, do których jednostka usługi ma dostęp). Naciśnij przycisk **Continue (Kontynuuj)**. W tym momencie aplikacja może wyświetlić monit o udzielenie dostępu do aparatu i magazynu zdjęć urządzenia. Udziel uprawnień dostępu.

    ![Ekran aplikacji przedstawiający pole listy rozwijanej dla docelowej subskrypcji platformy Azure](media/azure-logo-tutorial/app-az-subscription.png)


1. Aparat w Twoim urządzeniu zostanie uaktywniony. Zrób zdjęcie jednego z logo usługi platformy Azure, które było uwzględnione podczas trenowania. W oknie wdrożenia powinien pojawić się monit o wybór regionu i grupy zasobów dla nowych usług (tak jak w przypadku ich wdrożenia z witryny Azure Portal). 

    ![Ekran aparatu w smartfonie nakierowany na dwa wycinki papieru z logo platformy Azure](media/azure-logo-tutorial/app-camera-capture.png)

    ![Ekran aplikacji przedstawiający pola dla regionu wdrożenia i grupy zasobów](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli wykonasz już wszystkie kroki w tym scenariuszu i użyjesz aplikacji do wdrażania usług platformy Azure na swoim koncie, przejdź do witryny [Azure Portal](https://ms.portal.azure.com/). Anuluj tam usługi, których nie chcesz używać.

Jeśli planujesz utworzenie własnego projektu wykrywania obiektu za pomocą usługi Custom Vision, możesz chcieć usunąć projekt wykrywania logo utworzony w ramach tego samouczka. Bezpłatna wersja próbna usługi Custom Vision dopuszcza tylko dwa projekty. Aby usunąć projekt wykrywania logo w [witrynie internetowej usługi Custom Vision](https://customvision.ai), otwórz pozycję **Projects (Projekty)**, a następnie wybierz ikonę kosza na śmieci w obszarze **My New Project (Mój nowy projekt)**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku została skonfigurowana i przebadana w pełni funkcjonalna aplikacja platformy Xamarin.Forms, która używa usługi Custom Vision do wykrywania logo na zdjęciach z aparatu urządzenia przenośnego. Następnie poznaj najlepsze rozwiązania dotyczące budowania modelu usługi Custom Vision, dzięki czemu podczas tworzenia własnych aplikacji możesz zapewnić ich wydajność i dokładność.

> [!div class="nextstepaction"]
> [Jak poprawić swój klasyfikator](getting-started-improving-your-classifier.md)
