<properties
    pageTitle="Azure Active Directory B2C プレビュー: Google+ の構成 | Microsoft Azure"
    description="Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Google+ アカウントを使用するコンシューマーにサインアップとサインインを提供します"
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


# Azure Active Directory B2C プレビュー: Google+ アカウントでコンシューマーにサインアップおよびサインインを提供する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Google+ アプリケーションを作成する

Azure Active Directory (AD) B2C で ID プロバイダーとして Google+ を使用するには、最初に Google+ アプリケーションを作成し、適切なパラメーターを提供する必要があります。 これには Google + アカウントが必要ない場合にそれを取得できます [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)します。

1. 移動して、 [Google Developers Console](https://console.developers.google.com/) Google + アカウントの資格情報を使用してサインインします。
2. **[プロジェクトを作成]** をクリックし、**[プロジェクト名]** を入力し、サービス規約に同意して、**[作成]** をクリックします。

    ![G+ - 作業開始](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - 新しいプロジェクト](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. 左側のナビゲーションで、**[API と認証]** をクリックし、**[認証情報]** をクリックします。
4. 上部の **[OAuth 同意画面]** タブをクリックします。

    ![G+ - 資格情報](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. 有効な **[メール アドレス]** を選択または指定し、**サービス名**を入力して、**[保存]** をクリックします。

    ![G+ - OAuth 同意画面](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. **[認証情報の追加]** をクリックし、**[OAuth 2.0 クライアント ID]** を選択します。

    ![G+ - OAuth 同意画面](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。

    ![G+ - OAuth 同意画面](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. 提供、 **名** 、アプリケーションでは、次を入力してください。 [https://login.microsoftonline.com](https://login.microsoftonline.com) で、 **Authorized redirect URIs** フィールドと [https://login.microsoftonline.com/te/{tenant}/oauth2/authresp](https://login.microsoftonline.com/te/{tenant}/oauth2/authresp) で、 **Authorized redirect URIs** フィールドに、ここで **{tenant}** はテナントの名前 (たとえば、contosob2c.onmicrosoft.com) で置き換えられます。 **[作成]** をクリックします。
    > [AZURE.NOTE]
    **{tenant}** の値は大文字小文字が区別されます。

    ![G+ - クライアント ID の作成](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして Google+ を構成するには、両方の値が必要です。
    > [AZURE.NOTE]
    **[Client secret]** は、重要なセキュリティ資格情報です。

    ![G+ - クライアント シークレット](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## テナントで ID プロバイダーとして Google+ を構成する

1. [Azure プレビュー ポータルで B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)します。
2. B2C 機能ブレードで、**[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「G+」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Google]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、先に作成した Google+ アプリケーションの **[クライアント ID]** と **[クライアント シークレット]** を入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Google+ の構成を保存します。





