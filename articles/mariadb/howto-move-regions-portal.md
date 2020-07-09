---
title: Przenoszenie regionów platformy Azure — Azure Portal — Azure Database for MariaDB
description: Przenieś serwer Azure Database for MariaDB z jednego regionu świadczenia usługi Azure do innego przy użyciu repliki odczytu i Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: abb692f71a3ed69c6779b6141c9098dc94c75c4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568765"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>Przenoszenie serwera Azure Database for MariaDB do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze dotyczące przechodzenia istniejącego serwera Azure Database for MariaDB z jednego regionu do innego. Na przykład możesz chcieć przenieść serwer produkcyjny do innego regionu w ramach planowania odzyskiwania po awarii.

Do przechodzenia do innego regionu można użyć Azure Database for MariaDB [replik odczytu między regionami](concepts-read-replicas.md#cross-region-replication) . W tym celu należy najpierw utworzyć replikę odczytu w regionie docelowym. Następnie Zatrzymaj replikację do serwera repliki odczytu, aby udostępnić go serwerowi Autonomicznemu, który akceptuje ruch odczytu i zapisu. 

> [!NOTE]
> Ten artykuł koncentruje się na przenoszeniu serwera do innego regionu. Jeśli chcesz przenieść serwer do innej grupy zasobów lub subskrypcji, zapoznaj się z artykułem dotyczącym [przenoszenia](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) . 

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MariaDB w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer źródłowy znajduje się w jednej z tych warstw cenowych.

- Upewnij się, że serwer źródłowy Azure Database for MariaDB znajduje się w regionie świadczenia usługi Azure, z którego chcesz przejść.

## <a name="prepare-to-move"></a>Przygotuj do przeniesienia

Aby utworzyć serwer repliki odczytu między regionami w regionie docelowym przy użyciu Azure Portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz istniejący serwer Azure Database for MariaDB, który ma być używany jako serwer źródłowy. Ta akcja powoduje otwarcie strony **Przegląd** .
1. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.
1. Wybierz pozycję **Dodaj replikę**.
1. Wprowadź nazwę serwera repliki.
1. Wybierz lokalizację serwera repliki. Lokalizacja domyślna jest taka sama jak w przypadku serwera głównego. Sprawdź, czy została wybrana lokalizacja docelowa, w której ma zostać wdrożona replika.
1. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki. Podczas tworzenia repliki dane są kopiowane z serwera źródłowego do repliki. Czas utworzenia może trwać kilka minut lub dłużej, proporcjonalnie do rozmiaru serwera źródłowego.

>[!NOTE]
> Podczas tworzenia repliki nie są dziedziczone punkty końcowe usługi sieci wirtualnej serwera głównego. Te reguły należy skonfigurować niezależnie dla repliki.

## <a name="move"></a>Move

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Zatrzymywanie replikacji na serwerze repliki powoduje, że staje się ona serwerem autonomicznym. Aby zatrzymać replikację do repliki z Azure Portal, wykonaj następujące czynności:

1. Po utworzeniu repliki Znajdź i wybierz serwer źródłowy Azure Database for MariaDB. 
1. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.
1. Wybierz serwer repliki.
1. Wybierz pozycję **Zatrzymaj replikację**.
1. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

## <a name="clean-up-source-server"></a>Oczyść serwer źródłowy

Możesz chcieć usunąć źródłowy serwer Azure Database for MariaDB. Aby to zrobić, wykonaj następujące kroki:

1. Po utworzeniu repliki Znajdź i wybierz serwer źródłowy Azure Database for MariaDB.
1. W oknie **Przegląd** wybierz pozycję **Usuń**.
1. Wpisz nazwę serwera źródłowego, aby potwierdzić, że chcesz usunąć.
1. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono serwer Azure Database for MariaDB z jednego regionu do innego przy użyciu Azure Portal a następnie oczyszczono niepotrzebne zasoby źródłowe. 

- Dowiedz się więcej na temat [odczytu replik](concepts-read-replicas.md)
- Dowiedz się więcej o [zarządzaniu odczytanymi replikami w Azure Portal](howto-read-replicas-portal.md)
- Dowiedz się więcej o opcjach [ciągłości biznesowej](concepts-business-continuity.md)
