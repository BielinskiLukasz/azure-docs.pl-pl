---
title: Jak wykonać przegląd dostępu | Dokumentacja firmy Microsoft
description: Po rozpoczęciu przeglądu dostępu w usłudze Azure AD Privileged Identity Management Dowiedz się, jak i oznacz go jako ukończony i wyświetlić wyniki
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fc6376a7bf1abd81e7a5ff17549a7b4a193edec4
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950584"
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Jak wykonać przegląd dostępu w usłudze Azure AD Privileged Identity Management
Administratorzy ról uprzywilejowanych mogą raz Sprawdź dostęp uprzywilejowany [przeglądu zabezpieczeń została uruchomiona](active-directory-privileged-identity-management-how-to-start-security-review.md). Usługa Azure AD Privileged Identity Management (PIM) automatycznie wyśle wiadomość e-mail monitowaniu użytkowników, aby zapoznać się z ich dostęp. Jeśli użytkownik nie pobrały wiadomość e-mail, możesz wysłać im instrukcje [jak przeprowadzić przegląd zabezpieczeń](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Po zakończeniu okresu przeglądu zabezpieczeń lub wszystkich użytkowników została zakończona w ich własnym Przejrzyj, wykonaj kroki opisane w tym artykule, aby zarządzać przeglądu i wyświetlić wyniki.

## <a name="manage-security-reviews"></a>Zarządzanie przeglądów zabezpieczeń
1. Przejdź do [witryny Azure portal](https://portal.azure.com/) i wybierz **usługi Azure AD Privileged Identity Management** aplikacji na pulpicie nawigacyjnym.
2. Wybierz **przeglądów dostępu** części pulpitu nawigacyjnego.
3. Wybierz przeglądu dostępu, które mają być zarządzane.

W bloku szczegółów przeglądu dostępu istnieją opcje liczb do zarządzania przeglądu.

![Przyciski PIM do przeglądu dostępu — zrzut ekranu](./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png)

### <a name="remind"></a>Przypomnij
Jeśli przeglądu dostępu jest skonfigurowana tak, aby użytkownicy Przejrzyj, **Przypomnij** przycisk wysyła powiadomienie. 

### <a name="stop"></a>Stop
Wszystkie przeglądy dostępu mają datę końcową, ale można użyć **zatrzymać** przycisk, aby zakończyć jej początku. Jeśli jeszcze nie zostały sprawdzone wszyscy użytkownicy w tej chwili, będą mogli po zatrzymaniu przeglądu. Nie można ponownie uruchomić przeglądu, po jest zatrzymana.

### <a name="apply"></a>Zastosuj
Po zakończeniu przeglądu dostępu, ponieważ osiągnięto datę zakończenia lub ręcznie, zatrzymanie **Zastosuj** przycisk implementuje wyniku przeglądu. W przeglądzie nastąpiła odmowa dostępu użytkownika, to krok, który spowoduje usunięcie przypisania roli.  

### <a name="export"></a>Eksportowanie
Jeśli chcesz ręcznie zastosowania wyników przeglądu zabezpieczeń, możesz wyeksportować przeglądu. **Wyeksportować** przycisk rozpocznie się pobieranie pliku CSV. Wyniki w programie Excel lub inne programy, które otwierają pliki CSV można zarządzać.

### <a name="delete"></a>Usuwanie
Jeśli nie jesteś zainteresowany przeglądu w bardziej, należy go usunąć. **Usuń** przycisk usuwa przeglądu z poziomu aplikacji PIM.

> [!IMPORTANT]
> Nie zostanie wyświetlone ostrzeżenie, zanim nastąpi usunięcie, więc upewnij się, że chcesz usunąć tego przeglądu. 

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
