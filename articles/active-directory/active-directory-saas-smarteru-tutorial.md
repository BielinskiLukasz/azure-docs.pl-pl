<properties 
    pageTitle="チュートリアル: Azure Active Directory と SmarterU の統合 | Microsoft Azure" 
    description="Azure Active Directory で SmarterU を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />


# チュートリアル: Azure Active Directory と SmarterU の統合

このチュートリアルでは、Azure と SmarterU の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   SmarterU テナント

このチュートリアルを完了すると、SmarterU に割り当てた Azure AD ユーザーは、SmarterU 会社サイト (サービス プロバイダーが開始したサインオン) で、アプリケーションにシングル サインオンまたはできるを使用して、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  SmarterU のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scenario")

## SmarterU のアプリケーション統合の有効化

このセクションでは、SmarterU のアプリケーション統合を有効にする方法について説明します。

### SmarterU のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-smarteru-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-smarteru-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-smarteru-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に、「**SmarterU**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-smarteru-tutorial/IC777321.png "Application fallery")

7.  結果ウィンドウで **[SmarterU]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

## シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、SmarterU で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルでの **SmarterU** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configure Single Sign-On")

2.  **どのようなユーザーの SmarterU へのアクセス** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、] をクリックし、 **次**します。

    ![Configure Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configure Single Sign-On")

3.  **SmarterU でのシングル サインオンの構成** ] ページで、メタデータをダウンロードする] をクリックして **メタデータのダウンロード**, 、し、データ ファイルをローカルでとして **c:\\SmarterUMetaData.cer**します。

    ![Configure Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configure Single Sign-On")

4.  別の Web ブラウザー ウィンドウで、SmarterU 企業サイトに管理者としてログインします。

5.  上部のメニューで **[Account Settings]** をクリックします。

    ![アカウント設定](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Account Settings")

6.  アカウント構成ページで、次の手順に従います。

    ![外部認証](./media/active-directory-saas-smarteru-tutorial/IC777327.png "External Authorization")

    1.  **[Enable External Authorization]** を選択します。
    2.  **[Master Login Control]** セクションで、**[SmarterU]** タブを選択します。
    3.  **[User Default Login]** セクションで、**[SmarterU]** タブを選択します。
    4.  **[Enable Okta]** を選択します。
    5.  ダウンロードしたメタデータ ファイルの内容をコピーし、**[Okta Metadata]** テキスト ボックスに貼り付けます。
    6.  **[保存]** をクリックします。

7.  Azure AD ポータルでのシングル サインオンの構成情報を選択し、 **完了** を閉じる、 **[シングル サインオン** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configure Single Sign-On")

## ユーザー プロビジョニングの構成

Azure AD ユーザーが SmarterU にログインできるようにするには、そのユーザーを SmarterU にプロビジョニングする必要があります。  
SmarterU の場合、プロビジョニングは手動で行います。

### ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1.  **SmarterU** テナントにログインします。

2.  **[Users]** タブに移動します。

3.  ユーザー セクションで、次の手順に従います。

    ![新しいユーザー](./media/active-directory-saas-smarteru-tutorial/IC777329.png "New User")

    1.  **[+User]** をクリックします。
    2.  次のテキスト ボックスに、Azure AD のユーザー アカウントの関連する属性の値を入力します。**[Primary Email]**、**[Employee ID]**、**[Password]**、**[Verify Password]**、**[Given Name]**、**[Surname]**。
    3.  **[Active]** をクリックします。
    4.  **[保存]** をクリックします。

>[AZURE.NOTE] SmarterU から提供されている他の SmarterU ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを SmarterU に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **SmarterU * * アプリケーション統合ページで、をクリックして * * ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-smarteru-tutorial/IC777330.png "Assign Users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-smarteru-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。




