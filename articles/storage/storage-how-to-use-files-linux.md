<properties
    pageTitle="Linux で Azure File ストレージを使用する方法 | Microsoft Azure"
        description="クラウドにファイル共有を作成して、Azure VM または Linux で実行されているオンプレミスのアプリケーションからマウントします。"
        services="storage"
        documentationCenter="na"
        authors="jasontang501"
        manager="jahogg"
        editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="10/26/2015"
      ms.author="jutang;tamram" />


# Linux で Azure File Storage を使用する方法 

## 概要

Azure File Storageは、標準的な SMB プロトコルを使用してクラウドでのファイル共有を提供します。 File ストレージは現在一般に提供されており、SMB 3.0 と SMB 2.1 の両方をサポートしています。

使用して Azure ファイル共有を作成する、 [Azure ポータル](portal.azure.com), 、Azure Storage の PowerShell コマンドレット、Azure ストレージ クライアント ライブラリ、または Azure Storage REST API です。 また、ファイル共有は SMB 共有であるため、それらには標準の使い慣れたファイル システム API を使用してアクセスできます。

Azure で実行されているアプリケーションでは、Azure の仮想マシンのファイル共有を簡単にマウントできます。 また、File ストレージの最新のリリースでは、SMB 3.0 をサポートしているオンプレミス アプリケーションからファイル共有をマウントできます。

Linux の SMB クライアントでは暗号化はまだサポートされていないため、Linux からファイル共有をマウントするには、クライアントがファイル共有と同じ Azure リージョンに存在する必要があります。 ただし、Linux での暗号化のサポートは、SMB 機能を担当している Linux 開発者によって実装される予定です。 今後の暗号化をサポートする Linux ディストリビューションによって、任意の場所から Azure File 共有をマウントできるようになります。

## ビデオ: Linux で Azure File ストレージを使用する方法

このビデオでは、Linux で Azure のファイル共有を作成して使用する方法について説明しています。

> [AZURE.VIDEO azure-file-storage-with-linux]

## 使用する Linux ディストリビューションの選択 ##

Azure で Linux 仮想マシンを作成するときに、Azure イメージ ギャラリーの SMB 2.1 以降をサポートする Linux イメージを指定できます。 推奨される Linux イメージの一覧を次に示します。

- Ubuntu Server 14.04   
- Ubuntu Server 15.04   
- CentOS 7.1    
- Open SUSE 13.2    
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (Premium Image)

## ファイル共有をマウントする ##

使用するディストリビューションに組み込みのクライアントがない場合に Linux を実行する仮想マシンからファイル共有をマウントするには、SMB/CIFS クライアントをインストールする必要があります。 次に示すのは、1 つの選択肢である cifs-utils をインストールするための Ubuntu のコマンドです。

    sudo apt-get install cifs-utils

次に、マウント ポイント (mkdir mymountpoint) を作成した後、次のような mount コマンドを発行する必要があります。

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

共有は、/etc/fstab に設定を追加することでマウントすることもできます。

ここでの 0777 は、すべてのユーザーに実行/読み取り/書き込みアクセス許可を与えるディレクトリ/ファイルのアクセス許可を表します。 Linux ファイルのアクセス許可に関するドキュメントに従って、他のファイル アクセス許可コードに置き換えることができます。
 
再起動後にマウントされているファイル共有を保持するために、次のような設定を /etc/fstab に追加できます。

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

たとえば、Linux image Ubuntu Server 15.04 (Azure イメージ ギャラリーから入手可能) を使用して Azure VM を作成した場合、次のようにファイルをマウントできます。

    azureuser@azureconubuntu:~$ sudo apt-get install apt-file
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

CentOS 7.1 を使用する場合は、次に示すようにファイルをマウントできます。

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

Open SUSE 13.2 を使用する場合は、次に示すようにファイルをマウントできます。

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

## ファイル共有の管理 ##

 [Azure ポータル](portal.azure.com) Azure File ストレージを管理するためのユーザー インターフェイスを提供します。 Web ブラウザーから、次の操作を実行できます。

- ファイル共有からのファイルのアップロードおよびダウンロード
- 各ファイル共有の実際の使用状況の監視
- ファイル共有のサイズ クォータの調整
- Windows クライアントからファイル共有をマウントするために使用する `net use` コマンドのコピー 

また、Linux から Azure のクロスプラットフォームのコマンドライン インターフェイス (Azure CLI) を使用してファイル共有を管理することもできます。 Azure CLI には、File Storage など、Azure Storage を処理できるオープン ソースのクロスプラットフォーム コマンド セットが用意されています。 豊富なデータ アクセス機能だけでなく、Azure ポータルにあるものと同じ機能の多くを使用できます。 例については、次を参照してください。 [Azure Storage で Azure CLI の使用](storage-azure-cli.md)します。

## File Storage を使用した開発 ##

開発者は、ファイル ストレージを使用したアプリケーションをビルドを使用して、 [Java 用 Azure ストレージ クライアント ライブラリ](https://github.com/azure/azure-storage-java)します。 コード例については、次を参照してください。 [Java からファイル ストレージを使用する方法](storage-java-how-to-use-file-storage.md)します。

使用することも、 [Node.js 用 Azure ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-node) ファイル ストレージに対する開発をします。

## フィードバックと詳細情報 ##

Linux ユーザーからのご意見をお待ちしています。 

Azure File storage for Linux ユーザーのグループによって、File ストレージを Linux で評価および使用するときにフィードバックを共有できるフォーラムが提供されています。 電子メール [Azure ファイル ストレージの Linux ユーザー](mailto:azurefileslinuxusers@microsoft.com) ユーザーのグループに参加します。

## 次のステップ

Azure File Storage の詳細については、次のリンクを参照してください。

### 概念に関する記事とビデオ

- [Azure File Storage: Windows および Linux 用の円滑なクラウド SMB ファイル システム](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Windows で Azure File Storage を使用する方法](storage-dotnet-how-to-use-files.md)
- [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy)

### File Storage 用のツールのサポート

- [Microsoft Azure Storage で AzCopy を使用する方法](storage-use-azcopy.md)
- [Azure Storage での Azure CLI の使用](storage-azure-cli.md#create-and-manage-file-shares)

### リファレンス

- [File サービスの REST API リファレンス](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### ブログ記事

- [Azure File Storage の一般提供開始](http://go.microsoft.com/fwlink/?LinkID=626728&clcid=0x409)
- [Azure File ストレージの詳細情報](http://go.microsoft.com/fwlink/?LinkID=626729&clcid=0x409) 
- [Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Persisting connections to Microsoft Azure Files (Microsoft Azure Files への接続の維持)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

