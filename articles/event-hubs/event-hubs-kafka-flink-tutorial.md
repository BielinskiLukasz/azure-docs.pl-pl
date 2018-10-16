---
title: Na użytek Apache Flink przy użyciu usługi Azure Event Hubs platformy Apache Kafka | Dokumentacja firmy Microsoft
description: Centrum zdarzeń z funkcją łączące Flink Apache do platformy Kafka
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 08/06/2018
ms.author: bahariri
ms.openlocfilehash: 3642c1b7d3de751ecb8a72edaecfe7a15c0acbdb
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320836"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Na użytek Apache Flink przy użyciu usługi Azure Event Hubs platformy Apache Kafka
W tym samouczku dowiesz się, jak Apache Flink łączyć się z centrów zdarzeń z obsługą platformy Kafka bez zmieniania klientów protokołu lub działające własne klastry. Usługa Azure Event Hubs obsługuje [platformy Apache Kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html).

Jednym z kluczowych korzyści z używania platformy Apache Kafka jest ekosystemu platform, którymi można nawiązać. Platforma Kafka łączy usługi Event Hubs swobodne korzystanie z platformy Kafka z włączoną skalowalności, spójności i obsługa ekosystem platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Sklonuj projekt przykładowy
> * Uruchom Flink producenta 
> * Uruchom Flink konsumenta

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka, upewnij się, że masz następujące wymagania wstępne:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](http://maven.apache.org/download.cgi) i [zainstalować](http://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Usługa Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania z dowolnej usługi Event Hubs. Zobacz [tworzenie platformy Kafka włączone usługi Event Hubs](event-hubs-create-kafka-enabled.md) informacji o pobieraniu punktu końcowego usługi Event Hubs Kafka. Upewnij się skopiować parametry połączenia usługi Event Hubs w celu późniejszego użycia.

## <a name="clone-the-example-project"></a>Sklonuj projekt przykładowy

Teraz, gdy masz parametry połączenia z obsługą platformy Kafka z usługi Event Hubs, Azure Event Hubs dla platformy Kafka repozytorium klonowanie i przejdź do `flink` podfolder:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Uruchom Flink producenta

Korzystając z podanego przykładu producenta Flink, wysyłają komunikaty do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy platformy Kafka z centrów zdarzeń

#### <a name="producerconfig"></a>Producer.config

Aktualizacja `bootstrap.servers` i `sasl.jaas.config` wartości w `producer/src/main/resources/producer.config` do kierowania producenta do punktu końcowego platformy Kafka z centrów zdarzeń przy użyciu poprawnego uwierzytelnienia.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Uruchom producentów z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, Generuj plik JAR, a następnie uruchamiać z poziomu narzędzia Maven (lub wygenerować plik JAR przy użyciu narzędzia Maven, następnie uruchomić w środowisku Java przez dodanie niezbędnych JAR(s) Kafka do ścieżki):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producent rozpocznie się teraz na temat wysyłania zdarzeń do Kafka włączone Centrum zdarzeń `test` i drukowanie zdarzenia do strumienia wyjściowego stdout.

## <a name="run-flink-consumer"></a>Uruchom Flink konsumenta

Korzystając z przykładu podana konsumenta odbierają wiadomości ze Kafka włączone usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy platformy Kafka z centrów zdarzeń

#### <a name="consumerconfig"></a>Consumer.config

Aktualizacja `bootstrap.servers` i `sasl.jaas.config` wartości w `consumer/src/main/resources/consumer.config` kierować klientów do punktu końcowego platformy Kafka z centrów zdarzeń przy użyciu poprawnego uwierzytelnienia.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Uruchom klienta z poziomu wiersza polecenia

Aby uruchomić konsumenta w wierszu polecenia, Generuj plik JAR, a następnie uruchamiać z poziomu narzędzia Maven (lub generowanie pliku JAR przy użyciu narzędzia Maven, następnie uruchomić w środowisku Java przez dodanie niezbędnych JAR(s) Kafka do ścieżki):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Jeśli Centrum zdarzeń z obsługą platformy Kafka zdarzeń (na przykład, jeśli również korzysta z Twojego producenta), następnie konsument zaczyna się od odbieranie zdarzeń z tematu `test`.

Zapoznaj się z [przewodnik dotyczący łącznika platformy Kafka w Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) więcej szczegółowych informacji na temat łączenia Flink do platformy Kafka.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku swoje nauczony jak Apache Flink łączyć się z centrów zdarzeń z obsługą platformy Kafka bez zmieniania klientów protokołu lub działające własne klastry. Poniższe kroki są wykonywane w ramach tego samouczka: 

> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Sklonuj projekt przykładowy
> * Uruchom Flink producenta 
> * Uruchom Flink konsumenta

Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

* [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Dowiedz się więcej o usłudze Event Hubs dla platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Poznaj więcej przykładów na usługi Event Hubs dla platformy Kafka w usłudze GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Użyj narzędzia [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330), aby [przesyłać strumieniowo zdarzenia z lokalnego środowiska platformy Kafka do usługi Event Hubs z obsługą platformy Kafka w chmurze.](event-hubs-kafka-mirror-maker-tutorial.md)
* Dowiedz się, jak przesyłać strumieniowo do platformy Kafka włączone za pomocą usługi Event Hubs [natywnych aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) lub [Akka strumieni](event-hubs-kafka-akka-streams-tutorial.md)
