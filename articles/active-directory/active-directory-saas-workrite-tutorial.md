<properties
    pageTitle="チュートリアル: Azure Active Directory と Workrite の統合 | Microsoft Azure"
    description="Azure Active Directory と Workrite の間でシングル サインオンを構成する方法について説明します。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2015"
    ms.author="markusvi"/>



# チュートリアル: Azure Active Directory と Workrite の統合

このチュートリアルでは、Azure Active Directory (Azure AD) と Workrite を統合する方法を説明します。<br>Azure AD と統合する Workrite を使うと、次の利点があります。

- Workrite にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Workrite にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

Azure AD と SaaS アプリの統合に関する詳細を確認するを参照して [アプリケーションへのアクセスと Azure Active Directory でのシングル サインオン](active-directory-appssoaccess-whatis.md)します。

## 前提条件

Azure AD と Workrite の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workrite でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境を取得していない場合は、1 か月の試用版を入手できます [ここ](https://azure.microsoft.com/pricing/free-trial/)します。


## シナリオの説明

このチュートリアルでは、使用すると、テスト環境で Azure AD シングル サインオンをテストします。 <br>
このチュートリアルで紹介するシナリオは、次の 3 つの主な構成要素で構成されます。

1. ギャラリーからの Workrite の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Workrite の追加

Azure AD への Workrite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workrite を追加する必要があります。

**ギャラリーから Workrite を追加するには、次の手順に従います。**

1. **Azure 管理ポータル**, 、左側のナビゲーション ウィンドウで、次のようにクリックします。 **Active Directory**します。 <br><br>
![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. ディレクトリ ビューで、[アプリケーション] ビューを開くにはクリックして **アプリケーション** 上部のメニュー。<br><br>
![アプリケーション][2]<br>
4. クリックして **追加** ページの下部にあります。<br><br>
![アプリケーション][3]<br>
5. **実行する操作** ダイアログ ボックスで、をクリックして **ギャラリーからアプリケーションを追加**します。<br><br>
![アプリケーション][4]<br>
6. 検索ボックスに入力 **Workrite**します。<br><br>
![アプリケーション][5]<br>
7. 結果ウィンドウで [ **Workrite**, 、クリックして **完了** アプリケーションを追加します。
<br><br>![アプリケーション][500]<br>


## Azure AD シングル サインオンの構成とテスト

このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Workrite で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Workrite ユーザーが Azure AD で認識されている必要があります。つまり、Azure AD のユーザーと Workrite に関連するユーザーの間のリンクの関係を確立する必要があります。<br>
このリンク関係は、Azure AD の **[ユーザー名]** の値を、Workrite の **[Username]** の値として割り当てることで確立されます。

Workrite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. * *[構成する Azure AD シングル サインオン](#configuring-azure-ad-single-single-sign-on)* * - この機能を使用するユーザーをできるようにします。
2. * *[Azure AD テスト ユーザーを作成する](#creating-an-azure-ad-test-user)* * - を Azure AD シングル サインオン Britta Simon でテストします。
4. * *[Workrite テスト ユーザーを作成する](#creating-a-halogen-software-test-user)* * - 自分の Azure AD の表現にリンクされている Workrite に Britta Simon に対応します。
5. * *[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)* * - Azure AD シングル サインオンを使用する Britta Simon をできるようにします。
5. * *[シングル サインオンをテスト](#testing-single-sign-on)* * - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD シングル サインオン Azure AD ポータルで有効にして、Workrite アプリケーションでシングル サインオンを構成するには。<br>

**Workrite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルでの **Workrite** アプリケーション統合ページで、] をクリックして **でのシングル サインオンを構成する** を開くには、 **シングル サインオンを構成する**  ダイアログ。
<br><br> ![でのシングル サインオンを構成する][6] <br>

2. **どのような Workrite にサインオン** ] ページで、[ **Azure AD シングル サインオン**, 、順にクリック **次**します。
<br><br> ![Azure AD シングル サインオン][7] <br>

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
<br><br>![Azure AD シングル サインオン][8] <br>

     a.  **サインオン URL** Workrite サイトへのサインオンに、ユーザーが URL を使用] ボックスに、種類 (例:: *https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=1a82b5aa-4dd6-4472-9721-7d0193f59e22*)。
     > [AZURE.NOTE] Workrite サポート チームにお問い合わせください [support@workrite.co.uk](mailto:support@workrite.co.uk) 、サインオン URL の値がわからない場合。

     b. **[次へ]** をクリックします。

4. **Workrite でのシングル サインオンの構成** ] ページで、次の手順を実行します。
<br><br>![Azure AD シングル サインオン][9] <br>

    a. [証明書のダウンロード] をクリックし、コンピューターにファイルを保存します。

    b. Workrite サポート チームに連絡する [support@workrite.co.uk](mailto:support@workrite.co.uk), 、peovide ダウンロードした証明書を使用して、 **発行者 URL** (エンティティ ID)、 **シングル サインオン サービス URL**, 、 **シングル サインアウト URL**, 、Workrite アプリ用の SSO をセットアップすることを確認してください。

    c. **[次へ]** をクリックします。

6. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。 
<br><br>![Azure AD シングル サインオン][10]<br>

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。  
  <br><br>![Azure AD シングル サインオン][11]




### Azure AD のテスト ユーザーの作成

このセクションでは、Britta Simon と呼ばれる Azure ポータルでテスト ユーザーを作成します。<br>
ユーザーの一覧で選択 **Britta Simon**.<br><br>![Azure AD ユーザーを作成][20]<br>

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
<br><br>![Azure AD テスト ユーザーを作成する](./media/active-directory-saas-workrite-tutorial/create_aaduser_09.png) <br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで、ユーザーの一覧を表示する] をクリックして **ユーザー**します。
<br><br> ![Azure AD テスト ユーザーを作成する](./media/active-directory-saas-workrite-tutorial/create_aaduser_03.png) <br>

4. 開くには、 **ユーザーの追加** ] ダイアログ ボックスで、下部にあるツールバーをクリックして **ユーザーの追加**します。 
<br><br> ![Azure AD テスト ユーザーを作成する](./media/active-directory-saas-workrite-tutorial/create_aaduser_04.png) <br>

5. **このユーザーについてお聞かせ** ] ダイアログ ページで、次の手順を実行します。 
<br><br> ![Azure AD テスト ユーザーを作成する](./media/active-directory-saas-workrite-tutorial/create_aaduser_05.png) <br>

    a. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b. **[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c. **[次へ]** をクリックします。

6.  **ユーザー プロファイル** ] ダイアログ ページで、次の手順を実行します。 
<br><br>![Azure AD テスト ユーザーを作成する](./media/active-directory-saas-workrite-tutorial/create_aaduser_06.png) <br>

    a. **[名]** ボックスに「**Britta**」と入力します。

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。

    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
    e. **[次へ]** をクリックします。

7. **一時パスワードの取得** ダイアログ ページで、をクリックして **作成**します。
<br><br> ![Azure AD テスト ユーザーを作成する](./media/active-directory-saas-workrite-tutorial/create_aaduser_07.png) <br>

8. **一時パスワードの取得** ] ダイアログ ページで、次の手順を実行します。
<br><br>![Azure AD テスト ユーザーを作成する](./media/active-directory-saas-workrite-tutorial/create_aaduser_08.png) <br>

    a. **[新しいパスワード]** の値を書き留めます。

    b. **[完了]** をクリックします。



### Workrite テスト ユーザーの作成

このセクションの目的は、Workrite で Britta Simon というユーザーを作成することです。

**Workrite で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Workrite 企業サイトに管理者としてサインオンします。

2. ナビゲーション ウィンドウで、[ **Admin**します。
<br><br>![ユーザーを割り当てる][400]<br>

3. クイック リンクに移動し、クリックして **Create User**します。 
<br><br>![ユーザーを割り当てる][401]<br>

4. **[Create User]** ダイアログで、次の手順に従います。
<br><br>![ユーザーを割り当てる][402]<br>

    a. プロビジョニングする有効な Azure AD ユーザーの**電子メール**、**名**、**姓**を入力します。

    b. **[ロールの選択]** で **[クライアント管理者]** を選択します。

    c. **[保存]** をクリックします。


### Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon Workrite に自分のアクセスを与えることで Azure シングル サインオンの使用を有効にするとします。
<br><br>![ユーザーを割り当てる][200] <br>

**Workrite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルで、ディレクトリ ビューでアプリケーション ビューを開く] をクリックして **アプリケーション** 上部のメニュー。
<br><br>![ユーザーを割り当てる][201] <br>
2. アプリケーションの一覧で選択 **Workrite**します。
<br><br>![ユーザーを割り当てる][202] <br>
1. 上部のメニューで **[ユーザー]** をクリックします。
<br><br>![ユーザーを割り当てる][203] <br>
1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツールバー [ **割り当てる**します。
<br><br>![ユーザーを割り当てる][205]



### シングル サインオンのテスト

このセクションでは、構成のテスト Azure AD シングル サインオン アクセス パネルを使用します。<br>
アクセス パネルで [Workrite] タイルをクリックすると、自動的に Workrite アプリケーションにサインオンします。


## その他のリソース

* [Azure Active Directory による SaaS アプリを統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [アプリケーション アクセスと Azure Active Directory でのシングル サインオンとは](active-directory-appssoaccess-whatis.md)





[1]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_01.png 
[2]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_02.png 
[3]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_03.png 
[4]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_04.png 
[5]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_01.png 
[500]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_05.png 
[6]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_05.png 
[7]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_02.png 
[8]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_03.png 
[9]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_04.png 
[10]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_06.png 
[11]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_07.png 
[20]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_100.png 
[200]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_200.png 
[201]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_201.png 
[202]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_07.png 
[203]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_203.png 
[204]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_204.png 
[205]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_205.png 
[400]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_400.png 
[401]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_401.png 
[402]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_402.png 

