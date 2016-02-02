<properties
    pageTitle="Azure Active Directory ドメイン サービス プレビュー: トラブルシューティング ガイド | Microsoft Azure"
    description="Azure AD ドメイン サービスのトラブルシューティング ガイド"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="maheshu"/>


# Azure AD ドメイン サービス *(プレビュー)* - トラブルシューティング ガイド

この記事では、Azure Active Directory (AD) ドメイン サービスの設定や管理の際に生じる可能性のある問題をトラブルシューティングするためのヒントを提供します。


### ユーザーが Azure AD ドメイン サービスの管理対象ドメインにサインインできない

Azure AD テナントの 1 人または複数のユーザーが、新しく作成された管理対象ドメインにサインインすることができない状況に直面した場合、次のトラブルシューティングの手順を実行します。

- できることを確認 [パスワード同期を有効になっている](active-directory-ds-getting-started-password-sync.md) ファースト ステップ ガイド」の手順に従う。

- 影響を受けているユーザーのアカウントが Azure AD テナントの外部アカウントでないことを確認します。 外部アカウントには、Microsoft アカウント ("joe@live.com" など) や外部の Azure AD ディレクトリのユーザー アカウントが含まれます。 Azure AD ドメイン サービスにはこのようなユーザー アカウントの資格情報がないため、これらのユーザーは管理対象ドメインにサインインできません。

- Azure AD テナントの影響を受けているユーザーのアカウントの UPN プレフィックス (つまり、UPN の最初の部分) が 20 文字未満の長さであることを確認します。 たとえば、UPN "joereallylongnameuser@contoso.com" はプレフィックス ("joereallylongnameuser") が 20 文字を超えているため、このアカウントは Azure AD ドメイン サービスの管理対象ドメインでは使用できません。

- **同期されるアカウント**: 影響を受けているユーザー アカウントがオンプレミスのディレクトリから同期されている場合は、次の手順に従っていることを確認します。
    - Azure AD Connect の最新の推奨リリースをデプロイまたは更新している。
    - Azure AD の従来の資格情報の同期を有効にするために必要なレジストリ キーを作成している。
    - Azure AD Connect が実行されているサーバーに上記のレジストリ キーを作成した後で、ドキュメントの説明に従って完全同期を Azure AD で強制的に実行している。
    - ディレクトリのサイズによっては、ユーザー アカウントと資格情報のハッシュを Azure AD ドメイン サービスで使用できるまで時間がかかる可能性があります。 十分な時間 (ディレクトリのサイズに応じて数時間から 1 ～ 2 日間) を待機してから認証を再試行してください。

- **クラウド専用のアカウント**: 影響を受けているユーザー アカウントがクラウド専用のユーザー アカウントの場合は、Azure AD ドメイン サービスを有効にした後でユーザーが自分のパスワードを変更していることを確認します。 この手順によって、Azure AD ドメイン サービスに必要な資格情報ハッシュが生成されます。


### お問い合わせ

管理対象ドメインに問題がある場合は、このトラブルシューティング ガイドで説明されている手順で問題が解決するかどうかを確認してください。 問題が解消しない場合は、以下の方法でお気軽にお問い合わせください。

- [Azure Active Directory ユーザーの声チャネル](http://feedback.azure.com/forums/169401-azure-active-directory)します。 弊社が確認できるように、質問の先頭に **"AADDS"** という単語を付けてください。
- ことがありますものメールにて [Azure AD ドメイン サービスからのフィードバック](mailto:aaddsfb@microsoft.com)





