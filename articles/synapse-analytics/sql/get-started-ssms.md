---
title: 'SSMS: łączenie i wykonywanie zapytań w Synapse SQL'
description: Użyj SQL Server Management Studio (SSMS), aby nawiązać połączenie z usługą SQL Synapse i wysyłać do niej zapytania Synapse.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: a25049aee620a1cf14eeb51adfb75f6577defc2a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197065"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Nawiązywanie połączenia z usługą Synapse SQL z programem SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Za pomocą [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) można nawiązać połączenie z usługą SQL Synapse i wysyłać do niej zapytania, korzystając z zasobów usługi SQL na żądanie (wersja zapoznawcza) lub w puli SQL. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Obsługiwane narzędzia dla SQL na żądanie (wersja zapoznawcza)

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) jest w pełni obsługiwana począwszy od wersji 1.18.0. Program SSMS jest częściowo obsługiwany począwszy od wersji 18,5, można go używać do nawiązywania połączeń i tylko zapytań.

> [!NOTE]
> Jeśli logowanie w usłudze AAD ma otwarte połączenie przez ponad 1 godzinę w czasie wykonywania zapytania, wszelkie zapytania, które opierają się na usłudze AAD, zakończą się niepowodzeniem. Obejmuje to wysyłanie zapytań do magazynu przy użyciu przekazywania usługi AAD i instrukcji, które współpracują z usługą AAD (na przykład CREATE EXTERNAL PROVIDER). Ma to wpływ na wszystkie narzędzia, które utrzymują otwarte połączenie, takie jak w edytorze zapytań w programie SSMS i ADS. Nie dotyczy narzędzi otwierających nowe połączenie z wykonywaniem zapytań, takich jak Synapse Studio.
> Aby rozwiązać ten problem, możesz uruchomić ponownie narzędzie SSMS lub połączyć się i rozłączyć w usłudze ADS. .
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące wymagania wstępne:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* W przypadku puli SQL potrzebny jest istniejący magazyn danych. Aby go utworzyć, zobacz [Tworzenie puli SQL](../quickstart-create-sql-pool.md). W przypadku usługi SQL na żądanie jedna została już zainicjowana w obszarze roboczym podczas tworzenia. 
* W pełni kwalifikowana nazwa SQL Server. Aby to znaleźć, zobacz [nawiązywanie połączenia z usługą SQL Synapse](connect-overview.md).

## <a name="connect"></a>Połącz

### <a name="sql-pool"></a>Pula SQL

Aby nawiązać połączenie z usługą Synapse SQL przy użyciu puli SQL, wykonaj następujące kroki: 

1. Otwórz SQL Server Management Studio (SSMS). 
1. W oknie dialogowym **łączenie z serwerem** Wypełnij pola, a następnie wybierz pozycję **Połącz**: 
  
    ![Łączenie z serwerem](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera**: wprowadź wcześniej zidentyfikowaną **nazwę serwera** .
   * **Uwierzytelnianie**: Wybierz typ uwierzytelniania, na przykład **uwierzytelnianie SQL Server** lub **Active Directory uwierzytelnianie zintegrowane**.
   * **Nazwa użytkownika** i **hasło**: Wprowadź nazwę użytkownika i hasło w przypadku wybrania powyżej SQL Server uwierzytelniania.

1. Rozwiń SQL Server platformy Azure w **Eksplorator obiektów**. Można wyświetlić bazy danych skojarzone z serwerem, takie jak Przykładowa baza danych AdventureWorksDW. Bazę danych można rozwinąć, aby wyświetlić tabele:
   
    ![Poznawanie bazy danych AdventureWorksDW](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL na żądanie (wersja zapoznawcza)

Aby nawiązać połączenie z usługą SQL Synapse przy użyciu usługi SQL na żądanie, wykonaj następujące kroki: 

1. Otwórz SQL Server Management Studio (SSMS).
1. W oknie dialogowym **łączenie z serwerem** Wypełnij pola, a następnie wybierz pozycję **Połącz**: 
   
    ![Łączenie z serwerem](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera**: wprowadź wcześniej zidentyfikowaną **nazwę serwera** .
   * **Uwierzytelnianie**: Wybierz typ uwierzytelniania, na przykład **uwierzytelnianie SQL Server** lub **uwierzytelnianie zintegrowane Active Directory**:
   * **Nazwa użytkownika** i **hasło**: Wprowadź nazwę użytkownika i hasło w przypadku wybrania powyżej SQL Server uwierzytelniania.
   * Kliknij pozycję **Połącz**.

4. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń *demonstrację* , aby zobaczyć zawartość w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Uruchamianie przykładowego zapytania

### <a name="sql-pool"></a>Pula SQL

Po nawiązaniu połączenia z bazą danych można wykonywać zapytania dotyczące danych.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Skopiuj to zapytanie T-SQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie. Aby to zrobić, kliknij `Execute` lub użyj następującego skrótu: `F5` .
   
    ![Uruchamianie zapytania](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL na żądanie

Po nawiązaniu połączenia z bazą danych można wykonywać zapytania dotyczące danych.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](./media/get-started-ssms/new-query.png)
3. Skopiuj następujące zapytanie T-SQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Uruchom zapytanie. Aby to zrobić, kliknij `Execute` lub użyj następującego skrótu: `F5` .
   
    ![Uruchamianie zapytania](./media/get-started-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie widok usPopulationView ma 3664512 wierszy.
   
    ![Wyniki zapytania](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy można nawiązać połączenie i wykonywać zapytania, spróbuj [wizualizować dane za pomocą Power BI](get-started-power-bi-professional.md).

Aby skonfigurować środowisko do uwierzytelniania Azure Active Directory, zobacz [uwierzytelnianie w usłudze SQL Synapse](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

