<properties
   pageTitle="Azure Backup - 暗号化されているディスクを使用した Azure IaaS VM のバックアップ |Microsoft Azure"
   description="IaaS VM のバックアップ中に、Azure Backup が BitLocker や dmcrypt を使用して、どのように暗号化されたデータを扱うかについて学習します。この記事では、暗号化されているディスクを扱う場合のバックアップおよび復元エクスペリエンスにおける違いについて説明します。"
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="11/27/2015"
   ms.author="aashishr"/>


# VM のバックアップ中の暗号化されているディスクの処理

Azure で VM データの暗号化を検討している企業のソリューションは Windows では Bitlocker、Linux マシンでは dmcrypt です。 どちらもボリューム レベルの暗号化ソリューションです。 この記事では、上述の Azure VM のバックアップの設定の詳細、および暗号化されたデータが復元のワークフローへ与える影響について説明します。

## バックアップのしくみ

全体的なソリューションは、VM 層とストレージ層の 2 つで構成されます。

1. VM 層は、ゲスト オペレーティング システムと仮想マシンで実行されているアプリケーションから見たデータの処理を行います。 また、ディスクへ書き込む前にボリューム上のデータを透過的に暗号化する、暗号化ソフトウェア (Bitlocker または dmcrypt) を実行する層でもあります。
2. ストレージ層は、ページ BLOB と VM に接続されたディスクの処理を行います。 この層は、ディスクに書き込まれるデータの情報、およびデータが暗号化されているかどうかといった情報を持ちません。 この層では、VM のバックアップ機能が動作します。

![Bitlocker の暗号化と Azure VM のバックアップの共存の仕組み](./media/backup-azure-vms-encryption/how-it-works.png)

データ全体の暗号化は、VM 層で透過的かつシームレスに行われます。 したがって、VM に接続されているページ BLOB に書き込まれたデータは、暗号化データです。  [Azure Backup は、VM のディスクと転送データのスナップショットを取得](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines), 、ページ blob に暗号化されたデータをコピーします。

## ソリューションのコンポーネント

このソリューションを機能させるためには、適切に構成し、管理する必要がある部分が次のように数多く存在します。

| 関数| 使用されるソフトウェア| その他のメモ|
| -------- | ------------- | ------- |
| 暗号化| Bitlocker または dmcrypt| Azure Backup と比較した場合、暗号化は*異なる*層で実行されるため、どの暗号化ソフトウェアが使用されるかは問題になりません。ただし、このエクスペリエンスは Bitlocker と dmcrypt だけで検証されました。<br><br>データを暗号化するには、キーが必要になります。また、データへの承認されたアクセスを確保するためにも安全にキーを保持する必要があります。|
| キー管理| CloudLink SecureVM<br>または Azure key Vault| データを暗号化または復号化するためにキーは不可欠です。適切なキーがない場合、データは取得できません。これになります *信じられないほど* で重要な:<br><li>キーのロール オーバー<li>長期保有期間<br><br>7 年前にデータのバックアップに使用されるキーは、現在使用されている同じキーではないことがなどです。7 年前のキーがない場合、当時のバックアップから復元されるデータは使用できなくなるということです。|
| データのバックアップ| Azure Backup| Azure のバックアップを使用して、Azure IaaS Vm のバックアップを使用して、 [Azure 管理ポータル](http://manage.windowsazure.com) または PowerShell を使用して|
| データの復元| Azure Backup| Azure Backup を使用して、復旧ポイントからディスクまたは VM 全体を復元します。Azure Backup では、データは復元操作の一環として復号化されません。|
| 復号化| Bitlocker または dmcrypt| 復元されたデータ ディスクまたは復元された VM からデータを読み取るためには、キー管理ソフトウェアからのキーがソフトウェアで必要になります。適切なキーがない場合、データは複合化できません。|

> [AZURE.IMPORTANT]  (キーのロールオーバーを含む) キー管理は、Azure Backup の一部ではありません。 この側面については独立して管理する必要がありますが、全体的なバックアップ/復元操作において非常に重要です。

## CloudLink SecureVM

[CloudLink SecureVM](http://www.cloudlinktech.com/choose-your-cloud/microsoft-azure/) Azure IaaS 仮想マシン データを保護するために使用する VM の暗号化ソリューションです。 CloudLink SecureVM は、Azure Backup でサポートされています。

### サポート情報

- CloudLink SecureVM バージョン 4.0 (ビルド 21536.121 以降)
- Azure PowerShell バージョン 0.9.8 以降

### キー管理

既存のバックアップを持つ VM のキーをロールオーバーまたは変更する必要がある場合は、バックアップ時に使用したキーを利用できるようにする必要があります。 推奨される方法の 1 つは、キーストアまたは SecureVM システム全体のバックアップを作成することです。

### ドキュメントとリソース

- [展開ガイドの PDF](http://www.cloudlinktech.com/Azure/CL_SecureVM_4_0_DG_EMC_Azure_R2.pdf)
- [展開と使用 SecureVM - ビデオ](https://www.youtube.com/watch?v=8AIRe92UDNg)





