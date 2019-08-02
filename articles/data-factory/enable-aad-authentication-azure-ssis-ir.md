---
title: Włącz Azure Active Directory uwierzytelnianie dla platformy Azure — SSIS Integration Runtime | Microsoft Docs
description: W tym artykule opisano sposób włączania Azure Active Directory uwierzytelniania przy użyciu tożsamości zarządzanej Azure Data Factory do tworzenia Integration Runtime platformy Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: 1e55d1878b1a5616d467f2fa27b1b20132d5e77c
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517004"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Włączanie Azure Active Directory uwierzytelniania na platformie Azure — SSIS Integration Runtime

W tym artykule opisano sposób włączania uwierzytelniania Azure Active Directory (Azure AD) przy użyciu tożsamości zarządzanej dla Azure Data Factory (ADF) i używania go zamiast uwierzytelniania SQL w celu utworzenia Integration Runtime platformy Azure-SSIS, który zostanie włączony. Baza danych wykazu usług SSIS (SSISDB) w Azure SQL Database serwerze/wystąpieniu zarządzanym w Twoim imieniu.

Aby uzyskać więcej informacji na temat tożsamości zarządzanej dla Twojego ADF, zobacz [Managed Identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  W tym scenariuszu uwierzytelnianie usługi Azure AD za pomocą tożsamości zarządzanej na potrzeby usługi ADF jest używane tylko w przypadku tworzenia i kolejnych operacji uruchamiania środowiska IR usługi SSIS, które spowodują włączenie aprowizacji i nawiązanie połączenia z usługą SSISDB. W przypadku wykonań pakietów SSIS środowisko IR usług SSIS będzie nadal łączyć się z usługą SSISDB przy użyciu uwierzytelniania SQL z w pełni zarządzanymi kontami, które są tworzone podczas aprowizacji SSISDB.
>-  Jeśli masz już utworzone środowisko IR SSIS przy użyciu uwierzytelniania SQL, nie możesz ponownie skonfigurować go do korzystania z uwierzytelniania usługi Azure AD za pomocą programu PowerShell, ale możesz to zrobić za pośrednictwem aplikacji Azure Portal/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Włącz usługę Azure AD na Azure SQL Database

Serwer Azure SQL Database obsługuje tworzenie bazy danych za pomocą użytkownika usługi Azure AD. Najpierw należy utworzyć grupę usługi Azure AD z zarządzaną tożsamością dla Twojego ADF jako członka. Następnie musisz ustawić użytkownika usługi Azure AD jako administratora Active Directory dla serwera Azure SQL Database, a następnie nawiązać z nim połączenie w SQL Server Management Studio (SSMS) przy użyciu tego użytkownika. Na koniec należy utworzyć zawartego użytkownika reprezentującego grupę usługi Azure AD, dzięki czemu zarządzana tożsamość dla tego ADF może być używana przez środowisko Azure-SSIS IR do tworzenia SSISDB w Twoim imieniu.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Utwórz grupę usługi Azure AD z zarządzaną tożsamością dla Twojego ADF jako członka

Możesz użyć istniejącej grupy usługi Azure AD lub utworzyć nową grupę przy użyciu programu Azure AD PowerShell.

1.  Zainstaluj [usługi Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modułu.

2.  Zaloguj się przy `Connect-AzureAD`użyciu, uruchom następujące polecenie cmdlet, aby utworzyć grupę, i Zapisz ją w zmiennej:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Wynik wygląda podobnie do poniższego przykładu, który wyświetla również wartość zmiennej:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Dodaj zarządzaną tożsamość do usługi ADF do grupy. W celu uzyskania identyfikatora obiektu tożsamości zarządzanej podmiotu zabezpieczeń (np. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc nie należy używać identyfikatora aplikacji zarządzanej tożsamości). [Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Możesz również sprawdzić członkostwo w grupie później.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Konfigurowanie uwierzytelniania usługi Azure AD dla serwera Azure SQL Database

Można [skonfigurować uwierzytelnianie usługi Azure AD i zarządzać nimi za pomocą programu SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) , wykonując następujące czynności:

1.  W Azure Portal wybierz pozycję **wszystkie usługi** -> **serwery SQL** z lewej strony nawigacji.

2.  Wybierz serwer Azure SQL Database, który ma zostać skonfigurowany przy użyciu uwierzytelniania usługi Azure AD.

3.  W sekcji **Ustawienia** w bloku wybierz pozycję **Active Directory administrator**.

4.  Na pasku poleceń wybierz pozycję **Ustaw administratora**.

5.  Wybierz konto użytkownika usługi Azure AD, które ma być administratorem serwera, a następnie wybierz pozycję **Wybierz.**

6.  Na pasku poleceń wybierz pozycję **Zapisz.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Utwórz zawartego użytkownika w Azure SQL Database serwerze reprezentującym grupę usługi Azure AD

W tym następnym kroku potrzebujesz [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Uruchom narzędzie SSMS.

2. W oknie dialogowym **łączenie z serwerem** wprowadź nazwę serwera Azure SQL Database w polu **Nazwa serwera** .

3. W polu **Authentication (uwierzytelnianie** ) wybierz opcję **Active Directory — uniwersalna z obsługą usługi MFA** (można również użyć dwóch innych typów uwierzytelniania Active Directory, zobacz [Konfigurowanie i Zarządzanie uwierzytelnianiem w usłudze Azure AD przy użyciu programu SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. W polu **Nazwa użytkownika** wprowadź nazwę konta usługi Azure AD ustawianą jako administrator serwera, np. testuser@xxxonline.com.

5. Wybierz pozycję **Połącz** i Zakończ proces logowania.

6. W ->  **Eksplorator obiektów**rozwiń folder**bazy danych system** Databases.

7. Kliknij prawym przyciskiem myszy bazę danych **Master** , a następnie wybierz pozycję **nowe zapytanie**.

8. W oknie zapytania wprowadź następujące polecenie T-SQL, a następnie wybierz pozycję **Wykonaj** na pasku narzędzi.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Polecenie powinno zakończyć się pomyślnie, tworząc zawarty użytkownik reprezentujący grupę.

9. Wyczyść okno zapytania, wprowadź następujące polecenie T-SQL, a następnie wybierz pozycję **Wykonaj** na pasku narzędzi.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Polecenie powinno zakończyć się pomyślnie, przyznając zawartemu użytkownikowi możliwość tworzenia bazy danych (SSISDB).

10. Jeśli SSISDB został utworzony przy użyciu uwierzytelniania SQL i chcesz przełączyć się do korzystania z uwierzytelniania usługi Azure AD dla środowiska Azure-SSIS IR, aby uzyskać do niego dostęp, kliknij prawym przyciskiem myszy pozycję baza danych **SSISDB** , a następnie wybierz pozycję **nowe zapytanie**.

11. W oknie zapytania wprowadź następujące polecenie T-SQL, a następnie wybierz pozycję **Wykonaj** na pasku narzędzi.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Polecenie powinno zakończyć się pomyślnie, tworząc zawarty użytkownik reprezentujący grupę.

12. Wyczyść okno zapytania, wprowadź następujące polecenie T-SQL, a następnie wybierz pozycję **Wykonaj** na pasku narzędzi.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Polecenie powinno zakończyć się pomyślnie, przyznając zawartemu użytkownikowi możliwość dostępu do SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Włącz usługę Azure AD na Azure SQL Database wystąpieniu zarządzanym

Azure SQL Database wystąpienie zarządzane obsługuje tworzenie bazy danych z zarządzaną tożsamością dla Twojego ADF bezpośrednio. Nie musisz przyłączać tożsamości zarządzanej do Twojego ADF do grupy usługi Azure AD ani nie tworzyć zawartych użytkowników reprezentujących tę grupę w wystąpieniu zarządzanym.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Konfigurowanie uwierzytelniania usługi Azure AD dla Azure SQL Database wystąpienia zarządzanego

Wykonaj kroki opisane w temacie [Inicjowanie obsługi administracyjnej Azure Active Directory administratora dla wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Dodaj tożsamość zarządzaną dla Twojego ADF jako użytkownika w Azure SQL Database wystąpieniu zarządzanym

W tym następnym kroku potrzebujesz [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Uruchom narzędzie SSMS.

2.  Połącz się z wystąpieniem zarządzanym przy użyciu konta SQL Server, które jest **administratorem**systemu. Jest to tymczasowe ograniczenie, które zostanie usunięte po znalezieniu podmiotów zabezpieczeń serwera usługi Azure AD (logowania) dla Azure SQL Database wystąpienia zarządzanego. Jeśli spróbujesz użyć konta administratora usługi Azure AD do utworzenia nazwy logowania, zostanie wyświetlony następujący błąd: Msg 15247, poziom 16, stan 1, użytkownik wiersza 1 nie ma uprawnień do wykonania tej akcji.

3.  W ->  **Eksplorator obiektów**rozwiń folder**bazy danych system** Databases.

4.  Kliknij prawym przyciskiem myszy bazę danych **Master** , a następnie wybierz pozycję **nowe zapytanie**.

5.  W oknie zapytania wykonaj następujący skrypt T-SQL, aby dodać zarządzaną tożsamość dla Twojego ADF jako użytkownika

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Polecenie powinno zakończyć się pomyślnie, przyznając zarządzaną tożsamość dla usługi ADF, która umożliwia tworzenie bazy danych (SSISDB).

6.  Jeśli SSISDB został utworzony przy użyciu uwierzytelniania SQL i chcesz przełączyć się do korzystania z uwierzytelniania usługi Azure AD dla środowiska Azure-SSIS IR, aby uzyskać do niego dostęp, kliknij prawym przyciskiem myszy pozycję baza danych **SSISDB** , a następnie wybierz pozycję **nowe zapytanie**.

7.  W oknie zapytania wprowadź następujące polecenie T-SQL, a następnie wybierz pozycję **Wykonaj** na pasku narzędzi.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Polecenie powinno zakończyć się pomyślnie, przyznając zarządzaną tożsamość usługi ADF dostępną do SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Udostępnianie środowiska Azure-SSIS IR w aplikacji Azure Portal/ADF

W przypadku aprowizacji środowiska Azure-SSIS IR w aplikacji Azure Portal/ADF na stronie **Ustawienia SQL** wybierz opcję **Użyj uwierzytelniania usługi AAD z zarządzaną tożsamością dla opcji ADF** . Poniższy zrzut ekranu przedstawia ustawienia dla środowiska IR z Azure SQL Database Server Hosting SSISDB. W przypadku środowiska IR z zarządzanym wystąpieniem SSISDB nie ma zastosowania **warstwy usługi bazy danych wykazu** i **Zezwalaj usługom platformy Azure na dostęp do** ustawień, a inne ustawienia są takie same.

Aby uzyskać więcej informacji na temat sposobu tworzenia środowiska Azure-SSIS IR, zobacz temat [Create an Azure-SSIS Integration Runtime w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Ustawienia środowiska Azure-SSIS Integration Runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Udostępnianie środowiska Azure-SSIS IR przy użyciu programu PowerShell

Aby udostępnić środowisko Azure-SSIS IR przy użyciu programu PowerShell, wykonaj następujące czynności:

1.  Zainstaluj moduł [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  W skrypcie nie ustawiaj `CatalogAdminCredential` parametru. Przykład:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
