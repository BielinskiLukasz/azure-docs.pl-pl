<properties 
   pageTitle="クロスプラットフォーム コマンド ライン インターフェイスで Data Lake Store の使用を開始する |Microsoft Azure"
   description="Azure クロスプラットフォーム コマンド ラインを使用して、Data Lake Store アカウントを作成し、基本的な操作を実行します" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# Azure コマンド ラインを使用して Azure Data Lake Store の使用を開始する

> [AZURE.SELECTOR]
- [ポータルを使用してください。](data-lake-store-get-started-portal.md)
- [PowerShell の使用](data-lake-store-get-started-powershell.md)
- [.NET SDK の使用](data-lake-store-get-started-net-sdk.md)
- [Azure CLI の使用](data-lake-store-get-started-cli.md)
- [Node.js の使用](data-lake-store-manage-use-nodejs.md)

Azure コマンド ライン インターフェイスを使用して、Azure Data Lake Store アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。湖のデータ ストアの詳細については、次を参照してください。 [データ湖ストアの概要](data-lake-store-overview.md)します。

Azure CLI は Node.js で実装されます。 Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。 Azure CLI はオープン ソースです。 ソース コードは GitHub (<a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>) で管理されています。 この記事では、Data Lake Store での Azure CLI の使用についてのみ説明します。 Azure CLI を使用する方法の一般的なガイドラインを参照してください。 [Azure CLI の使用方法] [azure-command-line-tools]します。


##前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**します。 参照してください [取得 Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)します。
- **Azure サブスクリプションを有効にする** 湖のデータ ストアのパブリック プレビューします。 参照してください [指示](data-lake-store-get-started-portal.md#signup)します。 
- **Azure CLI** -を参照してください [をインストールし、Azure CLI の構成](../xplat-cli-install.md) インストールと構成情報。 CLI をインストールした後で、コンピューターを再起動してください。

##Azure サブスクリプションへのログイン

記載されている手順に従います [Azure サブスクリプションへの接続から、Azure コマンド ライン インターフェイス (Azure CLI)](xplat-cli-connect.md) を使用して、サブスクリプションに接続し、 __ログイン__ メソッドです。


## Azure Data Lake Store アカウントを作成する

コマンド プロンプト、シェル、またはターミナル セッションを開き、次のコマンドを実行します。

1. Azure サブスクリプションにログインします。

        azure login

    Web ページを開いて認証コードを入力するよう求められます。 ページの指示に従って、Azure サブスクリプションにログインします。 

2. 次のコマンドで Azure リソース マネージャー モードに切り替えます。

        azure config mode arm


3. アカウントの Azure サブスクリプションを一覧表示します。

        azure account list


4. 複数の Azure サブスクリプションがある場合は、次のコマンドを使用して、Azure CLI コマンドが使用するサブスクリプションを設定します。

        azure account set <subscriptionname>

5. 新しいリソース グループを作成します。 次のコマンドで、使用するパラメーター値を指定します。

        azure group create <resourceGroup> <location>

    場所の名前にスペースが含まれる場合は、名前を引用符で囲みます。 たとえば、"East US 2" などとします。

5. Data Lake Store アカウントを作成します。

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## Data Lake Store にフォルダーを作成する

Azure Data Lake Store アカウントにフォルダーを作成し、データの管理と保存を行うことができます。 次のコマンドを使用すると、Data Lake Store のルートに "mynewfolder" という名前のフォルダーが作成されます。

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

次に例を示します。

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## Data Lake Store にデータをアップロードする

データは、ルート レベルで直接 Data Lake Store に、またはアカウント内で作成したフォルダーにアップロードできます。 次のスニペット フォルダーにサンプル データをアップロードする方法を示して (**mynewfolder**) 前のセクションで作成します。

アップロードするには、いくつかのサンプル データを探している場合は取得できます、 **救急データ** フォルダーから、 [Azure Data Lake Git リポジトリ](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData)します。 ファイルをダウンロードし、C:\sampledata\ など、コンピューター上のローカル ディレクトリに格納します。

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

次に例を示します。

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## Data Lake Store 内のファイルを一覧表示する

Data Lake Store アカウントのファイルを一覧表示するには、以下のコマンドを使用します。

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

次に例を示します。

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

この出力は次のように表示されます。

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## Data Lake Store からデータの名前変更、ダウンロード、および削除を行う

* **ファイルの名前を変更する**, 、次のコマンドを使用します。

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    次に例を示します。

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **ファイルをダウンロードする**, 、次のコマンドを使用します。 既に存在するパスをダウンロード先として指定してください。

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    次に例を示します。

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **ファイルを削除する**, 、次のコマンドを使用します。

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path> 

    次に例を示します。

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
    
    求められたら、入力 **Y** アイテムを削除します。

## Data Lake Store のフォルダーのアクセス制御リストを表示する

Data Lake Store フォルダーの ACL を表示するには、次のコマンドを使用します。 現在のリリースでは、ACL を設定できるのは Data Lake Store のルートに対してだけです。 したがって、次の path パラメーターは常にルート (/) です。

    azure datalake store permissions show <dataLakeStoreName> <path>

次に例を示します。

    azure datalake store permissions show mynewdatalakestore /


## Data Lake Store アカウントを削除する

Data Lake Store アカウントを削除するには、次のコマンドを使用します。

    azure datalake store account delete <dataLakeStoreAccountName>

次に例を示します。

    azure datalake store account delete mynewdatalakestore

求められたら、入力 **Y** 、アカウントを削除します。

## Data Lake Store アカウントの他の作成方法

- [Azure プレビュー ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
- [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)
- [Azure PowerShell で Data Lake Store の使用を開始する](data-lake-store-get-started-powershell.md)


## 次のステップ

- [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
- [Data Lake Store で Azure Data Lake Analytics を使用する](data-lake-analytics-get-started-portal.md)
- [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md

