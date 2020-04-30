---
title: 'Szybki Start: przesyłanie strumieniowe danych za pomocą usługi Azure Event Hubs przy użyciu protokołu Kafka'
description: 'Szybki Start: Ten artykuł zawiera informacje dotyczące przesyłania strumieniowego do usługi Azure Event Hubs przy użyciu protokołu i interfejsów API Kafka.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 67ee882acab22d977f08124591289e9cfc7cded1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81261826"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Szybki Start: przesyłanie strumieniowe danych z Event Hubs przy użyciu protokołu Kafka
Ten przewodnik Szybki Start przedstawia sposób przesyłania strumieniowego do Event Hubs bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Dowiesz się, jak używać swoich producentów i konsumentów, aby komunikować się z Event Hubs tylko zmianą konfiguracji w aplikacjach. Usługa Azure Event Hubs obsługuje [platformę Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs
Podczas tworzenia warstwy **standardowa** Event Hubs przestrzeń nazw, punkt końcowy Kafka dla przestrzeni nazw jest automatycznie włączany. Zdarzenia z aplikacji korzystających z protokołu Kafka można przesyłać strumieniowo do warstwy Standardowa Event Hubs. Postępuj zgodnie z instrukcjami krok po kroku w temacie [Tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md) , aby utworzyć Event Hubs przestrzeni nazw w warstwie **standardowa** . 

> [!NOTE]
> Event Hubs dla Kafka jest dostępny tylko w warstwach **standardowa** i **dedykowana** . Warstwa **podstawowa** nie obsługuje Kafka na Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Wysyłanie i odbieranie komunikatów przy użyciu platformy Kafka w usłudze Event Hubs

1. Sklonuj [repozytorium usługi Azure Event Hubs dla platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Przejdź do adresu `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aktualizuj szczegóły konfiguracji dla producenta w `src/main/resources/producer.config` w następujący sposób:

    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Kod źródłowy dla przykładowej klasy obsługi CustomAuthenticateCallbackHandler w witrynie GitHub można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Uruchom kod producenta i zdarzenia strumienia w Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Przejdź do adresu `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aktualizuj szczegóły konfiguracji dla konsumenta w `src/main/resources/consumer.config` w następujący sposób:
   
    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Kod źródłowy dla przykładowej klasy obsługi CustomAuthenticateCallbackHandler w witrynie GitHub można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Wszystkie przykłady OAuth dla Event Hubs Kafka można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Uruchamianie kodu klienta i przetwarzanie zdarzeń z centrum zdarzeń przy użyciu klientów Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Jeśli klaster platformy Kafka w usłudze Event Hub ma zdarzenia, teraz powinno się rozpocząć ich odbieranie od konsumenta.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób przesyłania strumieniowego do Event Hubs bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby dowiedzieć się więcej, zobacz [Apache Kafka Przewodnik dla deweloperów dla Event Hubs platformy Azure](apache-kafka-developer-guide.md). 
