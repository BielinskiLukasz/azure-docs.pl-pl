---
title: 'SSMS: nawiązywanie połączenia i wykonywanie zapytań dotyczących danych w bazie danych Azure SQL Database | Microsoft Docs'
description: Dowiedz się, jak łączyć się z bazą danych SQL Database na platformie Azure przy użyciu programu SQL Server Management Studio (SSMS). Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: łączenie z bazą danych SQL, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 095d7cf43d071d3857160d05e721bf7ac165cba2
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756788"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Szybki start: używanie programu SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych w bazie danych SQL Azure

W tym przewodniku Szybki start nawiążesz połączenie z bazą danych SQL na platformie Azure przy użyciu programu [SQL Server Management Studio][ssms-install-latest-84g] (SSMS). Następnie uruchomisz instrukcje języka Transact-SQL w celu wykonywania zapytań, wstawiania, aktualizowania i usuwania danych. Program SQL Server Management Studio możesz wykorzystać do zarządzania dowolną infrastrukturą SQL — od programu SQL Server po usługę SQL Database dla systemu Microsoft Windows.  

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

* Skonfigurowana reguła zapory na poziomie serwera. Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).

## <a name="install-the-latest-ssms"></a>Instalowanie najnowszej wersji środowiska SSMS

Przed rozpoczęciem upewnij się, że zainstalowano najnowszą wersję środowiska [SSMS][ssms-install-latest-84g]. 

## <a name="sql-server-connection-information"></a>Informacje o połączeniu z serwerem SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

W środowisku SMSS połącz się z serwerem usługi Azure SQL Database. 

> [!IMPORTANT]
> Serwer usługi Azure SQL Database nasłuchuje na porcie 1433. Aby można było nawiązać połączenie z serwerem usługi SQL Database spoza firmowej zapory, ten port w zaporze musi być otwarty.
>

1. Otwórz program SSMS. Zostanie wyświetlone okno dialogowe **Nawiązywanie połączenia z serwerem**.

2. Wprowadź następujące informacje:

   | Ustawienie      | Sugerowana wartość    | Opis | 
   | ------------ | ------------------ | ----------- | 
   | **Typ serwera** | Aparat bazy danych | Wartość wymagana. |
   | **Nazwa serwera** | W pełni kwalifikowana nazwa serwera | Na przykład: **mojnowyserwer20170313.database.windows.net**. |
   | **Uwierzytelnianie** | Uwierzytelnianie programu SQL Server | W tym samouczku używane jest uwierzytelnianie SQL. |
   | **Logowanie** | Identyfikator użytkownika konta administratora serwera | Identyfikator użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   | **Hasło** | Hasło konta administratora serwera | Hasło użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   ||||

   ![łączenie z serwerem](./media/sql-database-connect-query-ssms/connect.png)  

3. Wybierz pozycję **Opcje** w oknie dialogowym **Połącz z serwerem**. Z menu rozwijanego **Połącz z bazą danych** wybierz pozycję **mojaPrzykladowaBazaDanych**.

   ![nawiązywanie połączenia z bazą danych na serwerze](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Wybierz przycisk **Połącz**. Zostanie otwarte okno Eksplorator obiektów. 

5. Aby wyświetlić obiekty bazy danych, rozwiń listę **Bazy danych**, a następnie rozwiń węzeł **mojaPrzykladowaBazaDanych**.

   ![wyświetlanie obiektów bazy danych](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Zapytania o dane

Uruchom następujący kod z instrukcją [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) języka Transact-SQL, aby wykonać zapytanie o 20 najpopularniejszych produktów według kategorii.

1. W Eksploratorze obiektów kliknij prawym przyciskiem myszy pozycję **mojaPrzykladowaBazaDanych** i wybierz opcję **Nowe zapytanie**. Zostanie otwarte nowe okno zapytania połączone z Twoją bazą danych.

2. Wklej w oknie zapytania to zapytanie SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na pasku narzędzi wybierz pozycję **Wykonaj**, aby pobrać dane z tabel `Product` i `ProductCategory`.

    ![zapytanie do pobierania danych z dwóch tabel](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Wstawianie danych

Uruchom następujący kod z instrukcją [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) języka Transact-SQL, aby utworzyć nowy produkt w tabeli `SalesLT.Product`.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Wybierz polecenie **Wykonaj**, aby wstawić nowy wiersz do tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

## <a name="view-the-result"></a>Wyświetlanie wyniku

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   SELECT * FROM [SalesLT].[Product] 
   WHERE Name='myNewProduct' 

2. Select **Execute**. The following result appears. 

   ![result](./media/sql-database-connect-query-ssms/result.png)

 
## Update data

Run this [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL code to modify your new product.

1. Replace the previous query with this one.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Wykonaj**, aby zaktualizować określony wiersz w tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

## <a name="delete-data"></a>Usuwanie danych

Uruchom następujący kod z instrukcją [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) języka Transact-SQL, aby usunąć nowy produkt.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Wykonaj**, aby usunąć określony wiersz z tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o programie SSMS, zobacz [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (Program SQL Server Management Studio).
- Aby połączyć się i wykonać zapytanie za pomocą witryny Azure Portal, zobacz [Connect and query with the Azure portal SQL Query editor (Nawiązywanie połączeń i wykonywanie zapytań za pomocą edytora zapytań SQL w witrynie Azure Portal)](sql-database-connect-query-portal.md).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą programu Visual Studio Code, zobacz [Connect and query with Visual Studio Code](sql-database-connect-query-vscode.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą programu Visual Studio Code).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą platformy .NET, zobacz [Connect and query with .NET](sql-database-connect-query-dotnet.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą platformy .NET).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka PHP, zobacz [Connect and query with PHP](sql-database-connect-query-php.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka PHP).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą oprogramowania Node.js, zobacz [Connect and query with Node.js](sql-database-connect-query-nodejs.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą oprogramowania Node.js).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Java, zobacz [Connect and query with Java](sql-database-connect-query-java.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Java).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Python, zobacz [Connect and query with Python](sql-database-connect-query-python.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Python).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Ruby, zobacz [Connect and query with Ruby](sql-database-connect-query-ruby.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Ruby).


<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms

