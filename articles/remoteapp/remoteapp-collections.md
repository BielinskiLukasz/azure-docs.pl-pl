<properties 
    pageTitle="Azure RemoteApp にはどのような種類のコレクションが必要ですか。 | Microsoft Azure" 
    description="Azure RemoteApp で使用可能なコレクションの種類について理解する。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/28/2015" 
    ms.author="elizapo" />



# Azure RemoteApp にはどのような種類のコレクションが必要ですか。

Azure RemoteApp では、任意のデバイスでユーザーとアプリおよびリソースを共有することができます。 これを行うには、アプリおよびリソースを保持するためのコレクションを作成して、そのコレクションを他のユーザーと共有します。 ネットワークと認証のオプションが異なる 2 種類のコレクション オプションがあります。どちらが適しているでしょうか。

Azure RemoteApp のコレクションを最大限に活用するために必要な、さまざまな考慮事項と選択肢を見てみましょう。 


## コレクションの種類の簡単な相違点

|           | クラウド | ハイブリッド |
|-----------|-------|--------|
|既存の VNET の使用| あり| あり|
|AD に接続しているアカウント (DirSync) が必要| いいえ| あり|
|ドメインへの参加が必要| いいえ| あり|
|ドメイン コント ローラーが VNET にアクセスできる必要| いいえ| あり|

## クラウドのコレクション
- 作成が迅速 - コレクションが迅速にプロビジョニングされれば、アプリがユーザーに早く届くことになります。
- 独自のアプリを取り込むか、マイクロソフトのアプリを共有します。 カスタム イメージ (Azure VM から作成) を使用するか、またはサブスクリプションに含まれているイメージの 1 つを選択します。
- コレクションと、オンプレミスのドメインの間の接続を構成する必要はありません。
- ただし、独自の Azure VNET を使用して、データ共有のためにオンプレミスの環境へのアクセスを提供したり、SQL Server のようなリソースに (データベース認証を使用して) Windows 以外の認証を使用したりできます。


では、どのようにしてコレクションを作成しますか?

- クラウドのみ? 作成、 **簡易作成** 、ポータルでオプションです。
- クラウド + VNET? 使用して作成、 **VNET で作成** オプションが、ドメインに参加しません。

## ハイブリッドのコレクション
- オンプレミスのネットワークと Azure VNET へのフル アクセスを提供します。
- アプリとデータのドメイン参加アクセスが含まれています。 リモート アプリケーションは、オンプレミスの Active Directory に対する認証ができます。これで、ドメイン内のリソースにアクセスできます。
- System Center ソリューションおよび Windows グループ ポリシーにより、(Windows Server 2012 R2 上に構築されたカスタムのイメージを使用して) 高度な監視および管理を有効にします。
- サポート [Express Route](http://azure.microsoft.com/services/expressroute/) ローカル VNET に Azure VNET を接続します。

使用して作成、 **VNET で作成** ドメインに参加を選択しないでください。

## 認証オプション
Azure RemoteApp は、Microsoft アカウントと Azure Active Directory アカウントの両方をサポートしていますが、すべてのコレクションがすべてのメソッドをサポートしているわけではありません。 

| アカウントの種類                      |                                                             | クラウド | クラウド + VNET | ハイブリッド |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|-------|
| Microsoft アカウント                 |                                                             | あり   | はい          | いいえ    |
| Azure Active Directory (Azure AD) |                                                             |       |              |       |
|                                   | Azure AD のみ                                               | あり   | はい          | いいえ    |
|                                   | パスワード同期がある AD Connect                               | あり   | はい          | あり   |
|                                   | パスワード同期がない AD Connect                            | あり   | はい          | いいえ    |
|                                   | AD FS がある AD Connect                                       | あり   | はい          | あり   |
|                                   | サード パーティの Azure サポート ID プロバイダー (Ping など) | あり   | はい          | あり   |
| Multi-Factor Authentication       |                                                             | あり   | はい          | あり   |



### クラウドとクラウド + VNET 間 
クラウドのコレクションでは、Microsoft アカウント、Azure AD アカウント、または 2 つの組み合わせを使用できます。 ユーザーに対して最も効果的なアカウントを使用します。

Microsoft アカウントを使用するための特定の要件はありません。 

Azure AD アカウントを使用する場合は、Azure AD テナントがサブスクリプションに関連付けられているテナントと一致するかどうかを確認する必要があります。 Azure RemoteApp サブスクリプションを作成したときに、使用していた Azure AD テナントがサブスクリプションに自動的に関連付けられていました。 権限を与えるすべての Azure AD ユーザーが、その同じテナントになる必要があります。 必要な場合は、 [Azure AD テナントの変更](remoteapp-changetenant.md) 、サブスクリプションに関連付けられています。
 
### ハイブリッド (またはクラウド + Azure AD + AD)

ハイブリッドのコレクションには、Azure AD とオンプレミスの Active Directory を使用する必要があります。 AD Connect を使用して、2 つのディレクトリを統合する必要があります。 ただし、AD Connect を構成する方法に関してはいくつかの選択肢があります。 

AD Connect には、パスワード同期を使用する場合と、AD フェデレーションを使用する場合の 2 つのシナリオがあります。 チェック アウト、 [AD Connect について](active-directory-aadconnect.md) かをこれらの作品のうち最適なのです。

クラウドのコレクションに Azure AD と AD を使用することもできます。 同じ設定手順に従ってください。

チェック アウト [Azure AD + Azure RemoteApp の Active Directory の要件](remoteapp-ad.md) を Azure AD を構成するために必要な手順については、Active Directory です。

## コレクションを作成しよう
これですべて説明したと思いますが、1 つだけやるべきことが残っていました。最初の Azure RemoteApp コレクションを作成することです。

[クラウド コレクションの作成](remoteapp-create-cloud-deployment.md) または [ハイブリッド コレクションの作成](remoteapp-create-hybrid-deployment.md) の取得を作成するだけです。
