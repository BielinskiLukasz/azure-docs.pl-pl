<properties 
    pageTitle="チュートリアル: Azure Active Directory と Citrix GoToMeeting の統合 | Microsoft Azure" 
    description="Azure Active Directory で Citrix GoToMeeting を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/01/2015" 
    ms.author="markvi" />


# チュートリアル: Azure Active Directory と Citrix GoToMeeting の統合

適用対象: Azure
>[AZURE.TIP]クリックして、フィードバック [ここ](http://go.microsoft.com/fwlink/?LinkId=522412)します。

このチュートリアルでは、Azure と Citrix GoToMeeting の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Citrix GoToMeeting のテナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Citrix GoToMeeting のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")
## Citrix GoToMeeting のアプリケーション統合の有効化

このセクションでは、Citrix GoToMeeting のアプリケーション統合を有効にする方法を説明します。

### Citrix GoToMeeting のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Add an application from gallery")

6.  **検索ボックス**に、「**Citrix GoToMeeting**」と入力します。

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  結果ウィンドウで **[Citrix GoToMeeting]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
## シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Citrix GoToMeeting に対する認証を行えるようにする方法を説明します。  
この手順の途中で、Base-64 でエンコードされた証明書を Citrix GoToMeeting テナントにアップロードする必要があります。  
この手順に慣れていない場合は、次を参照してください。 [バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)します。

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルの **Citrix GoToMeeting** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![シングル サインオンの有効化](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Enable single sign-on")

2.  **[ユーザーの Citrix GoToMeeting へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択します。

    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configure single sign-on")

3. **[アプリ設定の構成]** ページで、**[次へ]** をクリックします。<br><br>![シングル サインオンを有効にします。](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Enable single sign-on")

4.  **[Citrix GoToMeeting でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックしてから証明書ファイルをコンピューターに保存します。

    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configure single sign-on")

5.  別のブラウザー ウィンドウで、Citrix 組織センターにログイン ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/))します。

6. クリックして、 **Id プロバイダー** タブをクリックし、次の手順を実行します  <br><br> ![SAML のセットアップ](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")。

     6.1. **[手動]** を選択します。

     6.2. Azure ポータルで、**[Citrix GoToMeeting でのシングル サインオンの構成]** ダイアログ ページの **[サインイン ページの URL]** の値をコピーし、**[サインイン ページの URL]** ボックスに貼り付けます。

     6.3. Azure ポータルで、**[Citrix GoToMeeting でのシングル サインオンの構成]** ダイアログ ページの **[サインアウト ページの URL]** の値をコピーし、**[サインアウト ページの URL]** ボックスに貼り付けます。

     6.4. Azure ポータルで、**[Citrix GoToMeeting でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[ID プロバイダー エンティティ ID]** ボックスに貼り付けます。

     6.5. ダウンロードした証明書をアップロードするには、**[証明書のアップロード]** をクリックします。

     6.6. **[保存]** をクリックします。

6.  Azure AD ポータルでのシングル サインオンの構成情報を選択し、 **次**します。

    ![シングル サインオンの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configure single sign-on")

7. **シングル サインオンによる確認** ] ページで [ **完了**します。<br><br>![SAML のセットアップ](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML setup")





## ユーザー プロビジョニングの構成

このセクションでは、Citrix GoToMeeting への Active Directory ユーザー アカウントのプロビジョニングを有効にする方法を説明します。

### ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  Microsoft Azure 管理ポータルの **Citrix GoToMeeting** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして **[ユーザー プロビジョニングの構成]** ダイアログを開きます。

    ![Configure user provisioning](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure user provisioning")

2.  **[設定と管理者資格情報]** ページで、次の手順を実行します。

    ![ユーザー プロビジョニングの構成](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure user provisioning")

     2.1. **[Citrix GoToMeeting 管理ユーザー名]** ボックスに、管理者のユーザー名を入力します。

     2.2. **[Citrix GoToMeeting 管理パスワード]** ボックスに、管理者のパスワードを入力します。

     2.3. **[次へ]** をクリックします。

3.  **[確認]** ページで、チェックマークをクリックして構成を保存します。

4.  **[検証]** ボタンをクリックして、構成を検証します。
## ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、ユーザーにアプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Citrix GoToMeeting に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Citrix GoToMeeting** アプリケーション統合ページで、をクリックして **ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assign users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Yes")

10 分間待機し、アカウントが Dropbox for Business に同期されていることを確認します。

最初の検証手順として、D のダッシュ ボード] をクリックして、プロビジョニングの状態を確認することができます、 **Citrix GoToMeeting** アプリケーション統合ページで、Azure 管理ポータルで。

![ダッシュボード](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

![統合状態](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration status")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。

アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)します。





