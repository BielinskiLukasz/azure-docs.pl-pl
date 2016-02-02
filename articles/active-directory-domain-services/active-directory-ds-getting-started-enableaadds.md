<properties
    pageTitle="Azure Active Directory ドメイン サービス プレビュー: 作業の開始 | Microsoft Azure"
    description="Azure Active Directory ドメイン サービスの概要"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="udayh"
    editor="inhenk"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2015"
    ms.author="maheshu"/>


# Azure AD Domain Services *(プレビュー)* - 作業の開始

## ステップ 3: Azure AD Domain Services を有効にする

この手順では、ディレクトリに対して Azure AD Domain Services を有効にすることができます。 ディレクトリに対して Azure AD Domain Services を有効にするには、次の構成手順を実行します。

1. 移動し、 **Azure 管理ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com))します。
2. 左ウィンドウで、**[Active Directory]** を選択します。
3. Azure AD Domain Services を有効にする Azure AD テナント (ディレクトリ) を選択します。

    ![Azure AD ディレクトリの選択](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. **[構成]** タブをクリックします。

    ![ディレクトリの ](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. **[domain services]** というタイトルのセクションまで下にスクロールします。

    ![Domain Services 構成セクション](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. **[このディレクトリのドメイン サービスを有効にします]** というタイトルのオプションを **[はい]** に切り替えます。 Azure AD Domain Services 用の構成オプションがページに表示されます。

    ![Domain Services の有効化](./media/active-directory-domain-services-getting-started/enable-domain-services.png)
    > [AZURE.NOTE] テナントに対して Azure AD Domain Services を有効にすると、ユーザーを認証するために必要な Kerberos と NTLM の資格情報のハッシュが Azure AD によって生成され、保存されます。

7. **[ドメイン サービスの DNS ドメイン名]** を指定します。
   - ディレクトリの既定のドメイン名 (**.onmicrosoft.com** ドメイン サフィックスで終わる名前) が既定で選択されます。
   - 一覧には、Azure AD ディレクトリに対して構成されたすべてのドメインが含まれます。検証対象のドメインおよび [ドメイン] タブで構成する検証対象外のドメインなどがあります。
   - さらに、この一覧には、カスタム ドメイン名を入力して追加することもできます。
     > [AZURE.WARNING] (例: 指定したドメイン名のドメインのプレフィックスことを確認します。 'contoso' ['contoso.local' ドメイン名) は、15 文字未満です。 ドメイン プレフィックスが 15 文字より長いと、Azure AD Domain Services ドメインを作成することはできません。

8. 次の手順は、Azure AD Domain Services を利用できる仮想ネットワークを選択することです。 先ほど作成した仮想ネットワークを、**[ドメイン サービスをこの仮想ネットワークに接続します]** というタイトルのボックスの一覧から選択します。
   - 指定した仮想ネットワークが Azure AD Domain Services でサポートされている Azure リージョンに属することを確認します。
   - 参照してください、 [リージョン ページ](active-directory-ds-regions.md) サポートされている Azure リージョンの一覧を表示します。

9. 上記のオプションを選択したら、ページ下部のタスク ウィンドウで **[保存]** をクリックして、Azure AD Domain Services を有効にします。
10. ページが表示されます、' 保留中] ' Azure AD ドメイン サービスが有効になって、ディレクトリの中には、状態です。

    ![Domain Services の有効化 - 保留中状態](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)
    > [AZURE.NOTE] Azure AD Domain Services は、管理対象ドメインに対して高可用性を提供します。 Azure AD Domain Services を初めて有効にすると、仮想ネットワーク上で Domain Services を利用できる IP アドレスが 1 つづつ表示されます。 2 番目の IP アドレスは、このサービスがドメインに対する高可用性を有効にするとすぐに表示されます。 ドメインに対する高可用性が構成され、アクティブになると、**[構成]** タブの **[domain services]** セクションに 2 つの IP アドレスが表示されます。

11. 約 20 ～ 30 分後に、仮想ネットワーク上で Domain Services を利用できる最初の IP アドレスが、**[構成]** ページの **[IP アドレス]** フィールドに表示されます。

    ![Domain Services が有効化された - 最初の IP がプロビジョニングされた](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

12. 高可用性がドメインで運用されると、2 つの IP アドレスがページに表示されます。 これらは、選択した仮想ネットワーク上で Azure AD Domain Services を利用できる IP アドレスです。 仮想ネットワークの DNS 設定を更新できるように、これらの IP アドレスをメモしておきます。 この手順は、仮想ネットワーク上の仮想マシンが、ドメインへの参加などの操作のためにドメインに接続できるようにします。

    ![Domain Services が有効化された - 2 つの IP がプロビジョニングされた](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Azure AD ディレクトリのサイズ (ユーザーやグループの数など) によっては、Azure AD Domain Services でディレクトリの内容が利用できるようになるまで、多少時間がかかります。 この同期処理は、バック グラウンドで発生します。 数万のオブジェクトがある大きなディレクトリの場合、すべてのユーザー、グループ メンバーシップ、資格情報を同期して Azure AD Domain Services で利用できるようになるまで、1 ～ 2 日かかることがあります。


---

[* * 次の手順で、Azure 仮想 network.* * の更新プログラムの DNS の設定](active-directory-ds-getting-started-dns.md)





