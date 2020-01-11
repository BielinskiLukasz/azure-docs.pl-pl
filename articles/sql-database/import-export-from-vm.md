---
title: Importowanie lub eksportowanie bazy danych SQL
description: Zaimportuj lub wyeksportuj bazę danych SQL Azure bez zezwalania usługom platformy Azure na dostęp do serwera.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897847"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importowanie lub eksportowanie bazy danych SQL Azure bez zezwalania usługom platformy Azure na dostęp do serwera

W tym artykule opisano sposób importowania lub eksportowania bazy danych Azure SQL Database, gdy dla opcji *Zezwalaj na usługi platformy Azure* jest ustawiona wartość *off* na serwerze Azure SQL. Przepływ pracy korzysta z maszyny wirtualnej platformy Azure w celu wykonania operacji importowania lub eksportowania.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [portalu Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Tworzenie maszyny wirtualnej platformy Azure

Utwórz maszynę wirtualną platformy Azure, wybierając przycisk **Wdróż na platformie Azure** .

Ten szablon umożliwia wdrożenie prostej maszyny wirtualnej z systemem Windows przy użyciu kilku różnych opcji wersji systemu Windows przy użyciu najnowszej wersji poprawki. Spowoduje to wdrożenie maszyny wirtualnej o rozmiarze a2 w lokalizacji grupy zasobów i zwrócenie w pełni kwalifikowanej nazwy domeny maszyny wirtualnej.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Aby uzyskać więcej informacji, zobacz [bardzo proste wdrożenie maszyny wirtualnej z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Poniższe kroki pokazują, jak nawiązać połączenie z maszyną wirtualną za pomocą podłączania pulpitu zdalnego.

1. Po zakończeniu wdrażania przejdź do zasobu maszyny wirtualnej.

    ![Maszyna wirtualna](./media/import-export-from-vm/vm.png)  

2. Wybierz przycisk **Połącz**.

   Zostanie wyświetlony formularz protokołu Remote Desktop Protocol pliku (RDP), za pomocą publicznego adresu IP adres i numer portu dla maszyny wirtualnej.

   ![Formularz protokołu RDP](./media/import-export-from-vm/rdp.png)  

3. Wybierz opcję **Pobierz plik RDP**.

   > [!NOTE]
   > Można również używanie protokołu SSH, aby nawiązać połączenie z maszyną Wirtualną.

4. Zamknij **Połącz z maszyną wirtualną** formularza.
5. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP.
6. Po wyświetleniu monitu wybierz **Connect**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) ze sklepu Mac App Store.

7. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie wybierz **OK**.

8. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz **tak** lub **Kontynuuj** Aby nawiązać połączenie.



## <a name="install-sqlpackage"></a>Zainstaluj pakiet sqlpackage

[Pobierz i zainstaluj najnowszą wersję elementu sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Aby uzyskać dodatkowe informacje, zobacz [sqlpackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Tworzenie reguły zapory w celu zezwolenia na dostęp maszyny wirtualnej do bazy danych

Dodaj publiczny adres IP maszyny wirtualnej do zapory serwera SQL Database.

Poniższe kroki umożliwiają utworzenie reguły zapory adresów IP na poziomie serwera dla publicznego adresu IP maszyny wirtualnej i włączenie łączności z maszyną wirtualną.

1. Wybierz pozycję **bazy danych SQL** z menu po lewej stronie, a następnie wybierz swoją bazę danych ze strony **bazy danych SQL** . Zostanie otwarta strona przeglądu bazy danych zawierająca w pełni kwalifikowaną nazwę serwera (na przykład **servername.Database.Windows.NET**) i opcje dalszej konfiguracji.

2. Skopiuj tę w pełni kwalifikowaną nazwę serwera do użycia podczas nawiązywania połączenia z serwerem i jego bazami danych.

   ![nazwa serwera](./media/sql-database-get-started-portal/server-name.png)

3. Wybierz pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera bazy danych.

   ![reguła zapory bazująca na adresach IP na poziomie serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać publiczny adres IP maszyny wirtualnej do nowej reguły zapory adresów IP na poziomie serwera. Reguła zapory bazująca na adresach IP na poziomie serwera może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

5. Wybierz pozycję **Zapisz**. Reguła zapory adresów IP na poziomie serwera jest tworzona dla publicznego adresu IP maszyny wirtualnej otwierającej port 1433 na serwerze SQL Database.

6. Zamknij stronę **Ustawienia zapory**.



## <a name="export-a-database-using-sqlpackage"></a>Eksportowanie bazy danych przy użyciu elementu sqlpackage

Aby wyeksportować bazę danych SQL przy użyciu narzędzia wiersza polecenia [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage) , zobacz [Eksportowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Narzędzie sqlpackage jest dostarczane z najnowszymi wersjami narzędzi [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)lub można pobrać najnowszą wersję elementu [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Zalecamy używanie narzędzia sqlpackage do skalowania i wydajności w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

W tym przykładzie przedstawiono sposób eksportowania bazy danych przy użyciu narzędzia sqlpackage. exe z Active Directory uniwersalnego uwierzytelniania. Zamień na wartości, które są specyficzne dla danego środowiska.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Importowanie bazy danych przy użyciu elementu sqlpackage

Aby zaimportować bazę danych SQL Server przy użyciu narzędzia wiersza polecenia [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage) , zobacz [Importowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). Pakiet sqlpackage ma najnowsze [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server narzędzia do danych](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Możesz również pobrać najnowszą wersję elementu [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

W przypadku skalowania i wydajności zalecamy użycie elementu sqlpackage w większości środowisk produkcyjnych, a nie przy użyciu Azure Portal. Blog zespołu SQL Server Customer Advisory o migracji przy użyciu plików `BACPAC` można znaleźć w temacie [Migrowanie z SQL Server do Azure SQL Database przy użyciu plików BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Następujące polecenie sqlpackage importuje bazę danych **AdventureWorks2017** z magazynu lokalnego na serwer Azure SQL Database. Tworzy nową bazę danych o nazwie **myMigratedDatabase** z **Premium** warstwy usług i **P6** cel usługi. Zmień te wartości jako odpowiednie dla danego środowiska.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Aby nawiązać połączenie z serwerem SQL Database zarządzania pojedynczą bazą danych za zaporą firmową, zapora musi mieć otwarty port 1433. Aby nawiązać połączenie z wystąpieniem zarządzanym, musisz mieć [połączenie typu punkt-lokacja](sql-database-managed-instance-configure-p2s.md) lub połączenie z usługą Express Route.

Ten przykład przedstawia sposób importowania bazy danych przy użyciu narzędzia SqlPackage przy użyciu uwierzytelniania usługi Active Directory Universal.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Zagadnienia związane z wydajnością

Szybkość eksportowania zależy od wielu czynników (na przykład kształtu danych), dlatego nie można przewidzieć oczekiwanej szybkości. Pakiet sqlpackage może zająć dużo czasu, szczególnie w przypadku dużych baz danych.

Aby uzyskać najlepszą wydajność, możesz wypróbować następujące strategie:

1. Upewnij się, że żadne inne obciążenie nie jest uruchomione w bazie danych. Tworzenie kopii przed eksportem może być najlepszym rozwiązaniem, aby upewnić się, że żadne inne obciążenia nie są uruchomione.
2. Zwiększ cel poziomu usługi bazy danych (SLO), aby lepiej obsługiwać obciążenie eksportu (głównie we/wy). Jeśli baza danych jest obecnie GP_Gen5_4, być może warstwa Krytyczne dla działania firmy może pomóc w zapoznaniu się z obciążeniem.
3. Upewnij się, że istnieją indeksy klastrowane szczególnie dla dużych tabel. 
4. Maszyny wirtualne powinny znajdować się w tym samym regionie, w którym znajduje się baza danych, aby zapobiec ograniczeniom sieci.
5. Maszyny wirtualne powinny mieć dysk SSD o odpowiednim rozmiarze do generowania artefaktów tymczasowych przed przekazaniem do usługi BLOB Storage.
6. Maszyny wirtualne powinny mieć odpowiednią konfigurację podstawową i pamięciową dla określonej bazy danych.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Zapisz zaimportowane lub wyeksportowane. Plik BACPAC

Polu. Plik BACPAC może być przechowywany w obiektach [blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)lub [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Aby osiągnąć najlepszą wydajność, użyj Azure Files. Element sqlpackage działa z systemem plików, dzięki czemu może uzyskiwać dostęp do Azure Files bezpośrednio.

Aby obniżyć koszty, Użyj obiektów blob platformy Azure, które są tańsze niż udział plików platformy Azure w warstwie Premium. Jednak wymaga to skopiowania [. Plik BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) między obiektem BLOB i lokalnym systemem plików przed operacją importu lub eksportu. W efekcie proces zajmie więcej czasu.

W celu przeładowania lub pobrania. Pliki BACPAC, zobacz [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](../storage/common/storage-use-azcopy-blobs.md), a następnie [Transferowanie danych przy użyciu AzCopy i magazynu plików](../storage/common/storage-use-azcopy-files.md).

W zależności od środowiska może być konieczne [skonfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak nawiązać połączenie i wykonywać zapytania zaimportowanej bazy danych SQL, zobacz [Szybki Start: Azure SQL Database: Użyj programu SQL Server Management Studio do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-ssms.md).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby uzyskać informacje dotyczące całego programu SQL Server proces migracji bazy danych, w tym zalecenia dotyczące wydajności, zobacz [migracji bazy danych programu SQL Server do usługi Azure SQL Database](sql-database-single-database-migrate.md).
- Aby dowiedzieć się, jak zarządzać i udostępniania kluczy magazynu i dostępu współdzielonego podpisów bezpieczne, zobacz [Przewodnik po zabezpieczeniach magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
