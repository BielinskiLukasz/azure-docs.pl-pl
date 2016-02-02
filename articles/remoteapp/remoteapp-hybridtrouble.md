<properties
    pageTitle ="トラブルシューティング RemoteApp ハイブリッド コレクションを作成する |Microsoft Azure]
    description =「RemoteApp ハイブリッド コレクションの作成エラーのトラブルシューティングを行う方法について説明します」
    サービス =「remoteapp」
    documentationCenter=""
    authors ="vkbucha"
    manager ="mbaldwin"/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="elizapo"/>



# Azure RemoteApp ハイブリッド コレクション作成のトラブルシューティング

ハイブリッド コレクション では、Azure クラウドにホストされ、データはクラウドに格納されますが、ユーザーはローカル ネットワークに格納されたデータとリソースにアクセスすることもできます。 ユーザーは、Azure Active Directory と同期また連携した企業資格情報でログインしてアプリにアクセスできます。 既存の Azure Virtual Network を使用するハイブリッド コレクションをデプロイすることも、新しい仮想ネットワークを作成することもできます。 今後、予測される Azure RemoteApp の成長に十分な CIDR 範囲で仮想ネットワーク サブネットを作成または使用することが推奨されます。

コレクションをまだ作成していませんか。 参照してください [ハイブリッド コレクションの作成](remoteapp-create-hybrid-deployment.md) の手順を実行します。

コレクションの作成で問題が発生した場合、あるいはコレクションが予想どおりに動作しない場合、次の情報を確認してください。

## イメージが無効です

テンプレート イメージを満たしていない場合、"GoldImageInvalid"などのメッセージが表示されるは、Azure でのコレクションがプロビジョニングに待機している場合、意味、 [イメージの要件が定義されている](remoteapp-imagereqs.md)します。 そのため、移動、読み取り [要件](remoteapp-imagereqs.md), 、イメージを修正、およびコレクションを再度作成しようとしています。

## VNET で強制トンネリングを使用していますか。

現在のところ、RemoteApp は強制トンネリングを有効にした VNET に対応していません。 この関数が必要な場合にお問い合わせ、 [RemoteApp チーム](mailto:remoteappforum@microsoft.com) サポートを依頼します。

要求が承認されたら、Azure RemoteApp に選択したサブネットとサブネットの VM で次のポートが開いていることを確認します。 サブネットの VM はネットワーク セキュリティ グループに関するセクションで説明した URL にもアクセスできなければなりません。

送信: TCP: 443、TCP: 10101-10175

## VNET にはネットワーク セキュリティ グループが定義されていますか。

コレクションに使用しているサブネットでネットワーク セキュリティ グループを定義している場合、サブネット内から次の URL にアクセスできることを確認します。

    https://management.remoteapp.windowsazure.com  
    https://opsapi.mohoro.com  
    https://telemetry.remoteapp.windowsazure.com  
    https://*.remoteapp.windowsazure.com  
    https://login.windows.net (if you have Active Directory)  
    https://login.microsoftonline.com  
    Azure storage *.remoteapp.windowsazure.com  
    *.core.windows.net  
    https://www.remoteapp.windowsazure.com  
    https://www.remoteapp.windowsazure.com  

仮想ネットワーク サブネットで次のポートを開きます。

受信 - TCP: 3030、TCP: 443  
送信 - TCP: 443

厳密な制御のために、サブネットに配置した VM にネットワーク セキュリティ グループを追加できます。

## 独自の DNS サーバーを使用していますか。そのサーバーには VNET サブネットからアクセスできますか。

ハイブリッド コレクションには、独自の DNS サーバーを使用します。 それは仮想ネットワークの構築時にネットワーク構成スキーマに指定するか、管理ポータルで指定します。 DNS サーバーは (ラウンド ロビン方式ではなく) フェールオーバー方式で指定されている順序で使用されます。

コレクションの DNS サーバーにはそのコレクションに指定した VNET サブネットからアクセスできて利用できることを確認します。

次に例を示します。

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![DNS を定義する](./media/remoteapp-hybridtrouble/dnsvpn.png)

## コレクションで Active Directory ドメイン コント ローラーを使用していますか。

現在のところ、1 つの Active Directory ドメインだけを Azure RemoteApp に関連付けることができます。 ハイブリッド コレクションは、Windows Server Active Directory のデプロイから DirSync ツールを使用して同期されている Azure Active Directory アカウントのみをサポートします。具体的には、パスワード同期オプションで同期されているか、または Active Directory フェデレーション サービス (AD FS) の構成されたフェデレーションのいずれかで同期されます。 オンプレミスのドメインの UPN ドメイン サフィックスと一致するカスタム ドメインを作成し、ディレクトリ統合を設定する必要があります。

参照してください [Azure RemoteApp の Active Directory を構成する](remoteapp-ad.md) の詳細。

入力したドメイン詳細が有効であることと、Azure Remote App に使用されるサブネットで作成された VM からドメイン コントローラーに到達できることを確認します。 また、入力したサービス アカウント資格情報で指定ドメインにコンピューターを追加できることと、指定の AD 名を VNET の指定 DNS から解決できることを確認します。

## コレクションを作成したとき、どのようなドメイン名を指定しましたか。

作成または追加したドメイン名は (Azure AD ドメイン名ではなく) 内部ドメイン名であり、解決可能な DNS 形式 (contoso.local) になっている必要があります。 たとえば、Active Directory 内部名 (contoso.local) と Active Directory UPN (contoso.com) が与えられている場合、コレクションの作成時にその内部名を使用する必要があります。





