<properties 
    pageTitle="Machine Learning Recommendations API の設定と使用 | Microsoft Azure" 
    description="Azure Machine Learning で作成された Microsoft Recommendations API の FAQ" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/08/2015" 
    ms.author="luisca"/> 

#Machine Learning Recommendations API の設定と使用に関する FAQ


**Recommendations とは何ですか。**

顧客への製品やサービスのクロスセルやアップセルのために推奨機能を利用している組織や事業に対して、Azure Machine Learning の Recommendations サービスは、セルフサービスの推奨エンジンを提供します。 これは、コア アルゴリズムとして行列因子分解を使用した協調フィルタリングの実装です。 アプリケーション開発者は REST Api を使用してRecommendations にアクセスできます。 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Recommendations では何ができますか。**

Recommendations は、1 つの項目または一連の項目を入力として受け取り、関連する推奨の一覧を返します。 たとえば、オンライン小売業者の顧客が製品をクリックします。 オンライン小売業者が Recommendations への入力としてその製品を送信すると、製品一覧が返されます。業者は、一覧のどの製品を顧客に表示するかを決定します。 Recommendations を使用してオンライン ストアを最適化したり、社内の販売部門やコール センターに知らせたりすることもできます。

**使用制限はありますか。**

推奨事項には、次の使用率の制限があります。
* サブスクリプションごとのモデルの最大数: 10
* カタログが保持できる項目の最大数: 100,000
* 保持される使用状況ポイントの最大数は ~5,000,000 です。 新しいデータがアップロードまたは報告されると、最も古いデータが削除されます。
* (たとえば、カタログ データ、使用状況データのインポート) を電子メールで送信できるデータの最大サイズは 200 MB
* 1 秒間 (TP) がアクティブでない推奨モデル ビルドに対するトランザクションの数は、約 2 TPS です。 アクティブな推奨モデルのビルドは、最大 20 TPS を保持できます。

##購入と請求 


**開始期間中の Recommendations の料金はいくらですか。**

Recommendations は、サブスクリプション ベースのサービスです。 課金は、1 か月あたりのトランザクションのボリュームに基づいて行われます。 [オファーのページ] をチェックすることができます (https://datamarket.azure.com/dataset/amla/recommendations) 料金情報については、Microsoft Azure Marketplace でします。

**Recommendations の追跡やユーザー アクティビティの格納に関連する料金はありますか。**

現時点ではありません。

**Recommendations には、無料試用版がありますか。**

1 か月あたり 10,000 トランザクションに制限された無料試用版があります。

**Recommendations への請求はいつ行われますか。**

有料サブスクリプションは、月額料金のサブスクリプションです。 有料サブスクリプションを購入すると、最初の使用月からすぐに課金されます。 サブスクリプション ページのプランに表示される金額 (さらに適用される税金) が課金されます。 この月額料金は、サブスクリプションを取り消すまで、最初の購入時と同じ日付で毎月発生します。 

**上位のサービスへのアップグレード方法を教えてください。**

購入するか、[オファーのページ] からサブスクリプションを更新することができます (https://datamarket.azure.com/dataset/amla/recommendations) ページは、Microsoft Azure Marketplace をします。

サブスクリプションをアップグレードする場合:

* 古いサブスクリプションに残っているトランザクションは、新しいサブスクリプションには追加されません。 
* 古いサブスクリプションで未使用のトランザクションがある場合でも、新しいサブスクリプションの全額をお支払いください。

サブスクリプションのアップグレード プロセス:

* [プランのページ] にプラン (https://datamarket.azure.com/dataset/amla/recommendations)。
* まだサインインしていない場合は、Marketplace にサインインします。
* 右側のペインに、使用可能なすべてのプランが一覧表示されます。 アップグレードするプランのラジオ ボタンをクリックします。
* アップグレードする場合は、クリックして **OK**します。 アップグレードしない場合はクリックして **キャンセル**します。

**重要な** 請求と使用に関係があるために、アップグレードする前に、ダイアログ ボックスを注意深く読んでいます。

**Recommendations のサブスクリプションはいつ終了しますか。**

取り消した時点で、お客様のサブスクリプションが終了します。 サブスクリプションを取り消す場合は、以下の手順をご覧ください。

**Recommendations のサブスクリプションを取り消す方法を教えてください。**

サブスクリプションを取り消すには、次の手順を使用します。 現在のサブスクリプションが有料サブスクリプションの場合、お客様のサブスクリプションは現在の請求期間が終了するまで有効です。 取り消しをすぐに有効にする場合は、までご連絡 [Microsoft サポート](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)します。

**注** 払い戻しはありません、請求期間で未使用のトランザクションの請求期間の終了前にキャンセルするとします。

* [プランのページ] にプラン (https://datamarket.azure.com/dataset/amla/recommendations)。
* まだサインインしていない場合は、Marketplace にサインインします。
* クリックして **キャンセル** データセットの名前と状態の右側にします。 現在の請求期間が終了するまで、またはトランザクションの上限に達するまで、このサブスクリプションを使用することができます。

新しいサブスクリプションを購入するための直後に、サブスクリプションをキャンセルする場合は、ファイルでチケットを [Microsoft サポート](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn)します。

##Recommendations の概要

**Recommendations は私に適していますか。** 

Machine Learning の Recommendations サービスは、顧客への製品やサービスのクロスセルやアップセルのために推奨機能を利用している組織や事業を対象にしています。 顧客対応のための Web サイト、営業スタッフ、社内営業スタッフ、またはコール センターがあり、数十以上の製品やサービスのカタログを提供している場合、Recommendations を使用することによって収益が向上する可能性があります。 

Recommendations は簡単に利用できるように設計されています。 現在の API ベースのバージョンには、基本的なプログラミング スキルが必要です。 サポートが必要な場合は、お客様の Web サイトを作成したベンダーにお問い合わせください。 社内に IT 部門がある、または社内開発者がいらっしゃる場合は、それらの方に支援を依頼することをお勧めします。 

**Recommendations を設定するための前提条件とは何ですか。**

Recommendations には、お客様のカタログに関するユーザーの選択内容のログが必要です。 このようなログがなくても、顧客対応の Web サイトを運営している場合は、Recommendations がユーザー アクティビティを収集できます。 

Recommendations には、お客様の製品やサービスのカタログも必要です。 カタログがない場合は、Recommendations が実際の顧客の使用状況データを使用して、カタログを抽出することができます。 "暗黙" のカタログには、ユーザー トランザクションの一部として "報告" されていない項目は含まれません。

**Recommendations の最初の設定方法を教えてください。**

後に [購読] (https://datamarket.azure.com/dataset/amla/recommendations) の推奨事項に API のドキュメントを使用する必要があります、 [Azure Machine Learning Recommendations – クイック スタート ガイド](machine-learning-recommendation-api-quick-start-guide.md) 、サービスを設定します。

**API ドキュメントはどこで入手できますか。** 

API のドキュメントは [Azure Machine Learning Recommendations – クイック スタート ガイド](machine-learning-recommendation-api-quick-start-guide.md)します。

**Recommendations にカタログや使用状況データをアップロードする場合にはどのようなオプションがありますか。**

カタログや使用状況データのアップロードには 2 つのオプションがあります。CRM システムまたはその他のログからこれらのデータをエクスポートして Recommendations にアップロードするか、ユーザー アクティビティを追跡するタグを Web サイトに追加することができます。 後者を使用する場合、データは Azure に格納されます。

##メンテナンスとサポート

**データ セットの大きさはどの程度まで許容されますか。**

各データ セットには、最大 100,000 のカタログ項目、および最大 2,048 MB の使用状況データを保持できます。
さらに、サブスクリプションには最大 10 個のデータ セット (モデル) を含めることができます。

**どこで Recommendations についてのテクニカル サポートを受けられますか。**

テクニカル サポートには、 [Microsoft Azure サポート](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) サイトです。

**使用条件はどこで入手できますか。**

[Microsoft Azure Machine Learning Recommendations API Terms of Service](https://datamarket.azure.com/dataset/amla/recommendations#terms)します。



 

