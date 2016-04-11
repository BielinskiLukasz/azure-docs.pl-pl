<properties
    pageTitle="アプリ モデル v2.0 のプロトコル | Microsoft Azure"
    description="Azure AD v2.0 アプリ モデル パブリック プレビューでサポートされているプロトコル。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: プロトコル - OAuth 2.0 と OpenID Connect

v2.0 アプリ モデルは、業界標準のプロトコルである OpenID Connect と OAuth 2.0 をサポートすることによって Identity-as-a-Service (サービスとしての ID) 機能をアプリに提供します。  このサービスは標準に準拠していますが、これらのプロトコルには、実装によって微妙な違いが存在する場合があります。  ここでは、いずれかのオープン ソース ライブラリを使うのではなく、コードから直接 HTTP 要求を送信して処理する場合に役立つ情報を紹介します。
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。  一般的に使用できる Azure AD と統合する方法については、サービスを参照してください、 [Azure Active Directory 開発者ガイド 』](active-directory-developers-guide.md)します。

## 基本操作
バージョン 2.0 のアプリケーション モデルを使用するすべてのアプリに登録する必要があります [apps.dev.microsoft.com](https://apps.dev.microsoft.com)します。  アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

-  **アプリケーション Id** アプリを一意に識別します。
- A **リダイレクト URI** または **パッケージ識別子** アプリケーションに応答を返信に使用できます。
- その他シナリオに応じた値。  詳細については、学習方法 [アプリの登録](active-directory-v2-app-registration.md)します。

登録されると、アプリケーションは、バージョン 2.0 のエンドポイントに要求の送信を Azure AD と通信します。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

OAuth と OpenID Connect におけるフローはほぼすべて、情報のやり取りに 4 つの当事者が関係します。

![OAuth 2.0 Roles](../media/active-directory-v2-flows/protocols_roles.png)

-  **承認サーバー** はバージョン 2.0 のエンドポイント。  ユーザーの本人性確認、リソースへのアクセス権の付与と取り消し、トークンの発行という役割を担います。  ID プロバイダーと呼ばれることもあります。ユーザーの情報とそのアクセス、そしてフロー内の当事者間の信頼関係に関するすべてのことは、承認サーバーによって安全に処理されます。
-  **リソース所有者** 、エンドユーザーは、通常です。  データを所有し、そのデータ (またはリソース) へのアクセスをサード パーティに許可する権限を持つ当事者です。
-  **OAuth クライアント** アプリケーション id によって識別されるアプリは、  通常はこの当事者がエンド ユーザーと情報をやり取りし、承認サーバーにトークンを要求します。  クライアントには、リソース所有者がリソースへのアクセス権を付与する必要があります。
-  **リソース サーバー** はリソースまたはデータが存在します。  承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。リソースへのアクセスを許可するためにベアラー トークン (access_tokens) が使用されます。


## トークン
v2.0 アプリ モデルで実装している OAuth 2.0 と OpenID Connect では、ベアラー トークンが広範囲に使われています (JWT として表現されたベアラー トークンなど)。 ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。 この意味で、"ベアラー" はトークンを提示できる任意の利用者を表します。 利用者がベアラー トークンを受信するには、まず Azure AD による認証が必要となりますが、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。 一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがないため、トランスポート層セキュリティ (HTTPS) などのセキュリティで保護されたチャネルで転送する必要があります。 ベアラー トークンが暗号化されずに転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。 後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。 常にアプリが送信され、安全な方法でベアラー トークンを格納することを確認します。 ベアラー トークンのセキュリティの詳細を参照してください。 [RFC 6750 セクション 5](http://tools.ietf.org/html/rfc6750)します。

V2.0 アプリ モデルで使用されるトークンのさまざまな種類の詳細が記載されている [v2.0 アプリ モデル トークン参照](active-directory-v2-tokens.md)します。

## プロトコル

要求の例を理解できる状態になったら、最初に以下のチュートリアルのどれかをご覧ください。  いずれも、特定の認証シナリオに対応しています。  これは、右側のフローを特定する必要がある場合
チェック アウト [、種類のアプリケーション モデルのバージョン 2.0 を使用して構築できるアプリ](active-directory-v2-flows.md)します。

- [OAuth 2.0 でモバイル アプリケーションおよびネイティブ アプリケーションを作成する](active-directory-v2-protocols-oauth-code.md)
- [Open ID Connect で Web Apps を作成する](active-directory-v2-protocols-oidc.md)
- [OAuth 2.0 Implicit Flow で単一ページのアプリを作成する](active-directory-v2-protocols-implicit.md)
- OAuth 2.0 Client Credentials Flow でデーモンまたはサーバー側プロセスを作成する (近日公開予定)
- OAuth 2.0 On Behalf Of Flow を使用して Web API でトークンを取得する (近日公開予定)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 
<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->
