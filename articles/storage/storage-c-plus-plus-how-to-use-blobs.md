<properties 
    pageTitle="Blob ストレージ (C++) を使用する方法 |Microsoft Azure" 
    description="Azure で BLOB ストレージ サービスを使用する方法について説明します。サンプルは C++ で記述されています。" 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2015" 
    ms.author="tamram"/>


# C++ から BLOB ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概要

このガイドでは、Azure Blob ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは C++ および使用で記述され、 [C++ 用 Azure ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp/blob/v1.0.0/README.md)します。 紹介するシナリオは、BLOB の**アップロード**、**一覧表示**、**ダウンロード**、および**削除**です。
>[AZURE.NOTE] このガイドは、C++ 用 Azure ストレージ クライアント ライブラリ 1.0.0 以上のバージョンを対象としています。 推奨されるバージョンは、これはストレージ クライアント ライブラリ 1.0.0、 [NuGet](http://www.nuget.org/packages/wastorage) または [GitHub](https://github.com/Azure/azure-storage-cpp)します。 

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## C++ アプリケーションの作成

このガイドでは、C++ アプリケーション内で実行できるストレージ機能を使用します。

そのためには、C++ 用 Azure ストレージ クライアント ライブラリをインストールし、Azure サブスクリプションに Azure ストレージ アカウントを作成する必要があります。

C++ 用 Azure ストレージ クライアント ライブラリをインストールする場合、次の方法を使用できます。

-   **Linux:** 記載されている手順に従って、 [C++ README 用 Azure ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) ページです。
-   **Windows:** Visual Studio で、**[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順にクリックします。 次のコマンドを入力、 [NuGet パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) とキーを押します **ENTER**します。

        Install-Package wastorage


## BLOB ストレージにアクセスするようにアプリケーションを構成する

Azure Storage API を使用して BLOB にアクセスする C++ ファイルの先頭には、次の include ステートメントを追加します。

    #include "was/storage_account.h"
    #include "was/blob.h"

## Azure のストレージ接続文字列の設定

Azure ストレージ クライアントでは、ストレージ接続文字列を使用して、データ管理サービスにアクセスするためのエンドポイントおよび資格情報を保存します。 クライアント アプリケーションで実行するときに入力してください、次の形式でのストレージ接続文字列で示されたストレージ アカウントのストレージ アカウントとストレージ アクセス キーの名前を使用して、 [Azure ポータル](portal.azure.com) の *AccountName* と *AccountKey* 値。 詳細については、ストレージ アカウントとアクセス キーは、次を参照してください。 [Azure ストレージ アカウントについて](storage-create-storage-account.md)します。 この例では、接続文字列を保持する静的フィールドを宣言する方法を示しています。

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

ローカルの Windows コンピューターで、アプリケーションをテストするには、Microsoft Azure を使用することができます [ストレージ エミュレーター](https://msdn.microsoft.com/library/azure/hh403989.aspx)  と共にインストールされた、 [Azure SDK](http://azure.microsoft.com/downloads/)します。 ストレージ エミュレーターは、ローカルの開発マシンで、Azure で使用できる BLOB、Queue、および Table サービスをシミュレートするユーティリティです。 次の例では、ローカルのストレージ エミュレーターに接続文字列を保持する静的フィールドを宣言する方法を示しています。

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Azure のストレージ エミュレーターを起動するには、**[スタート]** ボタンをクリックするか、**Windows** キーを押します。 「**Azure ストレージ エミュレーター**」と入力し、アプリケーションの一覧から **[Microsoft Azure ストレージ エミュレーター]** を選択します。

次のサンプルでは、これら 2 つのメソッドのいずれかを使用してストレージ接続文字列を取得するとします。

## 接続文字列の取得

**cloud_storage_account** クラスを使用してストレージ アカウント情報を表すことができます。 ストレージ接続文字列からストレージ アカウント情報を取得するには、**parse** メソッド使用します。

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

次に、**cloud_blob_client** クラスへの参照を取得します。これにより、BLOB ストレージ サービス内に格納されているコンテナーと BLOB を表すオブジェクトを取得できます。 次のコードは、前に取得したストレージ アカウント オブジェクトを使用して、**cloud_blob_client** オブジェクトを作成します。

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## 方法: コンテナーを作成する

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

この例は、コンテナーがない場合に、コンテナーを作成する方法を示しています。

    try 
    {
        // Retrieve storage account from connection string.
        azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
    
        // Create the blob client.
        azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();
    
        // Retrieve a reference to a container.
        azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));
    
        // Create the container if it doesn't already exist.
        container.create_if_not_exists();
    }
    catch (const std::exception& e)
    {
        std::wcout << U("Error: ") << e.what() << std::endl;
    }  

既定では、新しいコンテナーはプライベートなので、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります。 コンテナー内のファイル (BLOB) をだれでも利用できるようにする場合は、次のコードを使ってコンテナーをパブリックに設定できます。

    // Make the blob container publicly accessible.
    azure::storage::blob_container_permissions permissions;
    permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
    container.upload_permissions(permissions);  

パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが表示できますが、変更または削除できるのは、適切なアクセス キーを持っているユーザーだけです。

## 方法: コンテナーに BLOB をアップロードする

Azure BLOB Storage では、ブロック BLOB とページ BLOB がサポートされています。 ほとんどの場合は、ブロック BLOB を使用することをお勧めします。

ファイルをブロック blob にアップロードするには、コンテナーの参照を取得し、それを使用してブロック blob の参照を取得します。 BLOB の参照を取得したら、**upload_from_stream** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。 この操作により、BLOB がまだ存在しない場合は作成され、存在する場合は上書きされます。 次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();
    
    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));
    
    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));
    
    // Create or overwrite the "my-blob-1" blob with contents from a local file.
    concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
    blockBlob.upload_from_stream(input_stream);
    input_stream.close().wait();
    
    // Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
    // Retrieve a reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
    blob2.upload_text(U("more text"));
    
    // Retrieve a reference to a blob named "my-blob-3".
    azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
    blob3.upload_text(U("other text"));  

**upload_from_file** メソッドを使用して、ブロック BLOB にファイルをアップロードすることもできます。

## 方法: コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。 次に、コンテナーの **list_blobs** メソッドを使用して、その中の BLOB やディレクトリを取得できます。 プロパティとメソッドが返されたの豊富なセットにアクセスする **list_blob_item**, 、呼び出す必要があります、 **list_blob_item.as_blob** 取得するメソッド、  **cloud_blob** オブジェクト、または **list_blob.as_directory** cloud_blob_directory オブジェクトを取得します。 次のコードは、**my-sample-container** コンテナー内の各アイテムの URI を取得して出力する方法を示しています。

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();
    
    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));
    
    // Output URI of each item.
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
        }
        else
        {
            std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
    }

操作の詳細については、次を参照してください。 [C++ での Azure ストレージ リソースの一覧の取得](storage-c-plus-plus-enumeration.md)します。

## 方法: BLOB をダウンロードする

BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **download_to_stream** メソッドを呼び出します。 次の例は、**download_to_stream** メソッドを使用して、ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();
    
    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));
    
    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));
    
    // Save blob contents to a file.
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    blockBlob.download_to_stream(output_stream);
    
    std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();
    
    outfile.write((char *)&data[0], buffer.size());
    outfile.close();  

代わりに、**download_to_file** メソッドを使用して、ファイルに BLOB の内容をダウンロードすることもできます。
**download_text** メソッドを使用して BLOB の内容をテキスト文字列としてダウンロードすることもできます。

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();
    
    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));
    
    // Retrieve reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));
    
    // Download the contents of a blog as a text string.
    utility::string_t text = text_blob.download_text();

## 方法: BLOB を削除する

BLOB を削除するには、まず BLOB の参照を取得し、次にその **delete_blob** メソッドを呼び出します。

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();
    
    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));
    
    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));
    
    // Delete the blob.
    blockBlob.delete_blob();

## 次のステップ

これで、BLOB ストレージの基本を学習できました。Azure Storage の詳細については、次のリンク先を参照してください。

-   [C++ から Queue ストレージを使用する方法](storage-c-plus-plus-how-to-use-queues.md)
-   [C++ から Table ストレージを使用する方法](storage-c-plus-plus-how-to-use-tables.md)
-   [C++ での Azure ストレージ リソースの一覧の取得](storage-c-plus-plus-enumeration.md)
-   [Storage Client Library for C リファレンス](http://azure.github.io/azure-storage-cpp)
-   [Azure Storage のドキュメント](http://azure.microsoft.com/documentation/services/storage/)
- [AzCopy コマンド ライン ユーティリティを使用してデータを転送します。](storage-use-azcopy)









