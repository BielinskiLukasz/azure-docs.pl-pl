---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Sposób przekazywania plików z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla języka Java. Przekazane pliki są przechowywane w kontenerze obiektów blob usługi Azure storage.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 09580f3bb5d6f6f5ccb15adddf0cf1f9e19c2210
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38619407"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ten samouczek opiera się na kodzie w [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-java-java-c2d.md) samouczka, aby dowiesz się, jak używać [pliku przekazywania możliwościami usługi IoT Hub](iot-hub-devguide-file-upload.md) można przekazać pliku do [obiektów blob platformy Azure Magazyn](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

- Bezpiecznie przekazać urządzenia z systemem Azure identyfikator URI obiektu blob przekazywania pliku.
- Powiadomienia przekazywania pliku usługi IoT Hub umożliwia wyzwalanie przetwarzania pliku w aplikacji zaplecza.

[Rozpoczynanie pracy z usługą IoT Hub](iot-hub-java-java-getstarted.md) i [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-java-java-c2d.md) samouczkach przedstawiono podstawowe funkcje obsługi komunikatów usługi IoT Hub urządzenia do chmury i z chmury do urządzeń. [Komunikatów z urządzenia chmury procesu](tutorial-routing.md) samouczku opisano sposób niezawodnego przechowywania komunikatów z urządzenia do chmury w usłudze Azure blob storage. Jednak w niektórych scenariuszach nie pozwala na łatwe mapowanie danych wysyłanych przez urządzenia do stosunkowo mały wiadomości urządzenia do chmury, które akceptuje usługi IoT Hub. Na przykład:

* Duże pliki, które zawierają obrazy
* Filmy wideo
* Wibracje danych próbkowania o wysokiej częstotliwości
* Pewnego rodzaju wstępnie przetworzonych danych.

Te pliki to typowo wsadowego przetwarzania w chmurze przy użyciu narzędzi takich jak [usługi Azure Data Factory](../data-factory/introduction.md) lub [Hadoop](../hdinsight/index.yml) stosu. Gdy zachodzi potrzeba wyżynne plików z urządzenia, można nadal używać zabezpieczeń i niezawodności usługi IoT Hub.

Na końcu tego samouczka, możesz uruchomić dwie aplikacje konsolowe Java:

* **Symulowane urządzenie**, zmodyfikowanej wersji aplikacji utworzonych w samouczku [komunikatów wysyłania chmury do urządzeń z usługą IoT Hub]. Ta aplikacja przekazuje plik do magazynu przy użyciu identyfikatora URI sygnatury dostępu Współdzielonego, dostarczone przez Centrum IoT hub.
* **Odczyt pliku-— powiadomienie o przekazywaniu**, która odbiera powiadomienia o przekazywania plików z usługi IoT hub.

> [!NOTE]
> Centrum IoT Hub obsługuje wiele platform urządzeń i językach (w tym C, .NET i języka Javascript) za pomocą zestawów SDK urządzeń Azure IoT. Zapoznaj się [Centrum deweloperów Azure IoT] instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do usługi Azure IoT Hub.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Najnowszy zestaw [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](http://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekaż plik z aplikacji urządzenia

W tej sekcji zmodyfikujesz aplikację urządzenia utworzone w [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-java-java-c2d.md) do przekazania pliku do usługi IoT hub.

1. Skopiuj plik obrazu do `simulated-device` folder i zmień jego nazwę `myimage.png`.

1. Za pomocą edytora tekstów otwórz `simulated-device\src\main\java\com\mycompany\app\App.java` pliku.

1. Dodaj deklarację zmiennej do **aplikacji** klasy:

    ```java
    private static String fileName = "myimage.png";
    ```

1. Do przetwarzania komunikatów wywołania zwrotnego stan przekazywania pliku, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Aby przekazywać obrazy do usługi IoT Hub, dodaj następującą metodę do **aplikacji** klasy, aby przekazywać obrazy do usługi IoT Hub:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

1. Modyfikowanie **głównego** metodę do wywołania **uploadFile** metody, jak pokazano w poniższym fragmencie kodu:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

1. Użyj następującego polecenia, aby zbudować **symulowane urządzenie** aplikację i sprawdzić błędy:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Otrzymywać powiadomienie o przekazywaniu pliku

W tej sekcji utworzysz aplikację konsolową Java, która odbiera komunikaty powiadomień przekazywania pliku z usługi IoT Hub.

Potrzebujesz **iothubowner** parametry połączenia dla Centrum IoT Hub do ukończenia tej sekcji. Można znaleźć w ciągu połączenia [witryny Azure portal](https://portal.azure.com/) na **zasady dostępu współdzielonego** bloku.

1. Utwórz projekt narzędzia Maven o nazwie **odczytu pliku-— powiadomienie o przekazywaniu** przy użyciu następującego polecenia w wierszu polecenia. Należy pamiętać, że to polecenie jest jedno długie polecenie:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. W wierszu polecenia przejdź do nowego `read-file-upload-notification` folderu.

1. Za pomocą edytora tekstów otwórz `pom.xml` w pliku `read-file-upload-notification` folderze i dodaj następującą zależność do **zależności** węzła. Dodawanie zależności pozwala na użycie **iothub-java-service-client** pakietu w aplikacji do komunikowania się z usługą IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Możesz sprawdzić dostępność najnowszej wersji **iot-service-client** przy użyciu [funkcji wyszukiwania narzędzia Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Zapisz i Zamknij `pom.xml` pliku.

1. Za pomocą edytora tekstów otwórz `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` pliku.

1. Dodaj do pliku następujące instrukcje **importowania**:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. Dodaj następujące zmienne poziomu klasa **aplikacji** klasy:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. Aby wydrukować informacji na temat przekazywania plików do konsoli, należy dodać następujące klasy zagnieżdżonej, aby **aplikacji** klasy:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Recieve file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Aby rozpocząć wątku, który nasłuchuje powiadomień przekazywania pliku, Dodaj następujący kod do **głównego** metody:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

1. Zapisz i Zamknij `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` pliku.

1. Użyj następującego polecenia, aby zbudować **odczytu pliku-— powiadomienie o przekazywaniu** aplikację i sprawdzić błędy:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

W wierszu polecenia w `read-file-upload-notification` folder, uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

W wierszu polecenia w `simulated-device` folder, uruchom następujące polecenie:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z **symulowane urządzenie** aplikacji:

![Dane wyjściowe z aplikacji symulowane urządzenia](media/iot-hub-java-java-upload/simulated-device.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z **odczytu pliku-— powiadomienie o przekazywaniu** aplikacji:

![Dane wyjściowe z aplikacji odczytu-— — powiadomienie o przekazywaniu pliku](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Aby wyświetlić przekazany plik w kontenerze magazynu, które zostały skonfigurowane, można użyć portalu:

![Przekazany plik](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików usługi IoT Hub można uproszczenie przekazywania plików z urządzeń. Możesz kontynuować poznawanie funkcji Centrum IoT i scenariusze z następujących artykułów:

* [Programistyczne tworzenie Centrum IoT hub][lnk-create-hub]
* [Wprowadzenie do zestawu SDK języka C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Symulowanie urządzenia za pomocą usługi IoT Edge][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Centrum deweloperów Azure IoT]: http://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md


