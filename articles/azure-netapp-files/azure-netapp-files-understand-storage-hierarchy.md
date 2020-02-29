---
title: Co to jest hierarchia magazynu Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano hierarchię magazynu usługi Azure NetApp Files, obejmującą konta, pule pojemności i woluminy.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/27/2020
ms.author: b-juche
ms.openlocfilehash: 70d3a2a501952a5e20b1ff8e99f48f4d7aefce8d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163968"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Co to jest hierarchia magazynu Azure NetApp Files

Przed utworzeniem woluminu w usłudze Azure NetApp Files należy kupić i skonfigurować pulę aprowizowanej pojemności.  Do skonfigurowania puli pojemności potrzebne jest konto usługi NetApp. Zrozumienie hierarchii magazynu pomaga w konfigurowaniu zasobów usługi Azure NetApp Files i zarządzaniu nimi.

> [!IMPORTANT] 
> Azure NetApp Files obecnie nie obsługuje migracji zasobów między subskrypcjami.

## <a name="azure_netapp_files_account"></a>Konta usługi NetApp

- Konto usługi NetApp grupuje składowe pule pojemności do celów administracyjnych.  
- Konto usługi NetApp różni się od ogólnego konta usługi Azure Storage. 
- Konto usługi NetApp ma zasięg regionalny.   
- W jednym regionie można mieć wiele kont usługi NetApp, ale każde konto usługi NetApp jest powiązane tylko z jednym regionem.

## <a name="capacity_pools"></a>Pule pojemności

- Pule pojemności są mierzone według aprowizowanej pojemności.  
- Pojemność jest aprowizowana zgodnie z liczbą zakupionych stałych jednostek SKU (zawierających na przykład 4 TiB pojemności).
- Pula pojemności może mieć tylko jeden poziom usługi.  
- Każda pula pojemności może należeć tylko do jednego konta usługi NetApp. Można jednak mieć wiele pul pojemności w ramach konta usługi NetApp.  
- Nie można przenosić pul pojemności między kontami usługi NetApp.   
  Na przykład na poniższym [diagramie koncepcyjnym hierarchii magazynu](#conceptual_diagram_of_storage_hierarchy) nie można przenieść puli pojemności 1 z konta usługi NetApp w regionie Wschodnie stany USA na konto usługi NetApp w regionie Zachodnie stany USA 2.  
- Puli pojemności nie można usunąć, dopóki nie zostaną usunięte wszystkie woluminy w puli pojemności.

## <a name="volumes"></a>Woluminy

- Woluminy są mierzone według użycia pojemności logicznej i można je skalować. 
- Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.
- Każdy wolumin należy tylko do jednej puli, ale jedna pula może zawierać wiele woluminów. 
- Nie można przenieść woluminu między pulami pojemności. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Na przykład na [diagramie koncepcyjnym hierarchii magazynu](#conceptual_diagram_of_storage_hierarchy) poniżej nie można przenieść woluminów z puli pojemności 1 do puli pojemności 2.
- Nie można usunąć woluminu, dopóki nie zostaną usunięte wszystkie jego migawki.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Diagram koncepcyjny hierarchii magazynu 
Poniższy przykład przedstawia zależności między subskrypcją platformy Azure, kontami usługi NetApp, pulami pojemności i woluminami.   

![Diagram koncepcyjny hierarchii magazynu](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Następne kroki

- [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Rejestrowanie w usłudze Azure NetApp Files](azure-netapp-files-register.md)
