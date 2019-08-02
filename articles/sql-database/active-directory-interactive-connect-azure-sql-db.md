---
title: ActiveDirectoryInteractive nawiązuje połączenie z serwerem SQL | Microsoft Docs
description: C#Przykład kodu z objaśnieniami do nawiązywania połączenia z Azure SQL Database przy użyciu trybu SqlAuthenticationMethod. ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
ms.openlocfilehash: 4f36dcc9953134ac5dd24d3d762ac0dde9949ab7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561362"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Łączenie się z usługą Azure SQL Database przy użyciu usługi Azure MFA Authentication

Ten artykuł zawiera C# program, który nawiązuje połączenie z Azure SQL Database. Program używa uwierzytelniania w trybie interaktywnym, który obsługuje [uwierzytelnianie wieloskładnikowe systemu Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Aby uzyskać więcej informacji na temat obsługi usługi uwierzytelniania wieloskładnikowego dla narzędzi SQL, zobacz [Azure Active Directory Support in SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Uwierzytelnianie wieloskładnikowe dla Azure SQL Database

Począwszy od .NET Framework w wersji 4.7.2, Wyliczenie [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ma nową wartość:. `ActiveDirectoryInteractive` W programie klienckim C# wartość wyliczenia kieruje system do korzystania z trybu interaktywnego Azure Active Directory (Azure AD), który obsługuje uwierzytelnianie wieloskładnikowe w celu nawiązania połączenia z bazą danych Azure SQL. Użytkownik, który uruchamia program, widzi następujące okna dialogowe:

* Okno dialogowe, w którym jest wyświetlana nazwa użytkownika usługi Azure AD z prośbą o podanie hasła użytkownika.

   Jeśli domena użytkownika jest federacyjny przy użyciu usługi Azure AD, to okno dialogowe nie zostanie wyświetlone, ponieważ nie jest wymagana żadna wartość hasła.

   Jeśli zasady usługi Azure AD nakładają uwierzytelnianie wieloskładnikowe na użytkownika, wyświetlane są następne dwa okna dialogowe.

* Gdy użytkownik po raz pierwszy przechodzi przez uwierzytelnianie wieloskładnikowe, system wyświetli okno dialogowe z prośbą o numer telefonu komórkowego do wysyłania wiadomości tekstowych do programu. Każdy komunikat zawiera *kod weryfikacyjny* , który użytkownik musi wprowadzić w następnym oknie dialogowym.

* Okno dialogowe z prośbą o kod weryfikacyjny uwierzytelniania wieloskładnikowego, który system wysłał do telefonu komórkowego.

Informacje o sposobie konfigurowania usługi Azure AD w celu wymagania uwierzytelniania wieloskładnikowego znajdują się [w temacie Wprowadzenie do usługi Azure MFA Authentication w chmurze](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Zrzuty ekranu tych okien dialogowych, zobacz [Konfigurowanie uwierzytelniania wieloskładnikowego dla SQL Server Management Studio i usługi Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> .NET Framework interfejsów API można wyszukiwać za pomocą [strony narzędzia przeglądarki interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/).
>
> Możesz również przeszukiwać bezpośrednio za pomocą [opcjonalnego&lt;parametru wartość&gt; wyszukiwania](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Skonfiguruj C# aplikację w Azure Portal

Przed rozpoczęciem należy utworzyć i udostępnić [serwer Azure SQL Database](sql-database-get-started-portal.md) .

### <a name="register-your-app-and-set-permissions"></a>Zarejestruj aplikację i Ustaw uprawnienia

Aby można było korzystać z uwierzytelniania usługi C# Azure AD, program musi być zarejestrowany jako aplikacja usługi Azure AD. Aby zarejestrować aplikację, musisz być administratorem usługi Azure AD lub użytkownikiem przypisaną rolą *dewelopera aplikacji* usługi Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Przypisywanie ról administratora i niebędących administratorami do użytkowników z Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Zakończenie rejestracji aplikacji powoduje wygenerowanie i wyświetlenie **identyfikatora aplikacji**. Program musi dołączyć ten identyfikator, aby nawiązać połączenie.

Aby zarejestrować i ustawić wymagane uprawnienia dla aplikacji:

1. W Azure Portal wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji** > **nowej rejestracji aplikacji**.

    ![Rejestracja aplikacji](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Po utworzeniu rejestracji aplikacji jest generowana i wyświetlana wartość **identyfikatora aplikacji** .

    ![Wyświetlany identyfikator aplikacji](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Wybierz **pozycję zarejestrowane** > **Ustawienia** > aplikacji**wymagane uprawnienia** > **Dodaj**.

    ![Ustawienia uprawnień dla zarejestrowanej aplikacji](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Wybierz **pozycję wymagane uprawnienia** > **Dodaj** > **Wybierz interfejs API** > **Azure SQL Database**.

    ![Dodawanie dostępu do interfejsu API dla Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Wybierz pozycję >  **dostęp do interfejsu API** **Wybierz uprawnienia** > **delegowane uprawnienia**.

    ![Delegowanie uprawnień do interfejsu API dla Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Ustawianie administratora usługi Azure AD dla serwera SQL Database

Aby C# program mógł zostać uruchomiony, administrator programu Azure SQL Server musi przypisać administratora usługi Azure AD dla serwera SQL Database. 

Na stronie **SQL Server** wybierz pozycję > Active Directory administrator**Ustaw administratora**.

Więcej informacji o administratorach i użytkownikach usługi Azure AD dla Azure SQL Database można znaleźć w temacie zrzuty ekranu w artykule [Konfigurowanie i zarządzanie Azure Active Directory uwierzytelnianiem przy użyciu SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Dodawanie użytkownika niebędącego administratorem do określonej bazy danych (opcjonalnie)

Administrator usługi Azure AD dla serwera SQL Database może uruchomić C# Przykładowy program. Użytkownik usługi Azure AD może uruchomić ten program, jeśli znajdują się w bazie danych. Administrator SQL usługi Azure AD lub użytkownik usługi Azure AD, który istnieje już w bazie danych i ma `ALTER ANY USER` uprawnienia do bazy danych, może dodać użytkownika.

Możesz dodać użytkownika do bazy danych za pomocą polecenia SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) . Może to być na przykład `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Aby uzyskać więcej informacji, zobacz [używanie uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania przy użyciu SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Nowa wartość wyliczenia uwierzytelniania

C# Przykład opiera się na [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) przestrzeni nazw. Ze względu na to, że uwierzytelnianie wieloskładnikowe jest wyliczeniem `SqlAuthenticationMethod`, które ma następujące wartości:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Użyj tej wartości przy użyciu nazwy użytkownika usługi Azure AD w celu zaimplementowania uwierzytelniania wieloskładnikowego. Ta wartość jest skoncentrowana na obecnym artykule. Zapewnia interaktywne środowisko, wyświetlając okna dialogowe hasła użytkownika, a następnie w celu weryfikacji uwierzytelniania wieloskładnikowego w przypadku nałożenia uwierzytelniania wieloskładnikowego na tym użytkowniku. Ta wartość jest dostępna od .NET Framework w wersji 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Użyj tej wartości dla konta *federacyjnego* . W przypadku konta federacyjnego nazwa użytkownika jest znana dla domeny systemu Windows. Ta metoda uwierzytelniania nie obsługuje uwierzytelniania wieloskładnikowego.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Użyj tej wartości do uwierzytelniania, które wymaga nazwy użytkownika i hasła usługi Azure AD. Azure SQL Database wykonuje uwierzytelnianie. Ta metoda nie obsługuje uwierzytelniania wieloskładnikowego.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Ustawianie C# wartości parametrów z Azure Portal

Aby C# program mógł zostać pomyślnie uruchomiony, należy przypisać odpowiednie wartości do pól statycznych. Pokazane tutaj są pola z przykładowymi wartościami. Pokazane są również lokalizacje Azure Portal, w których można uzyskać potrzebne wartości.

| Nazwa pola statycznego | Przykładowa wartość | Gdzie w Azure Portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Filtry serwerów** > SQL**według nazwy** |
| AzureAD_UserID | "auser\@ABC.onmicrosoft.com" | **Użytkownik Azure Active Directory** > **nowy użytkownik-Gość** >  |
| Initial_DatabaseName | "baza danych" | **SQL Server** > —**bazy danych SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** **rejestracje aplikacji wyszukiwania według**nazwyaplikacji > identyfikator >  >  |
| RedirectUri | nowy identyfikator URI (https://mywebserver.com/"") | **Azure Active Directory** > rejestracjeaplikacjiwyszukiwaniawedług > nazwy[ > Twoje ustawienia*aplikacji — Rejestracja] RedirectURIs* >  > <br /><br />W tym artykule wszystkie prawidłowe wartości są poprawne dla RedirectUri, ponieważ nie są używane w tym miejscu. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Weryfikuj przy użyciu SQL Server Management Studio

Przed uruchomieniem C# programu warto sprawdzić, czy konfiguracja i konfiguracje są poprawne w SQL Server Management Studio (SSMS). Wszelkie C# błędy programu można następnie zawęzić do kodu źródłowego.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Weryfikowanie SQL Database adresów IP zapory

Uruchom narzędzie SSMS z tego samego komputera, w tym samym budynku, w którym planujesz uruchomić C# program. W przypadku tego testu każdy tryb **uwierzytelniania** jest prawidłowy. Jeśli istnieją jakieś oznaki, że Zapora serwera bazy danych nie akceptuje adresu IP, zobacz [Azure SQL Database reguły zapory na poziomie serwera i na poziomie bazy danych](sql-database-firewall-configure.md) , aby uzyskać pomoc.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Weryfikowanie Azure Active Directory usługi uwierzytelniania wieloskładnikowego

Ponownie uruchom program SSMS, tym razem z uwierzytelnianiem ustawionym na **Active Directory — uniwersalna z obsługą usługi MFA**. Ta opcja wymaga programu SSMS w wersji 17,5 lub nowszej.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania wieloskładnikowego dla programu SSMS i usługi Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Jeśli jesteś użytkownikiem gościa w bazie danych, musisz również podać nazwę domeny usługi Azure AD dla bazy danych: Wybierz pozycję **Opcje** > **nazwa domeny usługi AD lub identyfikator dzierżawy**. Aby znaleźć nazwę domeny w Azure Portal, wybierz pozycję **Azure Active Directory** > **niestandardowych nazw domen**. W C# przykładzie programu podanie nazwy domeny nie jest konieczne.

## <a name="c-code-example"></a>C#przykład kodu

Przykładowy C# program opiera się na zestawie dll [Microsoft. IdentityModel. clients. ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) .

Aby zainstalować ten pakiet, w programie Visual Studio wybierz pozycję **projekt** > **Zarządzaj pakietami NuGet**. Wyszukaj i zainstaluj **Microsoft. IdentityModel. clients. ActiveDirectory**.

Jest to przykładowy kod C# źródłowy.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Jest to przykład danych wyjściowych C# testu.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
