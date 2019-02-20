---
title: Włącz uwierzytelnianie usługi Azure Active Directory dla środowiska Azure-SSIS Integration Runtime | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączania uwierzytelniania usługi Azure Active Directory za pomocą tożsamości zarządzanej usługi Azure Data Factory utworzyć środowisko Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 2/19/2019
ms.author: douglasl
ms.openlocfilehash: b672264e1cb3cd415532cf4bcfbbd268afffa70d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415941"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Włącz uwierzytelnianie usługi Azure Active Directory dla środowiska Azure-SSIS Integration Runtime

W tym artykule pokazano, jak włączyć uwierzytelnianie usługi Azure Active Directory (Azure AD) za pomocą tożsamości zarządzanej dla usługi Azure Data Factory (ADF) i użyj go zamiast uwierzytelniania SQL do tworzenia środowiska Azure-SSIS Integration Runtime (IR) to z kolei spowoduje utworzenie SSIS katalog bazy danych (SSISDB) w usłudze Azure SQL Database server/zarządzanego wystąpienia w Twoim imieniu.

Aby uzyskać więcej informacji na temat tożsamości zarządzanej dla usługi ADF, zobacz [tożsamości usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Jeśli utworzono już środowisko IR Azure-SSIS przy użyciu uwierzytelniania SQL nie można ponownie skonfigurować środowiska IR do użycia uwierzytelnianie usługi Azure AD przy użyciu programu PowerShell, w tym momencie, ale możesz to zrobić w aplikacji portal/ADF platformy Azure. 

## <a name="enable-azure-ad-on-azure-sql-database"></a>Włączanie usługi Azure AD w usłudze Azure SQL Database

Serwer usługi Azure SQL Database obsługuje tworzenie bazy danych za pomocą użytkownika usługi Azure AD. Najpierw należy utworzyć grupę usługi Azure AD za pomocą tożsamości zarządzanej dla usługi ADF jako członka. Następnie należy ustawić użytkownika usługi Azure AD jako administratora usługi Active Directory dla serwera usługi Azure SQL Database i następnie połączyć je w SQL Server Management Studio (SSMS) przy użyciu tego użytkownika. Na koniec należy utworzyć użytkownika zawartej reprezentująca grupę usługi Azure AD, aby tożsamości zarządzanej dla usługi ADF mogą zostać użyte przez Azure-SSIS IR do utworzenia bazy danych SSISDB w Twoim imieniu.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Utwórz grupę usługi Azure AD za pomocą tożsamości zarządzanej dla usługi ADF jako członka

Można użyć istniejącej grupy usługi Azure AD lub utworzyć nowe konto, przy użyciu programu Azure AD PowerShell.

1.  Zainstaluj [usługi Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu.

2.  Zaloguj się przy użyciu `Connect-AzureAD`, uruchom następujące polecenie cmdlet, aby utworzyć grupę i zapisz go w zmiennej:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Wynik wygląda podobnie jak poniższy przykład wyświetla również wartość zmiennej:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Dodaj zarządzaną tożsamością dla usługi ADF do grupy. Można wykonać tego artykułu [tożsamości usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) można pobrać identyfikator podmiotu zabezpieczeń tożsamości usługi (np. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ale nie należy używać identyfikator aplikacji tożsamości usługi w tym celu).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Możesz również sprawdzić członkostwa w grupie później.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Konfigurowanie uwierzytelniania usługi Azure AD dla serwera Azure SQL Database

Możesz [Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure AD przy użyciu języka SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) wykonując następujące czynności:

1.  W witrynie Azure portal wybierz **wszystkich usług** -> **serwerów SQL** nawigacji po lewej stronie.

2.  Wybierz swój serwer usługi Azure SQL Database, należy skonfigurować przy użyciu uwierzytelniania usługi Azure AD.

3.  W **ustawienia** części bloku wybierz **administratora usługi Active Directory**.

4.  Na pasku poleceń Wybierz **Ustaw administratora**.

5.  Wybierz konto użytkownika usługi Azure AD, można wprowadzić administrator serwera, a następnie wybierz pozycję **wybierz.**

6.  Na pasku poleceń Wybierz **Zapisz.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Utworzenia zawartego użytkownika w usłudze Azure SQL Database server reprezentującej grupę usługi Azure AD

W tym następnego kroku należy [programu Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Należy uruchomić program SSMS.

2.  W **Połącz z serwerem** okno dialogowe, wprowadź nazwę serwera usługi Azure SQL Database w **nazwy serwera** pola.

3.  W **uwierzytelniania** pól, zaznacz **usługi Active Directory — uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego** (można również użyć innych dwa typy uwierzytelniania usługi Active Directory, zobacz [ Konfigurowanie i zarządzanie nimi uwierzytelniania usługi Azure AD przy użyciu języka SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4.  W **nazwa_użytkownika** wprowadź nazwę konta usługi Azure AD, który został ustawiony jako administrator serwera, np. testuser@xxxonline.com.

5.  Wybierz **Connect** i ukończyć procesu logowania.

6.  W **Eksplorator obiektów**, rozwiń węzeł **baz danych** -> **systemowych baz danych** folderu.

7.  Kliknij prawym przyciskiem myszy **wzorca** bazy danych, a następnie wybierz pozycję **nowe zapytanie**.

8.  W oknie zapytania wprowadź następujące polecenie języka T-SQL, a następnie wybierz pozycję **Execute** na pasku narzędzi.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Polecenie powinno zakończyć się pomyślnie, tworząc zawartego użytkownika do reprezentowania grupy.

9.  Wyczyść okno zapytania, wprowadź następujące polecenie języka T-SQL, a następnie wybierz **Execute** na pasku narzędzi.

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Polecenie powinno zakończyć się pomyślnie, udzielania zawartej użytkownikowi możliwość tworzenia bazy danych (SSISDB).

10.  Jeśli chcesz się przełączyć na potrzeby uwierzytelniania usługi Azure AD dla środowiska Azure-SSIS IR do niego dostęp z bazy danych SSISDB został utworzony przy użyciu uwierzytelniania programu SQL, kliknij prawym przyciskiem myszy **SSISDB** bazy danych, a następnie wybierz pozycję **nowe zapytanie**.

11.  W oknie zapytania wprowadź następujące polecenie języka T-SQL, a następnie wybierz pozycję **Execute** na pasku narzędzi.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Polecenie powinno zakończyć się pomyślnie, tworząc zawartego użytkownika do reprezentowania grupy.

12.  Wyczyść okno zapytania, wprowadź następujące polecenie języka T-SQL, a następnie wybierz **Execute** na pasku narzędzi.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Polecenie powinno zakończyć się pomyślnie, udzielania zawartej użytkownikowi możliwość dostępu do bazy danych SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Włączanie usługi Azure AD na wystąpienie zarządzane usługi Azure SQL Database

Wystąpienie usługi Azure SQL Database Managed obsługuje tworzenie bazy danych za pomocą tożsamości zarządzanej dla usługi ADF bezpośrednio. Nie należy dołączyć tożsamości zarządzanej dla usługi ADF do grupy usługi Azure AD ani utworzenia zawartego użytkownika reprezentujące tej grupy w wystąpieniu zarządzanemu.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Konfigurowanie uwierzytelniania usługi Azure AD dla wystąpienia zarządzanego Azure SQL Database

1.   W witrynie Azure portal wybierz **wszystkich usług** -> **serwerów SQL** nawigacji po lewej stronie.

2.   Wybierz wystąpienie usługi zarządzane, należy skonfigurować przy użyciu uwierzytelniania usługi Azure AD.

3.   W **ustawienia** części bloku wybierz **administratora usługi Active Directory**.

4.   Na pasku poleceń Wybierz **Ustaw administratora**.

5.   Wybierz konto użytkownika usługi Azure AD, można wprowadzić administrator serwera, a następnie wybierz pozycję **wybierz**.

6.   Na pasku poleceń Wybierz **Zapisz**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Dodawanie tożsamości zarządzanej dla usługi ADF jako użytkownik w wystąpieniu zarządzanym usługi Azure SQL Database

W tym następnego kroku należy [programu Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Należy uruchomić program SSMS.

2.  Połączyć się z wystąpieniem zarządzanym przy użyciu konta administratora SQL/Active Directory.

3.  W **Eksplorator obiektów**, rozwiń węzeł **baz danych** -> **systemowych baz danych** folderu.

4.  Kliknij prawym przyciskiem myszy **wzorca** bazy danych, a następnie wybierz pozycję **nowe zapytanie**.

5.  Pobierz tożsamości zarządzanej dla usługi ADF. Można wykonać tego artykułu [tożsamości usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) Aby pobrać identyfikator podmiotu zabezpieczeń aplikacji tożsamości usługi (ale nie należy używać identyfikator tożsamości usługi w tym celu).

6.  W oknie zapytania Uruchom następujący skrypt języka T-SQL można przekonwertować tożsamości zarządzanej dla usługi ADF do typu binary:

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your SERVICE IDENTITY APPLICATION ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    Polecenie powinno zakończyć się pomyślnie, wyświetlanie tożsamości zarządzanej dla usługi ADF jako wartość binarną.

7.  Wyczyść okno zapytania i uruchom następujący skrypt języka T-SQL, aby dodać tożsamość zarządzaną dla usługi ADF jako użytkownik

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your SERVICE IDENTITY APPLICATION ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    Polecenie powinno zakończyć się pomyślnie, udzielanie tożsamości zarządzanej dla usługi ADF możliwość utworzenia bazy danych (SSISDB).

8.  Jeśli chcesz się przełączyć na potrzeby uwierzytelniania usługi Azure AD dla środowiska Azure-SSIS IR do niego dostęp z bazy danych SSISDB został utworzony przy użyciu uwierzytelniania programu SQL, kliknij prawym przyciskiem myszy **SSISDB** bazy danych, a następnie wybierz pozycję **nowe zapytanie**.

9.  W oknie zapytania wprowadź następujące polecenie języka T-SQL, a następnie wybierz pozycję **Execute** na pasku narzędzi.

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    Polecenie powinno zakończyć się pomyślnie, udzielanie tożsamości zarządzanej dla usługi ADF możliwość dostępu do bazy danych SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Aprowizacja Azure-SSIS IR w aplikacji portal/ADF platformy Azure

Podczas aprowizowania środowiska IR Azure-SSIS w aplikacji portal/ADF platformy Azure na **ustawienia programu SQL** wybierz opcję **uwierzytelnianie w usłudze AAD korzystanie z tożsamości zarządzanej dla usługi ADF** opcji. Poniższy zrzut ekranu przedstawia ustawienia dla środowiska IR za pomocą serwera usługi Azure SQL Database hostowania bazy SSISDB. Dla środowiska IR za pomocą wystąpienia zarządzanego hostowania bazy SSISDB **warstwy usługi bazy danych wykazu** i **Zezwalaj na usługi platformy Azure na dostęp** ustawienia nie dotyczą, podczas gdy inne ustawienia są takie same.

Aby uzyskać więcej informacji na temat tworzenia środowiska Azure-SSIS IR, zobacz [tworzenie środowiska Azure-SSIS integration runtime w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Ustawienia środowiska Azure-SSIS integration Runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Aprowizowanie środowiska Azure-SSIS IR przy użyciu programu PowerShell

Aby zainicjować obsługę środowiska IR Azure-SSIS przy użyciu programu PowerShell, wykonaj następujące czynności:

1.  Zainstaluj [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modułu.

2.  W skrypcie, nie należy ustawiać `CatalogAdminCredential` parametru. Na przykład:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
