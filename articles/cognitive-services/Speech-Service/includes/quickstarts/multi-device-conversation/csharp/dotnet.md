---
services: cognitive-services
author: ralphe
manager: cpoulain
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: ralphe
ms.openlocfilehash: 92fc7aeec87015a9ddbe4e52f870334582e28c14
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671501"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz **Program.cs**i zastąp cały w nim kod następującym kodem:

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Transcription;
    using System;
    using System.Threading.Tasks;
    
    namespace HelloWorld
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                await CreateConversationAsync();
            }
    
            static async Task CreateConversationAsync()
            {
                // Replace these values with the details of your Cognitive Speech subscription
                string subscriptionKey = "YourSubscriptionKey";

                // Replace below with your region identifier from here: https://aka.ms/speech/sdkregion
                string region = "YourServiceRegion";
    
                // Sets source and target languages.
                // Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
                string fromLanguage = "en-US";
                string toLanguage = "de";
    
                // Set this to the display name you want for the conversation host
                string displayName = "The host";
    
                // Create the task completion source that will be used to wait until the user presses Ctrl + C
                var completionSource = new TaskCompletionSource<bool>();
    
                // Register to listen for Ctrl + C
                Console.CancelKeyPress += (s, e) =>
                {
                    completionSource.TrySetResult(true);
                    e.Cancel = true; // don't terminate the current process
                };
    
                // Create an instance of the speech translation config
                var config = SpeechTranslationConfig.FromSubscription(subscriptionKey, region);
                config.SpeechRecognitionLanguage = fromLanguage;
                config.AddTargetLanguage(toLanguage);
    
                // Create the conversation
                using (var conversation = await Conversation.CreateConversationAsync(config).ConfigureAwait(false))
                {
                    // Start the conversation so the host user and others can join
                    await conversation.StartConversationAsync().ConfigureAwait(false);
    
                    // Get the conversation ID. It will be up to your scenario to determine how this is shared with other participants.
                    string conversationId = conversation.ConversationId;
                    Console.WriteLine($"Created '{conversationId}' conversation");
    
                    // At this point, you can use the conversation object to manage the conversation. 
                    // For example, to mute everyone else in the room you can call this method:
                    await conversation.MuteAllParticipantsAsync().ConfigureAwait(false);
    
                    // Configure which audio source you want to use. If you are using a text only language, you 
                    // can use the other overload of the constructor that takes no arguments
                    var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
                    using (var conversationTranslator = new ConversationTranslator(audioConfig))
                    {
                        // You should connect all the event handlers you need at this point
                        conversationTranslator.SessionStarted += (s, e) =>
                        {
                            Console.WriteLine($"Session started: {e.SessionId}");
                        };
                        conversationTranslator.SessionStopped += (s, e) =>
                        {
                            Console.WriteLine($"Session stopped: {e.SessionId}");
                        };
                        conversationTranslator.Canceled += (s, e) =>
                        {
                            switch (e.Reason)
                            {
                                case CancellationReason.EndOfStream:
                                    Console.WriteLine($"End of audio reached");
                                    break;
    
                                case CancellationReason.Error:
                                    Console.WriteLine($"Canceled due to error. {e.ErrorCode}: {e.ErrorDetails}");
                                    break;
                            }
                        };
                        conversationTranslator.ConversationExpiration += (s, e) =>
                        {
                            Console.WriteLine($"Conversation will expire in {e.ExpirationTime.TotalMinutes} minutes");
                        };
                        conversationTranslator.ParticipantsChanged += (s, e) =>
                        {
                            Console.Write("The following participant(s) have ");
                            switch (e.Reason)
                            {
                                case ParticipantChangedReason.JoinedConversation:
                                    Console.Write("joined");
                                    break;
    
                                case ParticipantChangedReason.LeftConversation:
                                    Console.Write("left");
                                    break;
    
                                case ParticipantChangedReason.Updated:
                                    Console.Write("been updated");
                                    break;
                            }
    
                            Console.WriteLine(":");
    
                            foreach (var participant in e.Participants)
                            {
                                Console.WriteLine($"\t{participant.DisplayName}");
                            }
                        };
                        conversationTranslator.TextMessageReceived += (s, e) =>
                        {
                            Console.WriteLine($"Received an instant message from '{e.Result.ParticipantId}': '{e.Result.Text}'");
                            foreach (var entry in e.Result.Translations)
                            {
                                Console.WriteLine($"\tTranslated into '{entry.Key}': '{entry.Value}'");
                            }
                        };
                        conversationTranslator.Transcribed += (s, e) =>
                        {
                            Console.WriteLine($"Received a transcription from '{e.Result.ParticipantId}': '{e.Result.Text}'");
                            foreach (var entry in e.Result.Translations)
                            {
                                Console.WriteLine($"\tTranslated into '{entry.Key}': '{entry.Value}'");
                            }
                        };
                        conversationTranslator.Transcribing += (s, e) =>
                        {
                            Console.WriteLine($"Received a partial transcription from '{e.Result.ParticipantId}': '{e.Result.Text}'");
                            foreach (var entry in e.Result.Translations)
                            {
                                Console.WriteLine($"\tTranslated into '{entry.Key}': '{entry.Value}'");
                            }
                        };
    
                        // Enter the conversation to start receiving events
                        await conversationTranslator.JoinConversationAsync(conversation, displayName).ConfigureAwait(false);
    
                        // You can now send an instant message to all other participants in the room
                        await conversationTranslator.SendTextMessageAsync("The instant message to send").ConfigureAwait(false);
    
                        // If specified a speech-to-text language, you can start capturing audio
                        await conversationTranslator.StartTranscribingAsync().ConfigureAwait(false);
                        Console.WriteLine("Started transcribing. Press Ctrl + c to stop");
    
                        // At this point, you should start receiving transcriptions for what you are saying using the default microphone. Press Ctrl+c to stop audio capture
                        await completionSource.Task.ConfigureAwait(false);
    
                        // Stop audio capture
                        await conversationTranslator.StopTranscribingAsync().ConfigureAwait(false);
    
                        // Leave the conversation. After this you will no longer receive events
                        await conversationTranslator.LeaveConversationAsync().ConfigureAwait(false);
                    }
    
                    // End the conversation
                    await conversation.EndConversationAsync().ConfigureAwait(false);
    
                    // Delete the conversation. Any other participants that are still in the conversation will be removed
                    await conversation.DeleteConversationAsync().ConfigureAwait(false);
                }
            }
        }
    }
    ```

1. W tym samym pliku `YourSubscriptionKey` zastąp ciąg kluczem subskrypcji mowy cognitive.

1. Zastąp ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz pozycję Zapisz**wszystkie** **pliki** > .

## <a name="build-and-run-the-application-to-create-a-new-conversation"></a>Tworzenie i uruchamianie aplikacji w celu utworzenia nowej konwersacji

1. Na pasku menu **wybierz** > build**build solution,** aby utworzyć aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub naciśnij klawisz **F5),** aby uruchomić aplikację **helloworld.**

1. Gdy pojawi `Started transcribing` się komunikat, możesz zacząć mówić. Zobaczysz transkrypcje pojawiają się podczas mówienia.
    - Jeśli udostępnisz kod konwersacji innym osobom i dołączą do konwersacji, zobaczysz również ich transkrypcje.

1. Po zakończeniu mówienia naciśnij <kbd>klawisze Ctrl+C,</kbd> aby zatrzymać przechwytywanie dźwięku, i zakończ konwersację.

## <a name="build-and-run-the-application-to-join-an-existing-conversation"></a>Tworzenie i uruchamianie aplikacji w celu dołączenia do istniejącej konwersacji

1. Skopiuj i wklej następującą funkcję do **Program.cs:**

    ```csharp
    static async Task JoinConversationAsync(string conversationId)
    {
        // Set this to the display name you want for the participant
        string displayName = "participant";

        // Set the speech to text, or text language you want to use
        string language = "en-US";

        // Create the task completion source that will be used to wait until the user presses Ctrl + c
        var completionSource = new TaskCompletionSource<bool>();

        // Register to listen for Ctrl+C
        Console.CancelKeyPress += (s, e) =>
        {
            completionSource.TrySetResult(true);
            e.Cancel = true; // don't terminate the current process
        };

        // As a participant, you don't need to specify any subscription key, or region. You can directly create
        // the conversation translator object
        var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using (var conversationTranslator = new ConversationTranslator(audioConfig))
        {
            // Register for any events you are interested here. For now let's just register for
            // transcription, and instant message events
            conversationTranslator.TextMessageReceived += (s, e) =>
            {
                Console.WriteLine($"Received an instant message from '{e.Result.ParticipantId}': '{e.Result.Text}'");
                foreach (var entry in e.Result.Translations)
                {
                    Console.WriteLine($"\tTranslated into '{entry.Key}': '{entry.Value}'");
                }
            };
            conversationTranslator.Transcribed += (s, e) =>
            {
                Console.WriteLine($"Received a transcription from '{e.Result.ParticipantId}': '{e.Result.Text}'");
                foreach (var entry in e.Result.Translations)
                {
                    Console.WriteLine($"\tTranslated into '{entry.Key}': '{entry.Value}'");
                }
            };
            conversationTranslator.Transcribing += (s, e) =>
            {
                Console.WriteLine($"Received a partial transcription from '{e.Result.ParticipantId}': '{e.Result.Text}'");
                foreach (var entry in e.Result.Translations)
                {
                    Console.WriteLine($"\tTranslated into '{entry.Key}': '{entry.Value}'");
                }
            };
    
            // To start receiving events, you will need to join the conversation
            await conversationTranslator.JoinConversationAsync(conversationId, displayName, language).ConfigureAwait(false);

            // You can now send an instant message
            await conversationTranslator.SendTextMessageAsync("Message from participant").ConfigureAwait(false);

            // Start capturing audio if you specified a speech-to-text language
            await conversationTranslator.StartTranscribingAsync().ConfigureAwait(false);
            Console.WriteLine("Started transcribing. Press Ctrl-C to stop");

            // At this point, you should start receiving transcriptions for what you are saying using
            // the default microphone. Press Ctrl+C to stop audio capture
            await completionSource.Task.ConfigureAwait(false);

            // Stop audio capture
            await conversationTranslator.StopTranscribingAsync().ConfigureAwait(false);

            // Leave the conversation. You will stop receiving events after this
            await conversationTranslator.LeaveConversationAsync().ConfigureAwait(false);
        }
    }
    ```

2. Zastąp `CreateConversationAsync();` w funkcji `public static async Task Main(string[] args)` na:

    ```csharp
    // Set this to the conversation you want to join
    JoinConversationAsync("YourConversationId");
    ```

[!INCLUDE [create-from-web](../create-from-web.md)]

4. Wróć do programu Visual Studio `YourConversationId` i zastąp ciąg identyfikatorem konwersacji utworzonym w poprzednim kroku.

5. Na pasku menu **wybierz** > build**build solution,** aby utworzyć aplikację. Kod powinien teraz zostać skompilowany bez błędów.

6. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub naciśnij klawisz **F5),** aby uruchomić aplikację **helloworld.**

7. Gdy pojawi `Started transcribing` się komunikat, możesz zacząć mówić. Zobaczysz transkrypcje pojawiają się podczas mówienia.
    - Jeśli wrócisz do przeglądarki, powinieneś zobaczyć, jak pojawiają się tam twoje transkrypcje, gdy mówisz.

8. Po zakończeniu mówienia naciśnij <kbd>klawisze Ctrl+C,</kbd> aby zatrzymać przechwytywanie dźwięku, i zakończ konwersację.

9. Wróć do przeglądarki i zakończ rozmowę za pomocą przycisku wyjścia w prawym górnym rogu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
