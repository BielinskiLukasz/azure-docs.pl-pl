<properties
    pageTitle="Azure AD Connect:  FAQ | Microsoft Azure"
    description="このページでは、Azure AD Connect についてよく寄せられる質問を紹介します。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="billmath"/>

# Azure AD Connect の FAQ

## 一般的なインストール
**Q: Azure AD のグローバル管理者が 2FA を有効化している場合、インストールを実行できますか。**

この場合、インストールは実行できません。 Azure AD Connect をインストールするグローバル管理者は、MFA を有効化しないでください。 この制限については、将来的にサポートされます。

**Q: Azure AD Connect を無人インストールする方法はありますか。**

インストール ウィザードを使用して Azure AD Connect をインストールする場合にのみサポートされます。 サイレント モードでの無人インストールはサポートされていません。

**Q: 1 つのドメインに接続できないフォレストがあります。 Azure AD Connect をインストールする方法**

このフィードバックは既に認識されています。今後のリリースでサポートされる予定です。

## ネットワーク
**Q: ファイアウォール、ネットワーク デバイスまたはマイ ネットワーク上で開いてに残ることができる最大時間の接続を制限するものです。 どのようにする必要がある、クライアント側のタイムアウトしきい値 Azure AD Connect を使用する場合ですか。**

すべてのネットワーク ソフトウェアや物理デバイスなど、接続を開ける最大時間を制限するものは、Azure AD Connect クライアントがインストールされているサーバーと Azure Active Directory 間の接続に対して少なくとも 5 分 (300 秒) のしきい値を使用する必要があります。 これは、以前リリースされたすべての Microsoft Identity 同期ツールにも適用されます。

**Q: Office 365 の証明書を更新するように求める電子メールを受信した場合はどうすればいいですか。**

解決するのには次の記事に記載されているガイダンスを使用して [ここ](active-directory-aadconnect-o365-certs.md) 証明書を更新します。

**Q: SLD (シングル ラベル ドメイン) はサポートされていますか。**

いいえ、Azure AD Connect は、SLD を使用するオンプレミスのフォレスト/ドメインはサポートしていません。

**Q: 「ドット形式」の NetBios 名はサポートされていますか。**

いいえ、Azure AD Connect では、NetBios 名にピリオド (.) が含まれているオンプレミスのフォレスト/ドメインはサポートしていません。

## 環境

**Q: Azure AD Connect のインストール後のサーバー名の変更はサポートされていますか。**

いいえ、できません。 サーバー名を変更すると、同期エンジンを SQL データベースに接続できませんが発生し、サービスは開始できません。

## ID データ

**Q: Azure AD の UPN (userPrincipalName) 属性がオンプレミスの UPN と一致しません。なぜでしょうか。**

次の記事を参照してください。

- [Office 365、Azure、Intune などの組織アカウントのユーザー名が、オンプレミスの UPN または代替ログイン ID と一致しない](https://support.microsoft.com/en-us/kb/2523192)
- [オンプレミス ユーザー アカウントの UPN を SSO が有効な異なるドメイン サフィックスに更新した後、Azure Active Directory に変更が同期されない](https://support.microsoft.com/en-us/kb/2669550)

## カスタム構成

**Q: Azure AD Connect 用の PowerShell コマンドレットのドキュメントはどこにありますか。**

このサイトに記載されているコマンドレットを除き、Azure AD Connect で使用されている PowerShell コマンドレットは、ユーザーによる使用をサポートしていません。

**Q: サービス マネージャーの [サーバーのエクスポート/インポート] を使用して、サーバー間で構成を移動できますか。**

いいえ、できません。 このオプションは、すべての構成設定を取得できませんが、使用しないで。 代わりに、2 台目のサーバーでウィザードを使用して基本構成を作成し、同期ルール エディターを使用して PowerShell スクリプトを生成し、サーバー間でカスタム ルールを移動してください。

## トラブルシューティング

**Q: Azure AD Connect に関するヘルプを参照する方法を教えてください。**

[Microsoft サポート技術情報 (KB) の検索](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Azure AD Connect のサポートに関する一般的な破損時補償の技術的な解決策について、Microsoft サポート技術情報 (KB) を検索してください。

[Microsoft Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- 検索して、技術的問題に関する質問しコミュニティから回答やをクリックして、独自の質問を投稿のブラウズ [ここ](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)します。


[Azure AD Connect のカスタマー サポート](https://manage.windowsazure.com/?getsupport=true)

- このリンクを使用して、Azure ポータルからサポートを受けることができます。

