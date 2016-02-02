<properties
    pageTitle="Azure SQL Database に支えられたモバイル サービスのスケーリング | Microsoft Azure"
    description="SQL Database を利用する Mobile Services で発生するスケーラビリティの問題を診断して解決する方法について説明します。"
    services="mobile-services"
    documentationCenter=""
    authors="lindydonna"
    manager="dwrede"
    editor="mollybos"/>


<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="donnam;ricksal"/>


# Azure SQL Database に支えられたモバイル サービスのスケーリング

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Azure Mobile Services を使用すると、SQL データベースにデータを格納するクラウド ホステッド バックエンドに接続するアプリケーションを非常に簡単に利用および構築できます。 アプリケーションの拡張に合わせて、サービス インスタンスをスケーリングするのは、ポータルでスケール設定を調整してコンピューティング容量やネットワーク容量を追加するのと同じくらい簡単です。 ただし、サービスが受ける負荷の増加に合わせて、サービスを支えている SQL データベースをスケーリングするには、先を見越した計画や監視が必要になります。 このドキュメントでは、SQL に基づいたモバイル サービスの高いパフォーマンスを維持するための一連のベスト プラクティスについて説明します。

このトピックは、次の基本的なセクションで構成されています。

1. [問題を診断します。](#Diagnosing)
2. [インデックスの作成](#Indexing)
3. [スキーマの設計](#Schema)
4. [クエリの設計](#Query)
5. [サービスのアーキテクチャ](#Architecture)
6. [高度なトラブルシューティング](#Advanced)

<a name="Diagnosing"></a>
## 問題の診断

モバイル サービスには、負荷の下で問題が発生したと思われる場合を確認するには、まず、 **ダッシュ ボード** [Azure クラシック ポータル] で、サービスのタブをクリックします。 ここでは、次の点について確認します。

- **API 呼び出し**や**アクティブなデバイス**の測定値など、使用量の測定値がクォータを超えていない
- **エンドポイントの監視**ステータスで、サービスが稼働していることが示されている (サービスが Standard 階層を使用し、エンドポイントの監視が有効になっている場合のみ利用できます)

上のどちらも当てはまらない場合は、*[スケール]* タブでスケール設定を調整することを検討します。 それでも問題が解決しない場合は、Azure SQL Database が問題の原因かどうかを調べます。 この後のセクションでは、問題がある可能性のある箇所を診断する方法をいくつか紹介します。

### 適切な SQL Database 階層の選択

アプリケーションのニーズに適した階層を確実に選択するには、利用できるさまざまなデータベース階層について理解することが重要です。 Azure SQL Database では、次の 3 つのサービス階層が提供されます。

- Basic
- Standard
- Premium


次に、データベースに適した階層を選択する際の推奨事項をいくつか示します。

- **Basic** - 開発時や、一度にデータベース クエリを 1 つだけ実行する小規模な運用サービスに使用する
- **Standard** - 同時に複数のデータベース クエリを実行する運用サービスに使用する
- **Premium** - 同時に多数のクエリを実行し、ピーク時の負荷が高く、すべての要求の待ち時間が短い必要がある大規模な運用サービスに使用する

各階層を使用する場合の詳細については、[新しいサービス階層を使用する理由] を参照してください。

### データベース メトリックの分析

さまざまなデータベース階層について説明したので、次は、階層内および階層間でのスケーリングについて判断を下す際に役立つデータベースのパフォーマンス メトリックについて説明します。

1. [Azure クラシック ポータル] を起動します。
2. [モバイル サービス] タブで、使用するサービスを選択します。
3. **[構成]** タブをクリックします。
4. **[データベースの設定]** セクションで、**SQL データベース**名を選択します。 ポータルの [Azure SQL データベース] タブに自動的に移動します。
5. 移動し、 **モニター** ] タブ
6. **[メトリックの追加]** ボタンを使用して、関連するメトリックを表示します。 次を含めます。
    - *CPU の割合* (Basic/Standard/Premium 階層でのみ使用できます)

    - *データ IO の割合* (Basic/Standard/Premium 階層でのみ使用できます)
    - *ログ IO の割合* (Basic/Standard/Premium 階層でのみ使用できます)
    - *ストレージ*
7. サービスに問題が発生していた期間のメトリックを調べます。

    ![Azure クラシック ポータル - SQL Database 指標][portalsqlmetrics]

いずれかのメトリックで使用率が長期間にわたって 80% を超えている場合は、パフォーマンスに問題がある可能性があります。 詳細については、データベースの使用率を理解することを参照してください。 [リソースの使用について](http://msdn.microsoft.com/library/azure/dn369873.aspx#Resource)します。

データベースの使用率が高いことをメトリックが示している場合は、最初の対応策として、**より高いサービス層にデータベースをスケールアップする**ことを検討します。 問題をすぐに解決するには、データベースの **[スケール]** タブを使用して、データベースをスケールアップすることを検討します。 スケールアップすると、請求金額が増えます。
![Azure クラシック ポータル - SQL Database スケール][portalsqlscale]

できるだけ早急に、次の追加の対応策について検討します。

- **データベースを調整する。**
  多くの場合、データベースを最適化することで、データベースの使用率を低減させ、より高い階層にスケーリングすることを回避できます。
- **サービス アーキテクチャを検討する。**
  時間の経過と共にサービスの負荷が均等に分散されなくなり、その一方で、大きな需要が "急増" することがよくあります。 データベースをスケールアップすると、需要の急増に対応できますが、需要が少ない期間は使用率が低下することになります。スケールアップする代わりに、サービス アーキテクチャを調整すると、多くの場合、そうした急増を避けることや、データベースにアクセスすることなく需要の急増に対応することができます。

このドキュメントの残りのセクションでは、これらの対応策の実装に役立つように調整されたガイダンスを紹介します。


### アラートの構成

多くの場合、事前の対策として、主要なデータベース メトリックのアラートを構成しておくと、リソースが枯渇した場合に時間をかけて対応することができます。

1. アラートを設定するデータベースの **[監視]** タブに移動します。
2. 前のセクションで説明したように、関連するメトリックを表示します。
3. アラートを設定するメトリックを選択し、**[ルールの追加]** を選択します。

    ![Azure Management Portal - SQL Alert][portalsqladdalert]

4. アラートの名前と説明を指定します。
    ![Azure Management Portal - SQL Alert Name and Description][portalsqladdalert2]

5. アラートのしきい値として使用する値を指定します。 対応時間を確保するために、**80%** を使用することを検討してください。 また、積極的に監視する電子メール アドレスを必ず指定します。

    ![Azure Management Portal - SQL Alert Threshold and Email][portalsqladdalert3]


SQL の問題の診断の詳細については、次を参照してください。 [高度な診断](#AdvancedDiagnosing) このドキュメントの下部にあります。

<a name="Indexing"></a>
## インデックス作成

クエリのパフォーマンスに問題が見られるようになったら、まずインデックスの設計を調べる必要があります。 インデックスは、SQL エンジンがクエリを実行する方法に直接影響するため、重要です。


たとえば、特定のフィールドで要素を検索する必要があることが多い場合は、その列のインデックスを追加することを検討する必要があります。 そうしないと、SQL エンジンは強制的にテーブル スキャンを実行し、各物理レコード (少なくともクエリ列) を読み取ります。また、レコードがディスク上でかなり分散する可能性があります。

そのため、特定の列に対して WHERE または JOIN ステートメントを記述することが多い場合は、必ずそれらの列のインデックスを作成する必要があります。 セクションを参照して [インデックスの作成](#CreatingIndexes) の詳細。

インデックスが非常に効率が良く、テーブル スキャンが非常に効率が悪いなら、念のために、テーブルのすべての列にインデックスを作成しておく必要があるのでしょうか。 手短に言えば、"おそらくありません"。 インデックスは領域を占有し、オーバーヘッドを伴います。テーブルにデータが挿入されるたびに、インデックス付きの各列のインデックス構造を更新する必要があります。 列インデックスを選択する方法のガイドラインについては、以下を参照してください。

### インデックスの設計ガイドライン

既に説明したように、インデックス自体、パフォーマンスとストレージのオーバーヘッドの両方の点でコストがかかる可能性があるため、1 つのテーブルに複数のインデックスを追加することが常に良いとは限りません。

#### クエリの考慮事項

- 以下のデータベースの考慮事項とのバランスを取ったうえで、述部 (WHERE 句など) や結合条件で頻繁に使用する列にインデックスを追加することを検討します。
- 複数のクエリを使用して同じ行を更新する代わりに、1 つのステートメントでできるだけ多くの行を挿入または変更するクエリを記述します。 ステートメントが 1 つだけの場合、データベース エンジンはインデックスの管理方法をより適切に最適化することができます。

#### データベースの考慮事項

テーブルのインデックスの数が多いと、INSERT、UPDATE、DELETE、および MERGE ステートメントのパフォーマンスに影響します。テーブル内のデータを変更する際に、すべてのインデックスを適切に調整する必要があるためです。

- **頻繁に更新される**テーブルでは、頻繁に更新される列にインデックスを作成しないでください。
- **頻繁に更新されない**が、データが大量にあるテーブルでは、たくさんのインデックスを使用します。 そうすると、クエリ オプティマイザーがより多くの選択肢の中から最適なアクセス方法を見つけることができるため、データを変更しないクエリ (SELECT ステートメントなど) のパフォーマンスを向上させることができます。

小さなテーブルにインデックスを作成すると、単純なテーブル スキャンを実行するよりも、クエリ オプティマイザーがインデックスを走査してデータを検索する時間の方が長くなることがあるため、最適でない場合があります。 そのため、小さなテーブルのインデックスはまったく使用されない場合がありますが、それでもテーブル内のデータが変更されたときに管理する必要があります。


<a name="CreatingIndexes"></a>
### インデックスの作成

#### JavaScript バックエンド

JavaScript バックエンドで列にインデックスを設定するには、次の手順を実行します。

1. [Azure クラシック ポータル] で、モバイル サービスを開きます。
2. **[データ]** タブをクリックします。
3. 変更するテーブルを選択します。
4. **[列]** タブをクリックします。
5. 列を選択します。 コマンド バーで、**[インデックスの設定]** をクリックします。

    ![Mobile Services Portal - Set Index][setindexjavascriptportal]

このビュー内でインデックスを削除することもできます。

#### .NET バックエンド

Entity Framework でインデックスを定義するには、属性を使用して `[Index]` フィールドのインデックスを作成します。 次に例を示します。

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
    
        [Index]
        public bool Complete { get; set; }
    }

インデックスの詳細については、次を参照してください。 [Entity Framework の [] のインデックス注釈][]します。 ヒント インデックスの最適化については、次を参照してください。 [高度なインデックス](#AdvancedIndexing) このドキュメントの下部にあります。

<a name="Schema"></a>
## スキーマの設計

ここでは、SQL データベースのスキーマに変換される、オブジェクトのデータ型を選択する際に注意する必要のある問題についていくつか説明します。 SQL にはさまざまなデータ型のインデックス作成とストレージを処理するために最適化されたカスタムの方法が用意されているため、多くの場合、スキーマを調整すると、パフォーマンスを大幅に向上させることができます。

- **提供された ID 列を使用する。** すべてのモバイル サービス テーブルには、主キーとして構成された既定の ID 列が用意されており、その列にインデックスが設定されています。 追加の ID 列を作成する必要はありません。
- **モデルの適切なデータ型を使用する。**モデルの特定のプロパティが数値またはブール値であることがわかっている場合は、文字列ではなく、モデルに従って定義します。 JavaScript バックエンドで使用してリテラルなど `true` の代わりに `"true"` と `5` の代わりに `「5」`します。 .NET バックエンドで使用して、 `int` と `bool` 型モデルのプロパティを宣言するとします。 そうすると、SQL がこれらの型に適したスキーマを作成でき、クエリの効率が向上します。

<a name="Query"></a>
## クエリの設計

データベースを照会する際に考慮する必要があるガイドラインを次に示します。

- **常にデータベースで結合操作を実行する。**共通のフィールドを共有している複数のテーブルのレコードをつなげる必要がよくあります (*結合*とも呼ばれます)。 この操作は、両方のテーブルからすべてのエンティティを取得し、そのすべてを反復処理する場合があるため、適切に実行しないと、効率が悪くなることがあります。 このような操作はデータベース自体に任せるのが最適ですが、誤ってクライアントまたはモバイル サービス コードで実行することがあります。
    - アプリケーション コードで結合を実行しない
    - モバイル サービス コードで結合を実行しない。 JavaScript バックエンドを使用する場合に留意する、 [テーブル オブジェクト](http://msdn.microsoft.com/library/windowsazure/jj554210.aspx) 結合が処理されません。 使用してください、 [mssql オブジェクト](http://msdn.microsoft.com/library/windowsazure/jj554212.aspx) 直接結合が、データベースで実行されるようにします。 詳細については、次を参照してください。 [リレーショナル テーブルを結合](mobile-services-how-to-use-server-scripts.md#joins)します。 .NET バックエンドを使用し、LINQ 経由で照会する場合は、Entity Framework によってデータベース レベルで結合が自動的に処理されます。
- **ページングを実装する。**データベースを照会すると、大量のレコードがクライアントに返されることがあります。 操作のサイズと待機時間を最小限に抑えるには、ページングを実装することを検討します。

    - 既定では、モバイル サービスにより、すべての受信クエリのページ サイズが 50 に制限されるため、手動で要求できるレコードは最大 1,000 件になります。 For more information, see "Return data in pages" for [Windows Store](mobile-services-windows-dotnet-how-to-use-client-library.md#paging), [iOS](mobile-services-ios-how-to-use-client-library.md#paging), [Android](mobile-services-android-how-to-use-client-library.md#paging), [HTML/JavaScript](mobile-services-html-how-to-use-client-library#paging), and [Xamarin](partner-xamarin-mobile-services-how-to-use-client-library.md#paging).
    - モバイル サービス コードから実行するクエリには、既定のページ サイズがありません。 アプリケーションにページングを実装していない場合だけでなく、防衛手段としても、クエリに既定の制限を適用することを検討してください。 JavaScript バックエンドで使用して、 **かかる** 演算子で、 [クエリ オブジェクト](http://msdn.microsoft.com/library/azure/jj613353.aspx)します。 .NET バックエンドを使用する場合は、LINQ クエリの一部として [Take メソッド] の使用を検討します。


クエリ プランを分析する方法など、クエリの設計の改善の詳細については、次を参照してください。 [高度なクエリ設計](#AdvancedQuery) このドキュメントの下部にあります。

<a name="Architecture"></a>
## サービス アーキテクチャ

すべての顧客にアプリケーションの新しいコンテンツのチェックを求めるプッシュ通知を送信しようとしているとします。 顧客が通知をタップすると、アプリケーションが起動し、おそらくモバイル サービスへの呼び出しと SQL データベースに対するクエリの実行がトリガーされます。 何百万人もの顧客がわずか数分の間にこの操作を実行するため、SQL の負荷が急増し、アプリケーションの安定状態の負荷よりもはるかに高くなる可能性があります。 急増時にアプリケーションを高い SQL 階層にスケーリングし、その後で元に戻すことで、この状況に対応することができます。ただし、その解決方法は、手動による介入が必要なうええに、コストの増加が伴います。 モバイル サービス アーキテクチャを頻繁に微調整することで、クライアントによって引き起こされる SQL データベースに対する負荷の大部分のバランスを取り、問題のある需要の急増を削減することができます。 これらの変更は、多くの場合、顧客の操作性にほとんど影響を与えることなく、容易に実装することができます。 次に例をいくつか示します。

- **負荷を時間的に分散する。**需要の急増を引き起こすと思われる特定のイベント (プッシュ通知のブロードキャストなど) のタイミングが変更可能なときに、それらのイベントのタイミングを制御する場合は、イベントを時間的に分散することを検討します。 前の例では、アプリケーションの顧客にとって、新しいアプリケーションのコンテンツの通知が、ほぼ同時ではなく、1 日の間に複数のバッチに分けて送信されることはおそらく許容できます。 顧客をグループに分け、各バッチに時差配信できるようにすることを検討します。 Notification Hubs を使用する場合は、バッチを追跡するための追加のタグを適用し、そのタグにプッシュ通知を配信すると、この方法を簡単に実装できます。 タグの詳細については、次を参照してください。 [通知ハブを使用したニュース速報の送信に](../notification-hubs-windows-store-dotnet-send-breaking-news.md)します。
- **適切であれば、BLOB とテーブル ストレージを使用する。**多くの場合、顧客が急増時に表示するコンテンツはほとんど静的であり、そのコンテンツにリレーショナル クエリ機能が必要な可能性が低いため、SQL データベースに格納する必要はありません。 その場合は、Blob または Table Storage にコンテンツを格納することを検討します。 Blob Storage のパブリック BLOB には、デバイスから直接アクセスできます。 セキュリティで保護された方法で BLOB にアクセスしたり、テーブル ストレージを使用したりするには、ストレージ アクセス キーを保護するために、Mobile Services カスタム API を使用する必要があります。 詳細については、次を参照してください。 [Mobile Services を使用した Azure ストレージにイメージのアップロード](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)します。
- **メモリ内キャッシュを使用する。** 別の方法として、よくアクセスされるメモリ内キャッシュでのトラフィックの急増時になどのデータを格納する [Azure Cache](http://azure.microsoft.com/services/cache/)します。 この方法では、受け取った要求に対して、データベースを繰り返し照会することなく、メモリから必要な情報を取得することができます。

<a name="Advanced"></a>
## 高度なトラブルシューティング

このセクションでは、これまでの方法では十分に問題に対処できない場合に有用な、より高度な診断タスクについて説明します。

### 前提条件

このセクションの一部の診断タスクを実行するには、**SQL Server Management Studio** などの SQL データベース管理ツールや **Azure クラシック ポータル**に組み込まれている管理機能にアクセスできる必要があります。

SQL Server Management Studio は無料の Windows アプリケーションで、非常に高度な機能を備えています。 (たとえば、Mac を使用している場合など) の Windows コンピューターにアクセスしない場合は、ように、Azure の仮想マシンをプロビジョニングことを検討してください [Windows Server を実行している仮想マシンを作成する](../virtual-machines-windows-tutorial.md) をリモートで接続するとします。 VM の使用目的が主に SQL Server Management Studio を実行することである場合は、**Basic A0** (以前の "XS") インスタンスで十分です。

Azure クラシック ポータルには管理機能が組み込まれています。機能は限定的ですが、ローカルにインストールすることなく利用できます。

次の手順では、モバイル サービスを支えている SQL データベースの接続情報を取得し、2 つのツールのどちらかを使用してその SQL データベースに接続する方法を説明します。 どちらでも好きなツールを使用できます。

#### SQL 接続情報の取得

1. [Azure クラシック ポータル] を起動します。
2. [モバイル サービス] タブで、使用するサービスを選択します。
3. **[構成]** タブをクリックします。
4. **[データベースの設定]** セクションで、**SQL データベース**名を選択します。 ポータルの [Azure SQL データベース] タブに自動的に移動します。
5. [**この IP アドレス用に Azure ファイアウォール ルールを設定する**] を選択します。
6. **[データベースに接続する]** セクションに表示されたサーバー アドレス (たとえば、*mcml4otbb9.database.windows.net*) を書き留めます。

#### SQL Server Management Studio

1. に移動 [SQL Server express Edition - ページ](http://www.microsoft.com/server-cloud/products/sql-server-editions/sql-server-express.aspx)
2. **SQL Server Management Studio** のセクションを見つけ、その下の **[ダウンロード]** ボタンをクリックします。
3. セットアップ手順を実行して、アプリケーションを正常に実行できるようにします。

    ![SQL Server Management Studio][ssms]

4. **[サーバーへの接続]** ダイアログで次の値を入力します。
    - サーバー名: *先ほど取得したサーバー アドレス*
    - 認証: *SQL Server 認証*
    - ログイン: *サーバーの作成時に選択したログイン*
    - パスワード: *サーバーの作成時に選択したパスワード*
5. これで接続されます。

#### SQL Database の管理ポータル

1. データベースの [Azure SQL データベース] タブで、**[管理]** ボタンをクリックします。
2. 次の値で接続を構成します。
    - サーバー: *事前に適切な値に設定*
    - データベース: *空白のまま*
    - ユーザー名: *サーバーの作成時に選択したログイン*
    - パスワード: *サーバーの作成時に選択したパスワード*
3. これで接続されます。

    ![Azure クラシック ポータル - SQL Database][portalsqlmanagement]

<a name="AdvancedDiagnosing" />
### 高度な診断

診断タスクの多くは、**Azure クラシック ポータル**で簡単に実行できます。ただし、いくつかの高度な診断タスクは、**SQL Server Management Studio** または **SQL Database の管理ポータル**でのみ実行できます。 ここでは、動的管理ビュー (データベースに関する診断情報が自動的に設定される一連のビュー) を活用します。 このセクションでは、さまざまなメトリックを調べるためにこれらのビューに対して実行できる一連のクエリを紹介します。 詳細については、次を参照してください。 [監視 SQL データベースを使用して動的管理ビューの []][]します。

SQL Server Management Studio で前のセクションの手順を完了して、データベースに接続したら、**オブジェクト エクスプローラー**でデータベースを選択します。 **[ビュー]**、**[システム ビュー]** の順に展開すると、管理ビューの一覧が表示されます。 以下のクエリを実行するには、**オブジェクト エクスプローラー**でデータベースを選択している状態で、**[新しいクエリ]** を選択し、クエリを貼り付け、**[実行]** を選択します。

![SQL Server management Studio - dynamic management views][ssmsdmvs]

代わりに SQL Database の管理ポータルを使用する場合は、まずデータベースを選択し、**[新しいクエリ]** を選択します。

![SQL Database Management Portal - new query][portalsqlmanagementnewquery]

以下のいずれかのクエリを実行するには、そのクエリをウィンドウに貼り付け、**[実行]** を選択します。

![SQL Database Management Portal - run query][portalsqlmanagementrunquery]

#### 高度なメトリック

Basic、Standard、および Premium 階層を使用している場合は、管理ポータルで特定のメトリックをすぐに利用できます。 これらと他のメトリックを使用して取得する簡単である、* *[sys.resource \_stats](http://msdn.microsoft.com/library/dn269979.aspx)* * 階層に関係なく、管理ビューを使用しています。 次のクエリについて考えてみましょう。

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'todoitem_db'
    ORDER BY start_time DESC

> [AZURE.NOTE]
> このクエリを実行してください。、 **マスター** サーバーで、データベース、 **sys.resource \_stats** ビューはデータベースに存在するだけです。

結果には、CPU (階層の制限の割合 (%))、ストレージ (MB)、物理データの読み取り (階層の制限の割合 (%))、ログの書き込み (階層の制限の割合 (%))、メモリ (階層の制限の割合 (%))、worker 数、セッション数などの有用なメトリックが含まれます。

#### SQL の接続イベント

* *[Sys.event \_log](http://msdn.microsoft.com/library/azure/jj819229.aspx)* * ビューには、接続関連のイベントの詳細が含まれています。

    select * from sys.event_log
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [AZURE.NOTE]
> このクエリを実行してください。、 **マスター** サーバーで、データベース、 **sys.event \_log** ビューはデータベースに存在するだけです。

<a name="AdvancedIndexing" ></a>
### 高度なインデックス作成

テーブルまたはビューには、次の種類のインデックスを含めることができます。

- **クラスター化**。 クラスター化インデックスでは、レコードをディスクに物理的に格納する方法を指定します。 データ行自体を並べ替えることができる順序は 1 つだけであるため、クラスター化インデックスはテーブルごとに 1 つだけ存在する必要があります。

- **非クラスター化**。 非クラスター化インデックスは、データ行とは別に保存され、インデックス値に基づいた検索に使用されます。 テーブルのすべての非クラスター化インデックスでは、検索キーとしてクラスター化インデックスのキー値を使用します。

現実の世界にたとえるには、本や技術マニュアルを考えてみてください。 各ページの内容がレコード、ページ番号がクラスター化インデックス、本の末尾の索引が非クラスター化インデックスです。 索引の各エントリはページ番号 (クラスター化インデックス) を指しています。
> [AZURE.NOTE]
> 既定では、Azure Mobile Services の JavaScript バックエンドは設定 **\_createdAt** クラスター化インデックスとします。 この列を削除する場合、または別のクラスター化インデックスの場合に必ず従って、 [インデックスのデザイン ガイドラインをクラスター化された](#ClusteredIndexes) 以下です。 クラスは、.NET バックエンドで `EntityData` 定義 `CreatedAt` 注釈を使用してクラスター化インデックスとして `[Index(IsClustered = true)]`します。

<a name="ClusteredIndexes"></a>
#### クラスター化インデックスの設計ガイドライン

すべてのテーブルには、次の特性を備えた列 (複合キーの場合は複数の列) にクラスター化インデックスを設定する必要があります。

- 狭い - 小さなデータ型を使用するかは、 [複合キー ][primary and foreign key constraints] の狭い列の数が少ない
- 一意またはほぼ一意
- 静的 - 値が頻繁に変更されない
- 増え続ける
- (オプション) 固定長
- (オプション) null でない

**狭い**という特性の理由は、クラスター化インデックスのキー値が、テーブルの他のすべてのインデックスで検索キーとして使用されるためです。 本の末尾の索引の例では、クラスター化インデックスはページ番号であり、小さい数値です。 代わりに章のタイトルがクラスター化インデックスに含まれている場合は、キー値が (章名、ページ番号) になるため、索引自体がかなり長くなります。

レコードの物理的な位置を管理する必要性 (つまり、レコードを物理的に移動すること、またはレコードが格納されているページを分割してストレージを断片化させてしまうこと) を回避するには、キーが**静的**で**増え続ける**必要があります。

クラスター化インデックスは、次のようなクエリで非常に重要になります。

- BETWEEN、>、>=、<、<= などの演算子を使用して一定範囲の値を返す。
    - クラスター化インデックスを使用して、最初の値を持つ行が見つかったら、後続のインデックス値を持つ行が物理的に隣接していることが保証されます。
- JOIN 句を使用する (通常、これらは外部キー列です)。
- ORDER BY または GROUP BY 句を使用する。
    - ORDER BY または GROUP BY 句に指定した列にインデックスが設定されている場合は、行が既に並べ替えられているため、データベース エンジンがデータを並べ替える必要がなくなることがあります。 これにより、クエリのパフォーマンスが向上します。

#### Entity Framework でのクラスター化インデックスの作成

Entity Framework を使用して .NET バックエンドでは、クラスター化インデックスを設定するには、設定、 `IsClustered` 、注釈のプロパティです。 たとえば、これは、定義 `CreatedAt` で `Microsoft.WindowsAzure.Mobile.Service.EntityData`:

    [Index(IsClustered = true)]
    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    [TableColumnAttribute(TableColumnType.CreatedAt)]
    public DateTimeOffset? CreatedAt { get; set; }

#### データベース スキーマでのインデックスの作成

JavaScript バックエンドでは、SQL Server Management Studio または Azure SQL Database ポータルのどちらかを使用して、データベース スキーマを直接変更することでのみ、テーブルのクラスター化インデックスを変更することができます。

以下のガイドでは、データベース スキーマを直接変更して、クラスター化インデックスまたは非クラスター化インデックスを設定する方法について説明しています。

- [作成と変更の主キー制約][]
- [の非クラスター化インデックスを作成します。][]
- [クラスター化インデックスを作成します。][]
- [の一意のインデックスを作成します。][]

#### 上位 N 個の不足しているインデックスの検索

個々のクエリのリソース使用量に関するより詳細な情報を返したり、追加する必要があるインデックスに関するヒューリスティックを提供したりする、動的管理ビューに対する SQL クエリを記述できます。 次のクエリでは、ユーザー クエリで最も高い累積のパフォーマンス向上を見込むことができる、上位 10 個の不足しているインデックスを降順で特定します。

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

次の例のクエリでは、これらのテーブルに対して結合を実行して、不足している各インデックスを構成する列の一覧を取得し、指定されたインデックスを検討する必要があるかどうかを判断するために "index advantage (インデックスのメリット)" を計算します。

    SELECT * from
    (
        SELECT
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

詳細については、次を参照してください。 [監視 SQL データベースを使用して動的管理ビューの []][] と [ないインデックスの動的管理ビューの []][]します。

<a name="AdvancedQuery" ></a>
### 高度なクエリの設計

多くの場合、データベースにとって最も負荷の高いクエリを診断することは簡単ではありません。


#### 上位 N 個のクエリの検索

次の例では、平均 CPU 時間の上位 5 個のクエリに関する情報を返します。 この例では、論理的に等価なクエリがリソースの累計消費量ごとにグループ化されるように、クエリ ハッシュに応じてクエリを集計します。

    SELECT TOP 5 query_stats.query_hash AS "Query Hash",
        SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
        MIN(query_stats.statement_text) AS "Statement Text"
    FROM
        (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
        ((CASE statement_end_offset
            WHEN -1 THEN DATALENGTH(st.text)
            ELSE QS.statement_end_offset END
                - QS.statement_start_offset)/2) + 1) AS statement_text
         FROM sys.dm_exec_query_stats AS QS
         CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
    GROUP BY query_stats.query_hash
    ORDER BY 2 DESC;

詳細については、次を参照してください。 [監視 SQL データベースを使用して動的管理ビューの []][]します。 **SQL Database の管理ポータル**では、クエリを実行できるだけでなく、データベースの **[概要]**、**[クエリ パフォーマンス]** の順に選択することで、このデータをすぐに表示できます。

![SQL Database Management Portal - query performance][portalsqlmanagementqueryperformance]

#### クエリ プランの分析

負荷の高いクエリを特定した場合や、新しいクエリを使用したコードをデプロイしようとしており、そのパフォーマンスを調べる必要がある場合は、ツールを使用して、**クエリ プラン**を分析できます。 クエリ プランでは、特定の SQL クエリを実行したときに CPU 時間と IO リソースの大部分を占有する操作を確認できます。 **SQL Server Management Studio** でクエリ プランを分析するには、強調表示されているツール バーのボタンを使用します。

![SQL Server Management Studio - query plan][ssmsqueryplan]

**SQL Database の管理ポータル**でクエリ プランを分析するには、強調表示されているツール バーのボタンを使用します。

![SQL Database Management Portal - query plan][portalsqlmanagementqueryplan]

## 関連項目

- [Azure SQL データベースのドキュメントの][]
- [Azure SQL データベースのパフォーマンスとスケーリング][]
- [トラブルシューティング Azure SQL データベースの][]

### インデックス作成

- [インデックスの基本 [][]
- [一般的なインデックスのデザインのガイドライン][]
- [一意のインデックスのデザインのガイドライン][]
- [クラスター化インデックスのデザインのガイドライン][]
- [主キーと外部キー制約][]
- [そのキーのコストはどのぐらいですか。[]][]

### Entity Framework

- [エンティティのパフォーマンスに関する考慮事項 Framework 5 の][]
- [コードの最初のデータ注釈][]









[ssms]: ./media/mobile-services-sql-scale-guidance/1.png 
[portalsqlmanagement]: ./media/mobile-services-sql-scale-guidance/2.png 
[portalsqlmetrics]: ./media/mobile-services-sql-scale-guidance/3.png 
[portalsqlscale]: ./media/mobile-services-sql-scale-guidance/4.png 
[portalsqladdalert]: ./media/mobile-services-sql-scale-guidance/5.png 
[portalsqladdalert2]: ./media/mobile-services-sql-scale-guidance/6.png 
[portalsqladdalert3]: ./media/mobile-services-sql-scale-guidance/7.png 
[setindexjavascriptportal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png 
[ssmsdmvs]: ./media/mobile-services-sql-scale-guidance/8.png 
[portalsqlmanagementnewquery]: ./media/mobile-services-sql-scale-guidance/9.png 
[portalsqlmanagementrunquery]: ./media/mobile-services-sql-scale-guidance/10.png 
[portalsqlmanagementqueryperformance]: ./media/mobile-services-sql-scale-guidance/11.png 
[ssmsqueryplan]: ./media/mobile-services-sql-scale-guidance/12.png 
[portalsqlmanagementqueryplan]: ./media/mobile-services-sql-scale-guidance/13.png 
[azure classic portal]: http://manage.windowsazure.com 
[azure sql database documentation]: http://azure.microsoft.com/documentation/services/sql-database/ 
[managing sql database using sql server management studio]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409 
[monitoring sql database using dynamic management views]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409 
[azure sql database performance and scaling]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409 
[troubleshooting azure sql database]: http://msdn.microsoft.com/library/azure/ee730906.aspx 
[reasons to use the new service tiers]: http://msdn.microsoft.com/library/azure/dn369873.aspx#Reasons 
[take method]: http://msdn.microsoft.com/library/vstudio/bb503062(v=vs.110).aspx 
[creating and modifying primary key constraints]: http://technet.microsoft.com/library/ms181043(v=sql.105).aspx 
[create clustered indexes]: http://technet.microsoft.com/library/ms186342(v=sql.120).aspx 
[create unique indexes]: http://technet.microsoft.com/library/ms187019.aspx 
[create nonclustered indexes]: http://technet.microsoft.com/library/ms189280.aspx 
[primary and foreign key constraints]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx 
[index basics]: http://technet.microsoft.com/library/ms190457(v=sql.105).aspx 
[general index design guidelines]: http://technet.microsoft.com/library/ms191195(v=sql.105).aspx 
[unique index design guidelines]: http://technet.microsoft.com/library/ms187019(v=sql.105).aspx 
[clustered index design guidelines]: http://technet.microsoft.com/library/ms190639(v=sql.105).aspx 
[missing index dynamic management views]: http://technet.microsoft.com/library/ms345421.aspx 
[performance considerations for entity framework 5]: http://msdn.microsoft.com/data/hh949853 
[code first data annotations]: http://msdn.microsoft.com/data/jj591583.aspx 
[index annotations in entity framework]: http://msdn.microsoft.com/data/jj591583.aspx#Index 
[how much does that key cost?]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/ 

