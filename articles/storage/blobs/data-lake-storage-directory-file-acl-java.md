---
title: Używanie języka Java dla plików & list ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)
description: Biblioteki usługi Azure Storage dla języka Java umożliwiają zarządzanie katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 0a57e87cc408d111893cbb2beaf4fc5afee2eca2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930785"
---
# <a name="use-java-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Używanie języka Java dla plików & list ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)

W tym artykule pokazano, jak używać języka Java do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS) i zarządzać nimi. 

> [!IMPORTANT]
> Biblioteka Java, która jest proponowana w tym artykule, jest obecnie dostępna w publicznej wersji zapoznawczej.

[Pakiet (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake/12.0.0-preview.6/jar) | [przykładów](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html) | [Gen1 do mapowania Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [przekazać opinię](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz artykuł [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, Otwórz [Tę stronę](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) i Znajdź najnowszą wersję biblioteki języka Java. Następnie otwórz plik *pliku pom. XML* w edytorze tekstów. Dodaj element zależności, który odwołuje się do tej wersji.

Następnie Dodaj te instrukcje Imports do pliku kodu.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Połącz z kontem 

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. Najprostszym sposobem na uzyskanie jednego jest użycie klucza konta. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      

```
## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć, wywołując metodę **DataLakeServiceClient. isfilesystem** .

Ten przykład tworzy system plików o nazwie `my-file-system`. 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując metodę **DataLakeFileSystemClient. setdirectory** .

Ten przykład dodaje katalog o nazwie `my-directory` do systemu plików, a następnie dodaje podkatalog o nazwie `my-subdirectory`. 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog, wywołując metodę **DataLakeDirectoryClient. Rename** . Przekaż ścieżkę do żądanego katalogu jako parametr. 

Ten przykład zmienia nazwę podkatalogu na nazwę `my-subdirectory-renamed`.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename("my-directory/my-subdirectory-renamed");
}
```

Ten przykład przenosi katalog o nazwie `my-subdirectory-renamed` do podkatalogu katalogu o nazwie `my-directory-2`. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując metodę **DataLakeDirectoryClient. deleteWithResponse** .

Ten przykład usuwa katalog o nazwie `my-directory`.   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Zarządzanie listą ACL katalogów

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory`. Ten przykład daje właścicielowi uprawnień Odczyt, zapis i wykonywanie, daje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a ponadto daje wszystkim innym osobom dostęp do odczytu.

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    PathPermissions permissions = new PathPermissions()

      .group(new RolePermissions().execute(true).read(true))
      .owner(new RolePermissions().execute(true).read(true).write(true))
      .other(new RolePermissions().read(true));

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Najpierw Utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy **DataLakeFileClient** . Przekaż plik, wywołując metodę **DataLakeFileClient. Append** . Upewnij się, że ukończono przekazywanie, wywołując metodę **DataLakeFileClient. FlushAsync** .

Ten przykład przekazuje plik tekstowy do katalogu o nazwie `my-directory`.

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

## <a name="manage-a-file-acl"></a>Zarządzanie listą ACL plików

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `upload-file.txt`. Ten przykład daje właścicielowi uprawnień Odczyt, zapis i wykonywanie, daje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a ponadto daje wszystkim innym osobom dostęp do odczytu.

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    PathPermissions permissions = new PathPermissions()

        .group(new RolePermissions().execute(true).read(true))
        .owner(new RolePermissions().execute(true).read(true).write(true))
        .other(new RolePermissions().read(false));

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Pobierz z katalogu

Najpierw Utwórz wystąpienie **DataLakeFileClient** reprezentujące plik, który chcesz pobrać. Użyj metody **DataLakeFileClient. Read** , aby odczytać plik. Użyj dowolnego interfejsu API przetwarzania plików platformy .NET, aby zaoszczędzić bajty ze strumienia do pliku. 

Upewnij się, że ukończono pobieranie, wywołując metodę **DataLakeFileClient. Flush** .

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();

    fileClient.flush(file.length());
        
}

```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

W tym przykładzie program drukuje nazwy poszczególnych plików znajdujących się w katalogu o nazwie `my-directory`.

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>Zobacz także

* [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html)
* [Pakiet (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake/12.0.0-preview.6/jar)
* [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)