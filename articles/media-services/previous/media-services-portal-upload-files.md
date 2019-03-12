---
title: Przekazywanie plików na konto usługi Media Services w witrynie Azure Portal | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki przekazywania plików na konto usługi Media Services w witrynie Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: f0f9e0bd3e80bc0cac5a77d2b641badee44642e2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761535"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Przekazywanie plików na konto usługi Media Services w witrynie Azure Portal 

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 

Za pomocą usługi Azure Media Services można przekazać pliki cyfrowe do elementu zawartości. Element zawartości może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki transkrypcji (oraz metadane dla tych plików). Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

> [!NOTE]
> W usłudze Media Services obowiązuje limit maksymalnego rozmiaru przetwarzanych plików. Aby uzyskać szczegółowe informacje na temat limitów rozmiarów plików, zobacz [Media Services quotas and limitations](media-services-quotas-and-limitations.md) (Limity przydziału i ograniczenia w usłudze Media Services).
>

## <a name="upload-files"></a>Przekazywanie plików
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz kolejno pozycje **Ustawienia** > **Elementy zawartości**. Następnie wybierz przycisk **Przekaż**.
   
    ![Przekazywanie plików](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Zostanie wyświetlone okno **Przekaż element zawartości wideo**.
   
   > [!NOTE]
   > W usłudze Media Services nie ma limitu rozmiaru pliku dla przekazywanych wideo.
 
3. Na swoim komputerze przejdź do wideo, które chcesz przekazać. Wybierz wideo, a następnie wybierz przycisk **OK**.  
   
    Rozpocznie się przekazywanie. Postęp będzie widoczny pod nazwą pliku.  

Po zakończeniu przekazywania nowy element zawartości będzie widoczny w okienku **Elementy zawartości**. 

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [zakodować przekazane elementy zawartości](media-services-portal-encode.md).

* Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania, gdy plik trafi do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz przykład w temacie [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps (Media Services: Integrowanie usługi Azure Media Services z usługami Azure Functions i Logic Apps)](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/).


