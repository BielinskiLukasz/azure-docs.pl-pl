---
title: Wdrażanie usługi przy użyciu szablonu Azure Resource Manager
titleSuffix: Azure Cognitive Search
description: Możesz szybko wdrożyć wystąpienie usługi Azure Wyszukiwanie poznawcze przy użyciu szablonu Azure Resource Manager.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/15/2020
ms.openlocfilehash: c7d11b9e699ec4cba59d2af9e411692cbce35278
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640178"
---
# <a name="quickstart-deploy-cognitive-search-using-an-arm-template"></a>Szybki Start: wdrażanie Wyszukiwanie poznawcze przy użyciu szablonu ARM

Ten artykuł przeprowadzi Cię przez proces używania szablonu Azure Resource Manager (szablon ARM) do wdrożenia zasobu Wyszukiwanie poznawcze platformy Azure w Azure Portal.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json":::

Zasób platformy Azure zdefiniowany w tym szablonie:

- [Microsoft. Search/searchServices](/azure/templates/Microsoft.Search/searchServices): Tworzenie usługi Azure wyszukiwanie poznawcze

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy zasób Wyszukiwanie poznawcze platformy Azure.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

W portalu jest wyświetlany formularz, który umożliwia łatwe udostępnianie wartości parametrów. Niektóre parametry są wstępnie wypełnione wartościami domyślnymi z szablonu. Musisz podać swoją subskrypcję, grupę zasobów, lokalizację i nazwę usługi. Jeśli chcesz użyć Cognitive Services w potoku [wzbogacenia AI](cognitive-search-concept-intro.md) , na przykład w celu przeanalizowania plików obrazów binarnych dla tekstu, wybierz lokalizację, w której są dostępne zarówno wyszukiwanie poznawcze, jak i Cognitive Services. Obie usługi muszą znajdować się w tym samym regionie dla obciążeń wzbogacania AI. Po zakończeniu formularza należy wyrazić zgodę na warunki i postanowienia, a następnie wybrać przycisk Kup, aby zakończyć wdrożenie.

> [!div class="mx-imgBorder"]
> ![Azure Portal wyświetlania szablonu](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po zakończeniu wdrażania możesz uzyskać dostęp do nowej grupy zasobów i nowej usługi wyszukiwania w portalu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne Wyszukiwanie poznawcze Przewodniki Szybki Start i samouczki zostały skompilowane na tym przewodniku Szybki Start. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, możesz pozostawić ten zasób na miejscu. Gdy nie jest już potrzebne, można usunąć grupę zasobów, która spowoduje usunięcie usługi Wyszukiwanie poznawcze i powiązanych zasobów.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono usługę Wyszukiwanie poznawcze przy użyciu szablonu ARM i sprawdzono poprawność wdrożenia. Aby dowiedzieć się więcej na temat Wyszukiwanie poznawcze i Azure Resource Manager, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem wyszukiwanie poznawcze platformy Azure](search-what-is-azure-search.md).
- [Utwórz indeks](search-get-started-portal.md) dla usługi wyszukiwania.
- [Tworzenie aplikacji demonstracyjnej](search-create-app-portal.md) za pomocą Kreatora portalu.
- [Utwórz zestawu umiejętności,](cognitive-search-quickstart-blob.md) aby wyodrębnić informacje z danych.
