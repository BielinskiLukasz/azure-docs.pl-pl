<properties 
    pageTitle="チュートリアル: Azure Active Directory と Druva の統合 | Microsoft Azure" 
    description="Azure Active Directory と Druva を使用して、シングル サインオン、自動化されたプロビジョニング、および詳細を有効にする方法を説明します。" 
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


# チュートリアル: Azure Active Directory と Druva の統合

このチュートリアルでは、Azure と Druva の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Druva でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Druva に割り当てた Azure AD ユーザーは、Druva 会社サイト (サービス プロバイダーが開始したサインオン) で、アプリケーションにシングル サインオンまたはできるを使用して、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  Druva のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  ユーザー プロビジョニングの構成
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-druva-tutorial/IC795084.png "Scenario")
## Druva のアプリケーション統合の有効化

このセクションでは、Druva のアプリケーション統合を有効にする方法について説明します。

### Druva のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-druva-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-druva-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-druva-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に、「**Druva**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-druva-tutorial/IC795085.png "Application Gallery")

7.  結果ウィンドウで **[Druva]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Druva に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  
この手順に慣れていない場合は、次を参照してください。 [バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)します。

Druva アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性**の構成に追加する必要があります。  
次のスクリーンショットはその例です。

![SAML トークンの属性](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML Token Attributes")

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure Active Directory ポータルでの **Druva** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795027.png "Configure Single Sign-On")

2.  **どのようなユーザーが Druva へのサインオン** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、] をクリックし、 **次**します。

    ![Configure Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795088.png "Configure Single Sign-On")

3.  **アプリケーション URL の構成** ] ページの [、 **Druva サインオン URL** URL は、ユーザーが Druva アプリケーションにサインオンする使用] ボックスに、型 (例::"*https://cloud.druva.com/home/*")、順にクリック **次**します。

    ![アプリケーション URL の構成](./media/active-directory-saas-druva-tutorial/IC795089.png "Configure App URL")

4.  **Druva でのシングル サインオンの構成** ] ページで、証明書をダウンロードする] をクリックして **証明書のダウンロード**, 、お使いのコンピューターにローカルで証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795090.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、Druva 企業サイトに管理者としてログインします。

6.  移動して **管理 \ > 設定**します。

    ![設定](./media/active-directory-saas-druva-tutorial/IC795091.png "Settings")

7.  [シングル サインオンの設定] ダイアログで、次の手順を実行します。

    ![シングル サインオンの設定](./media/active-directory-saas-druva-tutorial/IC795092.png "Singl Sign-On Settings")

    1.  Azure ポータルの **[Druva でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** の値をコピーし、**[ID プロバイダーのログイン URL]** ボックスに貼り付けます。
    2.  Azure ポータルの **[Druva でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** の値をコピーし、**[ID プロバイダー ログアウト URL]** ボックスに貼り付けます。
    3.  ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。
        >[AZURE.TIP] 詳細については、「 [バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)

    4.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[ID プロバイダー証明書]** ボックスに貼り付けます。
    5.  **[設定]** ページを開くには、**[保存]** をクリックします。

8.  **[設定]** ページで、**[SSO トークンの生成]** をクリックします。

    ![設定](./media/active-directory-saas-druva-tutorial/IC795093.png "Settings")

9.  **[シングル サインオン認証トークン]** ダイアログで、次の手順を実行します。

    ![SSO トークン](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")

    1.  **[コピー]** をクリックします。
    2.  **[閉じる]** をクリックします。

10. Azure AD ポータルでのシングル サインオンの構成情報を選択し、 **完了** を閉じる、 **[シングル サインオン** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795095.png "Configure Single Sign-On")

11. 上部にあるメニュー [ **属性** を開くには、 **SAML トークン属性** ダイアログ。

    ![属性](./media/active-directory-saas-druva-tutorial/IC795096.png "Attributes")

12. 必要な属性のマッピングを追加するには、次の手順を実行します。

    | 属性名| 属性値|
    |---|---|
    | insync\_auth\_token| <*clipboard value*>|

    1.  上記のテーブルの各データ行で、**[ユーザー属性の追加]** をクリックします。
    2.  **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    3.  **[属性値]** ボックスに、その行に対して表示される属性値を入力します。
    4.  **[完了]** をクリックします。

13. **[変更の適用]** をクリックします。
## ユーザー プロビジョニングの構成

Azure AD ユーザーが Druva にログインできるようにするには、そのユーザーを Druva にプロビジョニングする必要があります。  
Druva の場合、プロビジョニングは手動で行います。

### ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Druva** 企業サイトに管理者としてログインします。

2.  移動して **管理 \ > ユーザー**します。

    ![Manage Users](./media/active-directory-saas-druva-tutorial/IC795097.png "Manage Users")

3.  **[Create New]** をクリックします。

    ![Manage Users](./media/active-directory-saas-druva-tutorial/IC795098.png "Manage Users")

4.  [新しいユーザーの作成] ダイアログで、次の手順を実行します。

    ![新しいユーザーの作成](./media/active-directory-saas-druva-tutorial/IC795099.png "Create NewUser")

    1.  関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザー アカウントの電子メール アドレスと名前を入力します。
    2.  **[ユーザーの作成]** をクリックします。

>[AZURE.NOTE] Druva から提供されている他の Druva ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Druva に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **Druva * * アプリケーション統合ページで、をクリックして * * ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-druva-tutorial/IC795100.png "Assign Users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-druva-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。





