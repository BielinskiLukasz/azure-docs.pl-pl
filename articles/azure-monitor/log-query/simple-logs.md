---
title: Proste środowisko dzienników w Azure Monitor (wersja zapoznawcza) | Microsoft Docs
description: Proste środowisko logowania umożliwia tworzenie podstawowych zapytań w Azure Monitor bez bezpośredniego działania z KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660261"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Proste środowisko dzienników w Azure Monitor (wersja zapoznawcza)
Azure Monitor oferuje [bogate środowisko](get-started-portal.md) tworzenia [zapytań dzienników](log-query-overview.md) przy użyciu języka KQL. W przypadku podstawowych wymagań związanych z zapytaniami nie można wymagać pełnej mocy KQL. Proste środowisko dzienników umożliwia tworzenie podstawowych zapytań bez bezpośredniego działania z KQL. Możesz również użyć prostych dzienników jako narzędzia edukacyjnego dla KQL, ponieważ potrzebne są bardziej zaawansowane zapytania.

> [!NOTE]
> Dzienniki proste są obecnie zaimplementowane jako testy wyłącznie dla Cosmos DB i magazynów kluczy. Podziel się z firmą Microsoft za pomocą funkcji [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) , aby pomóc nam w ustaleniu, czy ta funkcja zostanie rozszerzona.


## <a name="scope"></a>Zakres
W ramach prostych dzienników środowisko to umożliwia pobieranie danych z tabeli *AzureDiagnostics*, *AzureMetrics*i *platformy Azure* dla wybranego zasobu. 

## <a name="using-simple-logs"></a>Korzystanie z prostych dzienników
Przejdź do dowolnego Cosmos DB lub Key Vault w subskrypcji platformy Azure z [ustawieniami diagnostycznymi skonfigurowanymi do zbierania dzienników w obszarze roboczym log Analytics](../platform/resource-logs-collect-storage.md). Kliknij pozycję **dzienniki** w menu **monitorowanie** , aby otworzyć proste środowisko dzienników.

![Menu](media/simple-logs/menu.png)

Wybierz **pole** i **operator** i określ **wartość** do porównania. Kliknij **+** i określ **i/lub,** aby dodać dodatkowe kryteria.

![Kryteria](media/simple-logs/criteria.png)

Kliknij przycisk **Uruchom** , aby wyświetlić wyniki zapytania.

## <a name="view-and-edit-kql"></a>Wyświetlanie i edytowanie KQL
Wybierz **Edytor zapytań** , aby otworzyć KQL wygenerowanego przez proste zapytanie dzienników w pełnym log Analytics. 

![Edytor zapytań](media/simple-logs/query-editor.png)

Możesz bezpośrednio edytować KQL i korzystać z innych funkcji w Log Analytics, takich jak filtry, aby udoskonalić wyniki.

![Edytuj KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Następne kroki

- Ukończ samouczek dotyczący [korzystania z log Analytics w Azure Portal](get-started-portal.md).
- Ukończ samouczek dotyczący [pisania zapytań dzienników](get-started-portal.md).
