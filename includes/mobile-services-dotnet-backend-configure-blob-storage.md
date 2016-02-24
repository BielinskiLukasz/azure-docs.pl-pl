
##モバイル サービス プロジェクトでのストレージ クライアントのインストール

SAS を生成して BLOB ストレージにイメージをアップロードできるようにするには、最初に、モバイル サービス プロジェクト用のストレージ クライアント ライブラリをインストールする NuGet パッケージを追加する必要があります。 

1.  **ソリューション エクスプ ローラー** Visual Studio では、モバイル サービス プロジェクトを右クリックして [ **NuGet パッケージの管理**します。

2. 左ペインで選択、 **オンライン** カテゴリで、 **安定しているリリースのみ**, 、検索 **WindowsAzure.Storage**, 、] をクリックして **インストール** 上、 **Azure ストレージ** をパッケージ化し、使用許諾契約に同意します。 

    ![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

    これにより、Azure Storage サービス用のクライアント ライブラリがモバイル サービス プロジェクトに追加されます。

##データ モデルでの TodoItem 定義の更新

TodoItem クラスはデータ オブジェクトを定義し、このクラスにはクライアントで定義したものと同じプロパティを追加する必要があります。

1. Visual Studio 2013 で、モバイル サービス プロジェクトを開きます。DataObjects フォルダーを展開し、TodoItem.cs プロジェクト ファイルを開きます。
    
2. 次の新しいプロパティを追加、 **TodoItem** クラス。

        public string containerName { get; set; }
        public string resourceName { get; set; }
        public string sasQueryString { get; set; }
        public string imageUri { get; set; } 

    これらのプロパティは、SAS の生成とイメージ情報の格納に使用されます。 これらのプロパティの大文字小文字の区別は、JavaScript バックエンド バージョンと一致している必要があります。 

    >[AZURE.NOTE] 既定のデータベース初期化子を使用する場合、Entity Framework は削除し、Code First 定義内のデータ モデルの変更を検出したときに、データベースを再作成します。 このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、Code First Migrations を使用する必要があります。 Azure 内の SQL Database に対して、既定の初期化子を使用することはできません。 詳細については、次を参照してください。 [を使用して Code First Migrations をデータ モデルを更新する方法](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)します。

##共有アクセス署名を生成する TodoItem コント ローラーを更新します。 

既存の **TodoItemController** が更新されるように、 **PostTodoItem** 、新しい TodoItem が挿入されたときに、メソッドが SAS を生成します。 また、次のことも実行できます。 

0. まだストレージ アカウントを作成していない場合は、[ストレージ アカウントを作成する方法] を参照してください。

1.  [Azure クラシック ポータル](https://manage.windowsazure.com/), 、] をクリックして **ストレージ**, ストレージ アカウントをクリックし、クリックして **キーの管理**します。 

2. 書き留めて、 **ストレージ アカウント名** と **アクセス キー**します。
 
3. モバイル サービス] をクリックして、 **構成** ] タブで、下へスクロールして **アプリ設定** を入力し、 **名前** と **値** ストレージ アカウントから取得した次のそれぞれの [ **保存**します。

    + `STORAGE_ACCOUNT_NAME`
    + `STORAGE_ACCOUNT_ACCESS_KEY`

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

    ストレージ アカウントのアクセス キーは暗号化された状態でアプリケーション設定に保存されます。 このキーには、実行時にどのサーバー スクリプトからでもアクセスできます。 詳細については、[アプリ設定] を参照してください。

4. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクトに対応する Web.config ファイルを開き、次の新しいアプリケーション設定を追加します。プレースホルダーは、ストレージ アカウント名、およびポータルに設定したアクセス キーと置き換えてください。

        <add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
        <add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

    モバイル サービスをローカル コンピューター上で実行するときに、モバイル サービスはこれら保存されている設定を使用するため、コードを発行する前にテストすることができます。 Azure 内で実行する場合、モバイル サービスは代わりにポータル内のアプリケーション設定値を使用し、これらのプロジェクト設定を無視します。 

7.  Controllers フォルダーで、TodoItemController.cs ファイルを開くし、次の追加 **を使用して** ディレクティブ。

        using System;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;
  
8.  既存 **PostTodoItem** メソッドを次のコード。

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    この POST メソッドは、挿入のための新しい SAS を生成し (5 分間有効)、生成された SAS の値を返された項目の `sasQueryString` プロパティに割り当てます。 `imageUri` プロパティに新しい BLOB のリソース パスが設定され、クライアント UI でのバインド中にイメージを表示できるようになります。

    >[AZURE.NOTE] このコードは、個々 の BLOB に対して SAS を作成します。 同じ SAS を使用してコンテナーに複数の blob をアップロードする場合は、代わりに呼び出せる、 <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">generateSharedAccessSignature メソッド</a> で次のように、空の blob リソース名。 
    <pre><code>blobService.generateSharedAccessSignature (containerName、'、sharedAccessPolicy);</code></pre>

次に、挿入時に生成される SAS を使用してイメージ アップロード機能を追加する、クイック スタート アプリケーションを更新します。
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[How To Create a Storage Account]: ../articles/storage/storage-create-storage-account.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

