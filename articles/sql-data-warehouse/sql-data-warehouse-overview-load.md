   <properties
   pageTitle ="SQL Data Warehouse にデータを読み込む |Microsoft Azure]
   description =「SQL Data Warehouse でのデータ読み込みの一般的なシナリオについて説明します」
   サービス =「sql データ ウェアハウス」
   documentationCenter="NA"
   authors ="lodipalm"
   manager ="barbkess"
   エディター ="jrowlandjones"/>

<tags
   ms.service=「sql データ ウェアハウス」
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload=「データ サービス」
   ms.date="12/02/2015"
   ms.author="lodipalm;barbkess"/>

# SQL Data Warehouse へのデータのロード

SQL Data Warehouse には、データをロードするために以下のような多数のオプションが用意されています。

- PolyBase
- Azure Data Factory
- BCP コマンドライン ユーティリティ
- SQL Server Integration Services (SSIS)
- サード パーティ製のデータ ロード ツール

SQL Data Warehouse では上記のすべてのメソッドを使用できますが、PolyBase が Azure BLOB Storage からの読み込みを透過的に並列処理する機能を使用すると、データの読み込みが最速になります。 チェック アウトする方法の詳細 [PolyBase で読み込む][]します。 また、ユーザーの多くがオンプレミス ソースから 100 GB 単位から 10 TB 単位の初期読み込みを要求した場合のように、以下のセクションでは、初期データ読み込みについていくつかのガイダンスを提供します。

## SQL Server から SQL Data Warehouse への初期ロード

オンプレミスの SQL Server インスタンスから SQL Data Warehouse にロードをするには、以下の手順をお勧めします。

1. SQL Server データをフラット ファイルに **BCP** する。
2. **AZCopy** または **インポート/エクスポート** (大きめのデータセットの場合) を使用して、ファイルを Azure に移動する。
3. PolyBase を構成して、自身のストレージ アカウントからファイルを読み込む。
4. 新しいテーブルを作成し、**PolyBase** でデータを読み込む。

次のセクションでは、各ステップをより詳しく説明し、処理の例を提供します。
> [AZURE.NOTE] SQL Server などのシステムからデータを移動する前に確認してください、 [移行スキーマ][] と [移行コード][] ドキュメントの記事です。 

## BCP によるファイルのエクスポート

Azure へのファイルの移動の準備として、ファイルをフラット ファイルにエクスポートする必要があります。 これには BCP コマンドライン ユーティリティを使用すると最適です。 ユーティリティがないまだを使用してダウンロードできるかどうか、 [SQL サーバーの Microsoft コマンド ライン ユーティリティ][]します。 サンプルの BCP コマンドは、次のようになります。

```
bcp "<Directory>\<File>" -c -T -S <Server Name> -d <Database Name>
```

このコマンドは、クエリの結果を、指定したディレクトリのファイルにエクスポートします。 複数の BCP コマンドを個別のテーブルを一度にが、サーバーのコアあたり 1 つの BCP プロセスまで実行します。 これによりを実行して、プロセスを並列化からのアドバイスが、環境に最適に動作してさまざまな構成で、いくつかの簡単な操作を実行してください。

さらに、PolyBase を使用してロードする際には、PolyBase が UTF-16 をまだサポートしておらず、すべてのファイルが UTF-8 でなければならないことに注意してください。 これは、BCP コマンドで "-c" フラグを使用することで容易に解決し、また、フラット ファイルを下記のコードで UTF-16 から UTF-8 に変換することもできます。

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

ファイルへのデータのエクスポートに成功したら、ファイルを Azure に移動します。 これは、次のセクションで述べる AZCopy もしくは "インポート/エクスポート" サービスで実行可能です。

## AZCopy またはインポート/エクスポートを使用した Azure へのロード

当社のディスク発送サービスを使用することをお勧め、5 ~ 10 テラバイトもの範囲内のデータを移動する場合、上記の [インポート/エクスポート][] 移動を行います。 しかし、我々が調べたところ、テラバイトまでの範囲なら、AZCopy で公開インターネットを使って問題なくデータを移動できることがわかっています。 この処理は ExpressRoute でスピードアップもしくは拡張できます。

次のステップで、AZCopy を使って、オンプレミスから Azure ストレージ アカウントにデータを移動する方法を詳しく説明します。 同じリージョンに Azure ストレージ アカウントを持っていない場合は、次、1 つを作成、 [Azure Storage のドキュメントの][]します。 異なるリージョンのストレージ アカウントからもデータをロードできます。しかし、この場合は最適なパフォーマンスとなりません。
> [AZURE.NOTE] 本ドキュメントでは、すでに AZCopy コマンド ライン ユーティリティがインストールされているものとしており、その場合、AZCopy は Powershell で実行可能です。 そうでない場合に従ってください、 [AZCopy インストール手順については][]します。  

BCP を使って作成されたファイルのセットができたら、Azure powershell から、または、powershell スクリプトの実行により、AZCopy を簡単に実行できます。 AZCopy を実行する際のプロンプトのおおよその形式は次のようになります。

```
AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256
```

AZCopy でのロードについては、基本に加えて、以下のベスト プラクティスをお勧めします。


+ **同時接続**: 一度に実行する AZCopy の操作の数を増やすことに加え、/NC パラメーターの設定でロード先への同時接続数を開くことにより、AZCopy 操作自体の並行処理を促進できます。 /NC パラメーターは 512 まで高い値に設定できますが、最適なデータ転送は 256 であることがわかっており、構成に最適なパラメーターの数値を試行して見つけることをお勧めします。

+ **Express Route**: 既に述べたように、AZCopy の処理は express route を有効にするとスピードアップできます。 Express Route と構成手順の概要を参照して、 [ExpressRoute ドキュメントの][]します。

+ **フォルダー構造**: PolyBase での転送を容易にするため、各テーブルが必ずその独自のフォルダーにマップされるようにしてください。 これにより、PolyBase でのロードを後で行う際に、手順が最小限に簡素化されます。 とは言っても、テーブルがフォルダー内で複数のファイルや、さらにはサブ ディレクトリに分割されていたとしても問題はありません。


## PolyBase の構成

データが Azure ストレージ BLOB に存在するようになったら、SQL Data Warehouse インスタンスへ PolyBase を使用してデータをインポートします。 以下の手順は構成のみであり、手順の多くは SQL Data Warehouse インスタンス、ユーザー、ストレージ アカウントごとに一回だけ完了する必要があります。 次の手順が説明していますより詳細に説明、 [PolyBase で読み込む][] ドキュメントです。

1. **データベース マスター キーの作成**この操作はデータベースごとに一回だけ完了する必要があります。

2. **データベース スコープの資格情報の作成**この操作は新しい資格情報/ユーザーを作成する場合にのみ必要であり、それ以外は事前に作成された資格情報を使用できます。

3. **外部ファイル形式の作成**外部ファイル形式も同様に再利用でき、新しいファイル形式をアップロードするときのみ作成が必要です。

4. **外部データ ソースの作成**ストレージ アカウントについては、同じコンテナーからロードする場合は、外部データ ソースを使用できます。 "LOCATION" パラメーターでは、フォーマットのロケーション： "wasbs://mycontainer@ test.blob.core.windows.net/path" を使用してください。

```
-- Creating master key
CREATE MASTER KEY;

-- Creating a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL <Credential Name> 
WITH 
    IDENTITY = '<User Name>'
,   Secret = '<Azure Storage Key>'
;

-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(
    FORMAT_TYPE = DELIMITEDTEXT 
,   FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|' 
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
);

--Creating an external data source
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH 
(
    TYPE = HADOOP 
,   LOCATION ='wasbs://<Container>@<Blob Path>'
,   CREDENTIAL = <Credential Name>
)
;
```

ストレージ アカウントが正しく構成されたら、SQL Data Warehouse へのデータのロードに進みます。

## PolyBase を使用したデータのロード

PolyBase を構成した後は、ストレージ内のデータを参照する外部テーブルを作成し、そのデータを SQL Data Warehouse 内の新しいテーブルにマップするだけで、データを SQL Data Warehouse に直接ロードできます。 これは以下の簡単な 2 つのコマンドだけで実行できます。

1. "CREATE EXTERNAL TABLE" コマンドを使用して、データの構造を定義します。 データの状態をすばやく効率的に取得できるように、SQL Server テーブルを SSMS でスクリプティングし、外部テーブルとの相違を解決する際は手動にて調整することをお勧めします。 Azure に外部テーブルを作成したら、外部テーブルはデータがアップデートされたり、データが追加されても、同じロケーションを参照しつづけます。

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE <External Table Name> 
(
    <Column name>, <Column type>, <NULL/NOT NULL>
)
WITH 
(   LOCATION='<Folder Path>'
,   DATA_SOURCE = <Data Source>
,   FILE_FORMAT = <File Format>      
);
```

2. "CREATE TABLE...AS SELECT" ステートメントでデータをロードします。

```
CREATE TABLE <Table Name> 
WITH 
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = <HASH(<Column Name>)>/<ROUND_ROBIN>
)
AS 
SELECT  * 
FROM    <External Table Name>
;
```

より詳細の SELECT ステートメントを使用すると、テーブルから行のサブセクションのロードもできることに注意してください。 しかし、PolyBase は今回はストレージ アカウントに追加のコンピューティングを転送してくれるわけではないので、SELECT ステートメントでサブセクションをロードする場合は、データセット全体をロードするよりも早くなることはありません。

だけでなく、 `表を作成しています.AS SELECT` ステートメント、読み込むこともできますデータ外部テーブルからを持つ既存のテーブルに、' INSERT.' ステートメントです。

## 新しくロードしたデータの統計を作成する

Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。 クエリから最高のパフォーマンスを取得するには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。 統計情報の詳細については、次を参照してください。、 [統計情報 []][] 開発一連のトピックの「します。 この例でロードしたテーブルの統計を作成する方法の簡単な例を次に示します。


```
create statistics [<name>] on [<Table Name>] ([<Column Name>]);
create statistics [<another name>] on [<Table Name>] ([<Another Column Name>]);
```

## 次のステップ

他の開発のヒントを参照してください、 [開発の概要][]します。







[load data with bcp]: sql-data-warehouse-load-with-bcp.md 
[load with polybase]: sql-data-warehouse-load-with-polybase.md 
[solution partners]: sql-data-warehouse-solution-partners.md 
[development overview]: sql-data-warehouse-overview-develop.md 
[migrate schema]: sql-data-warehouse-migrate-schema.md 
[migrate code]: sql-data-warehouse-migrate-code.md 
[statistics]: sql-data-warehouse-develop-statistics.md 
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx 
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx 
[sql server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx 
[ssis]: https://msdn.microsoft.com/library/ms141026.aspx 
[azcopy installation instructions]: https://azure.microsoft.com/en-us/documentation/articles/storage-use-azcopy/ 
[microsoft command line utilities for sql server]: http://www.microsoft.com/en-us/download/details.aspx?id=36433 
[import/export]: https://azure.microsoft.com/en-us/documentation/articles/storage-import-export-service/ 
[azure storage documentation]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/ 
[expressroute documentation]: http://azure.microsoft.com/en-us/documentation/services/expressroute/ 

