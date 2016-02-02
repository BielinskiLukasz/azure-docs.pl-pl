<properties
   pageTitle="SQL Data Warehouse での Azure Stream Analytics の使用 | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse での Azure Stream Analytics の使用に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/30/2015"
   ms.author="sahajs;twounder"/>


# SQL Data Warehouse での Azure Stream Analytics の使用

Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。 読み取ることによって、基本を学習できます [Azure Stream Analytics の概要][]します。 に従って、Stream Analytics によるエンド ツー エンド ソリューションを作成する方法を学習できますし、 [Azure Stream Analytics を使い始める][] チュートリアルです。

この記事では、Azure SQL Data Warehouse データベースを、Stream Analytics ジョブの出力シンクとして使用する方法について説明します。

## 前提条件

最初に、次の手順を実行、 [Azure Stream Analytics を使い始める][] チュートリアルです。

1. Event Hub 入力の作成
2. イベント ジェネレーター アプリケーションの構成と起動
3. Stream Analytics のジョブの準備
4. ジョブの入力とクエリの指定

次に、Azure SQL Data Warehouse データベースを作成します

## ジョブの出力の指定: Azure SQL Data Warehouse データベース

### 手順 1.

Stream Analytics ジョブで、ページ上部の **[出力]** をクリックし、**[出力の追加]** をクリックします。

### 手順 2.

SQL Database を選択し、[次へ] をクリックします。

![][add-output]

### 手順 3.

次の値を次のページに入力します。

- *出力のエイリアス*: このジョブの出力のフレンドリ名を入力します。
- *サブスクリプション*:
    - SQL Data Warehouse データベースが Stream Analytics ジョブと同じサブスクリプション内に存在する場合は、[現在のサブスクリプションの SQL データベースを使用] を選択します。
    - データベースが別のサブスクリプション内にある場合は、[別のサブスクリプションの SQL データベースを使用] を選択します。
- *データベース*: 宛先データベースの名前を指定します。
- *サーバー名*: 指定したばかりのデータベース用のサーバー名を指定します。 これは、Azure クラシック ポータルを使用して見つけることができます。

![][server-name]

- *ユーザー名*: データベースの書き込みアクセス許可を持つアカウントのユーザー名を指定します。
- *パスワード*: 指定したユーザー アカウントのパスワードを入力します。
- *テーブル*: データベース内の対象テーブルの名前を指定します。

![][add-database]

### 手順 4.

チェック ボタンをクリックして、このジョブ出力を追加し、Stream Analytics がデータベースに適切に接続できることを確認します。

![][test-connection]

データベースへの接続が成功すると、ポータルの下部に通知が表示されます。 下部にある [接続のテスト] をクリックすると、データベースへの接続をテストできます。

## 次のステップ

統合の概要については、次を参照してください。 [SQL Data Warehouse の統合の概要 []][]します。

他の開発のヒントを参照してください。 [SQL Data Warehouse の開発の概要 []][]します。









[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png 
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png 
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png 
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png 
[introduction to azure stream analytics]: stream-analytics-introductiond.md 
[get started using azure stream analytics]: stream-analytics-get-started.md 
[sql data warehouse development overview]: sql-data-warehouse-overview-develop.md 
[sql data warehouse integration overview]: sql-data-warehouse-overview-integrate.md 
[azure stream analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/ 

