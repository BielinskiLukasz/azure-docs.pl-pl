<properties 
    pageTitle="チュートリアル: Azure Active Directory と Zscaler Two の統合 | Microsoft Azure" 
    description="Azure Active Directory で Zscaler Two を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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


# チュートリアル: Azure Active Directory と Zscaler Two の統合

このチュートリアルでは、Azure と ZScaler Two の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   ZScaler Two でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、ZScaler Two に割り当てた Azure AD ユーザーができるよう、ZScaler Two 会社サイト (サービス プロバイダーが開始したサインオン)、または、を使用して、アプリケーションにシングル サインオン [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  ZScaler Two のアプリケーション統合の有効化
2.  シングル サインオンの構成
3.  プロキシ設定の構成
4.  ユーザー プロビジョニングの構成
5.  ユーザーの割り当て

![Configure Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800199.png "Configure Single Sign-On")

## ZScaler Two のアプリケーション統合の有効化

このセクションでは、ZScaler Two のアプリケーション統合を有効にする方法を説明します。

### ZScaler Two のアプリケーション統合を有効にするには、次の手順を実行します。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-zscaler-two-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-zscaler-two-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-zscaler-two-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-zscaler-two-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に、「**ZScaler Two**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-zscaler-two-tutorial/IC800200.png "Application Gallery")

7.  結果ウィンドウで **[ZScaler Two]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ZScaler Two](./media/active-directory-saas-zscaler-two-tutorial/IC800201.png "ZScaler Two")

## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで ZScaler Two に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書を ZScaler Two テナントにアップロードする必要があります。  
この手順に慣れていない場合は、を参照してください [バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルでの **ZScaler Two** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800202.png "Configure Single Sign-On")

2.  **どのようなユーザーが ZScaler Two へのサインオン** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、] をクリックし、 **次**します。

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800203.png "Configure Single Sign-On")

3.  **アプリケーション URL の構成** ] ページで、 **ZScaler Two サインオン URL** ] ボックスに、ユーザーが ZScaler Two アプリケーションにサインオンする際に使用する URL を入力し、順にクリックして **次**します。

    ![アプリケーション URL の構成](./media/active-directory-saas-zscaler-two-tutorial/IC800204.png "Configure App URL")
    >[AZURE.NOTE] ご使用の環境の実際の値は、ZScaler Two サポート チームから入手できます。

4.  **ZScaler Two でのシングル サインオンの構成** ] ページで、証明書をダウンロードする] をクリックして **証明書のダウンロード**, 、お使いのコンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800205.png "Configure Single Sign-On")

5.  別の Web ブラウザー ウィンドウで、ZScaler Two 企業サイトに管理者としてログインします。

6.  上部のメニューで **[管理]** をクリックします。

    ![管理](./media/active-directory-saas-zscaler-two-tutorial/IC800206.png "Administration")

7.  **[管理者とロールの管理]** をクリックし、**[ユーザーと認証の管理]** をクリックします。

    ![ユーザーと認証の管理](./media/active-directory-saas-zscaler-two-tutorial/IC800207.png "Manage Users & Authentication")

8.  **[組織の認証オプションの選択]** セクションで、次の手順を実行します。

    ![認証](./media/active-directory-saas-zscaler-two-tutorial/IC800208.png "Authentication")

    1.  **[SAML シングル サインオンを使用した認証]** を選択します。
    2.  **[SAML シングル サインオン パラメーターの構成]** をクリックします。

9.  **[SAML シングル サインオン パラメーターの構成]** ダイアログ ページで、次の手順に従い、**[完了]** をクリックします。

    ![シングル サインオン](./media/active-directory-saas-zscaler-two-tutorial/IC800209.png "Single Sign-On")

    1.  Azure ポータルの **[ZScaler Two でのシングル サインオンの構成]** ダイアログ ページで **[認証要求 URL]** の値をコピーし、それを **[認証のためにユーザーに表示される SAML ポータルの URL]** テキスト ボックスに貼り付けます。
    2.  **[ログイン名を含む属性]** テキスト ボックスに「**NameID**」と入力します。
    3.  ダウンロードした証明書をアップロードするには、**[Zscaler pem]** をクリックします。
    4.  **[SAML 自動プロビジョニングを有効にする]** を選択します。

10. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![管理](./media/active-directory-saas-zscaler-two-tutorial/IC800210.png "Administration")

    1.  **[保存]** をクリックします。
    2.  **[今すぐ認証する]** をクリックします。

11. Azure ポータルで、 **ZScaler Two でのシングル サインオンの構成** ] ダイアログ ページで、シングル サインオンの構成情報を選択し、 **完了**します。

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800211.png "Configure Single Sign-On")

## プロキシ設定の構成

### Internet Explorer でプロキシ設定を構成するには

1.  **Internet Explorer** を開始します。

2.  **[ツール]** メニューの **[インターネット オプション]** を選択し、**[インターネット オプション]** ダイアログを開きます。

    ![を開き](./media/active-directory-saas-zscaler-two-tutorial/IC769492.png "Internet Options")

3.  **[接続]** タブをクリックします。

    ![接続](./media/active-directory-saas-zscaler-two-tutorial/IC769493.png "Connections")

4.  **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5.  [プロキシ サーバー] セクションで、次の手順を実行します。

    ![プロキシ サーバー](./media/active-directory-saas-zscaler-two-tutorial/IC769494.png "Proxy server")

    1.  [LAN にプロキシ サーバーを使用する] をオンにします。
    2.  [アドレス] ボックスに「**gateway.zscalerone.net**」と入力します。
    3.  [ポート] ボックスに「**80**」と入力します。
    4.  **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。
    5.  **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

6.  **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

## ユーザー プロビジョニングの構成

Azure AD ユーザーが ZScaler Two にログインできるようにするには、そのユーザーを ZScaler Two にプロビジョニングする必要があります。  
ZScaler Two の場合、プロビジョニングは手動で行います。

### ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  **Zscaler** テナントにログインします。

2.  **[管理]** をクリックします。

    ![管理](./media/active-directory-saas-zscaler-two-tutorial/IC781035.png "Administration")

3.  **[ユーザー管理]** をクリックします。

    ![[追加] のいずれかを](./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Add")

4.  **[ユーザー]** タブで、**[追加]**.をクリックします。

    ![[追加] のいずれかを](./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Add")

5.  [ユーザーの追加] セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/active-directory-saas-zscaler-two-tutorial/IC781038.png "Add User")

    1.  プロビジョニングする有効な AAD アカウントの **[ユーザー ID]**、**[ユーザー表示名]**、**[パスワード]**、**[パスワードの確認]** に入力し、**[グループ]** と **[部署]** を選択します。
    2.  **[保存]** をクリックします。

>[AZURE.NOTE] ZScaler Two から提供されている他の ZScaler Two ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

## ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを ZScaler Two に割り当てるには、次の手順を実行します。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **ZScaler Two** アプリケーション統合ページで、をクリックして **ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-zscaler-two-tutorial/IC800212.png "Assign Users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-zscaler-two-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。




