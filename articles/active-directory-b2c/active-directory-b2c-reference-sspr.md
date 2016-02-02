<properties
    pageTitle="Azure Active Directory B2C プレビュー: セルフサービス パスワード リセット | Microsoft Azure"
    description="Azure Active Directory B2C でコンシューマー向けにセルフサービスによるパスワードのリセットをセットアップする方法を示すトピック"
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
    ms.date="09/22/2015"
    ms.author="swkrish"/>


# Azure Active Directory B2C プレビュー: コンシューマー向けにセルフサービス パスワードのリセットをセットアップする

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

この機能を使用すると、コンシューマー (ローカル アカウントにサインアップしたユーザー) はパスワードを自分でリセットできます。 これによりサポート スタッフの負担を大幅に軽減でき、アプリケーションを定期的に使用するコンシューマーが何百万人もいるような場合に特に効果的です。 現在、回復の手段としては検証済みの電子メール アドレスのみがサポートされています。 追加の復旧方法 (有効な電話番号、セキュリティの質問など) を追加します 今後します。 既定のディレクトリでは、セルフサービスによるパスワードのリセットは無効になっています。 この機能を有効にするには次のようにします。

1. サインイン、 [Azure ポータル](https://manage.windowsazure.com/) サブスクリプション管理者とします。 これは、ディレクトリを作成するときに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントです。
2. 左側のナビゲーション バーで Active Directory 拡張機能に移動します。
3. **[ディレクトリ]** タブで、作成したディレクトリをクリックします。
4. **[構成]** タブをクリックします。
5. 下部の **[ユーザー パスワードのリセット ポリシー]** セクションまで移動し、**[パスワードのリセットが有効になっているユーザー]** オプションを **[はい]** に変更します。 **[連絡用電子メール アドレス]** オプションがオンになっていることに注意してください (そのままにします)。

    ![セルフサービスのパスワード リセット](./media/active-directory-b2c-reference-sspr/sspr.png)

6. ページの下部にある **[保存]** をクリックします。 以上で終わりです。

テストするには、任意のサインイン ポリシー (ID プロバイダーがローカル アカウントのもの) で [今すぐ実行] 機能を使用します。ローカル アカウントのサインイン ページ (電子メール アドレスとパスワードまたはユーザー名とパスワードを入力する場所) で、**[アカウントにアクセスできない場合]** をクリックしてコンシューマー エクスペリエンスを確認します。
> [AZURE.NOTE]
セルフ サービス パスワード リセット ページはカスタマイズ可能なを使用して、 [会社のブランド機能](active-directory-add-company-branding.md)します。





