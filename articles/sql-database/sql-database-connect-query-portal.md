---
title: 'Witryna Azure Portal: wykonywanie zapytań do bazy danych Azure SQL za pomocą edytora zapytań | Microsoft Docs'
description: Dowiedz się, jak nawiązać połączenie z bazą danych SQL w witrynie Azure Portal za pomocą edytora zapytań SQL. Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: połącz z bazą danych sql, azure portal, portal, edytor zapytań
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: carlrab
manager: craigg
ms.date: 12/05/2018
ms.openlocfilehash: fa46260fdd5623ba32da9979aaea8470139096b8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091394"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Szybki start: używanie edytora zapytań SQL do nawiązywania połączenia i wykonywania zapytań o dane

Edytor zapytań SQL jest narzędziem przeglądarki portalu Azure, które ułatwia wykonywanie zapytań SQL w bazie danych Azure SQL Database lub usłudze Azure SQL Data Warehouse. W tym przewodniku Szybki start pokazano, jak używać edytora zapytań w celu nawiązywania połączenia z bazą danych SQL, a następnie uruchamiać instrukcje języka Transact-SQL, aby wysyłać zapytania o dane oraz wstawiać, aktualizować i usuwać dane.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

> [!NOTE]
> Upewnij się, że opcja **Zezwalaj na dostęp do usług platformy Azure** w ustawieniach zapory serwera SQL jest ustawiona na **WŁ.**. Ta opcja zapewnia edytorowi zapytań SQL dostęp do Twoich baz danych i magazynów danych.

## <a name="sign-in-the-azure-portal"></a>Logowanie w witrynie Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Nawiązywanie połączenia przy użyciu uwierzytelniania SQL

1. Wybierz pozycję **Bazy danych SQL** z menu po lewej stronie, a następnie pozycję **mySampleDatabase**.

2. W menu po lewej stronie wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**. Zostanie wyświetlona strona **Logowanie**.

    ![znajdowanie edytora zapytań](./media/sql-database-connect-query-portal/find-query-editor.PNG)

3. Z menu rozwijanego **Typ uwierzytelniania** wybierz pozycję **Uwierzytelnianie programu SQL Server** i wprowadź identyfikator użytkownika oraz hasło do konta administratora serwera, którego użyto do utworzenia bazy danych.

    ![logowanie](./media/sql-database-connect-query-portal/login-menu.png) 

4. Kliknij przycisk **OK**.


## <a name="connect-using-azure-active-directory"></a>Nawiązywanie połączenia przy użyciu usługi Azure Active Directory

Skonfigurowanie administratora usługi Active Directory (AD) umożliwia korzystanie z jednej tożsamości podczas logowania do witryny Azure Portal i do swojej bazy danych SQL. Wykonaj poniższe kroki, aby skonfigurować administratora usługi AD dla serwera SQL.

> [!NOTE]
* Konta e-mail (na przykład outlook.com, gmail.com, yahoo.com itd.) nie są jeszcze obsługiwane jako konta administratorów usługi AD. Wybierz użytkownika utworzonego natywnie w usłudze Azure AD lub sfederowanego z usługą Azure AD.
* Logowanie administratora usługi Azure AD nie działa w przypadku kont, dla których włączono uwierzytelnianie 2-etapowe.

1. Wybierz pozycję **Wszystkie zasoby** z menu po lewej stronie, a następnie wybierz serwer SQL.

2. W menu **Ustawienia** serwera SQL wybierz pozycję **Administrator usługi Active Directory**.

3. Na pasku narzędzi strony administratora usługi AD wybierz pozycję **Ustaw administratora**, a następnie wybierz użytkownika lub grupę jako administratora usługi AD.

    ![wybierz usługę active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Na pasku narzędzi strony administratora usługi AD wybierz pozycję **Zapisz**.

5. Przejdź do bazy danych **mySampleDatabase**, a następnie z menu po lewej stronie wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**. Zostanie wyświetlona strona **Logowanie**. Jeśli jesteś administratorem usługi AD, po prawej stronie w obszarze **Logowanie jednokrotne usługi Active Directory** zostanie wyświetlony komunikat, że użytkownik został zalogowany. 
   
6. Kliknij przycisk **OK**.


## <a name="view-data"></a>Wyświetlanie danych

1. Po uwierzytelnieniu wklej następujący kod SQL w edytorze zapytań, aby uzyskać 20 najważniejszych produktów według kategorii.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Na pasku narzędzi wybierz pozycję **Uruchom**, a następnie przejrzyj dane wyjściowe w okienku **Wyniki**.

![wyniki edytora zapytań](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Wstawianie danych

Użyj następującej instrukcji [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) języka Transact-SQL, aby dodać nowy produkt do tabeli `SalesLT.Product`.

1. Zastąp poprzednie zapytanie tym.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Wybierz polecenie **Uruchom**, aby wstawić nowy wiersz w tabeli Product. W okienku **Komunikaty** zostanie wyświetlony komunikat **Zapytanie zostało wykonane pomyślnie: Wiersze, których to dotyczy: 1**.


## <a name="update-data"></a>Aktualizowanie danych

Użyj następującej instrukcji [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) języka Transact-SQL, aby zmodyfikować nowy produkt.

1. Zastąp poprzednie zapytanie tym.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby zaktualizować wiersz w tabeli Product. W okienku **Komunikaty** zostanie wyświetlony komunikat **Zapytanie zostało wykonane pomyślnie: Wiersze, których to dotyczy: 1**.

## <a name="delete-data"></a>Usuwanie danych

Użyj następującej instrukcji [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) języka Transact-SQL, aby usunąć nowy produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby usunąć wiersz w tabeli Product. W okienku **Komunikaty** zostanie wyświetlony komunikat **Zapytanie zostało wykonane pomyślnie: Wiersze, których to dotyczy: 1**.


## <a name="query-editor-considerations"></a>Zagadnienia dotyczące edytora zapytań

Jest kilka rzeczy, o których trzeba wiedzieć podczas pracy z edytorem zapytań.

* Przy użyciu edytora zapytań nie można wykonywać zapytań dotyczących baz danych serwera SQL w sieci wirtualnej.

* Naciśnięcie klawisza F5 powoduje odświeżenie strony edytora i utratę tworzonego zapytania.

* Edytor zapytań nie obsługuje nawiązywania połączenia z główną bazą danych.

* Istnieje limit czasu na wykonanie zapytania wynoszący 5 minut.

* Edytor zapytań obsługuje tylko cylindryczne projekcje dla typów danych geograficznych.

* Tabele i widoki bazy danych nie obsługują funkcji IntelliSense. Jednak edytor obsługuje funkcję automatycznego uzupełniania dla nazw, które zostały już wpisane.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o języku Transact-SQL obsługiwanym w bazach danych Azure SQL, zobacz [Resolving Transact-SQL differences during migration to SQL Database (Rozwiązywanie różnic w języku Transact-SQL podczas migracji do usługi SQL Database)](sql-database-transact-sql-information.md).
