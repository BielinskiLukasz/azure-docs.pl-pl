---
title: Jak używać interfejsu wiersza polecenia do skalowania jednostek zarezerwowanych multimediów (MRUs) — Azure | Microsoft Docs
description: W tym temacie przedstawiono sposób skalowania przetwarzania multimediów przy użyciu interfejsu wiersza polecenia Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: bcbe5fe71e5a4d4d39a29d4a6828c104f6891c0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617149"
---
# <a name="how-to-scale-media-reserved-units"></a>Jak skalować jednostki zarezerwowane multimediów

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule przedstawiono sposób skalowania jednostek zarezerwowanych multimediów (MRSs) w celu przyspieszenia kodowania.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./create-account-howto.md).

Zrozumienie [jednostek zarezerwowanych multimediów](concept-media-reserved-units.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Skalowanie jednostek zarezerwowanych multimediów przy użyciu interfejsu wiersza polecenia

Uruchom polecenie `mru`.

Następujące polecenie [AZ AMS Account MRU](/cli/azure/ams/account/mru?view=azure-cli-latest) ustawia jednostki zarezerwowane multimediów na koncie "amsaccount" przy użyciu parametrów **Count** i **Type** .

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Rozliczenia

Opłata jest naliczana na podstawie liczby minut, w ciągu których zainicjowano alokację jednostek zarezerwowanych multimediów na Twoim koncie. Zdarza się to niezależnie od tego, czy na Twoim koncie są uruchomione jakieś zadania. Szczegółowe wyjaśnienie można znaleźć w sekcji często zadawane pytania na stronie [cennika Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Następny krok

[Analizowanie wideo](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Zobacz też

* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
