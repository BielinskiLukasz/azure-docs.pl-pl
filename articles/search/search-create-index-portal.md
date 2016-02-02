<properties
    pageTitle="ポータルでの Azure Search インデックスの作成 | Microsoft Azure | ホスト型クラウド検索サービス"
    description="Azure クラシック ポータルでフィールド定義を入力して、クラウドのホスト型検索サービスである Azure Search にインデックスを追加します。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="11/04/2015"
    ms.author="heidist"/>


# Azure クラシック ポータルでの Azure Search インデックスの作成

> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST API](search-create-index-rest-api.md)


Azure クラシック ポータルで作成することで、Azure Search のインデックスのプロトタイプをすばやく作成できます。 ポータルは概念実証テストでの使用に最適です。また、サービスにデプロイされているすべてのインデックスに対するスキーマの定義およびリソースの使用状況を表示するために使用することもできます。

このタスクを完了するには、すぐに使用できる Azure Search サービスを備えていることを確認してください。 参照してください [ポータルで Azure Search サービスの作成](search-create-service-portal.md) を設定する必要がある場合。

1. サインイン、 [Azure クラシック ポータル](https://portal.azure.com)します。

2. Azure Search サービスのサービス ダッシュボードを開きます。 ダッシュボードを見つけるには、次のいくつかの方法があります。
    - ジャンプバーで、[**ホーム**] をクリックします。 ホーム ページには、サブスクリプションのすべてのサービスのタイルが表示されます。 タイルをクリックして、サービス ダッシュボードを開きます。
    - ジャンプバーで **[すべて参照]**、**[フィルター]**、**[Search サービス]** の順にクリックして、一覧で Search サービスを見つけます。

3. サービス ダッシュボードの最上部には、[**インデックスの追加**] を含むコマンド バーが表示されます。

    価格レベルを確認します。 無料バージョンの場合は、最大 3 つのインデックスを作成できます。 空き領域を得るために、インデックスを削除しなければならない場合があります。

     ![][1]

4. インデックスを削除するには、インデックスをクリックしてブレードを開きます。 [**削除**] をクリックします。

     ![][2]

5. ポータルで新しいインデックスを作成すには、**[インデックスの追加]** をクリックし、*hotels* などの名前を付けます。

    インデックスの作成には時間がかかることがありますが、使用可能になったらインデックスの一覧に表示されます。

6. [**hotels**] をクリックして、インデックス定義ブレードを開きます。

    ポータルでインデックスを作成すると、入力必須フィールド (id) が作成されます。 これはキー フィールドであり、各ドキュメントを一意に識別するために使用します。 キーごとにフィールドは 1 つだけで (複合キーではない)、常に文字列となります。

    キー フィールドの名前を変更する場合は、インデックスの作成中のこの時点で、この手順を実行する必要があります。 インデックスを作成した後、フィールドの名前を変更することはできません。

    ![][3]

7. フィールド名を編集するには、フィールドの一覧にある右矢印をクリックします。

8. **id** を **hotelId** に置き換えます。

9. 各ブレード (フィールドおよびインデックス) で [**OK**] をクリックして、インデックスを作成します。

## フィールドの追加

Azure Search では、既定で searchable、 facetable、filterable などのインデックス属性が有効になっています。 通常、これらの属性を設定するときは、意味のない検索動作 (並べ替えや説明のファセットなど) を無効にします。

ポータルは異なります。 ポータルでは、該当するすべての動作をフィールド単位で選択できるように、既定で検索動作が無効になっています。

1. [**フィールドの追加/編集**] をクリックして、他のフィールドを追加します。 この演習で再作成、 *ホテル* の記事で説明されているインデックス [Azure Search で Fiddler を使用する方法](search-fiddler.md)します。

    ![][4]

2. フィールドを追加して構成し、スキーマを完了します。

    ![][5]

    レビュー [名前付け規則](https://msdn.microsoft.com/library/azure/dn857353.aspx) と [サポートされるデータ型](https://msdn.microsoft.com/library/azure/dn798938.aspx) フィールド名と型のリファレンス情報。

    インデックス属性は、以下の項目で構成されています。

    - **Retrievable** は、検索結果でフィールドを返すことができるかどうかを設定します。
    - **Filterable** は、**$filter** クエリでフィールドを使用できるようにします。
    - **Sortable** は、並べ替えオプションとしてフィールドを使用できるようにします。
    - **Facetable** は、自律フィルター処理のファセット ナビゲーション構造でフィールドを使用できるようにします。 通常は、複数のドキュメント (たとえば、1 つの製品やサービス カテゴリに属する複数のドキュメント) をまとめてグループ化するために使用できる、反復する値があるフィールドが、ファセットとして最適です。
    - **Key** は、ドキュメント検索に使用される、各ドキュメントの一意の ID です。 各インデックスに、1 つのキーが必要です。 1 つのフィールドだけをキーにすることができ、そのフィールドを Edm.String に設定する必要があります。
    - **Searchable** は、フィールドをフルテキスト検索可能としてマークします。

3. 不要なフィールドを削除するには、フィールドを右クリックし、[**削除**] を選択します。

4. [**OK**] をクリックして、定義したインデックスを保存し、[インデックスの追加] ページの [**OK**] をクリックしてインデックスを構築します。


## 次のステップ

インデックスが定義されましたが、ドキュメントが読み込まれるまでは使用できません。 テスト目的で使用される Hotels インデックスを再作成するいると仮定した場合でそのインデックス用のドキュメントの数が少ないを簡単に読み込むことができます [Fiddler](search-fiddler.md), 、ドキュメントを読み込む」の手順を使用して [Azure Search で Fiddler を使用する方法](search-fiddler.md)します。 その後、この記事の残りの手順に従って、いくつかのクエリを実行することができます。

基本のインデックスに慣れたら、言語アナライザーまたはサジェスターを追加して、多言語サポートまたは検索候補を追加することを検討します。 両方の機能は、インデックス スキーマで指定します。 参照してください [言語サポート](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) と [Create Index](https://msdn.microsoft.com/library/azure/dn798941.aspx) の詳細。



[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG 
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG 
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG 
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG 
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG 

