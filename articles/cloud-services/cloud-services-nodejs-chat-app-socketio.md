---
title: Node.js aplikacji przy użyciu programu Socket.io — Azure
description: Dowiedz się, jak używać socket.io w aplikacji node.js hostowanej na platformie Azure.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 5bea3bec68217881729f1daf8d75837e1d67e8ce
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919977"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Tworzenie aplikacji czatu Node.js przy użyciu usługi Socket.IO w usłudze w chmurze platformy Azure

Socket.IO zapewnia komunikację w czasie rzeczywistym między serwerem node.js i klientami. Ten samouczek przeprowadzi Cię przez hosting gniazda. Oparta na we/wy aplikacja czatu na platformie Azure. Aby uzyskać więcej informacji na temat Socket.IO, zobacz [Socket.IO](https://socket.io).

Zrzut ekranu ukończonej aplikacji jest poniżej:

![Okno przeglądarki zawierające usługę hostowaną na platformie Azure][completed-app]

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że następujące produkty i wersje zostały zainstalowane w celu pomyślnego przeprowadzenia przykładu w tym artykule:

* Zainstaluj [program Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Zainstaluj [Node.js](https://nodejs.org/download/)
* Zainstaluj [wersję 2.7.10 języka Python](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Tworzenie projektu usługi w chmurze
W poniższych krokach opisano tworzenie projektu usługi w chmurze, który będzie obsługiwał aplikację Socket.IO.

1. W **menu Start** lub **Start ekranu**Wyszukaj program **Windows PowerShell**. Na koniec kliknij prawym przyciskiem myszy program **Windows PowerShell** i wybierz polecenie **Uruchom jako administrator**.

    ![Ikona Azure PowerShell][powershell-menu]
2. Utwórz katalog o nazwie **c: \\ Node**.

    ```powershell
    PS C:\> md node
    ```

3. Zmień katalogi na katalog **c: \\ Node**

    ```powershell
    PS C:\> cd node
    ```

4. Wprowadź następujące polecenia, aby utworzyć nowe rozwiązanie o nazwie **ChatApp** i roli proces roboczy o nazwie **WorkerRole1**:

    ```powershell
    PS C:\node> New-AzureServiceProject chatapp
    PS C:\Node> Add-AzureNodeWorkerRole
    ```

    Zostanie wyświetlona następująca odpowiedź:

    ![Dane wyjściowe polecenia New-azureservice i Add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Pobierz przykład rozmowy
Dla tego projektu będziemy używać przykładu czatu z [repozytorium usługi Socket.IO]w serwisie GitHub. Wykonaj następujące kroki, aby pobrać przykład i dodać go do projektu, który został wcześniej utworzony.

1. Utwórz kopię lokalną repozytorium za pomocą przycisku **Klonuj** . Możesz również użyć przycisku **zip** , aby pobrać projekt.

   ![Wyświetlanie okna przeglądarki https://github.com/LearnBoost/socket.io/tree/master/examples/chat z wyróżnioną ikoną pobierania zip](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Przejdź do struktury katalogów lokalnego repozytorium do momentu, aż przytrzesz do katalogu ** \\ czatu przykładów** . Skopiuj zawartość tego katalogu do katalogu **C: \\ Node \\ ChatApp \\ WorkerRole1** utworzonego wcześniej.

   ![Eksplorator, wyświetlając zawartość przykładowego \\ katalogu rozmowy wyodrębnionego z archiwum][chat-contents]

   Wyróżnione elementy na poniższym zrzucie ekranu to pliki skopiowane z **przykładowego katalogu \\ czatu**
3. W katalogu **C: \\ Node \\ ChatApp \\ WorkerRole1** , usuń plik **server.js** , a następnie zmień nazwę pliku **app.js** na **server.js**. Spowoduje to usunięcie domyślnego pliku **server.js** utworzonego wcześniej przez polecenie cmdlet **Add-AzureNodeWorkerRole** i zastąpienie go plikiem aplikacji z przykładu rozmowy.

### <a name="modify-serverjs-and-install-modules"></a>Modyfikuj Server.js i zainstaluj moduły
Przed przetestowaniem aplikacji w emulatorze platformy Azure należy wprowadzić drobne modyfikacje. Wykonaj następujące kroki do pliku server.js:

1. Otwórz plik **server.js** w programie Visual Studio lub dowolnym edytorze tekstu.
2. Znajdź sekcję **zależności modułu** na początku server.js i Zmień wiersz zawierający **SIO = Wymagaj (".. //.. lib//Socket. io ')** do **SIO = Wymagaj ("Socket. IO")** , jak pokazano poniżej:

    ```js
    var express = require('express')
      , stylus = require('stylus')
      , nib = require('nib')
    //, sio = require('..//..//lib//socket.io'); //Original
      , sio = require('socket.io');                //Updated
      var port = process.env.PORT || 3000;         //Updated
    ```

3. Aby upewnić się, że aplikacja nasłuchuje na poprawnym porcie, Otwórz server.js w Notatniku lub w ulubionym edytorze, a następnie zmień następujący wiersz, zastępując **3000** with **Process. env. Port** , jak pokazano poniżej:

    ```js
    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });
    ```

Po zapisaniu zmian w **server.js**wykonaj następujące kroki, aby zainstalować wymagane moduły, a następnie przetestuj aplikację w emulatorze platformy Azure:

1. Przy użyciu **Azure PowerShell**zmień katalogi na katalog **C: \\ Node \\ ChatApp \\ WorkerRole1** i użyj następującego polecenia, aby zainstalować moduły wymagane przez tę aplikację:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> npm install
    ```

   Spowoduje to zainstalowanie modułów wymienionych w package.jspliku. Po wykonaniu tego polecenia dane wyjściowe powinny wyglądać mniej więcej tak:

   ![Dane wyjściowe polecenia instalacji npm][The-output-of-the-npm-install-command]
2. Ponieważ ten przykład był pierwotnie częścią repozytorium Socket.IO GitHub i bezpośrednio odwołuje się do biblioteki Socket.IO w ramach ścieżki względnej, Socket.IO nie została przywoływana w package.jsw pliku, więc należy ją zainstalować, wydając następujące polecenie:

   ```powershell
   PS C:\node\chatapp\WorkerRole1> npm install socket.io --save
    ```

### <a name="test-and-deploy"></a>Testowanie i wdrażanie
1. Uruchom emulator, wydając następujące polecenie:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
    ```

   > [!NOTE]
   > W przypadku wystąpienia problemów z uruchamianiem emulatora, np.: Start-AzureEmulator: Wystąpił nieoczekiwany błąd.  Szczegóły: napotkano nieoczekiwany błąd obiektu komunikacji, system. ServiceModel. Channels. ServiceChannel, nie można użyć do komunikacji, ponieważ jest w stanie awarii.
   >
   > Zainstaluj ponownie AzureAuthoringTools v 2.7.1 i AzureComputeEmulator v 2,7 — upewnij się, że wersja jest zgodna.

2. Otwórz przeglądarkę i przejdź do **http://127.0.0.1** .
3. Po otwarciu okna przeglądarki wprowadź pseudonim, a następnie naciśnij klawisz ENTER.
   Pozwoli to na publikowanie wiadomości jako określonego pseudonimu. Aby przetestować funkcje dla wielu użytkowników, Otwórz dodatkowe okna przeglądarki przy użyciu tego samego adresu URL i wprowadź różne pseudonimy.

   ![Dwa okna przeglądarki wyświetlają komunikaty czatu od Użytkownik1 do 2.](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Po przetestowaniu aplikacji Zatrzymaj emulator, wydając następujące polecenie:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
    ```

5. Aby wdrożyć aplikację na platformie Azure, użyj polecenia cmdlet **Publish-AzureServiceProject** . Przykład:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
    ```

   > [!IMPORTANT]
   > Upewnij się, że używasz unikatowej nazwy, w przeciwnym razie proces publikowania zakończy się niepowodzeniem. Po zakończeniu wdrażania zostanie otwarta przeglądarka i przejdziesz do wdrożonej usługi.
   >
   > Jeśli zostanie wyświetlony komunikat o błędzie informujący o tym, że podana nazwa subskrypcji nie istnieje w zaimportowanym profilu publikowania, należy pobrać i zaimportować profil publikacji subskrypcji przed wdrożeniem na platformie Azure. Zobacz sekcję **wdrażanie aplikacji do platformy Azure** , [Aby skompilować i wdrożyć aplikację Node.js w usłudze w chmurze platformy Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

   ![Okno przeglądarki zawierające usługę hostowaną na platformie Azure][completed-app]

   > [!NOTE]
   > Jeśli zostanie wyświetlony komunikat o błędzie informujący o tym, że podana nazwa subskrypcji nie istnieje w zaimportowanym profilu publikowania, należy pobrać i zaimportować profil publikacji subskrypcji przed wdrożeniem na platformie Azure. Zobacz sekcję **wdrażanie aplikacji do platformy Azure** , [Aby skompilować i wdrożyć aplikację Node.js w usłudze w chmurze platformy Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

Aplikacja działa teraz na platformie Azure i może przekazywać wiadomości czatu między różnymi klientami przy użyciu Socket.IO.

> [!NOTE]
> Dla uproszczenia ten przykład jest ograniczony do komunikacji między użytkownikami połączonymi z tym samym wystąpieniem. Oznacza to, że jeśli usługa w chmurze tworzy dwa wystąpienia roli procesu roboczego, użytkownicy będą mogli rozmawiać z innymi osobami podłączonymi do tego samego wystąpienia roli procesu roboczego. Aby skalować aplikację do pracy z wieloma wystąpieniami roli, można użyć technologii, takiej jak Service Bus, aby udostępnić stan magazynu Socket.IO w różnych wystąpieniach. Przykłady można znaleźć w sekcji Przykłady użycia Service Bus Queues i tematów w [zestawie Azure SDK dla Node.js repozytorium GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
>
>

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób tworzenia podstawowej aplikacji czatu hostowanej w usłudze w chmurze platformy Azure. Aby dowiedzieć się, jak hostować tę aplikację w witrynie sieci Web platformy Azure, zobacz [Node.js Kompilowanie aplikacji Socket.IO Chat przy użyciu usługi Website w witrynie internetowej platformy Azure][chatwebsite].

Aby uzyskać więcej informacji, zobacz również [Centrum deweloperówNode.js](https://docs.microsoft.com/azure/developer/javascript/).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Repozytorium GitHub Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png





