<properties
   pageTitle="チュートリアル: Azure Active Directory と Salesforce の統合 | Microsoft Azure"
   description="Azure Active Directory で Salesforce を使用してシングル サインオンや自動化されたプロビジョニングなどを有効にする方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/20/2015"
   ms.author="liviodlc"/>


# チュートリアル: Azure Active Directory と Salesforce を統合する方法

このチュートリアルでは、Azure Active Directory に Salesforce 環境を接続する方法を説明します。 Salesforce へのシングル サインオンを構成する方法、自動化されたユーザー プロビジョニングを有効にする方法、Salesforce へのアクセス権をユーザーに割り当てる方法などについて説明します。

## 前提条件

1. Azure Active Directory にアクセスする、 [Azure 管理ポータル](https://manage.windowsazure.com), 、有効な Azure サブスクリプションが必要です。

2. 有効なテナントが必要 [Salesforce.com](https://www.salesforce.com/)します。

> [AZURE.IMPORTANT] Salesforce.com の**試用**アカウントを使用している場合は、自動化されたユーザー プロビジョニングを構成できません。 試用アカウントの場合、アカウントを購入するまでは、必要な API にアクセスできません。
> 
> この制限を回避するを使用して、 [無料の開発者アカウント](https://developer.salesforce.com/signup) をこのチュートリアルを完了します。

Salesforce Sandbox 環境を使用している場合を参照してください、 [Salesforce Sandbox の統合に関するチュートリアル](https://go.microsoft.com/fwLink/?LinkID=521879)します。

## ビデオ チュートリアル

以下のビデオを使用して、このチュートリアルの手順に従います。

**ビデオ チュートリアル第 1 部: シングル サインオンを有効にする方法**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**ビデオ チュートリアル第 2 部: ユーザー プロビジョニングを自動化する方法**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

## 手順 1. Salesforce をディレクトリに追加する

1. [Azure 管理ポータル](https://manage.windowsazure.com), 、左側のナビゲーション ウィンドウで、次のようにクリックします。 **Active Directory**します。

    ![Select Active Directory from the left navigation pane.][0]

2. **[ディレクトリ]** ボックスの一覧から、Salesforce を追加するディレクトリを選択します。

3. 上部のメニューで **[アプリケーション]** をクリックします。

    ![Click on Applications.][1]

4. ページの下部にある **[追加]** をクリックします。

    ![新しいアプリケーションを追加します] をクリックします。][2]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![Click Add an application from the gallery.][3]

6. **検索ボックス**に「**Salesforce**」と入力します。 次に、結果の一覧から **[Salesforce]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Salesforce の追加][4]

7. これで、Salesforce の [クイック スタート] ページが表示されます。

    ![Azure AD の Salesforce の ][5]

## 手順 2. シングル サインオンを有効にする

1. シングル サインオンを構成する前に、Salesforce 環境用のカスタム ドメインを設定し、デプロイする必要があります。手順を実行する方法については、次を参照してください。 [ドメイン名の設定](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US)します。

2. Azure AD の Salesforce の [クイック スタート] ページで、**[シングル サインオンの構成]** ボタンをクリックします。

    ![The configure single sign-on button][6]

3. ダイアログが開き、ユーザーが Salesforce にサインオンする方法について質問する画面が表示されます。 **[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

    ![Select Azure AD Single Sign-On][7]
    > [AZURE.NOTE] さまざまなシングル サインオンのオプション、詳細について [ここをクリックして](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

4. **[アプリケーション設定の構成]** ページで **[サインオン URL]** に入力します。その場合、次の形式を使用して Salesforce ドメインの URL を入力します。
 - エンタープライズ アカウント: `https://&lt;domain&gt;.my.salesforce.com`
 - 開発者アカウント: `https://&lt;domain&gt;-dev-ed.my.salesforce.com`

    ![自分のサインオン URL を入力][8]

5. **[Salesforce でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。

    ![証明書のダウンロード][9]

6. ブラウザーで新しいタブを開き、Salesforce の管理者アカウントにログインします。

7. **[管理者]** ナビゲーション ウィンドウで **[セキュリティ コントロール]** をクリックし、関連するセクションを展開します。 次に、**[シングル サインオンの設定]** をクリックします。

    ![[セキュリティ コントロール] から ][10]

8. **[シングル サインオンの設定]** ページで **[編集]** ボタンをクリックします。

    ![[編集] ボタンをクリック][11]
    > [AZURE.NOTE] Salesforce アカウント用のシングル サインオン設定を有効にできない場合は、必要に応じて、この機能を有効にするために Salesforce のサポートにお問い合わせください。

9. **[SAML 有効]** を選択し、**[保存]** をクリックします。

    ![[SAML 有効] を選択][12]

10. SAML のシングル サインオン設定を構成するには、**[新規]** をクリックします。

    ![[SAML 有効] を選択][13]

11. **[SAML シングル サインオン設定の編集]** ページで、次の構成を行います。

    ![作成すべき構成のスクリーンショット][14]

 - **[名前]** フィールドに、この構成の表示名を入力します。 **[名前]** の値を指定すると、**[API 名]** ボックスが自動的に入力されます。

 - Azure AD で **[発行者の URL]** の値をコピーし、Salesforce の **[発行者]** フィールドに貼り付けます。

 - **[エンティティ ID]** ボックスに、次のパターンを使用して Salesforce のドメイン名を入力します。
     - エンタープライズ アカウント: `https://&lt;domain&gt;.my.salesforce.com`
     - 開発者アカウント: `https://&lt;domain&gt;-dev-ed.my.salesforce.com`

 - **[参照]** または **[ファイルの選択]** をクリックして **[アップロードするファイルの選択]** ダイアログを開きます。Salesforce の証明書を選択してから、**[開く]** をクリックして証明書をアップロードします。

 - **[SAML ID の種類]** で、**[アサーションに salesforce.com ユーザーの名前を含める]** を選択します。

 - **[SAML ID の場所]** で、**[ID を Subject ステートメントの NameIdentifier 要素にする]** チェックボックスをオンにします。

 - Azure AD で **[リモート ログイン URL]** の値をコピーし、Salesforce の **[ID プロバイダーのログイン URL]** フィールドに貼り付けます。

 - **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP リダイレクト]** を選択します。

 - 最後に **[保存]** をクリックし、SAML シングル サインオンの設定を適用します。

12. Salesforce の左側のナビゲーション ウィンドウで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックします。

    ![[マイ ドメイン] をクリック][15]

13. **[認証の構成]** セクションまで下へスクロールし、**[編集]** ボタンをクリックします。

    ![[編集] ボタンをクリック][16]

14. **[認証サービス]** セクションで SAML SSO 構成の表示名を選択し、**[保存]** をクリックします。

    ![自分の SSO 構成を選択][17]
    > [AZURE.NOTE] 複数の認証サービスを選択した場合、ユーザーが Salesforce 環境へのシングル サインオンを開始しようとすると、サインインに使用する認証サービスを選択するよう要求されます。 このメッセージが表示されないようにするには、**その他すべての認証サービスをオフのままに**しておいてください。

15. Salesforce にアップロードした証明書を有効にするには、Azure AD でシングル サインオンの構成確認のチェック ボックスをオンにします。 その後、**[次へ]** をクリックします。

    ![Check the confirmation checkbox][18]

16. ダイアログの最後のページでは、このシングル サインオン構成のメンテナンスに関連するエラーと警告の電子メール通知を受信する場合、電子メール アドレスを入力します。

    ![Type in your email address.][19]

17. **[完了]** をクリックしてダイアログを閉じます。 構成をテストするには、以下の「セクションを参照してください。 [Salesforce にユーザーを割り当てる](#step-4-assign-users-to-salesforce)します。

## 手順 3. 自動化されたユーザー プロビジョニングを有効にする

1. Azure AD の Salesforce の [クイック スタート] ページで、**[ユーザー プロビジョニングの構成]** ボタンをクリックします。

    ![[ユーザー プロビジョニングの構成] ボタンをクリック][20]

2. **[ユーザー プロビジョニングの構成]** ダイアログで、Salesforce の管理者のユーザー名とパスワードを入力します。

    ![管理者のユーザー名またはパスワードを入力][21]
    > [AZURE.NOTE] 運用環境を構成している場合は、特にこの手順で Salesforce の新しい管理者アカウントを作成することをお勧めします。 このアカウントには、Salesforce の**システム管理者**プロファイルが割り当てられている必要があります。

3. Salesforce のセキュリティ トークンを取得するには、新しいタブを開き、同じ Salesforce の管理者アカウントにサインインします。 ページの右上にある自分の名前をクリックし、**[個人の設定]** をクリックします。

    ![自分の名前をクリックして、][22]

4. 左側のナビゲーション ウィンドウで **[個人用]** をクリックして関連するセクションを展開し、**[個人のセキュリティ トークンのリセット]** をクリックします。

    ![自分の名前をクリックして、][23]

5. **[個人のセキュリティ トークンのリセット**] ページで、**[セキュリティ トークンのリセット]** ボタンをクリックします。

    ![警告文を確認][24]

6. この管理アカウントに関連付けられている電子メールの受信トレイを確認します。 新しいセキュリティ トークンが記載された Salesforce.com からの電子メールを探します。

7. トークンをコピーして Azure AD のウィンドウに移動し、**[ユーザー セキュリティ トークン]** フィールドに貼り付けます。 その後、**[次へ]** をクリックします。

    ![セキュリティ トークンへの貼り付け][25]

8. 確認ページで、プロビジョニング エラー発生時の電子メール通知の受信を選択できます。 **[完了]** をクリックしてダイアログを閉じます。

    ![お知らせを受け取るメール アドレスを入力][26]

## 手順 4. Salesforce にユーザーを割り当てる

1. 構成をテストするために、まずディレクトリに新しいテスト アカウントを作成します。

2. Salesforce の [クイック スタート] ページで **[ユーザーの割り当て]** ボタンをクリックします。

    ![Click on Assign Users][27]

3. テスト ユーザーを選択し、画面の下部にある **[割り当て]** ボタンをクリックします。

 - 自動化されたユーザー プロビジョニングを有効にしていない場合は、次のような確認メッセージが表示されます。

        ![割り当てを確認します。][28]

 - 自動化されたユーザー プロビジョニングを有効にしている場合は、ユーザーに必要な Salesforce プロファイルの種類の定義を求めるメッセージが表示されます。 新しくプロビジョニングされたユーザーは、数分後に Salesforce 環境に表示されます。

        ![割り当てを確認します。][29]
        > [AZURE.IMPORTANT] Salesforce の**開発者**環境向けにプロビジョニングしている場合は、プロファイルごとに利用できるライセンスの数がごく少なくなります。 そのため、4,999 個のライセンスを利用できる **Chatter Free User** プロファイル向けにユーザーをプロビジョニングすることをお勧めします。

4. シングル サインオン設定をテストするには、アクセス パネルを開きます [https://myapps.microsoft.com](https://myapps.microsoft.com/), テスト アカウントにサインインしをクリックして **Salesforce**します。

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]


[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png 
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png 
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png 
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png 
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png 
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png 
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png 
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png 
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png 
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png 
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png 
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png 
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png 
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png 
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png 
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png 
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png 
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png 
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png 
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png 
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png 
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png 
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png 
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png 
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png 
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png 
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png 
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png 
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png 
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png 

