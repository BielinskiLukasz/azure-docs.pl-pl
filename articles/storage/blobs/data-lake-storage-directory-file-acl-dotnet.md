---
title: Azure Data Lake Storage Gen2 zestawu SDK platformy .NET dla plików & list ACL (wersja zapoznawcza)
description: Biblioteka klienta usługi Azure Storage umożliwia zarządzanie katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 01/09/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 0c279118df3a9205e82f8444b261922c688578da
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969079"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Korzystanie z platformy .NET do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)

W tym artykule przedstawiono sposób korzystania z platformy .NET do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS), oraz do zarządzania nimi. 

> [!IMPORTANT]
> Pakiet NuGet [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) , który jest proponowany w tym artykule, jest obecnie w publicznej wersji zapoznawczej.

[Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [przykładów](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [odwołania do interfejsów API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz artykuł [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, zainstaluj pakiet NuGet [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Aby uzyskać więcej informacji o instalowaniu pakietów NuGet, zobacz [Instalowanie i zarządzanie pakietami w programie Visual Studio przy użyciu Menedżera pakietów NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Następnie Dodaj te instrukcje using na początku pliku kodu.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Połącz z kontem

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) reprezentujące konto magazynu. Najprostszym sposobem na uzyskanie jednego jest użycie klucza konta. 

Ten przykład tworzy wystąpienie [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) przy użyciu klucza konta.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć, wywołując metodę [DataLakeServiceClient. isfilesystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) .

Ten przykład tworzy system plików o nazwie `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując metodę [DataLakeFileSystemClient. CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) .

Ten przykład dodaje katalog o nazwie `my-directory` do systemu plików, a następnie dodaje podkatalog o nazwie `my-subdirectory`. 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Zmiana nazwy lub przeniesienie katalogu

Zmień nazwę lub Przenieś katalog, wywołując metodę [DataLakeDirectoryClient. RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) . Przekaż ścieżkę do żądanego katalogu jako parametr. 

Ten przykład zmienia nazwę podkatalogu na nazwę `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Ten przykład przenosi katalog o nazwie `my-subdirectory-renamed` do podkatalogu katalogu o nazwie `my-directory-2`. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując metodę [DataLakeDirectoryClient. Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) .

Ten przykład usuwa katalog o nazwie `my-directory`.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Zarządzanie listą ACL katalogów

Pobierz listę kontroli dostępu (ACL) katalogu, wywołując metodę [DataLakeDirectoryClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) i ustawiając listę ACL przez wywołanie metody [DataLakeDirectoryClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory`. Ciąg `user::rwx,group::r-x,other::rw-` przyznaje użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, nadaje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania oraz daje wszystkim innym uprawnienie do odczytu i zapisu.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Najpierw Utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) . Przekaż plik, wywołując metodę [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) . Upewnij się, że ukończono przekazywanie, wywołując metodę [DataLakeFileClient. FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) .

Ten przykład przekazuje plik tekstowy do katalogu o nazwie `my-directory`.    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

## <a name="manage-a-file-acl"></a>Zarządzanie listą ACL plików

Pobieranie listy kontroli dostępu (ACL) pliku przez wywołanie metody [DataLakeFileClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) i ustawienie listy ACL przez wywołanie metody [DataLakeFileClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `my-file.txt`. Ciąg `user::rwx,group::r-x,other::rw-` przyznaje użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, nadaje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania oraz daje wszystkim innym uprawnienie do odczytu i zapisu.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Pobierz z katalogu 

Najpierw Utwórz wystąpienie [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) reprezentujące plik, który chcesz pobrać. Użyj metody [DataLakeFileClient. ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) i Przeanalizuj wartość zwracaną, aby uzyskać obiekt [strumienia](https://docs.microsoft.com/dotnet/api/system.io.stream) . Użyj dowolnego interfejsu API przetwarzania plików platformy .NET, aby zaoszczędzić bajty ze strumienia do pliku. 

Ten przykład używa [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) i [FILESTREAM](https://docs.microsoft.com/dotnet/api/system.io.filestream) do zapisywania bajtów do pliku. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetlanie zawartości katalogu przez wywołanie metody [FileSystemClient. GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) , a następnie Wyliczenie przez wyniki.

W tym przykładzie program drukuje nazwy poszczególnych plików znajdujących się w katalogu o nazwie `my-directory`.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejsu API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)

