---
title: Obsługiwane zasoby dla systemu Azure Red Hat OpenShift
description: Dowiedz się, które regiony platformy Azure i rozmiary maszyn wirtualnych są obsługiwane przez Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243668"
---
# <a name="azure-red-hat-openshift-resources"></a>Zasoby usługi Azure Red Hat OpenShift

W tym temacie wymieniono regiony platformy Azure i rozmiary maszyn wirtualnych obsługiwane przez Microsoft Azure usługi Red Hat OpenShift.

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Zobacz temat [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) , aby zapoznać się z bieżącą listą regionów, w których można wdrożyć klastry usługi Azure Red Hat OpenShift.

## <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych

Poniżej przedstawiono obsługiwane rozmiary maszyn wirtualnych, które można określić dla węzłów obliczeniowych w klastrze Red Hat OpenShift platformy Azure.

> [!Important]
> Każda maszyna wirtualna ma inną liczbę dysków, które można dołączać. Może to nie być tak samo jasne jak rozmiar pamięci lub procesora CPU.
> Nie wszystkie rozmiary maszyn wirtualnych są dostępne we wszystkich regionach. Nawet jeśli interfejs API obsługuje określony rozmiar, może wystąpić błąd, jeśli rozmiar nie jest dostępny w określonym regionie.
> Aby uzyskać więcej informacji [, zobacz bieżącą listę obsługiwanych rozmiarów maszyn wirtualnych na region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .

## <a name="compute-node-sizes"></a>Rozmiary węzłów obliczeniowych

Następujące rozmiary węzłów obliczeniowych są obsługiwane przez interfejs API REST usługi Azure Red Hat OpenShift:

|Rozmiar|Procesor wirtualny|Pamięć RAM|
|-|-|-|
|Standardowa D4S v3|4|16 GB|
|Standardowa D8s v3|8|32 GB|
|Standardowa D16s v3|16|64 GB|
|Standardowa D32s v3|32|128 GB|
|-|-|-|
|Standardowa E4s v3|4|32 GB|
|Standardowa E8s v3|8|64 GB|
|Standardowa E16s v3|16|128 GB|
|Standardowa E32s v3|32|256 GB|
|-|-|-|
|Standardowa F8s v2|8|16 GB|
|Standardowa F16s v2|16|32 GB|
|Standardowa F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Rozmiary węzłów głównych

Poniższe rozmiary węzła głównego/infrastruktury są obsługiwane przez interfejs API REST usługi Azure Red Hat OpenShift:

|Rozmiar|Procesor wirtualny|Pamięć RAM|
|-|-|-|
|Standardowa D4S v3|4|16 GB|
|Standardowa D8s v3|8|32 GB|
|Standardowa D16s v3|16|64 GB|
|Standardowa D32s v3|32|128 GB|

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .
