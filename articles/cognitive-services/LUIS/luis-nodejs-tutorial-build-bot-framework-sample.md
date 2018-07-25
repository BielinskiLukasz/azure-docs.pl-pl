---
title: Integrowanie usługi LUIS z botem przy użyciu zestawu SDK Bot Builder dla środowiska Node.js na platformie Azure | Dokumentacja firmy Microsoft
description: Twórz Boty zintegrowane z aplikacją usługi LUIS przy użyciu platformy Bot Framework.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: diberry
ms.openlocfilehash: 6d6937105b11d94138b51660dc9f3c5e682e19bc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224079"
---
# <a name="integrate-luis-with-a-bot-using-the-bot-builder-sdk-for-nodejs"></a>Integrowanie usługi LUIS z botem przy użyciu zestawu SDK Bot Builder dla platformy Node.js

Ten samouczek przeprowadzi Cię przez tworzenie botów za pomocą [platformy Bot Framework] [ BotFramework] zintegrowana z aplikacją usługi LUIS.

## <a name="prerequisite"></a>Wymagania wstępne

Przed przystąpieniem do tworzenia bota należy wykonać czynności opisane w [tworzenie aplikacji](./luis-get-started-create-app.md) do tworzenia aplikacji usługi LUIS, która go używa.

Bot reaguje na intencje z domeny HomeAutomation, które znajdują się w aplikacji usługi LUIS. Dla każdego z tych opcji aplikacja usługi LUIS zapewnia cel, który mapuje do niego. Bot zapewnia okno dialogowe, które obsługuje cel, który wykrywa usługi LUIS.

| Przeznaczenie | Przykład wypowiedź | Bot funkcji |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Włączenie świateł. | Wywołuje bot `TurnOnDialog` podczas `HomeAutomation.TurnOn` jest wykryty. To okno dialogowe jest, gdzie możesz powodowałoby wywołanie pliku wykonywalnego usługi IoT w celu włączenia na urządzeniu, a następnie poinformuj użytkowników, że urządzenie zostało włączone. |
| HomeAutomation.TurnOff | Wyłącz światła sypialni. | Wywołuje bot `TurnOffDialog` podczas `HomeAutomation.TurnOff` jest wykryty. To okno dialogowe, gdzie możesz powodowałoby wywołanie pliku wykonywalnego usługi IoT, aby wyłączyć urządzenie i Powiadom użytkownika, że urządzenie zostało wyłączone. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Tworzenie botów Language Understanding przy użyciu usługi Bot Service

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Tworzenie nowego zasobu** w bloku menu, a następnie wybierz pozycję **holograficznych**.

    ![Tworzenie nowego zasobu](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. W polu wyszukiwania, wyszukaj **Bot aplikacji sieci Web**. 

    ![Tworzenie nowego zasobu](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. W **Bot Service** bloku, podaj wymagane informacje, a następnie wybierz pozycję **Utwórz**. To tworzy i wdraża bot service i LUIS aplikacji na platformie Azure. Jeśli chcesz używać [zalewanie mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), przejrzyj [wymagania region](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) przed utworzeniem bota. 
    * Ustaw **nazwy aplikacji** nazwę Twój bot. Nazwa jest używana jako domenę podrzędną, gdy Twój bot jest wdrażane w chmurze (na przykład mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Wybierz subskrypcję, [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), plan usługi App service, a [lokalizacji](https://azure.microsoft.com/regions/).
    * Wybierz **interpretacji języka (Node.js)** szablon **szablonu Bota** pola.
    * Wybierz **lokalizacji aplikacji LUIS**. Jest to, tworzeniem [region] [ LUIS] aplikacja zostanie utworzona w.
    * Zaznacz pole wyboru potwierdzenia prawne. Warunki prawne są wyświetlane poniżej pola wyboru.

    ![Blok usługi BOT](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Upewnij się, czy usługa bot service został pomyślnie wdrożony.
    * Wybierz opcję powiadomienia (ikonę dzwonka, który znajduje się wzdłuż górnej krawędzi w witrynie Azure Portal). Powiadomienie zmieni się z **Wdrażanie rozpoczęte** do **wdrażanie zakończyło się pomyślnie**.
    * Po powiadomienie zmieni się na **wdrażanie zakończyło się pomyślnie**, wybierz opcję **przejdź do zasobu** na powiadomienia.

## <a name="try-the-default-bot"></a>Spróbuj bot domyślne

Upewnij się, że bot został wdrożony, sprawdzając **powiadomienia**. Powiadomienia ulegnie zmianie z **wdrażanie jest w toku...**  do **wdrażanie zakończyło się pomyślnie**. Wybierz **przejdź do zasobu** przycisk, aby otworzyć blok zasobów botów.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Zainstaluj Menedżera NPM zasobów
Zainstaluj pakiety NPM w wykonaj następujące czynności:

1. Wybierz **kompilacji** z **zarządzania Bot** sekcji Bot aplikacji sieci Web. 

2. A druga nowe, zostanie otwarte okno przeglądarki. Wybierz **Otwórz online Edytor kodu**.

3. W górnym pasku nawigacyjnym, wybierz nazwę sieci web aplikacji bot `homeautomationluisbot`. 

4. Z listy rozwijanej wybierz **otwartej konsoli Kudu**.

5. Zostanie otwarte nowe okno przeglądarki. W konsoli wprowadź następujące polecenie:

    ```
    cd site\wwwroot && npm install
    ```

    Poczekaj na zakończenie procesu instalacji. Wróć do okna przeglądarki w pierwszym. 

## <a name="test-in-web-chat"></a>Testowanie w rozmów w sieci Web
Po zarejestrowaniu bot wybierz **testu w czatów internetowych** aby otworzyć okienko czatów internetowych. Wpisz "hello" w czatów internetowych.

  ![Testowanie bota w czatów internetowych](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

Bot odpowiada mówiąc "osiągnięto pozdrowienie. Można powiedzieć: hello ". Będzie to potwierdzenie, że bot otrzymał wiadomości i przekazanego domyślne aplikacją usługi LUIS, którego utworzona. To ustawienie domyślne aplikacją usługi LUIS wykrył intencji pozdrowienie. W następnym kroku połączysz bot się utworzoną wcześniej zamiast domyślnego aplikacją usługi LUIS aplikację usługi LUIS.

## <a name="connect-your-luis-app-to-the-bot"></a>Połącz z aplikacją usługi LUIS do robota

Otwórz **ustawienia aplikacji** w pierwszym okno przeglądarki i Edytuj **LuisAppId** pole będzie zawierać identyfikator aplikacji z aplikacją usługi LUIS.

  ![Zaktualizuj identyfikator aplikacji usługi LUIS na platformie Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Jeśli nie masz Identyfikatora aplikacji LUIS, zaloguj się do [LUIS](luis-reference-regions.md) witryny sieci Web przy użyciu tego samego konta, które umożliwia logowanie do platformy Azure. Wybierz **Moje aplikacje**. 

1. Znajdź LUIS utworzonego wcześniej, zawierający intencje i podmioty, z domeny HomeAutomation.

2. W **ustawienia** stronie aplikacji usługi LUIS, Znajdź i skopiuj identyfikator aplikacji.

3. Jeśli jeszcze nie przeprowadzono uczenie aplikacji, wybierz opcję **szkolenie** przycisk w prawym górnym rogu, to w opracowywaniu aplikacji.

4. Jeśli aplikacja jeszcze nie została opublikowana, wybierz opcję **PUBLIKUJ** na pasku górnym menu nawigacyjnym, aby otworzyć **Publikuj** strony. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

## <a name="modify-the-bot-code"></a>Modyfikowanie kodu bot

Przejdź do drugiej okna przeglądarki, jeśli jest nadal otworzyć lub w pierwszym oknie przeglądarki, zaznacz **kompilacji** , a następnie wybierz **Otwórz online Edytor kodu**.

   ![Otwórz online Edytor kodu](./media/luis-tutorial-node-bot/bot-service-build.png)

Otwórz w edytorze kodu `app.js`. Zawiera następujący kod:

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

Istniejące intencje w app.js są ignorowane. Możesz pozostawić je. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Dodaj okno dialogowe, który odpowiada HomeAutomation.TurnOn

Skopiuj poniższy kod i dodać go do `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

[Odpowiada] [ matches] opcja [element triggerAction] [ triggerAction] dołączone do okna dialogowego Określa nazwę intencji. Aparat rozpoznawania jest uruchamiany po każdym bot otrzymuje wypowiedź od użytkownika. Jeśli odpowiada najwyższej cel oceniania, który wykryje `triggerAction` powiązany z okna dialogowego, bot wywołuje tego okna dialogowego.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Dodaj okno dialogowe, który odpowiada HomeAutomation.TurnOff

Skopiuj poniższy kod i dodać go do `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Testowanie robota

W witrynie Azure Portal wybierz **testowania w czatów internetowych** do testowania robota. Spróbuj wiadomości typu like "Włączenie świateł" i "turn off Moje heater" do wywołania intencji, które dodano do niego.
   ![Testowanie HomeAutomation bot w czatów internetowych](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Jeśli okaże się, Twój bot zawsze nie rozpoznaje poprawne przeznaczenie lub jednostek, poprawić wydajność aplikacją usługi LUIS, dając więcej wypowiedzi przykład do nauczenia go. Mogą przechowywać aplikacją usługi LUIS bez żadnych modyfikacji kodu Twój bot. Zobacz [Dodawanie wypowiedzi przykład](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) i [nauczenia i przetestowania aplikacją usługi LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test).

## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej na temat platformy Bot Framework
Dowiedz się więcej o [platformy Bot Framework](https://dev.botframework.com/) i [3.x](https://github.com/Microsoft/BotBuilder) i [4.x](https://github.com/Microsoft/botbuilder-js) zestawów SDK.

## <a name="next-steps"></a>Kolejne kroki

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> Możesz dodać innej metody, takie jak uzyskać pomoc, Anuluj i powitanie, do aplikacji usługi LUIS. Następnie dodaj dialogów dla nowych intencje i Testuj je przy użyciu bota. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Dodawanie intencji](./luis-how-to-add-intents.md)
> [zalewanie mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

