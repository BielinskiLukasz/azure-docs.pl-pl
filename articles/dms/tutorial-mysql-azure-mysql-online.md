---
title: Używanie usługi Azure Database Migration Service do przeprowadzania migracji programu MySQL do usługi Azure Database for MySQL w trybie online | Microsoft Docs
description: Dowiedz się, w jaki sposób przeprowadzić migrację w trybie online z lokalnego programu MySQL do usługi Azure Database for MySQL za pomocą usługi Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 08/27/2018
ms.openlocfilehash: 88cd390e37273c95304dab5ba3153e8a63270ab1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042690"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Migrowanie programu MySQL do usługi Azure Database for MySQL w trybie online przy użyciu usługi DMS
Usługa Azure Database Migration Service służy do migrowania baz danych z lokalnego wystąpienia programu MySQL do usługi [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) z minimalnym przestojem. Innymi słowy migrację można przeprowadzić przy minimalnych przestojach w działaniu aplikacji. W tym samouczku przeprowadzisz migrację przykładowej bazy danych **Employees** z lokalnego wystąpienia programu MySQL 5.7 do usługi Azure Database for MySQL za pomocą działania migracji w trybie online w usłudze Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Migrowanie przykładowego schematu za pomocą narzędzia mysqldump.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitorowanie migracji.

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:

- Pobrany i zainstalowany program [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) w wersji 5.6 lub 5.7. Wersja lokalnego programu MySQL musi być zgodna z wersją usługi Azure Database for MySQL. Na przykład program MySQL 5.6 może być migrowany tylko do usługi Azure Database for MySQL 5.6, a nie do nowszej wersji 5.7.
- [Utwórz wystąpienie usługi Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Zapoznaj się z artykułem [Nawiązywanie połączeń z danymi i wykonywanie na nich zapytań przy użyciu środowiska roboczego bazy danych MySQL](https://docs.microsoft.com/azure/mysql/connect-workbench), aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych przy użyciu witryny Azure Portal.  
- Sieć wirtualna dla usługi Azure Database Migration Service utworzona przy użyciu modelu wdrożenia usługi Azure Resource Manager, która zapewnia łączność między lokacjami dla lokalnych serwerów źródłowych, z użyciem usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub sieci [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Sprawdzenie, czy reguły sieciowej grupy zabezpieczeń sieci wirtualnej Azure Virtual Network nie blokują następujących portów komunikacji: 443, 53, 9354, 445 i 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń usługi Azure VNET, zapoznaj się z artykułem [Filter network traffic with network security groups (Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz zaporę systemu Windows, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowego serwera MySQL Server, czyli domyślnie ustawionego portu TCP 3306.
- W przypadku korzystania z urządzenia zapory przed źródłową bazą danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowej bazy danych podczas migracji.
- Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla usługi Azure Database for MySQL, aby umożliwić usłudze Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi Azure Database Migration Service.
- Źródłowy serwer MySQL musi znajdować się w obsługiwanej bazie danych MySQL Community Edition. Aby określić wersję instancji programu MySQL, w narzędziu MySQL lub środowisku roboczym bazy danych MySQL uruchom następujące polecenie:
    ```
    SELECT @@version;
    ```
- Usługa Azure Database for MySQL obsługuje wyłącznie tabele InnoDB. Aby przekonwertować tabele MyISAM na format InnoDB, zapoznaj się z artykułem [Konwertowanie tabel z formatu MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) 

- Włącz rejestrowanie binarne w pliku my.ini (Windows) lub my.cnf (Unix) źródłowej bazy danych przy użyciu następującej konfiguracji:
- Użytkownik musi mieć rolę ReplicationAdmin z następującymi uprawnieniami:
    - **KLIENT REPLIKACJI** — wymagane tylko do zadań przetwarzania zmian. Inaczej mówiąc, zadania z tylko pełnym obciążeniem nie wymagają tego uprawnienia.
    - **REPLIKA REPLIKACJI** — wymagane tylko do zadań przetwarzania zmian. Inaczej mówiąc, zadania z tylko pełnym obciążeniem nie wymagają tego uprawnienia.
    - **SUPER** — wymagane tylko w wersjach wcześniejszych niż MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrowanie przykładowego schematu
Aby utworzyć wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy oraz procedury składowane, należy wyodrębnić schemat ze źródłowej bazy danych i zastosować go w bazie danych. Aby wyodrębnić schemat, użyj polecenia mysqldump z parametrem --no-data.
 
Zakładając, że lokalne środowisko zawiera przykładową bazę danych MySQL pracowników, polecenie mysqldump umożliwiające przeprowadzenie migracji schematu wygląda następująco:
```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```
Na przykład:
```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Aby zaimportować schemat do docelowej usługi Azure Database for MySQL, uruchom następujące polecenie:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Na przykład:
```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem.  Uruchom następujący skrypt w środowisku roboczym bazy danych MySQL w celu wyodrębnienia skryptu docelowego klucza obcego, a następnie dodaj skrypt klucza obcego.
```
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,    
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = ['SchemaName') Queries
  GROUP BY SchemaName;
 ```
        
Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania w celu usunięcia klucza obcego.

Jeśli dane zawierają wyzwalacz (wyzwalacz wstawienia lub aktualizacji), spowoduje to wymuszenie integralności danych w miejscu docelowym wcześniej replikowanych danych ze źródła. W czasie trwania migracji zaleca się wyłączenie wyzwalaczy we wszystkich tabelach w docelowej lokalizacji. Po jej zakończeniu wyzwalacze należy ponownie włączyć.

Aby wyłączyć wyzwalacze w docelowej bazie danych, użyj następującego polecenia:
```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration
1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.
 
   ![Wyświetlanie subskrypcji w portalu](media\tutorial-mysql-to-azure-mysql-online\portal-select-subscriptions.png)
       
2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.
 
    ![Wyświetlanie dostawców zasobów](media\tutorial-mysql-to-azure-mysql-online\portal-select-resource-provider.png)
    
3.  Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.
 
    ![Rejestrowanie dostawcy zasobów](media\tutorial-mysql-to-azure-mysql-online\portal-register-resource-provider.png)    

## <a name="create-a-dms-instance"></a>Tworzenie wystąpienia usługi DMS
1.  W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media\tutorial-mysql-to-azure-mysql-online\portal-marketplace.png)

2.  Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.
 
    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media\tutorial-mysql-to-azure-mysql-online\dms-create1.png)
  
3.  Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usłudze Azure Database Migration Service dostęp do źródłowego wystąpienia programu SQL Server i docelowego wystąpienia usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://aka.ms/DMSVnet).

5. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    Jeśli potrzebujesz pomocy podczas wybierania właściwej warstwy usługi Azure Database Migration Service, zapoznaj się z zaleceniami we wpisie na blogu [Choosing an Azure Database Migration Service (Azure DMS) tier (Wybieranie właściwej warstwy usługi Azure Database Migration Service [Azure DMS])](https://go.microsoft.com/fwlink/?linkid=861067). 

     ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media\tutorial-mysql-to-azure-mysql-online\dms-settings3.png)

7.  Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji
Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.
 
      ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media\tutorial-mysql-to-azure-mysql-online\dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia usługi Azure Database Migration Service, a następnie wybierz to wystąpienie.
 
     ![Znajdowanie wystąpienia usługi Azure Database Migration Service](media\tutorial-mysql-to-azure-mysql-online\dms-instance-search.png)
 
3. Wybierz pozycję + **Nowy projekt migracji**.
4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MySQL**, a w polu tekstowym **Typ serwera docelowego** wybierz pozycję **AzureDbForMySQL**.
5. W sekcji **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

    ![Tworzenie projektu usługi Database Migration Service](media\tutorial-mysql-to-azure-mysql-online\dms-create-project4.png)

    > [!NOTE]
    > Możesz też wybrać opcję **Utwórz tylko projekt**, aby utworzyć projekt migracji teraz i wykonać migrację później.

6. Wybierz pozycję **Zapisz**, pamiętając o uwzględnieniu wymagań niezbędnych do prawidłowego korzystania z usługi DMS na potrzeby migracji danych, a następnie wybierz pozycję **Utwórz i uruchom działanie**.

## <a name="specify-source-details"></a>Określanie szczegółów źródła
1. Na ekranie **Dodawanie szczegółów źródła** określ szczegóły połączenia dla źródłowego wystąpienia programu MySQL.
 
    ![Ekran Dodawanie szczegółów źródła](media\tutorial-mysql-to-azure-mysql-online\dms-add-source-details.png)   

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego
1. Wybierz polecenie **Zapisz**, a następnie na ekranie **Szczegóły elementu docelowego** określ szczegóły połączenia dla docelowego serwera usługi Azure Database for MySQL, czyli dla wstępnie zaaprowizowanego wystąpienia bazy danych Azure Database for MySQL, w której wdrożono schemat **Employees** za pomocą narzędzia mysqldump.

    ![Ekran Szczegóły elementu docelowego](media\tutorial-mysql-to-azure-mysql-online\dms-add-target-details.png)

2. Wybierz polecenie **Zapisz**, a następnie na ekranie **Mapuj do docelowych baz danych**, określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych, co źródłowa baza danych, usługa Azure Database Migration Service domyślnie wybierze docelową bazę danych.

    ![Mapowanie do docelowych baz danych](media\tutorial-mysql-to-azure-mysql-online\dms-map-target-details.png)

3.  Wybierz pozycję **Zapisz**. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji, a następnie sprawdź podsumowanie, aby upewnić się, że szczegółowe informacje źródłowe i docelowe pasują do wcześniej podanych.

    ![Podsumowanie migracji](media\tutorial-mysql-to-azure-mysql-online\dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji
- Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **Stan** działania będzie określony jako **Inicjowanie**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji
1. Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

     ![Stan działania — ukończono](media\tutorial-mysql-to-azure-mysql-online\dms-activity-completed.png)

2. W obszarze **Nazwa bazy danych** wybierz określoną bazę danych, aby uzyskać stan migracji dla operacji **Pełne ładowanie danych** oraz **Przyrostowa synchronizacja danych**.

    Operacja Pełne ładowanie danych wyświetli stan migracji ładowania początkowego, natomiast operacja Przyrostowa synchronizacja danych wyświetli stan przechwytywania zmian danych (CDC).
   
     ![Stan działania — ukończono pełne ładowanie](media\tutorial-mysql-to-azure-mysql-online\dms-activity-full-load-completed.png)

     ![Stan działania — przyrostowa synchronizacja danych](media\tutorial-mysql-to-azure-mysql-online\dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Przeprowadzanie migracji jednorazowej
Po zakończeniu początkowego pełnego ładowania bazy danych są oznaczone jako **Gotowe do migracji jednorazowej**.

1. Gdy wszystko jest gotowe do zakończenia migracji bazy danych, wybierz pozycję **Rozpocznij migrację jednorazową**.

    ![Rozpoczynanie migracji jednorazowej](media\tutorial-mysql-to-azure-mysql-online\dms-start-cutover.png)
 
2.  Upewnij się, że zatrzymano wszystkie transakcje przychodzące do źródłowej bazy danych. Poczekaj, aż licznik **Zmiany oczekujące** będzie pokazywać wartość **0**.
3.  Wybierz pozycję **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.
4. Gdy stan migracji bazy danych będzie wyświetlać wartość **Ukończono**, połącz aplikacje z nową docelową usługą Azure SQL Database.
 
## <a name="next-steps"></a>Następne kroki
- Aby uzyskać informacje o znanych problemach i ograniczeniach związanych z wykonywaniem migracji w trybie online do usługi Azure Database for MySQL, zobacz artykuł [Znane problemy i obejścia problemów związanych z migracjami online usługi Azure Database for MySQL](known-issues-azure-mysql-online.md).
- Aby uzyskać informacje o usłudze Azure Database Migration Service, zobacz artykuł [Czym jest usługa Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Aby uzyskać informacje o usłudze Azure Database for MySQL, zobacz artykuł [Czym jest usługa Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).