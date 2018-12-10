---
title: Wdrażanie modeli w środowisku produkcyjnym — zespołu danych dla celów naukowych
description: Jak wdrażać modele do produkcji, umożliwiając im odtwarzanie aktywną rolę w podejmowaniu decyzji biznesowych.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 35f9f369e752fa7c86a6bd295a79b79b23104d23
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53137875"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Wdrażaj modele do produkcji, aby odtworzyć aktywną rolę w podejmowaniu decyzji biznesowych

Wdrożenie produkcyjne umożliwia modelu do odtwarzania aktywną rolę w biznesie. Za pomocą wdrożonego modelu może służyć do podejmowania decyzji biznesowych.

## <a name="production-platforms"></a>Platform produkcyjnych

Istnieją różne podejścia i platformy, aby wdrożyć modele do produkcji. Poniżej przedstawiono kilka opcji:

- [Gdzie można wdrażać modele przy użyciu usługi Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Wdrażanie modelu w programie SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Przed wdrożeniem jedna ma upewnij się, że opóźnienie oceniania modelu jest niskie, do użycia w środowisku produkcyjnym.
>

>[!NOTE]
>Do wdrożenia przy użyciu usługi Azure Machine Learning Studio, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A / B, testowanie

W przypadku wielu modeli w środowisku produkcyjnym, może być przydatne do wykonania [A / B, testowanie](https://en.wikipedia.org/wiki/A/B_testing) porównywanie wydajności modeli. 
 
## <a name="next-steps"></a>Kolejne kroki

Wskazówki, które przedstawiają wszystkie kroki procesu **konkretnych scenariuszy** znajdują się także. Wymieniono i połączone z opisami miniatur w [przykładowe przewodniki](walkthroughs.md) artykułu. One ilustrują sposób łączenia chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 
