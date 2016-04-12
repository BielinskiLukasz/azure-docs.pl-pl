<properties 
    pageTitle="PowerShell を使用して Azure Media Services アカウントを管理する" 
    description="PowerShell コマンドレットを使用して Azure Media Services アカウントを管理する方法を説明します。" 
    authors="Juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/08/2015" 
    ms.author="juliako"/>


#PowerShell を使用して Azure Media Services アカウントを管理する

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Azure Media Services アカウントを作成するには、Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure の無料試用版サイト</a>」をご覧ください。

##概要 

この記事では PowerShell コマンドレットを使用して Azure Media Services アカウントを管理する方法を示します。

>[AZURE.NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure の無料試用版サイト</a>」を参照してください。

##Microsoft Azure PowerShell コマンドレットをインストールします。

最新の Azure PowerShell コマンドレットをインストールするを参照してください [をインストールして、Azure PowerShell を構成する方法。](../powershell-install-configure.md)

##Azure サブスクリプションの選択

PowerShell コマンドレットをインストールして構成した後、作業するサブスクリプションを指定する必要があります。 

利用可能なサブスクリプションの一覧を取得するには、次のコマンドレットを実行します。

    PS C:\> Get-AzureSubscription

次に、以下を実行していずれか 1 つを選択します。

    PS C:\> Select-AzureSubscription "TestSubscription"

 
##ストレージ アカウント名の取得

Azure Media Services は、メディア コンテンツの格納に Azure ストレージを使用します。 新しい Media Services アカウントを作成するときに、ストレージ アカウントに関連付ける必要があります。 ストレージ アカウントは、Media Services アカウントに使用するものと同じサブスクリプションに属する必要があります。 

この例では、既存のストレージ アカウントが使用されます。  [Get-azurestorageaccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) コマンドレットは、現在のサブスクリプションのストレージ アカウントを取得します。 メディア アカウントに関連付けるストレージ アカウントの名前 (StorageAccountName) を取得します。

    StorageAccountDescription : 
    AffinityGroup             :
    Location                  : East US
    GeoReplicationEnabled     : True
    GeoPrimaryLocation        : East US
    GeoSecondaryLocation      : West US
    Label                     : storagetest001
    StorageAccountStatus      : Created
    StatusOfPrimary           : Available
    StatusOfSecondary         : Available
    Endpoints                 : {https://storagetest001.blob.core.windows.net/,
                                https://storagetest001.queue.core.windows.net/,
                                https://storagetest001.table.core.windows.net/}
    AccountType               : Standard_GRS
    StorageAccountName        : storatetest001
    OperationDescription      : Get-AzureStorageAccount
    OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
    OperationStatus           : Succeeded

##Media Services アカウントの作成

新しい Azure Media Services アカウントの作成、 [New-azuremediaservicesaccount](https://msdn.microsoft.com/library/azure/dn495286.aspx) コマンドレットの Media Services アカウント名、場所作成は、データ センターの場所、ストレージ アカウント名を提供します。 


    PS C:\> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

##Media Services アカウントの取得

1 つ以上の Media Services アカウントを使用して情報を一覧表示を作成すると [Get-azuremediaservicesaccount](https://msdn.microsoft.com/library/azure/dn495286.aspx)

    
    PS C:\> Get-AzureMediaServicesAccount
    
    AccountId       Name                State
    ---------       ----                 -----
    xxxxxxxxxx      amstestaccount001   Active

Name パラメーターを提供することによってアカウント キーなどのより詳細な情報が表示されます。

    PS C:\> Get-AzureMediaServicesAccount -Name amstestaccount001

##Media Services アクセス キーの再生成

Media Services のプライマリまたはセカンダリ アクセス キーを更新する場合を使用して [New-azuremediaserviceskey](https://msdn.microsoft.com/library/azure/dn495215.aspx)します。 
アカウント名を指定して再生成するキー (プライマリまたはセカンダリ) を指定する必要があります。 

PowerShell で確認の質問を表示させないようにするには、-Force スイッチを指定します。

    PS C:\> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

##Media Services アカウントの削除

Azure Media アカウントを削除する準備ができたらを使用して [Remove-azuremediaservicesaccount](https://msdn.microsoft.com/library/azure/dn495220.aspx)します。

    PS C:\> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
