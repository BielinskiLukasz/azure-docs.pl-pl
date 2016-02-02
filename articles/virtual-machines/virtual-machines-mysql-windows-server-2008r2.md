<properties
    pageTitle="MySQL を実行する VM の作成 | Microsoft Azure"
    description="Windows Server 2012 R2 を実行するクラシック デプロイモデルで作成された Azure 仮想マシンを作成し、そこで MySQL データベースのインストールと構成を行います。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/09/2015"
    ms.author="cynthn"/>



# Windows Server 2012 R2 を実行するクラシック デプロイ モデルで作成された仮想マシンに MySQL をインストールする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] リソース マネージャーのモデルです。


[MySQL](http://www.mysql.com) は一般的なオープン ソースの SQL データベース。 使用して、 [Azure クラシック ポータル](http://manage.windowsazure.com), 、イメージ ギャラリーから Windows Server 2012 R2 を実行する仮想マシンを作成することができます。 次に、MySQL Server としてこれをインストールと構成できます。

Linux の MySQL をインストールする方法の詳細についてを参照してください: [Azure で MySQL をインストールする方法](virtual-machines-linux-install-mysql.md)します。

このチュートリアルでは、次の操作方法について説明します。

- Azure クラシック ポータルを使用して Windows Server 2012 R2 を実行する仮想マシンを作成します。

- MySQL 5.6.23 の Community バージョンを MySQL Server としてこの仮想マシン上にインストールと実行します。


## Windows Server を実行する仮想マシンの作成

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## データ ディスクの接続

仮想マシンを作成したら、追加のデータ ディスクを必要に応じて接続できます。 運用環境のワークロードとオペレーティング システムを含む OS ドライブ (c:) の容量の不足を回避する推奨されています。

参照してください [Windows 仮想マシンにデータ ディスクをアタッチする方法](storage-windows-attach-disk.md) の空のディスクを接続する手順に従います。 ホストのキャッシュ設定を **[なし]** または **[読み取り専用]** に設定します。

## 仮想マシンへのログオン

次に、MySQL をインストールするために、仮想マシンにログオンします。

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 仮想マシンに MySQL Community Server をインストールして実行する

MySQL Server の Community バージョンをインストール、構成、と実行するには、次の手順を実行します。
> [AZURE.NOTE] これらの手順は、MySQL 5.6.23.0 の Community バージョンと Windows Server 2012 R2 で実行する手順です。 異なるバージョンの MySQL または Windows Server を使用しているユーザーは、同一の結果にならない場合があります。

1.  リモート デスクトップを使用して仮想マシンに接続したら、スタート画面で **[Internet Explorer]** をクリックします。
2.  右上隅の **[ツール]** (歯車のアイコン) を選択して、**[インターネット オプション]** をクリックします。 **[セキュリティ]** タブ、**[信頼済みサイト]** アイコン、**[サイト]** の順にクリックします。 信頼済みサイトの一覧に http://*.mysql.com を追加します。 **[閉じる]**、**[OK]** の順にクリックします。
3.  Internet Explorer のアドレス バーに「http://dev.mysql.com/downloads/mysql/」と入力します。
4.  MySQL サイトで MySQL Installer for Windows の最新バージョンを見つけ、インストールします。 MySQL Installer を選ぶ際、すべてのファイル セットが揃っているバージョンをダウンロードし (ファイル サイズが 282.4 MB の mysql-installer-community-5.6.23.0.msi など)、このインストーラーを保存します。
5.  インストーラーでダウンロードが完了したら、[**実行**]をクリックしてセットアップを起動します。
6.  **[License Agreement]** ページで、ライセンス契約の内容に同意し、**[Next]** をクリックします。
7.  **[Choosing a Setup Type]** ページで目的のセットアップの種類をクリックしてから、**[Next]** をクリックします。 次の手順は、セットアップの種類として **[Server only]** を選択したことを前提にしています。
8.  **[Installation]** ページで、**[Execute]** をクリックします。 インストールが完了したら、**[Next]** をクリックします。
9.  **[Product Configuration]** ページで、**[Next]** をクリックします。
10. **[Type and Networking]** ページで、必要な構成の種類を指定します。さらに、TCP ポートなど接続性のオプションについても必要に応じて指定します。 **[Show Advanced Options]**、**[Next]** の順にクリックします。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_TypeNetworking.png)

11. **[Accounts and Roles]** ページで強力な MySQL のルート パスワードを指定します。 必要に応じて別の MySQL ユーザー アカウントを追加し、**[Next]** をクリックします。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AccountsRoles_Filled.png)

12. **[Windows Service]** ページで、Windows サービスとして MySQL Server を実行するための、既定の設定に対する変更を必要に応じて指定し、**[Next]** をクリックします。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_WindowsService.png)

13. **[Advanced Options]** ページで、必要に応じてログ オプションに対する変更を指定し、**[Next]** をクリックします。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AdvOptions.png)

14. **[Apply Server Configuration]** ページで、**[Execute]** をクリックします。 構成手順の終了後、**[Finish]** をクリックします。
15. **[Product Configuration]** ページで、**[Next]** をクリックします。
16. **[Installation Complete]** ページで、後で内容を検討する場合は **[Copy Log to Clipboard]** をクリックしてから **[Finish]** をクリックします。
17. スタート画面で「**mysql**」と入力してから、**[MySQL 5.6 Command Line Client]** をクリックします。
18. 手順 11. で指定したルート パスワードを入力すると、MySQL を構成するためのコマンドを実行できるプロンプトが表示されます。 コマンドと構文の詳細については、次を参照してください。 [MySQL のリファレンス マニュアル](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)します。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_CommandPrompt.png)

19. また、C:\Program Files (x86)\MySQL\MySQL Server 5.6\my-default.ini ファイルに入力することにより、ベース ディレクトリやデータ ディレクトリ、ドライブなど、サーバー構成に関する既定の設定についても構成できます。 詳細については、次を参照してください。 [5.1.2 Server Configuration Defaults](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)します。

## エンドポイントを構成する

インターネット上の MySQL クライアント コンピューターで MySQL Server サービスを利用可能にするには、MySQL Server サービスがリッスンしている TCP ポートにエンドポイントを構成し、別の Windows ファイアウォール ルールを作成する必要があります。 前の手順 10. で説明した **[Type and Networking]** ページで別のポートを指定した場合を除き、これは TCP ポート 3306 です。

> [AZURE.NOTE] インターネット上のすべてのコンピューターで MySQL サーバー サービスが利用できるようになるため、これを行う上のセキュリティへの影響を慎重に検討する必要があります。 Access Control リスト (ACL) でエンドポイントを使用することができる発信元 IP アドレスのセットを定義できます。 詳細については、次を参照してください。 [仮想マシンに対してエンドポイントを設定する方法](virtual-machines-set-up-endpoints.md)します。


MySQL Server サービスのエンドポイントを構成するには、次のようにします。

1.  Azure クラシック ポータルで、**[仮想マシン]**、MySQL 仮想マシンの名前、**[エンドポイント]** の順にクリックします。
2.  コマンド バーで、**[追加]** をクリックします。
3.  **[仮想マシンにエンドポイントを追加します]** ページで、右矢印をクリックします。
4.  既定の MySQL TCP ポート 3306 を使用している場合は、**[名前]** の **[MySQL]** をクリックし、チェック マークをクリックします。
5.  別の TCP ポートを使用している場合は、**[名前]** に一意の名前を入力します。 プロトコルに **[TCP]** を選択し、**[パブリック ポート]** と **[プライベート ポート]** の両方にポート番号を入力してからチェック マークをクリックします。

## Windows Firewall ルールを追加して MySQL トラフィックを許可する

インターネットからの MySQL トラフィックを許可する Windows ファイアウォール ルールを追加するには、MySQL サーバー仮想マシンで管理者特権の Windows PowerShell コマンド プロンプトで次のコマンドを実行します。

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## リモート接続をテストする

Azure 仮想マシンで実行する MySQL Server サービスへのリモート接続をテストするには、最初に、MySQL Server を実行する仮想マシンが属しているクラウド サービスに対応する DNS 名を特定する必要があります。

1.  Azure クラシック ポータルで、**[仮想マシン]**、MySQL Server 仮想マシンの名前、**[ダッシュボード]** の順にクリックします。
2.  仮想マシンのダッシュボードでは、**[概要]** セクションにある **[DNS 名]** の値をメモします。 たとえば次のようになります。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_DNSName.png)

3.  MySQL または MySQL クライアントを実行するローカル コンピューターで、次のコマンドを実行して MySQL ユーザーとしてログインします。

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    たとえば、MySQL のユーザー名が dbadmin3 で、仮想マシンの DNS 名が testmysql.cloudapp.net の場合、実際に実行するコマンドは次のようになります。

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net



## その他のリソース

MySQL の詳細については、次を参照してください。、 [MySQL のドキュメント](http://dev.mysql.com/doc/)します。





