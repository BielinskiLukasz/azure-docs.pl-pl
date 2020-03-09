---
title: Korzystanie z uwierzytelniania wieloskładnikowego usługi AAD
description: Azure SQL Database i usługi Azure Synapse obsługują połączenia z SQL Server Management Studio (SSMS) przy użyciu uwierzytelniania uniwersalnego Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358389"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>Korzystanie z uwierzytelniania wieloskładnikowego w usłudze AAD przy użyciu Azure SQL Database i usługi Azure Synapse Analytics (Obsługa programu SSMS)
Azure SQL Database i usługi Azure Synapse obsługują połączenia z SQL Server Management Studio (SSMS) przy użyciu *uwierzytelniania uniwersalnego Active Directory*. W tym artykule omówiono różnice między różnymi opcjami uwierzytelniania, a także ograniczenia związane z uwierzytelnianiem uniwersalnym. 

**Pobierz najnowszy program SSMS** — na komputerze klienckim Pobierz najnowszą wersję programu ssms z [SQL Server Management Studio pobierania (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Aby zapoznać się ze wszystkimi funkcjami omówionymi w tym artykule, należy użyć co najmniej 2017 lipca w wersji 17,2.  Ostatnie połączenie okno dialogowe powinno wyglądać podobnie do poniższej ilustracji:
 
  ![1mfa — połączenie uniwersalne](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Kończy działanie pola Nazwa użytkownika.")  

## <a name="the-five-authentication-options"></a>Pięć opcji uwierzytelniania  

Active Directory uniwersalne uwierzytelnianie obsługuje dwie nieinteraktywne metody uwierzytelniania:
    - Uwierzytelnianie `Active Directory - Password`
    - Uwierzytelnianie `Active Directory - Integrated`

Istnieją również dwa nieinteraktywne modele uwierzytelniania, które mogą być używane w wielu różnych aplikacjach (ADO.NET, JDCB, ODC itp.). Te dwie metody nigdy nie powodują wyskakujących okien dialogowych: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Interaktywna Metoda obejmuje również obsługę uwierzytelniania wieloskładnikowego (MFA) w usłudze Azure: 
- `Active Directory - Universal with MFA` 


Usługa Azure MFA zabezpiecza dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia silne uwierzytelnianie dzięki szerokiemu zakresowi opcji łatwej weryfikacji (połączenie telefoniczne, wiadomość tekstowa, kartach inteligentnych z numerem PIN lub powiadomieniem aplikacji mobilnej), co pozwala użytkownikom na wybranie preferowanej metody. Interaktywna usługa MFA z usługą Azure AD może spowodować wyskakujące okno dialogowe umożliwiające weryfikację.

Opis Multi-Factor Authentication można znaleźć w temacie [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Aby uzyskać instrukcje dotyczące konfiguracji, zobacz [konfigurowanie Azure SQL Database usługi uwierzytelniania wieloskładnikowego dla SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nazwa domeny usługi Azure AD lub parametr identyfikatora dzierżawy   

Począwszy od programu [SSMS w wersji 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), użytkownicy, którzy zostali zaimportowani do bieżącego Active Directory z innych katalogów usługi Azure Active Directory jako gość, mogą podać nazwę domeny usługi Azure AD lub identyfikator dzierżawy podczas nawiązywania połączenia. Użytkownicy-Goście obejmują użytkowników zaproszonych z innych reklam systemu Azure, kont Microsoft, takich jak outlook.com, hotmail.com, live.com lub innych kont, takich jak gmail.com. Te informacje umożliwiają **Active Directory uniwersalnego z uwierzytelnianiem MFA** w celu zidentyfikowania prawidłowego urzędu uwierzytelniającego. Ta opcja jest również wymagana do obsługi kont Microsoft (MSA), takich jak outlook.com, hotmail.com, live.com lub non-MSA. Wszyscy ci użytkownicy, którzy chcą być uwierzytelniani przy użyciu uwierzytelniania uniwersalnego, muszą wprowadzić nazwę domeny usługi Azure AD lub identyfikator dzierżawy. Ten parametr reprezentuje bieżącą nazwę domeny i identyfikator dzierżawy usługi Azure AD, z którą jest połączony serwer platformy Azure. Na przykład jeśli serwer platformy Azure jest skojarzony z domeną usługi Azure AD `contosotest.onmicrosoft.com` w przypadku, gdy `joe@contosodev.onmicrosoft.com` użytkownika jest hostowany jako zaimportowany użytkownik z domeny usługi Azure AD `contosodev.onmicrosoft.com`, nazwa domeny wymagana do uwierzytelnienia tego użytkownika jest `contosotest.onmicrosoft.com`. Jeśli użytkownik jest natywnym użytkownikiem usługi Azure AD połączonej z serwerem platformy Azure i nie jest kontem MSA, nie jest wymagana żadna nazwa domeny ani identyfikator dzierżawy. Aby wprowadzić parametr (począwszy od programu SSMS w wersji 17,2), w oknie dialogowym **łączenie z bazą danych** wypełnij okno dialogowe, wybierając **Active Directory-uniwersalne z** uwierzytelnianiem MFA, kliknij **przycisk Opcje**, Wypełnij pole **Nazwa użytkownika** , a następnie kliknij kartę **Właściwości połączenia** . Sprawdź **nazwę domeny usługi AD lub identyfikator dzierżawy** , a następnie podaj urząd uwierzytelniający, taki jak nazwa domeny (**contosotest.onmicrosoft.com**) lub identyfikator dzierżawy.  
   ![MFA-dzierżawca — SSMS](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

Jeśli używasz programu SSMS 18. x lub nowszego, nazwa domeny lub identyfikator dzierżawy usługi AD nie są już potrzebne dla użytkowników-Gości, ponieważ program automatycznie rozpoznaje ją.

   ![MFA — dzierżawca — SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Obsługa usługi Azure AD Business dla firm   
Użytkownicy usługi Azure AD obsługiwani w scenariuszach B2B usługi Azure AD jako użytkownicy-Goście (Zobacz artykuł [co to jest współpraca z usługą Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) mogą łączyć się z SQL Database i Azure Synapse tylko jako część elementów członkowskich grupy utworzonej w bieżącej usłudze Azure AD i zamapowana ręcznie przy użyciu instrukcji Transact-SQL `CREATE USER` w danej bazie danych. Na przykład jeśli `steve@gmail.com` zostanie zaproszony do `contosotest` usługi Azure AD (z domeną usługi Azure AD `contosotest.onmicrosoft.com`), Grupa usługi Azure AD, taka jak `usergroup`, musi zostać utworzona w usłudze Azure AD, która zawiera `steve@gmail.com` członka. Następnie należy utworzyć tę grupę dla konkretnej bazy danych (to jest baza danych) za pomocą administratora SQL usługi Azure AD lub roli DBO usługi Azure AD przez wykonanie instrukcji języka Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER`. Po utworzeniu użytkownika bazy danych `steve@gmail.com` użytkownik może zalogować się do `MyDatabase` przy użyciu opcji uwierzytelniania programu SSMS `Active Directory – Universal with MFA support`. Domyślnie użytkownik ma tylko uprawnienie łączenie i dalsze dostęp do danych, które będą musiały zostać przyznane w zwykły sposób. Należy zauważyć, że użytkownik `steve@gmail.com` jako użytkownik będący gościem musi zaznaczyć pole wyboru i dodać nazwę domeny usługi AD `contosotest.onmicrosoft.com` w oknie dialogowym **Właściwości połączenia** programu SSMS. Opcja **nazwy domeny lub identyfikatora dzierżawy usługi Active Directory** jest obsługiwana tylko dla opcji połączenia uniwersalnego z usługą MFA, w przeciwnym razie jest wyszarzona.

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>Ograniczenia uniwersalnego uwierzytelniania dla SQL Database i platformy Azure Synapse
- Narzędzie SSMS i sqlpackage. exe są jedynymi narzędziami obecnie włączonymi dla usługi MFA za pomocą uwierzytelniania uniwersalnego Active Directory.
- Program SSMS w wersji 17,2 obsługuje współbieżne dostęp użytkowników przy użyciu uwierzytelniania uniwersalnego w ramach usługi MFA. Wersje 17,0 i 17,1, ograniczone logowanie do wystąpienia programu SSMS przy użyciu uwierzytelniania uniwersalnego na pojedynczym koncie Azure Active Directory. Aby zalogować się jako inne konto usługi Azure AD, musisz użyć innego wystąpienia programu SSMS. To ograniczenie jest ograniczone do Active Directory uwierzytelniania uniwersalnego. możesz zalogować się do różnych serwerów przy użyciu uwierzytelniania za pomocą hasła Active Directory, Active Directory uwierzytelniania zintegrowanego lub SQL Server uwierzytelniania).
- Program SSMS obsługuje uwierzytelnianie uniwersalne Active Directory w przypadku Eksplorator obiektów, edytora zapytań i wizualizacji magazynu zapytań.
- Program SSMS w wersji 17,2 zapewnia obsługę kreatora DacFx dla bazy danych eksport/Extract/Deploy. Gdy określony użytkownik zostanie uwierzytelniony za pośrednictwem okna dialogowego uwierzytelniania początkowego przy użyciu uwierzytelniania uniwersalnego, Kreator DacFx działa tak samo jak w przypadku wszystkich innych metod uwierzytelniania.
- Projektant tabel programu SSMS nie obsługuje uwierzytelniania uniwersalnego.
- Nie ma dodatkowych wymagań dotyczących oprogramowania dla Active Directory uwierzytelniania uniwersalnego, z tą różnicą, że należy użyć obsługiwanej wersji programu SSMS.  
- Wersja Active Directory Authentication Library (ADAL) dla uwierzytelniania uniwersalnego została zaktualizowana do najnowszej udostępnionej wersji biblioteki ADAL. dll 3.13.9. Zobacz [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać instrukcje dotyczące konfiguracji, zobacz [konfigurowanie Azure SQL Database usługi uwierzytelniania wieloskładnikowego dla SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Przyznaj innym osobom dostęp do bazy danych: [SQL Database uwierzytelniania i autoryzacji: udzielanie dostępu](sql-database-manage-logins.md)  
- Upewnij się, że inne osoby mogą łączyć się za pośrednictwem zapory: [Skonfiguruj regułę zapory na poziomie serwera Azure SQL Database przy użyciu Azure Portal](sql-database-configure-firewall-settings.md)  
- [Konfigurowanie i Zarządzanie uwierzytelnianiem Azure Active Directory za pomocą SQL Database lub Azure Synapse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [Sqlpackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importowanie pliku BACPAC do nowej bazy danych Azure SQL Database](../sql-database/sql-database-import.md)  
- [Eksportowanie bazy danych Azure SQL Database do pliku BACPAC](../sql-database/sql-database-export.md)  
- C#Interfejs [IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx) interfejsu  
- W przypadku korzystania **z Active Directory — uniwersalne z** uwierzytelnianiem MFA, śledzenie ADAL jest dostępne od programu [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Domyślnie wyłączone jest śledzenie ADAL przy użyciu **narzędzi**, menu **opcji** , w obszarze **usługi platformy Azure**, **Azure Cloud**, **ADAL okno dane wyjściowe poziom śledzenia**, a następnie przez włączenie **danych wyjściowych** w menu **Widok** . Ślady są dostępne w oknie danych wyjściowych w przypadku wybrania **opcji Azure Active Directory**.  
