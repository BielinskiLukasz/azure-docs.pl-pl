---
title: Obsługiwane wersje w Azure Database for MariaDB
description: Opisuje obsługiwane wersje w Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e5d1dbc8c212d4cdefb12fb740a454324d3adfa1
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962919"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Obsługiwane wersje serwera Azure Database for MariaDB

Azure Database for MariaDB został opracowany z [serwera MariaDB](https://downloads.mariadb.org/)"open source" przy użyciu aparatu InnoDB. 

MariaDB używa schematu nazewnictwa X. Y. Z. X jest wersją główną, Y jest wersją pomocniczą i Z jest wersją poprawki.

> [!NOTE]
> W usłudze brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient MySQL wyświetla wersję zestawu MariaDB w bramie, a nie rzeczywistą wersję działającą w wystąpieniu serwera MariaDB. Aby określić wersję wystąpienia serwera MariaDB, użyj `SELECT VERSION();` polecenia.

Azure Database for MariaDB obecnie obsługuje następującą wersję:

## <a name="mariadb-version-102"></a>MariaDB w wersji 10,2

Wersja poprawki: 10.2.25

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="mariadb-version-103"></a>MariaDB w wersji 10,3

Wersja poprawki: 10.3.16

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza uaktualnieniami aktualizacji poprawek. Na przykład 10.2.21 do 10.2.23.  

Obecnie wersje pomocnicze i główne nie są obsługiwane. Na przykład uaktualnienie z MariaDB 10,2 do MariaDB 10,3 nie jest obsługiwane. Jeśli chcesz przeprowadzić uaktualnienie z 10,2 do 10,3, zrób [zrzut i Przywróć](./howto-migrate-dump-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md).
