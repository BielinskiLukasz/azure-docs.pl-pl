---
title: Jednostki SKU dla obrazu kontenerów platformy Azure | Dokumentacja firmy Microsoft
description: Skonfiguruj jednostki SKU dla usługi Azure container.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 81f7e46e626bb061881be53e8cace36e1478e0e1
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683982"
---
# <a name="container-skus-tab"></a>Kontener jednostek SKU kartę

**Jednostki SKU** karcie **nowa oferta** strona pozwala na tworzenie jednego lub więcej jednostek SKU i kojarzyć je z nowej oferty.  Różne jednostki SKU służy do rozróżnienia rozwiązania przez zestawy funkcji, modelami rozliczeń lub innych cech.

## <a name="sku-settings"></a>Ustawienia jednostki SKU

Po rozpoczęciu tworzenia nowej oferty, nie ma żadnych jednostek SKU skojarzone z ofertą. Aby utworzyć nowe jednostki SKU, wykonaj następujące kroki:

1. Na karcie jednostki SKU wybierz **nowej jednostki SKU**

   ![Nowy wiersz w jednostce SKU](./media/containers-sku-settings.png)

2. Podaj wymagane informacje jednostki SKU i kontenera. Każda jednostka SKU odnosi się do obrazu kontenera. Istnieją dwie części do jednostki SKU:

    -   Metadane jednostki SKU
    -   Metadane kontenera

### <a name="sku-metadata"></a>Metadane jednostki SKU

Metadane jednostki SKU zawierają storefront wyświetlanych informacji dla listy kontenera.

![Metadane jednostki SKU](./media/containers-sku-details.png)

### <a name="container-metadata"></a>Metadane kontenera

Metadane kontenera zawierają informacje o odwołaniu swojego repozytorium obrazów w usłudze Azure Container Registry (ACR). Portal Azure Marketplace kopiuje ten obraz do rejestru specyficzne dla portalu Marketplace, publiczna, a następnie udostępnia obraz dostępne dla klientów po certyfikacji. Wszystkie żądania od użytkownika platformy Azure z obrazu kontenera w witrynie Azure Marketplace są obsługiwane z rejestru publicznego w witrynie Marketplace, nie rejestru Azure container Registry.

![Metadane kontenera](./media/containers-image-repository.png)
    
**Szczegółów repozytorium obrazów** na poprzednim ekranie przechwytywania zawiera następujące pola:

-   **Identyfikator subskrypcji** — identyfikator subskrypcji platformy Azure, gdzie rekordu ACR jest obecny.
-   **Nazwa grupy zasobów** — Nazwa grupy zasobów usługi ACR.
-   **Nazwa rejestru** — Nazwa rekordu ACR.
-   **Nazwa repozytorium** — Nazwa repozytorium. Po ustawieniu tej nazwy, nie można zmienić tę wartość. Użyj unikatowej nazwy, aby uniknąć konfliktów z innymi ofertami na Twoim koncie.
-   **Nazwa użytkownika** -username (nazwa użytkownika administratora) skojarzone z obrazem usługi ACR.
-   **Hasło** -hasło skojarzone z obrazem usługi ACR.

    >[!NOTE]
    >Nazwa użytkownika i hasło są wymagane, aby upewnić się, że partnerzy mają dostęp do usługi ACR, o których wspomniano w procesie publikowania.

### <a name="image-version"></a>Wersja obrazu

Podczas publikowania obrazu kontenera, możesz podać jeden lub więcej tagów obrazu i skróty służące SHA.

**Tag obrazu lub skrótu**
 
- Tego tagu lub skrótu musi zawierać `latest` znacznikiem i wersji (na przykład, rozpoczynając od `xx.xx.xx-` gdzie xx jest liczbą). Powinny one być [manifestu tagi](https://github.com/estesp/manifest-tool) do wielu platform docelowych. Wszystkie tagi, które odwołuje się tag manifestu również musi zostać dodany, dzięki czemu możemy przekazać je. 
- Można dodać kilka wersji kontenerów przy użyciu tagów. Manifest wszystkie tagi (z wyjątkiem `latest`) musi rozpoczynać się albo `X.Y-` lub `X.Y.Z-` gdzie X, Y, Z są liczbami całkowitymi. <br/> Na przykład jeśli `latest` tag wskazuje `1.0.1-linux-x64`, `1.0.1-linux-arm32`, i `1.0.1-windows-arm32`, te znaczniki, które muszą zostać dodane w tym miejscu.

>[!NOTE]
>Pamiętaj, aby dodać **tag testowy** w obrazie, dzięki czemu można zidentyfikować obrazu podczas testowania.

## <a name="next-steps"></a>Kolejne kroki

Użyj [kartę Marketplace](./cpp-marketplace-tab.md) utworzyć portalu marketplace opis oferty. 