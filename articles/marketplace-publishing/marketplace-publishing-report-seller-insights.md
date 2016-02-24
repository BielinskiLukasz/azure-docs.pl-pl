<properties
   pageTitle="Azure Marketplace の使用量ベース レポートおよび Seller Insights レポートの概要 | Microsoft Azure"
   description="Azure Marketplace の販売者は、Seller Insights レポートとも呼ばれる使用量ベース レポートを理解してください"
   services="Azure Marketplace"
   documentationCenter="na"
   authors="v-jeana"
   manager="lakoch"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/12/2015"
   ms.author="v-jeana; hascipio"/>

# Seller Insights レポートの概要

**Seller Insights とは**

提供品で使用が発生した場合、すべての VM および使用量ベース課金制開発者サービスの公開元は、Microsoft から毎月レポートを受け取ります。

**受け取るレポート**

- **ようこそ電子メール:** 新しいパブリッシャーとして販売者 insights 月々 のレポートの提供が開始されることを通知するように、ウェルカム メールを受け取ります。

- **月間の売上レポート:**  使用率があれば、パスワードにアクセスする方法については、レポートを含む 2 つ目の電子メールを受け取ります。

    - VM がある場合、または使用量ベース課金制開発者サービス SKU の使用実績ある場合は、BYOL (ライセンス持ち込み) 以外の提供品の注文、使用量、市場、顧客に関する詳細がマンスリー レポートで示されます。
    - レポートは、顧客データを保護するため、パブリッシャーと Microsoft だけが知っているパスワードによってロックされています。
    - 該当月にどの提供品にも使用実績がない場合、レポートは送られません。

## Seller Insights レポートの概要


**SKU とライセンス タイプごとに注文: Marketplace Orders] タブ**

![readingreportbyorders][2]

- スライサーを使用して各要素でレポートをフィルター処理できます。
- グラフには、Azure ライセンス タイプごとの毎月の注文が示されます。 各バーには、その月の注文合計が Azure ライセンス タイプ別に表示されます。
- グラフに毎月の注文が SKU 別に表示されます。 各バーには、すべての SKU の毎月の注文合計が SKU 別に表示されます。
- グラフに、注文数の月ごとの傾向が Azure ライセンス タイプ別と Azure Marketplace ライセンス タイプ別に表示されます。
- 円グラフには、注文数が Azure ライセンス タイプ別と Marketplace ライセンス タイプ別に表示されます。
- 表には、Marketplace ライセンス タイプ別の月ごとの合計注文数、月ごとの合計、すべての月の総計が表示されます。


**SKU とライセンス タイプごとに注文: Marketplace の使用法] タブ**

![readingreportbyusage][3]

- スライサーを使用して各要素でレポートをフィルター処理できます。
- 正規化された VM の利用状況情報または処理されていない利用状況データのいずれかを選択する必要があります。
- グラフには、Azure ライセンス タイプごとの毎月の利用状況が示されます。 各バーには、その月の利用合計が Azure ライセンス タイプ別に表示されます。
- グラフに毎月の利用状況が SKU 別に表示されます。 各バーには、すべての SKU の毎月の利用合計が SKU 別に表示されます。
- グラフには、利用状況の月ごとの傾向が Azure ライセンス タイプ別と Marketplace ライセンス タイプ別に表示されます。
- 円グラフには、利用状況が Azure ライセンス タイプ別と Marketplace ライセンス タイプ別に表示されます。
- 表には、Marketplace ライセンス タイプ別の月ごとの利用合計、月ごとの合計、すべての月の総計が表示されます。


**[注文データ] タブおよび [利用状況データ] タブ**

これらのタブでは、レポートの生成に使われた詳細なデータが提供されます。

![orderdata][4]

![usagedata][5]



**SKU とライセンスの種類による利用状況: [Customers] タブ**

![customerstab][6]

- 機密性に関する記述に注意してください。
- このタブには、SKU 別の顧客のリスト、プロファイル情報、取引の日付、プロモーションの連絡に関するオプトインが含まれます。
- レポートには、SKU 別の注文数および合計が含まれます。


**免責:**

![legal][1]

法的免責事項を注意深くお読みください。 質問や意見がある場合は、免責事項の下部にあるリンクをクリックすると、Marketplace のサポート ページに移動します。

## パスワードのリマインダーを要求する

Https://publish.windowsazure.com/に移動し、Microsoft アカウントの資格情報でサインインします。
![passwordreminder][7]

選択、 **パブリッシャー** ] タブをクリックします。
![selectpublisherstab][8]


発行者 ID を URL にあります。
- パスワードとしてこの ID を使用して、販売者 insights Excel ファイルを開きます。
これは、さらに通知があるまで、パスワードです。
- 選択した、ブックの閲覧者として、Windows と Microsoft Office 2013 を使用することをお勧めします。  Microsoft Office for Mac を使うと問題があることが報告されています。

![publisherid][9]


## 次のステップ  
レポートおよび Insights に関して質問がある場合は、サポート チームにお問い合わせください。

1. Https://publish.windowsazure.com/にあるサポート ページに移動します。
2.  **問題の種類** ボックスで、 **レポート、分析**します。
3.  **カテゴリ** ボックスで、 **レポートに関連する質問**します。
4. クリックして **開始要求**します。
  ![sellerinsightsquestions][10]



[1]: ./media/marketplace-publishing-report-seller-insights/legal.png
[2]: ./media/marketplace-publishing-report-seller-insights/readingreportbyorders.png
[3]: ./media/marketplace-publishing-report-seller-insights/readingreportbyusage.png
[4]: ./media/marketplace-publishing-report-seller-insights/orderdata.png
[5]: ./media/marketplace-publishing-report-seller-insights/usagedata.png
[6]: ./media/marketplace-publishing-report-seller-insights/customerstab.png
[7]: ./media/marketplace-publishing-report-seller-insights/passwordreminder.png
[8]: ./media/marketplace-publishing-report-seller-insights/selectpublisherstab.png
[9]: ./media/marketplace-publishing-report-seller-insights/publisherid.png
[10]: ./media/marketplace-publishing-report-seller-insights/sellerinsightsquestions.png

