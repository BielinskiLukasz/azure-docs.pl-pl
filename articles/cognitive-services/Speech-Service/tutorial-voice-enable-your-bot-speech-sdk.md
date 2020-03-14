---
title: 'Samouczek: głosy umożliwiają bot przy użyciu zestawu mowy SDK-Speech Service'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz bot echo przy użyciu programu Microsoft bot-Framework, wdróżesz go na platformie Azure i zarejestrujesz za pomocą kanału mowy z obsługą instrukcji bot-Framework Direct line. Następnie skonfigurujesz przykładową aplikację kliencką dla systemu Windows, która pozwala mówić do bot i słyszy, że reagują na Ciebie.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.openlocfilehash: 9112c7070708f3b97d79c1978a9b7204721c3194
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220403"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Samouczek: Voice — Włączanie bot przy użyciu zestawu Speech SDK

Możesz teraz korzystać z możliwości usługi mowy, aby łatwo głosować i bot rozmowę.

W tym samouczku utworzysz bot echo przy użyciu programu Microsoft bot-Framework, wdróżesz go na platformie Azure i zarejestrujesz za pomocą kanału mowy z obsługą instrukcji bot-Framework Direct line. Następnie skonfigurujesz przykładową aplikację kliencką dla systemu Windows, która pozwala mówić do bot i słyszy, że reagują na Ciebie.

Ten samouczek jest przeznaczony dla deweloperów, którzy po prostu rozpoczynają swoją podróż przy użyciu platformy Azure, bot-Framework botów, bezpośredniej pomocy liniowej lub zestawu Speech SDK, a także chcą szybko utworzyć system roboczy z ograniczonym kodowaniem. Nie trzeba mieć doświadczenia ani znajomości tych usług.

Po zakończeniu tego ćwiczenia zostanie skonfigurowany system, który będzie działać w następujący sposób:

1. Przykładowa aplikacja kliencka jest skonfigurowana do nawiązywania połączenia z kanałem mowy z linią Direct i echo bot
1. Dźwięk jest nagrywany z domyślnego mikrofonu przy naciśnięciu przycisku (lub ciągle zarejestrowano, jeśli słowo kluczowe Custom jest aktywowane)
1. Opcjonalnie wykrywanie niestandardowego słowa kluczowego ma miejsce, kontroli przesyłanie strumieniowe audio do chmury
1. Za pomocą zestawu Speech SDK aplikacja nawiązuje połączenie z bezpośrednim kanałem mowy i strumieniem dźwięku
1. Opcjonalnie w usłudze występuje przeprowadzenie weryfikacji słowa kluczowego o większej dokładności
1. Dźwięk jest przesyłany do usługi rozpoznawania mowy i uzyskanego do tekstu
1. Rozpoznany tekst jest przesyłany do elementu echo-bot jako działanie bot Framework 
1. Tekst odpowiedzi jest przekształcany w dźwięk przez usługę zamiany tekstu na mowę (TTS) i przesyłany strumieniowo do aplikacji klienckiej na potrzeby odtwarzania

![Diagram — tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Przepływ kanału mowy")

> [!NOTE]
> Kroki opisane w tym samouczku nie wymagają płatnej usługi. Jako nowy użytkownik platformy Azure będziesz mieć możliwość użycia kredytów z bezpłatnej subskrypcji wersji próbnej platformy Azure i bezpłatnej warstwy usługi mowy do ukończenia tego samouczka.

Oto, co obejmuje ten samouczek:
> [!div class="checklist"]
> * Tworzenie nowych zasobów platformy Azure
> * Kompiluj, Testuj i wdrażaj przykład bot ECHA do Azure App Service
> * Zarejestruj swój bot za pomocą kanału mowy z bezpośrednim wierszem
> * Kompiluj i uruchamiaj bezpośredni wiersz klienta mowy do współpracy z bot ECHA
> * Dodaj aktywację niestandardowego słowa kluczowego
> * Dowiedz się, jak zmienić język rozpoznawanej i głosowej mowy

## <a name="prerequisites"></a>Wymagania wstępne

Oto co należy zrobić, aby ukończyć ten samouczek:

- KOMPUTER z systemem Windows 10 z działającym mikrofonem i głośnikami (lub słuchawą)
- [Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) lub nowszy
- [Zestaw .NET Core SDK](https://dotnet.microsoft.com/download) wersja 2,1 lub nowsza
- Konto platformy Azure. [Zarejestruj się bezpłatnie](https://azure.microsoft.com/free/ai/).
- Konto usługi [GitHub](https://github.com/)
- [Git dla systemu Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Aplikacja kliencka utworzona w tym samouczku korzysta z kilku usług platformy Azure. Aby skrócić czas błądzenia odpowiedzi z bot, upewnij się, że te usługi znajdują się w tym samym regionie świadczenia usługi Azure. W tej sekcji utworzysz grupę zasobów w regionie **zachodnie stany USA** . Ta grupa zasobów zostanie użyta podczas tworzenia poszczególnych zasobów dla bot-Framework, bezpośredniego kanału mowy i usługi mowy.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Tworzenie grupy zasobów<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. Zostanie wyświetlony monit o podanie pewnych informacji:
   * Ustaw **subskrypcję** na **bezpłatną wersję próbną** (możesz także użyć istniejącej subskrypcji).
   * Wprowadź nazwę **grupy zasobów**. Zalecamy **SpeechEchoBotTutorial-zasobów**.
   * Z listy rozwijanej **region** wybierz pozycję **zachodnie stany USA**.
1. Kliknij pozycję **Przejrzyj i utwórz**. Powinien pojawić się transparent informujący o pomyślnym **sprawdzeniu poprawności**odczytu.
1. Kliknij przycisk **Utwórz**. Utworzenie grupy zasobów może potrwać kilka minut.
1. Podobnie jak w przypadku zasobów utworzonych w dalszej części tego samouczka, dobrym pomysłem jest Przypinanie tej grupy zasobów do pulpitu nawigacyjnego w celu ułatwienia dostępu. Jeśli chcesz przypiąć tę grupę zasobów, kliknij ikonę pinezki w prawym górnym rogu pulpitu nawigacyjnego.

### <a name="choosing-an-azure-region"></a>Wybieranie regionu platformy Azure

Jeśli chcesz użyć innego regionu dla tego samouczka, te czynniki mogą ograniczyć wybór:

* Upewnij się, że korzystasz z [obsługiwanego regionu platformy Azure](regions.md#voice-assistants).
* Kanał mowy linii Direct używa usługi zamiany tekstu na mowę, która ma standardowe i neuronowych głosy. Głosy neuronowych są [ograniczone do określonych regionów świadczenia usługi Azure](regions.md#standard-and-neural-voices).
* Klucze bezpłatnej wersji próbnej mogą być ograniczone do określonego regionu.

Aby uzyskać więcej informacji na temat regionów, zobacz temat [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Tworzenie zasobów

Teraz, gdy masz grupę zasobów w obsługiwanym regionie, następnym krokiem jest utworzenie poszczególnych zasobów dla każdej usługi, która będzie używana w tym samouczku.

### <a name="create-a-speech-service-resource"></a>Tworzenie zasobu usługi mowy

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć zasób mowy:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu usługi mowy<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Zostanie wyświetlony monit o podanie pewnych informacji:
   * Nadaj **nazwę**zasobowi. Zalecamy **SpeechEchoBotTutorial-mowę**
   * W przypadku **subskrypcji**upewnij się, że wybrano **bezpłatną wersję próbną** .
   * W obszarze **Lokalizacja**wybierz pozycję **zachodnie stany USA**.
   * W obszarze **warstwa cenowa**wybierz pozycję **F0**. Jest to bezpłatna warstwa.
   * W obszarze **Grupa zasobów**wybierz pozycję **SpeechEchoBotTutorial-Resources**.
5. Po wprowadzeniu wszystkich wymaganych informacji kliknij pozycję **Utwórz**. Utworzenie zasobu może potrwać kilka minut.
6. W dalszej części tego samouczka będziesz potrzebować kluczy subskrypcji dla tej usługi. Dostęp do tych kluczy można uzyskać w dowolnym momencie z **omówienia** zasobów (zarządzanie kluczami) lub **kluczy**.

W tym momencie Sprawdź, czy grupa zasobów (**SpeechEchoBotTutorial-Resource**Group) zawiera zasób mowy:

| Name (Nazwa) | Typ  | Lokalizacja |
|------|-------|----------|
| SpeechEchoBotTutorial — Speech | Cognitive Services | Zachodnie stany USA |

### <a name="create-an-azure-app-service-plan"></a>Tworzenie planu usługi Azure App Service

Następnym krokiem jest utworzenie planu App Service. Plan usługi App Service definiuje zestaw zasobów obliczeniowych dla aplikacji internetowej używanych podczas jej uruchamiania.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Utwórz plan Azure App Service<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. Zostanie wyświetlony monit o podanie pewnych informacji:
   * Ustaw **subskrypcję** na **bezpłatną wersję próbną** (możesz także użyć istniejącej subskrypcji).
   * W obszarze **Grupa zasobów**wybierz pozycję **SpeechEchoBotTutorial-Resources**.
   * Nadaj **nazwę**zasobowi. Zalecamy **SpeechEchoBotTutorial-AppServicePlan**
   * W obszarze **system operacyjny**wybierz pozycję **Windows**.
   * W **obszarze region**wybierz pozycję **zachodnie stany USA**.
   * W przypadku **warstwy cenowej**upewnij się, że wybrano opcję **standardowa S1** . Powinna to być wartość domyślna. Jeśli nie, upewnij się, że **system operacyjny** jest ustawiony na **system Windows** zgodnie z powyższym opisem.
5. Kliknij pozycję **Przejrzyj i utwórz**. Powinien pojawić się transparent informujący o pomyślnym **sprawdzeniu poprawności**odczytu.
6. Kliknij przycisk **Utwórz**. Utworzenie grupy zasobów może potrwać kilka minut.

Na tym etapie należy sprawdzić, czy grupa zasobów (**SpeechEchoBotTutorial-Resource**Group) ma dwa zasoby:

| Name (Nazwa) | Typ  | Lokalizacja |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | Plan usługi App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial — Speech | Cognitive Services | Zachodnie stany USA |

## <a name="build-an-echo-bot"></a>Tworzenie bot ECHA

Teraz, po utworzeniu niektórych zasobów, przyjrzyjmy się bot. Zaczniemy od przykładu echo bot, który oznacza, że nazwa wskazuje, że tekst wprowadzony jako odpowiedź zostanie wyświetlony. Nie martw się, przykładowy kod jest gotowy do użycia bez wprowadzania żadnych zmian. Jest ona konfigurowana do pracy z kanałem bezpośredniego wiersza mowy, który zostanie nawiązane po wdrożeniu Bot na platformie Azure.

> [!NOTE]
> Poniższe instrukcje, a także dodatkowe informacje na temat ECHA bot są dostępne w [pliku Readme przykładu w serwisie GitHub](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md).

### <a name="run-the-bot-sample-on-your-machine"></a>Uruchamianie przykładu Bot na maszynie

1. Klonuj repozytorium przykładów:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Uruchom program Visual Studio.
3. Na pasku narzędzi wybierz pozycję **plik** > **Otwórz** > **projekt/rozwiązanie**, a następnie otwórz rozwiązanie bot projektu echo:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Po załadowaniu projektu naciśnij klawisz <kbd>F5</kbd> , aby skompilować i uruchomić projekt.
5. Przeglądarka powinna zostać uruchomiona i zobaczysz ekran podobny do tego.
    > [!div class="mx-imgBorder"]
    > [![echobot — uruchamianie na hoście lokalnym](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "EchoBot uruchomione na hoście lokalnym")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Testowanie przykładu bot za pomocą emulatora platformy bot Framework

[Emulator bot Framework](https://github.com/microsoft/botframework-emulator) to aplikacja klasyczna, która umożliwia deweloperom bot testowanie i debugowanie botów lokalnie lub zdalnie za pomocą tunelu. Emulator obsługuje wpisywany tekst jako dane wejściowe (nie głosowo). Bot będzie reagować z tekstem. Wykonaj następujące kroki, aby użyć emulatora bot Framework do testowania ECHA bot uruchomionego lokalnie z danymi wejściowymi i wyjściowymi tekstu. Po wdrożeniu Bot na platformie Azure przetestujemy go za pomocą danych wejściowych głosowych i głosowych.

1. Zainstaluj [emulator bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) w wersji 4.3.0 lub nowszej
2. Uruchom emulator bot Framework i Otwórz bot:
   * **Plik** -> **Otwórz bot**.
3. Wprowadź adres URL bot. Na przykład:

   ```
   http://localhost:3978/api/messages
   ```
   i naciśnij przycisk "Połącz".
4. Bot powinien natychmiast powitać użytkownika "Witaj i Witaj!" Komunikat. Wpisz dowolną wiadomość tekstową i Potwierdź, że otrzymasz odpowiedź od bot.
5. Jest to sposób, w jaki wymiana komunikacji z wystąpieniem ECHA bot może wyglądać następująco: [ ![bot-Framework-emulator](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Emulator struktury bot Framework")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Wdróż swój bot w Azure App Service

Następnym krokiem jest wdrożenie ECHA Bot na platformie Azure. Istnieje kilka sposobów wdrożenia bot, ale w tym samouczku będziemy skupić się na publikowaniu bezpośrednio z programu Visual Studio.

> [!NOTE]
> Alternatywnie można wdrożyć bot przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) i [szablonów wdrożenia](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates).

1. W programie Visual Studio Otwórz aplet echo bot, który został skonfigurowany do użycia z bezpośrednim kanałem mowy liniowej:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **EchoBot** i wybierz pozycję **Publikuj...**
1. Nowe okno zatytułowane **Wybieranie elementu docelowego publikowania** zostanie otwarte.
1. Wybierz **App Service** z nawigacji **usług platformy Azure** , wybierz pozycję **Utwórz nowy**, a następnie kliknij pozycję **Utwórz profil**.
1. Po wyświetleniu okna **utwórz App Service** :
   * Kliknij pozycję **Dodaj konto**i zaloguj się przy użyciu poświadczeń konta platformy Azure. Jeśli użytkownik jest już zalogowany, wybierz żądane konto z listy rozwijanej.
   * W przypadku **nazwy aplikacji**należy wprowadzić globalnie unikatową nazwę dla bot. Ta nazwa służy do tworzenia unikatowego adresu URL bot. Zostanie wypełniona wartość domyślna, w tym datę i godzinę (na przykład: "EchoBot20190805125647"). Możesz użyć domyślnej nazwy dla tego samouczka.
   * W przypadku **subskrypcji**ustaw ją na **bezpłatną wersję próbną**
   * W obszarze **Grupa zasobów**wybierz pozycję **SpeechEchoBotTutorial-zasobów**
   * W przypadku **planu hostingu**wybierz pozycję **SpeechEchoBotTutorial — AppServicePlan**
   * W przypadku **Application Insights**pozostaw jako **Brak**
1. Kliknij przycisk **Utwórz**
1. W programie Visual Studio powinien zostać wyświetlony komunikat o powodzeniu, który wygląda następująco:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Twoja domyślna przeglądarka powinna zostać otwarta i zostanie wyświetlona strona, która odczytuje: "Twoje bot jest gotowe!".
1. W tym momencie Sprawdź grupę zasobów **SpeechEchoBotTutorial-Resource** w Azure Portal i Potwierdź, że istnieją trzy zasoby:

| Name (Nazwa) | Typ  | Lokalizacja |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial-AppServicePlan | Plan usługi App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial — Speech | Cognitive Services | Zachodnie stany USA |

## <a name="enable-web-sockets"></a>Włącz gniazda sieci Web

Musisz wprowadzić małą zmianę konfiguracji, aby bot mógł komunikować się z kanałem mowy linii Direct przy użyciu gniazd sieci Web. Wykonaj następujące kroki, aby włączyć gniazda sieci Web:

1. Przejdź do [Azure Portal](https://portal.azure.com)i Znajdź App Service. Zasób powinien mieć nazwę podobną do **EchoBot20190805125647** (unikatową nazwę aplikacji).
2. W obszarze Nawigacja **usług platformy Azure** w obszarze **Ustawienia**kliknij pozycję **Konfiguracja**.
3. Wybierz kartę **Ustawienia ogólne** .
4. Znajdź przełącznik dla **gniazd sieci Web** i ustaw go na wartość **włączone**.
5. Kliknij przycisk **Save** (Zapisz).

> [!TIP]
> Możesz użyć kontrolek w górnej części strony Azure App Service, aby zatrzymać lub ponownie uruchomić usługę. Może się to okazać przydatne podczas rozwiązywania problemów.

## <a name="create-a-channel-registration"></a>Tworzenie rejestracji kanału

Teraz, po utworzeniu Azure App Service do hostowania bot, następnym krokiem jest utworzenie **rejestracji kanałów bot**. Tworzenie rejestracji kanału jest wymaganiem wstępnym rejestrowania bot za pomocą kanałów bot-Framework, w tym kanału Direct Speech line.

> [!NOTE]
> Jeśli chcesz dowiedzieć się więcej o tym, jak botów korzystać z kanałów, zobacz [Connect a bot to Channels](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Tworzenie rejestracji kanałów usługi Azure bot<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. Zostanie wyświetlony monit o podanie pewnych informacji:
   * Dla **uchwytu bot**wprowadź **SpeechEchoBotTutorial-BotRegistration**.
   * W obszarze **subskrypcja**wybierz opcję **bezpłatna wersja próbna**.
   * W obszarze **Grupa zasobów**wybierz pozycję **SpeechEchoBotTutorial-Resources**.
   * W obszarze **Lokalizacja**wybierz pozycję **zachodnie stany USA**.
     * W obszarze **warstwa cenowa**wybierz pozycję **F0**.
     * W polu **punkt końcowy obsługi komunikatów**wprowadź adres URL aplikacji internetowej z dołączoną ścieżką `/api/messages` na końcu. Na przykład: Jeśli Twoja globalnie unikatowa nazwa aplikacji została **EchoBot20190805125647**, punkt końcowy obsługi komunikatów będzie: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * W przypadku usługi **Application Insights**można ustawić tę wartość na **off**. Aby uzyskać więcej informacji, zobacz [bot Analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * Ignoruj **Autotworzenie identyfikatora aplikacji i hasła**.
5. W dolnej części bloku **rejestracji kanałów bot** kliknij pozycję **Utwórz**.

W tym momencie Sprawdź grupę zasobów **SpeechEchoBotTutorial-Resource** w Azure Portal. Powinny teraz być widoczne cztery zasoby:

| Name (Nazwa) | Typ  | Lokalizacja |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial-AppServicePlan | Plan usługi App Service | Zachodnie stany USA |
| SpeechEchoBotTutorial-BotRegistration | Rejestracja kanałów bot | global |
| SpeechEchoBotTutorial — Speech | Cognitive Services | Zachodnie stany USA |

> [!IMPORTANT]
> Zasób rejestracji kanałów bot będzie wyświetlany w regionie globalnym, mimo że wybrano zachodnie stany USA. Jest to oczekiwane.

## <a name="register-the-direct-line-speech-channel"></a>Rejestrowanie kanału mowy w linii bezpośredniej

Teraz czas na zarejestrowanie bot za pomocą kanału bezpośredniej linii mowy. Ten kanał jest używany do tworzenia połączenia między bot ECHA a aplikacją kliencką skompilowaną przy użyciu zestawu Speech SDK.

1. Zlokalizuj i Otwórz zasób **SpeechEchoBotTutorial-BotRegistration** w [Azure Portal](https://portal.azure.com).
1. W obszarze Nawigacja **usług platformy Azure** wybierz pozycję **kanały**.
   * Wyszukaj **więcej kanałów**, Znajdź i kliknij pozycję **Direct line Speech**.
   * Przejrzyj tekst na stronie zatytułowanej **Konfigurowanie Direct line Speech**, a następnie rozwiń menu rozwijane zatytułowane "konto usługi poznawczej".
   * Wybierz utworzony wcześniej zasób mowy (np. **SpeechEchoBotTutorial-Speech**) z menu, aby skojarzyć bot z kluczem subskrypcji mowy.
   * Kliknij przycisk **Save** (Zapisz).

1. W obszarze Nawigacja **bot Management** kliknij pozycję **Ustawienia**.
   * Zaznacz pole wyboru z etykietą **Włącz punkt końcowy przesyłania strumieniowego**. Jest to konieczne do włączenia protokołu komunikacyjnego opartego na gniazdach sieci Web między bot i kanałem mowy liniowej.
   * Kliknij przycisk **Save** (Zapisz).

> [!TIP]
> Jeśli chcesz dowiedzieć się więcej, zobacz [łączenie bot z bezpośrednim wierszem mowy](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Ta strona zawiera dodatkowe informacje i znane problemy.

## <a name="build-the-direct-line-speech-client"></a>Tworzenie bezpośredniego klienta mowy w wierszu

W tym kroku nastąpi utworzenie bezpośredniego klienta mowy line. Klient to aplikacja Windows Presentation Foundation (WPF) w programie C# , która korzysta z [zestawu Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) do zarządzania komunikacją z bot za pomocą kanału mowy linii Direct. Użyj jej do korzystania z i testowania bot przed zapisaniem niestandardowej aplikacji klienckiej.

Klient funkcji bezpośredniej obsługi mowy ma prosty interfejs użytkownika, który umożliwia skonfigurowanie połączenia z usługą bot, wyświetlenie konwersacji tekstowej, wyświetlenie działań bot-Framework w formacie JSON oraz wyświetlenie kart adaptacyjnych. Obsługuje również użycie niestandardowych słów kluczowych. Ten klient będzie używany do mówienia do bot i otrzymywania odpowiedzi głosowej.

Przed przejściem upewnij się, że mikrofon i głośniki są włączone i działają.

1. Przejdź do repozytorium GitHub, aby uzyskać [bezpośredni wiersz klienta mowy](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md).
2. Postępuj zgodnie z podanymi instrukcjami, aby sklonować repozytorium, skompilować projekt, skonfigurować klienta i uruchomić klienta.
3. Kliknij przycisk **Połącz ponownie** i upewnij się, że zobaczysz komunikat **, naciśnij przycisk mikrofonu, lub wpisz, aby zacząć mówić do bot**.
4. Przetestujmy ją. Kliknij przycisk Microphone (mikrofon) i zacznij mówić kilka słów w języku angielskim. Rozpoznany tekst pojawi się w trakcie mówienia. Gdy skończysz mówić, bot odpowie na swoim głosie, mówiąc "Echo", po którym następuje rozpoznane słowa.
5. Możesz również użyć tekstu do komunikowania się z bot. Po prostu wpisz tekst na dolnym pasku. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Rozwiązywanie problemów z błędami w bezpośrednim wierszu klient mowy

Jeśli w oknie głównym aplikacji zostanie wyświetlony komunikat o błędzie, Skorzystaj z tej tabeli, aby zidentyfikować i rozwiązać problem:

| Błąd | Co należy zrobić? |
|-------|----------------------|
|Błąd AuthenticationFailure: uaktualnienie protokołu WebSocket nie powiodło się z powodu błędu uwierzytelniania (401). Sprawdź poprawność klucza subskrypcji (lub tokenu autoryzacji) i nazwy regionu| Na stronie Ustawienia aplikacji upewnij się, że wprowadzono prawidłowy klucz subskrypcji mowy i jego region.<br>Upewnij się, że klucz mowy i klucz regionu zostały wprowadzone poprawnie. |
|Błąd ConnectionFailure: połączenie zostało zamknięte przez hosta zdalnego. Kod błędu: 1011. Szczegóły błędu: nie można nawiązać połączenia z usługą bot przed wysłaniem komunikatu | Upewnij się, że pole wyboru ["Włącz punkt końcowy przesyłania strumieniowego"](#register-the-direct-line-speech-channel) i/lub przełączono [ **gniazda sieci Web** ](#enable-web-sockets) na wartość włączone.<br>Upewnij się, że Azure App Service jest uruchomiony. Jeśli tak, spróbuj uruchomić ponownie App Service.|
|Błąd ConnectionFailure: połączenie zostało zamknięte przez hosta zdalnego. Kod błędu: 1011. Szczegóły błędu: kod stanu odpowiedzi nie wskazuje sukcesu: 500 (InternalServerError)| Bot określiła głos neuronowych w polu [wymawiane](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) działanie wyjściowe, ale region platformy Azure skojarzony z kluczem subskrypcji mowy nie obsługuje głosów neuronowych. Zobacz [głosy standardowe i neuronowych](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Błąd ConnectionFailure: połączenie zostało zamknięte przez hosta zdalnego. Kod błędu: 1000. Szczegóły błędu: przekroczono maksymalny czas bezczynności połączenia gniazda internetowego (> 300000 MS)| Jest to oczekiwany błąd, gdy połączenie z kanałem pozostaje otwarte i nieaktywne przez ponad pięć minut. |

Jeśli problem nie został rozwiązany w tabeli, zobacz [asystentów głosowych: często zadawane pytania](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Wyświetlanie działań bot

Każdy bot wysyła i odbiera komunikaty **aktywności** . W oknie **Dziennik aktywności** klienta usługi Direct line Speech można zobaczyć dzienniki z sygnaturami czasowymi z poszczególnymi działaniami, które klient otrzymał z bot. Możesz również zobaczyć działania wysyłane przez klienta do bot przy użyciu metody [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) . Po wybraniu elementu dziennika zostaną wyświetlone szczegóły skojarzonego działania jako plik JSON.

Oto przykładowy kod JSON działania otrzymanego przez klienta:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Aby dowiedzieć się więcej na temat informacji zwracanych w danych wyjściowych JSON, zobacz [pola w działaniu](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Na potrzeby tego samouczka możesz skupić się na polach [tekst](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) i [czytaj](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) .

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Wyświetl kod źródłowy klienta dla wywołań zestawu Speech SDK

Klient mowy linii Direct używa pakietu NuGet [Microsoft. CognitiveServices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/), który zawiera zestaw Speech SDK. Dobrym miejscem, aby rozpocząć przeglądanie przykładowego kodu, jest metoda InitSpeechConnector () w pliku [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs), która tworzy te dwa obiekty zestawu Speech SDK:
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) — dla ustawień konfiguracji (np. klucza subskrypcji mowy, regionu klucza)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) — w celu zarządzania zdarzeniami dotyczącymi połączeń kanałów i subskrypcji klienta na potrzeby obsługi rozpoznanych odpowiedzi mowy i bot.

## <a name="add-custom-keyword-activation"></a>Dodaj aktywację niestandardowego słowa kluczowego

Zestaw Speech SDK obsługuje aktywację niestandardowego słowa kluczowego. Podobnie jak "Hey Cortana" dla Asystenta firmy Microsoft, można napisać aplikację, która będzie w sposób ciągły nasłuchiwać wybranego słowa kluczowego. Należy pamiętać, że słowo kluczowe może być pojedynczym słowem lub frazą z autosłowami.

> [!NOTE]
> Termin *słowo kluczowe* jest często używany zamiennie z terminem *wznawiania*i może być widoczne zarówno w dokumentacji firmy Microsoft.

Wykrywanie słowa kluczowego jest wykonywane w aplikacji klienckiej. W przypadku użycia słowa kluczowego dźwięk jest przesyłany strumieniowo do kanału mowy z bezpośrednim wierszem, jeśli słowo kluczowe zostanie wykryte. Kanał mowy linii bezpośredniej zawiera składnik o nazwie *Verification (KWV)* , który wykonuje bardziej skomplikowane przetwarzanie w chmurze, aby sprawdzić, czy wybrane słowo kluczowe jest na początku strumienia audio. Jeśli weryfikacja słowa kluczowego powiedzie się, kanał będzie komunikować się z bot.

Wykonaj następujące kroki, aby utworzyć model słów kluczowych, skonfigurować bezpośredni klient rozpoznawania mowy do korzystania z tego modelu, a na koniec przetestuj go za pomocą bot.

1. Postępuj zgodnie z tymi instrukcjami, aby [utworzyć niestandardowe słowo kluczowe przy użyciu usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
2. Rozpakuj plik modelu pobrany w poprzednim kroku. Należy ją nazwać dla słowa kluczowego. Szukasz pliku o nazwie `kws.table`.
3. W bezpośrednim wierszu klienta mowy odszukaj menu **Ustawienia** (poszukaj ikony koła zębatego w prawym górnym rogu). Znajdź **ścieżkę pliku modelu** i wprowadź pełną nazwę ścieżki dla pliku `kws.table` z kroku 2.
4. Upewnij się, że pole wyboru zatytułowane **włączona**. Ten komunikat powinien pojawić się obok pola wyboru: "nasłuchuje słowa kluczowego przy następnym połączeniu". Jeśli podano niewłaściwy plik lub niepoprawną ścieżkę, powinien zostać wyświetlony komunikat o błędzie.
5. Wprowadź **klucz subskrypcji**mowy, **region klucza subskrypcji**, a następnie kliknij przycisk **OK** , aby zamknąć menu **Ustawienia** .
6. Kliknij przycisk **Połącz ponownie**. Powinien zostać wyświetlony komunikat o błędzie: "Nowa konwersacja została rozpoczęta — wpisz, naciśnij przycisk mikrofonu lub wymów słowo kluczowe". Aplikacja ciągle nasłuchuje.
7. Czytaj wszystkie frazy, które zaczynają się od słowa kluczowego. Na przykład: " **{słowo kluczowe}** , jaki jest czas?". Nie musisz wstrzymywać po wprowadzeniu słowa kluczowego. Po zakończeniu są wykonywane dwie czynności:
   * Zobaczysz transkrypcję
   * Wkrótce otrzymasz odpowiedź bot
8. Kontynuuj eksperymentowanie z trzema typami danych wejściowych obsługiwanymi przez program bot:
   * Tekst tekstowy na dolnym pasku
   * Naciskanie ikony mikrofonu i mówiąc
   * Wymawiając frazę rozpoczynającą się od słowa kluczowego

### <a name="view-the-source-code-that-enables-keyword"></a>Wyświetl kod źródłowy, który umożliwia użycie słowa kluczowego

W kodzie źródłowym klienta mowy linii bezpośredniej Poszukaj tych plików, aby przejrzeć kod, który jest używany do włączenia wykrywania słowa kluczowego:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs) obejmuje wywołanie metody zestawu Speech SDK [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-), która jest używana do tworzenia wystąpienia modelu z pliku lokalnego na dysku.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) zawiera wywołanie metody zestawu Speech SDK [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync), która aktywuje funkcję wykrywania ciągłego słowa kluczowego.

## <a name="optional-change-the-language-and-bot-voice"></a>Obowiązkowe Zmień język i Głos bot

Bot, który utworzyłeś, nasłuchuje i reaguje w języku angielskim przy użyciu domyślnego angielskiej wersji językowej zamiany tekstu na mowę. Nie można jednak używać języka angielskiego ani domyślnego głosu. W tej sekcji dowiesz się, jak zmienić język, w którym bot nasłuchuje i reagować. Dowiesz się również, jak wybrać inny głos dla tego języka.

### <a name="change-the-language"></a>Zmień język

Można wybrać jeden z języków wymienionych w tabeli [Zamiana mowy na tekst](language-support.md#speech-to-text) . W poniższym przykładzie zmienimy język na niemiecki.

1. Otwórz aplikację klienta bezpośredniej obsługi mowy w wierszu, kliknij przycisk Ustawienia (ikona koła zębatego), a następnie wprowadź `de-de` w polu język (jest to wartość ustawień regionalnych wymienionych w tabeli [zamiany mowy na tekst](language-support.md#speech-to-text) ). To ustawienie określa, że język mówiony zostanie rozpoznany, zastępując domyślne `en-us`. Powoduje to również, że kanał radiowy Direct line w celu użycia domyślnego głosu w języku niemieckim dla odpowiedzi bot.
2. Zamknij stronę Ustawienia, a następnie kliknij przycisk Połącz ponownie, aby nawiązać nowe połączenie z bot echo.
3. Kliknij przycisk Microphone (mikrofon) i wymów frazę w języku niemieckim. Zobaczysz rozpoznany tekst i odpowiedzi echa bot przy użyciu domyślnego głosu niemieckiego.

### <a name="change-the-default-bot-voice"></a>Zmiana domyślnego głosu bot

Wybranie głosu zamiany tekstu na mowę i kontrolowanie wymowy można wykonać, jeśli bot określa odpowiedź w postaci języka SSML ( [Speech syntezing](speech-synthesis-markup.md) Language) zamiast prostego tekstu. Bot ECHA nie używa SSML, ale możemy łatwo zmodyfikować kod, aby to zrobić. W poniższym przykładzie dodamy SSML do odpowiedzi echa bot, w taki sposób, że zamiast domyślnego głosu żeńskiego będą używane niemieckie Apollo głosu (głos męski). Zobacz listę odniesień [standardowych głosów](language-support.md#standard-voices) i [neuronowychych głosów](language-support.md#neural-voices) obsługiwanych w Twoim języku.

1. Zacznijmy od otwarcia `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`.
2. Znajdź te dwa wiersze:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Zamień na:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Kompiluj rozwiązanie w programie Visual Studio i napraw wszelkie błędy kompilacji.

Drugi argument metody "MessageFactory. text" ustawia [pole dyktowanie działania](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) w odpowiedzi bot. W przypadku powyższej zmiany został on zastąpiony tekstem prostym do SSML w celu określenia niestandardowej postaci głosu niemieckiego.

### <a name="redeploy-your-bot"></a>Ponowne wdrażanie bot

Po wykonaniu niezbędnych zmian w bot następnym krokiem jest ponowne opublikowanie go na Azure App Service i wypróbowanie:

1. W oknie Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **EchoBot** i wybierz polecenie **Publikuj**.
2. Poprzednia konfiguracja wdrożenia została już załadowana jako domyślna. Po prostu kliknij pozycję **Opublikuj** obok pozycji **EchoBot20190805125647-Web Deploy**.
3. W oknie danych wyjściowych programu Visual Studio zostanie wyświetlony komunikat **Publikowanie zakończyło się pomyślnie** , a na stronie sieci Web zostanie uruchomiony komunikat "Twoje bot jest gotowe!".
4. Otwórz aplikację klient bezpośredniego wiersza polecenia, kliknij przycisk Ustawienia (ikonę koła górnego) i upewnij się, że w polu Język nadal znajduje się `de-de`.
5. Postępuj zgodnie z instrukcjami w temacie [Tworzenie bezpośredniego klienta mowy wiersza](#build-the-direct-line-speech-client) , aby ponownie nawiązać połączenie z nowo wdrożonym bot, wypowiedz w nowym języku i posłuchaj bot odpowiedzi w tym języku z nowym głosem.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z funkcji echo-bot wdrożonej w tym samouczku, możesz usunąć ją i wszystkie skojarzone z nią zasoby platformy Azure, usuwając po prostu grupę zasobów platformy Azure **SpeechEchoBotTutorial-resourceName**.

1. W [Azure Portal](https://portal.azure.com)kliknij pozycję **grupy zasobów** w obszarze Nawigacja **usług platformy Azure** .
2. Znajdź grupę zasobów o nazwie: **SpeechEchoBotTutorial-Resource**Group. Kliknij trzy kropki (...).
3. Wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie własnej aplikacji klienckiej przy użyciu zestawu Speech SDK](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Zobacz też

* Wdrażanie w [regionie świadczenia usługi Azure w sąsiedztwie,](https://azure.microsoft.com/global-infrastructure/locations/) aby zobaczyć bot poprawy czasu odpowiedzi
* Wdrażanie w [regionie świadczenia usługi Azure, który obsługuje neuronowychy TTS o wysokiej jakości](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* Cennik związany z bezpośrednim kanałem mowy wiersza:
  * [Cennik usługi bot](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Usługa mowy](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Kompilowanie i wdrażanie własnych bot z obsługą głosu:
  * Kompiluj [bot-Framework bot](https://dev.botframework.com/). Zarejestruj się w [kanale mowy z linią Direct line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) i [Dostosuj Bot na potrzeby głosu](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Poznaj istniejące [rozwiązania bot-Framework](https://microsoft.github.io/botframework-solutions/index): Kompiluj [asystenta wirtualnego](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) i [rozwiń go, aby uzyskać bezpośrednią mowę liniową](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
