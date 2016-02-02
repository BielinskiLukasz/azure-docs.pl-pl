<properties
   pageTitle="SQL Data Warehouse へのデータの移行 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse へのデータの移行に関するヒント"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>


# データの移行

データを移行する主な目的は、SQLDW データベースにデータを取り込むことです。 このプロセスは、さまざまな方法で実行できます。 ADF コピー、SSIS、bcp はすべて、この目的を果たすために使用できます。 ただし、データ量が増えると、データ移行プロセスを複数のステップに分割することを検討する必要が生じます。 これにより、パフォーマンスと復元性の両面で各ステップを最適化し、スムーズなデータ移行を実行できる可能性が高まります。

この記事では最初に ADF コピー、SSIS、および bcp の単純な移行シナリオについて説明します。 次に、移行を最適化する方法についてもう少し詳細に検討していきます。

## Azure Data Factory (ADF) コピー

[ADF Copy[]][] is part of [Azure Data Factory[]][]. ADF コピーを使用して、ローカル ストレージに置かれているフラット ファイル、Azure BLOB ストレージに保持されているリモート フラット ファイル、または SQL Data Warehouse 内のディレクトリにデータをエクスポートできます。

データがフラット ファイルで始まる場合、SQL Data Warehouse へのロードを開始する前に、まず、Asure ストレージ BLOB にそれを転送する必要があります。 Azure blob ストレージにデータを転送した後は、使用することもできます [ADF コピー][] SQL Data Warehouse にデータをプッシュするには、もう一度です。

PolyBase には、データをロードするための非常に高いパフォーマンスのオプションも用意されています。 ただし、これは 1 つではなく、2 つのツールを使用するという意味です。 最高のパフォーマンスが必要であれば、PolyBase を使用してください。 1 つのツールのみを使用する場合は (および、データが大規模ではない場合は)、ADF を使用してください。
> [AZURE.NOTE] PolyBase では、データ ファイルは UTF-8 である必要があります。 これは ADF コピーの既定のエンコーディングであるため、変更することはできません。 ADF コピーの規定の動作は変更しないでください。

いくつかのすばらしい [ADF コピーの例] の次の記事を進んでいます。

## Integration Services

Integration Services (SSIS) は、強力で柔軟な抽出、変換、ロード (ETL) ツールであり、複雑なワークフロー、データの変換、およびいくつかのデータ ロード オプションをサポートします。 SSIS を使用して単にデータを Azure に転送したり、より広範囲にわたる移行の一部として転送したりします。
> [AZURE.NOTE] SSIS は、ファイルにバイト オーダーをマークせずに UTF-8 にエクスポートできます。 これを構成するには、まず派生された列コンポーネントを使用して、65001 UTF-8 コード ページを使用するようにデータ フローで文字データを変換します。 列が変換されたら、ファイルのコード ページとして 65001 も選択されていることを確認し、フラット ファイルの宛先アダプターにデータを書き込みます。

SSIS は、SQL Server のデプロイメントに接続する場合と同様に、SQL Data Warehouse に接続します。 ただし、接続には ADO.NET 接続マネージャーを使用する必要があります。 また、"使用可能な場合は一括挿入を使用する" 設定を構成してスループットを最大化することを検討する必要もあります。 参照してください、 [ADO.NET 変換先アダプターの][] このプロパティの詳細についての記事
> [AZURE.NOTE] OLEDB を使用した Azure SQL Data Warehouse への接続はサポートされていません。

さらに、調整やネットワークの問題が原因で、パッケージが失敗する可能性は常にあります。 パッケージを設計するときは、失敗する前に完了していた作業をやり直さなくても済むように、失敗した時点でパッケージを再開できるように設計してください。

詳細について参照して、 [SSIS のドキュメントの][]します。

## bcp

bcp は、フラット ファイル データのインポートとエクスポート用に設計されたコマンド ライン ユーティリティです。 データのエクスポート中に、いくつかの変換が実行される可能性があります。 単純な変換を実行するには、クエリを使用してデータを選択し、変換します。 エクスポートが完了したら、フラット ファイルをターゲットである SQL Data Warehouse のデータベースに直接ロードできます。
> [AZURE.NOTE] ソース システムのビューで、データ エクスポート中に使用される変換をカプセル化したほうがいい場合がよくあります。 これにより、ロジックが保持され、プロセスを反復できるようになります。

bcp の利点は次のとおりです。

- 簡単になります。 bcp コマンドは簡単にビルドおよび実行
- 再起動可能なロード プロセス。 エクスポートした後、ロードは何回でも実行できます

bcp の制限は次のとおりです。

- bcp は表形式のフラット ファイルのみで動作します。 xml や JSON などのファイルでは機能しません
- bcp では、UTF-8 へのエクスポートはサポートされません。 これにより、bcp でエクスポートされたデータで PolyBase を使用できない場合があります
- データ変換機能はエクスポート段階のみに限定されており、本質的に単純です
- インターネット経由でデータをロードする場合、bcp は堅牢な構成になりません。 ネットワークが不安定になると、ロードでエラーが発生する可能性があります。
- bcp はロードの前に対象のデータベースに存在していたスキーマに依存します

詳細については、次を参照してください。 [bcp を使用して、SQL Data Warehouse [] にデータを読み込む][]します。

## データの移行の最適化

SQLDW データ移行処理は、事実上、次の 3 つの別個のステップに分割できます。

1. ソース データのエクスポート
2. Azure へのデータの転送
3. 対象の SQLDW データベースへのロード

各ステップを個別に最適化して、各ステップでパフォーマンスを最大化する、堅牢で再開可能であり、回復力のある移行プロセスを作成できます。

## データ ロードの最適化

ここで振り返ってみると、最短でデータをロードする方法は、PolyBase を使用することであることがわかります。 PolyBase ロード プロセスを最適化するには、先行するステップで前提条件が生じるため、事前にそれを理解しておいてください。 次に例を示します。

1. データ ファイルのエンコード
2. データ ファイルの形式
3. データ ファイルの場所

### エンコード

PolyBase では、データ ファイルは UTF-8 形式でエンコードされている必要があります。 つまり、データをエクスポートする場合、この要件に準拠する必要があります。 データに基本 ASCII 文字しか含まれていない場合 (拡張 ASCII は含まれていない場合)、これらは UTF-8 標準に直接マップされるため、エンコードについてそれほど気にすることはありません。 ただし、データにウムラウト、アクセント、または記号などの特殊文字が含まれている場合や、データでラテン語以外の言語がサポートされている場合は、エクスポート ファイルが適切に UTF-8 形式にエンコードされていることを確認する必要があります。
> [AZURE.NOTE] bcp では、UTF-8 へのデータのエクスポートはサポートされません。 このため、データのエクスポートには Integration Services か ADF コピーのいずれかを使用するのが最適です。 データ ファイルでは UTF-8 バイト オーダー マーク (BOM) は必要ないことも重要な点です。

Utf-16 でエンコードされたファイルは再度書き込みを行う必要があります *** 前 *** データ転送します。

### データ ファイルの形式

PolyBase では、固定行ターミネータとして \n または改行を使用する必要があります。 データ ファイルは、この標準に準拠する必要があります。 文字列または列のターミネータに関する制限はありません。

PolyBase で外部テーブルの一部として、ファイル内のすべての列を定義する必要があります。 エクスポートされたすべての列が必要であり、型が必須の標準に準拠していることを確認します。

サポートされるデータ型の詳細については、前述の「スキーマの移行」に関する記事を参照してください。

### データ ファイルの場所

SQL Data Warehouse は、PolyBase を使用して Azure BLOB ストレージのみからデータをロードします。 このため、データは最初に BLOB ストレージに転送されている必要があります。

## データ転送の最適化

データ移行で最も時間を要する部分の 1 つは、Azure へのデータの転送です。 ネットワーク帯域幅が問題になり得るというだけでなく、ネットワークの信頼性によっても進捗が大幅に妨げられる可能性があります。 既定では、インターネットを介して Azure にデータが移行されるため、転送エラーが発生する可能性は十分にあります。 ただし、これらのエラーによって、データの全体または一部を再送信する必要が生じる場合があります。

幸いなことに、この処理の速度と回復性を向上させるいくつかのオプションがあります。

### [ExpressRoute][]

使用を検討することも [ExpressRoute][] 転送を高速化します。 [ExpressRoute][]で確立されたプライベート接続を Azure にパブリックなインターネット経由の接続にならないようにします。 これは、必須の手順ではありません。 ただし、オンプレミスまたは共用施設からデータを Azure にプッシュするときのスループットが改善されます。

使用する利点 [ExpressRoute][] は。

1. 信頼性が向上する
2. ネットワーク速度が高まる
3. ネットワーク待ち時間が短縮される
4. ネットワーク セキュリティが高まる

[ExpressRoute][] シナリオの数と効果的ですだけでなく、移行します。

ご興味がおありでしたら、 詳細についてとしてください。 料金の詳細を参照してください、 [ExpressRoute ドキュメントの []][]します。

### Azure Import/Export サービス

Azure Import/Export サービスは、大規模なデータ (GB (ギガバイト) 単位) から巨大なデータ (TB (テラバイト) 単位) を Azure に転送するために設計されたデータ転送プロセスです。 これには、データをディスクに書き込む作業や、それらを Azure データ センターに送付する作業も含まれています。 ディスクの内容は、自動的に Azure Storage BLOB にロードされます。

インポートおよびエクスポート処理の概要は次のとおりです。

1. データを受け取る Azure BLOB ストレージ コンテナーを構成します
2. ローカル ストレージにデータをエクスポートします
2. Azure Import/Export ツールを使用して、データを 3.5 インチ SATA II/III ハード ディスク ドライブにコピーします。
3. Azure Import/Export ツールで生成されたジャーナル ファイルを指定して、Azure Import/Export サービスを使用し、インポート ジョブを作成します。
4. 指定された Azure データ センターにディスクを送付します
5. データが Azure BLOB ストレージ コンテナーに転送されます
6. PolyBase を使用して、SQLDW にデータをロードします

### [AZCopy[]][] utility

[AZCopy:operator[]][] ユーティリティは、Azure ストレージ Blob に転送されたデータを取得するための優れたツールです。 これは、小規模なデータ (MB 単位) から非常に大規模なデータ (GB 単位) の転送用に設計されています。 [AZCopy] は、Azure にデータを転送するときに、適切な回復力のあるスループットは、データ転送に最適な選択を提供するも設計されています。 一度転送したら、PolyBase を使用して SQL Data Warehouse にデータをロードできます。 プロセスの実行タスクを使用して、SSIS パッケージに AZCopy を組み込むこともできます。

AZCopy を使用するには、最初にダウンロードしてインストールする必要があります。 ある、 [運用バージョンの][] と [プレビュー バージョンの][] 利用します。

ファイル システムからファイルをアップロードするには、次のようなコマンドが必要です。

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

大まかなプロセスは、次のようになります。

1. データを受け取る Azure Storage BLOB コンテナーを構成します
2. ローカル ストレージにデータをエクスポートします
3. Azure BLOB ストレージ コンテナーにデータを AZCopy します
4. PolyBase を使用して SQL Data Warehouse にデータをロードします

詳細なドキュメントは利用可能な: [AZCopy:operator[]][]します。

## データ エクスポートの最適化

PolyBase によって生じる要件にエクスポートが準拠していることを確認するほかに、データのエクスポートを最適化してプロセスをさらに改善することもできます。
> [AZURE.NOTE] PolyBase 可能性は高くありません utf-8 形式であるデータの必要に応じて、データのエクスポートを実行するのに bcp を使用します。 bcp は、utf-8 へのデータ ファイルの出力をサポートしていません。 SSIS や ADF コピーのほうが、この種のデータ エクスポートを実行するのに非常に適しています。

### データ圧縮

PolyBase は、gzip 圧縮データを読み取ることができます。 gzip ファイルにデータを圧縮できる場合は、ネットワーク経由でプッシュされるデータ量が最小限に抑えられます。

### 複数のファイル

容量の大きいテーブルを複数のファイルに分割することは、エクスポートの高速化に役立つだけでなく、転送を再始動したり、Azure BLOB ストレージに保存した後にデータ全体を管理したりするのにも役立ちます。 PolyBase が持つ多数の便利な機能の 1 つとして、フォルダー内のすべてのファイルを読み取り、それを 1 つのテーブルとして処理する機能があります。 このため、各テーブルのファイルを独自のフォルダーに分離することをお勧めします。

PolyBase では、「再帰的なフォルダー トラバーサル」と呼ばれる機能もサポートされています。 この機能を使用して、エクスポートされたデータの編成をさらに拡張し、データ管理を改善できます。

PolyBase でデータの読み込みの詳細については、次を参照してください。 [polybase 使用した SQL Data Warehouse [] にデータを読み込む][]します。


## 次のステップ

移行の詳細について「 [SQL Data Warehouse [] ソリューションを移行][]します。
他の開発のヒントを参照してください。 [開発の概要 []][]します。








[azcopy]: ../storage/storage-use-azcopy.md 
[adf copy]: ../data-factory/data-factory-copy-activity.md 
[adf copy examples]: ../data-factory/data-factory-copy-activity-examples.md 
[development overview]: sql-data-warehouse-develop-overview.md 
[migrate your solution to sql data warehouse]: sql-data-warehouse-overview-migrate.md 
[sql data warehouse development overview]: sql-data-warehouse-overview-develop.md 
[use bcp to load data into sql data warehouse]: sql-data-warehouse-load-with-bcp.md 
[use polybase to load data into sql data warehouse]: sql-data-warehouse-load-with-polybase.md 
[azure data factory]: http://azure.microsoft.com/services/data-factory/ 
[expressroute]: http://azure.microsoft.com/services/expressroute/ 
[expressroute documentation]: http://azure.microsoft.com/documentation/services/expressroute/ 
[production version]: http://aka.ms/downloadazcopy/ 
[preview version]: http://aka.ms/downloadazcopypr/ 
[ado.net destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx 
[ssis documentation]: https://msdn.microsoft.com/library/ms141026.aspx 

