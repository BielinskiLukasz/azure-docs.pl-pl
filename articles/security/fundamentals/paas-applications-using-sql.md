---
title: Zabezpieczanie bazy danych PaaS na platformie Azure | Microsoft Docs
description: 'Dowiedz się więcej o Azure SQL Database i SQL Data Warehouse najlepszych rozwiązaniach dotyczących zabezpieczeń w zakresie zabezpieczania aplikacji internetowych i mobilnych PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 1b0a4627d377f5fa9ca997d1cc96bc38b0a6c37f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217220"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Najlepsze rozwiązania dotyczące zabezpieczania baz danych PaaS na platformie Azure

W tym artykule omówiono zbieranie [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) i [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) najlepszych rozwiązań z zakresu bezpieczeństwa na potrzeby zabezpieczania aplikacji sieci Web platformy jako usługi (PaaS). Te najlepsze rozwiązania wynikają z naszych rozwiązań związanych z platformą Azure i klientami.

Azure SQL Database i SQL Data Warehouse zapewniają usługi relacyjnej bazy danych dla aplikacji internetowych. Przyjrzyjmy się usługom, które pomagają chronić aplikacje i dane przy użyciu Azure SQL Database i SQL Data Warehouse we wdrożeniu PaaS:

- Uwierzytelnianie Azure Active Directory (zamiast uwierzytelniania SQL Server)
- Zapora Azure SQL
- Transparent Data Encryption (TDE)

## <a name="use-a-centralized-identity-repository"></a>Używanie scentralizowanego repozytorium tożsamości
Bazy danych Azure SQL Database można skonfigurować do korzystania z jednego z dwóch typów uwierzytelniania:

- **Uwierzytelnianie SQL** używa nazwy użytkownika i hasła. Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń można uwierzytelniać się w dowolnej bazie danych na tym serwerze jako właściciel bazy danych.

- **Uwierzytelnianie Azure Active Directory** używa tożsamości zarządzanych przez Azure Active Directory i jest obsługiwane w przypadku domen zarządzanych i zintegrowanych. Aby użyć uwierzytelniania Azure Active Directory, należy utworzyć innego administratora serwera o nazwie "administrator usługi Azure AD", który może administrować użytkownikami i grupami usługi Azure AD. Ten administrator może również wykonywać wszystkie operacje, które może wykonać zwykły administrator serwera.

[Azure Active Directory uwierzytelnianiem](../../active-directory/develop/authentication-scenarios.md) jest mechanizm łączenia się z Azure SQL Database i SQL Data Warehouse przy użyciu tożsamości w Azure Active Directory (AD). Usługa Azure AD zapewnia alternatywę dla SQL Server uwierzytelniania, dzięki czemu można zatrzymać proliferację tożsamości użytkowników na serwerach baz danych. Uwierzytelnianie usługi Azure AD umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie IDENTYFIKATORami zapewnia pojedyncze miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Zalety korzystania z usługi Azure AD zamiast uwierzytelniania SQL
- Umożliwia obrót hasła w jednym miejscu.
- Zarządza uprawnieniami bazy danych przy użyciu zewnętrznych grup usługi Azure AD.
- Eliminuje przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure AD.
- Używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
- Obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z SQL Database.
- Obsługuje Federacji domeny z Active Directory Federation Services (ADFS) lub natywne uwierzytelnianie użytkownika/hasła dla lokalnej usługi Azure AD bez synchronizacji z domeną.
- Obsługuje połączenia z SQL Server Management Studio, które używają Active Directory uwierzytelniania uniwersalnego, w tym [Multi-Factor Authentication (MFA)](/azure/active-directory/authentication/multi-factor-authentication). Usługa MFA obejmuje silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji, takich jak połączenia telefoniczne, wiadomości SMS, karty inteligentne z numerem PIN lub powiadomienia przez aplikacje mobilne. Aby uzyskać więcej informacji, zobacz [uniwersalne uwierzytelnianie przy użyciu SQL Database i SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure AD, zobacz:

- [Użyj uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania z SQL Database, wystąpieniem zarządzanym lub SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md)
- [Authentication to Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-authentication.md) (Uwierzytelnianie w usłudze Azure SQL Data Warehouse)
- [Obsługa uwierzytelniania opartego na tokenach w usłudze Azure SQL DB przy użyciu uwierzytelniania usługi Azure AD](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Aby upewnić się, że Azure Active Directory jest dobrze dopasowane do danego środowiska, zobacz [funkcje i ograniczenia dotyczące usługi Azure AD](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Ograniczanie dostępu na podstawie adresu IP
Można utworzyć reguły zapory określające zakresy akceptowalnych adresów IP. Te reguły mogą być wskazywane zarówno na poziomie serwera, jak i bazy danych. Zalecamy używanie reguł zapory na poziomie bazy danych zawsze wtedy, gdy jest to możliwe, aby zwiększyć bezpieczeństwo i zwiększyć możliwości przenoszenia bazy danych. Reguły zapory na poziomie serwera najlepiej używać dla administratorów i gdy istnieje wiele baz danych, które mają takie same wymagania dotyczące dostępu, ale nie chcesz poświęcać czasu na konfigurowanie każdej bazy danych indywidualnie.

SQL Database domyślnych ograniczeń źródłowych adresów IP Zezwalaj na dostęp z dowolnego adresu platformy Azure, w tym innych subskrypcji i dzierżawców. Można to ograniczyć, aby zezwalać na dostęp do wystąpienia tylko Twoim adresom IP. Nawet w przypadku ograniczeń zapory SQL i adresów IP nadal jest potrzebne silne uwierzytelnianie. Zobacz zalecenia przedstawione wcześniej w tym artykule.

Aby dowiedzieć się więcej na temat ograniczeń dotyczących zapory i adresów IP usługi Azure SQL, zobacz:

- [Azure SQL Database i SQL Data Warehouse kontroli dostępu](../../sql-database/sql-database-manage-logins.md)
- [Reguły zapory Azure SQL Database i SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Szyfruj dane przechowywane
[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) jest domyślnie włączona. TDE w sposób przezroczysty szyfruje SQL Server, Azure SQL Database i Azure SQL Data Warehouse plików danych i dziennika. TDE chroni przed naruszeniem bezpośredniego dostępu do plików lub ich kopii zapasowych. Dzięki temu można szyfrować dane przechowywane bez zmieniania istniejących aplikacji. TDE powinna zawsze pozostać włączona; nie spowoduje to jednak zatrzymania osoby atakującej przy użyciu normalnej ścieżki dostępu. TDE zapewnia możliwość zapewnienia zgodności z wieloma przepisami, regulacjami i wytycznymi w różnych branżach.

Usługa Azure SQL zarządza problemami związanymi z kluczami dla TDE. Podobnie jak w przypadku TDE, należy podjąć specjalne uwagi, aby zapewnić możliwość odzyskiwania i przeniesieniu baz danych. W bardziej zaawansowanych scenariuszach klucze mogą być jawnie zarządzane w Azure Key Vault za pomocą rozszerzalnego zarządzania kluczami. Zobacz [Włączanie TDE na SQL Server przy użyciu EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Umożliwia to również Bring Your Own Key (BYOK) za pomocą usługi Azure Key Magazyns BYOK.

Usługa Azure SQL udostępnia szyfrowanie kolumn za pomocą [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Dzięki temu tylko autoryzowane aplikacje mogą uzyskiwać dostęp do poufnych kolumn. Użycie tego rodzaju szyfrowania ogranicza kwerendy SQL dla zaszyfrowanych kolumn do wartości na podstawie równości.

Szyfrowanie na poziomie aplikacji powinno być również używane na potrzeby danych selektywnych. Problemy z niezależnością danych można czasami ograniczyć, szyfrując dane przy użyciu klucza przechowywanego w prawidłowym kraju/regionie. Zapobiega to nawet przypadkowemu transferowi danych spowodowanym problemem, ponieważ nie można odszyfrować danych bez klucza, przy założeniu, że używany jest silny algorytm (na przykład AES 256).

Aby ułatwić Zabezpieczanie bazy danych, na przykład projektowania zabezpieczeń systemu, szyfrowania poufnych zasobów i tworzenia zapory wokół serwerów baz danych, można użyć dodatkowych środków ostrożności.

## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzono do kolekcji SQL Database i SQL Data Warehouse najlepszych rozwiązań w zakresie zabezpieczeń w celu zabezpieczania aplikacji internetowych i mobilnych PaaS. Aby dowiedzieć się więcej o zabezpieczaniu wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](paas-deployments.md)
- [Zabezpieczanie aplikacji PaaS Web i Mobile przy użyciu usługi Azure App Services](paas-applications-using-app-services.md)
