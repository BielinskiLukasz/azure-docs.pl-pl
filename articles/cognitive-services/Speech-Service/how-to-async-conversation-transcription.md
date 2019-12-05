---
title: Transkrypcja konwersacji asynchronicznej (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać asynchronicznego transkrypcji konwersacji przy użyciu usługi mowy. Dostępne tylko dla języka Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: f34ce66d126a47e186cd1196fb5c92d670def445
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806171"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Transkrypcja konwersacji asynchronicznej (wersja zapoznawcza)

W tym artykule, transkrypcja konwersacji asynchronicznej jest wykazana przy użyciu interfejsu API **RemoteConversationTranscriptionClient** . Jeśli skonfigurowano transkrypcję konwersacji do wykonywania asynchronicznego transkrypcji i ma `conversationId`, można uzyskać transkrypcję skojarzoną z tym `conversationId` przy użyciu interfejsu API **RemoteConversationTranscriptionClient** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchroniczny a w czasie rzeczywistym + asynchronicznie

Dzięki operacji transkrypcji asynchronicznej można przesyłać strumieniowo konwersacje audio, ale nie musi istnieć transkrypcja zwrócona w czasie rzeczywistym. Zamiast tego, po wysłaniu dźwięku, użyj `conversationId` `Conversation`, aby wykonać zapytanie o stan asynchronicznego transkrypcji. Gdy asynchroniczne transkrypcje jest gotowe, uzyskasz `RemoteConversationTranscriptionResult`.

W czasie rzeczywistym i asynchronicznie można uzyskać transkrypcję w czasie rzeczywistym, ale również uzyskać transkrypcję, wykonując zapytania o `conversationId` (podobnie jak w scenariuszu asynchronicznym).

Do wykonania asynchronicznego transkrypcji wymagane są dwa kroki. Pierwszym krokiem jest przekazanie dźwięku, wybranie opcji tylko asynchroniczne lub w czasie rzeczywistym, a asynchronicznie. Drugim krokiem jest uzyskanie wyników transkrypcji.

## <a name="upload-the-audio"></a>Przekaż dźwięk

Aby można było wykonać asynchroniczne transkrypcje, musisz wysłać dźwięk do usługi transkrypcji do konwersacji przy użyciu zestawu SDK programu Microsoft poznawczego klienta (wersja 1.8.0 lub nowsza).

Ten przykładowy kod pokazuje, jak utworzyć konwersację Transcriber w trybie tylko asynchronicznie. Aby przesłać strumieniowo dźwięk do Transcriber, musisz dodać kod strumieniowego audio pochodzący z [konwersacji transkrypcja w czasie rzeczywistym za pomocą zestawu Speech SDK](how-to-use-conversation-transcription-service.md). Zapoznaj się z sekcją **ograniczenia** tego tematu, aby wyświetlić obsługiwane interfejsy API platform i języków.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Jeśli chcesz, aby w czasie rzeczywistym _Plus_ asynchroniczny, komentarz i Usuń komentarz z odpowiednich wierszy kodu w następujący sposób:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Pobierz wyniki transkrypcji

Ten krok umożliwia pobranie asynchronicznych wyników transkrypcji, ale założenie, że wymagane jest przetwarzanie w czasie rzeczywistym w innym miejscu. Aby uzyskać więcej informacji, zobacz [transkrypcja konwersacje w czasie rzeczywistym za pomocą zestawu Speech SDK](how-to-use-conversation-transcription-service.md).

W pokazanym tu kodzie potrzebna jest **wersja 1.8.0**, obsługiwana tylko w języku Java (1.8.0 lub nowszy) w systemach Windows, Linux i Android (tylko poziom interfejsu API 26 lub nowsza).

### <a name="obtaining-the-client-sdk"></a>Uzyskiwanie zestawu SDK klienta

Możesz uzyskać dostęp do **konwersacji zdalnej** , edytując plik pliku pom. XML w następujący sposób.

1. Na końcu pliku przed `</project>`tagu zamykającego Utwórz element `repositories` z odwołaniem do repozytorium Maven dla zestawu Speech SDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Dodaj również element `dependencies` z remoteconversation-Client-SDK 1.8.0 jako zależność:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Zapisz zmiany

### <a name="sample-transcription-code"></a>Przykładowy kod transkrypcji

Po uzyskaniu `conversationId`utworzyć zapytanie o transkrypcję konwersacji zdalnej **RemoteConversationTranscriptionClient** w aplikacji klienckiej w celu zbadania stanu asynchronicznego transkrypcji. Użyj metody **getTranscriptionOperation** w **RemoteConversationTranscriptionClient** , aby pobrać obiekt [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) . Obiekt PollerFlux będzie miał informacje o stanie operacji zdalnej **RemoteConversationTranscriptionOperation** i ostatnim wyniku **RemoteConversationTranscriptionResult**. Po zakończeniu operacji Pobierz **RemoteConversationTranscriptionResult** przez wywołanie **getFinalResult** na [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). W tym kodzie po prostu wydrukujesz zawartość wyniku do danych wyjściowych systemu.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
