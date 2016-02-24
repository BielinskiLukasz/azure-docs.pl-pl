<properties
    pageTitle="Azure AD Connect の複数のドメイン"
    description="このドキュメントでは、O365 と Azure AD で複数の最上位レベル ドメインを構成する方法について説明します。"
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

#複数ドメインのサポート

最上位レベルの複数の Office 365 または Azure AD ドメインとサブドメインを、フェデレーションを使用して構成する方法に関して、多くの質問を受けました。
このほとんどは、単純明快な方法で行えますが、舞台裏でいくつかのことを行っているため、いくつかのヒントやテクニックを知っていると以下の問題を回避できます。

- ドメインを追加してフェデレーションを構成する際に表示されるエラー メッセージ
- 複数の最上位レベルのドメインをフェデレーション構成すると、サブドメインのユーザーがログインできなくなります

## 複数の最上位レベルのドメイン
fabrikam.com をという名前のドメインを追加する contoso.com という組織の設定例を取り上げます。

オンプレミスのシステムに、フェデレーション サービス名 fs.jenfield.com の AD FS 構成があるとします。  

Office 365 または Azure AD に最初にサインアップした際、最初にサインオンするドメインとして contoso.com を構成しました。  これは、New-MsolFederatedDomain を使用して Azure AD Connect または Azure AD Powershell で実行できます。

これが完了したら、2 つの新しい Azure AD ドメインの新しい構成プロパティの既定値を見てみましょう (これらは Get-MsolDomainFederationSettings を使用して問い合わせることができます)。

| プロパティ名 | 値 | 説明|
| ----- | ----- | -----|
|IssuerURI | http://fs.jenfield.com/adfs/services/trust| 次の URL のように、中に設置型の認証システムの名前では実際には、このプロパティは、そのため、パスがどこにも解決するのには必要ありません。  Azure AD は既定で、これを自分のオンプレミスの AD FS 構成のフェデレーション サービスの ID の値に設定します。
|PassiveClientSignInUrl|https://fs.jenfield.com/adfs/ls/|これは、先パッシブ サインイン要求が送信され、AD FS システム実際に解決される場所です。  実際にはいくつか "* Url" プロパティがありますが、このプロパティと、IssuerURI などの URI の違いについては、1 つ例を見ればご理解いただけます。

ここで、2 番目のドメイン fabrikam.com を追加したと想像してください。  ここでも再度 Azure AD Connect ウィザードを実行するか、または PowerShell を使用してこれを実行します。

Azure AD PowerShell を使用してフェデレーションを構成する 2 番目のドメインを追加すると、エラーが表示されます。

これは、複数のドメインで IssuerURI 値を同じにできないという制約が Azure AD にあるためです。  この制限をなくすには、新しいドメインには別の IssuerURI を使用する必要があります。  これが SupportMultipleDomain パラメーターが実質的に行うことです。  フェデレーションを構成するコマンドレット (New-、Convert-、および Update-msolfederateddomain) と使用すると、このパラメーターにより、Azure AD のテナント全体で一意である必要があるドメイン名に基づく IssuerURI が Azure AD によって構成されます。  要求規則の変更もありますが、これについては、すぐ後で説明します。

つまり、Powershell で、SupportMultipleDomain パラメーターを使用して fabrikam を追加すると、

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

Azure AD の構成は次のようになります。

- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 

IssuerURI は自分のドメインが使用された値に設定され一意ですが、エンドポイント URL の値は、元の contoso.com ドメインのときと同様に、fs.jenfield.com 上のフェデレーション サービスをまだポイントしていることに注意してください。  つまり、すべてのドメインはまだ同じ AD FS システムをポイントし続けています。

SupportMultipleDomain では、AD FS システムの Azure AD 用に発行されたトークンに適切な Issuer 値が含まれることも保証します。  これは、ユーザーの upn のドメイン部分を取得し、issuerURI、つまり https://{upn サフィックスのドメインとして設定して}/adfs/services/信頼します。  したがって、Azure AD または Office 365に対する認証の際、ユーザー トークンの Issuer 要素は Azure AD のドメインを検出するために使用されます。  一致が見つからない場合、認証は失敗します。

たとえば、ユーザーの UPN が johndoe@fabrikam.com の場合は、トークン AD FS に関する問題の Issuer 要素は http://fabrikam.com/adfs/services/trust に設定されます。  これは Azure AD の構成と一致し、認証は成功します。

以下にこのロジックを実装する、カスタム要求規則を示します。

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

これで最初の contoso.com の設定に、supportMultipleDomains スイッチなしの既定値 IssuerURI が登録されました。  fabrikam.com を追加する際、要求規則の更新で、既定の IssuerURI は送信されなくなり、IssuerURI の不一致で認証は失敗するため、contoso.com で SupportMultiple Domains スイッチの使用が構成されていることを確認する必要があります。  別のドメインで supportMultipleDomain スイッチを使用することを許可する前に、これについては警告するので、心配はしないでください。

これを修復するには、ドメイン contoso.com の構成も同様に更新する必要があります。  
Azure AD Connect ウィザードは、上記を行うタイミング、および 2 番目のドメインを追加するときの適切な処理の検出が非常に上手です。  最初の段階で SupportMultipleDomain の構成を既に行っている場合は、上書きはされません。

PowerShell では、手動で SupportMultipleDomain スイッチを指定する必要があります。

単一ドメインから複数ドメインに移行する際の詳細なすべての手順については、以下を参照してください。

これの実行が完了すると、Azure AD には 2 つのドメインが構成されます。

- DomainName: contoso.com
- IssuerURI: http://contoso.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 
- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.jenfield.com/adfs/ls/ 

これで、contoso.com と fabrikam.com ドメインのユーザーのフェデレーション サインオンが機能するようになりました。  ここで、サブドメインのユーザーのサインインの問題が 1 つ残ります。

##サブドメイン
サブドメイン sub.contoso.com を Azure AD に追加したとします。  Azure AD がドメインを管理する方法により、サブドメインには、この場合は contoso.com の親ドメインの設定が継承されます。  これは、user@sub.contoso.com の IssuerURI が必要になる http://contoso.com/adfs/services/trust ことを意味します。  しかしながら、上で Azure AD に実装した標準的なルール 

Azure AD は、ドメインの必要な値が一致しなくなる http://sub.contoso.com/adfs/services/trust として発行者でトークンを生成し、認証は失敗します。
さいわいに、これにも解決策がありますが、同様にツールには組み込まれていません。  手動で Microsoft Online の AD FS の証明書利用者の信頼を更新する必要があります。  

カスタム Issuer 値を構築する際、ユーザーの UPN サフィックスからサブドメインをすべて削除する、カスタム要求規則を構築する必要があります。  これを実行する正確な手順については、以下を参照してください。

要約すると、完全に異なる名前の複数のドメインとサブドメインを、すべて同じ AD FS サーバーにフェデレーション構成できました。すべてのユーザーの Issuer 値を正しく設定するには、いくつかの手順を追加で行います。

