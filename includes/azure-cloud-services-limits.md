---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85838963"
---
| Zasób | Limit |
| --- | --- |
| [Role sieci Web i procesu roboczego na wdrożenie](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Wejściowe punkty końcowe wystąpienia](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) dla wdrożenia |25 |
| [Wejściowe punkty końcowe](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) na wdrożenie |25 |
| [Wewnętrzne punkty końcowe](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) na wdrożenie |25 |
| [Certyfikaty usługi hostowanej](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) na wdrożenie |199 |

<sup>1</sup> Każda usługa w chmurze platformy Azure z rolami sieci Web lub procesu roboczego może mieć dwa wdrożenia — jeden dla środowiska produkcyjnego i jeden do przemieszczania. Ten limit odnosi się do liczby różnych ról, czyli konfiguracji. Ten limit nie odnosi się do liczby wystąpień na rolę, czyli skalowania.

