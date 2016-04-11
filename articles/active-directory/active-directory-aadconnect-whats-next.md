<properties
    pageTitle="Azure AD Connect: 次のステップと Azure AD Connect の管理方法 | Microsoft Azure"
    description="Azure AD Connect の既定の構成と運用タスクを拡張する方法について説明します。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2015"
    ms.author="billmath"/>

# 次のステップと Azure AD Connect の管理方法
組織のニーズや要件を満たすように Azure Active Directory Connect をカスタマイズするための高度な運用に関するトピックを次に示します。  

## Azure AD Premium および Enterprise Mobility のユーザーにライセンスを割り当てる

ユーザーがクラウドに同期されたため、Office 365 などのクラウド アプリを利用できるように、そのユーザーにライセンスを割り当てる必要があります。

### Azure AD Premium または Enterprise Mobility Suite のライセンスを割り当てるには
--------------------------------------------------------------------------------
1. Azure ポータルに管理者としてサインインします。
2. 左側で、次のように選択します。 **Active Directory**します。
3. [Active Directory] ページで、有効にするユーザーが存在するディレクトリをダブルクリックします。
4. ディレクトリ ページの上部にある次のように選択します。 **ライセンス**します。
5. [ライセンス] ページで、[Active Directory Premium または Enterprise Mobility Suite を選択し、クリックして **割り当てる**します。
6. ダイアログ ボックスで、ライセンスを割り当てるユーザーを選択し、チェック マーク アイコンをクリックして変更を保存します。


## スケジュールされた同期タスクを確認する
同期の状態を確認する場合は、Azure ポータルで確認します。

### スケジュールされた同期タスクを確認するには
--------------------------------------------------------------------------------
1. Azure ポータルに管理者としてサインインします。
2. 左側で、次のように選択します。 **Active Directory**します。
3. [Active Directory] ページで、有効にするユーザーが存在するディレクトリをダブルクリックします。
4. ディレクトリ ページの上部にある次のように選択します。 **ディレクトリ統合**します。
5. [ローカル Active Directory との統合] で、最後の同期時刻を確認します。

<center>![クラウド](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## スケジュールされた同期タスクの開始
同期タスクを実行する必要がある場合は、Azure AD Connect ウィザードを再度実行して行うことができます。  Azure AD 資格情報を入力する必要があります。  ウィザードで、次のように選択します。、 **同期オプションをカスタマイズする** 作業し、ウィザードの次へ] をクリックします。 最後にあることを確認、 **初期構成が完了するとすぐに同期プロセスを開始** チェック ボックスをオンします。

<center>![クラウド](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>


## Azure AD Connect で利用可能なその他のタスク
Azure AD Connect の初回インストール後は、Azure AD Connect のスタート ページまたはデスクトップのショートカットから、いつでもウィザードを再び起動できます。  再度ウィザードを実行すると、追加のタスクの形式で新しいオプションがいくつか表示されます。  

次の表に、これらのタスクの概要と各タスクの簡単な説明を示します。

![参加ルール](./media/active-directory-aadconnect-whats-next/addtasks.png)


その他のタスク | 説明
------------- | ------------- |
選択したシナリオを表示する  |現在の Azure AD Connect のソリューションを表示できます。  これには、全般設定、同期されたディレクトリ、同期設定などが含まれます。
同期オプションをカスタマイズする | 構成への Active Directory フォレストの追加、ユーザー、グループ、デバイス、パスワード ライトバックなどの同期オプションの有効化など、現在の構成を変更できます。
ステージング モードを有効にする |  これを使用すると、後で同期される情報をステージングできますが、Azure AD または Active Directory には何もエクスポートされません。  事前に同期をプレビューすることができます。

## 次のステップ
詳細について [内部設置型 id と Azure Active Directory の統合](active-directory-aadconnect.md)します。

