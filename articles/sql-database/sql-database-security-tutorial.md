---
title: Zabezpieczanie pojedynczej bazy danych w usłudze Azure SQL Database | Microsoft Docs
description: Dowiedz się więcej o technikach i funkcjach umożliwiających zabezpieczenie pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: e0311174303fc91767d3f99e6db05927b25aea05
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051666"
---
# <a name="tutorial-secure-a-single-database"></a>Samouczek: zabezpieczanie pojedynczej bazy danych

Usługa Azure SQL Database zabezpiecza dane w pojedynczej bazie danych SQL Database, umożliwiając:

- Ograniczanie dostępu przy użyciu reguł zapory.
- Korzystanie z mechanizmów uwierzytelniania wymagających od użytkowników potwierdzenia tożsamości.
- Stosowanie autoryzacji za pośrednictwem członkostwa i uprawnień opartych na rolach.
- Włączanie funkcji zabezpieczeń.

> [!NOTE]
> Baza danych Azure SQL Database w wystąpieniu zarządzanym jest zabezpieczona przez zasady zabezpieczeń sieci i prywatne punkty końcowe, zgodnie z opisem w tematach [Wystąpienie zarządzane usługi Azure SQL Database](sql-database-managed-instance-index.yml) i [Connectivity architecture (Architektura łączności)](sql-database-managed-instance-connectivity-architecture.md).

Możesz zwiększyć poziom bezpieczeństwa bazy danych, wykonując kilka prostych kroków. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie reguł zapory na poziomie serwera i na poziomie bazy danych
> - Konfigurowanie administratora usługi Azure Active Directory (AD)
> - Zarządzanie dostępem użytkowników za pomocą funkcji uwierzytelniania SQL, uwierzytelniania usługi Azure AD i bezpiecznych parametrów połączenia
> - Włączanie funkcji zabezpieczeń takich jak ochrona przed zagrożeniami, inspekcja, maskowanie danych i szyfrowanie

Aby dowiedzieć się więcej, zobacz artykuły [Azure SQL Database security overview (Omówienie zabezpieczeń usługi Azure SQL Database)](/azure/sql-database/sql-database-security-index) i [Capabilities (Funkcje)](sql-database-security-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że dysponujesz następującymi elementami:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Serwer i baza danych SQL na platformie Azure
    - Utwórz je za pomocą [witryny Azure Portal](sql-database-get-started-portal.md), [interfejsu wiersza polecenia](sql-database-cli-samples.md) lub [programu PowerShell](sql-database-powershell-samples.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Aby wykonać wszystkie kroki tego samouczka, zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-firewall-rules"></a>Tworzenie reguł zapory

Bazy danych SQL są chronione przez zapory na platformie Azure. Domyślnie wszystkie połączenia z serwerem i bazami danych są odrzucane, poza połączeniami z innych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure SQL Database server-level and database-level firewall rules (Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database)](sql-database-firewall-configure.md).

Najbezpieczniejsza konfiguracja to ustawienie opcji **Zezwalaj na dostęp do usług platformy Azure** na wartość **WYŁ**. Następnie należy utworzyć [zastrzeżony adres IP (wdrożenie klasyczne)](../virtual-network/virtual-networks-reserved-public-ip.md) dla zasobu wymagającego możliwości połączenia, na przykład maszyny wirtualnej platformy Azure czy usługi w chmurze, i zezwolić na dostęp przez zaporę tylko z tego adresu IP. Jeśli używasz modelu wdrożenia usługi [Resource Manager](/azure/virtual-network/virtual-network-ip-addresses-overview-arm), wymagany jest dedykowany publiczny adres IP dla każdego zasobu.

> [!NOTE]
> Usługa SQL Database nawiązuje komunikację na porcie 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez zaporę sieciową. Jeśli wystąpi taka sytuacja, nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że administrator otworzy port 1433.

### <a name="set-up-server-level-firewall-rules"></a>Konfigurowanie reguł zapory na poziomie serwera

Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych na tym samym serwerze logicznym.

Aby skonfigurować regułę zapory na poziomie serwera:

1. W witrynie Azure Portal wybierz opcję **Bazy danych SQL** z menu po lewej stronie, a następnie wybierz bazę danych na stronie **Bazy danych SQL**.

    ![reguła zapory serwera](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Pamiętaj o skopiowaniu w pełni kwalifikowanej nazwy serwera (na przykład *serwer.baza_danych.windows.net*), która będzie używana w dalszej części samouczka.

1. Na stronie **Przegląd** wybierz opcję **Ustaw zaporę serwera**. Zostanie otwarta strona **Ustawienia zapory** dla serwera bazy danych.

    1. Wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać bieżący adres IP do nowej reguły zapory. Reguła może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP. Wybierz pozycję **Zapisz**.

    ![ustawianie reguły zapory serwera](./media/sql-database-security-tutorial/server-firewall-rule2.png)

    1. Wybierz przycisk **OK** i zamknij stronę **Ustawienia zapory**.

Teraz możesz nawiązać połączenie z dowolną bazą danych na serwerze przy użyciu określonego adresu IP lub zakresu adresów IP.

> [!IMPORTANT]
> Domyślnie dostęp za pośrednictwem zapory usługi SQL Database jest włączony dla wszystkich usług platformy Azure w obszarze **Zezwalaj na dostęp do usług platformy Azure**. Wybierz przycisk **WYŁ.**, aby wyłączyć dostęp dla wszystkich usług platformy Azure.

### <a name="setup-database-level-firewall-rules"></a>Konfigurowanie reguł zapory na poziomie bazy danych

Reguły zapory na poziomie bazy danych dotyczą tylko pojedynczych baz danych. Są to reguły przenośne, podążające za bazą danych podczas przełączania serwera w tryb failover. Reguły zapory na poziomie bazy danych można skonfigurować wyłącznie za pomocą instrukcji języka Transact-SQL (T-SQL) oraz wyłącznie po skonfigurowaniu reguły zapory na poziomie serwera.

Aby skonfigurować regułę zapory na poziomie bazy danych:

1. Nawiąż połączenie z bazą danych, na przykład przy użyciu programu [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy bazę danych i wybierz pozycję **Nowe zapytanie**.

1. W oknie zapytania dodaj następującą instrukcję i zmień adres IP na swój publiczny adres IP:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na pasku narzędzi wybierz polecenie **Wykonaj**, aby utworzyć regułę zapory.

> [!NOTE]
> W programie SQL Server Management Studio można również utworzyć regułę zapory na poziomie serwera, używając polecenia [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) polecenia, ale wymagane jest wówczas połączenie z bazą danych *master*.

## <a name="create-an-azure-ad-admin"></a>Tworzenie administratora usługi Azure AD

Upewnij się, że używasz odpowiedniej domeny zarządzanej usługi Azure Active Directory (AD). Domenę usługi Azure AD można wybrać w prawym górnym rogu witryny Azure Portal. Dzięki temu masz pewność, że używasz tej samej subskrypcji zarówno dla usługi Azure AD, jak i dla serwera SQL, na którym znajduje się baza danych Azure SQL Database lub magazyn danych.

   ![Wybór usługi AD](./media/sql-database-security-tutorial/8choose-ad.png)

Aby ustawić administratora usługi Azure AD:

1. W witrynie Azure Portal na stronie **SQL Server** wybierz pozycję **Administrator usługi Active Directory**. Następnie wybierz pozycję **Ustaw administratora**.

    ![wybierz usługę active directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Aby wykonać to zadanie, musisz mieć rolę „Administrator firmy” lub „Administrator globalny”.

1. Na stronie **Dodawanie administratora** wyszukaj i wybierz użytkownika lub grupę usługi AD, a następnie wybierz pozycję **Wybierz**. Na liście znajdują się wszystkie grupy i wszyscy użytkownicy w usłudze Active Directory, a wpisy wyświetlane na szaro oznaczają elementy, które nie mogą zostać administratorami usługi Azure AD. Zobacz [funkcje i ograniczenia usługi Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![Wybór administratora](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Kontrola dostępu oparta na rolach (RBAC) ma zastosowanie tylko do portalu i nie jest stosowana na serwerze SQL.

1. W górnej części strony **Administrator usługi Active Directory** wybierz polecenie **Zapisz**.

    Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu **Administrator usługi Active Directory**.

> [!NOTE]
> Podczas konfigurowania administratora usługi Azure AD nowa nazwa administratora (użytkownika lub grupy) nie może być istniejącą nazwą użytkownika uwierzytelniania w bazie danych *master* na serwerze SQL. Jeśli tak jest, konfiguracja zakończy się niepowodzeniem, a zmiany zostaną wycofane — to oznacza, że taka nazwa administratora już istnieje. Ponieważ nazwa użytkownika do uwierzytelniania na serwerze SQL nie jest częścią usługi Azure AD, próba połączenia tego użytkownika z serwerem za pomocą uwierzytelniania usługi Azure AD nie powiedzie się.

Aby uzyskać informacje na temat konfigurowania usługi Azure AD, zobacz:

- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Add your own domain name to Azure AD (Dodawanie własnej nazwy domeny do usługi Azure Active Directory)](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure now supports federation with Windows Server AD (Platforma Microsoft Azure obsługuje teraz federację z usługą AD systemu Windows Server)](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administrowanie katalogiem usługi Azure AD](../active-directory/fundamentals/active-directory-administer.md)
- [Zarządzanie usługą Azure AD przy użyciu programu PowerShell](/powershell/azure/overview?view=azureadps-2.0)
- [Wymagane porty i protokoły dotyczące tożsamości hybrydowej](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Zarządzanie dostępem do bazy danych

Możesz zarządzać dostępem do bazy danych, dodając do niej użytkowników lub zezwalając na dostęp użytkowników za pomocą bezpiecznych parametrów połączenia. Parametry połączenia są przydatne w przypadku aplikacji zewnętrznych. Aby dowiedzieć się więcej, zobacz [Azure SQL access control (Kontrola dostępu w usłudze Azure SQL)](sql-database-control-access.md) i [AD authentication (Uwierzytelnianie usługi AD)](sql-database-aad-authentication.md).

Aby dodać użytkowników, wybierz typ uwierzytelniania bazy danych:

- **Uwierzytelnianie SQL**, używające nazwy użytkownika i hasła do logowania, które obowiązuje tylko w kontekście konkretnej bazy danych w ramach serwera.

- **Uwierzytelnianie usługi Azure AD**, używające tożsamości zarządzanych przez usługę Azure AD.

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

Aby dodać użytkownika przy użyciu uwierzytelniania SQL:

1. Nawiąż połączenie z bazą danych, na przykład przy użyciu programu [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

1. W oknie zapytania wprowadź następujące polecenie:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na pasku narzędzi wybierz polecenie **Wykonaj**, aby utworzyć użytkownika.

1. Domyślnie użytkownik może łączyć się z bazą danych, ale nie ma uprawnień do odczytywania i zapisywania danych. Aby przyznać te uprawnienia, wykonaj następujące polecenia w nowym oknie zapytania:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Utwórz konta użytkowników innych niż administrator na poziomie bazy danych, chyba że potrzebują oni możliwości wykonywania zadań administratora, takich jak tworzenie nowych użytkowników.

### <a name="azure-ad-authentication"></a>Uwierzytelnianie w usłudze Azure AD

Uwierzytelnianie w usłudze Azure Active Directory wymaga utworzenia użytkowników bazy danych jako użytkowników zawartej bazy danych. Użytkownik zawartej bazy danych jest mapowany na tożsamość w katalogu usługi Azure AD skojarzoną z bazą danych i nie ma danych logowania w bazie danych *master*. Tożsamość usługi Azure AD może być skonfigurowana dla jednego użytkownika lub grupy. Aby uzyskać więcej informacji, zobacz [Contained database users, make your database portable (Użytkownicy zawartej bazy danych — tworzene przenośnej bazy danych)](https://msdn.microsoft.com/library/ff929188.aspx) i zapoznaj się z [samouczkiem usługi Azure AD](./sql-database-aad-authentication-configure.md) dotyczącym uwierzytelniania przy użyciu usługi Azure AD.

> [!NOTE]
> Użytkowników bazy danych (z wyjątkiem administratorów) nie można tworzyć za pośrednictwem witryny Azure Portal. Role RBAC platformy Azure nie są stosowane na serwerach SQL, w bazach danych i magazynach danych. Służą wyłącznie do zarządzania zasobami platformy Azure i nie mają zastosowania do uprawnień do bazy danych.
>
> Na przykład osoba o roli *Współautor serwera SQL Server* nie może udzielać dostępu do połączenia z bazą danych lub magazynem danych. Uprawnienia te należy nadać w bazie danych za pomocą instrukcji języka T-SQL.

> [!IMPORTANT]
> Znaki specjalne, takie jak dwukropek `:` lub handlowe „i” `&`, nie są obsługiwane w nazwach użytkowników w instrukcjach języka T-SQL `CREATE LOGIN` i `CREATE USER`.

Aby dodać użytkownika przy użyciu uwierzytelniania usługi Azure AD:

1. Połącz się z serwerem Azure SQL Server przy użyciu konta w usłudze Azure AD z uprawnieniem co najmniej *ALTER ANY USER*.

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy bazę danych i wybierz pozycję **Nowe zapytanie**.

1. W oknie zapytania wprowadź następujące polecenie, a następnie zastąp wartość `<Azure_AD_principal_name>` odpowiednią nazwą główną użytkownika w usłudze Azure AD lub nazwą wyświetlaną grupy w usłudze Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Użytkownicy usługi Azure AD są oznaczeni w metadanych bazy danych za pomocą typu `E (EXTERNAL_USER)`, a grupy za pomocą typu `X (EXTERNAL_GROUPS)`. Aby uzyskać więcej informacji, zobacz [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Bezpieczne parametry połączenia

W celu zapewnienia bezpiecznego, szyfrowanego połączenia pomiędzy aplikacją kliencką i usługą SQL Database, należy skonfigurować parametry połączenia, aby:

- żądać połączenia szyfrowanego oraz
- nie ufać certyfikatowi serwera.

Dzięki temu połączenie jest nawiązywane przy użyciu zabezpieczeń Transport Layer Security (TLS) i zmniejsza się ryzyko ataków typu „man-in-the-middle” (człowiek pośrodku). Parametry połączenia są dostępne na poziomie bazy danych i są wstępnie skonfigurowane w celu obsługi sterowników klienta takich jak ADO.NET, JDBC, ODBC czy PHP. Aby uzyskać informacje dotyczące zabezpieczeń TLS i łączności, zobacz [Zagadnienia dotyczące protokołu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Aby skopiować bezpieczne parametry połączenia:

1. W witrynie Azure Portal wybierz opcję **Bazy danych SQL** z menu po lewej stronie, a następnie wybierz bazę danych na stronie **Bazy danych SQL**.

1. Na stronie **Omówienie** kliknij pozycję **Pokaż parametry połączenia bazy danych**.

1. Wybierz kartę sterownika i skopiuj pełne parametry połączenia.

    ![Parametry połączenia sterownika ADO.NET](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.

Usługa Azure SQL Database udostępnia funkcje zabezpieczeń, które są dostępne w witrynie Azure Portal. Te funkcje są dostępne na poziomie bazy danych oraz serwera, z wyjątkiem maskowania danych, które jest dostępne wyłącznie na poziomie bazy danych. Aby dowiedzieć się więcej, zobacz [Advanced threat detection (Zaawansowane wykrywanie zagrożeń)](sql-advanced-threat-protection.md), [Auditing (Inspekcja)](sql-database-auditing.md), [Dynamic data masking (Dynamiczne maskowanie danych)](sql-database-dynamic-data-masking-get-started.md) i [Transparent data encryption (Niewidoczne szyfrowanie danych)](transparent-data-encryption-azure-sql.md).

### <a name="advanced-threat-protection"></a>Zaawansowana ochrona przed zagrożeniami

Funkcja zaawansowanej ochrony przed zagrożeniami wykrywa potencjalne zagrożenia w czasie rzeczywistym i udostępnia alerty zabezpieczeń dotyczące podejrzanych działań. Użytkownicy mogą zbadać te podejrzane zdarzenia za pomocą funkcji inspekcji, aby określić, czy dane zdarzenie było próbą uzyskania dostępu, naruszenia zabezpieczeń lub wykorzystania danych w bazie danych. Użytkownicy mają do dyspozycji także przegląd zabezpieczeń z oceną podatności oraz narzędzie do odnajdowania i klasyfikacji danych.

> [!NOTE]
> Przykładem zagrożenia jest na przykład wstrzyknięcie kodu SQL, czyli atak polegający na wprowadzeniu złośliwego kodu SQL wraz z danymi wejściowymi do aplikacji. W wyniku takiego ataku aplikacja może niezauważenie wykonać złośliwy kod SQL, umożliwiając dostęp do danych przechowywanych w bazie danych lub ich modyfikację.

Aby włączyć ochronę przed zagrożeniami:

1. W witrynie Azure Portal wybierz opcję **Bazy danych SQL** z menu po lewej stronie, a następnie wybierz bazę danych na stronie **Bazy danych SQL**.

1. Na stronie **Przegląd** wybierz link z **nazwą serwera**. Zostanie otwarta strona serwera bazy danych.

1. Na stronie **SQL Server** znajdź sekcję **Zabezpieczenia** i wybierz pozycję **Zaawansowana ochrona przed zagrożeniami**.

    1. Aby włączyć funkcję, wybierz pozycję **WŁ.** w obszarze **Zaawansowana ochrona przed zagrożeniami**. Następnie wybierz pozycję **Zapisz**.

    ![Okienko nawigacji](./media/sql-database-security-tutorial/threat-settings.png)

    Można również skonfigurować wiadomości e-mail, aby otrzymywać alerty zabezpieczeń, szczegółowe informacje o magazynie i typy wykrytych zagrożeń.

1. Wróć do strony **Bazy danych SQL** w bazie danych i wybierz pozycję **Zaawansowana ochrona przed zagrożeniami** w sekcji **Zabezpieczenia**. W tym miejscu znajdziesz różne wskaźniki zabezpieczeń dostępne w bazie danych.

    ![Stan zagrożenia](./media/sql-database-security-tutorial/threat-status.png)

W przypadku wykrycia nietypowych działań otrzymasz wiadomość e-mail z informacjami o zdarzeniu. Zawiera ona rodzaj działania, bazę danych, serwer, czas zdarzenia, możliwe przyczyny i zalecane działania do wykonania w celu zbadania zdarzenia i zminimalizowania potencjalnego zagrożenia. Jeśli otrzymasz taką wiadomość e-mail, wybierz link **Dziennik inspekcji usługi Azure SQL**, aby przejść do witryny Azure Portal i wyświetlić odpowiednie rekordy inspekcji z czasu tego zdarzenia.

   ![Wiadomość e-mail dotycząca wykrywania zagrożeń](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Inspekcja

Funkcja inspekcji śledzi zdarzenia w bazie danych i zapisuje te zdarzenia w dzienniku inspekcji w usłudze Azure Storage, Log Analytics lub w centrum zdarzeń usługi Event Hubs. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać potencjalne naruszenia zabezpieczeń.

Aby włączyć inspekcję:

1. W witrynie Azure Portal wybierz opcję **Bazy danych SQL** z menu po lewej stronie, a następnie wybierz bazę danych na stronie **Bazy danych SQL**.

1. W sekcji **Zabezpieczenia** wybierz pozycję **Inspekcja**.

1. W ustawieniach funkcji **Inspekcja** ustaw następujące wartości:

    1. Ustaw opcję **Inspekcja** w pozycji **WŁ**.

    1. Wybierz dowolną z następujących lokalizacji w polu **Lokalizacja docelowa dziennika inspekcji**:

        - **Storage** — konto usługi Azure Storage, na którym dzienniki zdarzeń będą zapisywane z możliwością pobrania w postaci plików *xel*.

           > [!TIP]
           > Użyj tego samego konta magazynu dla wszystkich baz danych poddawanych inspekcji, aby najlepiej wykorzystać szablony raportów inspekcji.

        - **Log Analytics** — automatyczne zapisywanie zdarzeń do celów zapytań lub dalszej analizy.

            > [!NOTE]
            > Do obsługi zaawansowanych funkcji takich jak analiza, niestandardowe reguły alertów czy eksportowanie do programu Excel lub usługi Power BI jest wymagany **obszar roboczy usługi Log Analytics**. Bez obszaru roboczego dostępny jest tylko edytor zapytań.

        - **Centrum zdarzeń** — umożliwia kierowanie zdarzeń do użytku w innych aplikacjach

    1. Wybierz pozycję **Zapisz**.

    ![Ustawienia inspekcji](./media/sql-database-security-tutorial/audit-settings.png)

1. Możesz teraz wybrać pozycję **Wyświetl dzienniki inspekcji**, aby wyświetlić dane zdarzeń z bazy danych.

    ![Rekordy inspekcji](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Zobacz temat [SQL database auditing (Inspekcja w usłudze SQL Database)](sql-database-auditing.md), aby zapoznać się z instrukcjami dalszego dostosowywania zdarzeń inspekcji za pomocą programu PowerShell lub interfejsu API REST.

### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

Funkcja maskowania danych automatycznie ukrywa dane poufne w bazie danych.

Aby włączyć maskowanie danych:

1. W witrynie Azure Portal wybierz opcję **Bazy danych SQL** z menu po lewej stronie, a następnie wybierz bazę danych na stronie **Bazy danych SQL**.

1. W sekcji **Zabezpieczenia** wybierz pozycję **Dynamiczne maskowanie danych**.

1. W ustawieniach funkcji **Dynamiczne maskowanie danych** wybierz polecenie **Dodaj maskę**, aby dodać regułę maskowania. Platforma Azure automatycznie wyświetli dostępne schematy bazy danych, tabele i kolumny do wyboru.

    ![Ustawienia maskowania](./media/sql-database-security-tutorial/mask-settings.png)

1. Wybierz pozycję **Zapisz**. Wybrane informacje będą teraz maskowane w celu ochrony ich poufności.

    ![Przykład maskowania](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparent Data Encryption

Funkcja szyfrowania automatycznie szyfruje dane magazynowane, nie wymagając wprowadzania zmian w aplikacjach uzyskujących dostęp do szyfrowanej bazy danych. W przypadku nowych baz danych szyfrowanie jest włączone domyślnie. Możesz również szyfrować dane, używając programu SQL Server Management Studio i funkcji [Always Encrypted](sql-database-always-encrypted.md).

Aby włączyć lub sprawdzić szyfrowanie:

1. W witrynie Azure Portal wybierz opcję **Bazy danych SQL** z menu po lewej stronie, a następnie wybierz bazę danych na stronie **Bazy danych SQL**.

1. W sekcji **Zabezpieczenia** wybierz pozycję **Transparent Data Encryption**.

1. Jeśli jest to konieczne, ustaw opcję **Szyfrowanie danych** w pozycji **WŁ**. Wybierz pozycję **Zapisz**.

    ![Niewidoczne szyfrowanie danych](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Aby wyświetlić stan szyfrowania, połącz się z bazą danych za pomocą programu [SQL Server Management Studio](./sql-database-connect-query-ssms.md) i wykonaj zapytanie dotyczące kolumny `encryption_state` w widoku [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017). Stan `3` oznacza, że baza danych jest szyfrowana.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wyjaśniono, jak zwiększyć poziom bezpieczeństwa bazy danych w kilku prostych krokach. W tym samouczku omówiono:

> [!div class="checklist"]
> - Tworzenie reguł zapory na poziomie serwera i na poziomie bazy danych
> - Konfigurowanie administratora usługi Azure Active Directory (AD)
> - Zarządzanie dostępem użytkowników za pomocą funkcji uwierzytelniania SQL, uwierzytelniania usługi Azure AD i bezpiecznych parametrów połączenia
> - Włączanie funkcji zabezpieczeń takich jak ochrona przed zagrożeniami, inspekcja, maskowanie danych i szyfrowanie

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć bazę danych rozproszoną geograficznie.

> [!div class="nextstepaction"]
>[Implementowanie rozproszonej geograficznie bazy danych](sql-database-implement-geo-distributed-database.md)
