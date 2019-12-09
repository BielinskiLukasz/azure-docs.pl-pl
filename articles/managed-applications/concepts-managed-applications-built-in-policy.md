---
title: Wdróż skojarzenia dla aplikacji zarządzanej przez platformę Azure przy użyciu zasad
description: Dowiedz się więcej o wdrażaniu skojarzeń dla aplikacji zarządzanej przy użyciu usługi Azure Policy.
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: e3dda343c1d5664e188b434c54e4c288b515d420
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932530"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Wdróż skojarzenia dla aplikacji zarządzanej przy użyciu Azure Policy

Za pomocą zasad platformy Azure można wdrażać skojarzenia w celu kojarzenia zasobów z zarządzaną aplikacją. W tym artykule opisano wbudowane zasady, które wdrażają skojarzenia i jak można korzystać z tych zasad.

## <a name="built-in-policy-to-deploy-associations"></a>Wbudowane zasady wdrażania skojarzeń

Wdróż skojarzenia dla aplikacji zarządzanej to wbudowane zasady, których można użyć do wdrożenia skojarzenia w celu skojarzenia zasobu z zarządzaną aplikacją. Zasady akceptują trzy parametry:

- Identyfikator aplikacji zarządzanej — ten identyfikator jest IDENTYFIKATORem zasobu zarządzanej aplikacji, do której należy skojarzyć zasoby.
- Typy zasobów do skojarzenia — te typy zasobów są listami typów zasobów, które mają być skojarzone z zarządzaną aplikacją. Za pomocą tych samych zasad można skojarzyć wiele typów zasobów z zarządzaną aplikacją.
- Prefiks nazwy skojarzenia — ten ciąg jest prefiksem, który ma zostać dodany do nazwy tworzonego zasobu skojarzenia. Wartość domyślna to "DeployedByPolicy".

Zasady korzystają z oceny DeployIfNotExists. Jest on uruchamiany po obsłudze żądania Create lub Update zasobu dla wybranych typów zasobów przez dostawcę zasobów, a Ocena zwróciła kod stanu sukcesu. Następnie zasób skojarzenia zostanie wdrożony przy użyciu wdrożenia szablonu.
Aby uzyskać więcej informacji na temat skojarzeń, zobacz Dołączanie [zasobów niestandardowych dostawców platformy Azure](./concepts-custom-providers-resourceonboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Jak używać wbudowanych zasad skojarzeń 

### <a name="prerequisites"></a>Wymagania wstępne
Jeśli zarządzana aplikacja wymaga uprawnień do wykonania akcji, wdrożenie zasad zasobu skojarzenia nie będzie działać bez udzielania uprawnień.

### <a name="policy-assignment"></a>Przypisywanie zasad
Aby użyć wbudowanych zasad, Utwórz przypisanie zasad i przypisz skojarzenia wdrażania dla zasad zarządzanej aplikacji. Po pomyślnym przypisaniu zasad zasady będą identyfikować niezgodne zasoby i wdrażać skojarzenia dla tych zasobów.

![Przypisywanie wbudowanych zasad](media/builtin-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Uzyskiwanie pomocy

Jeśli masz pytania dotyczące programowania niestandardowych dostawców zasobów platformy Azure, spróbuj zadawać je na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Na podobnej pytanie mogły już zostać odebrane odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag ```azure-custom-providers```, aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o używaniu wbudowanych zasad do wdrażania skojarzeń. Zobacz następujące artykuły, aby dowiedzieć się więcej:

- [Pojęcia: dołączanie zasobów niestandardowych dostawców platformy Azure](./concepts-custom-providers-resourceonboarding.md)
- [Samouczek: dołączanie zasobów z dostawcami niestandardowymi](./tutorial-custom-providers-resource-onboarding.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-custom-providers-101.md)
- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
