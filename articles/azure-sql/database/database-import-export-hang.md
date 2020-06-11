---
title: Importowanie i eksportowanie bazy danych zajmuje dużo czasu
description: Importowanie lub eksportowanie bazy danych usługi Azure SQL instance Managed/Export nie potrwa dużo czasu Azure SQL Database
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 15b27990842da8837e6cd8fc8d29f361961ee044
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669549"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Importowanie lub eksportowanie bazy danych za pomocą usługi importu/eksportu wystąpienia zarządzanego Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W przypadku korzystania z usługi Import/Export proces może trwać dłużej niż oczekiwano. W tym artykule opisano potencjalne przyczyny tego opóźnienia i alternatywne metody obejścia.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database usługi Import/Export

Usługa Azure SQL Database Import/Export to usługa sieci Web oparta na protokole REST, która działa w każdym centrum danych platformy Azure. Ta usługa jest wywoływana w przypadku używania opcji [Importuj bazę danych](database-import.md#using-azure-portal) lub [Eksportuj](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) , aby przenieść bazę danych SQL do Azure Portal. Usługa udostępnia bezpłatne usługi kolejkowania żądań i usług obliczeniowych w celu przeprowadzania importu i eksportu między Azure SQL Database i magazynem obiektów blob platformy Azure.

Operacje importu i eksportu nie reprezentują tradycyjnej fizycznej kopii zapasowej bazy danych, ale zamiast logicznej kopii zapasowej bazy danych, która używa specjalnego formatu BACPAC. Format BACPAC pozwala uniknąć konieczności używania fizycznego formatu, który może się różnić między wersjami Microsoft SQL Server i Azure SQL Database. Z tego względu można użyć go do bezpiecznego przywrócenia bazy danych do bazy danych SQL Server i bazy danych SQL.

## <a name="what-causes-delays-in-the-process"></a>Co powoduje opóźnienia w procesie?

Usługa Import/Export Azure SQL Database udostępnia ograniczoną liczbę maszyn wirtualnych obliczeniowych na region w celu przetwarzania operacji importowania i eksportowania. Maszyny wirtualne obliczeń są hostowane na region, aby upewnić się, że import lub eksport zapobiega opóźnieniu przepustowości i opłatom między regionami. Jeśli zbyt wiele żądań jest wykonywanych w tym samym czasie w tym samym regionie, podczas przetwarzania operacji mogą wystąpić znaczne opóźnienia. Czas wymagany do ukończenia żądań może się różnić od kilku sekund do wielu godzin.

> [!NOTE]
> Jeśli żądanie nie zostanie przetworzone w ciągu czterech dni, usługa automatycznie anuluje żądanie.

## <a name="recommended-solutions"></a>Zalecane rozwiązania

Jeśli eksporty bazy danych są używane tylko do odzyskiwania po przypadkowym usunięciu danych, wszystkie wersje Azure SQL Database zapewniają możliwość przywracania samoobsługowego z kopii zapasowych generowanych przez system. Ale jeśli potrzebne są te eksporty z innych powodów, a w przypadku konieczności zapewnienia ciągłej szybszej lub bardziej przewidywalnej wydajności importu/eksportu należy wziąć pod uwagę następujące opcje:

* [Wyeksportuj do pliku BACPAC przy użyciu narzędzia sqlpackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Eksportuj do pliku BACPAC przy użyciu programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Uruchom BACPAC zaimportuj lub wyeksportuj bezpośrednio w kodzie przy użyciu interfejsu API Microsoft SQL Server Data-Tier Application Framework (DacFx). Aby uzyskać dodatkowe informacje, zobacz:
  * [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Przestrzeń nazw Microsoft. SqlServer. DAC](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Pobierz DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Zagadnienia, które należy wziąć pod uwagę podczas eksportowania lub importowania bazy danych Azure SQL Database

* Wszystkie metody omówione w tym artykule korzystają z limitu przydziału jednostki transakcji bazy danych (DTU), co powoduje ograniczenie przepustowości przez usługę Azure SQL Database. Można [wyświetlić statystyki jednostek DTU dla bazy danych na Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Jeśli baza danych osiągnęła limity zasobów, [Uaktualnij warstwę usług](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) , aby dodać więcej zasobów.
* Najlepiej uruchamiać aplikacje klienckie (takie jak narzędzie sqlpackage lub niestandardowa aplikacja DAC) z poziomu maszyny wirtualnej w tym samym regionie, w którym znajduje się baza danych SQL. W przeciwnym razie mogą wystąpić problemy z wydajnością związane z opóźnieniem sieci.
* Eksportowanie dużych tabel bez indeksów klastrowanych może być bardzo wolne lub nawet przyczyną błędu. Takie zachowanie występuje, ponieważ nie można jednocześnie podzielić i wyeksportować tabeli. Zamiast tego należy go wyeksportować w jednej transakcji, co powoduje spowolnienie wydajności i potencjalne błędy podczas eksportowania, szczególnie w przypadku dużych tabel.


## <a name="related-documents"></a>Powiązane dokumenty

[Zagadnienia dotyczące eksportowania bazy danych Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
