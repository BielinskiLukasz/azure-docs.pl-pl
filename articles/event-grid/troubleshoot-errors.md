---
title: Azure Event Grid — Przewodnik rozwiązywania problemów
description: Ten artykuł zawiera listę kodów błędów, komunikatów o błędach, opisów i zalecanych akcji.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645076"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Rozwiązywanie problemów z błędami Azure Event Grid
Ten przewodnik rozwiązywania problemów zawiera listę Azure Event Grid kodów błędów, komunikatów o błędach, ich opisach oraz zalecanych czynnościach, które należy wykonać po otrzymaniu tych błędów. 

## <a name="error-code-400"></a>Kod błędu: 400
| Kod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. nieprawidłowego żądania<br/>400 | Nazwa tematu musi mieć długość od 3 do 50 znaków. | Nazwa tematu niestandardowego powinna mieć długość od 3 do 50 znaków. W nazwie tematu są dozwolone tylko litery alfanumeryczne, cyfry i znaki "-". Ponadto nazwa nie powinna zaczynać się od następujących słów zarezerwowanych: <ul><li>Microsoft</li><li>EventGrid</li><li>System</li></ul> | Wybierz inną nazwę tematu, która jest zgodna z wymaganiami dotyczącymi nazwy tematu. |
| HttpStatusCode. nieprawidłowego żądania<br/>400 | Nazwa domeny musi mieć długość od 3 do 50 znaków. | Nazwa domeny powinna mieć długość od 3 do 50 znaków. W nazwie tematu są dozwolone tylko litery alfanumeryczne, cyfry i znaki "-". Ponadto nazwa nie powinna zaczynać się od następujących słów zarezerwowanych:<ul><li>Microsoft</li><li>EventGrid</li><li>System</li> | Wybierz inną nazwę domeny, która jest zgodna z wymaganiami dotyczącymi nazwy domeny. |
| HttpStatusCode. nieprawidłowego żądania<br/>400 | Nieprawidłowy czas wygaśnięcia. | Czas wygaśnięcia subskrypcji zdarzenia określa, kiedy subskrypcja zdarzeń zostanie wycofana. Ta wartość powinna być prawidłową wartością daty/godziny w przyszłości.| Upewnij się, że czas wygaśnięcia subskrypcji zdarzenia w prawidłowym formacie daty i godziny jest ustawiony na wartość w przyszłości. |

## <a name="error-code-409"></a>Kod błędu: 409
| Kod błędu | Komunikat o błędzie | Opis | Zalecana akcja |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. konflikt <br/>409 | Temat o podanej nazwie już istnieje. Wybierz inną nazwę tematu.   | Nazwa tematu niestandardowego powinna być unikatowa w jednym regionie świadczenia usługi Azure, aby zapewnić poprawną operację publikowania. Tej samej nazwy można używać w różnych regionach świadczenia usługi Azure. | Wybierz inną nazwę tematu. |
| HttpStatusCode. konflikt <br/> 409 | Domena o określonej już istnieje. Wybierz inną nazwę domeny. | Nazwa domeny powinna być unikatowa w jednym regionie świadczenia usługi Azure, aby zapewnić poprawną operację publikowania. Tej samej nazwy można używać w różnych regionach świadczenia usługi Azure. | Wybierz inną nazwę domeny. |
| HttpStatusCode. konflikt<br/>409 | Osiągnięto limit przydziału. Aby uzyskać więcej informacji na temat tych limitów, zobacz [limity Azure Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Dla każdej subskrypcji platformy Azure obowiązuje limit liczby zasobów Azure Event Grid, których może używać. Przekroczono część lub wszystkie te limity przydziału i nie można utworzyć więcej zasobów. |    Sprawdź bieżące użycie zasobów i usuń te, które nie są potrzebne. Jeśli nadal trzeba zwiększyć limit przydziału, Wyślij wiadomość e-mail w celu [aeg@microsoft.com](mailto:aeg@microsoft.com) o dokładnej liczbie potrzebnych zasobów. |


## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, Opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
