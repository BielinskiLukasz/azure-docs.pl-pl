<properties
    pageTitle="チュートリアル - Azure Batch .NET ライブラリの概要 | Microsoft Azure"
    description="Azure Batch の基本的な概念と、単純なシナリオで Batch サービスを開発する方法について説明します。"
    services="batch"
    documentationCenter=".net"
    authors="yidingzhou"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/23/2015"
    ms.author="yidingz"/>

# .NET 向け Azure Batch ライブラリの概要  

Azure Batch .NET ライブラリで作業を開始するにはまず、サポート ファイルを設定するコンソール アプリケーションと、Azure Batch プールの複数のコンピューティング ノードで実行されるプログラムを作成します。 このチュートリアルで作成されるタスクは、Azure Storage にアップロードされたファイルのテキストを評価して、これらのファイルで最もよく使用される単語を返します。 サンプルは C# および使用で記述され、 [Azure Batch .NET ライブラリ](https://msdn.microsoft.com/library/azure/mt348682.aspx)します。

## 前提条件

- アカウント:

    - **Azure アカウント** -ほんの数分で無料の試用アカウントを作成することができます。 詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)をご覧ください。

    - **Batch アカウント** -を参照してください [を作成して、Azure Batch アカウントを管理](batch-account-create-portal.md)します。

    - **ストレージ アカウント** -を参照してください、 **ストレージ アカウントの作成** の [は Azure ストレージ アカウント](../storage-create-storage-account.md)します。 このチュートリアルではという名前のアカウントでコンテナーを作成する **testcon1**します。

- Visual Studio コンソール アプリケーション プロジェクト:

    1.  Visual Studio を開き、 **ファイル** ] メニューのをクリックして **新規**, 、クリックして **プロジェクト**します。

    2.   **Windows**, [ **Visual c#**, 、] をクリックして **コンソール アプリケーション**, 、プロジェクトに名前を **GettingStarted**, 、ソリューションの名前 **AzureBatch**, 、] をクリックし、 **[ok]**します。

- NuGet アセンブリ:

    1. Visual Studio でプロジェクトを作成した後で、プロジェクトを右クリックして **ソリューション エクスプ ローラー** 選択 **NuGet パッケージの管理**します。 オンラインで検索 **Azure.Batch** ] をクリックし、 **インストール** 、Microsoft Azure Batch のパッケージと依存関係をインストールします。

    2. オンラインで検索 **WindowsAzure.Storage** ] をクリックし、 **インストール** 、Azure Storage のパッケージと依存関係をインストールします。

> [AZURE.TIP] これにより、チュートリアルで説明したコア Batch の概念のいくつか使用する [Azure Batch の機能概要](batch-api-basics.md), 、もののバッチに新しいを読み込んでをお勧めします。

## 手順 1: サポート ファイルを作成してアップロードする

アプリケーションをサポートするには、Azure Storage にコンテナーを作成し、テキスト ファイルを作成し、テキスト ファイルとサポート ファイルをコンテナーにアップロードします。

### ストレージ接続文字列を設定する

1. GettingStarted プロジェクトの App.config ファイルを開き、追加、 *& lt; appSettings & gt;* 要素を *& lt; 構成 & gt;*します。

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <appSettings>
                <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
            </appSettings>
        </configuration>

    次の値を置き換えます。

    - **[アカウント名]** -以前に作成したストレージ アカウントの名前。

    - **[アカウント キー]** -ストレージ アカウントのプライマリ キー。 Azure ポータルの [Storage] ページで、プライマリ キーを確認できます。

2. App.config ファイルを保存します。

Azure のストレージ接続文字列の詳細については、次を参照してください。 [Azure ストレージ接続文字列の構成](../storage/storage-configure-connection-string.md)します。

### ストレージ コンテナーを作成する

1. これらの using ディレクティブを、GettingStarted プロジェクトの Program.cs の上部に追加します。

        using System.Configuration;
        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

2. 追加 *System.Configuration* に **参照** で **ソリューション エクスプ ローラー** GettingStarted プロジェクトの

3. 次のメソッドを Program クラスに追加します。このメソッドは、ストレージ接続文字列を取得し、コンテナーを作成し、アクセス許可を設定します。

        static void CreateStorage()
        {
            // Get the storage connection string
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                ConfigurationManager.AppSettings["StorageConnectionString"]);

            // Create the container
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
            container.CreateIfNotExists();

            // Set permissions on the container
            BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
            containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
            container.SetPermissions(containerPermissions);
            Console.WriteLine("Created the container. Press Enter to continue.");
            Console.ReadLine();
        }

4. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        CreateStorage();

5. Program.cs ファイルを保存します。

    > [AZURE.NOTE] 運用環境では勧めを使用すること、 [共有アクセス署名](https://msdn.microsoft.com/library/azure/ee395415.aspx)します。

Blob ストレージの詳細については、次を参照してください [.NET から Blob ストレージを使用する方法。](../storage/storage-dotnet-how-to-use-blobs.md)

### 処理プログラムを作成する

1.  **ソリューション エクスプ ローラー**, 、という名前の新しいコンソール アプリケーション プロジェクトを作成 **ProcessTaskData**します。

2. Visual Studio でプロジェクトを作成した後で、プロジェクトを右クリックして **ソリューション エクスプ ローラー** 選択 **NuGet パッケージの管理**します。 オンラインで検索 **WindowsAzure.Storage** ] をクリックし、 **インストール** 、Azure Storage のパッケージと依存関係をインストールします。

3. 次の using ディレクティブを Program.cs の上部に追加します。

        using Microsoft.WindowsAzure.Storage.Blob;

4. 次のコードを Main に追加します。このコードは、ファイルのテキストを処理します。

        string blobName = args[0];
        Uri blobUri = new Uri(blobName);
        int numTopN = int.Parse(args[1]);

        CloudBlockBlob blob = new CloudBlockBlob(blobUri);
        string content = blob.DownloadText();
        string[] words = content.Split(' ');
        var topNWords =
          words.
            Where(word => word.Length > 0).
            GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
            OrderByDescending(x => x.Value).
            Take(numTopN).
            ToList();

        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

5. ProcessTaskData プロジェクトを保存してビルドします。

### データ ファイルを作成する

1. GettingStarted プロジェクトの作成という新しいテキスト ファイル **taskdata1.txt**, 、次のテキストをコピー、およびファイルを保存します。

    ビジネス ニーズに柔軟なリソースが必要な場合は、オンデマンドでスケーラブルなコンピューティング インフラストラクチャのプロビジョニングに Azure Virtual Machines を使用することができます。 ギャラリーから、Windows、Linux、エンタープライズ アプリケーション (SharePoint や SQL Server など) を実行する仮想マシンを作成することができます。 または、独自のイメージをキャプチャして使用し、カスタマイズされた仮想マシンを作成することができます。

2. という新しいテキスト ファイルを作成する **taskdata2.txt**, 、次のテキストをコピー、およびファイルを保存します。

    Azure Cloud Services を使用して、迅速なデプロイと強力なアプリケーション管理を実現します。 アプリケーションをアップロードするだけで、Azure は継続的な可用性のために、プロビジョニングと負荷分散から稼動状況の監視まで、デプロイの詳細を処理します。 アプリケーションは、業界最高レベルの 99.95% の月間 SLA で支えられています。 インフラストラクチャでなく、アプリケーションに焦点を当てるだけです。

3. という新しいテキスト ファイルを作成する **taskdata3.txt**, 、次のテキストをコピー、およびファイルを保存します。

    Azure の Web サイトでは、Web アプリケーションをホストするための、スケーラブルで信頼性が高く、使いやすい環境を提供します。 幅広いフレームワークおよびテンプレートからの選択により、わずか数秒で Web サイトを作成できます。 ツールや OS を使用して、.NET、PHP、Node.js、Python でサイトを開発します。 TFS、GitHub、BitBucket を含むさまざまなソース管理オプションから選択して、継続的インテグレーションの設定とチームとしての開発を実現できます。 ストレージ、CDN、SQL Database など、追加の Azure マネージ サービスを活用して、サイトの機能を段階的に拡張できます。

### Storage コンテナーにファイルをアップロードする

1. Program.cs ファイルを開き、 **GettingStarted** プロジェクト、およびファイルをアップロードする、このメソッドを追加します。

        static void CreateFiles()
        {
          CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            ConfigurationManager.AppSettings["StorageConnectionString"]);
          CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
          CloudBlobContainer container = blobClient.GetContainerReference("testcon1");

          CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
          CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
          CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
          taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
          taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
        taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);

            CloudBlockBlob storageassembly = container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
            storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);

            CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
          dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);

          Console.WriteLine("Uploaded the files. Press Enter to continue.");
          Console.ReadLine();
        }

2. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        CreateFiles();

3. Program.cs ファイルを保存します。

## 手順 2: Batch アカウントにプールを追加する

コンピューティング ノードのプールは、タスクを実行するときに最初に作成する必要があるリソースのセットです。  

1.  これらの using ディレクティブを、GettingStarted プロジェクトの Program.cs の上部に追加します。

            using Microsoft.Azure.Batch;
            using Microsoft.Azure.Batch.Auth;
            using Microsoft.Azure.Batch.Common;

2. 次のコードを Main に追加します。このコードは、Azure Batch サービスを呼び出す際に資格情報を設定します。

            BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("[account-url]", "[account-name]", "[account-key]");
            BatchClient client = BatchClient.Open(cred);

    かっこで囲まれた値を置き換えてそれぞれ参照して、Batch アカウントに関連付けられた、 [Azure ポータル](https://portal.azure.com)します。 これらの値を見つけるには、にログイン、 [Azure ポータル](https://portal.azure.com) とします。

    - **[アカウント名]** -[ **Batch アカウント**, 、先ほど作成した Batch アカウントを選択
    - **[アカウントの url]** -Batch アカウント] ブレード内をクリックして **プロパティ** > **URL**
    - **[アカウント キー]** -Batch アカウント] ブレード内をクリックして **プロパティ** > **キー** > **プライマリ アクセス キー**

3.  次のメソッドを Program クラスに追加します。このメソッドは、プールを作成します。

            static void CreatePool(BatchClient client)
            {
              CloudPool newPool = client.PoolOperations.CreatePool(
                "testpool1",
                "3",
                "small",
                3);
              newPool.Commit();
              Console.WriteLine("Created the pool. Press Enter to continue.");
              Console.ReadLine();
            }

4. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        CreatePool(client);

5. 次のメソッドを Program クラスに追加します。このメソッドは、アカウント内のプールを一覧します。 これにより、プールが作成されたことを確認できます。

            static void ListPools(BatchClient client)
            {
                IPagedEnumerable<CloudPool> pools = client.PoolOperations.ListPools();
                foreach (CloudPool pool in pools)
                {
                    Console.WriteLine("Pool name: " + pool.Id);
                    Console.WriteLine("   Pool status: " + pool.State);
                }
                Console.WriteLine("Press enter to continue.");
                Console.ReadLine();
            }

6. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        ListPools(client);

7. Program.cs ファイルを保存します。

## 手順 3: アカウントにジョブを追加する

プールで実行するタスクの管理に使用するジョブを作成します。 すべてのタスクをジョブに関連付ける必要があります。

1. 次のメソッドを Program クラスに追加します。このメソッドは、ジョブを作成します。

        static CloudJob CreateJob (BatchClient client)
        {
            CloudJob newJob = client.JobOperations.CreateJob();
            newJob.Id = "testjob1";
            newJob.PoolInformation = new PoolInformation() { PoolId = "testpool1" };
            newJob.Commit();
            Console.WriteLine("Created the job. Press Enter to continue.");
            Console.ReadLine();

            return newJob;
        }

2. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        CreateJob(client);

3. 次のメソッドを Program クラスに追加します。このメソッドは、アカウント内のジョブを一覧します。 これにより、ジョブが作成されたことを確認できます。

        static void ListJobs (BatchClient client)
        {
            IPagedEnumerable<CloudJob> jobs = client.JobOperations.ListJobs();
            foreach (CloudJob job in jobs)
            {
                Console.WriteLine("Job id: " + job.Id);
                Console.WriteLine("   Job status: " + job.State);
            }
            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }

4. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        ListJobs(client);

5. Program.cs ファイルを保存します。

## 手順 4: ジョブにタスクを追加する

ジョブが作成されたら、タスクを追加できます。 各タスクはコンピューティング ノードで実行され、テキスト ファイルを処理します。 このチュートリアルでは、ジョブに 3 つのタスクを追加します。

1. 次のメソッドを Program クラスに追加します。このメソッドは、ジョブに 3 つのタスクを追加します。

        static void AddTasks(BatchClient client)
        {
            CloudJob job = client.JobOperations.GetJob("testjob1");
            ResourceFile programFile = new ResourceFile(
                "https://[account-name].blob.core.windows.net/testcon1/ProcessTaskData.exe",
                "ProcessTaskData.exe");
          ResourceFile assemblyFile = new ResourceFile(
                "https://[account-name].blob.core.windows.net/testcon1/Microsoft.WindowsAzure.Storage.dll",
                "Microsoft.WindowsAzure.Storage.dll");
            for (int i = 1; i < 4; ++i)
            {
                string blobName = "taskdata" + i;
                string taskName = "mytask" + i;
                ResourceFile taskData = new ResourceFile("https://[account-name].blob.core.windows.net/testcon1/" +
                  blobName, blobName);
                CloudTask task = new CloudTask(taskName, "ProcessTaskData.exe https://[account-name].blob.core.windows.net/testcon1/" +
                  blobName + " 3");
                List<ResourceFile> taskFiles = new List<ResourceFile>();
                taskFiles.Add(taskData);
                taskFiles.Add(programFile);
                taskFiles.Add(assemblyFile);
                task.ResourceFiles = taskFiles;
                job.AddTask(task);
                job.Commit();
                job.Refresh();
            }

            client.Utilities.CreateTaskStateMonitor().WaitAll(job.ListTasks(),
        TaskState.Completed, new TimeSpan(0, 30, 0));
            Console.WriteLine("The tasks completed successfully.");
            foreach (CloudTask task in job.ListTasks())
            {
                Console.WriteLine("Task " + task.Id + " says:\n" + task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
            }
            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }


    **[アカウント名]** 以前に作成したストレージ アカウントの名前に置き換える必要があります。 前の例では、4 つのすべてのインスタンスを更新 **[アカウント名]**します。


2. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

            AddTasks(client);

3. 次のメソッドを Program クラスに追加します。このメソッドは、ジョブに関連付けられているタスクを一覧します。

        static void ListTasks(BatchClient client)
        {
            IPagedEnumerable<CloudTask> tasks = client.JobOperations.ListTasks("testjob1");
            foreach (CloudTask task in tasks)
            {
                Console.WriteLine("Task id: " + task.Id);
                Console.WriteLine("   Task status: " + task.State);
              Console.WriteLine("   Task start: " + task.ExecutionInformation.StartTime);
            }
            Console.ReadLine();
        }

4. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        ListTasks(client);

5. Program.cs ファイルを保存します。

## 手順 5: リソースを削除する

Azure のリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。

### タスクを削除する

1.  次のメソッドを Program クラスに追加します。このメソッドは、タスクを削除します。

            static void DeleteTasks(BatchClient client)
            {
                CloudJob job = client.JobOperations.GetJob("testjob1");
                foreach (CloudTask task in job.ListTasks())
                {
                    task.Delete();
                }
                Console.WriteLine("All tasks deleted.");
                Console.ReadLine();
            }

2. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        DeleteTasks(client);

3. Program.cs ファイルを保存します。

### ジョブを削除する

1.  次のメソッドを Program クラスに追加します。このメソッドは、ジョブを削除します。

            static void DeleteJob(BatchClient client)
            {
                client.JobOperations.DeleteJob("testjob1");
                Console.WriteLine("Job was deleted.");
                Console.ReadLine();
            }

2. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        DeleteJob(client);

3. Program.cs ファイルを保存します。

### プールの削除

1. 次のメソッドを Program クラスに追加します。このメソッドは、プールを削除します。

        static void DeletePool (BatchClient client)
        {
            client.PoolOperations.DeletePool("testpool1");
            Console.WriteLine("Pool was deleted.");
            Console.ReadLine();
        }

2. 次のコードを Main に追加します。このコードは、先ほど追加したメソッドを呼び出します。

        DeletePool(client);

3. Program.cs ファイルを保存します。

## 手順 6: アプリケーションを実行する

1. GettingStarted プロジェクトを開始し、コンテナーを作成すると、コンソール ウィンドウに次のように表示されます。

        Created the container. Press Enter to continue.

2. Enter キーを押すと、ファイルが作成され、アップロードされます。ウィンドウに新しい行が表示されます。

        Uploaded the files. Press Enter to continue.

3. Enter キーを押すと、プールが作成されます。

        Created the pool. Press Enter to continue.

4. Enter キーを押すと、新しいプールの一覧が表示されます。

        Pool name: testpool1
            Pool status: Active
        Press Enter to continue.

5. Enter キーを押すと、ジョブが作成されます。

        Created the job. Press Enter to continue.

6. Enter キーを押すと、新しいジョブの一覧が表示されます。

        Job id: testjob1
            Job status: Active
        Press Enter to continue.

7. Enter キーを押すと、タスクがジョブに追加されます。 タスクが追加されると、自動的に実行されます。

        The tasks completed successfully.
        Task mytask1 says:
        can 3
        you 3
        and 3

        Task mytask2 says:
        and 5
        application 3
        the 3

        Task mytask3 says:
        a 5
        and 5
        to 3

        Press Enter to continue.

7. Enter キーを押すと、タスクとその状態の一覧が表示されます。

        Task id: mytask1
            Task status: Completed
            Task start: 7/17/2015 8:31:58 PM
        Task id: mytask2
            Task status: Completed
            Task start: 7/17/2015 8:31:57 PM
        Task id: mytask3
            Task status: Completed
            Task start: 7/17/2015 8:31:57 PM

8. この時点で、Azure ポータルに移動して作成されたリソースを確認できます。 リソースを削除するには、プログラムが終了するまで Enter キーを押します。

## 次のステップ

1. ここまでは、タスクの実行の基本について説明してきました。アプリケーションの需要が変化した場合に、コンピューティング ノードを自動的にスケーリングする方法については、 これを行うには、次を参照してください [Azure Batch プール内の計算ノードを自動的にスケール。](batch-automatic-scaling.md)

2. 一部のアプリケーションは、処理するのに困難な大量のデータを生成します。 これは解決するために 1 つの方法で [効率的なリスト クエリ](batch-efficient-list-queries.md)します。

