<properties 
   pageTitle="StorSimple Snapshot Manager のバックアップ ポリシー | Microsoft Azure"
   description="StorSimple Snapshot Manager MMC スナップインを使用して、スケジュールされたバックアップを管理するバックアップ ポリシーを作成し、管理する方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/15/2015"
   ms.author="v-sharos" />


# StorSimple Snapshot Manager を使用したバックアップ ポリシーの作成と管理

## 概要

バックアップ ポリシーでは、ローカルまたはクラウドのボリューム データをバックアップするスケジュールを作成します。 バックアップ ポリシーを作成する際に、アイテム保持ポリシーも指定できます  (最大 64 個のスナップショットを保持できます)。 バックアップ ポリシーの詳細については、次を参照してください。 [種類とバックアップ ポリシーのバックアップ](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies)します。

このチュートリアルでは、次の方法について説明します。

- バックアップ ポリシーの作成
- バックアップ ポリシーの編集
- バックアップ ポリシーの削除

## バックアップ ポリシーの作成

新しいバックアップ ポリシーを作成するには、次の手順に従います。

#### バックアップ ポリシーを作成するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **スコープ** ウィンドウで **[バックアップ ポリシー]** を右クリックし、**[バックアップ ポリシーの作成]** をクリックします。

    ![バックアップ ポリシーの作成](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    **[ポリシーの作成]** ダイアログ ボックスが表示されます。

    ![[ポリシーの作成] - ](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. **[全般]** タブで、次の情報を入力します。

   1. **[名前]** ボックスに、ポリシーの名前を入力します。

   2. ** ボリューム グループ **ボリューム グループの名前が、ポリシーに関連付けられているテキスト ボックスに「します。

   3. **[ローカル スナップショット]** または **[クラウド スナップショット]** を選択します。

   4. 保持するスナップショットの数を選択します。 **[すべて]** を選択すると、64 個のスナップショットが保持されます (最大)。

4. **[スケジュール]** タブをクリックします。

    ![[ポリシーの作成] - ](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. **[スケジュール]** タブで、次の情報を入力します。

   1. **[有効化]** チェック ボックスをオンにし、次回のバックアップをスケジュールします。

   2. **[設定]** で、**[1 回限り]**、**[毎日]**、**[毎週]**、または **[毎月]** を選択します。

   3. **[開始]** ボックスで、カレンダー アイコンをクリックし、開始日を選択します。

   4. **[詳細設定]** では、必要に応じて、繰り返しスケジュールと終了日を設定できます。

   5. **[OK]** をクリックします。

バックアップ ポリシーを作成すると、**結果**ウィンドウに次の情報が表示されます。

- **[名前]** - バックアップ ポリシーの名前。

- **[種類]** - ローカル スナップショットまたはクラウド スナップショット。

- **[ボリューム グループ]** - ポリシーに関連付けられているボリューム グループ。

- **[保有期間]** - 保持するスナップショットの数。最大 64 個です。

- **[作成日]** - このポリシーの作成日。

- **[有効化]** - ポリシーが現在有効になっているかどうか。**True** はポリシーが有効になっていることを示し、**False** は無効になっていることを示します。

## バックアップ ポリシーの編集

既存のバックアップ ポリシーを編集するには、次の手順に従います。

#### バックアップ ポリシーを編集するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **スコープ** ウィンドウで **[バックアップ ポリシー]** ノードをクリックします。 **結果**ウィンドウにすべてのバックアップ ポリシーが表示されます。

3. 編集するポリシーを右クリックし、**[編集]** をクリックします。

    ![バックアップ ポリシーの編集](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)

4. **[ポリシーの作成]** ウィンドウが表示されたら、変更内容を入力し、**[OK]** をクリックします。

## バックアップ ポリシーの削除

バックアップ ポリシーを削除するには、次の手順に従います。

#### バックアップ ポリシーを削除するには

1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。

2. **スコープ** ウィンドウで **[バックアップ ポリシー]** ノードをクリックします。 **結果**ウィンドウにすべてのバックアップ ポリシーが表示されます。

3. 削除するバックアップ ポリシーを右クリックし、**[削除]** をクリックします。 
e
4. 確認メッセージが表示されたら、**[はい]** をクリックします。

    ![バックアップ ポリシーの削除の確認](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## 次のステップ

- 学習方法 [StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理](storsimple-snapshot-manager-admin.md)します。
- 学習方法 [StorSimple Snapshot Manager を使用して、バックアップ ジョブを表示および管理](storsimple-snapshot-manager-manage-backup-jobs.md)します。




