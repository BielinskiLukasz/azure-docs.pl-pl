<properties
   pageTitle="SQL Data Warehouse での Power BI の使用 | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse での Power BI の使用に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/06/2015"
   ms.author="lodipalm"/>


# SQL Data Warehouse での Power BI の使用

Azure SQL Database の場合と同様、SQL Data Warehouse Direct Connect を使用すると、強力な論理プッシュ ダウンと共に Power BI の分析機能を活用できます。 Direct Connect では、データを検索する際に、クエリがリアルタイムで Azure SQL Data Warehouse に送信されます。 これを SQL Data Warehouse のスケールで組み合わすことで、テラバイト単位のデータに対して動的レポートを数分で作成できます。 さらに、[Power BI で開く] ボタンが導入されたため、Azure の他の部分から情報を収集しなくても、Power BI を直接 SQL Data Warehouse に接続できるようになりました。

Direct Connect を使用するときは、次の点に注意してください。

+ 接続する際 (詳細については後述) は、サーバーの完全修飾名を指定します。
+ データベースのファイアウォール ルールでは、必ず "Azure サービスへのアクセスを許可する" ように構成します。
+ 列の選択やフィルターを追加するなどのすべてのアクションは、データ ウェアハウスを直接クエリします。
+ タイルは約 15 分ごとに更新されます (更新をスケジュールする必要はありません)。
+ Q&A は、Direct Connect データセットでは使用できません。
+ スキーマ変更は、自動的に選択されるわけではありません。

これらの制限および注意事項は、エクスペリエンスが改善される中で変更される場合があります。 接続手順の詳細を以下に示します。

## [Power BI で開く] ボタンの使用

SQL Data Warehouse と Power BI の間を移動する最も簡単な方法は、[Power BI で開く] ボタンを使用することです。 このボタンをクリックすると、Power BI で新しいダッシュボードの作成をシームレスに開始できます。

1.  作成を開始するには、Azure クラシック ポータルで SQL Data Warehouse インスタンスに移動します。
2.  [Power BI で開く] ボタンをクリックします。
3.  直接サインインできない場合、または Power BI アカウントがない場合は、サインインする必要があります。
4.  SQL Data Warehouse の情報があらかじめ入力された SQL Data Warehouse 接続ページにリダイレクトされます。
5.  資格情報を入力すると、SQL Data Warehouse に完全に接続されます。

## Power BI ポータル経由での接続

[Power BI で開く] ボタンを使用する方法以外に、ユーザーは Power BI ポータルを介して SQL Data Warehouse に接続することもできます。

1.  ナビゲーション ウィンドウの下部にある [データの取得] をクリックします。
2.  [データベース] を選択します。
3.  [データベース] ページで、[Azure SQL Data Warehouse] を選択し、[接続] をクリックします。
4.  必要な接続情報を入力します。 次の「パラメーターの値の検索」セクションに、このデータを見つけることができる場所の説明があります。
5.  接続が行われると、Power BI のメイン ページに戻り、[データベース] の下に新しいエントリがインスタンスの名前で表示されます。
6.   新しいデータセットをクリックして、データベース内のすべてのテーブルとビューを確認できます。 列を選択すると、ソースにクエリが送信されて、ビジュアルが動的に作成されます。 これらのビジュアルは新しいレポートに保存され、ダッシュボードに固定できます。

## パラメーターの値の検索

Azure クラシック ポータルでは、サーバーの完全修飾名とデータベース名を検索できます。 現時点で Azure ポータルに表示されるは、SQL Data Warehouse のみであることに注意してください。














[sql data warehouse development overview]: ./sql-data-warehouse-overview-develop/ 
[sql data warehouse integration overview]: ./sql-data-warehouse-overview-integration/ 

