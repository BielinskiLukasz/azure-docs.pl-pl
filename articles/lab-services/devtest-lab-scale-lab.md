---
title: Skalowanie limity przydziału i ograniczenia w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować laboratorium w usłudze Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 00f64deaa5e02f9bb74baaf86bfdf71690a3345d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248252"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skalowanie w usłudze DevTest Labs limity przydziału i ograniczenia
Podczas pracy w usłudze DevTest Labs, może się okazać, że nie istnieją pewne domyślne limity do niektórych zasobów platformy Azure, które mogą mieć wpływ na usługi DevTest Labs. Limity te są nazywane **przydziały**.

> [!NOTE]
> Usługa DevTest Labs nie nakłada żadnych limitów przydziału. Przydziały, które mogą wystąpić są domyślne ograniczenia subskrypcja ogólna platformy Azure.

Można użyć poszczególnych zasobów platformy Azure, aż osiągniesz limit przydziału. Każda subskrypcja ma oddzielne limity przydziału i użycie jest śledzone oddzielnie dla każdej subskrypcji.

Przykładowo Każda subskrypcja ma domyślny limit przydziału rdzeni 20. Dlatego w przypadku tworzenia maszyn wirtualnych w środowisku laboratoryjnym przy użyciu cztery rdzenie, następnie można tylko utworzyć pięciu maszyn wirtualnych. 

[Limity usług i subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits) wymieniono niektóre z najbardziej typowych limitów przydziału dla zasobów platformy Azure. Zasoby są najczęściej używane w laboratorium, a dla mogą wystąpić przydziałów, zawierają rdzeni maszyn wirtualnych, publiczne adresy IP, interfejs sieciowy, dyski zarządzane, przypisania ról RBAC i obwodów usługi ExpressRoute.

## <a name="view-your-usage-and-quotas"></a>Wyświetlanie użycia i przydziałów
Te kroki pokazują, jak wyświetlić bieżące limity przydziału w subskrypcji dla określonych zasobów platformy Azure i zobacz, jaki procent każdy przydział były używane.

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz **więcej usług**, a następnie wybierz pozycję **rozliczeń** z listy.
1. W bloku rozliczenia Wybierz subskrypcję.
4. Wybierz **użycie i przydziały**.

   ![Przycisk użycie i przydziały](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Użycie i przydziały zostanie wyświetlony blok, listę różnych zasobów dostępnych w tej subskrypcji i procent przydziału, który jest używany dla każdego zasobu.

   ![Limity przydziału i użycia](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Żądanie więcej zasobów w ramach subskrypcji
Jeśli przekroczysz limit przydziału, domyślny limit zasobów w ramach subskrypcji można zwiększyć do maksymalnego limitu, zgodnie z opisem w [limity usług i subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Te kroki pokazują, jak można zażądać zwiększenia limitu przydziału, za pośrednictwem [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Wybierz **więcej usług**, wybierz opcję **rozliczeń**, a następnie wybierz pozycję **użycie i przydziały**.
1. W użycie i przydziały bloku wybierz **żądanie zwiększenia** przycisku.

   ![Przycisk wzrost żądania](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Aby wypełnić oraz przesłać żądanie, wypełnij wymagane informacje, na wszystkich trzech kartach **nowe żądanie obsługi** formularza.

   ![Formularz wniosku o zwiększenie](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Informacje o limitach usługi Azure i zwiększa](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) zawiera więcej informacji na temat kontaktując się z pomocą techniczną platformy Azure, aby zażądać zwiększenia limitu przydziału.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z [galerii szablonów DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
