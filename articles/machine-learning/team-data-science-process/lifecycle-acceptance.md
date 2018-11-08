---
title: Etap akceptacji klienta cykl życia zespołowego danych naukowych — Azure | Dokumentacja firmy Microsoft
description: Cele, zadania i elementy dostarczane dla etapu akceptacji klienta projektów do nauki o danych
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 9dd3ab8c9451ecfe6b095b52f5af083a7c7e9550
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232763"
---
# <a name="customer-acceptance"></a>Akceptacja klienta

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzone z etapem akceptacji klienta procesu do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i opis**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
**Finalizowanie elementy dostarczane**: Upewnij się, że potok, model i ich wdrażania w środowisku produkcyjnym spełnia cele klienta.

## <a name="how-to-do-it"></a>Jak to zrobić
Istnieją dwa główne zadania, które zostały rozwiązane podczas tego etapu:

   * **Sprawdzanie poprawności systemu**: Upewnij się, że wdrożony model i potoku potrzeb klienta.
   * **Projekt ręcznie wyłączyć**: przekazują projektu do jednostki, która ma wejść do zapewnienia działania systemu w środowisku produkcyjnym.

Klienta należy zweryfikować, czy system spełnia ich potrzeby biznesowe i odpowiedzi pytania, z dokładnością dopuszczalne do wdrożenia systemu do środowiska produkcyjnego do użycia przez aplikację swoich klientów. Cała dokumentacja sfinalizowana, a przeglądane. Projekt jest przekazywane wyłączenia go z jednostką odpowiedzialny za operacje. Ta jednostka może być na przykład IT lub zespół do nauki o danych klientów lub agenta klienta, który jest odpowiedzialny za działanie systemu w środowisku produkcyjnym. 

## <a name="artifacts"></a>Artefakty
Główne artefakt, utworzone w tym ostatnim krokiem jest **wyjść raportów programu projekt klienta**. Ten raport techniczny zawiera wszystkie szczegóły projektu, które są przydatne do nauki o tym, jak korzystać z systemu. Przetwarzanie TDSP zapewnia [Zamknij raport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) szablonu. Możesz użyć szablonu, ponieważ jest lub można go dostosować do potrzeb określonego klienta. 


## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i opis](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Firma Microsoft oferuje instruktaży pełnej end-to-end, które przedstawiają wszystkie kroki w procesie dla konkretnych scenariuszy. [Przykładowe przewodniki](walkthroughs.md) artykuł zawiera listę scenariuszy wraz z linkami i opisy miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Przykłady sposób wykonywania kroków w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [przetwarzania TDSP za pomocą usługi Azure Machine Learning](https://aka.ms/datascienceprocess).
