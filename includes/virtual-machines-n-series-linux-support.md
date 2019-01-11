---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a1200494eac2d60083a6608d1af91379d8298070
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201503"
---
## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

### <a name="nvidia-cuda-drivers"></a>Sterowniki NVIDIA CUDA

Sterowniki NVIDIA CUDA dla NC, NCv2, NCv3, ND i NDv2 maszyny wirtualne z serii (opcjonalnie na potrzeby seria NV) są obsługiwane tylko na dystrybucje systemu Linux, wymienione w poniższej tabeli. Informacje o sterownik CUDA są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki CUDA, odwiedź stronę [NVIDIA](https://developer.nvidia.com/cuda-zone) witryny sieci Web. Upewnij się, instalowania lub uaktualnienia do najnowsze sterowniki CUDA dla Twojej dystrybucji. 

> [!TIP]
> Jako alternatywy dla ręcznej instalacji sterownik CUDA na maszynie Wirtualnej systemu Linux, możesz wdrożyć platformy Azure [maszyny wirtualnej do nauki o danych](../articles/machine-learning/data-science-virtual-machine/overview.md) obrazu. Wersje maszyny DSVM dla Ubuntu 16.04 LTS i CentOS 7.4 przed instalacją sterowniki NVIDIA CUDA, CUDA głębokiego neuronowych biblioteki sieciowej i innych narzędzi.

| Dystrybucja | Sterownik |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> Opartych na systemie centOS 7.3, 7.4, 7.5, 7.6, opartych na systemie CentOS 7.4 HPC | NVIDIA CUDA 10.0, gałąź sterownika R410 |

### <a name="nvidia-grid-drivers"></a>Sterowniki NVIDIA GRID

Microsoft dystrybuuje instalatory sterownik technologii NVIDIA GRID NV i maszyny wirtualne z serii NVv2 używany jako wirtualnych stacji roboczych lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych serii NV Azure, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują licencji na program siatki wirtualnej oprogramowania procesora GPU na platformie Azure. Nie trzeba skonfigurować serwer licencji oprogramowania vGPU firmy NVIDIA.

| Dystrybucja | Sterownik |
| --- | -- |
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/>7.4, 7.5, 7.6 w opartych na systemie centOS 7.3 | Procesory GPU NVIDIA GRID 7, gałąź sterownika R410|

> [!WARNING] 
> Instalacja oprogramowania innych firm na produktach systemu Red Hat może wpłynąć na warunki wsparcia systemu Red Hat. Zobacz [artykuł bazy wiedzy systemu Red Hat](https://access.redhat.com/articles/1067).
>
