<properties
    pageTitle="Azure Active Directory B2C プレビュー: Facebook の構成 | Microsoft Azure"
    description="Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Facebook アカウントを使用するコンシューマーにサインアップとサインインを提供します"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2015"
    ms.author="swkrish"/>


# Azure Active Directory B2C プレビュー: Facebook アカウントでコンシューマーにサインアップおよびサインインを提供する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Facebook アプリケーションを作成する

Azure Active Directory (AD) B2C で ID プロバイダーとして Facebook を使用するには、最初に Facebook アプリケーションを作成し、適切なパラメーターを提供する必要があります。 これには、Facebook アカウントは必要があります。ない場合にそれを取得できます [https://www.facebook.com/](https://www.facebook.com/)します。

1. 移動して、 [Facebook Developers の web サイト](https://developers.facebook.com/) 、Facebook アカウントの資格情報を使用してサインインします。
2. まだ登録していない場合は、**[Apps]**、**[Register as a Developer]** の順にクリックし、ポリシーに同意して、登録手順に従います。
3. **[Apps]** をクリックし、**[Add a new App]** をクリックします。 プラットフォームとして **[Website]** を選択し、**[Skip and Create App ID]** をクリックします。

    ![FB - 新規アプリの追加](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB - 新規アプリの追加 - Web サイト](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB - アプリ ID の作成](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. フォームで、**[Display Name]** を指定し、適切な **[Category]** を選択して、**[Create App ID]** をクリックします。 注: Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。

    ![FB - アプリ ID の作成](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. 左側のナビゲーションで **[Settings]** をクリックします。 有効な **[Contact Email]** を入力します。
6. **[+Add Platform]** をクリックし、**[Website]** を選択します。

    ![FB - 設定](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB - 設定](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. 入力 [https://login.microsoftonline.com/](https://login.microsoftonline.com/) で、 **サイトの URL** フィールドをクリックして **Save Changes**します。
8. **[App ID]** の値をコピーします。 **[Show]** をクリックし、**[App Secret]** の値をコピーします。 ディレクトリで ID プロバイダーとして Facebook を構成するには、両方の値が必要です。
    > [AZURE.NOTE]
    **[App Secret]** は、重要なセキュリティ資格情報です。

    ![FB - サイト URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. をクリックして、 **詳細** ] タブの上部にあると入力し、 [https://login.microsoftonline.com/te/{ディレクトリ}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) で、 **Valid OAuth redirect URIs** フィールド (で、 **セキュリティ** セクション) ここで、 **{ディレクトリ}** は、ディレクトリの名前 (たとえば、contosob2c.onmicrosoft.com) で置き換えられます。 ページの下部にある **[Save Changes]** をクリックします。

    ![FB - OAuth リダイレクト URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、アプリケーションをパブリックに利用できるようにする必要があります。そのためには、左側のナビゲーションで **[Status & Review]** をクリックし、ページの先頭にあるスイッチを **[YES]** にします。**[Confirm]** をクリックします。

    ![FB - OAuth リダイレクト URI](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## ディレクトリで ID プロバイダーとして Facebook を構成する

1. [Azure プレビュー ポータルで B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)します。
2. B2C 機能ブレードで、**[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「FB」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Facebook]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、先に作成した Facebook アプリケーションの **[アプリ ID]** と **[アプリ シークレット]** を **[クライアント ID]** および **[クライアント シークレット]** フィールドに入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Facebook の構成を保存します。





