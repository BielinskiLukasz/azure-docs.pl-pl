<properties 
    pageTitle="クラウド サービス向けの SSL の構成 | Microsoft Azure" 
    description="Web ロールの HTTPS エンドポイントを指定する方法および SSL 証明書をアップロードしてアプリケーションを保護する方法を説明します。" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2015"
    ms.author="adegeo"/>





# Azure でアプリケーションの SSL を構成する

> [AZURE.SELECTOR]
- [Azure classic portal](cloud-services-configure-ssl-certificate.md)
- [Azure portal](cloud-services-configure-ssl-certificate-portal.md)


Secure Socket Layer (SSL) の暗号化は、インターネットを介して送信されるデータをセキュリティで保護する際に最もよく使用される方法です。 この一般的なタスクでは、Web ロールの HTTPS エンドポイントを指定する方法および SSL 証明書をアップロードしてアプリケーションを保護する方法を説明します。
> [AZURE.NOTE] このタスクの手順は Azure クラウド サービスに適用します。web サイトの場合、次を参照してください。 [Azure の web サイトの SSL 証明書を構成する](../web-sites-configure-ssl-certificate.md)します。

このタスクでは、運用環境のデプロイを使用します。ステージング環境のデプロイを使用する場合に関する情報については、このトピックの最後で紹介します。

読み取り [この](cloud-services-how-to-create-deploy.md) 最初のクラウド サービスを作成がない場合。

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]


## 手順 1. SSL 証明書を取得する

アプリケーションの SSL を構成するには、最初に、セキュリティ保護のための証明書を発行する信頼されたサード パーティである、証明機関 (CA) によって署名された SSL 証明書を取得する必要があります。 まだ SSL 証明書がない場合は、SSL 証明書を販売する会社から取得する必要があります。

証明書は、Azure における SSL 証明書の次の要件を満たす必要があります。

-   証明書は秘密キーを含む必要があります。
-   証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
-   証明書の件名はクラウド サービスへのアクセスに使用されるドメインと一致する必要があります。 証明機関 (CA) から cloudapp.net ドメインの SSL 証明書を取得することはできません。 サービスにアクセスするときに使用するカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書の件名がアプリケーションにアクセスするために使用するカスタム ドメイン名と一致している必要があります。 たとえば、カスタム ドメイン名が **contoso.com** である場合は、***.contoso.com** または **www.contoso.com** の証明書を CA に要求します。
-   証明書では、2048 ビット以上の暗号化を使用する必要があります。

テスト目的ですることができます [作成](cloud-services-certs-create.md) 自己署名証明書を使用します。 自己署名証明書は CA を通じて認証されないため、cloudapp.net ドメインを Web サイト URL として使用できます。 たとえば、下のタスクが、証明書に使用される共通名 (CN) の自己署名証明書を使用して **sslexample.cloudapp.net**します。

次に、この証明書に関する情報を、サービス定義ファイルおよびサービス構成ファイルに含める必要があります。

## ステップ 2: サービス定義ファイルとサービス構成ファイルを変更する

アプリケーションは、証明書を使用するように構成する必要があります。また、HTTPS エンドポイントを追加する必要があります。 その結果として、サービス定義ファイルおよびサービス構成ファイルを更新する必要があります。

1.  お使いの開発環境で、サービス定義ファイル
    (CSDEF) を追加、 **証明書** セクション内、 **WebRole**
    セクション内に追加し、証明書に関する次の情報を追加します
    。

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                             storeLocation="LocalMachine" 
                             storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    **Certificates** セクションでは、証明書の名前、場所、およびこの証明書があるストアの名前を定義します。 CA (証明機関) ストアにこの証明書を保存することを選択しましたが、その他のオプションを選択することもできます。 参照してください [サービスで、証明書に関連付ける方法][] の詳細。

2.  サービス定義ファイルで追加、 **InputEndpoint** 要素
    内で、 **エンドポイント** セクション HTTPS を有効にします。

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  サービス定義ファイルで追加、 **バインド** 内の要素
    **サイト** セクションです。 これにより、HTTPS バインドが追加され、
    エンドポイントがサイトにマップされます。

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    サービス定義ファイルに対して必要な変更はすべて完了しましたが、
    サービス構成ファイルに証明書の情報を追加する必要もあります
    。

4.  サービス構成ファイル (CSCFG) である ServiceConfiguration.Cloud.cscfg で追加、 **証明書**
    セクション内、 **ロール** ] セクションで、サンプルのサムプリントを置き換える
    次に示す拇印値のサンプルを証明書の拇印値に置き換えます。

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>


(上記の例では、拇印アルゴリズムに **sha1** を使用しています。 証明書の拇印アルゴリズムに適切な値を指定してください。)

サービス定義ファイルとサービス構成ファイルが更新されたので、
Azure にアップロードするためにデプロイをパッケージ化します もし
使用している **cspack**, 、使用しないことを確認します
**/generateConfigurationFile** フラグが上書きされると、
先ほど挿入した証明書情報が上書きされるためです。

## ステップ 3: 証明書のアップロード

展開パッケージがこの証明書を使用するように更新され、HTTPS エンドポイント
が追加されました。 これで、管理ポータルを使用して
Azure に Azure クラシック ポータルで証明書。

1. ログイン、 [Azure クラシック ポータルの [][]します。
2. 左側のナビゲーション ウィンドウで、**[Cloud Services]** をクリックします。
3. 目的のクラウド サービスをクリックします。
4. **[証明書]** タブをクリックします。

    ![[証明書] タブをクリックします。](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. **[アップロード]** ボタンをクリックします。

    ![アップロード](./media/cloud-services-configure-ssl-certificate/upload-button.png)

6. **[ファイル]**、**[パスワード]** を指定し、**[完了]** (チェック マーク) をクリックします。

## ステップ 4: HTTPS を使用してロール インスタンスに接続する

Azure でデプロイを実行できるようになったため、HTTPS を使用して
接続できます。

1.  Azure クラシック ポータルでデプロイを選択し、**[サイトの URL]** の下にあるリンクをクリックします。

    ![サイトの URL の確認][2]

2.  Web ブラウザーで、**http** ではなく **https** を使用するようにリンクを修正し、ページにアクセスします。

    **注:** 場合は、自己署名証明書を使用しているときにします。
    自己署名証明書に関連付けられている HTTPS エンドポイントを参照すると、
    ブラウザーで証明書エラーが表示されます。 信頼された
    証明機関によって署名された証明書を使用すると、この問題は解消されます。それまでの間、このエラーは無視してかまいません (また、信頼された証明書機関のユーザーの証明書ストアに自己署名証明書を追加する方法もあります)。

    ![SSL のサンプル Web サイト][3]

運用環境のデプロイではなくステージング環境のデプロイに SSL を使用する場合は、最初に、ステージング環境のデプロイに使用されている URL を確認する必要があります。 証明書または証明書情報を含めずに、ステージング環境にクラウド サービスをデプロイしてください。 展開すると、Azure クラシック ポータルの **[サイトの URL]** に表示される、GUID ベースの URL を確認できます。 GUID ベースの URL (**32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net** など) と同じ共通名 (CN) で証明書を作成し、その証明書をステージングされたクラウド サービスに Azure クラシック ポータルを使用して追加します。CSDEF ファイルと CSCFG ファイルに証明書情報を追加し、アプリケーションの再パッケージ化を実行して、新しいパッケージと CSCFG ファイルを使用するようステージング デプロイを更新します。

## 次のステップ

* [、クラウド サービスの一般的な構成](cloud-services-how-to-configure.md)します。
* 学習方法 [クラウド サービス デプロイ](cloud-services-how-to-create-deploy.md)します。
* 構成、 [カスタム ドメイン名](cloud-services-custom-domain-name.md)します。
* [クラウド サービスを管理する](cloud-services-how-to-manage.md)します。



[azure classic portal]: http://manage.windowsazure.com 
[0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png 
[1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png 
[2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png 
[3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png 
[4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png 

