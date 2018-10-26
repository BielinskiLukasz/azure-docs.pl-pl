---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133570"
---
Za pomocą usługi Azure Resource Manager można zdefiniować parametry dla wartości do użycia podczas wdrażania szablonu. Szablon zawiera `parameters` sekcji, która zawiera wszystkich wartości parametrów. Każda wartość parametru jest używany przez szablon do definiowania zasoby, które mają zostać wdrożone.

> [!NOTE]
> Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Zdefiniuj parametry tylko dla wartości, które różnią się, na podstawie projektu, w której przeprowadzasz wdrożenie lub opartych na środowisku, w której wdrażasz.

Podczas definiowania parametrów:

* Aby określić dozwolone wartości, które użytkownik może podać podczas wdrażania, użyj **allowedValues** pola.

* Aby przypisać wartości domyślne w parametrów podczas wdrażania usługi zostaną podane żadne wartości, należy użyć **defaultValue** pola. 
