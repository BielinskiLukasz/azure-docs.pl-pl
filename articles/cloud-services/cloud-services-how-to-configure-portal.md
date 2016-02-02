<properties 
    pageTitle="クラウド サービスを構成する方法 |Microsoft Azure" 
    description="Azure のクラウド サービスの構成方法について説明します。クラウド サービスの構成の更新方法と、ロール インスタンスへのリモート アクセスの構成方法を紹介します。これらの例では、Azure ポータルを使用します。" 
    services="cloud-services" 
    documentationCenter="" 
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





# Cloud Services の構成方法

> [AZURE.SELECTOR]
- [Azure classic portal](cloud-services-how-to-configure.md)
- [Azure portal](cloud-services-how-to-configure-portal.md)


クラウド サービスで最もよく使用される設定は Azure ポータルで構成できます。 また、構成ファイルを直接更新する場合は、サービス構成ファイルをダウンロードして内容を更新し、更新したファイルをアップロードして、クラウド サービスの構成を更新します。 どちらの方法でも、構成の更新はすべてのロール インスタンスに適用されます。

クラウド サービスで実行中の 1 つまたはすべてのロールへのリモート デスクトップ接続を有効にすることもできます。 リモート デスクトップを使用すると、アプリケーションの実行中にそのデスクトップにアクセスし、問題のトラブルシューティングや診断を行うことができます。 アプリケーション開発時にリモート デスクトップのサービス定義ファイル (.csdef) を構成しなかった場合でも、ロールへのリモート デスクトップ接続を有効にできます。 リモート デスクトップ接続を有効にするために、アプリケーションを再デプロイする必要はありません。

Azure で構成の更新中に 99.95% の可用性を保証できるのは、各ロールに少なくとも 2 つのロール インスタンスがある場合だけです。 この場合、1 台の仮想マシンでクライアントからの要求を処理し、もう 1 台で更新を行うことができます。 詳細については、次を参照してください。 [サービス レベル アグリーメント](http://azure.microsoft.com/support/legal/sla/)します。

## クラウド サービスの変更

1. [Azure ポータル](http://portal.azure.com/), 、クラウド サービスに移動します。

2. **[設定]** アイコンまたは **[Essentials]/[すべての設定]** リンクをクリックして、**[設定]** ブレードを開きます。

    ![[設定] ページ](./media/cloud-services-how-to-configure-portal/cloud-service.png)

    ここから、**プロパティ**の表示、**構成**の変更、**証明書**の管理、およびこのクラウド サービスにアクセスできる**ユーザー**の管理を行うことができます。

2. **[監視]** セクションで、任意のタイルをクリックして、アラートを構成できます。

    ![クラウド サービスの監視](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

3. **[ロールとインスタンス]** セクションで、クラウド サービスの任意のロールをクリックして、インスタンスを管理できます。

    ![クラウド サービス インスタンス](./media/cloud-services-how-to-configure-portal/cs-instance.png)

    ここから、クラウド サービスへのリモート接続、クラウド サービスの再起動または再イメージ化を行うことができます。

    ![クラウド サービス インスタンスのボタン](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

>[AZURE.NOTE]
>使用して、クラウド サービスで使用されるオペレーティング システムを変更することはできません、 **Azure ポータル**, 、この設定を変更することができます、 [Azure クラシック ポータル](http://manage.windowsazure.com/)します。 詳細については [ここ](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file)します。

## クラウド サービス構成ファイルの更新

1. まず、既存のクラウド サービス構成ファイル (.cscfg) をダウンロードします。

    1. [Azure ポータル](http://portal.azure.com/), 、クラウド サービスに移動します。

    2. **[設定]** アイコンまたは **[Essentials]/[すべての設定]** リンクをクリックして、**[設定]** ブレードを開きます。

        ![[設定] ページ](./media/cloud-services-how-to-configure-portal/cloud-service.png)

    3. **[構成]** 項目をクリックします。

        ![[構成] ブレード](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)

    4. **[ダウンロード]** ボタンをクリックします。

        ![ダウンロード](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

2. サービス構成ファイルを更新した後、次のステップでファイルをアップロードして構成の更新内容を適用します。

    1. 上記の最初の 3 つの手順に従って、クラウド サービスの **[構成]** ブレードを開きます。

    2. **[アップロード]** ボタンをクリックします。

        ![アップロード](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)

    3. .cscfg ファイルを選択し、**[OK]** をクリックします。

## ロール インスタンスへのリモート アクセスの構成

使用してリモート アクセスを構成することはできません、 **Azure ポータル**, 、この設定を変更することができます、 [Azure クラシック ポータル](http://manage.windowsazure.com/)します。 これについては説明 [ここ](cloud-services-role-enable-remote-desktop.md)します。

## 次のステップ

* 学習方法 [クラウド サービス デプロイ](cloud-services-how-to-create-deploy-portal.md)します。
* 構成、 [カスタム ドメイン名](cloud-services-custom-domain-name-portal.md)します。
* [クラウド サービスを管理する](cloud-services-how-to-manage-portal.md)します。
* 構成 [ssl 証明書](cloud-services-configure-ssl-certificate-portal.md)します。




