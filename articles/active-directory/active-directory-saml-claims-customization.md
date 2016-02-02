<properties
    pageTitle="Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ| Microsoft Azure"
    description="Azure Active Directory において事前に統合されたアプリの SAML トークンで発行された要求をカスタマイズする方法を学習します"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/18/2015"
    ms.author="asmalser"/>


# Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ

現在 Azure Active Directory は、SAML 2.0 プロトコルを使用したシングル サインオンをサポートする 150 以上のアプリケーションを含め、何千もの事前統合済みのアプリケーションを Azure AD アプリケーション ギャラリーでサポートしています。 SAML を使用し、Azure AD を介してアプリケーションを認証する場合、Azure AD は、(HTTP 302 リダイレクトを介して) アプリケーションにトークンを送信します。次に、アプリケーションはトークンを検証し、ユーザー名とパスワードを要求する代わりにトークンを使用してユーザーをログインさせます。 これらの SAML トークンには、「要求」と呼ばれる、ユーザーに関する情報が含まれています。

ID 用語で「要求」とは、そのユーザーに発行するトークンの中にあるユーザーに関する ID プロバイダーが提示した情報を指します。  [SAML トークン](http://en.wikipedia.org/wiki/SAML_2.0), 、このデータは通常、SAML 属性ステートメントに含まれ、ユーザーの一意の ID は通常、SAML サブジェクトで表されます。

既定では、Azure AD は、NameIdentifier 要求、Azure AD でのユーザー名の値 (この値は、ユーザーを一意に識別します) を含む、SAML トークンを発行します。 また、SAML トークンには、ユーザーの電子メール アドレス、姓名を含むその他の要求も含まれています。

SAML トークンで発行された要求を表示し、編集するには、Microsoft Azure 管理ポータルでアプリケーション レコードを開き、アプリケーションの下にある **[属性]** タブを選択します。

![][1]

SAML トークンで発行された要求を編集しなければならない理由、2 つの理由が考えられます。
• アプリケーションは、別のクレームの Uri のセットが必要または要求の値に書き込まれています 
•Your アプリケーションは、Azure Active Directory に格納されているユーザー名 (AKA ユーザー プリンシパル名) 以外の場合に NameIdentifier 要求するような方法で展開されています。

SAML トークンの属性テーブル内の行のいずれかにマウスを置いた際に、右側に表示される、鉛筆の形のアイコンを選択して、既定の要求値のいずれかを編集できます。 また、**[X]** アイコンを使用することで、(NameIdentifier 以外の) 要求を削除できます。さらに、**[ユーザー属性の追加]** ボタンを使用することで、新しい要求を追加できます。

## NameIdentifier 要求の編集

別のユーザー名を使用して、デプロイされたアプリケーションの問題を解決するには、NameIdentifier 要求の横にある鉛筆アイコンをクリックします。 これにより、次のいくつかのオプションを示すダイアログ ボックスが表示されます。

![][2]

**[属性値]** メニューで、**[user.mail]** を選択し、ディレクトリで NameIdentifier 要求がユーザーの電子メール アドレスになるように設定するか、**[user.onpremisessamaccountname]** を選択し、オンプレミスの Azure AD から同期されたユーザーの SAM アカウント名に設定します。

電子メール アドレスまたはユーザー プリンシパル名 (例:"joesmith"joesmith@contoso.com ではなく) を通じて渡されるユーザー名の最初の部分のみで結果のいずれかからドメイン サフィックスを削除するのには特殊な ExtractMailPrefix() 関数を使用することもできます。

![][3]

## 要求の追加

新しい要求を追加する場合は、ディレクトリに格納されているユーザーの属性に値を設定するのと同様に、属性名 (SAML 仕様のように厳密に URI パターンに従う必要はありません) を指定できます。

![][4]

たとえば、組織でユーザーが所属する部門 (例: 営業) を要求として送信する必要がある場合、アプリケーションで求められる要求値を入力し、**[user.department]** を値として選択できます。

特定のユーザーに対し、選択された属性に格納されている値がない場合は、トークンでその要求は発行されません。

**注:** **user.onpremisesecurityidentifier** と **user.onpremisesamaccountname** は、オンプレミスの Active Directory から AAD Connect ツールの最新のプレビューを使用してユーザー データを同期する場合にのみサポートされます。 Connect ツールのプレビューは、次のリンクからダウンロードすることができます。

http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=53949



[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png 
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png 
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png 
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png 

