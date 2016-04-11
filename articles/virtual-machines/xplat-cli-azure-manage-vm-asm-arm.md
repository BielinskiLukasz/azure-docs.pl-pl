<properties
    pageTitle="VM タスクに対応する Azure CLI コマンド | Microsoft Azure"
    description="Azure リソース マネージャー モードと Azure サービス管理モードで Azure VM を作成および管理するための同等の Azure CLI コマンドを紹介します。"
    services="virtual-machines"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="command-line-interface"
    ms.workload="infrastructure-services"
    ms.date="08/28/2015"
    ms.author="danlep"/>


# Mac、Linux、Windows 用 Azure CLI で VM タスクに使用するリソース マネージャーとサービス管理の同等のコマンド
この記事では、Azure サービス管理と Azure リソース マネージャーで Azure VM を作成および管理するための Microsoft Azure コマンド ライン インターフェイス (Azure CLI) の同等のコマンドを紹介します。 この記事は、スクリプトをコマンド モード間で移行するための簡易ガイドとして使用してください。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



* Azure CLI をインストールして、サブスクリプションに接続していない場合、は、次を参照してください。 [Azure CLI をインストール](../xplat-cli-install.md) と [Azure CLI から Azure サブスクリプションへの接続](../xplat-cli-connect.md)します。 リソース マネージャー モードのコマンドを使用する場合は、必ず所定のログイン方法を使用して接続してください。

* をコマンド モードの Azure CLI とスイッチで、リソース マネージャー モードを開始するには、「 [リソース マネージャーで Azure コマンド ライン インターフェイスを使用して](xplat-cli-azure-resource-manager.md)います。 既定では、CLI はサービス管理モードで起動します。 リソース マネージャー モードに変更するには、`azure config mode arm` を実行します。 サービス管理モードに戻るには、`azure config mode asm` を実行します。

* オンライン コマンドのヘルプとオプションについては、「`azure <command> <subcommand> --help`」または「`azure help <command> <subcommand>`」と入力します。

## VM タスク
次の表では、サービス管理とリソース マネージャーで Azure CLI コマンドを使用して実行できる一般的な VM タスクを比較します。 リソース マネージャーの多くのコマンドでは、既存のリソース グループの名前を渡す必要があります。

> [AZURE.NOTE] これらの例では、リソース マネージャーのテンプレート ベースの操作を含めないでください。 詳細については、次を参照してください。 [リソース マネージャーで Azure コマンド ライン インターフェイスを使用して](xplat-cli-azure-resource-manager.md)します。

タスク | サービス管理 | リソース マネージャー
-------------- | ----------- | -------------------------
最も基本的な VM の作成 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(取得、 `image-urn` から、 `azure vm image list` コマンドです)。
Linux VM の作成 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Windows VM の作成 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
VM の一覧表示 | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
VM に関する情報の取得 | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
VM の起動 | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
VM の停止 | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
VM の割り当て解除 | 使用できません。 | `azure vm deallocate [options] <resource-group> <name>`
VM の再起動 | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
VM の削除 | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
VM のキャプチャ | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
ユーザー イメージからの VM の作成 | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
専用ディスクからの VM の作成 | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
VM へのデータ ディスクの追加 | `azure  vm disk attach [options] <vm-name> <disk-image-name>` OR- <br/>  `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
VM からのデータ ディスクの削除 | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
VM への一般的な拡張機能の追加 | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
VM への VM アクセス拡張機能の追加 | 使用できません。 | `azure vm reset-access [options] <resource-group> <name>`
VM への Docker 拡張機能の追加 | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
VM への Chef 拡張機能の追加 | `azure  vm extension get-chef [options] <vm-name>` | 使用できません。
VM 拡張機能の無効化 | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | 使用できません。
VM 拡張機能の削除 | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
VM 拡張機能の一覧表示 | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
VM イメージの一覧表示 | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` OR- <br/> `azure vm image list-publishers [options] <location>` OR- <br/> `azure vm image list-offers [options] <location>` OR- <br/> `azure vm image list-skus [options] <location>`
VM イメージの表示 | `azure vm image show [options]` | 使用できません。
VM リソースの使用量の取得 | 使用できません。 | `azure vm list-usage [options] <location>`
使用可能なすべての VM サイズの取得 | 使用できません。 | `azure vm sizes [options]`


## 次のステップ

* Azure CLI を使用して、リソース マネージャーのリソースを使用する方法の詳細を参照してください。 [リソース マネージャーで Azure コマンド ライン インターフェイスを使用して](xplat-cli-azure-resource-manager.md) と [ベース アクセス制御 Azure コマンド ライン インターフェイスによる](../role-based-access-control-xplat-cli-install.md)します。
* CLI コマンドの他の例を参照してください [Azure サービス管理で Azure コマンド ライン インターフェイスを使用して](../virtual-machines-command-line-tools.md) と。
[Azure リソース マネージャーで Azure CLI を使用して](azure-cli-arm-commands.md)します。


