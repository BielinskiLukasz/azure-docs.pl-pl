---
title: 'Samouczek: Przetwarzanie danych z usługi Azure Event Hubs przy użyciu platformy Apache Spark w usłudze Azure HDInsight '
description: Platforma Apache Spark w usłudze Azure HDInsight nawiązać połączenie z usługi Azure Event Hubs i przetwarzanie danych przesyłanych strumieniowo.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 12/28/2018
ms.openlocfilehash: 81104c7b206d4fe158df1ae9d329084ad88c3bdd
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976634"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Samouczek: Przetwarzaj tweety za pomocą usługi Azure Event Hubs i Apache Spark w HDInsight

W tym samouczku dowiesz się, jak utworzyć [platformy Apache Spark](https://spark.apache.org/) przesyłania strumieniowego aplikacji tweety są wysyłane do Centrum zdarzeń platformy Azure i utworzyć inną aplikację do odczytywania tweetów z Centrum zdarzeń. Aby uzyskać szczegółowy opis przesyłania strumieniowego platformy Spark, zobacz [platformy Apache Spark streaming Przegląd](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight udostępnia te same funkcje przesyłania strumieniowego klastra Spark na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wysyłanie komunikatów do usługi Azure Event Hub
> * Odczytywanie komunikatów z usługi Azure Event Hub

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* **Ukończenie tego artykułu [samouczka: Załaduj dane i uruchamiać zapytania dotyczące klastra Apache Spark w usłudze Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter

Aby otrzymywać strumień tweetów, musisz utworzyć aplikację w usłudze Twitter. Postępuj zgodnie z instrukcjami, tworzenie aplikacji usługi Twitter i zanotuj wartości potrzebnych do ukończenia tego samouczka.

1. Przejdź do [zarządzania aplikacjami w usłudze Twitter](https://apps.twitter.com/).

1. Wybierz **Utwórz nową aplikację**.

1. Podaj następujące wartości:

    - Nazwa: Podaj nazwę aplikacji. Wartość używana na potrzeby tego samouczka jest **HDISparkStreamApp0423**. Ta nazwa musi być unikatowa.
    - Opis: Podaj krótki opis aplikacji. Wartość używana na potrzeby tego samouczka jest **przesyłania strumieniowego aplikacji proste HDInsight Spark**.
    - Witryna sieci Web: Podaj aplikacji witryny sieci Web. Nie musi być prawidłową witryną sieci Web.  Wartość używana na potrzeby tego samouczka jest **http://www.contoso.com**.
    - Adres URL wywołania zwrotnego: Możesz pozostawić pusty.

1. Wybierz **tak, po ich przeczytaniu i zaakceptuj umowę dla deweloperów w usłudze Twitter**, a następnie wybierz pozycję **tworzenie aplikacji usługi Twitter**.

1. Wybierz **klucze i tokeny dostępu** kartę.

1. Wybierz **Utwórz Mój token dostępu** na końcu strony.

1. Zanotuj następujące wartości ze strony.  Te wartości będą potrzebne w dalszej części tego samouczka:

    - **Klucz klienta (klucz interfejsu API)**    
    - **Klucz tajny klienta (klucz tajny interfejsu API)**  
    - **Token dostępu**
    - **Klucz tajny tokenu dostępu**   

## <a name="create-an-azure-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Azure Event Hubs

To Centrum zdarzeń jest służy do przechowywania tweetów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Z menu po lewej stronie wybierz **wszystkich usług**.  

1. W obszarze **INTERNET OF THINGS**, wybierz opcję **usługi Event Hubs**. 

    ![Tworzenie Centrum zdarzeń dla aparatu Spark streaming przykład](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Tworzenie Centrum zdarzeń dla aparatu Spark streaming przykład")

4. Wybierz pozycję **+ Dodaj**.
5. Wprowadź następujące wartości dla nowej przestrzeni nazw usługi Event Hubs:

    - **Nazwa**: Wprowadź nazwę Centrum zdarzeń.  Wartość używana na potrzeby tego samouczka jest **myeventhubns20180403**.

    - **Warstwa cenowa**: Wybierz opcję **Standardowa**.

    - **Subskrypcja**: Wybierz odpowiednią subskrypcję.

    - **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej lub **Utwórz nową** do tworzenia nowej grupy zasobów.

    - **Lokalizacja**: Wybierz taki sam **lokalizacji** co klaster Apache Spark w HDInsight w celu zmniejszenia opóźnień i kosztów.

    - **Włącz automatyczne rozszerzanie**: (Opcjonalnie)  Automatyczne rozszerzanie automatycznie skaluje liczbę jednostek przepływności, przypisanych do Twojej Namespace centra zdarzeń, gdy ruchu przekracza pojemność jednostki przepływności do niej przypisany.  

    - **Automatyczne rozszerzanie maksymalna liczba jednostek przepływności**: (Opcjonalnie)  Suwak będą wyświetlane tylko wtedy, jeśli zaznaczysz **włączyć automatyczne rozszerzanie**.  

      ![Podaj nazwę Centrum zdarzeń dla aparatu Spark streaming przykład](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Podaj nazwę Centrum zdarzeń dla aparatu Spark streaming przykład")
6. Wybierz **Utwórz** można utworzyć przestrzeni nazw.  Wdrożenie potrwa kilka minut.

## <a name="create-an-azure-event-hub"></a>Tworzenie Centrum zdarzeń platformy Azure
Tworzenie Centrum zdarzeń, po wdrożeniu przestrzeni nazw usługi Event Hubs.  Z poziomu portalu:

1. Z menu po lewej stronie wybierz **wszystkich usług**.  

1. W obszarze **INTERNET OF THINGS**, wybierz opcję **usługi Event Hubs**.  

1. Wybierz przestrzeń nazw usługi Event Hubs z listy.  

1. Z **Event Hubs Namespace** wybierz opcję **+ Centrum zdarzeń**.  
1. Wprowadź następujące wartości w **Tworzenie Centrum zdarzeń** strony:

    - **Nazwa**: Nadaj nazwę Centrum zdarzeń. 
 
    - **Liczba partycji**: 10.  

    - **Przechowywania wiadomości**: 1.   
   
      ![Podaj szczegóły Centrum zdarzeń dla aparatu Spark streaming przykład](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Podaj szczegóły Centrum zdarzeń dla aparatu Spark streaming przykład")

1. Wybierz pozycję **Utwórz**.  Wdrożenie powinno zakończyć się w ciągu kilku sekund i nastąpi powrót do strony Event Hubs Namespace.

1. W obszarze **ustawienia**, wybierz opcję **zasady dostępu współdzielonego**.

1. Wybierz **RootManageSharedAccessKey**.
    
     ![Ustawianie zasad Centrum zdarzeń dla aparatu Spark streaming przykład](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "zasad Ustaw Centrum zdarzeń dla aparatu Spark streaming przykład")

1. Zapisz wartości **klucza podstawowego** i **parametry połączenia — klucz podstawowy** w dalszej części tego samouczka.

     ![Wyświetl klucze zasad Centrum zdarzeń dla aparatu Spark streaming przykład](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "zasad Centrum zdarzeń widoku kluczy w przypadku przykładu przesyłania strumieniowego platformy Spark")


## <a name="send-tweets-to-the-event-hub"></a>Tweety są wysyłane do Centrum zdarzeń

Tworzenie notesu Jupyter i nadaj mu nazwę **SendTweetsToEventHub**. 

1. Uruchom następujący kod, aby dodać zewnętrznych bibliotekach narzędzia Apache Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Uruchom następujący kod, aby wysyłać tweety do Centrum zdarzeń:

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
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
    ```

3. Otwórz Centrum zdarzeń w witrynie Azure portal.  Na **Przegląd**, zobaczysz Niektóre wykresy wyświetlane komunikaty wysyłane do Centrum zdarzeń.

## <a name="read-tweets-from-the-event-hub"></a>Odczytywanie tweetów z Centrum zdarzeń

Tworzenie innej notesu Jupyter i nadaj mu nazwę **ReadTweetsFromEventHub**. 

1. Uruchom następujący kod, aby dodać zewnętrznej biblioteki narzędzia Apache Maven:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Uruchom następujący kod do odczytywania tweetów z Centrum zdarzeń:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Z HDInsight Twoje dane są przechowywane w usłudze Azure Storage lub usługi Azure Data Lake Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Jeśli planujesz pracę w następnym samouczku natychmiast, możesz chcieć zachować klaster, w przeciwnym razie przejdź dalej i usunąć klaster.

Otwórz klaster w witrynie Azure Portal, a następnie wybierz pozycję **Usuń**.

![Usuwanie klastra usługi HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usunięcie grupy zasobów powoduje usunięcie zarówno klastra Spark w usłudze HDInsight, jak i domyślnego konta magazynu.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Przeczytaj komunikat z Centrum zdarzeń.
Przejdź do następnego artykułu, aby zobaczyć, że możesz tworzyć aplikacji uczenia maszynowego. 

> [!div class="nextstepaction"]
> [Tworzenie aplikacji uczenia maszynowego](./apache-spark-ipython-notebook-machine-learning.md)


