<properties 
    pageTitle="JSON データベースである DocumentDB の概要 | Microsoft Azure" 
    description="NoSQL JSON データベースである Azure DocumentDB について説明します。このドキュメント データベースは、ビッグ データ、エラスティックな拡張性、および高可用性用に構築されています。" 
    keywords="json database, document database"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/18/2015" 
    ms.author="mimig"/>


# DocumentDB の概要: NoSQL JSON Database

DocumentDB とは、簡単に拡大縮小でき、高い可用性を必要とする、JSON データを扱う、ビッグ データ ソリューション用の NoSQL ドキュメント データベースです。

この JSON データベースを簡単に理解して動作を確認するには、次の 3 つの手順に従います。

1. 2 分間のを見る [DocumentDB は何ですか?](http://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) ビデオで、DocumentDB を使用する利点について説明します。
2. 次の 3 つの分を見る [Azure で作成する DocumentDB](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) Azure ポータルを使用して DocumentDB を導入する方法をハイライトするビデオ、します。
3. 参照してください、 [クエリのプレイ グラウンド](http://www.documentdb.com/sql/demo), 、DocumentDB で使用できる機能のクエリを実行する豊富な詳細については、さまざまなアクティビティを操作することができます。 次に [サンドボックス] タブに移動して、独自のカスタム SQL クエリを実行し、DocumentDB を試してみます。

その後でこの記事に戻ると、より詳しく調べることができ、次の質問の答えを見つけることができます。

-   [DocumentDB の定義とどのような値にもたらすクラウドやモバイル アプリケーションですか。](#what-is-azure-documentdb)
-   [DocumentDB のデータの管理方法とアクセス方法でしょうか。](#data-management)
-   [DocumentDB を使用したアプリケーションの開発方法](#develop)
-   [DocumentDB アプリケーションを構築する手順とは](#next-steps)

## Azure DocumentDB とは

最近のアプリケーションは膨大なデータを生成し、取り込み、そしてすばやく応答を返します。 そうしたアプリケーションはきわめて速いペースで進化しており、その基盤となるデータ スキーマにも同じことがいえます。 アプリケーション データ モデルと非構造化データ フィードを高速に反復処理する能力を保ちつつ、データを保存、処理するためのシンプルで高速、かつスケーラブルなソリューションが求められます。そのような状況の中で、スキーマが不要な NoSQL ドキュメント データベースを選ぶ開発者が増えてきました。 しかし、スキーマが不要なデータベースの多くは複雑なクエリやトランザクション処理に対応しておらず、そのことが高度なデータ管理を困難にしています。 こういった機能を、 今日のアプリケーションのデータを扱う際にも利用できるようにするために、マイクロソフトは DocumentDB を開発しました。

DocumentDB は、最近のモバイル アプリケーションと Web アプリケーション向けに設計された完全にスキーマフリーの NoSQL ドキュメント データベース サービスで、 終始高速な読み取りと書き込み、スキーマの柔軟性、そして需要に応じてデータベースを容易にスケールアップ/ダウンできる能力を備えています。 インデックスを作成する JSON ドキュメントには、前提となるスキーマはなく、どのようなスキーマも不要です。 既定では、データベース内のすべてのドキュメントについてインデックスが自動的に作成されるため、スキーマや、セカンダリ インデックスの作成は不要です。 DocumentDB は、SQL 言語を使用した複雑なアドホック クエリに対応し、明確に定義された一貫性レベルをサポートしています。また、JavaScript 言語を使ったプログラミングが可能となっており、ストアド プロシージャ、トリガー、UDF に馴染みのあるプログラミング モデルを使って複数ドキュメントにわたるトランザクション処理を実現することができます。

DocumentDB は、JSON データベースとして JSON ドキュメントをネイティブ サポートしているため、アプリケーション スキーマを容易に反復処理することができます。 JSON と JavaScript という広く普及した技術を積極的に採用することで、アプリケーション定義オブジェクトとデータベース スキーマ間のミスマッチをなくしています。 JavaScript のネイティブ サポートには、開発者がデータベース トランザクションの中でデータベース エンジン内から直接アプリケーション ロジックを効率的に実行できるメリットもあります。

Azure DocumentDB が備えている主要な機能と利点は次のとおりです。

-   **馴染みのある SQL 構文を使用したアドホック クエリ:** 多種多様な JSON ドキュメントを DocumentDB に格納し、馴染みのある SQL 構文を使用して照会できます。 DocumentDB は、同時実行性の高い、ロックを用いないログ構造のインデックス作成技術を利用して、すべてのドキュメント コンテンツのインデックスを自動的に作成します。 そのため、スキーマのヒント、セカンダリ インデックス、ビューを指定せずに、豊富なリアルタイム クエリが可能となっています。 詳細について [DocumentDB のクエリ](documentdb-sql-query.md)します。

-   **データベース内で JavaScript を実行:** 標準の JavaScript を使用し、ストアド プロシージャ、トリガー、ユーザー定義関数 (UDF) としてアプリケーション ロジックを表現することができるため、 アプリケーション スキーマとデータベース スキーマ間のミスマッチに悩まされることなく、アプリケーション ロジックでデータを扱うことができます。 DocumentDB は、JavaScript アプリケーション ロジックを完全なトランザクションとしてデータベース エンジン内から直接実行できるようになっています。 JavaScript が深いレベルで統合されているため、INSERT、REPLACE、DELETE、SELECT の操作を分離されたトランザクションとして JavaScript プログラム内から実行することができます。 詳細について [DocumentDB サーバー側プログラミング](documentdb-programming.md)します。

-   **一貫性レベルをチューニング可能:** 明確に定義された 4 つの一貫性レベルの中からいずれかのレベルを選択し、一貫性とパフォーマンス間のトレードオフを最適な形で行うことができます。 DocumentDB では、クエリと読み取り操作に関して、Strong、Bounded-Staleness、Session、Eventual の 4 種類の一貫性レベルが利用できます。 きめ細かな一貫性レベルが明確に定義されていることによって、一貫性、可用性、
-   待機時間の最適なトレードオフを検討することができます。 詳細について [一貫性レベルを使用して、DocumentDB でのパフォーマンスと可用性を最大化する](documentdb-consistency-levels.md)します。

-   **完全管理:** データベースやコンピューター リソースを管理する手間がかかりません。 Microsoft Azure サービスは完全に管理されているため、仮想マシンの管理、ソフトウェアのデプロイと構成、複雑なデータ層のアップグレードを手作業で行う必要はありません。 すべてのデータベースは自動的にバックアップされ、局地的障害から保護されます。 DocumentDB アカウントは簡単に追加し、必要に応じて容量をプロビジョニングすることができます。データベースの運用と管理に煩わされることなく、アプリケーションに専念することが可能です。

-   **スループットとストレージのスケールを柔軟に調整:** アプリケーションのニーズに合わせて DocumentDB JSON データベースを簡単にスケールアップおよびスケールダウンできます。 SSD を使用したストレージとスループットの単位 (コレクション) が細かく規定されており、スケール調整は、それらを予約することによって行います。 アプリケーションの成長に合わせて単位を追加作成すれば、確実なパフォーマンスと共に、DocumentDB のスケールを臨機応変に拡張することができます。

-   **設計に込められたオープンな環境:** 既存のスキルやツールをそのまま活かすことができます。 DocumentDB に対するプログラミングは、シンプルで親しみやすく、新しいツールを導入する必要がないうえ、JSON や JavaScript のカスタム拡張機能への縛りもありません。 CRUD、クエリ、JavaScript 処理を含め、データベースのすべての機能には、単純な RESTful HTTP インターフェイスでアクセスすることができます。 DocumentDB は、既にあるフォーマット、言語、標準を積極的に採用すると共に、それを基盤として価値の高いデータベース機能を提供しています。

DocumentDB を使用すると、クエリ検索やトランザクション処理を必要とする多様なデータセットを格納することができます。 たとえば、双方向の Web アプリケーションやモバイル アプリケーションのユーザー データ、アプリケーションの JSON データの保存、検索、処理に利用することが可能です。 1 つのデータベースに JSON ドキュメントをいくつでも保存できることから、DocumentDB は、インターネットで動作するアプリケーションに最適です。

## <a name="data-management"></a>Azure DocumentDB のリソース

Azure DocumentDB では、明確に定義されたデータベース リソースによってデータが管理されます。 これらのリソースは、高可用性を確保するためにレプリケートされ、論理 URI によって一意にアドレス指定されます。 DocumentDB のすべてのリソースには、HTTP ベースのシンプルで RESTful なプログラミング モデルを適用することができます。

DocumentDB データベース アカウントは、Azure DocumentDB にアクセスできる一意の名前空間です。 データベース アカウントを作成するには、事前に Azure サブスクリプション (多様な Azure サービスにアクセスが可能) が必要です。

DocumentDB 内のリソースはいずれも、JSON ドキュメントとしてモデル化されて保存されます。 リソースはアイテム (メタデータを含んだ JSON ドキュメント) およびフィード (アイテムのコレクション) として管理されます。 一連のアイテムがそれぞれのフィード内に格納されます。

以下の図は、DocumentDB のリソース間の関係を示したものです。

![NoSQL JSON データベースである DocumentDB のリソース間の階層関係です][1]

データベース アカウントは、一連のデータベースから成ります。それぞれのデータベースには、複数のコレクションが含まれており、それぞれのコレクションに、ストアド プロシージャ、トリガー、UDF のほか、ドキュメントおよび関連する添付ファイルが含まれています。 また、データベースにはユーザーが関連付けられ、それぞれのユーザーには、他のさまざまなコレクション、ストアド プロシージャ、トリガー、UDF、ドキュメント、添付ファイルにアクセスするための一連のアクセス許可が関連付けられます。 データベース、ユーザー、アクセス許可、コレクションが、既知のスキーマを持ったシステム定義のリソースであるのに対し、ドキュメント、ストアド プロシージャ、トリガー、UDF、添付ファイルは、ユーザーが自由に定義できる JSON コンテンツを格納します。

## <a name="develop"></a> Azure DocumentDB の使用による開発

Azure DocumentDB が公開するリソースには、HTTP/HTTPS 要求機能を持つ任意の言語から REST API を呼び出すことでアクセスできます。 さらに、DocumentDB にはいくつかの主要な言語のプログラミング ライブラリも用意されています。 アドレスのキャッシュ、例外管理、自動再試行などに伴う細部の処理がライブラリ側で行われるため、Azure DocumentDB の操作が多くの点で単純化されます。 ライブラリは、次の言語およびプラットフォーム用が現在提供されています。

 ダウンロード| ドキュメント
--- | ---
 [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989)| [.NET ライブラリ](https://msdn.microsoft.com/library/azure/dn948556.aspx)
 [Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990)| [Node.js ライブラリ](http://dl.windowsazure.com/documentDB/nodedocs/)
 [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380)| [Java ライブラリ](http://dl.windowsazure.com/documentdb/javadoc/)
 [JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991)| [JavaScript ライブラリ](http://dl.windowsazure.com/documentDB/jsclientdocs/)
 該当なし| [サーバー側 JavaScript SDK](http://dl.windowsazure.com/documentDB/jsserverdocs/)
 [Python SDK](https://pypi.python.org/pypi/pydocumentdb)| [Python ライブラリ](http://dl.windowsazure.com/documentDB/pythondocs/)

DocumentDB には、作成、読み取り、更新、削除という基本的な操作以外にも、JSON ドキュメントを検索するための多彩な SQL クエリ インターフェイスが備わっているほか、JavaScript のアプリケーション ロジックをサーバー側でトランザクション実行する機能がサポートされています。 クエリとスクリプトの実行インターフェイスは、REST API に加え、あらゆるプラットフォーム ライブラリから利用できます。

### SQL クエリ

Azure DocumentDB では、JavaScript の型システムにマッチした SQL 言語と、豊富な階層クエリに対応した式を使用してドキュメントを照会することができます。 DocumentDB のクエリ言語は、JSON ドキュメントを照会するための、シンプルでありながら強力なインターフェイスとなっています。 この言語は、ANSI SQL の文法のサブセットをサポートしたうえで、深いレベルで JavaScript のオブジェクト、配列、オブジェクト生成、関数呼び出しとの統合が図られています。 DocumentDB のクエリ モデルでは、スキーマやインデックスのヒントを開発者が明示的に指定する必要がありません。

カスタム アプリケーション ロジックへの対応は、ユーザー定義関数 (UDF) を DocumentDB に登録して SQL クエリの中で参照するという、文法の拡張によって実現できます。 これらの UDF は JavaScript プログラムで記述し、データベース内から実行します。

.NET の開発者にとっても用意されています LINQ クエリ プロバイダーの一部として、 [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx)します。

### トランザクションと JavaScript の実行

DocumentDB では、アプリケーション ロジックを JavaScript だけで、名前付きのプログラムとして記述できます。 これらのプログラムは、コレクションに登録され、特定のコレクション内のドキュメントに対してデータベース操作を発行できるようになっています。 JavaScript は、トリガー、ストアド プロシージャ、ユーザー定義関数のいずれかとして登録することができます。 トリガーとストアド プロシージャは、ドキュメントの作成、読み取り、更新、削除を実行できます。これに対し、ユーザー定義関数はクエリの一部として実行され、コレクションに対する書き込みアクセス権はありません。

DocumentDB における JavaScript は、Transact-SQL の後継として、リレーショナル データベース システムによって裏付けられた概念に沿って実行がモデル化されています。 すべての JavaScript ロジックは、スナップショット分離機能を使用し、現在参加している ACID トランザクション内で実行されます。 その実行中に JavaScript で例外がスローされた場合、トランザクション全体が中止されます。

## 次のステップ

既に、Azure アカウントがある場合を始めることができますで DocumentDB、 [Azure ポータル](https://portal.azure.com/#gallery/Microsoft.DocumentDB) によって [DocumentDB データベース アカウントを作成する](documentdb-create-account.md)します。

Azure アカウントをお持ちでない場合は、

- サインアップするため、 [Azure 無料評価版](https://azure.microsoft.com/pricing/free-trial/), 、30 日間と 200 ドルにすべての Azure サービスを提供します。
- MSDN サブスクリプションがある場合の資格が [12,500 円 1 か月あたりの無料の Azure クレジットの](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) すべての Azure サービスを使用します。

次に、詳細については準備ができたらを参照してください、 [ラーニング パス](http://azure.microsoft.com/documentation/learning-paths/documentdb/) を使用するすべてのラーニング リソースを移動します。



[1]: ./media/documentdb-introduction/json-database-resources1.png 

