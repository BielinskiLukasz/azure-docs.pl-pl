---
title: Tworzenie i wykonywanie zapytań o pulę SQL Synapse (witryna Azure portal)
description: Tworzenie i wykonywanie zapytań o pulę SQL synapse przy użyciu portalu Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/28/2019
ms.author: Kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9605d20fa6a1480b24d7b64963aa9579ed3b5a11
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115178"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-using-the-azure-portal"></a>Szybki start: tworzenie i wykonywanie zapytań o pulę SQL Synapse przy użyciu portalu Azure

Szybko twórz i wysyłaj zapytania do puli SQL Synapse (hurtownia danych) w usłudze Azure Synapse Analytics (dawniej SQL DW) przy użyciu witryny Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

1. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

   > [!NOTE]
   > Utworzenie puli SQL w usłudze Azure Synapse może spowodować nową usługę podlegającą rozliczaniu. Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Tworzenie puli SQL

Magazyny danych są tworzone przy użyciu puli SQL w usłudze Azure Synapse Analytics. Pula SQL jest tworzona ze zdefiniowanym zestawem [zasobów obliczeniowych](memory-concurrency-limits.md). Baza danych jest tworzona w [grupie zasobów platformy Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oraz na [serwerze logicznym SQL platformy Azure](../../sql-database/sql-database-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Wykonaj następujące kroki, aby utworzyć pulę SQL, która zawiera przykładowe dane **AdventureWorksDW.**

1. wybierz **pozycję Utwórz zasób** w lewym górnym rogu witryny Azure portal.

   ![tworzenie zasobu w witrynie Azure portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. Wybierz **bazy danych** na nowej stronie i wybierz pozycję Azure **Synapse Analytics (dawniej SQL DW)** na liście **Polecane.** **New**

   ![tworzenie pustego magazynu danych](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. W **sekcji Podstawowe**podaj subskrypcję, grupę zasobów, nazwę puli SQL i nazwę serwera:

   | Ustawienie | Sugerowana wartość | Opis |
   | :------ | :-------------- | :---------- |
   | **Subskrypcja** | Twoja subskrypcja | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
   | **Grupa zasobów** | myResourceGroup | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Reguły i ograniczenia nazewnictwa). |
   | **Nazwa puli SQL** | Dowolna unikatowa nazwa globalnie (przykładem jest *mySampleDataWarehouse)* | Aby zapoznać się z prawidłową nazwymi baz danych, zobacz [Identyfikatory baz danych](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Uwaga: pula SQL jest jednym z typów bazy danych. |
   | **Serwer** | Dowolna nazwa unikatowa w skali globalnej | Wybierz istniejący serwer lub utwórz nową nazwę serwera, wybierz pozycję **Utwórz nowy**. Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Reguły i ograniczenia nazewnictwa). |

   ![tworzenie podstawowych szczegółów magazynu danych](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. W obszarze **Poziom wydajności**wybierz pozycję Wybierz **poziom wydajności,** aby opcjonalnie zmienić konfigurację za pomocą suwaka.

   ![zmienianie poziomu wydajności magazynu danych](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Aby uzyskać więcej informacji na temat poziomów wydajności, zobacz [Zarządzanie obliczeniami w usłudze Azure SQL Data Warehouse](sql-data-warehouse-manage-compute-overview.md).

5. Po zakończeniu karty Podstawowe informacje w formularzu Usługi Azure Synapse Analytics wybierz pozycję **Przejrzyj + Utwórz,** a następnie **utwórz,** aby utworzyć pulę SQL. Aprowizacja zajmuje kilka minut.

   ![wybierz przejrzyj + Utwórz](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![wybieranie pozycji Utwórz](./media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Na pasku narzędzi wybierz **pozycję Powiadomienia,** aby monitorować proces wdrażania.

   ![powiadomienie](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Tworzenie reguły zapory na poziomie serwera

Usługa Azure Synapse tworzy zaporę na poziomie serwera. Ta zapora uniemożliwia zewnętrznym aplikacjom i narzędziom łączenie się z serwerem lub bazami danych na serwerze. Aby umożliwić łączność, możesz dodać reguły zezwalające na połączenia dla konkretnych adresów IP. Wykonaj następujące kroki, aby utworzyć [regułę zapory na poziomie serwera](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) dla Twojego adresu IP klienta.

> [!NOTE]
> Usługa Azure Synapse komunikuje się za pośrednictwem portu 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. Jeśli nastąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 1433.

1. Po zakończeniu wdrażania wybierz **wszystkie usługi** z menu po lewej stronie. Wybierz **bazy danych**, wybierz gwiazdkę obok usługi Azure **Synapse Analytics,** aby dodać usługę Azure Synapse Analytics do ulubionych.

2. Wybierz **usługę Azure Synapse Analytics** z menu po lewej stronie, a następnie wybierz **mySampleDataWarehouse** na stronie **Usługi Azure Synapse Analytics.** Zostanie otwarta strona przeglądu bazy danych z w pełni kwalifikowaną nazwą serwera (na przykład **sqlpoolservername.database.windows.net)** i zawiera opcje dalszej konfiguracji.

3. Skopiuj tę w pełni kwalifikowaną nazwę serwera do połączenia się z serwerem i jego bazami danych w tym i innych szybkich startach. Aby otworzyć ustawienia serwera, wybierz nazwę serwera.

   ![znajdowanie nazwy serwera](./media/create-data-warehouse-portal/find-server-name.png)

4. Wybierz **pozycję Pokaż ustawienia zapory**.

   ![ustawienia serwera](./media/create-data-warehouse-portal/server-settings.png)

5. Zostanie otwarta strona **Ustawienia zapory** dla serwera bazy danych SQL.

   ![reguła zapory serwera](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Aby dodać bieżący adres IP do nowej reguły zapory, wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi. Reguła zapory może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

7. wybierz **zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera otwierająca port 1433 na serwerze logicznym.

8. wybierz **przycisk OK,** a następnie zamknij stronę **ustawień zapory.**

Teraz można połączyć się z serwerem SQL i jego pulami SQL przy użyciu tego adresu IP. Połączenie działa z programu SQL Server Management Studio lub dowolnego innego narzędzia. Przy łączeniu się używaj wcześniej utworzonego konta administratora serwera.

> [!IMPORTANT]
> Domyślnie dostęp za pośrednictwem zapory usługi SQL Database jest włączony dla wszystkich usług platformy Azure. wybierz **pozycję OFF** na tej stronie, a następnie wybierz pozycję **Zapisz,** aby wyłączyć zaporę dla wszystkich usług platformy Azure.

## <a name="get-the-fully-qualified-server-name"></a>Uzyskiwanie w pełni kwalifikowanej nazwy serwera

Uzyskaj w pełni kwalifikowaną nazwę serwera dla swojego serwera SQL w witrynie Azure Portal. Nazwa ta będzie używana później przy nawiązywaniu połączenia z serwerem.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz **usługę Azure Synapse Analytics** z menu po lewej stronie i wybierz ją na stronie Usługi Azure **Synapse Analytics.**

3. W okienku **Essentials** na stronie bazy danych w witrynie Azure Portal zlokalizuj i skopiuj **nazwę serwera**. W tym przykładzie w pełni kwalifikowana nazwa jest sqlpoolservername.database.windows.net.

    ![informacje o połączeniu](./media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Nawiąż połączenie z serwerem jako administrator serwera

W tej sekcji używany jest program [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) do nawiązywania połączenia z serwerem SQL platformy Azure.

1. Otwórz program SQL Server Management Studio.

2. W oknie dialogowym **Połącz z serwerem** wprowadź następujące informacje:

   | Ustawienie | Sugerowana wartość | Opis |
   | :------ | :-------------- | :---------- |
   | Typ serwera | Aparat bazy danych | Ta wartość jest wymagana |
   | Nazwa serwera | W pełni kwalifikowana nazwa serwera | Oto przykład: **sqlpoolservername.database.windows.net**. |
   | Authentication | Uwierzytelnianie programu SQL Server | Uwierzytelnianie SQL to jedyny typ uwierzytelniania skonfigurowany w tym samouczku. |
   | Logowanie | Konto administratora serwera | Konto określone podczas tworzenia serwera. |
   | Hasło | Hasło konta administratora serwera | Hasło określone podczas tworzenia serwera. |
   ||||

   ![łączenie z serwerem](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. wybierz **połącz**. W programie SSMS zostanie otwarte okno Eksplorator obiektów.

4. W Eksploratorze obiektów rozwiń pozycję **Bazy danych**. Następnie rozwiń pozycję **mySampleDatabase**, aby wyświetlić obiekty w nowej bazy danych.

   ![obiekty bazy danych](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Uruchamianie zapytań

Usługa SQL Data Warehouse używa T-SQL jako języka zapytań. Aby otworzyć okno zapytania i uruchomić kilka zapytań T-SQL, użyj następujących kroków:

1. Wybierz prawym przyciskiem **wyboru mySampleDataWarehouse** i wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.

2. W oknie zapytania wprowadź następujące polecenie, aby wyświetlić listę baz danych.

    ```sql
    SELECT * FROM sys.databases
    ```

3. wybierz **opcję Wykonaj**. W wynikach zapytania są widoczne dwie bazy danych: **master** i **mySampleDataWarehouse**.

   ![Wykonywanie zapytań kierowanych do baz danych](./media/create-data-warehouse-portal/query-databases.png)

4. Aby zobaczyć trochę danych, wpisz następujące polecenie w celu wyświetlenia liczby klientów o nazwisku Adams, którzy mają trójkę dzieci. Wynikiem jest lista z sześcioma klientami.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Zapytanie dotyczące tabeli dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz zachować dane w magazynie, możesz wstrzymać obliczenia, gdy nie używasz puli SQL. Wstrzymując obliczenia, opłaty są naliczane tylko za magazyn danych. Obliczenia można wznowić, gdy będziesz gotowy do pracy z danymi.

- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć pulę SQL.

Wykonaj następujące kroki, aby oczyścić zasoby, które nie są już potrzebne.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz pulę SQL.

   ![Oczyszczanie zasobów](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj.** Gdy pula SQL jest wstrzymana, zostanie wyświetlony przycisk **Wznów.** Aby wznowić obliczenia, wybierz pozycję **Wznów**.

3. Aby usunąć pulę SQL, aby nie była naliczana opłata za zasoby obliczeniowe lub magazyn, wybierz pozycję **Usuń**.

4. Aby usunąć utworzony serwer SQL, zaznacz **sqlpoolservername.database.windows.net** na poprzednim obrazie, a następnie wybierz pozycję **Usuń**. Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz **myResourceGroup**, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat ładowania danych do puli SQL, przejdź do artykułu [Załaduj dane do puli SQL.](load-data-from-azure-blob-storage-using-polybase.md)
