<properties 
    pageTitle="チュートリアル: Azure AD と Sciforma の統合 | Microsoft Azure" 
    description="Azure Active Directory で Sciforma を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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


# チュートリアル: Azure AD と Sciforma の統合

このチュートリアルでは、Azure と Sciforma の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Sciforma テナント

このチュートリアルを完了すると、Sciforma に割り当てた Azure AD ユーザーは、Sciforma 会社サイト (サービス プロバイダーが開始したサインオン) で、アプリケーションにシングル サインオンまたはできるを使用して、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Sciforma のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")
## Sciforma のアプリケーション統合の有効化

このセクションでは、Sciforma のアプリケーション統合を有効にする方法について説明します。

### Sciforma のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に、「**Sciforma**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Application gallery")

7.  結果ウィンドウで **[Sciforma]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Sciforma に対する認証を行えるようにする方法について説明します。

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルでの **Sciforma** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![シングル サインオンの構成](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configure single sign-on")

2.  **どのようなユーザーを Sciforma へのサインオン** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、] をクリックし、 **次**します。

    ![シングル サインオンの構成](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configure single sign-on")

3.  **アプリケーション URL の構成** ] ページで、 **Sciforma サインイン URL** ] ボックスに、次のパターンを使用して URL を入力"* https://\<tenant-name\>します。Sciforma.com*"、クリックして **次**します。

    ![アプリケーション URL の構成](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configure app URL")

4.  **Sciforma でのシングル サインオンの構成** ] ページで、メタデータをダウンロードする] をクリックして **メタデータのダウンロード**, 、し、データ ファイルをローカルでとして **c:\\SciformaMetaData.xml**します。

    ![シングル サインオンの構成](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configure single sign-on")

5.  メタデータ ファイルを Sciforma サポート チームに転送します。 サポート チームは、シングル サインオンを構成する必要があります。

6.  シングル サインオンの構成情報を選択し、 **完了** を閉じる、 **[シングル サインオン** ダイアログ。

    ![シングル サインオンの構成](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configure single sign-on")
## ユーザー プロビジョニングの構成

Sciforma へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当て済みユーザーがアクセス パネルを使用して Sciforma にログインしようとすると、そのユーザーが存在するかどうかが Sciforma によって確認されます。  
使用可能なユーザー アカウントがない場合、ユーザー アカウントは Sciforma により自動的に作成されます。
## ユーザーを割り当てる

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Sciforma に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Sciforma * * アプリケーション統合ページで、をクリックして * * ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assign users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。




