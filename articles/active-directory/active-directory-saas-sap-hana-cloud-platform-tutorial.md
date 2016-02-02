<properties 
    pageTitle="チュートリアル: Azure Active Directory と SAP HANA Cloud Platform の統合 | Microsoft Azure" 
    description="Azure Active Directory で SAP HANA Cloud Platform を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。" 
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


# チュートリアル: Azure Active Directory と SAP HANA Cloud Platform の統合

このチュートリアルでは、Azure と SAP HANA Cloud Platform の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   SAP HANA Cloud Platform アカウント

このチュートリアルを完了すると、SAP HANA Cloud Platform に割り当てた Azure AD ユーザーができるようにアプリケーションを使用して、シングル サインオン、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。
>[AZURE.IMPORTANT]シングル サインオンをテストするには、独自のアプリケーションをデプロイするか、SAP HANA Cloud Platform アカウントでアプリケーションをサブスクライブする必要があります。 このチュートリアルでは、アプリケーションはアカウントにデプロイされます。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1.  SAP HANA Cloud Platform でアプリケーション統合を有効にする
2.  シングル サインオンの構成
3.  ユーザーにロールを割り当てる
4.  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario")
## SAP HANA Cloud Platform でアプリケーション統合を有効にする

このセクションでは、SAP HANA Cloud Platform のアプリケーション統合を有効にする方法について説明します。

### SAP HANA Cloud Platform のアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![アプリケーション](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")

4.  ページの下部にある **[追加]** をクリックします。

    ![アプリケーションの追加](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Add application")

5.  **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Add an application from gallerry")

6.  **検索ボックス**に、「**SAP HANA Cloud Platform**」と入力します。

    ![アプリケーション ギャラリー](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Application Gallery")

7.  結果ウィンドウで **[SAP HANA Cloud Platform]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで SAP HANA Cloud Platform に対する認証を行えるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書を SAP HANA Cloud Platform テナントにアップロードする必要があります。  
この手順に慣れていない場合は、を参照してください [バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)

### シングル サインオンを構成するには、次の手順に従います。

1.  Azure AD ポータルでの **SAP HANA Cloud Platform** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 ** [シングル サインオン ** ダイアログ。

    ![シングル サインオンの構成](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configure single sign-on")

2.  **どのようなユーザーを SAP HANA Cloud Platform へのサインオン** ] ページで、[ **Microsoft Azure AD シングル サインオン**, 、] をクリックし、 **次**します。

    ![Configure Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configure Single Sign-On")

3.  別の web ブラウザーのウィンドウでへのサインオン SAP HANA Cloud Platform コックピットに https://account。 \<landscape host\>.ondemand.com/cockpit (例:: *https://account.hanatrial.ondemand.com/cockpit*)。

4.  **[Trust (信頼)]** タブをクリックします。

    ![信頼](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Trust")

5.  信頼管理セクションで、次の手順に従います。

    ![メタデータの取得](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Get Metadata")

    1.  **[Local Service Provider (ローカル サービス プロバイダー)]** タブをクリックします。
    2.  SAP HANA クラウド プラットフォームのメタデータ ファイルをダウンロードするには、**[Get Metadata (メタデータの取得)]** をクリックします。

6.  Azure Active Directory ポータルでの **アプリケーション URL の構成** ] ページで、次の手順を実行して順にクリックして **次**します。

    ![アプリケーション URL の構成](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configure App URL")

    1.  **サインオン URL** URL は、ユーザーへのサインインに使用] ボックスに、型、 **SAP HANA Cloud Platform** アプリケーションです。これは、SAP HANA Cloud Platform アプリケーションで保護されたリソースのアカウント固有の URL です。URL は次のパターンに基づいている: * https://\<applicationName\>\<accountName\>. \<landscape host\>.ondemand.com/\<path\_to\_protected\_resource\>* (例:: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)
        >[AZURE.NOTE]これは、ユーザーが認証を必要とする SAP HANA Cloud Platform アプリケーションの URL です。

    2.  ダウンロードした SAP HANA Cloud Platform のメタデータ ファイルを開き、**ns3:AssertionConsumerService** タグを見つけます。
    3.  **Location** 属性の値をコピーして、**[SAP HANA Cloud Platform 応答 URL]** テキストボックスに貼り付けます。

7.  **SAP HANA Cloud Platform でのシングル サインオンの構成** ] ページで、メタデータをダウンロードする] をクリックして **メタデータのダウンロード**, 、お使いのコンピューター上のファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configure Single Sign-On")

8.  SAP HANA Cloud Platform コックピットに、 **Local Service Provider** セクションで、次の手順に従います。

    ![信頼管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Trust Management")

    1.  **[編集]** をクリックします。
    2.  **[構成の種類]** として **[カスタム]** を選択します。
    3.  **[Local Provider Name (ローカル プロバイダー名)]** は既定値のままにします。
    4.  **署名キー**と**署名証明書**キーのペアを生成するには、**[Generate Key Pair (キー ペアの生成)]** をクリックします。
    5.  **[Principal Propagation (プリンシパル伝達)]** に **[無効]** を選択します。
    6.  **[Force Authentication (強制認証)]** に **[無効]** を選択します。
    7.  **[保存]** をクリックします。

9.  **[信頼できる ID プロバイダー]** タブをクリックし、**[Add Trusted Identity Provider (信頼できる ID プロバイダーの追加)]** をクリックします。

    ![信頼管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Trust Management")
    >[AZURE.NOTE]信頼できる id プロバイダーの一覧を管理するには、Local Service Provider] セクションで、カスタム構成の種類を選択する必要があります。 既定の構成の種類には、SAP ID サービスを編集不可能で暗黙的な信頼関係が必要です。 None の場合、すべての信頼の設定はありません。

10. **[全般]** タブをクリックし、**[参照]** をクリックして、ダウンロードしたメタデータ ファイルをアップロードします。

    ![信頼管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Trust Management")
    >[AZURE.NOTE] メタデータ ファイルをアップロードすると、**[シングル サインオン URL]**、**[Single Logout URL (シングル ログアウト URL)]**、**[署名証明書]** の値が自動的に設定されます。

11. **[属性]** タブをクリックします。

12. **[属性]** タブで、次の手順に従います。

    ![属性](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributes")

    1.  **[Add Assertion-Based Attribute (アサーション ベースの属性の追加)]** をクリックして、次のアサーション ベースの属性を追加します。

        | アサーション属性| プリンシパル属性|
        |-------------------|--------------------|
        | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname| firstname| --------------------| --------------------|
        | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname| lastname| -----------|
        | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress| email|


    >[AZURE.NOTE]属性の構成は、HCP 上のアプリケーションがどのように作成されているかに依存します。つまり、SAML 応答で必要になる属性の種類や、コード内でこの属性にアクセスする際に使用される名前 (プリンシパル属性) によって異なります。
    >  
    >a. スクリーンショットの **[既定の属性]** に指定されている値はサンプルです。 このシナリオでは必要ありません。  
    >
    >b. スクリーンショットに表示されている **[プリンシパル属性]** の名前と値は、アプリケーションがどのように作成されているかに依存します。 使用するアプリケーションによって、異なるマッピングが必要になる場合があります。

13. Azure ポータルで、 **SAP HANA Cloud Platform でのシングル サインオンの構成** ] ダイアログ ページで、シングル サインオンの構成情報を選択してクリックして **完了**します。

    ![Configure Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configure Single Sign-On")

オプションの手順として、Azure Active Directory ID プロバイダーのアサーション ベースのグループを構成できます。
>[AZURE.NOTE]SAP HANA Cloud Platform でグループを使用すると、SAP HANA Cloud Platform アプリケーションで、1 つ以上のロールに 1 人以上のユーザーを動的に割り当てることができます。この割り当ては SAML 2.0 アサーションの属性値によって決定されます。 たとえば、アサーションに属性 "*contract=temporary*" が含まれている場合、対象となるすべてのユーザーがグループ "*TEMPORARY*" に追加されます。 グループ "*TEMPORARY*" には、SAP HANA Cloud Platform アカウントにデプロイされた 1 つ以上のアプリケーションからの 1 つ以上のロールが含まれます。
>  
>SAP HANA Cloud Platform アカウントでアプリケーションの 1 つ以上のロールに多くのユーザーを一括で割り当てる場合は、アサーション ベースのグループを使用します。 直接割り当てることをお勧め (a) 特定の役割に割り当てるユーザーの 1 人または少数の数だけの場合、"**承認**"SAP HANA Cloud Platform コックピットのタブをクリックします。

## ユーザーにロールを割り当てる

Azure AD ユーザーが SAP HANA Cloud Platform にログインできるようにするには、SAP HANA Cloud Platform のロールをそれらのユーザーに割り当てる必要があります。

### ロールをユーザーに割り当てるには、次の手順に従います。

1.  **SAP HANA Cloud Platform** コックピットにログインします。

2.  次の手順に従います。

    ![承認](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Authorizations")

    1.  **[Authorization]** をクリックします。
    2.  **[Users]** タブをクリックします。
    3.  **[User]** テキストボックスに、ユーザーのメール アドレスを入力します。
    4.  **[Assign]** をクリックしてユーザーをロールに割り当てます。
    5.  **[保存]** をクリックします。

## ユーザーを割り当てる

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを SAP HANA Cloud Platform に割り当てるには、次の手順に従います。

1.  Azure AD ポータルで、テスト アカウントを作成します。

2.  **SAP HANA Cloud Platform** アプリケーション統合ページで、をクリックして **ユーザーの割り当て**します。

    ![ユーザーの割り当て](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assign Users")

3.  テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。

    ![はい](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、次を参照してください。 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)します。




