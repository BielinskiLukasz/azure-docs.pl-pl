---
title: Operacje Menedżera usługi synchronizacji programu Azure AD Connect | Dokumentacja firmy Microsoft
description: Dowiedz się, na karcie operacje Menedżera usługi synchronizacji programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195298"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Za pomocą karty operacje Menedżera usługi synchronizacji

![Menedżera usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Na karcie operacje przedstawia wyniki z ostatnich operacji. Ta karta jest kluczem do zrozumienia i rozwiązywania problemów.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Objaśnienie informacji widocznych na karcie operacje
Górnej połowie pokazuje wszystkie przebiegi w porządku chronologicznym. Domyślnie operacje logowania zachowuje informacje o ostatnich siedmiu dni, ale ustawienie to można zmienić za pomocą [harmonogramu](how-to-connect-sync-feature-scheduler.md). Chcesz wyszukać każde uruchomienie, które nie są wyświetlane stan oznaczający powodzenie. Można zmienić sortowania, klikając nagłówki.

**Stan** kolumna jest najważniejsze informacje i pokazuje najpoważniejszych problemów dotyczące przebiegu. Poniżej przedstawiono krótkie podsumowanie najbardziej typowych stanów w kolejności priorytetów w celu zbadania (gdzie * kilka ciągi błędów można wskazać,).

| Stan | Komentarz |
| --- | --- |
| Zatrzymano-\* |Nie można ukończyć działanie. Jeśli na przykład nie działa i nie można skontaktować się z systemu zdalnego. |
| stopped-error-limit |Wystąpiły błędy w więcej niż 5000. Uruchom automatycznie zostało zatrzymane z powodu dużej liczby błędów. |
| ukończone -\*— błędy |Przebieg został ukończony, ale występują błędy (mniej niż 5000), które należy zbadać. |
| ukończone -\*— ostrzeżenia |Uruchom ukończone, ale niektóre dane nie jest w oczekiwanym stanie. Jeśli błędy, następnie ten komunikat jest zwykle tylko objawem. Dopóki nie zostały rozwiązane błędy, nie powinien być sprawdzony ostrzeżenia. |
| powodzenie |Brak problemów. |

Po zaznaczeniu wiersza dolnej aktualizuje umożliwiające wyświetlenie szczegółów uruchomienia. Na końcu z lewej strony dolnego, Niewykluczone, że powiedzenie listy **kroku #**. Ta lista pojawia się tylko, jeśli masz wiele domen w lesie, gdzie każda domena jest reprezentowany przez krok. Nazwa domeny można znaleźć pod nagłówkiem **partycji**. W obszarze **statystyki synchronizacji**, można znaleźć więcej informacji na temat liczby zmian, które zostały przetworzone. Po kliknięciu łącza, aby uzyskać listę zmienionych obiektów. Jeśli masz obiektów z błędami, usuń te błędy są wyświetlane w oknie **błędów synchronizacji**.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z obiektem, który nie jest synchronizowany](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
