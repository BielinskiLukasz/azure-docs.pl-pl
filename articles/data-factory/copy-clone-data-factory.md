---
title: Kopiowanie lub klonowanie fabryki danych w Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak skopiować lub sklonować fabrykę danych w Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: e4d204e9bcf601bf968f06b5d9df3f36414bd5a5
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884187"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopiowanie lub klonowanie fabryki danych w Azure Data Factory

W tym artykule opisano sposób kopiowania lub klonowania fabryki danych w Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Przypadki użycia do klonowania fabryki danych

Poniżej przedstawiono niektóre sytuacje, w których warto przystąpić do kopiowania lub klonowania fabryki danych:

-   **Zmiana nazw zasobów**. Platforma Azure nie obsługuje zmieniania nazw zasobów. Jeśli chcesz zmienić nazwę fabryki danych, możesz sklonować fabrykę danych z inną nazwą, a następnie usunąć istniejącą.

-   **Debugowanie zmian** , gdy funkcje debugowania nie są wystarczające. Czasami testować zmiany, warto przetestować zmiany w innej fabryce przed zastosowaniem ich do swojej głównej. W większości scenariuszy można używać debugowania. Zmiany w wyzwalaczach, jednak takie jak zachowanie zmian, gdy wyzwalacz jest wywoływany automatycznie lub w przedziale czasowym, mogą nie być weryfikowalne łatwo bez ewidencjonowania. W takich przypadkach klonowanie fabryki i stosowanie zmian w tym miejscu sprawia dużo sensu. Ponieważ Azure Data Factory opłaty są naliczane głównie przez liczbę przebiegów, druga fabryka nie prowadzi do żadnych dodatkowych opłat.

## <a name="how-to-clone-a-data-factory"></a>Jak sklonować fabrykę danych

1. Interfejs użytkownika Data Factory w Azure Portal umożliwia wyeksportowanie całego ładunku fabryki danych do szablonu Menedżer zasobów oraz pliku parametrów, który umożliwia zmianę wszelkich wartości, które mają zostać zmienione podczas klonowania fabryki.

1. Jako warunek wstępny należy utworzyć docelową fabrykę danych z Azure Portal.

1. Jeśli w fabryki źródłowej znajduje się SelfHosted IntegrationRuntime, musisz utworzyć ją z tą samą nazwą w fabryce docelowej. Jeśli chcesz udostępnić SelfHosted urząd skarbowy między różnymi fabrykami, możesz użyć wzorca opublikowanego w [tym miejscu](source-control.md#best-practices-for-git-integration).

1. Jeśli jesteś w trybie GIT, przy każdym publikowaniu z portalu szablon Menedżer zasobów fabryki zostanie zapisany w usłudze GIT w gałęzi adf_publish repozytorium.

1. W przypadku innych scenariuszy szablon Menedżer zasobów można pobrać, klikając przycisk **Eksportuj szablon Menedżer zasobów** w portalu.

1. Po pobraniu szablonu Menedżer zasobów można wdrożyć go za pomocą standardowych metod wdrażania szablonu Menedżer zasobów.

1. Ze względów bezpieczeństwa wygenerowany szablon Menedżer zasobów nie zawiera żadnych informacji tajnych, takich jak hasła dla połączonych usług. W związku z tym należy podać te hasła jako parametry wdrożenia. Jeśli podanie parametrów nie jest pożądane, należy uzyskać parametry połączenia i hasła połączonej usługi z Azure Key Vault.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze wskazówkami dotyczącymi tworzenia fabryki danych w Azure Portal w temacie [Tworzenie fabryki danych przy użyciu interfejsu użytkownika Azure Data Factory](quickstart-create-data-factory-portal.md).
