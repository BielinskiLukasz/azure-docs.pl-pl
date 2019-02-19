---
title: Utwórz konto usługi Media Services przy użyciu wiersza polecenia platformy Azure — Azure | Dokumentacja firmy Microsoft
description: Wykonaj kroki tego przewodnika Szybki start, aby utworzyć konto usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/15/2019
ms.author: juliako
ms.openlocfilehash: f4ce64599aad2b2eebbef6ca8d81acfca2a7a702
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342511"
---
# <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

Aby rozpocząć, szyfrowanie, kodowanie, analizowanie, zarządzanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto usługi Media Services. Konto usługi Media Services musi być skojarzony z co najmniej jedno konto magazynu.

> [!NOTE]
> Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych kosztów ruch wychodzący opóźnienia i danych.

W tym artykule opisano kroki tworzenia nowego konta usługi Azure Media Services przy użyciu wiersza polecenia platformy Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Ustawianie subskrypcji platformy Azure

W poniższym poleceniu podaj identyfikator subskrypcji platformy Azure, który ma być używany dla konta usługi Media Services. Listę subskrypcji, do których masz dostęp, możesz wyświetlić, przechodząc do obszaru [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Kolejne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

