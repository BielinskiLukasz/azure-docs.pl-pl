<properties 
   pageTitle="ホスト名の表示と変更 | Microsoft Azure"
   description="名前解決のため、Azure Virtual Machines のホスト名、および Web ロールと Worker ロールを表示、変更する方法"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />


# ホスト名の表示と変更

ロール インスタンスをホスト名で参照できるようにするには、各ロールのサービス構成ファイルでホスト名の値を設定する必要があります。 そのためには、使用するホスト名を **Role** 要素の **vmName** 属性に追加します。 **vmName** 属性の値は、各ロール インスタンスのホスト名に対するベースとして使用されます。 たとえば、**vmName** が *webrole* であり、そのロールに 3 つのインスタンスがある場合、インスタンスのホスト名は *webrole0*、*webrole1*、*webrole2* になります。 仮想マシンのホスト名は、仮想マシン名に基づいて設定されるため、構成ファイルでバーチャル マシンのホスト名を指定する必要はありません。Microsoft Azure サービスの構成の詳細については、を参照してください [Azure サービス構成スキーマ (.cscfg ファイル)](https://msdn.microsoft.com/library/azure/ee758710.aspx)。

## ホスト名の表示

さまざまなツールを使用して、クラウド サービスで仮想マシンとロール インスタンスのホスト名を表示することができます。 Azure ポータル、サービス構成ファイル、リモート デスクトップと [Azure サービス管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)します。

### Azure ポータル

Azure ポータルを使用して、仮想マシンのホスト名を仮想マシン ダッシュボード ページに表示できます。 ダッシュボードには **[名前]** と **[ホスト名]** の値が表示されることに注意してください。 これらは最初は同じですが、ホスト名を変更しても、仮想マシンまたはロール インスタンスの名前は変わりません。

ロール インスタンスは Azure ポータルでもわかりますが、クラウド サービスでインスタンスの一覧を表示しても、ホスト名は表示されません。 各インスタンスの名前は表示されますが、その名前はホスト名ではありません。

### サービス構成ファイル

Azure ポータルのサービスの **[構成]** ページから、デプロイされているサービスのサービス構成ファイルをダウンロードできます。 その後、**Role name** 要素の **vmName** 属性で、ホスト名を確認できます。 このホスト名は各ロール インスタンスのホスト名に対するベースとして使用されることに留意してください。 たとえば、**vmName** が *webrole* であり、そのロールに 3 つのインスタンスがある場合、インスタンスのホスト名は *webrole0*、*webrole1*、*webrole2* になります。

### リモート デスクトップ

仮想マシンまたはロール インスタンスに対するリモート デスクトップ接続 (Windows)、Windows PowerShell リモート処理接続 (Windows)、または SSH 接続 (Linux と Windows) を有効にした後は、さまざまな方法でアクティブなリモート デスクトップ接続からホスト名を表示できます。

- コマンド プロンプトまたは SSH ターミナルで「hostname」と入力します。

- コマンド プロンプトで「ipconfig /all」と入力します (Windows のみ)。

- システム設定でコンピューター名を表示します (Windows のみ)。

### Azure Service Management REST API

REST クライアントから次の手順を実行します。

1. Azure ポータルに接続するためのクライアント証明書があることを確認します。 クライアント証明書を取得するに表示される手順に従います [方法: ダウンロードとインポート発行の設定とサブスクリプション情報](https://msdn.microsoft.com/library/dn385850.aspx)します。

1. x-ms-version という名前のヘッダー エントリの値を 2013-11-01 に設定します。

1. 次の形式で要求を送信: https://management.core.windows.net/\<subscrition-id\>/services/hostedservices//\<service-name\>? 埋め込む詳細 = true

1. 各 **RoleInstance** 要素の **HostName** 要素を検索します。

>[AZURE.WARNING] **InternalDnsSuffix** 要素を調べて、リモート デスクトップ セッションのコマンド プロンプトから ipconfig /all を実行して (Windows)、または SSH 端末から cat /etc/resolv.conf を実行して (Linux)、REST 呼び出しの応答からクラウド サービスの内部ドメイン サフィックスを表示することもできます。

## ホスト名の変更

変更したサービス構成ファイルをアップロードすることにより、またはリモート デスクトップ セッションからコンピューターの名前を変更することにより、仮想マシンまたはロール インスタンスのホスト名を変更できます。

## 次のステップ

[名前解決 (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure サービス構成スキーマ (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure 仮想ネットワーク構成スキーマ)](http://go.microsoft.com/fwlink/?LinkId=248093)

[ネットワーク構成ファイルを使用して DNS 設定を指定します。](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)





