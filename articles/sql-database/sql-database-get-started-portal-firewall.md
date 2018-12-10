---
title: 'Witryna Azure Portal: tworzenie reguły zapory bazy danych SQL Database | Microsoft Docs'
description: Tworzenie reguły zapory na poziomie serwera bazy danych SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/01/2018
ms.openlocfilehash: cab92539b5019d4807ddefb2b84279c844f53016
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721905"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>Szybki start: tworzenie reguły zapory na poziomie serwera dla bazy danych SQL Database za pomocą witryny Azure Portal

Ten przewodnik Szybki start przeprowadzi Cię przez sposób tworzenia reguły zapory na poziomie serwera dla bazy danych Azure SQL Database tak, aby umożliwić łączenie z nią z zasobu lokalnego.

## <a name="prerequisites"></a>Wymagania wstępne

Jako punktu początkowego ten przewodnik Szybki start używa zasobów utworzonych w przewodniku [Tworzenie bazy danych SQL platformy Azure w witrynie Azure Portal](sql-database-get-started-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule"></a>Tworzenie reguły zapory na poziomie serwera

Usługa SQL Database tworzy zaporę na poziomie serwera. Ta zapora uniemożliwia aplikacjom i narzędziom nawiązywanie połączeń z serwerem i wszelkimi bazami danych serwera, chyba że utworzona zostanie reguła zapory otwierająca zaporę. Na potrzeby połączenia z adresu IP spoza platformy Azure utwórz regułę zapory dla określonego adresu lub zakresu adresów IP. Aby uzyskać więcej informacji na temat reguł zapory, zobacz [Reguła zapory na poziomie serwera usługi SQL Database](sql-database-firewall-configure.md).

> [!NOTE]
> Usługa SQL Database nawiązuje komunikację na porcie 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. Jeśli wystąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 1433.
>

Wykonaj następujące kroki, aby utworzyć regułę zapory na poziomie serwera dla podanego adresu IP klienta i włączyć zewnętrzną łączność przez zaporę usługi SQL Database wyłącznie dla konkretnego adresu IP.

1. Po zakończeniu wdrażania [wymaganej wstępnie bazy danych Azure SQL Database](#prerequisites) wybierz pozycję **Bazy danych SQL** w menu po lewej stronie, a następnie wybierz bazę danych **mySampleDatabase** na stronie **Bazy danych SQL**. Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną nazwę serwera (na przykład **mynewserver-20170824.database.windows.net**) i opcje dalszej konfiguracji.

2. Skopiuj tę w pełni kwalifikowaną nazwę serwera do użycia podczas nawiązywania połączenia z serwerem i jego bazami danych w innych przewodnikach Szybki start.

   ![nazwa serwera](./media/sql-database-get-started-portal/server-name.png)

3. Wybierz pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera SQL Database.

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory. Reguła zapory może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

   > [!IMPORTANT]
   > Domyślnie dostęp za pośrednictwem zapory usługi SQL Database jest włączony dla wszystkich usług platformy Azure. Wybierz przycisk **WYŁ.** na tej stronie, aby wyłączyć tę opcję dla wszystkich usług platformy Azure.
   >

5. Wybierz pozycję **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera otwierająca port 1433 na serwerze logicznym.

6. Zamknij stronę **Ustawienia zapory**.

Teraz można połączyć się z serwerem usługi SQL Database i jego bazami danych przy użyciu programu SQL Server Management Studio lub innego wybranego narzędzia z tego adresu IP przy użyciu poprzednio utworzonego konta administratora serwera.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Zapisz te zasoby, jeśli chcesz przejść do sekcji [Następne kroki](#next-steps) i dowiedzieć się, jak połączyć bazę danych i wykonywać w niej zapytania przy użyciu różnych metod. Jeśli jednak chcesz usunąć zasoby utworzone w tym przewodniku Szybki start, wykonaj poniższe czynności.


1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz pozycję **myResourceGroup**.
2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym nazwę **myResourceGroup**, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Teraz, gdy już masz bazę danych, możesz [nawiązać z nią połączenie i uruchamiać zapytania](sql-database-connect-query.md) za pomocą jednego z Twoich ulubionych narzędzi lub języków, w tym
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Aby dowiedzieć się, jak zaprojektować pierwszą bazę danych, tworzyć tabele i wstawiać dane, zapoznaj się z jednym z następujących samouczków:
  - [Projektowanie pierwszej bazy danych Azure SQL Database przy użyciu programu SSMS](sql-database-design-first-database.md)
  - [Projektowanie bazy danych Azure SQL Database i nawiązywanie połączenia za pomocą języka C# i narzędzia ADO.NET](sql-database-design-first-database-csharp.md)
