---
title: Obsługa programu renderowania Manager — Azure Batch
description: Korzystanie z integracji z programem Azure Batch Render Manager. Poznaj wbudowane wsparcie lub Dodatki dla popularnych menedżerów renderowania.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449682"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Używanie Azure Batch z menedżerami renderowania farmy

W przypadku korzystania z istniejącej lokalnej farmy renderowania jest wysoce prawdopodobnie, że Menedżer renderowania kontroluje wydajność farmy renderowania i zadania renderowania.

Platforma Azure udostępnia wbudowane wsparcie lub Dodatki dla popularnych menedżerów renderowania. Następnie można dodawać i usuwać maszyny wirtualne platformy Azure, w tym maszyny wirtualne z licencjonowaniem aplikacji do użycia i maszynami wirtualnymi o niskim priorytecie.

Obsługiwane są następujące Menedżery renderowania:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Renderowanie wzdania](https://www.royalrender.de/)
* [Thinkbox termin](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

Centrum renderowania platformy Azure upraszcza tworzenie Farm renderowania platformy Azure i zarządzanie nimi.  Centrum renderowania ma natywną obsługę PipelineFx Qube i ostateczny termin 10.  Aby uzyskać więcej informacji i szczegółowe instrukcje, zobacz [repozytorium GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Korzystanie z platformy Azure z usługą PipelineFX Qube

Usługa Azure rendering Hub obsługuje popularne menedżerów renderowania, w tym termin.  Aby uzyskać instrukcje dotyczące wdrażania i używania centrum renderowania, zobacz [repozytorium GitHub](https://github.com/Azure/azure-render-hub).

Skrypty i instrukcje dotyczące włączania maszyn wirtualnych puli Azure Batch, które będą używane jako procesy robocze Qube są również dostępne w [repozytorium GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Korzystanie z platformy Azure z funkcją renderowania do prezentowania

Funkcja renderowania do prezentowania ma wbudowaną integrację z platformą Azure i Azure Batch, co pozwala na rozszerzenie farmy renderowania z maszynami wirtualnymi opartymi na platformie Azure. Aby uzyskać podsumowanie, zobacz [pliki pomocy](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Aby zapoznać się z przykładem, jak korzystać z integracji z platformą Azure, zobacz [artykuł Jellyfish obrazy klientów](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Korzystanie z platformy Azure z terminem Thinkbox

Usługa Azure rendering Hub obsługuje popularne menedżerów renderowania, w tym termin.  Aby uzyskać instrukcje dotyczące wdrażania i używania centrum renderowania, zobacz [repozytorium GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Następne kroki

Wypróbuj Azure Batch integrację z menedżerem programu renderowania przy użyciu odpowiedniej wtyczki i instrukcji w witrynie GitHub, jeśli ma to zastosowanie.
