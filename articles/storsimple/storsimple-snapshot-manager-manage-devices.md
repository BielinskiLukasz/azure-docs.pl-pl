<properties 
   pageTitle="StorSimple Snapshot Manager を使用したデバイス管理 | Microsoft Azure"
   description="StorSimple Snapshot Manager MMC スナップインを使用して、StorSimple デバイスを接続および管理する方法について説明します。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/02/2015"
   ms.author="v-sharos" />


# StorSimple Snapshot Manager を使用した StorSimple デバイスの接続と管理

## 概要

StorSimple Snapshot Manager の **[スコープ]** ウィンドウのノードを使用して、インポートされた StorSimple デバイスのデータを確認し、接続されたストレージ デバイスを更新できます。 また、**[デバイス]** ノードをクリックして、接続されているデバイスと、それに対応するステータス情報の一覧を **[結果]** ウィンドウに表示できます。

![接続されているデバイス](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**図 1: StorSimple Snapshot Manager が接続されたデバイス**

**[表示]** での選択内容に従って、**[結果]** ウィンドウには各デバイスに関する次の情報が表示されます  (表示の構成の詳細についてを参照してください [表示] メニューの](storsimple-use-snapshot-manager.md#view-menu)します。


| 結果の列| 説明|
|:----------------|:--------------------|
| 名前| Azure クラシック ポータルで構成されているデバイスの名前|
| モデル| デバイスのモデル番号|
| バージョン| デバイスにインストールされているソフトウェアのバージョン|
| 状態| デバイスが使用できるかどうか|
| 最後の同期時刻| デバイスが最後に同期された日時|
| シリアル番号| デバイスのシリアル番号|

**[スコープ]** ウィンドウで **[デバイス]** ノードを右クリックすると、次の操作を選択できます。

- デバイスの追加または置き換え
- デバイスの接続、およびインポートの検証
- 接続されたデバイスの更新

**[結果]** ウィンドウで **[デバイス]** ノードをクリックし、デバイス名を右クリックすると、次の操作を選択できます。

- デバイスの認証
- デバイスの詳細の表示
- デバイスの更新
- デバイスの構成の削除
- デバイスのパスワードの変更

>[AZURE.NOTE] これらの操作は、**[操作]** ウィンドウでも使用できます。

このチュートリアルでは、StorSimple Snapshot Manager を使用してデバイスの接続および管理を行い、次のタスクを実行する方法について説明します。

- デバイスの追加または置き換え
- デバイスの接続、およびインポートの検証
- 接続されたデバイスの更新
- デバイスの認証
- デバイスの詳細の表示
- 個々のデバイスの更新
- デバイスの構成の削除
- 有効期限が切れたデバイス パスワードの変更
- 障害が発生したデバイスの置き換え

>[AZURE.NOTE] 概要については、StorSimple Snapshot Manager のインターフェイスを使用して、参照してください [StorSimple Snapshot Manager ユーザー インターフェイス](storsimple-use-snapshot-manager.md)します。


## デバイスの追加または置き換え

StorSimple デバイスを追加または置き換えるには、以下の手順を実行します。

#### デバイスを追加または置き換えるには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **[スコープ]** ウィンドウで **[デバイス]** ノードを右クリックし、**[デバイスの構成]** をクリックします。 **[デバイスの構成]** ダイアログ ボックスが表示されます。

    ![StorSimple デバイスの構成](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png)

3. **[デバイス]** ドロップダウン ボックスで、デバイスまたは仮想デバイスの IP アドレスを選択します。

4. **[パスワード]** テキスト ボックスに、Azure クラシック ポータルでデバイス用に作成した StorSimple Snapshot Manager パスワードを入力します。 **[OK]** をクリックします。 StorSimple Snapshot Manager により、指定したデバイスが検索されます。

    - デバイスを使用できる場合は、StorSimple Snapshot Manager によって接続が追加されます。

    - 何かの理由でデバイスを使用できない場合は、StorSimple Snapshot Manager によってエラー メッセージが返されます。 **[OK]** をクリックしてエラー メッセージを閉じ、**[キャンセル]** をクリックして **[デバイスの構成]** ダイアログ ボックスを閉じます。

## デバイスの接続、およびインポートの検証

次の手順に従って StorSimple デバイスを接続し、関連するバックアップが含まれた既存のボリュームがインポートされていることを確認します。

#### デバイスを接続し、インポートを検証するには

1. デバイスを StorSimple Snapshot Manager に接続するには、デバイスの追加または置き換えに関する指示に従ってください。 デバイスに接続されると、StorSimple Snapshot Manager は次のように応答します。

    - 何かの理由でデバイスを使用できない場合は、StorSimple Snapshot Manager によってエラー メッセージが返されます。

   - デバイスを使用できる場合は、StorSimple Snapshot Manager によって接続が追加されます。 デバイスを選択すると、**[結果]** ウィンドウに表示され、状態フィールドにそのデバイスが **[使用可能]** であることが示されます。 StorSimple Snapshot Manager は、ボリューム グループに関連するバックアップがある場合、デバイス用に構成されたすべてのボリューム グループをインポートします。 バックアップ ポリシーはインポートされません。 関連付けられているバックアップがないボリューム グループはインポートされません。

2. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

3. **[スコープ]** ウィンドウで最上位のノードを右クリックし、**[インポートの表示の切り替え]** をクリックします。

    ![[インポートの表示の切り替え] の選択](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png)

4. **[インポートの表示の切り替え]** ダイアログ ボックスが表示され、インポートされたボリューム グループとバックアップの状態が示されます。 **[OK]** をクリックします。

ボリューム グループおよびバックアップが正常にインポートされたら、StorSimple Snapshot Manager を使用して作成および構成したボリューム グループやバックアップを管理する場合と同様に、StorSimple Snapshot Manager を使用してそれらを管理できます。

## 接続されたデバイスの更新

接続された StorSimple デバイスを StorSimple Snapshot Manager と同期するには、以下の手順を実行します。

#### 接続されているデバイスを更新するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **[スコープ**] ウィンドウで **[デバイス]** を右クリックし、**[デバイスの更新]** をクリックします。 これにより、接続されたデバイスと StorSimple Snapshot Manager が同期されるため、最近追加したものを含め、ボリューム グループとバックアップを表示できるようになります。

    ![StorSimple デバイスの更新](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

**[デバイスの更新]** 操作では、接続されているデバイスからすべての新しいボリューム グループと、関連付けられているすべてのバックアップが取得されます。 **[ボリューム]** ノードで使用可能な **[ボリュームの再スキャン]** 操作とは異なり、**[デバイスの更新]** ではバックアップ レジストリは復元されません。

## デバイスの認証

次の手順に従って、StorSimple Snapshot Manager で StorSimple デバイスを認証します。

#### デバイスの認証するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **[スコープ]** ウィンドウで、**[デバイス]** をクリックします。

3. **[結果]** ウィンドウで、デバイスの名前を右クリックしてから **[認証]** をクリックします。

4. **[認証]** ダイアログ ボックスが表示されます。 デバイスのパスワードを入力し、**[OK]** をクリックします。

    ![ダイアログ ボックスの認証](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png)

## デバイスの詳細の表示

次の手順に従って、StorSimple デバイスの詳細を表示し、必要に応じてデバイスと StorSimple Snapshot Manager を再同期します。

#### デバイスの詳細を表示し、再同期するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **[スコープ]** ウィンドウで、**[デバイス]** をクリックします。

3. **[結果]** ウィンドウで、デバイスの名前を右クリックしてから **[詳細]** をクリックします。

4.**デバイスの詳細** ] ダイアログ ボックスが表示されます。 このボックスには、名前、モデル、バージョン、シリアル番号、状態、対象の iSCSI 修飾名 (IQN)、および最終同期の日時が表示されます。

   - **[再同期]** をクリックして、デバイスを同期します。

   - **[OK]** または **[キャンセル]** をクリックして、ダイアログ ボックスを閉じます。

    ![デバイスの詳細](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png)

## 個々のデバイスの更新

次の手順に従って、個々 の StorSimple デバイスを StorSimple Snapshot Manager と再同期します。

#### デバイスを更新するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **[スコープ]** ウィンドウで、**[デバイス]** をクリックします。

3. **[結果]** ウィンドウで、デバイスの名前を右クリックしてから **[デバイスの更新]** をクリックします。 これにより、デバイスが StorSimple Snapshot Manager と同期されます。

## デバイスの構成の削除

次の手順に従って、StorSimple Snapshot Manager から個々の StorSimple デバイスの構成を削除します。

#### デバイス構成を削除するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **[スコープ]** ウィンドウで、**[デバイス]** をクリックします。

3. **[結果]** ウィンドウで、デバイスの名前を右クリックしてから **[削除]** をクリックします。

4. 次のメッセージが表示されます。 **[はい]** をクリックして構成を削除するか、**[いいえ]** をクリックして削除をキャンセルします。

    ![デバイスの構成の削除](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## 有効期限が切れたデバイス パスワードの変更

パスワードを入力して、StorSimple Snapshot Manager で StorSimple デバイスを認証する必要があります。 このパスワードは、Windows PowerShell インターフェイスを使用してデバイスを設定するときに構成します。 ただし、パスワードは期限切れになる場合があります。 この場合、Azure クラシック ポータルを使用してパスワードを変更することができます。 次に、デバイスはパスワードが期限切れになる前に StorSimple Snapshot Manager で構成されていたため、デバイスを StorSimple Snapshot Manager で再認証する必要があります。

#### 有効期限が切れたパスワードを変更するには

1. Azure クラシック ポータルで StorSimple Manager サービスを開始します。

2. **[デバイス]**、**[デバイスの構成]** の順にクリックします。

3. StorSimple Snapshot Manager のセクションまでスクロールします。 14 から 15 文字のパスワードを入力します。 大文字、小文字、数字、および特殊文字を含むパスワードを使用してください。

4. 確認のためにパスワードを再入力します。

5. ページの下部にある **[保存]** をクリックします。

#### デバイスを再認証するには

1. StorSimple Snapshot Manager を開始します。

2. **[スコープ]** ウィンドウで、**[デバイス]** をクリックします。 構成されているデバイスの一覧が **[結果]** ウィンドウに表示されます。

3. デバイスを選択して、右クリックし、**[認証]** をクリックします。

4. **[認証]** ウィンドウに、新しいパスワードを入力します。

5. デバイスを選択して、右クリックし、**[デバイスの更新]** を選択します。 これにより、デバイスが StorSimple Snapshot Manager と同期されます。

## 障害が発生したデバイスの置き換え

StorSimple device に障害が発生し、スタンバイ (フェールオーバー) デバイスによって置き換えられている場合は、次の手順に従って新しいデバイスに接続し、関連するバックアップを表示します。

#### フェールオーバー後に新しいデバイスに接続するには

1. 新しいデバイスへの iSCSI 接続を再構成します。 手順についてを参照してください"手順 7: マウント、初期化、およびボリュームの形式"で [オンプレミス StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)します。

>[AZURE.NOTE] 新しい StorSimple デバイスの IP アドレスが古いものと同じ IP アドレスである場合は、古い構成を接続できる可能性があります。 

2. Microsoft StorSimple Management Service を停止します。

    1. Server Manager を起動します。

    2. Server Manager ダッシュボードの **[ツール]** メニューで、**[サービス]** を選択します。

    3. **[サービス]** ウィンドウで、**[Microsoft StorSimple Management Service]** を選択します。

    4. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスの停止]** をクリックします。

3. 古いデバイスに関連する構成情報を削除します。

    1. File Explorer で、C:\ProgramData\Microsoft\StorSimple\BACatalog を参照します。

    2. BACatalog フォルダーでファイルを削除します。

4. Microsoft StorSimple Management Service を再起動します。

    1. Server Manager ダッシュボードの **[ツール]** メニューで、**[サービス]** を選択します。

    2. **[サービス]** ウィンドウで、**[Microsoft StorSimple Management Service]** を選択します。

    3. 右側のウィンドウで、**[Microsoft StorSimple Management Service]** の下にある **[サービスを再起動する]** をクリックします。

5. StorSimple Snapshot Manager を開始します。

6. 新しい StorSimple デバイスを構成するには、ステップ 2 の手順を完了します。 で StorSimple デバイスを接続 [StorSimple Snapshot Manager の展開](storsimple-snapshot-manager-deployment.md)します。

7. **[スコープ]** ウィンドウの最上位ノード (StorSimple Snapshot Manager など) を右クリックし、**[インポートの表示の切り替え]** をクリックします。

8. インポートされたボリューム グループとバックアップが StorSimple Snapshot Manager に表示されると、メッセージが表示されます。 **[OK]** をクリックします。

## 次のステップ

- 学習方法 [StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理](storsimple-snapshot-manager-admin.md)します。
- 学習方法 [StorSimple Snapshot Manager を使用して表示してボリュームを管理](storsimple-snapshot-manager-manage-volumes.md)します。






