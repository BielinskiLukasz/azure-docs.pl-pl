---
title: Zarządzanie właściwościami i metadanymi dla obiektu BLOB za pomocą platformy .NET i usługi Azure Storage
description: Informacje dotyczące ustawiania i pobierania właściwości systemu oraz przechowywania niestandardowych metadanych w obiektach Blob na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 395210c582ba7f5e8170a96a46e2816336e33b2d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624032"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Zarządzanie właściwościami i metadanymi obiektów BLOB przy użyciu platformy .NET

Poza danymi, które zawierają, obiekty blob obsługują właściwości systemu i metadane zdefiniowane przez użytkownika. W tym artykule pokazano, jak zarządzać właściwościami systemu i metadanymi zdefiniowanymi przez użytkownika za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Informacje o właściwościach i metadanych

- **Właściwości systemu**: właściwości systemu istnieją w każdym zasobie magazynu obiektów BLOB. Niektóre z nich mogą być odczytywane lub ustawiane, a inne są tylko do odczytu. W obszarze okładek niektóre właściwości systemu odpowiadają określonym standardowym nagłówkom HTTP. Biblioteka klienta usługi Azure Storage dla platformy .NET zachowuje te właściwości.

- **Metadane zdefiniowane przez użytkownika**: metadane zdefiniowane przez użytkownika składają się z co najmniej jednej pary nazwa-wartość określonej dla zasobu usługi BLOB Storage. Możesz użyć metadanych do przechowywania dodatkowych wartości z zasobem. Wartości metadanych są tylko do celów własnych i nie mają wpływu na sposób zachowania zasobu.

Pobieranie metadanych i wartości właściwości dla zasobu usługi BLOB Storage jest procesem dwuetapowym. Aby można było odczytać te wartości, należy je jawnie pobrać, wywołując metodę `FetchAttributes` lub `FetchAttributesAsync`. Wyjątkiem od tej reguły jest to, że metody `Exists` i `ExistsAsync` wywołują odpowiednie metody `FetchAttributes` w ramach okładek. Po wywołaniu jednej z tych metod nie trzeba również wywoływać `FetchAttributes`.

> [!IMPORTANT]
> Jeśli okaże się, że wartości właściwości lub metadanych dla zasobu magazynu nie zostały wypełnione, sprawdź, czy kod wywołuje metodę `FetchAttributes` lub `FetchAttributesAsync`.

## <a name="set-and-retrieve-properties"></a>Ustawianie i pobieranie właściwości

Poniższy przykład kodu ustawia `ContentType` i `ContentLanguage` właściwości systemu w obiekcie blob.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
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

Aby uzyskać właściwości obiektu BLOB, wywołaj metodę `FetchAttributes` lub `FetchAttributesAsync` w obiekcie blob, aby wypełnić Właściwość `Properties`. Poniższy przykład kodu pobiera właściwości systemu obiektu BLOB i wyświetla niektóre wartości:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
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

Można określić metadane jako jedną lub więcej par nazwa-wartość w obiekcie blob lub zasobie kontenera. Aby ustawić metadane, Dodaj pary nazwa-wartość do kolekcji `Metadata` w zasobie. Następnie należy wywołać jedną z następujących metod, aby zapisać wartości:

- [Setmetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Pary nazwa/wartość metadanych są prawidłowymi nagłówkami HTTP i powinny być zgodne ze wszystkimi ograniczeniami dotyczącymi nagłówków HTTP. Nazwa metadanych musi być Prawidłowymi nazwami nagłówka HTTP C# i prawidłowymi identyfikatorami, może zawierać tylko znaki ASCII i powinna być traktowana jako niezależna od wielkości liter. Zakodowane w [formacie base64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) lub wartości metadanych [kodu URL](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) zawierające znaki nienależące do zestawu znaków ASCII.

Nazwa metadanych musi być zgodna z konwencjami nazewnictwa dla C# identyfikatorów. Nazwy metadanych zachowują przypadek używany, gdy zostały utworzone, ale bez uwzględniania wielkości liter podczas ustawiania lub odczytywania. Jeśli co najmniej dwa nagłówki metadanych o tej samej nazwie są przesyłane do zasobu, usługa Azure Blob Storage zwraca kod błędu HTTP 400 (Nieprawidłowe żądanie).

Poniższy przykład kodu ustawia metadane w obiekcie blob. Jedna wartość jest ustawiana za pomocą metody `Add` kolekcji. Druga wartość jest ustawiana za pomocą niejawnej składni klucz/wartość.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
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

Aby pobrać metadane, wywołaj metodę `FetchAttributes` lub `FetchAttributesAsync` do obiektu BLOB lub kontenera, aby wypełnić kolekcję `Metadata`, a następnie przeczytaj wartości, jak pokazano w poniższym przykładzie.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
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

- [Operacja ustawiania właściwości obiektu BLOB](/rest/api/storageservices/set-blob-properties)
- [Operacja pobierania właściwości obiektu BLOB](/rest/api/storageservices/get-blob-properties)
- [Operacja ustawiania metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Operacja pobierania metadanych obiektu BLOB](/rest/api/storageservices/get-blob-metadata)
