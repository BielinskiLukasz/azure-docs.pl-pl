##<a name="add-select-images"></a>アプリケーションの更新イメージをキャプチャおよびアップロードするクイック スタート クライアント アプリケーションの更新

1. Visual Studio で Package.appxmanifest ファイルを開きし、[、 **機能** ] タブの有効化、 **web カメラ** と **マイク** 機能します。

    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
    これにより、コンピューターに接続されたカメラをアプリケーションで使用できます。 ユーザーは、アプリケーションを最初に実行したときに、カメラのアクセスを許可することを求められます。

1. MainPage.xaml ファイルを開き、置換、 **StackPanel** 要素の 1 つ目の直後後 **タスク** 要素を次のコード。

        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                    Click="OnTakePhotoClick" />
            <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                    Click="ButtonSave_Click"/>
        </StackPanel>

2. 置き換える、 **StackPanel** 内の要素、 **DataTemplate** を次のコード。

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

    これにイメージが追加、 **ItemTemplate** され、Blob ストレージ サービスにアップロードされたイメージの URI として、バインディング ソースを設定します。

3. MainPage.xaml.cs プロジェクト ファイルを開き、次の追加 **を使用して** ステートメント。
    
        using Windows.Media.Capture;
        using Windows.Storage;
        using Windows.UI.Popups;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;
    
4. TodoItem クラスで、次のプロパティを追加します。

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
        
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
        
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
        
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

    >[AZURE.NOTE]JavaScript バックエンド モバイル サービスの TodoItem オブジェクトには、新しいプロパティを追加するには、動的スキーマが、モバイル サービスで有効になっているが必要です。 動的スキーマが有効になっていると、TodoItem テーブルに、これらの新しいプロパティに対応する新しい列が自動的に追加されます。 .NET バックエンド モバイル サービスのトピックを参照してください。 [.NET バックエンド モバイル サービスにデータ モデルの変更を加える方法](../articles/mobile-services/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)します。

5. MainPage クラスで、次のコードを追加します。

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
        
        private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
        {
            // Capture a new photo or video from the device.
            CameraCaptureUI cameraCapture = new CameraCaptureUI();
            media = await cameraCapture
                .CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
        }

    このコードにより、イメージをキャプチャしてイメージをストレージ ファイルに保存する、カメラ UI が表示されます。

6. 既存の `InsertTodoItem` メソッドを次のコードに置き換えます。
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;
            
            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = media.Name;
            }
            
            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);
            
            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);
                
                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);

                // Get the new image as a stream.
                using (var fileStream = await media.OpenStreamForReadAsync())
                {                                       
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                }
                
                // When you request an SAS at the container-level instead of the blob-level,
                // you are able to upload multiple streams using the same container credentials.
            }
            
            // Add the new item to the collection.
            items.Add(todoItem);
        }

    このコードは、新しい TodoItem を挿入する要求をモバイル サービスに送信します (イメージ ファイル名を含む)。 応答には、SAS (その後、BLOB ストア内のイメージの挿入に使用される) と、データ バインド用のイメージの URI が含まれます。

最後の手順では、アプリケーションをテストし、アップロードが成功するかどうかを検証します。
        
##<a name="test"></a>アプリケーションのイメージのアップロードをテストする

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. [テキスト ボックスにテキストを入力 **Insert a TodoItem**, 、] をクリックし、 **フォト**します。

    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

    これにより、カメラ キャプチャ UI が表示されます。 

3. クリックし、写真を撮ってイメージ] をクリックして **OK**します。
  
    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

4. クリックして **アップロード** 新しい項目を挿入し、イメージをアップロードします。

    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

5. 新しい項目がアップロード イメージと共にリスト ビューに表示されます。

    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

    >[AZURE.NOTE]イメージは Blob ストレージから自動的にダウンロード サービス、 <code>imageUri</code> 新しい項目のプロパティにバインドする、 <strong>イメージ</strong> コントロールです。


