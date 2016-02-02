# Azure の Web サイトのカスタム ドメイン名の構成

Web サイトを作成するときに Azure は、ユーザーは http://&lt;mysite&gt;.azurewebsites.net のような URL を使用して web サイトにアクセスできるように、azurewebsites.net ドメイン上フレンドリ サブドメインを提供します。 ただし、Web サイトを Shared モードまたは Standard モード用に構成すると、Web サイトを独自のドメイン名にマップできます。

必要に応じて、Azure Traffic Manager を使用して Web サイトへの着信トラフィックを負荷分散できます。 Web サイトが Traffic Manager のしくみの詳細については、次を参照してください。 [Azure トラフィック マネージャー ][trafficmanager]します。
> [AZURE.NOTE] このタスクの手順は、Azure の web サイトに適用します。クラウド サービスを参照してください。 <a href="/develop/net/common-tasks/custom-dns/">Azure でのカスタム ドメイン名の構成</a>します。

> [AZURE.NOTE] このタスクの手順は、Web サイトを Shared モードまたは Standard モードに構成することを必要とします。サブスクリプションに対して課金される金額が変更される可能性があります。 参照してください <a href="/pricing/details/web-sites/">Websites の料金詳細</a> の詳細。

この記事の内容:

-   [Cname レコードと A レコードをについてください。](#understanding-records)
-   [Shared または standard モードの web サイトの構成します。](#bkmk_configsharedmode)
-   [Web サイトの Traffic Manager への追加します。](#trafficmanager)
-   [カスタム ドメインの cname レコードを追加します。](#bkmk_configurecname)
-   [カスタム ドメインの A レコードを追加します。](#bkmk_configurearecord)

<h2>
            <a name="understanding-records"></a>
            CNAME レコードと A レコードについて
          </h2>

CNAME レコード (またはエイリアス レコード) および A レコードはどちらもドメイン名を Web サイトに関連付けることができますが、それぞれ機能は異なります。

### CNAME レコードまたはエイリアス レコード

CNAME レコードは、*contoso.com* や **www.contoso.com** などの**特定の**ドメインを正規のドメイン名にマップします。 この場合、正規のドメイン名は Azure Website の **&lt;myapp&gt;.azurewebsites.net** ドメイン名または Traffic Manager プロファイルの **&lt;myapp&gt;.trafficmgr.com** ドメイン名です。 CNAME を作成すると、**&lt;myapp&gt;.azurewebsites.net** ドメイン名または **&lt;myapp&gt;.trafficmgr.com** ドメイン名のエイリアスが CNAME によって作成されます。 CNAME エントリは **&lt;myapp&gt;.azurewebsites.net** ドメイン名または **&lt;myapp&gt;.trafficmgr.com** ドメイン名の IP アドレスを自動的に解決するため、Web サイトの IP アドレスが変更されても、特別な対応を行う必要はありません。
> [AZURE.NOTE] いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (contoso.com など) ではなく、サブドメイン (www.contoso.com など) のみです。 CNAME レコードの詳細については、レジストラーが提供するドキュメントを参照してください。 <a href="http://en.wikipedia.org/wiki/CNAME_record">CNAME レコードに関するウィキペディア</a>, 、または <a href="http://tools.ietf.org/html/rfc1035">IETF ドメイン名 - 実装と仕様書</a> ドキュメントです。

### A レコード

A レコードがなど、ドメインをマップ **contoso.com** または **www.contoso.com**, 、*またはワイルドカード ドメイン* など **\*.contoso.com**, 、IP アドレスにします。 Azure Web サイトの場合、サービスの仮想 IP または Web サイト用に購入した特定の IP アドレスです。 CNAME レコードと比較したときの A レコードの主な利点は、ワイルドカードを使用する 1 つのエントリを使用することができて (***.contoso.com** など)、複数のサブドメイン (**mail.contoso.com**、**login.contoso.com**、**www.contso.com** など) の要求を処理できることです。
> [AZURE.NOTE] A レコードは静的 IP にマップされるため、変更を Web サイトの IP アドレスに自動的に解決することはできません。 A レコードと共に使用する IP アドレスは、Web サイトのカスタム ドメイン名を設定する場合に提供されます。ただし、この値は Web サイトを削除または再作成する場合や Web サイトを無料モードに戻すように変更する場合に変更されることがあります。

> [AZURE.NOTE] A レコードは Traffic Manager での負荷分散に使用することはできません。 詳細については、次を参照してください。 [Azure トラフィック マネージャー ][trafficmanager]します。

<a name="bkmk_configsharedmode"></a><h2>Websites の shared または standard モード用の構成します。</h2>

Web サイトのカスタム ドメイン名は、Azure Websites の Shared モードおよび Standard モードでのみ設定できます。 Website を Free Website モードから Shared モードまたは Standard モードに切り替える場合、最初に Website サブスクリプションに設定されている使用制限を解除する必要があります。 共有モードと標準モードの料金の詳細については、次を参照してください。 [料金の詳細 ][pricingdetails]します。

1. ブラウザーで開く、 [管理ポータルの ][portal]します。
2. **[Websites]** タブで、サイトの名前をクリックします。

    ![][standardmode1]

3. **[スケール]** タブをクリックします。

    ![][standardmode2]

4. **[全般]** セクションで、**[SHARED]** をクリックして Web サイト モードを設定します。

    ![][standardmode3]
    > [AZURE.NOTE] この Web サイトで Traffic Manager を使用する場合は、Shared モードではなく Standard モードを使用する必要があります。

5. **[保存]** をクリックします。
6. Shared モードの追加料金についてのメッセージが表示された場合 (または Standard モードでも同様に)、これに同意するときは **[はい]** をクリックします。


 **注**<br />
 "'Web サイト名' の Web サイトのスケールの構成に失敗しました" というエラーが発生する場合は、詳細ボタンを使用して詳細情報を表示できます。

<a name="trafficmanager"></a><h2>(省略可能)トラフィック マネージャーに、web サイトを追加します。</h2>

Website で Traffic Manager を使用する場合は、次の手順を実行します。

1. Traffic Manager プロファイルがあるない場合は、内の情報を使用して [[createprofile] の簡易作成による Traffic Manager プロファイルを作成する][createprofile] を作成します。 Traffic Manager プロファイルに関連付けられている **.trafficmgr.com** ドメイン名に注意してください。 これは、後の手順で使用します。

2. 内の情報を使用して [の追加と削除エンドポイント ][addendpoint] Traffic Manager プロファイルでエンドポイントとして、web サイトを追加します。
    > [AZURE.NOTE] エンドポイントを追加する際に Web サイトが表示されない場合は、Web サイトが Standard モードに構成されていることを確認します。 Web サイトを Traffic Manager で操作するには、Standard モードを使用する必要があります。

3. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

4. ここで CNAME レコードを選択または入力する場所を探します。 一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。 **CNAME**、**エイリアス**、または **サブドメイン**という単語を探します。

5. また、CNAME のドメインまたはサブドメイン エイリアスを指定する必要があります。 たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。

5. また、この CNAME エイリアスの正規のドメイン名であるホスト名を指定する必要があります。 これは Web サイトの **.trafficmgr.com** の名前です。

たとえば、次の CNAME レコードでは、**www.contoso.com** からのすべてのトラフィックが、Website のドメイン名である **contoso.trafficmgr.com** に転送されます。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>エイリアス/ホスト名/サブドメイン</strong></td>
<td><strong>正規のドメイン</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

訪問者 **www.contoso.com** 本当のホストは参照できません
(contoso.azurewebsite.net) を認識できないため、転送プロセスはエンド ユーザーに表示されません。
> [AZURE.NOTE] Web サイトで Traffic Manager を使用している場合は、次のセクション「**カスタム ドメインの CNAME レコードの追加**」および「**カスタム ドメインの A レコードの追加**」の手順に従う必要はありません。 前の手順で作成された CNAME レコードは、着信トラフィックを Traffic Manager にルーティングし、これは Web サイトのエンドポイントにルーティングされます。

<a name="bkmk_configurecname"></a><h2>カスタム ドメインの cname レコードを追加します。</h2>

CNAME レコードを作成するには、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルに新しいエントリを追加する必要があります。 それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。

1. これらの手段のいずれかを使用して、Website に割り当てられた **.azurewebsite.net** ドメイン名を見つけます。

    * ログイン、 [Azure 管理ポータルの ][portal], を自分の web サイトを選択し、選択 **ダッシュ ボード**, 、見つけて、 **サイトの URL** 内のエントリ、 **概要** セクションです。

    * インストールし、構成 [Azure Powershell](/manage/install-and-configure-windows-powershell/), 、次のコマンドを使用します。

            get-azurewebsite yoursitename | select hostnames

    * インストールし、構成、 [Azure コマンド ライン インターフェイス](/manage/install-and-configure-cli/), 、次のコマンドを使用します。

            azure site domain list yoursitename

    次のステップで使用するため、この **.azurewebsite.net** という名前を保存します。

3. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

4. ここで CNAME レコードを選択または入力する場所を探します。 一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。 **CNAME**、**エイリアス**、または **サブドメイン**という単語を探します。

5. また、CNAME のドメインまたはサブドメイン エイリアスを指定する必要があります。 たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。 ルート ドメインのエイリアスを作成する場合は、レジストラーの DNS ツールに '**@**' シンボルとして示される場合があります。

5. また、この CNAME エイリアスの正規のドメイン名であるホスト名を指定する必要があります。 これは Website の **.azurewebsite.net** の名前です。

たとえば、次の CNAME レコードでは、**www.contoso.com** からのすべてのトラフィックが、Website のドメイン名である **contoso.azurewebsite.net** に転送されます。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>エイリアス/ホスト名/サブドメイン</strong></td>
<td><strong>正規のドメイン</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

訪問者 **www.contoso.com** 本当のホストは参照できません
(contoso.azurewebsite.net) を認識できないため、転送プロセスは
エンド ユーザーに表示されません。
> [AZURE.NOTE] 上の例は、__www__ サブドメインのトラフィックのみに該当します。 CNAME レコードにはワイルドカードを使用できないため、各ドメインおよびサブドメインに 1 つの CNAME を作成する必要があります。 などのサブドメインでは、トラフィックを送信するかどうか *. contoso.com を azurewebsite.net アドレスを構成できます、 __URL リダイレクト__ または __URL 転送__ 、DNS 設定のエントリまたは A レコードを作成します。

> [AZURE.NOTE] CNAME が DNS に反映されるまで多少の時間がかかります。 CNAME が反映されるまで、Website に対して CNAME を設定することはできません。 など、サービスを使用できる <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 、CNAME を利用できることを確認します。

### Web サイトへのドメイン名の追加

ドメイン名の CNAME レコードが反映されたら、それを Website に関連付ける必要があります。 Azure コマンド ライン インターフェイス (Azure CLI) または Azure 管理ポータルを使用して、CNAME レコードで定義されたカスタム ドメイン名を Web サイトに追加することができます。

**コマンド ライン ツールを使用してドメイン名を追加するには**

インストールし、構成、 [Azure コマンド ライン インターフェイス](/manage/install-and-configure-cli/), 、次のコマンドを使用します。

    azure site domain add customdomain yoursitename

たとえば、次では、**www.contoso.com** のカスタム ドメイン名が **contoso.azurewebsite.net** Website に追加されます。

    azure site domain add www.contoso.com contoso

次のコマンドを使用してカスタム ドメイン名が Web サイトに追加されたことを確認できます。

    azure site domain list yoursitename

このコマンドで返される一覧には、カスタム ドメイン名、および既定の **.azurewebsite.net** エントリが含まれます。

**Azure 管理ポータルを使用してドメイン名を追加するには**

1. ブラウザーで開く、 [Azure 管理ポータルの ][portal]します。

2. **[Web サイト]** タブで、サイト名をクリックし、**[ダッシュボード]** を選択して、ページの下部にある **[ドメインの管理]** を選択します。

    ![][setcname2]

6. **[ドメイン名]** ボックスに、構成したドメインの名前を入力します。

    ![][setcname3]

6. チェック マークをクリックしてドメイン名を受け入れます。

構成が完了したら、カスタム ドメイン名は Web サイトの **[構成]** ページの **[ドメイン名]** セクションに表示されます。

<a name="bkmk_configurearecord"></a><h2>カスタム ドメインの A レコードを追加します。</h2>

A レコードを作成するには、まず、Website の IP アドレスを見つける必要があります。 次に、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルにエントリを追加します。 それぞれのレジストラーでは A レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。 また、A レコードの作成に加え、A レコードを確認するために使用される CNAME レコードを作成する必要があります。

1. ブラウザーで開く、 [Azure 管理ポータルの ][portal]します。

2. **[Websites]** タブで、サイト名をクリックし、**[ダッシュボード]** を選択して、画面の下部にある **[ドメインの管理]** を選択します。

    ![][setcname2]

5. **[カスタム ドメインを管理する]** ダイアログで、**[A レコードの構成時に使用する IP アドレス]** を探します。 IP アドレスをコピーします。 これは、A レコードの作成時に使用されます。

5. **[カスタム ドメインを管理する]** ダイアログで、ダイアログ上部のテキストの最後の awverify ドメイン名をメモします。 **mysite** が Website の名前である場合、これは **awverify.mysite.azurewebsites.net** となります。 検証 CNAME レコードを作成する場合に使用されるドメイン名であるため、これをコピーします。

6. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

6. A レコードと CNAME レコードを選択または入力する場所を探します。 一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。

7. A レコードを作成するには、次のステップを実行します。

    1. A レコードを使用するドメインまたはサブドメインを選択または入力します。 たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。 すべてのサブドメインのワイルドカード エントリを作成する場合は、入力 '__*__' です。 これは、**mail.customdomain.com**、**login.customdomain.com**、**www.customdomain.com** などすべてのサブドメインを対象とします。

        ルート ドメインに A レコードを作成する場合は、レジストラーの DNS ツールに ''**@**" シンボルとして示される場合があります。

    2. 表示されたフィールドのクラウド サービスの IP アドレスを入力します。 これによって、A レコードで使用されるドメイン エントリがクラウド サービスのデプロイの IP アドレスに関連付けられます。

        たとえば、次の A レコードでは、すべてのトラフィックが **contoso.com** から、展開されたアプリケーションの IP アドレス名である **137.135.70.239** に転送されます。

      <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>ホスト名/サブドメイン</strong></td>
        <td><strong>IP アドレス</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        この例では、ルート ドメインの A レコードを作成する方法を示します。 すべてのサブドメインをカバーするワイルドカード エントリを作成する場合は、入力 '__*__'、サブドメインとして。

7. 次に、**awverify** のエイリアスがある CNAME レコード、および先に取得した **awverify.mysite.azurewebsites.net** の正規のドメインを作成します。
    > [AZURE.NOTE] レジストラーで使用される awverify のエイリアスもあれば、awverify.www.customdomainname.com または awverify.customdomainname.com の完全エイリアス ドメイン名を必要とするものもあります。

    たとえば、次は A レコード構成を確認するために使用する CNAME レコードを作成します。

   <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>エイリアス/ホスト名/サブドメイン</strong></td>
    <td><strong>正規のドメイン</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.net</td>
    </tr>
    </table>

> [AZURE.NOTE] awverify CNAME が DNS に反映されるまで多少の時間がかかります。 awverify CNAME が反映されるまで、Website に対して A レコードで定義されたカスタム ドメイン名を設定することはできません。 など、サービスを使用できる <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 、CNAME を利用できることを確認します。

### Web サイトへのドメイン名の追加

ドメイン名の **awverify** CNAME レコードが反映されたら、A レコードで定義されたカスタム ドメインを Website に関連付けることができます。 Azure CLI または Azure 管理ポータルを使用して、A レコードで定義されたカスタム ドメイン名を Web サイトに追加することができます。

**Azure コマンド ライン インターフェイス (Azure CLI) を使用してドメイン名を追加するには**

インストールし、構成、 [Azure CLI](/manage/install-and-configure-cli/), 、次のコマンドを使用します。

    azure site domain add customdomain yoursitename

たとえば、次では、**contoso.com** のカスタム ドメイン名が **contoso.azurewebsite.net** Website に追加されます。

    azure site domain add contoso.com contoso

次のコマンドを使用してカスタム ドメイン名が Web サイトに追加されたことを確認できます。

    azure site domain list yoursitename

このコマンドで返される一覧には、カスタム ドメイン名、および既定の **.azurewebsite.net** エントリが含まれます。

**Azure 管理ポータルを使用してドメイン名を追加するには**

1. ブラウザーで開く、 [Azure 管理ポータルの ][portal]します。

2. **[Web サイト]** タブで、サイト名をクリックし、**[ダッシュボード]** を選択して、ページの下部にある **[ドメインの管理]** を選択します。

    ![][setcname2]

6. **[ドメイン名]** ボックスに、構成したドメインの名前を入力します。

    ![][setcname3]

6. チェック マークをクリックしてドメイン名を受け入れます。

構成が完了したら、カスタム ドメイン名は Web サイトの **[構成]** ページの **[ドメイン名]** セクションに表示されます。
> [AZURE.NOTE] A レコードで定義されたカスタム ドメイン名を Web サイトに追加したら、レジストラーから提供されるツールを使用して awverify CNAME レコードを削除できます。 ただし、今後、別の A レコードを追加するには、新しい A レコードで定義された新しいドメイン名を Website に関連付ける前に awverify レコードを再作成する必要があります。

## 次のステップ

-   [Web サイトを管理する方法](/manage/services/web-sites/how-to-manage-websites/)

-   [Web サイトの SSL 証明書を構成します。](/develop/net/common-tasks/enable-ssl-web-site/)










[configure your web sites for shared mode]: #bkmk_configsharedmode 
[configure the cname on your domain registrar]: #bkmk_configurecname 
[configure a cname verification record on your domain registrar]: #bkmk_configurecname 
[configure an a record for the domain name]: #bkmk_configurearecord 
[set the domain name in management portal]: #bkmk_setcname 
[pricingdetails]: /pricing/details/ 
[portal]: http://manage.windowsazure.com 
[digweb]: http://www.digwebinterface.com/ 
[cloudservicedns]: ../articles/custom-dns.md 
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md 
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md 
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md 
[setcname1]: ../media/dncmntask-cname-5.png 
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png 
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png 
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png 
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png 
[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png 
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png 

