---
title: 'Samouczek: Analiza tonacji na strumieniu danych wykonywana przy użyciu usługi Azure Databricks | Microsoft Docs'
description: Dowiedz się, jak przeprowadzać analizę tonacji w czasie rzeczywistym na strumieniu danych za pomocą usług Azure Databricks i Event Hubs oraz interfejsu API usług Cognitive Services.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-databricks
ms.custom: mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/15/2018
ms.author: nitinme
ms.openlocfilehash: 00456bdc4dc0e8562af9be6c827e8ab32bf03a31
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-sentiment-analysis-on-streaming-data-using-azure-databricks"></a>Samouczek: Analiza tonacji na strumieniu danych wykonywana przy użyciu usługi Azure Databricks

W tym samouczku dowiesz się, jak przy użyciu usługi Azure Databricks przeprowadzać analizę tonacji na strumieniu danych przesyłanych w czasie rzeczywistym. Konfiguracja systemu pozyskiwania danych w czasie rzeczywistym jest oparta na usłudze Azure Event Hubs. Do przesyłania komunikatów z usługi Event Hubs do usługi Azure Databricks służy łącznik Spark Event Hubs. Ostatnim etapem jest przeprowadzanie analizy tonacji na strumieniu danych za pomocą interfejsów API usługi Microsoft Cognitive Service. 

Wykonanie kroków tego samouczka pozwoli Ci przesłać strumieniowo tweety, które zawierają termin „Azure”, i przeprowadzić na nich analizę tonacji.

Poniższa ilustracja przedstawia przepływ aplikacji:

![Połączenie usługi Azure Databricks z usługami Event Hubs i Cognitive Services](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-tutorial.png "Połączenie usługi Azure Databricks z usługami Event Hubs i Cognitive Services")

Ten samouczek obejmuje następujące zadania: 

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie aplikacji usługi Twitter umożliwiającej dostęp do danych w czasie rzeczywistym
> * Tworzenie notesów w usłudze Azure Databricks
> * Dołączanie bibliotek usługi Event Hubs oraz interfejsu API usługi Twitter
> * Tworzenie konta usług Microsoft Cognitive Services i pobieranie klucza dostępu
> * Wysyłanie tweetów do usługi Event Hubs
> * Odczytywanie tweetów z usługi Event Hubs
> * Przeprowadzanie analizy tonacji na tweetach

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem upewnij się, że zostały spełnione następujące wymagania:
- Przestrzeń nazw usługi Azure Event Hubs.
- Centrum zdarzeń w przestrzeni nazw.
- Parametry połączenia umożliwiające dostęp do przestrzeni nazw usługi Event Hubs. Parametry połączenia powinny mieć mniej więcej taki format: `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>”`.
- Nazwa zasad dostępu współdzielonego i klucz zasad dla usługi Event Hubs.

Aby spełnić te wymagania, wystarczy wykonać kroki opisane w artykule [Create an Azure Event Hubs namespace and event hub (Tworzenie przestrzeni nazw i centrum zdarzeń usługi Azure Event Hubs)](../event-hubs/event-hubs-create.md).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu witryny Azure Portal. 

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Dane i analiza** > **Azure Databricks (wersja zapoznawcza)**.

    ![Usługa Databricks w witrynie Azure Portal](./media/databricks-sentiment-analysis-cognitive-services/azure-databricks-on-portal.png "Usługa Databricks w witrynie Azure Portal")

2. W obszarze **Azure Databricks (wersja zapoznawcza)** wybierz pozycję **Utwórz**.

3. W obszarze **Usługa Azure Databricks** podaj wartości umożliwiające utworzenie obszaru roboczego usługi Databricks.

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-workspace.png "Tworzenie obszaru roboczego usługi Azure Databricks")

    Podaj następujące wartości: 
     
    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **East US 2** (Wschodnie stany USA 2). Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).        |
    |**Warstwa cenowa**     |  Wybierz warstwę **Standardowa** lub **Premium**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

4. Tworzenie konta potrwa kilka minut. Podczas tworzenia konta po prawej stronie portalu jest wyświetlany kafelek **Przesyłanie wdrożenia dla usługi Azure Databricks**. Aby go zobaczyć, być może trzeba będzie przesunąć pulpit nawigacyjny w prawo. W górnej części ekranu jest również wyświetlany pasek postępu. Postęp można obserwować w dowolnym z tych obszarów.

    ![Kafelek wdrażania usługi Databricks](./media/databricks-sentiment-analysis-cognitive-services/databricks-deployment-tile.png "Kafelek wdrażania usługi Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Usługa Databricks na platformie Azure](./media/databricks-sentiment-analysis-cognitive-services/databricks-on-azure.png "Usługa Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Spark usługi Databricks na platformie Azure](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-spark-cluster.png "Tworzenie klastra Spark usługi Databricks na platformie Azure")

    Zaakceptuj pozostałe wartości domyślne poza następującymi:

    * Wprowadź nazwę klastra.
    * W tym artykule należy utworzyć klaster ze środowiskiem uruchomieniowym **4.0 (beta)**. 
    * Upewnij się, że jest zaznaczone pole wyboru **Zakończ po ___ min aktywności**. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.

    Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby otrzymywać strumień tweetów w czasie rzeczywistym, musisz utworzyć aplikację w usłudze Twitter. Wykonaj poniższe kroki, aby utworzyć aplikację usługi Twitter i zarejestrować wartości potrzebne do ukończenia tego samouczka.

1. W przeglądarce internetowej przejdź do strony [Twitter Application Management (Zarządzanie aplikacjami usługi Twitter)](http://twitter.com/app) i wybierz pozycję **Create New App (Utwórz nową aplikację)**.

    ![Tworzenie aplikacji usługi Twitter](./media/databricks-sentiment-analysis-cognitive-services/databricks-create-twitter-app.png "Tworzenie aplikacji usługi Twitter")

2. Na stronie **Create an application (Tworzenie aplikacji)** podaj szczegóły nowej aplikacji, a następnie wybierz pozycję **Create your Twitter application (Utwórz aplikację usługi Twitter)**.

    ![Szczegóły aplikacji usługi Twitter](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details.png "Szczegóły aplikacji usługi Twitter")

3. Na stronie aplikacji wybierz kartę **Keys and Access Tokens (Klucze i tokeny dostępu)** i skopiuj wartości pól **Consume Key (Klucz klienta)** i **Consumer Secret (Klucz tajny klienta)**. Zaznacz również pole **Create my access token (Utwórz mój token dostępu)**, aby wygenerować tokeny dostępu. Skopiuj wartości pól **Access Token (Token dostępu)** i **Access Token Secret (Klucz tajny tokenu dostępu)**.

    ![Szczegóły aplikacji usługi Twitter](./media/databricks-sentiment-analysis-cognitive-services/twitter-app-key-secret.png "Szczegóły aplikacji usługi Twitter")

Zapisz wartości dotyczące aplikacji usługi Twitter. Będą one potrzebne w dalszej części tego samouczka.

## <a name="attach-libraries-to-spark-cluster"></a>Dołączanie bibliotek do klastra Spark

W tym samouczku tweety są wysyłane do usługi Event Hubs za pomocą interfejsów API usługi Twitter. Ponadto dane są odczytywane i zapisywane w usłudze Azure Event Hubs za pomocą [łącznika Event Hubs platformy Apache Spark](https://github.com/Azure/azure-event-hubs-spark). Aby korzystać z tych interfejsów API w ramach klastra, dodaj je jako biblioteki do usługi Azure Databricks, a następnie je skojarz z klastrem Spark. Poniżej przedstawiono, jak dodać bibliotekę do folderu **Udostępnione** w obszarze roboczym.

1.  W obszarze roboczym usługi Azure Databricks wybierz pozycję **Obszar roboczy**, a następnie kliknij prawym przyciskiem myszy pozycję **Udostępnione**. Z menu kontekstowego wybierz polecenie **Utwórz** > **Biblioteka**.

    ![Okno dialogowe Dodawanie biblioteki](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-option.png "Okno dialogowe Dodawanie biblioteki")

2. Na stronie Nowa biblioteka w polu **Źródło** wybierz pozycję **Współrzędna Maven**. W polu **Współrzędna** wprowadź współrzędną pakietu, który chcesz dodać. Oto współrzędne Maven bibliotek używanych w tym samouczku:

    * Łącznik Event Hubs platformy Spark — `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.0`
    * Interfejs API usługi Twitter — `org.twitter4j:twitter4j-core:4.0.6`

    ![Podawanie współrzędnych Maven](./media/databricks-sentiment-analysis-cognitive-services/databricks-eventhub-specify-maven-coordinate.png "Podawanie współrzędnych Maven")

3. Wybierz pozycję **Utwórz bibliotekę**.

4. Wybierz folder, do którego dodano bibliotekę, a następnie wybierz nazwę biblioteki.

    ![Wybieranie biblioteki, która ma zostać dodana](./media/databricks-sentiment-analysis-cognitive-services/select-library.png "Wybieranie biblioteki, która ma zostać dodana")

5. Na stronie biblioteki wybierz klaster, w którym chcesz używać biblioteki. Po pomyślnym skojarzeniu biblioteki z klastrem stan natychmiast zmienia się na wartość **Dołączone**.

    ![Dołączanie biblioteki do klastra](./media/databricks-sentiment-analysis-cognitive-services/databricks-library-attached.png "Dołączanie biblioteki do klastra")

6. Powtórz te kroki dla pakietu Twitter: `twitter4j-core:4.0.6`.

## <a name="get-a-cognitive-services-access-key"></a>Pobieranie klucza dostępu usług Cognitive Services

W tym samouczku do przeprowadzania analizy tonacji na strumieniu tweetów w czasie rzeczywistym są używane [interfejsy API analizy tekstu usług Microsoft Cognitive Services](../cognitive-services/text-analytics/overview.md). Przed użyciem interfejsów API analizy tekstu musisz utworzyć konto usług Microsoft Cognitive Services na platformie Azure i pobrać klucz dostępu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **+ Utwórz zasób**.
 
3. W obszarze Azure Marketplace wybierz pozycję **SI i usługi Cognitive Services** > **Interfejs API analizy tekstu**.

    ![Tworzenie konta usług Cognitive Services](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-text-api.png "Tworzenie konta usług Cognitive Services")

4. W oknie dialogowym **Tworzenie** podaj następujące wartości:

    ![Tworzenie konta usług Cognitive Services](./media/databricks-sentiment-analysis-cognitive-services/create-cognitive-services-account.png "Tworzenie konta usług Cognitive Services")

    - Wprowadź nazwę konta usług Cognitive Services.
    - Wybierz subskrypcję platformy Azure, w której utworzono konto.
    - Wybierz lokalizację platformy Azure.
    - Wybierz warstwę cenową usługi. Więcej informacji na temat cen usług Cognitive Services zawiera [strona z cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/).
    - Określ, czy chcesz utworzyć nową grupę zasobów, czy wybrać istniejącą grupę.

    Wybierz pozycję **Utwórz**.

5. Po utworzeniu konta na karcie **Przegląd** wybierz pozycję **Pokaż klucze dostępu**.

    ![Wyświetlanie kluczy dostępu](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-get-access-keys.png "Wyświetlanie kluczy dostępu")

    Ponadto skopiuj część adresu URL punktu końcowego, jak pokazano na zrzucie ekranu. Ten adres URL będzie potrzebny w dalszej części samouczka.

6. W obszarze **Zarządzanie kluczami** wybierz ikonę kopiowania obok klucza, którego chcesz użyć.

    ![Kopiowanie kluczy dostępu](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-copy-access-keys.png "Kopiowanie kluczy dostępu")

7. Zapisz wartości adresu URL punktu końcowego i klucza dostępu, które zostały pobrane w tym kroku. Będą one potrzebne w dalszej części tego samouczka.

## <a name="create-notebooks-in-databricks"></a>Tworzenie notesów w usłudze Databricks

W tej sekcji w obszarze roboczym usługi Databricks zostaną utworzone dwa notesy o następujących nazwach:

- **SendTweetsToEventHub** — notes producenta służący do pobierania tweetów z usługi Twitter i przesyłania ich w strumieniu do usługi Event Hubs.
- **AnalyzeTweetsFromEventHub** — notes użytkownika służący do odczytywania tweetów z usługi Event Hubs i przeprowadzania analizy tonacji.

1. W lewym okienku wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycje **Utwórz** i **Notes**.

    ![Tworzenie notesu w usłudze Databricks](./media/databricks-sentiment-analysis-cognitive-services/databricks-create-notebook.png "Tworzenie notesu w usłudze Databricks")

2. W oknie dialogowym **Tworzenie notesu** wpisz **SendTweetsToEventHub**, jako język wybierz pozycję **Scala** i wybierz utworzony wcześniej klaster Spark.

    ![Tworzenie notesu w usłudze Databricks](./media/databricks-sentiment-analysis-cognitive-services/databricks-notebook-details.png "Tworzenie notesu w usłudze Databricks")

    Wybierz pozycję **Utwórz**.

3. Powtórz te kroki, aby utworzyć notes **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Wysyłanie tweetów do usługi Event Hubs

W notesie **SendTweetsToEventHub** wklej następujący kod i zastąp symbol zastępczy utworzonymi wcześniej wartościami przestrzeni nazw usługi Event Hubs i aplikacji usługi Twitter. Ten notes przesyła w czasie rzeczywistym strumień tweetów ze słowem kluczowym „Azure” do usługi Event Hubs.

    import java.util._
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)
    
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData) 
      System.out.println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder
    
    // Twitter configuration!
    // Replace values below with yours
    
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
      cb.setDebugEnabled(true)
      .setOAuthConsumerKey(twitterConsumerKey)
      .setOAuthConsumerSecret(twitterConsumerSecret)
      .setOAuthAccessToken(twitterOauthAccessToken)
      .setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()
    
    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query) 
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){ 
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()

Aby uruchomić notes, naciśnij klawisze **SHIFT + ENTER**. Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu. Każde zdarzenie w danych wyjściowych jest tweetem pobieranym w czasie rzeczywistym do usługi Event Hubs. 

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah 
    #cloudcomputing #Azure
    
    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie
    
    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk
    
    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud
    
    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Odczytywanie tweetów z usługi Event Hubs

W notesie **AnalyzeTweetsFromEventHub** wklej następujący kod i zastąp symbol zastępczy utworzonymi wcześniej wartościami dotyczącymi usługi Azure Event Hubs. Ten notes umożliwia odczyt tweetów przesłanych strumieniowo do usługi Event Hubs przy użyciu notesu **SendTweetsToEventHub**.

    import org.apache.spark.eventhubs._

    // Build connection string with the above information 
    val connectionString = ConnectionStringBuilder("<EVENT HUBS CONNECTION STRING>")
      .setEventHubName("<EVENT HUB NAME>")
      .build
    
    val customEventhubParameters = 
      EventHubsConf(connectionString)
      .setMaxEventsPerTrigger(5)
    
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    
    incomingStream.printSchema
    
    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()

Dane wyjściowe są następujące:

  
    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)
   
    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        | 
    +------+------+--------------+---------------+---------+------------+
    
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Ponieważ dane wyjściowe są przedstawione w trybie binarnym, przy użyciu następującego fragmentu kodu można je przekształcić w ciąg.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages = 
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()

Dane wyjściowe są teraz podobne do następującego fragmentu kodu:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)
    
    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah 
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

## <a name="run-sentiment-analysis-on-tweets"></a>Przeprowadzanie analizy tonacji na tweetach

W tej sekcji jest przeprowadzana analiza tonacji na tweetach odebranych przy użyciu interfejsu API usługi Twitter. Fragmenty kodu należy dodać do tego samego notesu **AnalyzeTweetsFromEventHub**.

Najpierw dodaj nową komórkę kodu w notesie i wklej poniższy fragment kodu. Ten fragment kodu definiuje typy danych umożliwiające korzystanie z interfejsu API języka i tonacji.

    import java.io._
    import java.net._
    import java.util._    

    class Document(var id: String, var text: String, var language: String = "", var sentiment: Double = 0.0) extends Serializable 

    class Documents(var documents: List[Document] = new ArrayList[Document]()) extends Serializable {
    
        def add(id: String, text: String, language: String = "") {
            documents.add (new Document(id, text, language))
        }
        def add(doc: Document) {
            documents.add (doc)
        }
    }

Dodaj nową komórkę kodu i wklej poniższy fragment kodu. Jest on wymagany do analizowania ciągów JSON.

    class CC[T] extends Serializable { def unapply(a:Any):Option[T] = Some(a.asInstanceOf[T]) }
    object M extends CC[scala.collection.immutable.Map[String, Any]]
    object L extends CC[scala.collection.immutable.List[Any]]
    object S extends CC[String]
    object D extends CC[Double]

Dodaj nową komórkę kodu i wklej poniższy fragment. Definiuje on obiekt zawierający funkcje umożliwiające wywołanie interfejsu API analizy tekstu w celu wykrycia języka i przeprowadzenia analizy tonacji. Zastąp symbole zastępcze `<PROVIDE ACCESS KEY HERE>` i `<PROVIDE HOST HERE>` wartościami pobranymi dla konta usług Cognitive Services.

    import javax.net.ssl.HttpsURLConnection
    import com.google.gson.Gson
    import com.google.gson.GsonBuilder
    import com.google.gson.JsonObject
    import com.google.gson.JsonParser
    import scala.util.parsing.json._
    
    object SentimentDetector extends Serializable {
      
      // Cognitive Services API connection settings
      val accessKey = "<PROVIDE ACCESS KEY HERE>"
      val host = "<PROVIDE HOST HERE>"
      val languagesPath = "/text/analytics/v2.0/languages"
      val sentimentPath = "/text/analytics/v2.0/sentiment"
      val languagesUrl = new URL(host+languagesPath)
      val sentimenUrl = new URL(host+sentimentPath)
      
      def getConnection(path: URL): HttpsURLConnection = {
        val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
        connection.setRequestMethod("POST")
        connection.setRequestProperty("Content-Type", "text/json")
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey)
        connection.setDoOutput(true)
        return connection
      }
      
      def prettify (json_text: String): String = {
        val parser = new JsonParser()
        val json = parser.parse(json_text).getAsJsonObject()
        val gson = new GsonBuilder().setPrettyPrinting().create()
        return gson.toJson(json)
      }
      
      // Handles the call to Cognitive Services API.
      // Expects Documents as parameters and the address of the API to call.
      // Returns an instance of Documents in response.
      def processUsingApi(inputDocs: Documents, path: URL): String = {
        val docText = new Gson().toJson(inputDocs)
        val encoded_text = docText.getBytes("UTF-8")
        val connection = getConnection(path)
        val wr = new DataOutputStream(connection.getOutputStream())
        wr.write(encoded_text, 0, encoded_text.length)
        wr.flush()
        wr.close()
    
        val response = new StringBuilder()
        val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
        var line = in.readLine()
        while (line != null) {
            response.append(line)
            line = in.readLine()
        }
        in.close()
        return response.toString()
      }
      
      // Calls the language API for specified documents.
      // Returns a documents with language field set.
      def getLanguage (inputDocs: Documents): Documents = { 
        try {
          val response = processUsingApi(inputDocs, languagesUrl)
          // In case we need to log the json response somewhere
          val niceResponse = prettify(response)
          val docs = new Documents()
          val result = for {
                // Deserializing the JSON response from the API into Scala types
                Some(M(map)) <- scala.collection.immutable.List(JSON.parseFull(niceResponse))
                L(documents) = map("documents")
                M(document) <- documents
                S(id) = document("id")
                L(detectedLanguages) = document("detectedLanguages")
                M(detectedLanguage) <- detectedLanguages
                S(language) = detectedLanguage("iso6391Name")
          } yield {
                docs.add(new Document(id = id, text = id, language = language))
          }
          return docs
        } catch {
              case e: Exception => return new Documents()
        }
      }
      
      // Calls the sentiment API for specified documents. Needs a language field to be set for each of them.
      // Returns documents with sentiment field set, taking a value in the range from 0 to 1.
      def getSentiment (inputDocs: Documents): Documents = {
        try {
          val response = processUsingApi(inputDocs, sentimenUrl)
          val niceResponse = prettify(response)
          val docs = new Documents()
          val result = for {
                // Deserializing the JSON response from the API into Scala types
                Some(M(map)) <- scala.collection.immutable.List(JSON.parseFull(niceResponse))
                L(documents) = map("documents")
                M(document) <- documents
                S(id) = document("id")
                D(sentiment) = document("score")
          } yield {
                docs.add(new Document(id = id, text = id, sentiment = sentiment))
          }
          return docs
        } catch {
            case e: Exception => return new Documents()
        }
      }
    }
    
    // User Defined Function for processing content of messages to return their sentiment.
    val toSentiment = udf((textContent: String) => {
      val inputDocs = new Documents()
      inputDocs.add (textContent, textContent)
      val docsWithLanguage = SentimentDetector.getLanguage(inputDocs)
      val docsWithSentiment = SentimentDetector.getSentiment(docsWithLanguage)
      if (docsWithLanguage.documents.isEmpty) {
        // Placeholder value to display for no score returned by the sentiment API
        (-1).toDouble
      } else {
        docsWithSentiment.documents.get(0).sentiment.toDouble
      }
    })

Dodaj ostatnią komórkę kodu w celu przygotowania ramki danych z zawartością tweetu i tonacją skojarzoną z tym tweetem.

    // Prepare a dataframe with Content and Sentiment columns
    val streamingDataFrame = incomingStream.selectExpr("cast (body as string) AS Content").withColumn("Sentiment", toSentiment($"Content"))
    
    // Display the streaming data with the sentiment
    streamingDataFrame.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()

Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +--------------------------------+------------------+
    |Content                         |Sentiment         |
    +--------------------------------+------------------+
    |Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah   #cloudcomputing #Azure          |0.7761918306350708|
    |Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |0.8558163642883301|
    |@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|0.5               |
    |4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |0.5               |
    +--------------------------------+------------------+ 

Wartość bliższa **1** w kolumnie **Opinia** sugeruje zadowolenie z korzystania z platformy Azure. Wartość bliższa **0** sugeruje, że użytkownicy napotkali problemy podczas pracy z platformą Microsoft Azure. 

Gotowe. Za pomocą usługi Azure Databricks udało się przesłać w czasie rzeczywistym strumień danych do usługi Azure Event Hubs, pobrać go przy użyciu łącznika usługi Event Hubs, a następnie przeprowadzić analizę tonacji na tym strumieniu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** w lewym okienku. W obszarze klastra, którego działanie chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje**, a następnie wybierz ikonę **Zakończ**.

![Zatrzymywanie klastra usługi Databricks](./media/databricks-sentiment-analysis-cognitive-services/terminate-databricks-cluster.png "Zatrzymywanie klastra usługi Databricks")

Jeśli nie zakończysz działania klastra ręcznie, zostanie on automatycznie zatrzymany, o ile podczas tworzenia klastra zaznaczono pole wyboru **Zakończ po __ min aktywności**. W takim przypadku nieaktywny klaster zostanie automatycznie zatrzymany po określonym czasie.

## <a name="next-steps"></a>Następne kroki 
W tym samouczku przedstawiono użycie usługi Azure Databricks w celu przesłania strumienia danych do usługi Azure Event Hubs oraz odczytania tego strumienia z usługi Event Hubs w czasie rzeczywistym. W tym samouczku omówiono:
> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie aplikacji usługi Twitter umożliwiającej dostęp do danych w czasie rzeczywistym
> * Tworzenie notesów w usłudze Azure Databricks
> * Dodawanie i dołączanie bibliotek usługi Event Hubs oraz interfejsu API usługi Twitter
> * Tworzenie konta usług Microsoft Cognitive Services i pobieranie klucza dostępu
> * Wysyłanie tweetów do usługi Event Hubs
> * Odczytywanie tweetów z usługi Event Hubs
> * Przeprowadzanie analizy tonacji na tweetach

W następnym samouczku dowiesz się, jak wykonywać zadania uczenia maszynowego przy użyciu usługi Azure Databricks.

> [!div class="nextstepaction"]
>[Uczenie maszynowe za pomocą usługi Azure Databricks](https://docs.azuredatabricks.net/spark/latest/mllib/decision-trees.html)
