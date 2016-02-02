<properties
    pageTitle="Node.js から BLOB ストレージを使用する方法 | Microsoft Azure"
    description="Azure BLOB サービスを使用して、BLOB の内容をアップロード、ダウンロード、一覧表示、および削除する方法について説明します。サンプルは Node.js で記述されています。"
    services="storage"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="robmcm"/>




# Node.js から BLOB ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概要

この記事では、Azure BLOB サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Node.js API を使用して記述されています。 紹介するシナリオには、アップロード、一覧表示、ダウンロード、および blob を削除する方法が含まれます。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Node.js アプリケーションの作成

Node.js アプリケーションを作成する方法の手順については、次を参照してください。 [作成と Azure の web サイトに Node.js アプリケーションを展開]、 [Node.js クラウド Service][node.js cloud service] (Windows PowerShell を使用)、または [WebMatrix を使用した Web アプリ] です。

## アプリケーションのストレージへのアクセスの構成

Azure Storage を使用するには、Azure Storage SDK for Node.js が必要です。ここには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### ノード パッケージ マネージャー (NPM) を使用してパッケージを取得する

1.  **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用して、サンプル アプリケーションを作成したフォルダーに移動します。

2.  コマンド ウィンドウに「**npm install azure-storage**」と入力します。 コマンドの出力は次のコード例に似ています。

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  手動で実行することができます、 **%.*ls** いることを確認するにはコマンド、 **node \_modules** フォルダーが作成されました。 このフォルダーで **azure-storage** パッケージを検索します。このパッケージには、ストレージにアクセスするために必要なライブラリが含まれています。

### パッケージをインポートする

メモ帳などのテキスト エディターを使用して、ストレージを使用するアプリケーションの **server.js** ファイルの先頭に次の内容を追加します。

    var azure = require('azure-storage');

## Azure Storage の接続文字列の設定

Azure モジュールでは、環境変数を読み取ります `AZURE_STORAGE_ACCOUNT` と `AZURE_STORAGE_ACCESS_KEY`, 、または `AZURE_STORAGE_CONNECTION_STRING`, 、Azure ストレージ アカウントへの接続に必要な情報です。 これらの環境変数が設定されていない場合は、**createBlobService** を呼び出すときにアカウント情報を指定する必要があります。

環境変数を設定の例については、 [Azure ポータル](portal.azure.com) Azure web アプリでは、[使用する Node.js Web アプリケーション] を参照してください。

## コンテナーを作成する

**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。 次のコードでは、**BlobService** オブジェクトを作成します。 **server.js** ファイルの先頭付近に次の内容を追加します。

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] **createBlobServiceAnonymous** を使用してホスト アドレスを指定すると、BLOB に匿名でアクセスできます。 たとえば、使用して `「var blobSvc = azure.createBlobServiceAnonymous ('https://myblob.blob.core.windows.net/');`します。

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

新しいコンテナーを作成するには、**createContainerIfNotExists** を使用します。 次のコード例では、'mycontainer' という名前の新しいコンテナーを作成します。

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows
        // anonymous read access to blob
        // content and metadata within this container
      }
    });

コンテナーを新規に作成された場合 `結果` は true を指定します。 コンテナーが既に存在する場合 `結果` は false。 `応答` 、操作に関する情報を含むなど、 [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) 、コンテナーの情報です。

### コンテナーのセキュリティ

既定では、新しいコンテナーはプライベートであり、匿名でアクセスすることはできません。 コンテナーを公開して匿名でアクセスできるようにするには、コンテナーのアクセス レベルを **blob** または **container** に設定します。

* **blob** - BLOB の内容およびこのコンテナー内のメタデータへの匿名の読み取りは許可されますが、1 つのコンテナー内の全 BLOB のリストなど、コンテナーのメタデータに対する匿名読み取りは許可されません。

* **container** - コンテナーのメタデータに加えて、BLOB の内容とメタデータに対する匿名読み取りが許可されます。

次のコード例では、アクセス レベルを **blob** に設定する方法を示します。

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
    });

代わりに、**setContainerAcl** を使用してアクセス レベルを指定することによって、コンテナーのアクセス レベルを変更できます。 次のコード例では、アクセス レベルを container に変更します。

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

結果には、コンテナーの現在の **ETag** 情報を含む、操作に関する情報が含まれます。

### フィルター

オプションのフィルター操作は、**BlobService** を使用して行われる操作に適用できます。 フィルター操作には、ログや自動的な再試行などが含まれる場合があります。フィルターは、次のシグネチャを持つメソッドを実装するオブジェクトです。

        function handle (requestOptions, next)

要求オプションに対するプリプロセスを行った後で、このメソッドは "next" を呼び出して、次のシグネチャのコールバックを渡す必要があります。

        function (returnObject, finalCallback, next)

このコールバックで、returnObject (サーバーへの要求からの応答) の処理の後に、コールバックは next を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に finalCallback を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。 次のコードは、**ExponentialRetryPolicyFilter** を使用する **BlobService** オブジェクトを作成します。

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## コンテナーに BLOB をアップロードする

BLOB はブロックベースまたはページ ベースのいずれにもできます。 ブロック blob は大量のデータを効率的にアップロードできる一方、ページ blob は読み取りと書き込みの操作に適しています。 詳細については、次を参照してください。 [ブロック blob とページ blob について](http://msdn.microsoft.com/library/azure/ee691964.aspx)します。

### ブロック blob

データをブロック blob にアップロードするには、以下のメソッドを使用します。

* **createBlockBlobFromLocalFile** - 新しいブロック blob を作成してファイルの内容をアップロードします。

* **createBlockBlobFromStream** - 新しいブロック blob を作成してストリームの内容をアップロードします。

* **createBlockBlobFromText** - 新しいブロック blob を作成して文字列の内容をアップロードします。

* **createWriteStreamToBlockBlob** - ブロック blob への書き込みストリームを提供します。

次のコード例では、**test.txt** ファイルの内容を **myblob** にアップロードします。

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

`結果` これらのメソッドによって返される、操作に関する情報を含む、 **ETag** blob のです。

### ページ blob

データをページ blob にアップロードするには、以下のメソッドを使用します。

* **createPageBlob** - 指定の長さの新しいページ blob を作成します。

* **createPageBlobFromLocalFile** - 新しいページ blob を作成してファイルの内容をアップロードします。

* **createPageBlobFromStream** - 新しいページ blob を作成してストリームの内容をアップロードします。

* **createWriteStreamToExistingPageBlob** - 既存のページ blob への書き込みストリームを提供します。

* **createWriteStreamToNewPageBlob** - 新しい BLOB を作成してから、この BLOB に書き込むストリームを提供します。

次のコード例では、**test.txt** ファイルの内容を **mypageblob** にアップロードします。

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] ページ blob は、512 バイトの "ページ" で構成されています。 512 の倍数でないサイズのデータをアップロードするとエラーになる場合があります。

## コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、**listBlobsSegmented** メソッドを使用します。 特定のプレフィックスと共に BLOB を返す必要がある場合は、**listBlobsSegmentedWithPrefix** を使用します。

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result.entries contains the entries
        // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

`結果` を含む、 `エントリ` コレクションがある各 blob を記述するオブジェクトの配列。 すべての blob を返すできない場合、 `結果` も用意されています、 `continuationToken`, 、追加のエントリを取得する 2 番目のパラメーターとして使用することができます。

## BLOB をダウンロードする

BLOB からデータをダウンロードするには、以下のメソッドを使用します。

* **getBlobToLocalFile** - BLOB の内容をファイルに書き込みます。

* **getBlobToStream** - BLOB の内容をストリームに書き込みます。

* **getBlobToText** - BLOB の内容を文字列に書き込みます。

* **createReadStream** - BLOB から読み取るためのストリームを提供します。

次のコード例は、**getBlobToStream** を使用して **myblob** BLOB の内容をダウンロードし、ストリームを使用して **output.txt** ファイルに格納する方法を示しています。

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

`結果` 、blob に関する情報を含むなど **ETag** 情報。

## BLOB を削除する

最後に、BLOB を削除するには、**deleteBlob** を呼び出します。 次のコード例では、**myblob** という名前の BLOB を削除します。

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## 同時アクセス

複数のクライアントまたは複数のプロセス インスタンスからの BLOB への同時アクセスをサポートするには、**ETag** または**占有**を使用します。

* **Etag** - BLOB またはコンテナーが別のプロセスによって変更されていることを検出する手段を提供します。

* **占有** - 一定期間、BLOB に対する排他的で更新可能な書き込みアクセスまたは削除アクセスを取得する手段を提供します。

### ETag

ETag は、複数のクライアントまたはインスタンスからの BLOB への同時書き込みを許可する必要がある場合に使用してください。 ETag を使用すると、最初の読み取りまたは作成以降にコンテナーまたは BLOB が変更されているかどうかを確認できることから、別のクライアントまたはプロセスによってコミットされた変更の上書きを回避できます。

ETag の条件を設定するには、オプションを使用して `options.accessConditions` パラメーター。 次のコード例のアップロードだけ、 **test.txt** に含まれている場合は、blob が既に存在し、ETag 値を持つファイル `etagToMatch`します。

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

ETag を使用している場合、一般的なパターンは次のとおりです。

1. 作成、一覧表示、取得の操作の結果として ETag を取得する。

2. ETag 値が変更されていないことを確認するアクションを実行する。

値が変更されていた場合は、ETag 値を取得して以降、別のクライアントまたはインスタンスがこの BLOB またはコンテナーを変更したことを示しています。

### 占有

占有を取得する BLOB またはコンテナーを指定して **acquireLease** メソッドを使用すると、新しい占有を取得できます。 たとえば、以下のコードでは **myblob** での占有を取得します。

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

後続の処理を **myblob** を提供する必要があります、 `は options.leaseId` パラメーター。 ID として返されるリース `result.id` から **acquireLease**します。
> [AZURE.NOTE] 既定では、占有期間は無限です。 提供することで、有限の期間 (15 ~ 60 秒) を指定することができます、 `options.leaseDuration` パラメーター。

占有を削除するには、**releaseLease** を使用します。 占有を中断するものの、元の期間が期限切れになるまでは新しい占有が取得されないようにするには、**breakLease** を使用します。

## 共有アクセス署名を操作する

共有アクセス署名 (SAS) は、ストレージ アカウント名やキーを指定せずに BLOB やコンテナーへのきめ細やかで安全なアクセスを提供する方法です。 SAS は、モバイル アプリからの BLOB へのアクセスを許可する場合など、データへの制限されたアクセスを提供する場合によく使用されます。
> [AZURE.NOTE] BLOB への匿名のアクセスも許可できるものの、共有アクセス署名ではより制御されたアクセスを提供することができます。SAS は生成の必要があるためです。

クラウドベースのサービスなどの信頼されたアプリケーションは、**BlobService** の **generateSharedAccessSignature** を使用して共有アクセス署名を生成し、信頼されていないか、モバイル アプリなどの部分的に信頼されたアプリケーションにこれを提供します。 共有アクセス署名は、ポリシーを使用して作成されます。共有アクセス署名には、有効期間の開始日と終了日、共有アクセス署名フォルダーに付与されているアクセス レベルが記述されています。

次のコード例では、共有アクセス署名の保有者による **myblob** BLOB に対する読み取り操作を許可する新しい共有アクセス ポリシーを生成します。このポリシーは作成後 100 分が経過すると期限切れになります。

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);
    
    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };
    
    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

共有アクセス署名の保有者がコンテナーにアクセスするときに必要なホスト情報も提供する必要があることに注意してください。

その後、クライアント アプリケーションは、この共有アクセス署名と **BlobServiceWithSAS** を使用して BLOB に対する操作を実行します。 次の例では、**myblob** に関する情報を取得します。

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

共有アクセス署名は読み取り専用アクセスで生成されるので、BLOB を変更しようとすると、エラーが返されます。

### アクセス制御リスト

SAS のアクセス ポリシーを設定するために、アクセス制御リスト (ACL) も使用できます。 複数のクライアントにコンテナーへのアクセスを許可し、各クライアントに異なるアクセス ポリシーを提供する場合に便利です。

ACL は、アクセス ポリシーの配列と、各ポリシーに関連付けられた ID を使用して実装されます。 次のコード例では、2 つのポリシーを定義しています。1 つは "user1" 用、もう 1 つは "user2" 用です。

    var sharedAccessPolicy = [
      {
        AccessPolicy: {
          Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user1'
      },
      {
        AccessPolicy: {
          Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user2'
      }
    ];

次のコード例では、**mycontainer** の現在の ACL を取得してから、**setBlobAcl** を使用して新しいポリシーを追加しています。 この手法で以下を実行できます。

    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        //push the new policy into signedIdentifiers
        result.signedIdentifiers.push(sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

ACL を設定したら、ポリシーの ID に基づいて共有アクセス署名を作成できます。 次のコード例では、'user2' の新しい共有アクセス署名を作成します。

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## 次のステップ

詳細については、次のリソースを参照してください。

-   [Azure Storage SDK for ノード API 参照][]
-   [Azure ストレージ チーム ブログ][]
-   [ノードの azure Storage SDK][] GitHub のリポジトリ
-   [Node.js デベロッパー センター](/develop/nodejs/)
-   [AzCopy コマンド ライン ユーティリティを使用してデータを転送します。](storage-use-azcopy)


[azure storage sdk for node]: https://github.com/Azure/azure-storage-node 
[create and deploy a node.js application to an azure web site]: /develop/nodejs/tutorials/create-a-website-(mac)/ 
[node.js cloud service with storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md 
[node.js web application with storage]: ../storage-nodejs-use-table-storage-web-site.md 
[web app with webmatrix]: ../web-sites-nodejs-use-webmatrix.md 
[using the rest api]: http://msdn.microsoft.com/library/azure/hh264518.aspx 
[azure portal]: portal.azure.com 
[node.js cloud service]: ../cloud-services-nodejs-develop-deploy-app.md 
[azure storage team blog]: http://blogs.msdn.com/b/windowsazurestorage/ 
[azure storage sdk for node api reference]: http://dl.windowsazure.com/nodestoragedocs/index.html 

