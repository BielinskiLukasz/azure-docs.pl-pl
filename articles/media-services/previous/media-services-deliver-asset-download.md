---
title: Pobieranie Media Services zasobów na komputer — Azure | Microsoft Docs
description: Dowiedz się więcej na temat pobierania zasobów do komputera. Przykłady kodu są zapisywane w języku C# i używają zestawu Media Services SDK dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 21fcc6ae09718ffbb22e1d438926586dd3cde71d
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "61465664"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Instrukcje: dostarczanie elementu zawartości przez pobranie  
W tym artykule omówiono opcje dostarczania zawartości multimedialnej przekazanej do Media Services. Zawartość Media Services można dostarczyć w wielu scenariuszach aplikacji. Po kodowaniu Pobierz wygenerowane zasoby multimedialne lub uzyskaj do nich dostęp przy użyciu lokalizatora przesyłania strumieniowego. Aby zwiększyć wydajność i skalowalność, można również dostarczyć zawartość przy użyciu Content Delivery Network (CDN).

Ten przykład przedstawia sposób pobierania zasobów multimedialnych z Media Services na komputer lokalny. Kod wysyła zapytania do zadań skojarzonych z kontem Media Services według identyfikatora zadania i uzyskuje dostęp do swojej kolekcji **OutputMediaAssets** (czyli zestawu co najmniej jednego wyjściowego zasobu multimedialnego, który wynika z uruchamiania zadania). Ten przykład pokazuje, jak pobrać wyjściowe zasoby multimedialne z zadania, ale można zastosować takie samo podejście do pobierania innych zasobów.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni/uprawnień dostępu, na przykład zasad dla lokalizatorów, które mają być nadal wykonywane przez długi czas (zasady bez przekazywania). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Dostarczanie zawartości przesyłania strumieniowego](media-services-deliver-streaming-content.md)

