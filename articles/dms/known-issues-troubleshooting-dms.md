---
title: Artykuł dotyczący znanych problemów dotyczących rozwiązywania typowych problemów/błędów związanych z używaniem Azure Database Migration Service | Microsoft Docs
description: Dowiedz się, jak rozwiązywać typowe znane problemy i błędy związane z używaniem Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: e33f195ea821b34147c748e9c0aa64cb63b58fdc
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249980"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Rozwiązywanie typowych problemów i błędów Azure Database Migration Service

W tym artykule opisano niektóre typowe problemy i błędy, które mogą występować Azure Database Migration Service użytkownicy. Artykuł zawiera również informacje dotyczące sposobu rozwiązywania tych problemów i błędów.

## <a name="migration-activity-in-queued-state"></a>Działanie migracji w stanie w kolejce

W przypadku tworzenia nowych działań w projekcie Azure Database Migration Service działania pozostają w stanie w kolejce.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie Azure Database Migration Service osiągnie maksymalną pojemność dla bieżących zadań, które jednocześnie są uruchamiane. Wszelkie nowe działania są umieszczane w kolejce do momentu udostępnienia pojemności. | Sprawdź, czy wystąpienie usługi migracji danych ma uruchomione działania między projektami. Można nadal tworzyć nowe działania, które zostaną automatycznie dodane do kolejki w celu wykonania. Gdy tylko wszystkie istniejące działania są zakończone, następne działanie w kolejce zacznie działać i stan zmieni się na uruchomiony automatycznie. Nie musisz podejmować żadnych dodatkowych czynności, aby rozpocząć migrację działania z kolejki.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maksymalna liczba baz danych wybranych do migracji

Wystąpił następujący błąd podczas tworzenia działania dla projektu migracji bazy danych na potrzeby przeniesienia do Azure SQL Database lub wystąpienia zarządzanego Azure SQL Database:

* **Błąd**: Błąd walidacji ustawień migracji "," errorDetail ": dla migracji wybrano" więcej niż maksymalną liczbę obiektów "Databases" dla "Database". "

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany, gdy wybrano więcej niż cztery bazy danych dla jednego działania migracji. W danej chwili każde działanie migracji jest ograniczone do czterech baz danych. | Wybierz cztery lub mniej baz danych na działanie migracji. Jeśli musisz migrować więcej niż cztery bazy danych równolegle, zainicjuj inne wystąpienie Azure Database Migration Service. Obecnie Każda subskrypcja obsługuje do dwóch wystąpień Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Błędy migracji MySQL do usługi Azure MySQL z błędami odzyskiwania

W przypadku migrowania z programu MySQL do Azure Database for MySQL przy użyciu Azure Database Migration Service działanie migracji kończy się niepowodzeniem z powodu następującego błędu:

* **Błąd**: Błąd migracji bazy danych — zadanie "TaskID" zostało wstrzymane z powodu [n] kolejnych błędów odzyskiwania.

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd może wystąpić, gdy użytkownik wykonujący migrację nie ma ReplicationAdmin roli i/lub uprawnień klienta replikacji, repliki replikacji i SUPER (wersje wcześniejsze niż MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Upewnij się, że [wstępnie wymagane uprawnienia](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) dla konta użytkownika są skonfigurowane prawidłowo w wystąpieniu Azure Database for MySQL. Na przykład następujące kroki można wykonać, aby utworzyć użytkownika o nazwie "MigrateUser" z wymaganymi uprawnieniami:<br>1. Utwórz użytkownika MigrateUser @ "%" IDENTYFIKOWANego przez element "Secret"; <br>2. Przyznaj wszystkim uprawnienia do db_name. * do "MigrateUser" @ "%" identyfikowanego przez "Secret"; Powtórz ten krok, aby przyznać dostęp do większej liczby baz danych <br>3. Przyznaj replikację podrzędną *.* na element "MigrateUser" @ "%" identyfikowany przez element "Secret";<br>4. Udziel klienta replikacji na komputerze *.* na element "MigrateUser" @ "%" identyfikowany przez element "Secret";<br>5. Uprawnienia opróżniania; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Wystąpił błąd podczas próby zatrzymania Azure Database Migration Service

Podczas zatrzymywania wystąpienia Azure Database Migration Service pojawia się następujący błąd:

* **Błąd**: Nie można zatrzymać usługi. Błąd: {"Error": {"Code": "InvalidRequest", "Message": "co najmniej jedno działanie jest obecnie uruchomione. Aby zatrzymać usługę, poczekaj na zakończenie działania lub Zatrzymaj te działania ręcznie i spróbuj ponownie. "}}

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany, gdy wystąpienie usługi, które ma zostać zatrzymane, obejmuje działania, które są nadal uruchomione lub obecne w projektach migracji. <br><br><br><br><br><br> | Upewnij się, że nie ma żadnych działań uruchomionych w wystąpieniu Azure Database Migration Service, które próbujesz zatrzymać. Przed podjęciem próby zatrzymania usługi można również usunąć działania lub projekty. Poniższe kroki ilustrują, jak usunąć projekty w celu oczyszczenia wystąpienia usługi migracji przez usunięcie wszystkich uruchomionych zadań:<br>1. Install-module-name AzureRM. datamigration <br>2. Login-AzureRmAccount <br>3. SELECT-AzureRmSubscription-subscriptionname "\<SubName >" <br> 4. Remove-AzureRmDataMigrationProject-Name \<projectName >-ResourceGroupName \<rgName >-ServiceName \<ServiceName >-DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Wystąpił błąd podczas próby uruchomienia Azure Database Migration Service

Podczas uruchamiania wystąpienia Azure Database Migration Service pojawia się następujący błąd:

* **Błąd**: Uruchamianie usługi nie powiodło się. Błąd: {"errorDetail": "nie można uruchomić usługi, skontaktuj się z pomocą techniczną firmy Microsoft".

| Przyczyna         | Rozwiązanie |
| ------------- | ------------- |
| Ten błąd jest wyświetlany, gdy poprzednie wystąpienie nie powiodło się wewnętrznie. Ten błąd występuje rzadko, a zespół inżynieryjny zna go. <br> | Usuń wystąpienie usługi, której nie można uruchomić, a następnie Udostępnij nowe, aby je zastąpić. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Wystąpił błąd podczas przywracania bazy danych programu SQL Server do wystąpienia zarządzanego usługi Azure SQL DB

Podczas przeprowadzania migracji w trybie online z SQL Server do wystąpienia zarządzanego Azure SQL Database uruchomienie produkcyjne kończy się niepowodzeniem z powodu następującego błędu:

* **Błąd**: Operacja przywracania nie powiodła się dla identyfikatora operacji "operationId". Kod "AuthorizationFailed", komunikat "klient" clientId "o identyfikatorze obiektu" objectId ", nie ma autoryzacji do wykonania akcji" Microsoft. SQL/Locations/managedDatabaseRestoreAzureAsyncOperation/Read "w zakresie/subscriptions/ Identyfikator subskrypcji ".".

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten błąd wskazuje, że podmiot zabezpieczeń aplikacji używany do migracji w trybie online z SQL Server do wystąpienia zarządzanego Azure SQL Database nie ma uprawnienia Współtworzenie w ramach subskrypcji. Niektóre wywołania interfejsu API z wystąpieniem zarządzanym w obecnym momencie wymagają tego uprawnienia do subskrypcji dla operacji przywracania. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Użyj polecenia cmdlet `-ObjectId` `Get-AzureADServicePrincipal` programu PowerShell z opcją dostępne w komunikacie o błędzie, aby wyświetlić nazwę wyświetlaną używanego identyfikatora aplikacji.<br><br> Sprawdź poprawność uprawnień do tej aplikacji i upewnij się, że ma ona [rolę współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) na poziomie subskrypcji. <br><br> Zespół inżynierów Azure Database Migration Service pracuje nad ograniczeniem dostępu wymaganego przez bieżącą rolę programu w ramach subskrypcji. Jeśli masz wymóg biznesowy, który nie zezwala na korzystanie z roli programu, skontaktuj się z pomocą techniczną platformy Azure w celu uzyskania dodatkowej pomocy. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Wystąpił błąd podczas usuwania karty sieciowej skojarzonej z Azure Database Migration Service

Podczas próby usunięcia karty interfejsu sieciowego skojarzonej z Azure Database Migration Service próba usunięcia nie powiedzie się z powodu tego błędu:

* **Błąd**: Nie można usunąć karty sieciowej skojarzonej z Azure Database Migration Service z powodu usługi DMS korzystającej z karty sieciowej

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie Azure Database Migration Service nadal może być obecne i zużywać kartę sieciową. <br><br><br><br><br><br><br><br> | Aby usunąć tę kartę sieciową, Usuń wystąpienie usługi DMS, które automatycznie usunie kartę sieciową używaną przez usługę.<br><br> **Ważne**: Upewnij się, że usuwane wystąpienie Azure Database Migration Service nie ma uruchomionych działań.<br><br> Po usunięciu wszystkich projektów i działań skojarzonych z wystąpieniem Azure Database Migration Service można usunąć wystąpienie usługi. Karta sieciowa używana przez wystąpienie usługi jest automatycznie czyszczona w ramach usunięcia usługi. |

## <a name="connection-error-when-using-expressroute"></a>Błąd połączenia podczas korzystania z ExpressRoute

Podczas próby nawiązania połączenia ze źródłem w kreatorze projektu usługi Azure Database Migration Service, połączenie nie powiedzie się po dłuższym czasie oczekiwania, jeśli źródło używa usługi ExpressRoute dla połączeń.

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| W przypadku korzystania z [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [wymaga](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) obsługi trzech punktów końcowych usługi w podsieci Virtual Network skojarzonej z usługą:<br> --Service Bus punkt końcowy<br> --Punkt końcowy magazynu<br> --Docelowy punkt końcowy bazy danych (np. punkt końcowy SQL, Cosmos DB punkt końcowy)<br><br><br><br><br> | [Włącz](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) wymagane punkty końcowe usługi dla łączności ExpressRoute między źródłem a Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Błąd przekroczenia limitu czasu podczas migrowania bazy danych MySQL do usługi Azure MySQL

Podczas migrowania bazy danych MySQL do wystąpienia Azure Database for MySQL za pośrednictwem Azure Database Migration Service migracja kończy się niepowodzeniem z powodu następującego błędu limitu czasu:

* **Błąd**: Błąd migracji bazy danych — nie można załadować pliku — nie można uruchomić procesu ładowania dla pliku "n" RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 komunikat: [MySQL] [ODBC Driver] [MySQL] Przekroczono limit czasu oczekiwania na blokadę; Spróbuj ponownie uruchomić transakcję

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten błąd występuje, gdy migracja nie powiedzie się z powodu przekroczenia limitu czasu oczekiwania blokady podczas migracji. | Rozważ zwiększenie wartości parametru serwera **"innodb_lock_wait_timeout"** . Najwyższa dozwolona wartość to 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Błąd podczas nawiązywania połączenia ze źródłem SQL Server przy użyciu portu dynamicznego lub nazwanego wystąpienia

Podczas próby nawiązania połączenia Azure Database Migration Service ze źródłem SQL Server, które jest uruchamiane w nazwanym wystąpieniu lub na porcie dynamicznym, połączenie kończy się niepowodzeniem z powodu tego błędu:

* **Błąd**: nie można nawiązać połączenia z programem SQL Server. Podczas nawiązywania połączenia z serwerem SQL wystąpił błąd dotyczący sieci lub wystąpienia. Serwer nie został znaleziony lub był niedostępny. Sprawdź, czy nazwa wystąpienia jest poprawna i czy SQL Server jest skonfigurowany do zezwalania na połączenia zdalne. dostawcy Interfejsy sieciowe SQL, błąd: 26-błąd podczas lokalizowania określonego serwera/wystąpienia)

| Przyczyna         | Rozwiązanie    |
| ------------- | ------------- |
| Ten problem występuje, gdy wystąpienie źródła SQL Server, które Azure Database Migration Service próbuje nawiązać połączenie z portem dynamicznym lub używa nazwanego wystąpienia. Usługa SQL Server Browser nasłuchuje na porcie UDP 1434 dla połączeń przychodzących z nazwanym wystąpieniem lub przy użyciu portu dynamicznego. Port dynamiczny może ulec zmianie przy każdym ponownym uruchomieniu usługi SQL Server. Możesz sprawdzić port dynamiczny przypisany do wystąpienia za pośrednictwem konfiguracji sieci w SQL Server Configuration Manager.<br><br><br> |Sprawdź, czy Azure Database Migration Service może nawiązać połączenie z usługą SQL Server Browser źródłowej na porcie UDP 1434 i wystąpieniu SQL Server za pośrednictwem dynamicznie przypisanego portu TCP. |

## <a name="additional-known-issues"></a>Dodatkowe znane problemy

* [Znane problemy/ograniczenia migracji z migracją online do Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Znane problemy/ograniczenia migracji z migracją online do Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Znane problemy/ograniczenia migracji z migracją online do Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się z artykułem [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Zapoznaj się z artykułem [jak skonfigurować parametry serwera w Azure Database for MySQL przy użyciu Azure Portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Zapoznaj się z artykułem [Przegląd wymagań wstępnych dotyczących korzystania z Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zobacz [często zadawane pytania dotyczące korzystania z Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
