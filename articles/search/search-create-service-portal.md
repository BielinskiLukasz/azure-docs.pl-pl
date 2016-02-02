<properties
    pageTitle="ポータルでの Azure Search サービスの作成 | Microsoft Azure | ホスト型クラウド検索サービス"
    description="Azure クラシック ポータルを使用して、Free または Standard の Azure Search を既存のサブスクリプションに追加します。Azure Search は、カスタム アプリケーション向けのクラウド ホステッド検索サービスです。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="11/04/2015"
    ms.author="heidist"/>


# Azure クラシック ポータルでの Azure Search サービスの作成

Microsoft Azure Search は、検索機能をカスタム アプリケーションに埋め込むことができるホスト型クラウド検索サービスです。 Microsoft Azure Search には、検索エンジンと、検索データのストレージが用意されています。これは、Azure クラシック ポータル、.NET SDK、または REST API を使用してアクセスおよび管理できます。 主な機能には、オートコンプリート クエリ、あいまい一致、検索結果の強調表示、ファセット ナビゲーション、スコアリング プロファイル、多言語のサポートなどがあります。 Azure Search の機能の詳細についてを参照してください [Azure Search は](search-what-is-azure-search.md)します。

## Azure Search を無料でサブスクリプションに追加する

管理者は、共有サービスを選択する場合は無料で、専用のリソースを使用する場合は Standard 料金で、Azure Search を既存の Azure サブスクリプションに追加できます。

1. サインイン、 [Azure クラシック ポータル](https://portal.azure.com)します。

2. ジャンプバーで、**[新規]** > **[データ + ストレージ]** > **[検索]** をクリックします。

     ![][1]

3. サービス名、価格レベル、リソース グループ、サブスクリプション、および場所を構成します。 これらの設定は必須であり、サービスがプロビジョニングされた後は変更できません。

     ![][2]

    - **[サービス名]** はスペースなしで、15 文字以下の小文字で、一意である必要があります。 この名前は、Azure Search サービスのエンドポイントの一部になります。 参照してください [名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx) 名前付け規則の詳細についてです。

    - [**価格レベル**] では、容量と課金を決定します。 どちらのレベルも同じ機能を提供しますが、リソース レベルが異なります。

        - **Free**  は他のサブスクライバーと共有しているクラスターで実行します。 Free 版はチュートリアルを試用して概念実証コードを書くには十分な機能を提供しますが、運用アプリケーションには対応していません。 Free サービスは、通常は数分でデプロイできます。
        - **[Standard]** レベルは専用リソースで実行され、拡張性に優れています。 最初、Standard サービスは 1 つのレプリカと 1 つのパーティションを使用してプロビジョニングされますが、サービスを作成した後で容量を調整することができます。 Standard サービスをデプロイするには、通常は約 15 分かかります。

    - **リソース グループ**は、一般的な目的で使用するサービスとリソースのコンテナーです。 たとえば、Azure Search、Azure App Service の Web Apps 機能、Azure BLOB ストレージを使用してカスタム検索アプリケーションを構築する場合は、リソース グループを作成することで、これらのサービスをポータル管理ページにまとめておくことができます。

    - **[サブスクリプション]** では、複数のサブスクリプションがある場合に、複数のサブスクリプションから選択できます。

    - **[場所]** はデータ センターのリージョンです。 現時点では、すべてのリソースは同じデータ センターで実行する必要があります。 複数のデータ センターにリソースを分散させることはできません。

4. **[作成]** をクリックしてサービスをプロビジョニングします。

ジャンプバーで、通知を確認します。 サービスが使用できるようになると、通知が表示されます。

<a id="sub-3"></a>
## Standard レベルの Search サービスを追加して専用リソースを手に入れる

多くのお客様は、まず Free サービスから始め、ワークロードの増大に合わせて Standard レベルに切り替えます。 Standard レベルは、Azure データ センター内の本人だけが使用できる専用リソースを提供します。

Azure Search の操作には、ストレージとサービス レプリカの両方が必要です。 Standard レベルでは、リソースを追加するオプションがない Free サービスとは異なり、スケールアップしてストレージまたはクエリのサポートを追加することができます。自分のシナリオにとってより重要なリソースを増やせます。

Standard レベルを使うには、その価格レベルで新しい Search サービスを作成する必要があります。 この記事の前の手順を繰り返せば、新しい Azure Search サービスを作成できます。 専用リソースの設定には時間がかかる場合があります (最大 15 分以上)。

Free バージョンのインプレース アップグレードはありません。 スケールの拡張を可能とする Standard に切り替えるには、新しいサービスが必要です。 検索アプリケーションで使用するインデックスとドキュメントをリロードする必要があります。

Standard レベルの Azure Search サービスは 1 つのレプリカとパーティションから開始されますが、リソースの上位レベルで簡単に変更できます。

1.  サービスを作成してから、サービス ダッシュボードに戻ります。

2.  [**スケール**] タイルをクリックします。

3.  スライダーを使用して、レプリカ、パーティション、または両方を追加します。

追加のレプリカおよびパーティションは、検索単位で課金されます。 リソースの追加に応じて、特定のリソース構成に必要な検索単位の合計数がページに表示されます。

チェックすることができます [の料金詳細](http://go.microsoft.com/fwlink/p/?LinkID=509792) 、単位あたりの課金情報を取得します。 参照してください [制限および制約](search-limits-quotas-capacity.md) パーティションとレプリカを構成する方法を決定するときにヘルプを参照します。

<a id="sub-2"></a>
## Azure Search サービスのサービス名と API キーの取得

サービスを作成した後、URL を取得するには、Azure クラシック ポータルに返すことができますか `api キー`します。 Azure Search サービスへの接続が必要、URL に加えて、 `api キー` 、呼び出しを認証します。

1. ジャンプ バーで **[ホーム]** をクリックし、Azure Search サービスをクリックして、サービスのダッシュボードを開きます。

2. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。

    ![][3]

3. サービスの URL と管理者キーをコピーします。 次のタスクに必要になります [サービス操作をテストする](#sub-4)します。


<a id="sub-4"></a>
## サービス操作をテストする

Azure Search 構成の最後の手順では、サービスがクライアント アプリケーションから操作およびアクセスできるかどうかを確認します。 サービスの可用性を確認するためのコードを使用しないアプローチについては、以下のいずれかのリンクを使用できます。

- [Azure Search で Chrome Postman を使用する方法](search-chrome-postman.md)
- [Azure Search で Telerik Fiddler を使用する方法](search-fiddler.md)


<a id="next-steps"></a>
## 次のステップ

次では、Azure Search を使用する検索アプリケーションを作成して管理する方法について説明しています。

- [.NET で Azure Search を使用する方法](search-howto-dotnet-sdk.md)

- [Microsoft Azure で検索ソリューションを管理します。](search-manage.md)

- [MSDN の azure Search](http://msdn.microsoft.com/library/dn798933.aspx)

- [Channel 9 ビデオ: Azure Search の概要](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)





[find the service name and api-keys of your azure search service]: #sub-2 
[upgrade to the standard tier]: #sub-3 
[test service operations]: #sub-4 
[next steps]: #next-steps 
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG 
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG 
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG 

