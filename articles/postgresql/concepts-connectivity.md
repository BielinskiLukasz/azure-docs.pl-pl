---
title: Obsługa błędów przejściowych łączności dla usługi Azure Database for PostgreSQL
description: Informacje o sposobie obsługi błędów przejściowych łączności dla usługi Azure Database for PostgreSQL.
keywords: połączenie postgresql, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 264656da38608026e3f9e866e2184ff55ba102d8
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536223"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-postgresql"></a>Obsługa błędów przejściowych łączności dla usługi Azure Database for PostgreSQL

W tym artykule opisano sposób obsługi błędów przejściowych, łączenia z bazą danych Azure database for PostgreSQL.

## <a name="transient-errors"></a>Błędy przejściowe

Błąd przejściowy, znany także jako błędów przejściowych, jest błąd, który zostanie rozwiązany automatycznie. Najczęściej te błędy manifestu, ponieważ połączenie z serwerem bazy danych Trwa porzucanie. Ponadto nie można otworzyć nowego połączenia z serwerem. Błędy przejściowe może wystąpić na przykład po awarii sprzętu lub sieci się dzieje. Inną przyczyną może być nowa wersja usługi PaaS, która jest wdrażana. Większość z tych zdarzeń są automatycznie skorygowany przez system w mniej niż 60 sekund. Najlepszym rozwiązaniem dla projektowania i tworzenia aplikacji w chmurze jest oczekiwany błędów przejściowych. Załóżmy, że ich może nastąpić w dowolnej części w dowolnym momencie i mieć odpowiednią logikę w celu obsługi tych sytuacji.

## <a name="handling-transient-errors"></a>Obsługa błędów przejściowych

Błędy przejściowe z niepoprawnymi przy użyciu logikę ponawiania próby. Sytuacje, które należy wziąć pod uwagę:

* Występuje błąd podczas próby otwarcia połączenia
* Połączenie zostało porzucone po stronie serwera. Podczas próby wydać polecenie nie można wykonać
* Aktywnego połączenia, który aktualnie wykonuje polecenie zostało porzucone.

W przypadku pierwszego i drugiego są stosunkowo proste do obsługi. Spróbuj otworzyć ponownie nawiązać połączenie. Gdy powiedzie, błąd przejściowy ma zostały skorygowane przez system. Można ponownie użyć usługi Azure Database for PostgreSQL. Firma Microsoft zaleca posiadanie czeka przed ponowieniem próby połączenia. Możesz się wycofać w przypadku awarii początkowej ponownych prób. Dzięki temu system można użyć wszystkich zasobów dostępnych do pokonania sytuacji błędu. Dobre wzorzec do wykonania jest:

* Poczekaj 5 sekund przed swoje pierwsze ponowienie.
* Dla każdego następujących ponawiania próby, zwiększyć czas oczekiwania wykładniczo, maksymalnie 60 sekund.
* Ustaw maksymalną liczbę ponownych prób w tym momencie aplikacji uwzględnia operacja nie powiodła się.

Połączenie z aktywnej transakcji nie powiedzie się, jest trudniejsze do odzyskiwania poprawnie. Są dwa przypadki: Jeśli transakcja została tylko do odczytu z natury, jest bezpieczne, aby ponownie otworzyć połączenie i spróbuj ponownie wykonać transakcję. Jeśli jednak jeśli transakcja została również zapisuje w bazie danych, należy określić, jeśli transakcja została wycofana, czy udało mu się przed wystąpił błąd przejściowy. W takiej sytuacji może po prostu nie otrzymano potwierdzenia zatwierdzenie z serwera bazy danych.

Jest jednym ze sposobów to zrobić, można wygenerować unikatowy identyfikator klienta, który jest używany dla wszystkich ponownych prób. Przekażesz ten unikatowy identyfikator w ramach transakcji do serwera i zapisz go w kolumnie z unikatowego ograniczenia. Dzięki temu można bezpiecznie spróbuj ponownie wykonać transakcję. Zakończy się pomyślnie, jeśli poprzednia transakcja została wycofana i unikatowy identyfikator klienta generowany jeszcze nie istnieje w systemie. Zakończy się niepowodzeniem, wskazująca zduplikowane naruszenie klucza, czy unikatowy identyfikator wcześniej została zapisana, ponieważ poprzednia transakcja została ukończona pomyślnie.

Gdy program komunikuje się z usługą Azure Database for PostgreSQL za pomocą oprogramowania pośredniczącego innych firm, należy poprosić dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponowień w przypadku błędów przejściowych.

Upewnij się, że można przetestować Logika ponawiania próby. Na przykład spróbuj wykonywanie kodu podczas skalowania w górę lub w dół zasobów obliczeniowych, bazy danych Azure Database dla serwera PostgreSQL. Aplikacja powinna obsługiwać krótki Przestój, które zostanie osiągnięty podczas tej operacji bez problemów.

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów z połączeniem z usługą Azure Database for PostgreSQL](howto-troubleshoot-common-connection-issues.md)
