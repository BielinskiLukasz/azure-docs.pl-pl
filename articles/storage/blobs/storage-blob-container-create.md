---
title: Tworzenie lub usuwanie kontenera obiektów BLOB przy użyciu platformy .NET — Azure Storage
description: Dowiedz się, jak utworzyć lub usunąć kontener obiektów BLOB na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 99773b83f15ea4b386c1078eed510fc233322f0f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985686"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Tworzenie lub usuwanie kontenera w usłudze Azure Storage przy użyciu platformy .NET

Obiekty blob w usłudze Azure Storage są zorganizowane w kontenery. Aby można było przekazać obiekt BLOB, należy najpierw utworzyć kontener. W tym artykule przedstawiono sposób tworzenia i usuwania kontenerów za pomocą [biblioteki klienckiej usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage/client).

## <a name="name-a-container"></a>Nazwij kontener

Nazwa kontenera musi być prawidłową nazwą DNS, ponieważ stanowi część unikatowego identyfikatora URI używanego do adresowania kontenera lub jego obiektów BLOB. Po nadaniu nazwy kontenera postępuj zgodnie z następującymi regułami:

- Nazwy kontenerów mogą mieć długość od 3 do 63 znaków.
- Nazwy kontenerów muszą zaczynać się literą lub cyfrą i mogą zawierać tylko małe litery, cyfry i znak kreski (-).
- W nazwach kontenerów nie są dozwolone dwa lub więcej następujących po sobie znaków kreski.

Identyfikator URI dla kontenera ma następujący format:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, należy wywołać jedną z następujących metod:

- [Tworzenie](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [Metoda async](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [createIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Metody **Create** i **xmlasync** zgłaszają wyjątek, jeśli kontener o tej samej nazwie już istnieje.

Metody **metodę createifnotexists** i **CreateIfNotExistsAsync** zwracają wartość logiczną wskazującą, czy kontener został utworzony. Jeśli kontener o tej samej nazwie już istnieje, te metody zwracają **wartość false** , aby wskazać, że nowy kontener nie został utworzony.

Kontenery są tworzone bezpośrednio pod kontem magazynu. Nie można zagnieżdżać jednego kontenera poniżej innego.

W poniższym przykładzie jest tworzony kontener asynchronicznie:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Tworzenie kontenera głównego

Kontener główny służy jako domyślny kontener dla konta magazynu. Każde konto magazynu może mieć jeden kontener główny, który musi mieć nazwę *$root.* . Należy jawnie utworzyć lub usunąć kontener główny.

Można odwoływać się do obiektu BLOB przechowywanego w kontenerze głównym bez dołączania nazwy kontenera głównego. Kontener główny umożliwia odwoływanie się do obiektu BLOB na najwyższym poziomie w hierarchii kont magazynu. Na przykład można odwołać się do obiektu BLOB znajdującego się w kontenerze głównym w następujący sposób:

`https://myaccount.blob.core.windows.net/default.html`

Poniższy przykład tworzy synchronicznie kontener główny:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Usuwanie kontenera

Aby usunąć kontener w programie .NET, należy użyć jednej z następujących metod:

- [Usuwanie](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Metody **delete** i **DeleteAsync** zgłaszają wyjątek, jeśli kontener nie istnieje.

Metody **DeleteIfNotExists** i **DeleteIfNotExistsAsync** zwracają wartość logiczną wskazującą, czy kontener został usunięty. Jeśli określony kontener nie istnieje, wówczas te metody zwracają **wartość false** , aby wskazać, że kontener nie został usunięty.

Po usunięciu kontenera nie można utworzyć kontenera o tej samej nazwie przez co najmniej 30 sekund i prawdopodobnie dłużej. Podczas usuwania kontenera próba utworzenia kontenera o takiej samej nazwie zakończy się niepowodzeniem z kodem błędu HTTP 409 (konflikt). Wszystkie inne operacje dotyczące kontenera lub obiektów blob, które zawiera, będą kończyć się niepowodzeniem z kodem błędu HTTP 404 (nie znaleziono), gdy kontener jest usuwany.

Poniższy przykład usuwa określony kontener i obsługuje wyjątek, jeśli kontener nie istnieje:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Poniższy przykład pokazuje, jak usunąć wszystkie kontenery, które zaczynają się od określonego prefiksu. W przykładzie przerwano dzierżawę, jeśli istnieje dzierżawa w kontenerze.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zobacz także

- [Operacja tworzenia kontenera](/rest/api/storageservices/create-container)
- [Operacja usuwania kontenera](/rest/api/storageservices/delete-container)
