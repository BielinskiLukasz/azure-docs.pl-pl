---
title: Sposób wypełniania formularza informacje techniczne | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak wprowadzić wartości w formularzu informacje techniczne dla nowej aplikacji Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5aa118815146287d00a3bb8ee7d5fce57a6ad9ca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120299"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Sposób wypełniania formularza informacje techniczne
===========================================

1.  W **wybierz typ aplikacji** sekcji, przekazywanie rozszerzenia pliku pakietu (.app) i wszystkie pliki pakietu rozszerzenia rozszerzenia ma zależność.

    ![Pakiet aplikacji informacji](./media/d365-financials/image015.png)

-   **Plik pakietu rozszerzenia** — wymagana: rozszerzenie pliku pakietu (.app).

-   **Plik pakietu zależności** — wymagany, jeśli aplikacja ma zależności w innej aplikacji opublikowanych w usłudze AppSource. Ten plik .app rozszerzenie już opublikowane w usłudze AppSource bieżącej aplikacji jest zależne od. 

-   **Plik pakietu biblioteki** -wymagane, jeśli aplikacja ma zależności w innej aplikacji, która jest *nie* opublikowane w usłudze AppSource. Ta .app plik z istniejącej aplikacji, ale jedna nie została i nie zostaną opublikowane w usłudze AppSource.

-   **Automatyzacja testowania aplikacji** — wymagana: potrzeby automatycznego testowania rozszerzeń, należy utworzyć pakiet testowy kodowanego programu VS.

1. W **dodatkowe informacje dotyczące rozszerzenia** sekcji, a następnie przekaż dodatkowe informacje dotyczące Twojego rozszerzenia. Te informacje są używane podczas weryfikacji.

   ![Dodatkowe informacje dotyczące formularza rozszerzenia aplikacji](./media/d365-financials/image016.png)


-   **Adres URL dokumentacji dotyczącej produktu** — wymagane — adres URL do dokumentacji dla rozszerzenia.

-   **Scenariusze użycia klucza** — wymagana: dokument, który wyświetla szczegóły konfiguracji i używania krok po kroku dla rozszerzenia. Przykład można znaleźć w artykule [dokumentacja scenariusza użytkownika](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/).

-   **Docelowa wersja** — wymagana — Wybieranie wersji, na którym chcesz wdrożyć aplikację. Wybierz **bieżącego** do wdrożenia na bieżącej wersji na rynku. Wybierz **obok niewielkie** do wdrożenia z następną wersją pomocniczą do wydania. Wybierz **obok głównych** do wdrożenia z następnej wersji głównej mogą być wprowadzane.

-   **Wymaga jednostki SKU Premium** — opcjonalnie — wybierz pozycję warstwa Premium — przycisk, jeśli aplikacja wymaga jednostki SKU Premium. Zarządzanie usługami i Produkcja są dostępne tylko w warstwie premium. Szczegółowe informacje na temat podstawowych vs Premium można znaleźć w artykule [zmiana które funkcje są wyświetlane](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **Wyjaśnienie błędy analizy kodu** — opcjonalnie — dokument, który zawiera listę i uzasadnia wszelki kod, który nie spełnia wymagań.

-   **Wyjaśnienie wpływu na podstawowe funkcje** — opcjonalnie — dokument, który przedstawiono oraz wyjaśniono wszystkie podstawową funkcję, która jest ograniczona przez rozszerzenie.

-   **Konta testowe** kont użytkowników — opcjonalnie — dla usługi zdalnej, witryn sieci web, itp., który będzie wymagany do ukończenia testu użycia typu end to end.

-   **Wyjątki wymagania dotyczące środowiska użytkownika** — opcjonalnie — dokument, który zawiera listę i uzasadnia wszelkie środowisko użytkownika, wymagania nie zostały spełnione przez rozszerzenie.

Następnym krokiem jest, aby dodać StoreFront — szczegóły oferty.
