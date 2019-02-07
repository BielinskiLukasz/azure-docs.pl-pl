---
title: Konfigurowanie zasad platformy Apache Kafka w usłudze HDInsight przy użyciu pakietu Enterprise Security — Azure
description: Dowiedz się, jak skonfigurować zasady platformy Apache Ranger dla platformy Kafka w usłudze Azure HDInsight przy użyciu pakietu Enterprise Security.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 381958b462b668447a6548029a1be616df7a3a4b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750226"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Samouczek: Konfigurowanie zasad platformy Apache Kafka w usłudze HDInsight przy użyciu pakietu Enterprise Security (wersja zapoznawcza)

Dowiedz się, jak skonfigurować zasady platformy Apache Ranger na potrzeby klastrów Apache Kafka pakietów Enterprise Security Package (ESP). Klastry ESP są łączone z domeną, co pozwala użytkownikom na uwierzytelnianie przy użyciu poświadczeń domeny. Korzystając z tego artykułu, utworzysz dwie zasady platformy Ranger, aby ograniczyć dostęp do tematów `sales*` i `marketingspend`.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie użytkowników domeny
> * Tworzenie zasad platformy Ranger
> * Tworzenie tematów w klastrze platformy Kafka
> * Testowanie zasad platformy Ranger

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

* Utwórz [klaster platformy Kafka usługi HDInsight, używając pakietu Enterprise Security](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Łączenie z interfejsem użytkownika administratora platformy Apache Ranger

1. W przeglądarce połącz się z interfejsem użytkownika administratora platformy Ranger przy użyciu adresu URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Pamiętaj, aby zmienić wartość `<ClusterName>` na nazwę klastra platformy Kafka.

    > [!NOTE]  
    > Poświadczenia platformy Ranger są inne niż poświadczenia klastra Hadoop. Aby zapobiec używaniu w przeglądarce buforowanych poświadczeń usługi Hadoop, należy połączyć się z interfejsem użytkownika administratora platformy Ranger w nowym oknie przeglądarki InPrivate.

2. Zaloguj się przy użyciu poświadczeń administratora usługi Azure Active Directory (AD). Poświadczenia administratora usługi Azure AD są inne niż poświadczenia klastra HDInsight i poświadczenia protokołu SSH węzła usługi HDInsight w systemie Linux.

   ![Interfejs użytkownika administratora platformy Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Tworzenie użytkowników domeny

Zapoznaj się z sekcją dotyczącą [tworzenia klastra usługi HDInsight przy użyciu pakietu Enterprise Security](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds), aby dowiedzieć się, jak utworzyć użytkowników domeny **sales_user** i **marketing_user**. W scenariuszu produkcyjnym użytkownicy domeny pochodzą z dzierżawy usługi Active Directory.

## <a name="create-ranger-policy"></a>Tworzenie zasad platformy Ranger

Utwórz zasady platformy Ranger dla użytkowników **sales_user** i **marketing_user**.

1. Otwórz **interfejs użytkownika administratora platformy Ranger**.

2. Kliknij pozycję **\<Nazwa_klastra>_kafka** w obszarze **Kafka**. Lista może zawierać tylko jedne wstępnie skonfigurowane zasady.

3. Kliknij pozycję **Add New Policy** (Dodaj nowe zasady) i wprowadź następujące wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Policy Name (Nazwa zasad)  |  hdi sales* policy   |
   |Temat   |  sales* |
   |Select User (Wybierz użytkownika)  |  sales_user1 |
   |Uprawnienia  | publish, consume, create |

   W nazwie tematu można uwzględnić następujące symbole wieloznaczne:

   * „*” oznacza zero lub więcej wystąpień znaków.
   * „?” oznacza pojedynczy znak.

   ![Interfejs użytkownika administratora platformy Apache Ranger — tworzenie zasad](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE]
   >Zaczekaj kilka minut na zsynchronizowanie platformy Ranger z usługą Azure AD, jeśli użytkownik domeny nie zostanie automatycznie wypełniony dla ustawienia **Select User** (Wybierz użytkownika).

4. Kliknij pozycję **Add** (Dodaj), aby zapisać zasady.

5. Kliknij pozycję **Add New Policy** (Dodaj nowe zasady), a następnie wprowadź następujące wartości:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Policy Name (Nazwa zasad)  |  hdi marketing policy   |
   |Temat   |  marketingspend |
   |Select User (Wybierz użytkownika)  |  marketing_user1 |
   |Uprawnienia  | publish, consume, create |

   ![Interfejs użytkownika administratora platformy Apache Ranger — tworzenie zasad](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Kliknij pozycję **Add** (Dodaj), aby zapisać zasady.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Tworzenie tematów w klastrze platformy Kafka przy użyciu pakietu ESP

Aby utworzyć dwa tematy — `salesevents` i `marketingspend`:

1. Użyj następującego polecenia w celu otwarcia połączenia protokołu SSH z klastrem:

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Zastąp ciąg `DOMAINADMIN` za pomocą użytkownika będącego administratorem dla klastra skonfigurowanego podczas [tworzenia klastra](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp), a ciąg `CLUSTERNAME` zastąp nazwą klastra. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika będącego administratorem. Aby uzyskać więcej informacji na temat używania polecenia `SSH` w usłudze HDInsight, zobacz [Korzystanie z protokołu SSH w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

2. Użyj następujących poleceń, aby zapisać nazwę klastra w zmiennej i zainstalować narzędzie do analizy JSON, `jq`. Po wyświetleniu monitu wprowadź nazwę klastra platformy Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Użyj następujących poleceń, aby uzyskać hosty brokera platformy Kafka. Po wyświetleniu monitu wprowadź hasło konta administratora klastra.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > Zanim przejdziesz dalej, może być konieczne skonfigurowanie środowiska projektowego, jeśli nie wykonano tego wcześniej. Potrzebne będą Ci następujące składniki: zestaw Java JDK, narzędzie Apache Maven i klient SSH z punktem połączenia z usługą. Aby uzyskać więcej informacji, zobacz [instrukcje instalacji](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
   
1. Pobierz [przykłady odbiorców i producentów przyłączonych do domeny na platformie Apache Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Wykonaj kroki 2 i 3 w sekcji **Kompilowanie i wdrażanie przykładu** w artykule [Samouczek: Korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Uruchom następujące polecenia:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testowanie zasad platformy Ranger

W oparciu o skonfigurowane zasady platformy Ranger użytkownik **sales_user** może tworzyć/wykorzystywać temat `salesevents`, ale nie temat `marketingspend`. Z drugiej strony użytkownik **marketing_user** może tworzyć/wykorzystywać temat `marketingspend`, ale nie temat `salesevents`.

1. Otwórz nowe połączenie SSH z klastrem. Użyj następującego polecenia, aby zalogować się jako użytkownik **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Wykonaj następujące polecenie:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Użyj nazw brokera z poprzedniej sekcji, aby ustawić następujące zmienne środowiskowe:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Przykład: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Wykonaj krok 3 w sekcji **Kompilowanie i wdrażanie przykładu** w artykule [Samouczek: korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example), aby zapewnić dostępność pliku `kafka-producer-consumer.jar` także dla użytkownika **sales_user**.

5. Upewnij się, że użytkownik **sales_user1** może tworzyć do tematu `salesevents`, wykonując następujące polecenie:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Wykonaj następujące polecenie, aby korzystać z elementów tematu `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Sprawdź, czy możesz odczytywać komunikaty.

7. Upewnij się, że użytkownik **sales_user1** nie może tworzyć do tematu `marketingspend`, wykonując następujące polecenie w tym samym oknie protokołu SSH:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Wystąpi błąd autoryzacji, który można zignorować.

8. Zauważ, że użytkownik **marketing_user1** nie może korzystać z elementów tematu `salesevents`.

   Powtórz kroki 1–4 powyżej, ale tym razem jako użytkownik **marketing_user1**.

   Wykonaj następujące polecenie, aby korzystać z elementów tematu `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Poprzednie komunikaty nie będą widoczne.

9. Wyświetl zdarzenia dostępu inspekcji z poziomu interfejsu użytkownika platformy Ranger.

   ![Inspekcja zasad interfejsu użytkownika platformy Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Następne kroki

* [Bring your own key to Apache Kafka (Używanie własnego klucza na platformie Apache Kafka)](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [An introduction to Apache Hadoop security with Enterprise Security Package (Wprowadzenie do zabezpieczeń usługi Apache Hadoop z pakietem Enterprise Security)](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
