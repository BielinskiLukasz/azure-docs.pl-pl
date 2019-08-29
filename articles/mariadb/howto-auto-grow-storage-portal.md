---
title: Powiększ magazyn w Azure Database for MariaDB przy użyciu Azure Portal
description: W tym artykule opisano sposób włączania magazynu z możliwością autowzrostu dla Azure Database for MariaDB przy użyciu Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9d7b126b5c07cc8661cf50b4c4e13f78592b118e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70136694"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Powiększ magazyn w Azure Database for MariaDB przy użyciu Azure Portal
W tym artykule opisano, jak można skonfigurować magazyn serwera Azure Database for MariaDB do wzrostu bez wpływu na obciążenie.

Gdy serwer osiągnie przydzieloną liczbę magazynów, serwer jest oznaczony jako tylko do odczytu. Jeśli jednak włączysz funkcję autozwiększania magazynu, magazyn serwera zwiększy się, aby uwzględnić rosnące dane. W przypadku serwerów o rozmiarze mniejszym niż 100 GB zainicjowany magazyn jest zwiększany o 5 GB, gdy tylko ilość wolnego miejsca w magazynie jest mniejsza niż 1 GB lub 10% zainicjowanego magazynu. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca w magazynie jest mniejsza od 5% rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone w [tym miejscu](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) .

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:
- [Serwer Azure Database for MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Włącz funkcję autowzrostu magazynu 

Wykonaj następujące kroki, aby ustawić funkcję autowzrostu magazynu serwera MariaDB:

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MariaDB.

2. Na stronie serwer MariaDB, w polu Nagłówek **ustawień** kliknij pozycję **warstwa cenowa** , aby otworzyć stronę warstwa cenowa.

3. W sekcji autoprzyrost wybierz pozycję **tak** , aby włączyć funkcję automatycznego zwiększania rozmiaru magazynu.

    ![Azure Database for MariaDB-Settings_Pricing_tier — autowzrostu](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknij przycisk **OK** , aby zapisać zmiany.

5. Powiadomienie zostanie potwierdzone, że funkcja autozwiększania została pomyślnie włączona.

    ![Azure Database for MariaDB — powodzenie automatycznego wzrostu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tworzenia alertów dotyczących metryk](howto-alert-metric.md).
