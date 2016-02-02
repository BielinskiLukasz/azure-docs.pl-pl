<properties 
    pageTitle="Java Service Bus API で AMQP 1.0 を使用する方法 | Microsoft Azure" 
    description="Java Message Service (JMS) API を Azure Service Bus と Advanced Message Queuing Protodol (AMQP) 1.0 と共に使用する方法。" 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    writer="sethm" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/06/2015" 
    ms.author="sethm"/>


# Service Bus と AMQP 1.0 で Java Message Service (JMS) API を使用する方法に関するページ

Advanced Message Queuing Protocol (AMQP) 1.0 は、堅牢なクロスプラットフォーム メッセージング アプリケーションを作成するために使用できる、効率的で信頼性の高い回線レベルのメッセージング プロトコルです。

Service Bus での AMQP 1.0 のサポートにより、仲介型メッセージング機能 (キューおよびトピック発行/サブスクライブ) をさまざまなプラットフォームから効率的なバイナリ プロトコルを使って利用できます。 さらに、さまざまな言語、フレームワーク、およびオペレーティング システムを使って作成されたコンポーネントで構成されたアプリケーションを作成できます。

この記事では、一般的な Java Message Service (JMS) API 規格を使用して Java アプリケーションから Service Bus のブローカー メッセージング機能 (キューおよびトピック発行/サブスクライブ) を使用する方法について説明します。 Service Bus .NET API を使用して同じ作業を実行する方法が説明されているハウツー ガイドがあります。 これら 2 種類のガイドを使用して、AMQP 1.0 を使用したクロスプラットフォームのメッセージングについて学習できます。

## Service Bus の概要

このガイドは、"queue1" という名前のキューが含まれている Service Bus 名前空間が既にあることを前提としています。 そうしないかどうか、名前空間を作成してキューを使用して、 [Azure クラシック ポータル](http://manage.windowsazure.com)します。 Service Bus 名前空間とキューを作成する方法の詳細については、次を参照してください。 [Service Bus キューの使用方法](service-bus-dotnet-how-to-use-queues.md)します。
> [AZURE.NOTE] パーティション分割されたキューおよびトピックも AMQP をサポートします。 詳細については、次を参照してください。 [パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md) と [Service Bus の AMQP 1.0 のサポートには、キューおよびトピックがパーティション分割されている](service-bus-partitioned-queues-and-topics-amqp-overview.md)します。

## AMQP 1.0 JMS クライアント ライブラリのダウンロード

Apache Qpid JMS AMQP 1.0 クライアント ライブラリの最新バージョンをダウンロードする場所については、次を参照してください。 [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html)します。

Service Bus を使用する JMS アプリケーションをビルドおよび実行するときは、次の 4 つの JAR ファイルを Apache Qpid JMS AMQP 1.0 ディストリビューション アーカイブから Java CLASSPATH に追加する必要があります。

*    geronimo-jms\_1.1\_spec-1.0.jar

*    qpid-amqp-1-0-client-[version].jar

*    qpid-amqp-1-0-client-jms-[version].jar

*    qpid-amqp-1-0-common-[version].jar


## Java アプリケーションのコーディング

### Java Naming and Directory Interface (JNDI)

JMS では、Java Naming and Directory Interface (JNDI) を使用して論理名と物理名が関連付けられます。 2 種類の JMS オブジェクト、ConnectionFactory および Destination が JNDI を使用して解決されます。 JNDI で使用されるプロバイダー モデルでは、さまざまなディレクトリ サービスに接続して、名前解決のタスクを処理できます。 Apache Qpid JMS AMQP 1.0 ライブラリには、次の形式のプロパティ ファイルを使用して構成されるシンプルなプロパティ ファイル ベースの JNDI Provider が付属しています。

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### ConnectionFactory の構成

Qpid Properties File JNDI Provider で **ConnectionFactory** の定義に使用するエントリは、次のような形式になります。

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

ここで、**[jndi_name]** と **[ConnectionURL]** には次の意味があります。

- **[jndi_name]**: ConnectionFactory の論理名。 Java アプリケーションで JNDI IntialContext.lookup() メソッドを使用して解決される名前です。
- **[ConnectionURL]**: AMQP ブローカーに必要な情報を JMS ライブラリに渡すための URL。

**ConnectionURL** の形式は次のようになります。

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```
Where **[namespace]**, **[username]** and **[password]** have the following meanings:

- **[namespace]**: The Service Bus namespace.
- **[username]**: The Service Bus issuer name.
- **[password]**: URL-encoded form of the Service Bus issuer key.

> [AZURE.NOTE] You must URL-encode the password manually. A useful URL-encoding utility is available at [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### Configure destinations

The entry used to define a destination in the Qpid Properties File JNDI Provider is of the following format:
```
キューです。[jndi_name] = [physical_name]
```

or

```
topic.[jndi_name] = [physical_name]
```

ここで **[jndi \_name]** と **[physical \_name]** は次の意味があります。

- **[jndi_name]**: 送信先の論理名。 Java アプリケーションで JNDI IntialContext.lookup() メソッドを使用して解決される名前です。
- **[physical_name]**: アプリケーションでのメッセージの送受信に使用する Service Bus エンティティの名前。

> [AZURE.NOTE] Service Bus トピック サブスクリプションから受信した場合は、JNDI で指定された物理名がトピックの名前になります。 サブスクリプション名は、持続性の高いサブスクリプションが JMS アプリケーション コードで作成されるときに指定されます。  [Service Bus AMQP 1.0 開発者ガイド](service-bus-amqp-dotnet.md) JMS から Service Bus トピック サブスクリプションの使用方法に関する詳細を示します。

### JMS アプリケーションの記述

JMS と Service Bus の使用時に必要になる特殊な API やオプションはありません。 ただし、この後で取り上げているようないくつかの制限があります。 いずれの JMS アプリケーションでも、まず、**ConnectionFactory** と送信先の名前解決が可能になるように JNDI 環境を構成する必要があります。

#### JNDI InitialContext の構成

JNDI 環境を構成するには、構成情報のハッシュ テーブルを javax.naming.InitialContext クラスのコンストラクターに渡します。 ハッシュ テーブル内の 2 つの必須の要素は Initial Context Factory と Provider URL です。 次のコードでは、Qpid Properties File JNDI Provider と、**servicebus.properties** という名前のプロパティ ファイルを使用して、JNDI 環境を構成する方法を示しています。

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
```

### Service Bus キューを使用するシンプルな JMS アプリケーション

次のサンプル プログラムでは、JNDI 論理名が QUEUE の Service Bus キューに JMS TextMessages を送信し、折り返しそれらのメッセージを受信します。

    // SimpleSenderReceiver.java
    
    import javax.jms.*;
    import javax.naming.Context;
    import javax.naming.InitialContext;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.util.Hashtable;
    import java.util.Random;
    
    public class SimpleSenderReceiver implements MessageListener {
        private static boolean runReceiver = true;
        private Connection connection;
        private Session sendSession;
        private Session receiveSession;
        private MessageProducer sender;
        private MessageConsumer receiver;
        private static Random randomGenerator = new Random();
    
        public SimpleSenderReceiver() throws Exception {
            // Configure JNDI environment
            Hashtable<String, String> env = new Hashtable<String, String>();
            env.put(Context.INITIAL_CONTEXT_FACTORY, 
                    "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
            env.put(Context.PROVIDER_URL, "servicebus.properties");
            Context context = new InitialContext(env);
    
            // Lookup ConnectionFactory and Queue
            ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
            Destination queue = (Destination) context.lookup("QUEUE");
    
            // Create Connection
            connection = cf.createConnection();
    
            // Create sender-side Session and MessageProducer
            sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
            sender = sendSession.createProducer(queue);
    
            if (runReceiver) {
                // Create receiver-side Session, MessageConsumer,and MessageListener
                receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
                receiver = receiveSession.createConsumer(queue);
                receiver.setMessageListener(this);
                connection.start();
            }
        }
    
        public static void main(String[] args) {
            try {
    
                if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                    runReceiver = false;
                }
    
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
                System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
                BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
                while (true) {
                    String s = commandLine.readLine();
                    if (s.equalsIgnoreCase("exit")) {
                        simpleSenderReceiver.close();
                        System.exit(0);
                    } else {
                        simpleSenderReceiver.sendMessage();
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    
        private void sendMessage() throws JMSException {
            TextMessage message = sendSession.createTextMessage();
            message.setText("Test AMQP message from JMS");
            long randomMessageID = randomGenerator.nextLong() >>>1;
            message.setJMSMessageID("ID:" + randomMessageID);
            sender.send(message);
            System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
        }
    
        public void close() throws JMSException {
            connection.close();
        }
    
        public void onMessage(Message message) {
            try {
                System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
                message.acknowledge();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }   

### アプリケーションの実行

このアプリケーションを実行すると、次の形式の出力が生成されます。

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## JMS と .NET の間のクロスプラットフォーム メッセージング

このガイドでは、JMS を使用して Service Bus との間でメッセージを送信および受信する方法について説明しました。 しかし、AMQP 1.0 の主な利点の 1 つは、さまざまな言語で書かれたコンポーネントからアプリケーションを作成して、高い信頼性と完全な忠実度でメッセージ交換を行えることにあります。

上記で説明したサンプル JMS アプリケーションと必携ガイドから取得した類似の .NET アプリケーションを使用して [.NET Service Bus .NET API で AMQP 1.0 を使用する方法](service-bus-dotnet-advanced-message-queuing.md), 、.NET と Java の間でメッセージを交換することができます。

クロスプラット フォーム メッセージングの詳細についてはサービス バスと AMQP 1.0 を使用して参照してください、 [Service Bus AMQP 1.0 開発者ガイド](service-bus-amqp-dotnet.md)します。

### JMS から .NET

JMS から .NET のメッセージングを試してみるには、次の手順を実行します。

* .NET サンプル アプリケーションを、コマンド ライン引数を使わずに起動します。
* Java サンプル アプリケーションを、"sendonly" コマンド ライン引数を使って起動します。 このモードでは、アプリケーションはキューからメッセージを受信せずに送信のみを行います。
* Java アプリケーションのコンソールで **Enter** キーを何度か押します。メッセージが送信されます。
* それらのメッセージが .NET アプリケーションによって受信されます。

#### JMS アプリケーションの出力

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### .NET アプリケーションの出力

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### .NET から JMS

.NET から JMS のメッセージングを試してみるには、次の手順を実行します。

* .NET サンプル アプリケーションを、"sendonly" コマンド ライン引数を使って起動します。 このモードでは、アプリケーションはキューからメッセージを受信せずに送信のみを行います。
* Java サンプル アプリケーションを、コマンド ライン引数を使わずに起動します。
* .NET アプリケーションのコンソールで **Enter** キーを何度か押します。メッセージが送信されます。
* それらのメッセージが Java アプリケーションによって受信されます。

#### .NET アプリケーションの出力

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### JMS アプリケーションの出力

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## サポートされていない機能および制限

JMS を AMQP 1.0 と Service Bus で使用する場合は、次の制限があります。

* **Session** ごとに作成できる **MessageProducer** または **MessageConsumer** は 1 つのみです。 アプリケーションで複数の **MessageProducers** または **MessageConsumers** を作成する必要がある場合は、それぞれに専用の**セッション**を作成してください。
* 揮発性トピック サブスクリプションは現在サポートされていません。
* **MessageSelectors** は現在サポートされていません。
* 一時的な送信先である **TemporaryQueue** と **TemporaryTopic**、およびそれらを使用する **QueueRequestor** API と **TopicRequestor** API は現在サポートされていません。
* トランザクション セッションと分散トランザクションはサポートされません。

## まとめ

このガイドでは、一般的な JMS API と AMQP 1.0 を使って Java から Service Bus の仲介型メッセージング機能 (キューおよびトピック発行/サブスクライブ) を使用する方法について説明しました。

Service Bus AMQP 1.0 のサポートは、.NET、C、Python、PHP など、その他の言語からも使用できます。 Service Bus で AMQP 1.0 のサポートを使用すると、これらのさまざまな言語を使って作成されたコンポーネントで高い信頼性と十分な忠実度のメッセージ交換が実現されます。 詳細については、次を参照してください。、 [Service Bus AMQP 1.0 開発者ガイド](service-bus-amqp-dotnet.md)します。

## 次のステップ

* [Azure Service Bus で AMQP 1.0 サポート](service-bus-amqp-overview.md)
* [Service Bus .NET API で AMQP 1.0 を使用する方法](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 開発者ガイド](service-bus-amqp-dotnet.md)
* [Service Bus キューの使用方法](service-bus-dotnet-how-to-use-queues.md)
* [Java デベロッパー センター](/develop/java/)します。









