---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: b59b45de04ebb717dfe55eb17c9dbd92f7523976
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120887"
---
[!INCLUDE [resource group intro text](resource-group.md)]

W usłudze Cloud Shell utwórz grupę zasobów za pomocą polecenia [`az group create`](/cli/azure/group?view=azure-cli-latest). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *West Europe* (Europa Zachodnia). Aby wyświetlić wszystkie obsługiwane lokalizacje dla usługi App Service w warstwie **Bezpłatna**, uruchom polecenie [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie. 

Po zakończeniu działania polecenia zostaną wyświetlone dane wyjściowe JSON z właściwościami grupy zasobów.