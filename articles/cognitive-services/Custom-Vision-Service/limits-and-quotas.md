---
title: Limity i przydziały — Custom Vision Service
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano różne typy kluczy licencjonowania oraz limity i przydziały dla Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: 089d5dbb1eece2904f104dfecb3de8adec052dfc
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391744"
---
# <a name="limits-and-quotas"></a>Limity przydziału i ograniczenia

Istnieją dwie warstwy kluczy dla usługi Custom Vision. Możesz zarejestrować się w celu uzyskania subskrypcji F0 (bezpłatna) lub S0 (standardowa) za pomocą Azure Portal. Aby uzyskać szczegółowe informacje o cenach i transakcjach, zobacz [stronę z cennikiem Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) .

Liczba obrazów szkoleniowych na projekt i Tagi dla projektu powinna wzrosnąć w czasie dla projektów S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projekty|2|100|
|Obrazy szkoleniowe na projekt |5000|100 000|
|Przewidywania/miesiąc|10 000 |Nieograniczona liczba|
|Tagi/projekt|50|500|
|Iteracji |10|10|
|Minimalna liczba obrazów oznaczonych etykietą na tag, klasyfikacja (zalecane: 50) |5|5|
|Minimalna liczba obrazów oznaczonych etykietą na tag, wykrywanie obiektów (zalecane: 50 +)|15|15|
|Jak długo przechowywane są obrazy predykcyjne|30 dni|30 dni|
|Operacje [przewidywania](https://go.microsoft.com/fwlink/?linkid=865445) z magazynem (liczba transakcji na sekundę)|2|10|
|Operacje [przewidywania](https://go.microsoft.com/fwlink/?linkid=865445) bez magazynu (transakcje na sekundę)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (wywołania interfejsu API na sekundę)|2|10|
|[Inne wywołania interfejsu API](https://go.microsoft.com/fwlink/?linkid=865446) (liczba transakcji na sekundę)|10|10|
|Akceptowane typy obrazów|jpg, PNG, BMP, GIF|jpg, PNG, BMP, GIF|
|Minimalna wysokość/szerokość obrazu w pikselach|256 (patrz Uwaga)|256 (patrz Uwaga)|
|Maksymalna wysokość/szerokość obrazu (w pikselach)|bez ograniczeń|bez ograniczeń|
|Maksymalny rozmiar obrazu (przekazywanie obrazów szkoleniowych) |6 MB|6 MB|
|Maksymalny rozmiar obrazu (przewidywanie)|4 MB|4 MB|
|Obraz szkoleń dotyczących liczby regionów na wykrycie obiektu|300|300|
|Maksymalna liczba tagów na obraz klasyfikacji|100|100|

> [!NOTE]
> Obrazy o rozmiarze mniejszym niż 256 pikseli zostaną zaakceptowane, ale przeskalowane.
