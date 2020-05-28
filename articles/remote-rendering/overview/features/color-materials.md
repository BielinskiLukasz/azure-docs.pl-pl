---
title: Materiały kolorów
description: Opisuje typ materiału koloru.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: af33a777d2d6ef53965c2168ac0abee00f59bc50
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021385"
---
# <a name="color-materials"></a>Materiały kolorów

*Materiały koloru* są jednym z obsługiwanych [typów materiałów](../../concepts/materials.md) w ramach renderowania zdalnego na platformie Azure. Są one używane w przypadku [siatek](../../concepts/meshes.md) , które nie powinny mieć żadnego rodzaju oświetlenia, ale zamiast tego są zawsze w pełni jasność. Może to dotyczyć materiałów poświata, takich jak pulpity nawigacyjne samochodu, żarówki lub dane, które już obejmują statyczne oświetlenie, takie jak modele uzyskane za pomocą [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

Materiały koloru są wydajniejsze do renderowania niż w przypadku użycia [materiałów PBR](pbr-materials.md) ze względu na prostszy model cieniowania. Obsługują one również różne tryby przezroczystości.

## <a name="common-material-properties"></a>Właściwości wspólnego materiału

Te właściwości są wspólne dla wszystkich materiałów:

* **albedoColor:** Ten kolor jest mnożony przez inne kolory, takie jak *albedoMap* lub * :::no-loc text="vertex"::: kolory*. Jeśli *przezroczystość* jest włączona w materiale, kanał alfa jest używany do dostosowywania nieprzezroczystości, `1` co oznacza całkowite nieprzezroczystość i `0` znaczenie w pełni przezroczyste. Wartość domyślna to biały.

  > [!NOTE]
  > Ponieważ materiały koloru nie odzwierciedlają środowiska, całkowicie przezroczysty materiał koloru jest niewidoczny. Jest to różne dla [materiałów PBR](pbr-materials.md).

* **albedoMap:** [Tekstura 2D](../../concepts/textures.md) dla wartości albedo dla pikseli.

* **alphaClipEnabled** i **AlphaClipThreshold:** Jeśli *alphaClipEnabled* ma wartość true, wszystkie piksele, w których wartość alfa albedo jest niższa niż *alphaClipThreshold* , nie będą rysowane. Przycinanie alfa może być używane nawet bez włączania przezroczystości i jest znacznie szybsze do renderowania. Materiały przycinane alfa są ciągle wolniejsze, aby były renderowane od całkowicie nieprzezroczystych materiałów. Domyślnie przycinanie alfa jest wyłączone.

* **textureCoordinateScale** i **textureCoordinateOffset:** Skala jest mnożona do współrzędnych tekstury UV, przesunięcie jest dodawane do niego. Może służyć do rozciągnięcia i przesunięcia tekstur. Domyślna Skala to (1, 1), a przesunięcie to (0, 0).

* **useVertexColor:** Jeśli siatka zawiera :::no-loc text="vertex"::: kolory, a ta opcja jest włączona, :::no-loc text="vertex"::: kolor siatki jest mnożony do *albedoColor* i *albedoMap*. Domyślnie *useVertexColor* jest wyłączone.

* **isDoubleSided:** Jeśli jest ustawiona wartość true, Trójkąty z tym materiałem są renderowane, nawet jeśli lampa jest oglądana na ich powierzchni. Domyślnie ta opcja jest wyłączona. Zobacz też [ :::no-loc text="Single-sided"::: renderowanie](single-sided-rendering.md).

## <a name="color-material-properties"></a>Właściwości materiału koloru

Następujące właściwości są specyficzne dla materiałów kolorów:

* **vertexMix:** Ta wartość między `0` i `1` określa, jak silnie :::no-loc text="vertex"::: kolor w [siatce](../../concepts/meshes.md) składa się na kolor końcowy. Wartość domyślna 1 oznacza, że :::no-loc text="vertex"::: kolor jest mnożony do koloru albedo w pełni. Jeśli wartość jest równa 0, :::no-loc text="vertex"::: kolory są ignorowane całkowicie.

* **przezroczysty:** W przeciwieństwie do [materiałów PBR](pbr-materials.md), materiały koloru odróżniają między różnymi trybami przezroczystości:

  1. **Nieprzezroczysty:** Tryb domyślny wyłącza przezroczystość. Przycinanie alfa jest nadal możliwe, ale powinno być preferowane, jeśli jest wystarczające.
  
  1. **AlphaBlended:** Ten tryb jest podobny do trybu przezroczystości dla materiałów PBR. Powinien być używany do materiałów do zapatrzenia, takich jak szkło.

  1. **Dodatek:** Ten tryb jest najprostszym i najbardziej wydajnym trybem przezroczystości. Wkład materiału jest dodawany do renderowanego obrazu. Tego trybu można użyć do symulowania blasków (ale nadal przezroczystych) obiektów, takich jak znaczniki używane do wyróżniania ważnych obiektów.

## <a name="next-steps"></a>Następne kroki

* [Materiały PBR](pbr-materials.md)
* [Tekstury](../../concepts/textures.md)
* [Siatki](../../concepts/meshes.md)
