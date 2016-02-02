<properties
    pageTitle="PHP から Queue ストレージを使用する方法 | Microsoft Azure"
    description="Azure Queue ストレージを使用して、キューの作成と削除のほか、メッセージの挿入、取得、削除を行う方法を説明します。サンプルは PHP で記述されています。"
    documentationCenter="php"
    services="storage"
    authors="tfitzmac"
    manager="adinah"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="09/23/2015"
    ms.author="tomfitz"/>


# PHP から Queue ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概要

このガイドでは、Azure キュー ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Windows SDK for PHP のクラスを経由して記述されています。 キュー メッセージの挿入、ピーク、取得、削除のシナリオ、キューの作成と削除のシナリオについて説明します。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## PHP アプリケーションの作成

Azure キュー ストレージにアクセスする PHP アプリケーションを作成するための要件は、コード内から Azure SDK for PHP のクラスを参照することのみです。 アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

このガイドで使用するキュー ストレージ機能は、PHP アプリケーション内でローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

## Azure クライアント ライブラリの入手

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## キュー ストレージにアクセスするようにアプリケーションを構成する

Azure キュー ストレージで API を使用するには次が必要になります。

1. 使用してオートローダー ファイルを参照、 [require_once ][require_once] ステートメントです。
2. 使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、**ServicesBuilder** クラスを参照する方法を示しています。
> [AZURE.NOTE]
> この例 (およびこの記事のその他の例) では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。 ライブラリを手動でまたは PEAR パッケージとしてインストールした場合は、`WindowsAzure.php` オートローダー ファイルを参照する必要があります。

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

以下の例で、 `require_once` ステートメントは、常に表示されますが、使用例を実行するために必要なクラスのみ参照されます。

## Azure のストレージ接続文字列の設定

Azure キュー ストレージ クライアントをインスタンス化するには、まず有効な接続文字列が必要です。 キュー サービスの接続文字列の形式は次のとおりです。

ライブ サービスにアクセスする場合:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

エミュレーター ストレージにアクセスする場合:

    UseDevelopmentStorage=true

いずれの Azure サービス クライアントを作成するにも、**ServicesBuilder** クラスを使用する必要があります。 次の手法のうちどちらかを使用できます。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する
    * 既定では 1 つの外部ソース (環境変数) のみサポートされています。
    * **ConnectionStringSource** クラスを継承して新しいソースを追加できます。

ここで概説している例では、接続文字列を直接渡します。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

## キューを作成する

**QueueRestProxy** オブジェクトの **createQueue** メソッドを使用してキューを作成できます。 キューの作成時にキューのオプションを設定できますが、この設定は必須ではありません (次の例では、キューのメタデータを設定する方法を示しています)。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\CreateQueueOptions;
    
    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
    
    // OPTIONAL: Set queue metadata.
    $createQueueOptions = new CreateQueueOptions();
    $createQueueOptions->addMetaData("key1", "value1");
    $createQueueOptions->addMetaData("key2", "value2");
    
    try {
        // Create queue.
        $queueRestProxy->createQueue("myqueue", $createQueueOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

> [AZURE.NOTE] メタデータ キーでは大文字と小文字は区別されません。 すべてのキーはサービスから小文字で返されます。


## メッセージをキューに追加する

メッセージをキューに追加するには、**QueueRestProxy->createMessage** を使用します。 このメソッドにはキュー名、メッセージ テキスト、メッセージ オプション (省略可能) を渡します。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\CreateMessageOptions;
    
    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
    
    try {
        // Create message.
        $builder = new ServicesBuilder();
        $queueRestProxy->createMessage("myqueue", "Hello World!");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## 次のメッセージをピークする

**QueueRestProxy->peekMessages** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。 既定では、**peekMessage** メソッドによって 1 つのメッセージが返されますが、その数は **PeekMessagesOptions->setNumberOfMessages** メソッドを使用して変更できます。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\PeekMessagesOptions;
    
    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
    
    // OPTIONAL: Set peek message options.
    $message_options = new PeekMessagesOptions();
    $message_options->setNumberOfMessages(1); // Default value is 1.
    
    try {
        $peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }
    
    $messages = $peekMessagesResult->getQueueMessages();
    
    // View messages.
    $messageCount = count($messages);
    if($messageCount <= 0){
        echo "There are no messages.<br />";
    }
    else{
        foreach($messages as $message)  {
            echo "Peeked message:<br />";
            echo "Message Id: ".$message->getMessageId()."<br />";
            echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
            echo "Message text: ".$message->getMessageText()."<br /><br />";
        }
    }

## 次のメッセージをデキューする

コードでは、2 つの手順でキューからメッセージを削除します。 まず、**QueueRestProxy->listMessages** を呼び出すことです。このメソッドから返されたメッセージは、このキューからメッセージを読み取る他のコードからは参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます (メッセージがこの時間内に削除されない場合、このキュー内で再び参照できるようになります)。 キューからのメッセージの削除を完了するには、**QueueRestProxy->deleteMessage** を呼び出す必要があります。 2 段階の手順でメッセージを削除するこの方法では、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 コードでは、メッセージが処理された直後に **deleteMessage** を呼び出します。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    
    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
    
    // Get message.
    $listMessagesResult = $queueRestProxy->listMessages("myqueue");
    $messages = $listMessagesResult->getQueueMessages();
    $message = $messages[0];
    
    /* ---------------------
        Process message.
       --------------------- */
    
    // Get message ID and pop receipt.
    $messageId = $message->getMessageId();
    $popReceipt = $message->getPopReceipt();
    
    try {
        // Delete message.
        $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## キューに配置されたメッセージの内容を変更する

**QueueRestProxy->updateMessage** を呼び出すことで、キュー内のメッセージの内容をインプレースで変更できます。 メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。 次のコードでは、キュー メッセージを新しい内容に更新し、表示タイムアウトを設定して、60 秒延長します。 これにより、メッセージに関連付けられている作業の状態が保存され、クライアントにメッセージの操作を続行する時間が 1 分与えられます。 この方法を使用すると、キュー メッセージに対する複数の手順から成るワークフローを追跡でき、ハードウェアまたはソフトウェアの問題が原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。 通常は、さらに再試行回数を保持し、メッセージの再試行回数が *n* 回を超えた場合はメッセージを削除するようにします。 こうすることで、処理するたびにアプリケーション エラーをトリガーするメッセージから保護されます。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    
    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
    
    // Get message.
    $listMessagesResult = $queueRestProxy->listMessages("myqueue");
    $messages = $listMessagesResult->getQueueMessages();
    $message = $messages[0];
    
    // Define new message properties.
    $new_message_text = "New message text.";
    $new_visibility_timeout = 5; // Measured in seconds.
    
    // Get message ID and pop receipt.
    $messageId = $message->getMessageId();
    $popReceipt = $message->getPopReceipt();
    
    try {
        // Update message.
        $queueRestProxy->updateMessage("myqueue",
                                    $messageId,
                                    $popReceipt,
                                    $new_message_text,
                                    $new_visibility_timeout);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## メッセージのデキュー用の追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、表示タイムアウトの設定を長くまたは短くすることができます。 次のコード例では、**getMessages** メソッドを使用して、1 回の呼び出しで 16 個のメッセージを取得します。 その後、**for** ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\ListMessagesOptions;
    
    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
    
    // Set list message options.
    $message_options = new ListMessagesOptions();
    $message_options->setVisibilityTimeoutInSeconds(300);
    $message_options->setNumberOfMessages(16);
    
    // Get messages.
    try{
        $listMessagesResult = $queueRestProxy->listMessages("myqueue",
                                                         $message_options);
        $messages = $listMessagesResult->getQueueMessages();
    
        foreach($messages as $message){
    
            /* ---------------------
                Process message.
            --------------------- */
    
            // Get message Id and pop receipt.
            $messageId = $message->getMessageId();
            $popReceipt = $message->getPopReceipt();
    
            // Delete message.
            $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## キューの長さを取得する

キュー内のメッセージの概数を取得できます。 **QueueRestProxy->getQueueMetadata** メソッドを使用して、キューのメタデータを返すようにキュー サービスに要求します。 返されたオブジェクトの **getApproximateMessageCount** メソッドを呼び出して、キュー内のメッセージの数を取得します。 キュー サービスが要求に応答した後にメッセージが追加または削除される可能性があるため、これらの値は概数にすぎません。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    
    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
    
    try {
        // Get queue metadata.
        $queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
        $approx_msg_count = $queue_metadata->getApproximateMessageCount();
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }
    
    echo $approx_msg_count;

## キューを削除する

キューとキューに含まれているすべてのメッセージを削除するには、**QueueRestProxy->deleteQueue** メソッドを呼び出します。

    require_once 'vendor\autoload.php';
    
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    
    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
    
    try {
        // Delete queue.
        $queueRestProxy->deleteQueue("myqueue");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## 次のステップ

これで、Azure キュー ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先をご覧ください。

- 参照してください、 [Azure Storage チームのブログ](http://blogs.msdn.com/b/windowsazurestorage/)します。

詳細については、「関連項目、 [PHP デベロッパー センター](/develop/php/)します。


[download]: http://go.microsoft.com/fwlink/?LinkID=252473 
[require_once]: http://www.php.net/manual/en/function.require-once.php 
[azure portal]: portal.azure.com 

