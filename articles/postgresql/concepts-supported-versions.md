---
title: Obsługiwane wersje w Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano obsługiwane wersje Azure Database for PostgreSQL-jednego serwera.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2a2b8b71e07e5dac74d73d3a81c150ac5d980ea2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935353"
---
# <a name="supported-postgresql-database-versions"></a>Obsługiwane wersje bazy danych PostgreSQL
Firma Microsoft dąży do obsługi wersji n-2 aparatu PostgreSQL w Azure Database for PostgreSQL-pojedynczym serwerze. Wersje byłyby w bieżącej wersji głównej na platformie Azure (n) i dwóch wcześniejszych wersjach głównych (-2).

Azure Database for PostgreSQL obecnie obsługuje następujące wersje główne:

## <a name="postgresql-version-11"></a>PostgreSQL, wersja 11
Bieżąca wersja pomocnicza to 11,5. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-5.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-10"></a>PostgreSQL wersja 10
Bieżąca wersja pomocnicza to 10,10. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-10.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-96"></a>PostgreSQL w wersji 9,6
Bieżąca wersja pomocnicza to 9.6.15. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-95"></a>PostgreSQL w wersji 9,5
Bieżąca wersja pomocnicza to 9.5.19. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="managing-upgrades"></a>Zarządzanie uaktualnieniami
Azure Database for PostgreSQL automatycznie zarządza uaktualnieniami wersji pomocniczych. 

Automatyczne uaktualnianie wersji głównej nie jest obsługiwane. Na przykład nie jest możliwe automatyczne uaktualnienie bazy danych PostgreSQL 9.5 do wersji 9.6. Aby przeprowadzić uaktualnienie do następnej wersji głównej, wykonaj [zrzut bazy danych i przywróć go](./howto-migrate-using-dump-and-restore.md) na serwer, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="version-syntax"></a>Składnia wersji
Przed PostgreSQL w wersji 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej. Na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ . Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej. Na przykład 10,0 do 10,1 jest uaktualnieniem wersji _pomocniczej_ . Wersja 10 do 11 to uaktualnienie wersji _głównej_ .

## <a name="next-steps"></a>Następne kroki
Informacje na temat obsługiwanych rozszerzeń PostgreSQL można znaleźć [w dokumencie rozszerzenia](concepts-extensions.md).
