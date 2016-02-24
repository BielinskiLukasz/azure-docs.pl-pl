<properties
    pageTitle="Azure AD Connect: 簡単設定を使用した開始 | Microsoft Azure"
    description="Azure AD Connect のセットアップ ウィザードをダウンロード、インストール、および実行する方法について説明します。"
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
    ms.date="10/13/2015"
    ms.author="billmath;andkjell"/>

# 簡単設定を使用した Azure AD Connect の開始
次のドキュメントは、Azure Active Directory Connect の使用を開始する際に役立ちます。 このドキュメントでは、Azure AD Connect の高速インストールの使用について説明します。  

## 関連ドキュメント
上のドキュメントを読んでいない場合 [内部設置型 id と Azure Active Directory の統合](active-directory-aadconnect.md), 、次の表は、関連するトピックへのリンクを提供します。 インストールを開始する前に、太字で表示した最初の 2 つのトピックをお読みいただく必要があります。

| トピック |  |
| --------- | --------- |
| **Azure AD Connect のダウンロード** | [Azure AD Connect のダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **ハードウェアと前提条件** | [Azure AD Connect: ハードウェアと前提条件](active-directory-aadconnect-prerequisites.md) |
| カスタマイズした設定を使用したインストール | [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md) |
| DirSync からのアップグレード | [Azure AD 同期ツール (DirSync) からのアップグレード](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| インストール後 | [インストールの確認とライセンスの割り当て ](active-directory-aadconnect-whats-next.md) |
| インストールで使用するアカウント | [Azure AD Connect アカウントとアクセス許可の詳細](active-directory-aadconnect-accounts-permissions.md) |


## Azure AD Connect の高速インストール
[簡単設定] の選択は、既定のオプションであり、最も一般的なシナリオの 1 つです。 このとき、Azure AD Connect はパスワード同期オプションを使用して同期をデプロイします。 これは、単一のフォレスト用のみであり、ユーザーはオンプレミスのパスワードを使用して、クラウドにサインインできます。 簡単設定を使用した場合、インストールが完了すると、自動的に同期が開始されます (ただし、開始されないようにすることもできます)。 このオプションでは、数回クリックするだけで、オンプレミスのディレクトリをクラウドに拡張できます。

![Azure AD Connect へようこそ](./media/active-directory-aadconnect-get-started/welcome.png)

### 簡単設定を使用して Azure AD Connect をインストールするには

1. Azure AD Connect をインストールするサーバーにローカル管理者としてログインします。  これには、同期サーバーにするサーバーを使用します。
2. AzureADConnect.msi を検索し、ダブルクリックします。
3. [ようこそ] 画面で、ライセンス条項に同意するチェック ボックスを選択し、クリックして **続行**します。
4. 簡単設定] 画面をクリックして **express 設定を使用する**です。
![Azure AD Connect へようこそ](./media/active-directory-aadconnect-get-started/express.png)
5. [Azure AD に接続] 画面で、Azure AD の Azure グローバル管理者のユーザー名とパスワードを入力します。 クリックして **次**します。
6. [AD DS に接続] 画面で、エンタープライズ管理者アカウントのユーザー名とパスワードを入力します。  クリックして **次**します。
![Azure AD Connect へようこそ](./media/active-directory-aadconnect-get-started/install4.png)
7. 画面を構成する準備完了で、次のようにクリックします。 **インストール**します。
    - 必要に応じて [準備完了の構成] ページで、ことができますオフ、"**構成が完了するとすぐに同期プロセスを開始**"チェック ボックスをオンします。  このチェック ボックスをオフにすると、ウィザードでは、同期が構成されますが、タスクは無効のままになります。そのため、タスクは、タスク スケジューラで手動で有効にするまで実行されません。  タスクを有効にすると、同期は 3 時間ごとに実行されます。
    - また、必要に応じて同期サービスを構成することができます **Exchange ハイブリッド展開** 対応するチェック ボックスをオンします。  Exchange メールボックスをクラウドにもオンプレミスにも配置する予定がない場合は、このチェック ボックスをオンにする必要はありません。
![Azure AD Connect へようこそ](./media/active-directory-aadconnect-get-started/readyinstall.png)<br>
8. インストールが完了すると、クリックして **終了**します。
9. インストールが完了した後、 Synchronization Service Manager または同期規則エディターを使用する前に、ログオフし、もう一度ログオンします。

<br>
<br>

高速インストールの使用方法に関するビデオについては、以下を確認してください。

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>


## 次のステップ
Azure AD Connect がインストールされているがあるわかったできます [のインストールを確認し、ライセンスを割り当てる](active-directory-aadconnect-whats-next.md)します。

詳細について [内部設置型 id と Azure Active Directory の統合](active-directory-aadconnect.md)します。

