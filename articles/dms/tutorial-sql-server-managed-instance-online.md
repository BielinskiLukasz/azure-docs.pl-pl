---
title: 'Samouczek: Migrowanie SQL Server online do wystąpienia zarządzanego SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online SQL Server lokalnie do wystąpienia zarządzanego Azure SQL Database przy użyciu Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298904"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Samouczek: Migrowanie SQL Server do wystąpienia zarządzanego Azure SQL Database w trybie online za pomocą usługi DMS

Azure Database Migration Service do migracji baz danych z wystąpienia lokalnego SQL Server do [wystąpienia zarządzanego Azure SQL Database](../sql-database/sql-database-managed-instance.md) o minimalnym przestoju. Dodatkowe metody, które mogą wymagać ręcznej pracy, są dostępne w artykule [Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

W tym samouczku przeprowadzisz migrację bazy danych **Adventureworks2012** z wystąpienia lokalnego SQL Server do wystąpienia zarządzanego SQL Database o minimalnym przestoju przy użyciu Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji i Rozpocznij migrację w trybie online przy użyciu Azure Database Migration Service.
> * Monitorowanie migracji.
> * Wykonaj uruchomienie produkcyjne migracji, gdy wszystko będzie gotowe.

> [!IMPORTANT]
> W przypadku migracji w trybie online z SQL Server do SQL Database wystąpienia zarządzanego przy użyciu Azure Database Migration Service należy zapewnić pełną kopię zapasową bazy danych i kolejne kopie zapasowe dzienników w udziale sieciowym SMB, którego usługa może używać do migrowania baz danych. Azure Database Migration Service nie inicjuje żadnych kopii zapasowych, a zamiast tego używa istniejących kopii zapasowych, które mogą już znajdować się w ramach planu odzyskiwania po awarii dla migracji.
> Należy pamiętać o wykonywaniu [kopii zapasowych, korzystając z opcji with Checksum](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Upewnij się również, że nie dołączysz wielu kopii zapasowych (tj. pełnych i t-log) do pojedynczego nośnika kopii zapasowej. Wykonaj każdą kopię zapasową w osobnym pliku kopii zapasowej. Na koniec można użyć skompresowanych kopii zapasowych, aby zmniejszyć prawdopodobieństwo wystąpienia potencjalnych problemów związanych z migracją dużych kopii zapasowych.

> [!NOTE]
> Użycie Azure Database Migration Service do przeprowadzenia migracji w trybie online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca Tworzenie wystąpienia Azure Database Migration Service w tym samym regionie świadczenia usługi Azure jako docelowej bazie danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

> [!IMPORTANT]
> Skrócenie czasu trwania procesu migracji w trybie online jest możliwe, aby zminimalizować ryzyko przerwania spowodowane przez ponowną konfigurację lub planowaną konserwację. W przypadku takiego zdarzenia proces migracji rozpocznie się od początku. W przypadku planowanej konserwacji istnieje okres prolongaty wynoszący 36 godzin przed ponownym uruchomieniem procesu migracji.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację w trybie online z SQL Server do wystąpienia zarządzanego SQL Database. Aby przeprowadzić migrację w trybie offline, zobacz [migrowanie SQL Server do wystąpienia zarządzanego SQL Database w trybie offline za pomocą usługi DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Informacje o topologii sieci dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu Azure Database Migration Service](https://aka.ms/dmsnetworkformi). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) do podsieci, w której zostanie zainicjowana usługa:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.
    >
    >Jeśli nie masz łączności między lokacjami w sieci lokalnej i na platformie Azure lub jeśli masz ograniczoną przepustowość połączenia lokacja-lokacja, rozważ użycie Azure Database Migration Service w trybie hybrydowym (wersja zapoznawcza). Tryb hybrydowy wykorzystuje lokalny proces roboczy migracji wraz z wystąpieniem Azure Database Migration Service działającego w chmurze. Aby utworzyć wystąpienie Azure Database Migration Service w trybie hybrydowym, zapoznaj się z artykułem [Tworzenie wystąpienia Azure Database Migration Service w trybie hybrydowym przy użyciu Azure Portal](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > W odniesieniu do konta magazynu używanego w ramach migracji należy wykonać następujące działania:
    > * Wybierz, aby zezwolić wszystkim sieci na dostęp do konta magazynu.
    > * Włącz [delegowanie podsieci](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) w podsieci mi i zaktualizuj reguły zapory konta magazynu, aby zezwolić na tę podsieć.

* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują następujących portów komunikacji przychodzącej do Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu źródłowej bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do SQL Server źródłowej, która domyślnie jest portem TCP 1433.
* Jeśli używasz wielu nazwanych wystąpień SQL Server przy użyciu portów dynamicznych, możesz włączyć usługę SQL Browser i zezwolić na dostęp do portu UDP 1434 za pośrednictwem zapór, aby Azure Database Migration Service mógł nawiązać połączenie z nazwanym wystąpieniem na serwerze źródłowym.
* Jeśli używasz urządzenia zapory przed źródłowymi bazami danych, może być konieczne dodanie reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji, a także plików za pośrednictwem portu SMB 445.
* Utwórz SQL Database wystąpienie zarządzane, postępując zgodnie ze szczegółowymi informacjami w artykule [tworzenie Azure SQL Database wystąpienia zarządzanego w Azure Portal](https://aka.ms/sqldbmi).
* Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.
* Podaj udział sieciowy SMB, który zawiera wszystkie pliki kopii zapasowej bazy danych i kolejne pliki kopii zapasowej dziennika transakcji, które Azure Database Migration Service mogą służyć do migracji bazy danych.
* Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
* Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage w celu wykonania operacji przywracania.
* Utwórz identyfikator aplikacji Azure Active Directory, który generuje klucz identyfikatora aplikacji, którego Azure Database Migration Service może używać do nawiązywania połączenia z docelowym wystąpieniem zarządzanym usługi Azure Database i kontenerem usługi Azure Storage. Aby uzyskać więcej informacji, zobacz artykuł [Use portal to create an Azure Active Directory application and service principal that can access resources (Tworzenie aplikacji usługi Azure Active Directory i jednostki usługi, które mogą uzyskać dostęp do zasobów, w portalu)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Azure Database Migration Service wymaga uprawnienia współautor w subskrypcji dla określonego identyfikatora aplikacji. Alternatywnie można utworzyć niestandardowe role, które przyznają określone uprawnienia, które są wymagane przez Azure Database Migration Service. Instrukcje krok po kroku dotyczące korzystania z ról niestandardowych można znaleźć w artykule [role niestandardowe dla SQL Server do SQL Database migracji w trybie online wystąpienia zarządzanego](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Utworzenie lub zanotowanie konta usługi Azure Storage w **standardowej warstwie wydajności**, które umożliwia usłudze DMS przekazanie plików kopii zapasowej bazy danych i użycie ich do migrowania bazy danych.  Upewnij się, że utworzono konto usługi Azure Storage w tym samym regionie, w którym jest tworzone wystąpienie Azure Database Migration Service.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration**wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj pozycję **Azure Database Migration Service**, a następnie wybierz pozycję **Azure Database Migration Service** z listy rozwijanej.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz ją.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do SQL Server źródłowych i docelowym SQL Database wystąpieniem zarządzanym.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](https://aka.ms/DMSVnet).

    Aby uzyskać dodatkowe informacje, zobacz artykuł [topologie sieci dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Wybierz jednostkę SKU z warstwy cenowej Premium.

    > [!NOTE]
    > Migracje online są obsługiwane tylko w przypadku korzystania z warstwy Premium.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL Database**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

   ![Utwórz projekt Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Wybierz pozycję **Utwórz i uruchom działanie**, aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia TLS szyfrowane przy użyciu certyfikatu z podpisem własnym nie zapewniają silnych zabezpieczeń. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na protokole TLS przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Wybierz pozycję **Zapisz**.

4. Na ekranie **Wybierz źródłowe bazy danych** wybierz bazę danych **Adventureworks2012** na potrzeby migracji.

   ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Jeśli używasz usługi SQL Server Integration Services (SSIS), usługa DMS nie obsługuje obecnie migrowania bazy danych katalogu dla projektów/pakietów usługi SSIS (SSISDB) z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database. Możesz jednak aprowizować usługę SSIS w usłudze Azure Data Factory (ADF) i ponownie wdrożyć projekty/pakiety usługi SSIS w docelowej bazie danych SSISDB hostowanej przez wystąpienie zarządzane usługi Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **szczegóły docelowej migracji** Określ identyfikator i klucz **aplikacji** , których **Key** wystąpienie DMS może używać do nawiązywania połączenia z wystąpieniem docelowym Azure SQL Database wystąpienia zarządzanego i konta usługi Azure Storage.

    Aby uzyskać więcej informacji, zobacz artykuł [Use portal to create an Azure Active Directory application and service principal that can access resources (Tworzenie aplikacji usługi Azure Active Directory i jednostki usługi, które mogą uzyskać dostęp do zasobów, w portalu)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Wybierz **subskrypcję** zawierającą wystąpienie docelowe Azure SQL Database wystąpienia zarządzanego, a następnie wybierz wystąpienie docelowe.

    Jeśli jeszcze nie zainicjowano obsługi administracyjnej Azure SQL Database wystąpienia zarządzanego, wybierz [link](https://aka.ms/SQLDBMI) , aby ułatwić obsługę administracyjną wystąpienia. Gdy wystąpienie zarządzane usługi Azure SQL Database będzie gotowe, możesz wrócić do tego projektu, aby przeprowadzić migrację.

3. Podaj **nazwę użytkownika bazy danych SQL** i **hasło**, aby nawiązać połączenie z wystąpieniem zarządzanym usługi Azure SQL Database.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Wybierz pozycję **Zapisz**.

## <a name="select-source-databases"></a>Wybieranie źródłowych baz danych

1. Na ekranie **Wybierz źródłowe bazy danych** wybierz źródłową bazę danych, którą chcesz zmigrować.

    ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Wybierz pozycję **Zapisz**.

## <a name="configure-migration-settings"></a>Konfigurowanie ustawień migracji

1. Na ekranie **Konfiguruj ustawienia migracji** podaj następujące informacje:

    | | |
    |--------|---------|
    |**Udział lokalizacji sieciowej protokołu SMB** | Lokalny udział sieciowy SMB lub udział plików platformy Azure, który zawiera pliki pełnej kopii zapasowej bazy danych i pliki kopii zapasowej dziennika transakcji, które Azure Database Migration Service mogą być używane do migracji. Konto usługi, na którym uruchomiono źródłowe wystąpienie programu SQL Server, musi mieć uprawnienia do odczytu/zapisu w tym udziale sieciowym. Podaj nazwę FQDN lub adresy IP serwera w udziale sieciowym, na przykład „\\\nazwa_serwera.nazwa_domeny.com\folder_kopii_zapasowych” lub „\\\adres_IP\folder_kopii_zapasowych”. W celu zwiększenia wydajności zaleca się użycie osobnego folderu dla każdej bazy danych, która ma zostać zmigrowana. Ścieżkę udziału plików na poziomie bazy danych można podać przy użyciu opcji **Ustawienia zaawansowane** . |
    |**Nazwa użytkownika** | Upewnij się, że użytkownik systemu Windows ma uprawnienia pełnej kontroli w udziale sieciowym, który podano powyżej. Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage w celu wykonania operacji przywracania. Jeśli korzystasz z udziału plików platformy Azure, użyj nazwy konta magazynu wstępnie na platformie AZURE jako nazwy użytkownika. |
    |**Hasło** | Hasło użytkownika. Jeśli używasz udziału plików platformy Azure, użyj klucza konta magazynu jako hasła. |
    |**Subskrypcja konta usługi Azure Storage** | Wybierz subskrypcję, która zawiera konto usługi Azure Storage. |
    |**Konto usługi Azure Storage** | Wybierz konto usługi Azure Storage, na które usługa DMS może przekazać pliki kopii zapasowej z udziału sieciowego protokołu SMB używane podczas migracji bazy danych.  Zalecamy wybranie konta usługi Storage w tym samym regionie co usługa DMS w celu uzyskania optymalnej wydajności przekazywania plików. |

    ![Konfigurowanie ustawień migracji](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Jeśli Azure Database Migration Service pokazuje błąd "System Error 53" lub "System Error 57", przyczyna może skutkować niezdolnością Azure Database Migration Service dostępu do udziału plików platformy Azure. W przypadku wystąpienia jednego z tych błędów Udziel dostępu do konta magazynu z sieci wirtualnej, korzystając z instrukcji znajdujących się [tutaj](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Jeśli funkcja sprawdzania sprzężenia zwrotnego jest włączona, a SQL Server źródłowa i udział plików znajdują się na tym samym komputerze, źródło nie będzie w stanie uzyskać dostępu do plików udziału przy użyciu nazwy FQDN. Aby rozwiązać ten problem, wyłącz funkcję sprawdzania sprzężenia zwrotnego przy użyciu instrukcji w [tym miejscu](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Wybierz pozycję **Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

    ![Podsumowanie projektu migracji](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Uruchamianie i monitorowanie migracji

1. Wybierz polecenie **Uruchom migrację**.

2. Na ekranie działania migracji wybierz pozycję **Odśwież**, aby zaktualizować wyświetlane dane.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Możesz rozwinąć kategorie baz danych i danych logowania, aby monitorować stan migracji odpowiednich obiektów serwera.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po przywróceniu pełnej kopii zapasowej bazy danych w docelowym wystąpieniu SQL Database wystąpieniem zarządzanym baza danych będzie dostępna do wykonywania migracji uruchomienie produkcyjne.

1. Gdy wszystko będzie gotowe do zakończenia migracji bazy danych online, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Zatrzymaj cały ruch przychodzący do źródłowych baz danych.

3. Weź kopię zapasową końcowego fragmentu dziennika, udostępnij plik kopii zapasowej w udziale sieciowym protokołu SMB i zaczekaj na przywrócenie ostatecznej wersji kopii zapasowej dziennika transakcji.

    W tym momencie wartość w polu **Oczekujące zmiany** będzie wynosić 0.

4. Wybierz polecenie **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.

    ![Przygotowanie do zakończenia migracji jednorazowej](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Gdy stan migracji bazy danych zostanie **zakończony**, Połącz swoje aplikacje z nowym wystąpieniem docelowym Azure SQL Database wystąpienia zarządzanego.

    ![Ukończona migracja jednorazowa](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Następne kroki

* Samouczek pokazujący, jak przeprowadzić migrację bazy danych do wystąpienia zarządzanego przy użyciu polecenia przywracania T-SQL, zobacz [przywracanie kopii zapasowej do wystąpienia zarządzanego przy użyciu polecenia Restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Aby uzyskać informacje o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane](../sql-database/sql-database-managed-instance.md).
* Aby uzyskać informacje na temat łączenia aplikacji z wystąpieniem zarządzanym, zobacz [łączenie aplikacji](../sql-database/sql-database-managed-instance-connect-app.md).
