---
title: Porty powyżej 1433 dla SQL Database | Microsoft Docs
description: Połączenia klientów z ADO.NET do Azure SQL Database mogą ominąć serwer proxy i korzystać bezpośrednio z bazy danych przy użyciu portów innych niż 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: a39cfd1981041c807a91a08c198378d238f0846e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568911"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porty powyżej 1433 dla ADO.NET 4,5

W tym temacie opisano zachowanie Azure SQL Database połączenia dla klientów korzystających z ADO.NET 4,5 lub nowszej wersji.

> [!IMPORTANT]
> Informacje o architekturze łączności można znaleźć w temacie [Azure SQL Database Architecture (architektura łączności](sql-database-connectivity-architecture.md)).
>

## <a name="outside-vs-inside"></a>Poza programem a wewnątrz

W przypadku połączeń do Azure SQL Database musimy najpierw zażądać, czy program kliencki działa *poza* granicą chmury platformy Azure, czy *wewnątrz* niej. Podsekcje omawiają dwa typowe scenariusze.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Dział* Klient uruchomiony na komputerze stacjonarnym

Port 1433 jest jedynym portem, który musi być otwarty na komputerze stacjonarnym, który hostuje SQL Database aplikację kliencką.

### <a name="inside-client-runs-on-azure"></a>*Ramce* Klient uruchomiony na platformie Azure

Gdy klient działa w ramach granicy chmury platformy Azure, korzysta z tego, co możemy wywołać *bezpośrednią trasę* do współdziałania z serwerem SQL Database. Po ustanowieniu połączenia dalsze interakcje między klientem i bazą danych nie obejmują bramy Azure SQL Database.

Sekwencja jest następująca:

1. ADO.NET 4,5 (lub nowszy) inicjuje krótką interakcję z chmurą Azure i otrzymuje dynamicznie zidentyfikowany numer portu.

   * Dynamicznie zidentyfikowany numer portu znajduje się w zakresie 11000-11999.
2. ADO.NET następnie łączy się bezpośrednio z serwerem SQL Database, bez żadnego oprogramowania pośredniczącego między.
3. Zapytania są wysyłane bezpośrednio do bazy danych, a wyniki są zwracane bezpośrednio do klienta.

Upewnij się, że zakresy portów 11000-11999 na komputerze klienckim platformy Azure są pozostawione do interakcji z programem ADO.NET 4,5 z SQL Database.

* W szczególności porty w zakresie muszą być wolne od innych zablokowanych wychodzących bloków.
* Na maszynie wirtualnej platformy Azure **Zapora systemu Windows z zabezpieczeniami zaawansowanymi** kontroluje ustawienia portu.
  
  * Za pomocą [interfejsu użytkownika zapory](https://msdn.microsoft.com/library/cc646023.aspx) można dodać regułę określającą protokół **TCP** wraz z zakresem portów o składni podobnej do **11000-11999**.

## <a name="version-clarifications"></a>Wyjaśnienia dotyczące wersji

W tej części objaśniono monikery odwołujące się do wersji produktu. Zawiera również listę kilku par wersji między produktami.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4,0 obsługuje protokół TDS 7,3, ale nie 7,4.
* ADO.NET 4,5 i nowsze obsługuje protokół TDS 7,4.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 lub nowszy

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4,2 lub nowszy (JDBC 4,0 faktycznie obsługuje TDS 7,4, ale nie implementuje "przekierowania")

## <a name="related-links"></a>Linki pokrewne

* ADO.NET 4,6 został opublikowany 20 lipca 2015. Ogłoszenie w blogu z zespołu .NET jest dostępne [tutaj](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx).
* ADO.NET 4,5 został opublikowany 15 sierpnia 2012. Ogłoszenie w blogu z zespołu .NET jest dostępne [tutaj](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  * Wpis w blogu dotyczący ADO.NET 4.5.1 jest dostępny [tutaj](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® ODBC Driver 17 dla SQL Server® — Windows, Linux, & macOS https://www.microsoft.com/download/details.aspx?id=56567

* Nawiązywanie połączenia z usługą Azure SQL Database V12 za pomocą przekierowania https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lista wersji protokołu TDS](https://www.freetds.org/userguide/tdshistory.htm)
* [Przegląd SQL Database Development](sql-database-develop-overview.md)
* [Zapora Azure SQL Database](sql-database-firewall-configure.md)
* [Instrukcje: Konfigurowanie ustawień zapory w usłudze SQL Database](sql-database-configure-firewall-settings.md)


