<properties 
    pageTitle="Linux 仮想マシンでの root 権限の使用 | Microsoft Azure" 
    description="Azure 上の Linux 仮想マシンで root 権限を使用する方法について説明します。" 
    services="virtual-machines" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2015" 
    ms.author="szark"/>


# Azure 上の Linux 仮想マシンでの root 権限の使用

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

既定では、`root` ユーザーは Azure 上の Linux 仮想マシンで無効になっています。 ユーザーは `sudo` コマンドを使用して、昇格された特権でコマンドを実行できます。 ただし、システムがプロビジョニングされている方法によって操作が異なります。

1. **SSH キーとパスワードまたはパスワードのみ** -仮想マシンが証明書でプロビジョニングされました (`.CER` ファイル) または SSH キーだけでなく、パスワード、またはユーザー名とパスワードのみです。 この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力が求められます。

2. **SSH キーのみ** -証明書で、仮想マシンがプロビジョニングされている (`.cer`, 、`.pem`, 、または `.pub` ファイル) または SSH キー、パスワードがないです。  ここで `sudo` **されません** コマンドを実行する前に、ユーザーのパスワードの入力を求める。


## SSH キーとパスワード、またはパスワードのみ

SSH キーまたはパスワード認証を使用して Linux 仮想マシンにログインし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

    # sudo <command>
    [sudo] password for azureuser:

この場合はパスワードの入力が求められます。 パスワードの入力後、`sudo` によってコマンドが `root` 権限で実行されます。

`/etc/sudoers.d/waagent` ファイルを編集して、パスワードなし sudo を有効にすることもできます。

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

この変更によって、"azureuser" ユーザーはパスワードなしで sudo を実行できます。

## SSH キーのみ

SSH キー認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

    # sudo <command>

この場合、ユーザーは **いない** パスワードを求められます。 `<enter>` を押した後、`sudo` によってコマンドが `root` 権限で実行されます。

 

