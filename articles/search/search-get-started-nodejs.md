<properties
    pageTitle="NodeJS での Azure Search の使用 | Microsoft Azure | ホスト型クラウド検索サービス"
    description="プログラミング言語として NodeJS を使用して Azure でホスト型クラウド検索アプリケーションを作成する手順を示します。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="11/04/2015"
    ms.author="heidist"/>


# NodeJS での Azure Search の使用

検索エクスペリエンスとして Azure Search を使用するカスタム NodeJS 検索アプリケーションを作成する方法を説明します。 このチュートリアルでは、 [Azure Search サービス REST API](https://msdn.microsoft.com/library/dn798935.aspx) オブジェクトおよびこの演習で使用する操作を作成します。

使用して [NodeJS](https://nodejs.org) と NPM、 [Sublime Text 3](http://www.sublimetext.com/3), 、および Windows PowerShell を Windows 8.1 このコードを開発し、テストします。

このサンプルを実行する必要がありますでにサインアップできる Azure Search サービス、 [Azure Classic Portal](https://portal.azure.com)します。
> [AZURE.TIP] このチュートリアルのソース コードをダウンロード [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198)します。

## データについて

このサンプル アプリケーションからデータを使用して、 [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), 、ロードアイランドでフィルター処理、状態のデータセットのサイズを小さきます。 このデータを使用して、病院や学校などの目立つ建物および河川、湖沼、山などの地理的特徴を返す検索アプリケーションを作成します。

このアプリケーションで、 **DataIndexer** プログラムがビルドされ、インデックスを使用して、ロード、 [インデクサー](https://msdn.microsoft.com/library/azure/dn798918.aspx) コンストラクト、パブリック Azure SQL Database からフィルター処理された USGS データセットを取得します。 資格情報と接続プログラム コードでオンラインでのデータ ソースへの情報を提供します。 それ以上の構成は必要ありません。
> [AZURE.NOTE] このデータセットにフィルターを提供し、無料価格レベルのドキュメントを 10,000 件未満に制限しました。 標準レベルを使用する場合は、この制限は適用されません。 各価格レベルの容量についての詳細については、「 [制限および制約](search-limits-quotas-capacity.md)します。

## サービスの作成

1. サインイン [Azure クラシック ポータル](https://portal.azure.com)します。

2. ジャンプバーで、**[新規]** > **[データ + ストレージ]** > **[検索]** をクリックします。

     ![][1]

3. サービス名、価格レベル、リソース グループ、サブスクリプション、および場所を構成します。 これらの設定は必須であり、サービスがプロビジョニングされた後は変更できません。

     ![][2]

    - **[サービス名]** はスペースなし、15 文字以下の小文字で、一意である必要があります。 この名前は、Azure Search サービスのエンドポイントの一部になります。 参照してください [名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx) 名前付け規則の詳細についてです。

    - [**価格レベル**] では、容量と課金を決定します。 どちらのレベルも同じ機能を提供しますが、リソース レベルが異なります。

        - **Free**  は他のサブスクライバーと共有しているクラスターで実行します。 Free 版はチュートリアルを試用して概念実証コードを書くには十分な機能を提供しますが、運用アプリケーションには対応していません。 Free サービスは、通常は数分でデプロイできます。
        - **[Standard]** レベルは専用リソースで実行され、拡張性に優れています。 最初、標準サービスは 1 つのレプリカと 1 つのパーティションを使用してプロビジョニングされますが、サービスを作成した後で容量を調整することができます。 標準サービスをデプロイするには、通常は約 15 分かかります。

    - **リソース グループ**は、一般的な目的で使用するサービスとリソースのコンテナーです。 たとえば、Azure Search、Azure Websites、Azure BLOB ストレージを使用してカスタム検索アプリケーションを構築する場合は、リソース グループを作成することで、これらのサービスをポータル管理ページにまとめておくことができます。

    - **[サブスクリプション]** では、複数のサブスクリプションがある場合に、複数のサブスクリプションから選択できます。

    - **[場所]** はデータ センターのリージョンです。 現時点では、すべてのリソースは同じデータ センターで実行する必要があります。 複数のデータ センターにリソースを分散させることはできません。

4. **[作成]** をクリックしてサービスをプロビジョニングします。

ジャンプバーで、通知を確認します。 サービスが使用できるようになると、通知が表示されます。

<a id="sub-2"></a>
## Azure Search サービスのサービス名と API キーの取得

サービスを作成したら、URL を取得するポータルに戻り、または `api キー`します。 検索サービスに接続できることが必要、URL に加えて、 `api キー` 、呼び出しを認証します。

1. ジャンプ バーで **[ホーム]** をクリックし、Search サービスをクリックして、サービスのダッシュボードを開きます。

2. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。

    ![][3]

3. サービスの URL、管理キー、クエリ キーをコピーします。 後で config.js ファイルに追加するときにこれら 3 つがすべて必要になります。

## サンプル ファイルのダウンロード

次のいずれかの方法を使用してサンプルをダウンロードします。

1. 移動して [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198)します。
2. **[Download ZIP]** をクリックして .zip ファイルを保存した後、すべてのファイルをコンテナーに抽出します。

以降のすべてのファイル変更および実行ステートメントは、このフォルダー内のファイルに対して行われます。

または、パス ステートメントに GIT があれば、開ける PowerShell ウィンドウと種類 `git クローン https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git`

## Search サービスの URL と API キーでの config.js の更新

先にコピーした URL と API キーを使用し、構成ファイルで URL、管理キー、クエリ キーを指定します。

管理キーは、インデックスの作成または削除やドキュメントの読み込みなど、サービス操作に対する完全な制御を付与します。 これに対し、クエリ キーは読み取り専用の操作用であり、通常は、Azure Search に接続するクライアント アプリケーションによって使用されます。

このサンプルでは、クライアント アプリケーションではクエリ キーを使用するというベスト プラクティスを補強するためにクエリ キーを含めます。

次のスクリーン ショットは、**config.js** をテキスト エディターで開いたところです。実際の Search サービスで有効な値でファイルを更新する箇所がわかるように、関連するエントリを囲んであります。

![][5]


## サンプルのランタイム環境のホスト

このサンプルには HTTP サーバーが必要です。これは、npm を使用してグローバルにインストールできます。

PowerShell ウィンドウを使用して次のコマンドを実行します。

1. **package.json** ファイルを含むフォルダーに移動します。
2. 型 `npm インストール`します。
2. 型 `npm-g http サーバーをインストールする`します。

## インデックスの作成とアプリケーションの実行

1. 型 `npm 実行 indexDocuments`します。
2. 型 `npm ビルドを実行する`します。
3. 型 `npm 実行 start_server`します。
4. ブラウザーを直接 `http://localhost:8080/index.html`

## USGS データの検索

USGS データ セットには、ロードアイランド州に関連するレコードが含まれています。 検索ボックスが空の状態で **[Search]** をクリックすると、既定で、上位 50 のエントリが取得されます。

検索語句を入力すると、検索エンジンに処理するものが提供されます。 地域の名前を入力してみてください。 "Roger Williams" はロードアイランド州の最初の州知事でした。 多数の公園、建造物、および学校に彼の名前が付けられています。

![][9]

次のような語句も試すことができます。

- Pawtucket
- Pembroke
- goose +cape


## 次のステップ

これは、NodeJS と USGS データセットに基づく最初の Azure Search チュートリアルです。 カスタム ソリューションで使用できる他の検索機能を紹介できるように、時間をかけてこのチュートリアルを拡張する予定です。

Azure Search についての知識が既にある場合は、このサンプルを基にして、サジェスター (先行入力またはオートコンプリート クエリ)、フィルター、ファセット ナビゲーションなどを試すことができます。 また、件数を追加してドキュメントを一括処理することで検索結果の表示を改善し、ユーザーが結果をページ移動できるようにすることもできます。

Azure Search を初めて使用する場合は、 他のチュートリアルも試して、作成できるものについての理解を深めることをお勧めします。 参照してください、 [ドキュメント ページ](http://azure.microsoft.com/documentation/services/search/) を他のリソースを検索します。 内のリンクを表示することも、 [ビデオとチュートリアルの一覧](search-video-demo-tutorial-list.md) 多くの情報にアクセスします。



[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG 
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG 
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG 
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png 
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png 

