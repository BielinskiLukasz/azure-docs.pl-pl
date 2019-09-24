---
title: Tworzenie użytkowników w Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób tworzenia nowych kont użytkowników w celu współdziałania z serwerem Azure Database for PostgreSQL-pojedynczym.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 91ba485347aeb19ce9b173bd4cec944a655a56dc
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203501"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Tworzenie użytkowników w Azure Database for PostgreSQL — pojedynczy serwer
W tym artykule opisano, jak można tworzyć użytkowników w ramach serwera Azure Database for PostgreSQL. 

Jeśli chcesz dowiedzieć się, jak tworzyć użytkowników subskrypcji platformy Azure i zarządzać nimi, możesz odwiedzić [artykuł kontroli dostępu opartej na rolach (RBAC) na platformie Azure](../role-based-access-control/built-in-roles.md) lub zapoznać [się z tematem jak dostosować role](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Konto administratora serwera
Podczas pierwszego tworzenia Azure Database for PostgreSQL podano nazwę użytkownika i hasło administratora serwera. Aby uzyskać więcej informacji, możesz skorzystać z [przewodnika Szybki Start](quickstart-create-server-database-portal.md) , aby zobaczyć podejście krok po kroku. Ponieważ nazwa użytkownika administratora serwera jest nazwą niestandardową, można zlokalizować wybraną nazwę użytkownika administratora serwera z Azure Portal.

Serwer Azure Database for PostgreSQL jest tworzony z trzema zdefiniowanymi rolami domyślnymi. Te role można wyświetlić, uruchamiając polecenie:`SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- Administrator serwera

Administrator serwera jest członkiem roli azure_pg_admin. Jednak konto administratora serwera nie jest częścią roli azure_superuser. Ponieważ ta usługa jest zarządzaną usługą PaaS, tylko firma Microsoft jest częścią roli administratora. 

Aparat PostgreSQL używa uprawnień do kontrolowania dostępu do obiektów bazy danych, jak to opisano w [dokumentacji produktu PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). W Azure Database for PostgreSQL administrator serwera otrzymuje następujące uprawnienia: LOGOWANIE, NOSUPERUSER, DZIEDZICZENIE, CREATEDB, ROLA, NOREPLICATION

Konta administratora serwera można użyć do utworzenia dodatkowych użytkowników i przyznania tych użytkowników do roli azure_pg_admin. Ponadto konto administratora serwera może służyć do tworzenia mniej uprzywilejowanych użytkowników i ról, które mają dostęp do poszczególnych baz danych i schematów.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Jak utworzyć dodatkowych użytkowników administracyjnych w Azure Database for PostgreSQL
1. Pobierz informacje o połączeniu i nazwa użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo znaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera lub stronie **Właściwości** w Azure Portal. 

2. Aby nawiązać połączenie z serwerem bazy danych, użyj konta administratora i hasła. Skorzystaj z preferowanego narzędzia klienckiego, takiego jak pgAdmin lub PSQL.
   Jeśli nie wiesz, jak nawiązać połączenie, zobacz [Przewodnik Szybki Start](./quickstart-create-server-database-portal.md)

3. Edytuj i uruchom następujący kod SQL. Zamień nową nazwę użytkownika na wartość symbolu zastępczego < new_user > i Zastąp hasło zastępcze własnym silnym hasłem. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Jak utworzyć użytkowników bazy danych w Azure Database for PostgreSQL

1. Pobierz informacje o połączeniu i nazwa użytkownika administratora.
   Aby można było nawiązać połączenie z serwerem bazy danych, potrzebne są pełna nazwa serwera i poświadczenia logowania administratora. Możesz łatwo znaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera lub stronie **Właściwości** w Azure Portal. 

2. Aby nawiązać połączenie z serwerem bazy danych, użyj konta administratora i hasła. Skorzystaj z preferowanego narzędzia klienckiego, takiego jak pgAdmin lub PSQL.

3. Edytuj i uruchom następujący kod SQL. Zastąp wartość `<db_user>` symbolu zastępczego zamierzoną nową nazwą użytkownika i wartość `<newdb>` symbolu zastępczego własną nazwą bazy danych. Zastąp hasło symbolem zastępczym własnym silnym hasłem. 

   Ta składnia kodu SQL tworzy nową bazę danych o nazwie TestDB, na przykład. Następnie tworzy nowego użytkownika w usłudze PostgreSQL i przyznaje uprawnienia do łączenia się z nową bazą danych dla tego użytkownika. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Korzystając z konta administratora, może być konieczne przyznanie dodatkowych uprawnień do zabezpieczania obiektów w bazie danych. Więcej informacji na temat ról i uprawnień bazy danych znajduje się w [dokumentacji PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) . Na przykład: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Zaloguj się na serwerze, określając wydaną bazę danych przy użyciu nowej nazwy użytkownika i hasła. Ten przykład pokazuje wiersz polecenia PSQL. Za pomocą tego polecenia zostanie wyświetlony monit o podanie hasła dla nazwy użytkownika. Zastąp własną nazwę serwera, nazwę bazy danych i nazwę użytkownika.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Następne kroki
Otwórz Zaporę dla adresów IP nowych maszyn użytkowników, aby umożliwić im nawiązanie połączenia: [Tworzenie reguł zapory Azure Database for PostgreSQL i zarządzanie nimi za pomocą Azure Portal](howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).

Aby uzyskać więcej informacji dotyczących zarządzania kontami użytkowników, zobacz dokumentację produktu PostgreSQL dla [ról i uprawnień bazy danych](https://www.postgresql.org/docs/current/static/user-manag.html), [składnię](https://www.postgresql.org/docs/current/static/sql-grant.html)i [uprawnienia](https://www.postgresql.org/docs/current/static/ddl-priv.html).
