<properties
 pageTitle="Azure ポータル内で Azure Scheduler を使用した作業開始 | Microsoft Azure"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Azure クラシック ポータル内で Azure Scheduler を使用した作業開始

## 作業開始

Azure Scheduler のジョブとジョブ コレクションを作成するのは簡単です。 このチュートリアルでは、ジョブの格納に使用するジョブ コレクションの作成、ジョブ コレクションに含めるジョブの作成、使用できるジョブ監視タスクと管理タスクの概要について説明します。 このチュートリアルを利用するにあたって、Azure の使用経験があることが求められます。

初めて Azure クラシック ポータルを開くに自動的に配置されて、 **すべてのアイテム** ] タブをクリックします。 内の列、 **すべてのアイテム** ] タブを並べ替えることができます。 スケジューラ ジョブとジョブ コレクションを表示する] をクリックして、 **スケジューラ** ] タブをクリックします。

![][1]

## ジョブ コレクションとジョブの作成

1.  サインイン、 [Azure クラシック ポータル](https://manage.windowsazure.com/)します。  

2.  クリックして **App Services** > **新規作成** > **スケジューラ**, 、] をクリックし、 **カスタム作成**します。 <br /><br /> ![][2]

3.   **ジョブ コレクション**, 、下にある既存のジョブ コレクションの名前を選択、 **ジョブ コレクション** ボックスの一覧です。 既存のジョブ コレクションがない場合は、ジョブを選択して追加たい **新規作成** し、新しいジョブ コレクションを識別するために名前を入力します<br /><br /> 。![][3]

4.   **地域**, 、ジョブ コレクションのリージョンを選択します。  

5.  矢印キーをクリックしてジョブ コレクションを作成し、次のジョブの作成ステージに移動します。

6.  単純に http://www.microsoft.com/に GET 要求をアクセスするジョブを作成してみましょう。  **ジョブ アクション** 画面で、要求されたフォーム フィールドに次の値を定義します。  

    1.  **名前:** ` getmicrosoft`  

    2.  **アクションの種類:** ` HTTP`  

    3.  **方法:** ` GET`  

    4.  **URI:** ` http://www.microsoft.com`  

    ![][4]

7.  ジョブを作成した後にスケジュールを定義します。 ジョブは 1 回限りのジョブとして定義することができますが、ここでは定期的なスケジュールを選択しましょう。 このチュートリアルのスクリーンショットでは、例として繰り返し間隔が 1 分になっているものがありますが、ここでは 12 時間の間隔を選択します。  

    1.  **繰り返すごと:** ` 12 Hours`  

    2.  **開始:** ` Now`  

    3.  **[終了日]:** ` Select date 2 days after current day and any time`  

    ![][5]

8.  Click **OK**.  
    ジョブおよびジョブ コレクションの作成には、時間がかかる場合があります。 状態を確認するには、ポータルの下部にある通知を監視します。

    ![][6]

    ジョブおよびジョブ コレクションが作成されると、ジョブまたはジョブ コレクションが正常に作成されたことを示すメッセージが表示されます。 ジョブは [スケジューラ] セクションの [ジョブ] セクションに一覧表示され、ジョブ コレクションは [ジョブ コレクション] セクションに一覧表示されます。 ジョブに追加の高度な設定を構成する方法については、以下の「ジョブの構成」セクションを参照してください。

    ![][7]

## ジョブ コレクションおよびジョブの管理および監視

作成されたジョブ コレクションは、Scheduler のメイン管理画面に表示されます。

![][8]

ジョブ コレクションをクリックすると、新しいウィンドウが開き、次のオプションが表示されます。

1.  ダッシュボード  

2.  スケール  

3.  履歴  

4.  ジョブ  

以降のトピックでは、これらのタブについて詳しく説明します。

### ダッシュボード

ジョブ コレクション名をクリックすると、 **ダッシュ ボード** ] タブが表示されます。 ダッシュボードには次の情報が表示されます。

![][9]

#### ジョブ使用状況の概要と実行使用状況の概要

メトリックの固定のリストを表示するテーブルと一連のグラフです。 これらのメトリックは、次のようなジョブ コレクションの正常性状態に関するリアルタイムの値を提供します。

1.  現在のジョブ  

2.  完了したジョブ  

3.  フォールトが発生したジョブ  

4.  有効なジョブ  

5.  無効なジョブ  

6.  ジョブの実行  

#### 概要

ステータスと設定のメトリックの固定のリストを表示するテーブルです。 これらのメトリックは、次のようなジョブ コレクションに関連するステータスと設定に関するリアルタイムの値を提供します。

1.  状態  

2.  リージョン  

3.  エラーの数  

4.  実行の失敗回数  

5.  URI  

### スケール

 **スケール** ] タブで、設定と、スケジューラが使用するサービスの層を変更できます。

![][10]

#### 全般

使用しているかどうかが表示されます、 **Free** または **標準** 計画します。

#### Quotas (クォータ)

Azure Scheduler は、いくつかの条件に基づいてクォータを実装します。 このセクションにはクォータのしきい値が表示されます。これらの値は変更できます。 既定では、1 つのクォータのセットが構成されています。 これらのクォータ設定の限度は、プランによって制約されます。プランを変更すると、価格に影響が及びます。 クォータを変更して Scheduler の規模を変更できます。 次のオプションがあります。

1.  最大ジョブ数  

2.  最大頻度  

3.  最大間隔  

### 履歴

 **履歴** ] タブには、選択したジョブに関する次の情報が表示されます。

![][11]

#### 履歴テーブル

選択したジョブのシステム内でのすべてのジョブ実行に関して選択したメトリックが表示されるテーブル。 これらのメトリックは、Scheduler の正常性状態に関するリアルタイムの値を提供します。

#### 使用可能なメトリック

次のパフォーマンス カウンター/メトリックを使用できます。

1.  状態  

2.  詳細  

3.  再試行  

4.  実行回数 (1 回目、2 回目、3 回目など)  

5.  実行のタイムスタンプ  

クリックして **履歴詳細の表示** な全体の応答を実行するたびに確認します。 このダイアログ ボックスでは、応答をクリップボードにコピーすることもできます。

![][12]

### ジョブ

[ジョブ] タブには、ジョブの実行履歴を監視するための次の情報が表示されます。

![][13]

#### ジョブ テーブル

システム内のすべてのジョブに関して選択したメトリックが表示されるテーブル。 これらのメトリックは、Scheduler の正常性状態に関するリアルタイムの値を提供します。

#### ジョブの無効化、有効化、または削除

ジョブ名をクリックすると、ジョブを有効化、無効化、削除するためのオプションが表示されます。 削除したジョブは回復できない可能性があります。

#### 使用可能なメトリック

次のカウンターとメトリックを使用できます。

1.  名前  

2.  最後の実行  

3.  次の実行  

4.  状態  

5.  頻度  

6.  エラー  

7.  フォールト  

8.  実行  

9.  アクションの種類  

### ジョブの構成

ジョブをクリックすると、 **ジョブ** 画面では、そのジョブを構成することができます。 これにより、簡易作成ウィザードに用意されている設定以外の高度な設定を構成できます。 ジョブを構成するでジョブの名前の横にある右矢印をクリックして、 **ジョブ** 画面です。

ジョブの構成ページでは、ジョブ設定を更新することができます。 HTTP ジョブおよび HTTPS ジョブの構成ページを次に示します。 HTTP および HTTPS のジョブのアクションの種類では、方法を許可されている任意の HTTP 動詞に変更できます。 ヘッダーおよび基本的な認証情報を追加、削除、変更することもできます。

![][14]

ストレージ キュー ジョブの構成ページを次に示します。 ストレージ キュー アクションの種類では、ストレージ アカウント、キュー名、SAS トークン、および本文を変更することができます。 [スケジュール] セクション (ここには表示されていません) は、HTTP および HTTPS のジョブのアクションの種類の [スケジュール] セクションと同じです。

![][15]

最後に、すべてのアクションの種類について、スケジュール自体と定期実行動作を変更できます。 開始日時、定期実行のスケジュール、および終了日時 (定期実行ジョブの場合) を変更できます。変更を加えたらをクリックして変更を保存することがあります **保存** をクリックして変更を破棄または **破棄**します。

## 関連項目

 [Scheduler とは](scheduler-intro.md)

 [Scheduler Concepts, Terminology, and Entity Hierarchy (Scheduler の概念、用語集、エンティティ階層構造)](scheduler-concepts-terms.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法](scheduler-advanced-complexity.md)

 [Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)

 [Scheduler PowerShell Cmdlets Reference (Scheduler PowerShell コマンドレット リファレンス)](scheduler-powershell-reference.md)

 [Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Scheduler 送信認証](scheduler-outbound-authentication.md)



[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

