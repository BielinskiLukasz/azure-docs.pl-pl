<properties
    pageTitle="Azure App Service でのカスタム ドメイン名の構成 (GoDaddy)"
    description="GoDaddy から購入したカスタム ドメイン名を Azure Web Apps で使用する方法"
    services="app-service"
    documentationCenter=""
    authors="erikre"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/23/2015"
    ms.author="erikre"/>


# Azure App Service でのカスタム ドメイン名の構成 (GoDaddy から直接購入した場合)

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

Azure App Service Web Apps を使用してドメインを購入した場合はの最後の手順を参照してください [Web アプリ用のドメインの購入](custom-dns-web-site-buydomains-web-app.md)します。

この記事から直接購入したカスタム ドメイン名の使用方法については [GoDaddy](https://godaddy.com) と [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)します。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## DNS レコードについて

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>
## カスタム ドメインの DNS レコードの追加

カスタム ドメインを App Service の Web アプリケーションに関連付けるには、GoDaddy のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。 次の手順を使用して GoDaddy.com の DNS ツールを見つけます。

1. GoDaddy.com のアカウントにログオンし、**[My Account]**、**[Manage my domains]** の順に選択します。 最後に、Azure Web アプリで使用するドメイン名をドロップダウン メニューで選択し、**[Manage DNS]** を選択します。

    ![GoDaddy のカスタム ドメイン ページ](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. **[Domain details]** ページの **[DNS Zone File]** タブまでスクロールします。 このセクションを使用して、ドメイン名の DNS レコードを追加したり変更したりします。

    ![[DNS Zone File] タブ](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

    既存のレコードを追加するには、**[Add Record]** を選択します。

    既存のレコードを**編集**するには、レコードの横にあるペンと紙のアイコンを選択します。
    > [AZURE.NOTE] 新しいレコードを追加する前に、GoDaddy では一般的なサブドメイン (Editor では **[Host]**) の DNS レコード (**[email]**、**[files]**、**[mail]** など) が既に作成されていることに注意してください。 使用する名前が既に存在する場合は、新しいレコードを作成せずに既存のレコードを変更してください。

4. レコードを追加する場合は、まずレコードの種類を選択する必要があります。

    ![レコードの種類の選択](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

    次に、**[Host]** (カスタム ドメインまたはサブドメイン) および **[Points to]** を指定する必要があります。

    ![ゾーン レコードの追加](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

    * **A (ホスト) レコード**を追加するときは、**[Host]** フィールドを、**@** (**contoso.com** などのルート ドメイン名)、* (複数のサブドメインに一致するワイルドカード)、または使用するサブドメイン (**www** など) のいずれかに設定する必要があります。 **[Points to]** フィールドを、Azure Web アプリの IP アドレスに設定する必要があります。

    * **CNAME (エイリアス) レコード**を追加するときは、**[Host]** フィールドを、使用するサブドメインに設定する必要があります。 たとえば **www** にします。 **[Points to]** フィールドを、Azure Web アプリの **.azurewebsites.net** ドメイン名に設定する必要があります。 たとえば **contoso.azurwebsites.net** にします。

5. **[Add Another]** をクリックします。
6. 選択 **CNAME** レコードの種類を指定し、 **ホスト** の値 **awverify** と **指す** の値 **awverify. < yourwebappname >. azurewebsites.net**します。
    > [AZURE.NOTE] この CNAME レコードが Azure で使用され、A レコードまたは最初の CNAME レコードで記述されたドメインの所有者であることが検証されます。 ドメインが Azure ポータルで Web アプリにマップされた後、 **awverify** エントリを削除できます。

5. レコードの追加または変更が完了したら、**[Finish]** をクリックして変更を保存します。

<a name="enabledomain"></a>
## Web アプリケーションでのドメイン名の有効化

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]
>[AZURE.NOTE] 場合は、Azure アカウントがサインアップする前に Azure App Service の使用を開始するには、 [App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751), 、App Service で有効期間の短いスターター web アプリをすぐに作成する場所です。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容

* Web サイトから App Service への変更のガイドを参照してください: [Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)





