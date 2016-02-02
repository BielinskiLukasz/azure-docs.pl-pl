<properties 
    pageTitle ="Azure AD + Azure RemoteApp の Active Directory の要件 |Microsoft Azure] 
    description =「Azure RemoteApp を使用する Active Directory をセットアップする方法について説明します」。 
    サービス =「remoteapp」 
    documentationCenter="" 
    authors ="lizap" 
    manager ="mbaldwin"/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/28/2015" 
    ms.author="elizapo"/>



# Azure RemoteApp に関する Azure AD と Active Directory の要件

AD Connect を使用してフェデレーションするクラウド コレクションまたは Azure RemoteApp ハイブリッド コレクションでは、次を行う必要があります。

### Azure AD と Active Directory を接続する

Azure AD テナントとオンプレミスの Active Directory 環境を接続する場合、AD Connect を使用します。 のにかかるのみ [4 回クリック](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) に 2 つのディレクトリを接続します。

注 - ハイブリッド コレクションではディレクトリの同期が必要です。

### "@domain.com" が一致することを確認する

作業を開始する前に、オンプレミス フォレストの UPN が、Azure AD ドメインのサフィックスと一致していることを確認してください。

Azure AD での UPN ドメイン サフィックスを設定した後 Azure RemoteApp にログインするすべてのユーザーとしてログイン"@ ユーザー<the suffix you set up>"です。ユーザーが同じ user@suffix を使用して内部設置型ドメインを確認します。場合によっては、Azure AD に 1 つのドメイン名をセットアップし、オンプレミスのユーザー用に別のドメイン サフィックスを指定することもできます。この場合、ユーザーは Azure RemoteApp を介してドメインに参加しているコンピューターまたはリソースに接続することはできません。

たとえば、UPN ドメイン サフィックスを AAD に contoso.com としてセットアップした場合に、オンプレミス/AD の一部のユーザーが @contoso.uk を使用してログインするように構成されている場合、そのようなユーザーは ARA コレクションに正しくログインすることはできません。 ログインするには、AAD と AD のユーザー UPN が同じである必要があります。

### Azure RemoteApp のオブジェクトを作成する

また、次のオンプレミス Active Directory オブジェクトを作成する必要もあります。

- オンプレミスのドメインに RDSH エンド ポイントを結合して、RemoteApp プログラムのドメイン リソースにアクセスするサービス アカウント。
- RemoteApp マシン オブジェクトを含む組織単位 (OU)。 RemoteApp で使用するアカウントとポリシーを特定するために OU の使用を推奨します (必須ではありません)。

RemoteApp コレクションを作成するときは、これらのオブジェクトが両方とも必要であるため、これらの手順を最初に行ってください。




