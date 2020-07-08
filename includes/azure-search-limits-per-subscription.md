---
title: dołączanie pliku
description: dołączanie pliku
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80272689"
---
Można utworzyć wiele usług w ramach subskrypcji. Każdy z nich może być inicjowany w określonej warstwie. Ograniczenie jest ograniczone tylko do liczby usług dozwolonych w każdej warstwie. Można na przykład utworzyć maksymalnie 12 usług w warstwie Podstawowa i inne 12 usług w warstwie S1 w ramach tej samej subskrypcji. Aby uzyskać więcej informacji o warstwach, zobacz [Wybieranie jednostki SKU lub warstwy dla wyszukiwanie poznawcze platformy Azure](../articles/search/search-sku-tier.md).

Na żądanie można zwiększyć maksymalną liczbę limitów usług. Jeśli potrzebujesz więcej usług w ramach tej samej subskrypcji, skontaktuj się z pomocą techniczną platformy Azure.

| Zasób            | Bezpłatnie<sup>1</sup> | Podstawowy | S1  | S2 | S3 | &nbsp;Wyjście S3 HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maksymalna liczba usług    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maksymalna skala w jednostkach wyszukiwania (SU)<sup>2</sup> |Nie dotyczy |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> bezpłatna jest oparta na udostępnionych, niededykowanych zasobach. Skalowanie w górę nie jest obsługiwane w zasobach udostępnionych.

<sup>2</sup> jednostki wyszukiwania to jednostki rozliczeń, które są przydzielone jako *replika* lub *partycja*. Potrzebne są oba zasoby dotyczące magazynu, indeksowania i operacji zapytań. Aby dowiedzieć się więcej na temat obliczeń SU, zobacz [skalowanie poziomów zasobów dla obciążeń zapytań i indeksów](../articles/search/search-capacity-planning.md). 