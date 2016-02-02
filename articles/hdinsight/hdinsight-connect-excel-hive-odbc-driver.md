<properties
   pageTitle="Hive ODBC ドライバーを使用した Excel から Hadoop への接続 | Microsoft Azure"
   description="Excel 用の Microsoft Hive ODBC ドライバーを使用できるようにセットアップし、HDInsight クラスターのデータを照会する方法を説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/15/2015"
   ms.author="jgao"/>


# Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft のビッグ データ ソリューションでは、Microsoft ビジネス インテリジェンス (BI) コンポーネントを Azure HDInsight を使用して展開されている Apache Hadoop クラスターを統合します。 たとえば、Microsoft Hive Open Database Connectivity (ODBC) ドライバーを使用すれば、HDInsight で Hadoop クラスターの Hive データ ウェアハウスに Excel を接続できます。

また、Microsoft Power Query for Excel アドインを使用して Excel から HDInsight クラスターや、その他の (HDInsight 以外の) Hadoop クラスターなどのデータ ソースを接続することもできます。 詳細については、Power Query のインストールとは、次を参照してください。 [Excel から Power Query ][hdinsight-power-query]します。
> [AZURE.NOTE] この記事の手順は、Linux と Windows ベースの HDInsight クラスターのどちらにも使用できますが、クライアント ワークステーションには Windows が必要です。

**前提条件**:

この記事を読み始める前に、次の項目を用意する必要があります。

- **HDInsight クラスター**。 1 つを作成するを参照してください。 [[hdinsight の入門] Azure HDInsight の概要][hdinsight-get-started]します。
- Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、または Office 2010 Professional Plus がインストールされた**ワークステーション**。


## Microsoft Hive ODBC ドライバーのインストール

ダウンロードしてから [Microsoft Hive ODBC ドライバーをインストール、 [ダウンロード センターの ][hive-odbc-driver-download]します。

このドライバーは Windows 7、Windows 8、Windows 10、Windows Server 2008 R2、Windows Server 2012 のいずれかの 32 ビットまたは 64 ビット バージョンにインストールすることができ、これによって Azure HDInsight (バージョン 1.6 以降) および Azure HDInsight Emulator (v.1.0.0.0 以降) への接続が許可されます。 ODBC ドライバーを使用するアプリケーションのバージョンに合致したバージョンをインストールする必要があります。 このチュートリアルでは、Office Excel のドライバーが使用されます。

## Hive ODBC データ ソースの作成

次の手順に従って、Hive ODBC データ ソースを作成します。

1. Windows 8 または Windows 10 で、Windows キーを押してスタート画面を開き、**データ ソース**を入力します。
2. 使用している Office バージョンに応じて、**[ODBC データ ソースのセットアップ (32 ビット)]** または **[ODBC データ ソースのセットアップ (64 ビット)]** をクリックします。 Windows 7 を使用している場合は、**[管理ツール]** の **[ODBC データ ソース (32 ビット)]** または **[ODBC データ ソース (64 ビット)]** をクリックします。 **[ODBC データ ソース アドミニストレーター]** ダイアログが開きます。

    ![ODBC データ ソース アドミニストレーター][img-hdi-simbahiveodbc-datasource-admin]

3. ユーザー DNS から、**[追加]** をクリックすると、**データ ソースの新規作成**ウィザードが開きます。
4. **[Microsoft Hive ODBC ドライバー]** を選択し、**[完了]** をクリックします。 **[Microsoft Hive ODBC ドライバーの DNS セットアップ]** ダイアログが開きます。

5. 次の値を入力または選択します。

 プロパティ| 説明
---|---
 データ ソース名| データ ソースに名前を付けます。
 ホスト| 入力 <HDInsightClusterName>. >.azurehdinsight.net です。たとえば、「myHDICluster.azurehdinsight.net」と入力します。
 ポート| Use <strong>443</strong>.(このポートは 563 から 443 に変更されました)。
 データベース| Use <strong>Default</strong>.
 Hive サーバーの種類| 選択 <strong>Hive Server 2</strong>
 メカニズム| 選択 <strong>Azure HDInsight サービス</strong>
 HTTP パス| 空白のままにします。
 ユーザー名| HDInsight クラスター ユーザーのユーザー名を入力します。これは、クラスターのプロビジョニング処理中に作成されるユーザー名です。簡易作成オプションを使用する場合に、既定のユーザー名は <strong>admin</strong>します。
 パスワード| HDInsight クラスター ユーザーのパスワードを入力します。

</table>

**[詳細オプション]** をクリックするときに、注意する必要のある重要なパラメーターがいくつかあります。

 パラメーター| 説明
---|---
 ネイティブ クエリの使用| これを選択すると、ODBC ドライバーは TSQL を HiveQL に変換しません。純粋な HiveQL ステートメントを送信していることを確認している場合にのみ使用します。SQL Server または Azure SQL Database に接続している場合は、オフのままにします。
 ブロック単位でフェッチされた行| 大量のレコードをフェッチする場合、このパラメーターを調整してパフォーマンスを最適化する必要がある場合があります。
 既定の文字列の列の長さ、バイナリ列の長さ、10 進数の列の桁数| データ型の長さおよび精度は、データが返される方法に影響する可能性があります。精度が失われたり、切り捨てられたりするために間違った情報が返されます。


    ![Advanced options][img-HiveOdbc-DataSource-AdvancedOptions]

6. **[テスト]** をクリックして、データ ソースをテストします。 データ ソースが正しく構成された場合、*テストは無事に完了しました。*と表示されます。
7. **[OK]** をクリックして [テスト] ダイアログを閉じます。 これで新しいデータ ソースが **[ODBC データ ソース アドミニストレーター]** ダイアログに表示されます。
8. **[OK]** をクリックしてウィザードを終了します。

## HDInsight クラスターから Excel へのデータのインポート

ここでは、上記の手順で作成した ODBC データ ソースを使用して、Hive テーブルから Excel ブックへデータをインポートする方法を説明します。

1. Excel で新しいブックまたは既存のブックを開きます。
2. **[データ]** タブから **[その他のデータ ソース]** タイルをクリックし、**[データ接続ウィザード]** の順にクリックして、**データ接続ウィザード**を開きます。

    ![データ接続ウィザードを開く][img-hdi-simbahiveodbc.excel.dataconnection]

3. データ ソースとして **[ODBC DSN]** を選択して、**[次へ]** をクリックします。
4. ODBC データ ソースから、前の手順で作成したデータ ソース名を選択し、をクリックして **次**します。
5. ウィザードにクラスターのパスワードを再入力し、必要に応じて再度 **[テスト]** をクリックして構成を確認します。
6. **[OK]** をクリックして [テスト] ダイアログを閉じます。
7. **[OK]** をクリックします。 **[データベースとテーブルの選択]** ダイアログが開くのを待ちます。 この処理には数秒かかります。
8. インポートするテーブルを選択し、**[次へ]** をクリックします。 *hivesampletable* は HDInsight クラスターに付属する Hive テーブルのサンプルです。 作成していない場合は、ここで選択できます。 詳細については、Hive クエリを実行および Hive テーブルの作成を参照してください。 [を使用して HDInsight での Hive ][hdinsight-use-hive]します。
8. **[完了]** をクリックします。
9. **[データのインポート]** ダイアログでは、クエリを変更または指定できます。 これを行うには、**[プロパティ]** をクリックします。 この処理には数秒かかります。
10. をクリックして、 **定義** タブをクリックし、追加 **LIMIT 200** select ステートメントで、Hive に、 **コマンド テキスト** ] ボックスに貼り付けます。 変更によって、返されるレコード セットが 200 に制限されます。

    ![接続のプロパティ][img-hdi-simbahiveodbc-excel-connectionproperties]

11. **[OK]** をクリックして [接続プロパティ] ダイアログを閉じます。
12. **[OK]** をクリックして **[データのインポート]** ダイアログを閉じます。
13. パスワードを再入力して **[OK]** をクリックします。 データが Excel にインポートされるまでに、数秒かかります。

## 次のステップ

この記事では、Microsoft Hive ODBC ドライバーを使用して HDInsight サービスから Excel にデータを取得する方法を学習しました。 同様に、SQL Database に HDInsight サービスからデータを取得することもできます。 また、HDInsight サービスにデータをアップロードすることもできます。 詳細については、次を参照してください。

- [HDInsight ][hdinsight-analyze-flight-data]
- [[Hdinsight でのデータのアップロード] を HDInsight にデータをアップロードします。][hdinsight-upload-data]
- [[Hdinsight での sqoop の使用] を HDInsight での Sqoop を使用します。][hdinsight-use-sqoop]



[hdinsight-use-sqoop]: hdinsight-use-sqoop.md 
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md 
[hdinsight-use-hive]: hdinsight-use-hive.md 
[hdinsight-upload-data]: hdinsight-upload-data.md 
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md 
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md 
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698 
[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png 
[img-hiveodbc-datasource-advancedoptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png 
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png 
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png 

