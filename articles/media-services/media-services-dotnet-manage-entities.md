
<properties 
    pageTitle="Media Services .NET SDK を使用するアセットと関連エンティティの管理" 
    description="Media Services SDK for .NET を使用してアセットと関連エンティティを管理する方法について説明します。" 
    authors="juliako" 
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
    ms.date="12/05/2015"
    ms.author="juliako"/>


#Media Services .NET SDK を使用するアセットと関連エンティティの管理


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [REST ()](media-services-rest-manage-entities.md)


このトピックでは、次の Media Services 管理タスクを実行する方法を示します。

- アセット参照を取得する 
- ジョブ参照を取得する 
- すべてのアセットを一覧表示する 
- ジョブとアセットを一覧表示する 
- すべてのアクセス ポリシーを一覧表示する 
- すべてのロケーターを一覧表示する 
- アセットを削除する 
- ジョブを削除する 
- アクセス ポリシーを削除する 

##前提条件 

参照してください [環境を設定します。](media-services-set-up-computer.md)

##アセット参照を取得する

頻繁に実行するタスクでは、Media Services の既存のアセットへの参照を取得します。 次のコード例では、アセット ID に基づいて、サーバー コンテキスト オブジェクトで Assets コレクションからアセット参照を取得する方法を示します。
次のコード例では、Linq クエリを使用して、既存の IAsset オブジェクトへの参照を取得します。

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();
    
        return asset;
    }

##ジョブ参照を取得する

Media Services のコードで処理タスクを使用するときは、多くの場合、ID に基づいて既存のジョブへの参照を取得する必要があります。 次のコード例では、Jobs コレクションから IJob オブジェクトへの参照を取得する方法を示します。
警告:
実行時間の長いエンコード ジョブを開始するときに、ジョブの参照を取得し、スレッドでジョブの状態を確認する必要がある必要があります。 このような場合は、メソッドがスレッドから返されるときに、更新されたジョブ参照を取得する必要があります。

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();
    
        return job;
    }

##すべてのアセットを一覧表示する

ストレージに保持しているアセットの数が増えていくと、アセットの一覧表示が役立ちます。 次のコード例では、サーバー コンテキスト オブジェクトで Assets コレクションを反復処理する方法を示します。 またこのコード例では、各アセットについて、そのプロパティ値の一部をコンソールに出力します。 たとえば、各アセットには多数のメディア ファイルが含まれることがあります。 このコード例では、各アセットに関連付けられているすべてのファイルを書き出します。



    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");
    
            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##ジョブとアセットを一覧表示する

重要な関連タスクとして、Media Services のアセットと関連付けられているジョブを一覧表示することがあります。 次のコード例では、各 IJob オブジェクトを一覧表示し、各ジョブについて、ジョブに関するプロパティ、すべての関連するタスク、すべての入力アセット、およびすべての出力アセットを表示する方法を示します。 この例のコードは、他の多数のタスクに役立てることができます。 たとえば、これまでに実行した 1 つまたは複数のエンコード ジョブの出力アセットを一覧表示する場合は、このコードから出力アセットへのアクセス方法が分かります。 出力アセットへの参照が分かっている場合は、コンテンツをダウンロードまたは URL を提供することで、他のユーザーやアプリケーションにコンテンツを配信できます。 

アセットを配信するためのオプションの詳細については、次を参照してください。 [Media Services SDK for .NET によるアセットの配信](media-services-deliver-streaming-content.md)します。

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");
    
    
            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }
    
            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {
    
                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##すべてのアクセス ポリシーを一覧表示する

Media Services では、アセットまたはそのファイルに関するアクセス ポリシーを定義できます。 アクセス ポリシーで、ファイルやアセットに対するアクセス許可 (アクセスの種類や期間) を定義します。 Media Services のコードでアクセス ポリシーを定義するには、通常は IAccessPolicy オブジェクトを作成して既存のアセットに関連付けます。 次に、ILocator オブジェクトを作成します。このオブジェクトにより、Media Services のアセットに直接アクセスできるようになります。 このドキュメント シリーズ付属の Visual Studio プロジェクトには、アクセス ポリシーとロケーターを作成してアセットに割り当てる方法を示すコード例がいくつか含まれています。

次のコード例では、サーバーのすべてのアクセス ポリシーを一覧表示し、それぞれに関連付けられている権限の種類を表示する方法を示します。 アクセス ポリシーを表示するもう 1 つの便利な方法は、サーバーのすべての ILocator オブジェクトを一覧表示することです。各ロケーターについて、関連付けられているアクセス ポリシーを一覧表示するには、AccessPolicy プロパティを使用します。

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");
    
        }
    }

##すべてのロケーターを一覧表示する

ロケーターは、アセットに直接アクセスできる直接パス、およびロケーターの関連付けられているアクセス ポリシーの定義に従ってアセットへのアクセス許可を提供する URL です。 各アセットの Locators プロパティは、関連付けられている ILocator オブジェクトのコレクションを持つことがあります。 サーバー コンテキストも、すべてのロケーターが含まれる Locators コレクションを持ちます。

次のコード例では、サーバーのすべてのロケーターを一覧表示します。 各ロケーターについて、関連するアセットの ID とアクセス ポリシーを表示します。 また、アクセス許可の種類、有効期限、およびアセットの完全パスも表示します。

アセットのロケーター パスがアセットの唯一のベース URL であることに注意してください。 ユーザーまたはアプリケーションが参照することができる個々のファイルへの直接パスを作成するには、コードで、ロケーター パスに個別のファイルのパスを追加する必要があります。 これを行う方法の詳細については、トピックを参照してください。 [Media Services SDK for .NET によるアセットの配信](media-services-deliver-streaming-content.md)します。

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }


##アセットを削除する

次の例では、アセットを削除します。

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();
    
        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");
    
    }

##ジョブを削除する

ジョブを削除するには、State プロパティで指定されているジョブの状態を確認する必要があります。 終了または取り消し済みのジョブは削除できますが、キューに登録済み、スケジュール済み、処理中などの他の状態のジョブは、取り消さないと削除できません。
次のコード例では、ジョブを削除する方法を示します。この例では、ジョブの状態をチェックして、状態が終了または取り消し済みであるときに削除します。 このコードは、このトピックで前述したジョブ参照を取得するためのセクション (「ジョブ参照を取得する」) をベースにしています。

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;
    
        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);
    
            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }
    
        }
    }


##アクセス ポリシーを削除する

次のコード例では、ポリシー ID に基づいてアクセス ポリシーへの参照を取得し、ポリシーを削除する方法を示します。

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();
    
        policy.Delete();
    
    }
    


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

