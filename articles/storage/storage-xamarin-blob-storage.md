<properties 
    pageTitle="Xamarin から BLOB ストレージを使用する方法 (プレビュー) | Microsoft Azure" 
    description="Azure Storage Client Library for Xamarin プレビューを利用すれば、開発者は iOS、Android、Windows Store アプリをネイティブ ユーザー インターフェイスで作成できます。 このチュートリアルでは、Xamarin を利用し、Azure BLOB ストレージを使用する Android アプリケーションを作成する方法を紹介します。" 
    services="storage" 
    documentationCenter="xamarin" 
    authors="micurd" 
    manager="" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="tamram"/>

# Xamarin から BLOB ストレージを使用する方法 (プレビュー)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概要

Xamarin を利用すれば、開発者は共有 C# コードベースを利用して iOS、Android、Windows Store アプリをネイティブ ユーザー インターフェイスで作成できます。 Azure Storage Client Library for Xamarin はプレビュー ライブラリです。将来、変更される可能性があることにご注意ください。

このチュートリアルでは、Xamarin Android アプリケーションで Azure BLOB ストレージを使用する方法を紹介します。 コードに入る前に Azure Storage の詳細については、次を参照してください。 [次のステップ](#next-steps) この文書の最後にします。

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 共有アクセス署名を生成する

Azure Storage Client Library for Xamarin で開発するとき、自分のアカウント アクセス キーで Azure ストレージ アカウントへのアクセスを認証することはできません。 これはあなたのアプリをダウンロードするかもしれないユーザーにあなたのアカウント資格情報が分配されるのを防ぐためです。 代わりに、共有アクセス署名 (SAS) の使用を奨励します。これはあなたのアカウント資格情報を公開しません。

このガイドでは、Azure PowerShell を使用して SAS トークンを生成します。 次に、その生成された SAS を利用する Xamarin アプリを作成します。

まず、Azure PowerShell をインストールする必要があります。 チェック アウト [をインストールして、Azure PowerShell を構成する方法](../powershell-install-configure.md#Install) についてです。

次に、Azure PowerShell を開き、次のコマンドを実行します。 `ACCOUNT_NAME` と `ACCOUNT_KEY== ` を自分のストレージ アカウント資格情報に換えます。 `CONTAINER_NAME` を自分で選択した名前に変えます。

    PS C:\> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
    PS C:\> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
    PS C:\> $now = Get-Date
    PS C:\> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

新しいコンテナーの共有アクセス署名 URI は次のようになります。

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

コンテナーに作成した共有アクセス署名は次の日まで有効です。 署名によって完全なアクセス許可が付与 (*など*, 、読み取り、書き込み、削除、および一覧表示)、コンテナー内の blob にします。

共有アクセス署名に関する詳細については、次を参照してください。、 [.NET の SAS チュートリアル](storage-dotnet-shared-access-signature-part-2.md)します。

## 新しい Xamarin アプリケーションの作成

このチュートリアルでは、Visual Studio で Xamarin アプリケーションを作成します。 次の手順でアプリケーションを作成します。

1. ダウンロードしてインストール [Visual Studio](https://www.visualstudio.com/)します。
2. ダウンロードしてインストール [Xamarin](http://xamarin.com/platform)します。
3. Visual Studio を開き、選択 **ファイル > 新規 > プロジェクト > Android > 空白 App(Android)**します。
4. ソリューション エクスプ ローラーでプロジェクトを右クリックして **NuGet パッケージの管理**します。 検索し、 **Azure Storage** およびインストール **Azure Storage 4.4.0-preview**します。

これでボタンをクリックし、カウンターをインクリメントできるアプリケーションが与えられます。

## 共有アクセス署名を利用し、コンテナー操作を実行します。

次に、SAS URI を利用して一連のコンテナーの操作を実行するコードを追加します。

最初に次のコードを追加 **を使用して** ステートメント。

    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.Storage.Blob;


次に、SAS トークンの行を追加します。 `"SAS_URI"` 文字列を Azure PowerShell で生成した SAS URI で換えます。 下で作成する `UseContainerSAS` メソッドの呼び出しの行を追加します。 注意してください、 **async** キーワードがデリゲートの前に追加されています。


    public class MainActivity : Activity
    {
        int count = 1;
        string sas = "SAS_URI";
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);

            // Get our button from the layout resource, and attach an event to it
            Button button = FindViewById<Button>(Resource.Id.MyButton);

            button.Click += async delegate  {
                button.Text = string.Format("{0} clicks!", count++);
                await UseContainerSAS(sas);
            };
     }

新しいメソッドの `UseContainerSAS` を `OnCreate` メソッドの下に追加します。

    static async Task UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
        string date = DateTime.Now.ToString();
        try
        {
            //Write operation: write a new blob to the container.
            CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new
            MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (Exception e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        try
        {
            //Read operation: Get a reference to one of the blobs in the container and read it.
            CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
            string data = await blob.DownloadTextAsync();

            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine("Blob contents: " + data);
        }
        catch (Exception e)
        {
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine();
        }
        Console.WriteLine();
        try
        {
            //Delete operation: Delete a blob in the container.
            CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
            await blob.DeleteAsync();

            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (Exception e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
    }

## アプリケーションの実行

これでこのアプリケーションをエミュレーターまたは Android デバイスで実行できます。

この入門では Android を取り上げていますが、iOS や Windows Store アプリケーションでも `UseContainerSAS` メソッドを利用できます。 Xamarin を利用すれば、開発者は Windows Phone アプリを作成できます。ただし、このライブラリはそれにまだ対応していません。

## 次のステップ

このチュートリアルでは、Xamarin アプリケーションで Azure BLOB ストレージと SAS を使用する方法を紹介しました。 これ以降の演習として、同様のパターンを応用し、Azure テーブルまたはキューの SAS トークンを生成できます。

BLOB、テーブル、キューの詳細は次のリンクで確認できます。

[Microsoft Azure Storage の概要](storage-introduction.md)  
[How to use Blob Storage from .NET (.NET から BLOB ストレージを使用する方法)](storage-dotnet-how-to-use-blobs.md)  
[How to use Table Storage from .NET (.NET からテーブル ストレージを使用する方法)](storage-dotnet-how-to-use-tables.md)  
[How to use Queue Storage from .NET (.NET からキュー ストレージを使用する方法)](storage-dotnet-how-to-use-queues.md)
[AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy) 

