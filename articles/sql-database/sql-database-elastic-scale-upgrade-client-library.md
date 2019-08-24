---
title: Uaktualnianie do najnowszej biblioteki klienta Elastic Database | Microsoft Docs
description: Użyj NuGet, aby uaktualnić bibliotekę klienta Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 286fa60fef8de5240fb2ccd0f14ced0c4e38ff73
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981369"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Uaktualnij aplikację, aby użyć najnowszej biblioteki klienta Elastic Database

Nowe wersje [biblioteki klienta Elastic Database](sql-database-elastic-database-client-library.md) są dostępne za pomocą narzędzia NuGet i interfejsu Menedżera pakietów NuGet w programie Visual Studio. Uaktualnienia zawierają poprawki błędów i obsługują nowe możliwości biblioteki klienta.

**Najnowsza wersja:** Przejdź do [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Skompiluj ponownie aplikację przy użyciu nowej biblioteki, a także zmień istniejące metadane Menedżera mapy fragmentu przechowywane w bazach danych Azure SQL w celu obsługi nowych funkcji.

Wykonanie tych kroków w celu zapewnienia, że stare wersje biblioteki klienta nie będą już obecne w Twoim środowisku, gdy zostaną zaktualizowane obiekty metadanych, co oznacza, że po uaktualnieniu nie zostaną utworzone obiekty metadanych starej wersji.

## <a name="upgrade-steps"></a>Kroki uaktualniania

**1. Uaktualnij aplikacje.** W programie Visual Studio Pobierz najnowszą wersję biblioteki klienta i odwołuje się do wszystkich projektów programistycznych korzystających z biblioteki; następnie Skompiluj i Wdróż.

* W rozwiązaniu programu Visual Studio wybierz kolejno pozycje **Narzędzia** -->  -->  **Menedżer pakietów NuGet**zarządzanie pakietami**NuGet dla rozwiązania**.
* (Visual Studio 2013) W lewym panelu wybierz pozycję **aktualizacje**, a następnie wybierz przycisk **aktualizuj** w pakiecie **Azure SQL Database biblioteki klienta elastycznej skalowania** , która jest wyświetlana w oknie.
* (Visual Studio 2015) Ustaw pole filtru na **dostępne do uaktualnienia**. Wybierz pakiet do zaktualizowania, a następnie kliknij przycisk **Aktualizuj** .
* (Visual Studio 2017) W górnej części okna dialogowego wybierz pozycję **aktualizacje**. Wybierz pakiet do zaktualizowania, a następnie kliknij przycisk **Aktualizuj** .
* Kompiluj i wdrażaj.

**2. Uaktualnij skrypty.** Jeśli używasz skryptów **programu PowerShell** do zarządzania usługą fragmentów, [Pobierz nową wersję biblioteki](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) i skopiuj ją do katalogu, z którego wykonujesz skrypty.

**3. Uaktualnij usługę Split-Merge.** W przypadku używania narzędzia do dzielenia i scalania baz danych w celu reorganizowania danych podzielonej na fragmenty należy [pobrać i wdrożyć najnowszą wersję narzędzia](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Szczegółowe kroki uaktualniania usługi można znaleźć [tutaj](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Uaktualnij bazy danych**Menedżera map fragmentu. Uaktualnij metadane obsługujące mapy fragmentu w Azure SQL Database.  Można to zrobić na dwa sposoby przy użyciu programu PowerShell lub C#. Poniżej przedstawiono obie opcje.

***Opcja 1: Uaktualnianie metadanych przy użyciu programu PowerShell***

1. Pobierz najnowsze narzędzie wiersza polecenia dla programu NuGet z tego [miejsca](https://nuget.org/nuget.exe) i Zapisz je w folderze.
2. Otwórz wiersz polecenia, przejdź do tego samego folderu i wydaj polecenie:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Przejdź do podfolderu zawierającego nową wersję biblioteki DLL klienta, na przykład:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Pobierz skrypt uaktualnienia Elastic Database Client z [Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)i Zapisz go w tym samym folderze, w którym znajduje się biblioteka DLL.
5. W tym folderze Uruchom polecenie "PowerShell .\upgrade.ps1" w wierszu polecenia i postępuj zgodnie z monitami.

***Opcja 2: Uaktualnij metadane przy użyciuC#***

Alternatywnie możesz utworzyć aplikację programu Visual Studio, która otwiera ShardMapManager, iteruje wszystkie fragmentów i wykonuje uaktualnienie metadanych, wywołując metody [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) i [UpgradeGlobalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) , jak w poniższym przykładzie:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Metody uaktualniania metadanych można stosować wiele razy bez szkody. Na przykład jeśli Starsza wersja klienta przypadkowo tworzy fragmentu po aktualizacji, można uruchomić uaktualnienie ponownie dla wszystkich fragmentów, aby upewnić się, że Najnowsza wersja metadanych jest obecna w całej infrastrukturze.

**Uwaga:**  Nowe wersje opublikowanej biblioteki klienckiej nadal pracują z wcześniejszymi wersjami metadanych Menedżera mapy fragmentu w usłudze Azure SQL DB i na odwrót.   Jednak aby korzystać z niektórych nowych funkcji w najnowszym kliencie, należy uaktualnić metadane.   Należy pamiętać, że uaktualnienia metadanych nie wpłyną na dane użytkownika lub dane specyficzne dla aplikacji, tylko obiekty utworzone i używane przez Menedżera mapy fragmentu.  Aplikacje i nadal działają przez sekwencję uaktualniania opisaną powyżej.

## <a name="elastic-database-client-version-history"></a>Historia wersji klienta Elastic Database

W obszarze historia wersji przejdź do pozycji [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
