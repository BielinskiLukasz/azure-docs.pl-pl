---
title: Korzystanie z platformy .NET do zarządzania właściwościami i metadanymi kontenera obiektów BLOB
titleSuffix: Azure Storage
description: Informacje dotyczące ustawiania i pobierania właściwości systemu oraz przechowywania niestandardowych metadanych w kontenerach obiektów BLOB na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135925"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Zarządzanie właściwościami kontenera i metadanymi przy użyciu platformy .NET

Kontenery obiektów BLOB obsługują właściwości systemu i metadane zdefiniowane przez użytkownika, a także zawarte w nich dane. W tym artykule pokazano, jak zarządzać właściwościami systemu i metadanymi zdefiniowanymi przez użytkownika za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Informacje o właściwościach i metadanych

- **Właściwości systemu**: właściwości systemu istnieją w każdym zasobie magazynu obiektów BLOB. Niektóre z nich mogą być odczytywane lub ustawiane, a inne są tylko do odczytu. W obszarze okładek niektóre właściwości systemu odpowiadają określonym standardowym nagłówkom HTTP. Biblioteka klienta usługi Azure Storage dla platformy .NET zachowuje te właściwości.

- **Metadane zdefiniowane przez użytkownika**: metadane zdefiniowane przez użytkownika składają się z co najmniej jednej pary nazwa-wartość określonej dla zasobu usługi BLOB Storage. Możesz użyć metadanych do przechowywania dodatkowych wartości z zasobem. Wartości metadanych są tylko do celów własnych i nie mają wpływu na sposób zachowania zasobu.

Pobieranie wartości właściwości i metadanych dla zasobu usługi BLOB Storage jest procesem dwuetapowym. Aby można było odczytać te wartości, należy je jawnie pobrać, wywołując metodę **FetchAttributes** lub **FetchAttributesAsync** . Wyjątkiem od tej reguły jest to, że metody **EXISTS** i **ExistsAsync** wywołują odpowiednią metodę **FetchAttributes** w ramach okładek. Po wywołaniu jednej z tych metod nie trzeba również wywoływać **FetchAttributes**.

> [!IMPORTANT]
> Jeśli okaże się, że wartości właściwości lub metadanych dla zasobu magazynu nie zostały wypełnione, sprawdź, czy kod wywołuje metodę **FetchAttributes** lub **FetchAttributesAsync** .

Pary nazwa/wartość metadanych są prawidłowymi nagłówkami HTTP i dlatego powinny być zgodne ze wszystkimi ograniczeniami dotyczącymi nagłówków HTTP. Nazwa metadanych musi być Prawidłowymi nazwami nagłówka HTTP C# i prawidłowymi identyfikatorami, może zawierać tylko znaki ASCII i powinna być traktowana jako niezależna od wielkości liter. Wartości metadanych zawierające znaki nienależące do zestawu znaków ASCII powinny być zakodowane w formacie base64 lub kodowane przy użyciu adresu URL.

## <a name="retrieve-container-properties"></a>Pobierz właściwości kontenera

Aby pobrać właściwości kontenera, wywołaj jedną z następujących metod:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Poniższy przykład kodu pobiera właściwości systemu kontenera i zapisuje niektóre wartości właściwości do okna konsoli:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

## <a name="set-and-retrieve-metadata"></a>Ustawianie i Pobieranie metadanych

Można określić metadane jako jedną lub więcej par nazwa-wartość w obiekcie blob lub zasobie kontenera. Aby ustawić metadane, Dodaj pary nazwa-wartość do kolekcji **metadanych** zasobu, a następnie Wywołaj jedną z następujących metod, aby zapisać wartości:

- [Setmetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Nazwa metadanych musi być zgodna z konwencjami nazewnictwa dla C# identyfikatorów. Nazwy metadanych zachowują przypadek, za pomocą którego zostały utworzone, ale bez uwzględniania wielkości liter podczas ustawiania lub odczytywania. Jeśli co najmniej dwa nagłówki metadanych o tej samej nazwie są przesyłane do zasobu, Magazyn obiektów BLOB zwraca kod błędu HTTP 400 (Nieprawidłowe żądanie).

Poniższy przykład kodu ustawia metadane w kontenerze. Jedna wartość jest ustawiana za pomocą metody **Add** kolekcji. Druga wartość jest ustawiana za pomocą niejawnej składni klucz/wartość. Oba są prawidłowe.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Aby pobrać metadane, wywołaj metodę **FetchAttributes** lub **FetchAttributesAsync** obiektu BLOB lub kontenera w celu wypełnienia kolekcji **metadanych** , a następnie odczytaj wartości, jak pokazano w poniższym przykładzie.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zobacz też

- [Operacja pobrania właściwości kontenera](/rest/api/storageservices/get-container-properties)
- [Operacja ustawiania metadanych kontenera](/rest/api/storageservices/set-container-metadata)
- [Operacja pobierania metadanych kontenera](/rest/api/storageservices/set-container-metadata)
