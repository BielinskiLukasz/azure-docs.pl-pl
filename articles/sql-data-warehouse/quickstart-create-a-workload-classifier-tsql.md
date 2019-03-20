---
title: 'Szybki start: Tworzenie klasyfikatora obciążenia — język T-SQL | Dokumentacja firmy Microsoft'
description: Użyj T-SQL do tworzenia klasyfikatora obciążenia o wysokiej ważności
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 3355f9068d35f29483e257e5c236275f76a1c9d0
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2019
ms.locfileid: "58008603"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql-preview"></a>Szybki start: Tworzenie klasyfikatora obciążenia przy użyciu języka T-SQL (wersja zapoznawcza)

W tym przewodniku Szybki Start szybko utworzysz klasyfikatora obciążenia o wysokiej ważności dla Dyrektora Generalnego Twojej organizacji. Klasyfikator to obciążenie będzie umożliwiać Dyrektor Naczelny zapytania mają pierwszeństwo względem innych zapytań o niższe ważności w kolejce.

> [!Note]
> Klasyfikacja obciążenia jest dostępny na Gen2 magazynu danych SQL.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!NOTE]
> Utworzenie bazy danych w usłudze SQL Data Warehouse może skutkować powstaniem nowej usługi płatnej.  Aby uzyskać więcej informacji, zobacz [Cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start założono, masz już bazę danych SQL data warehouse i czy masz uprawnienia bazy danych kontroli. Jeżeli chcesz utworzyć taki magazyn, skorzystaj z przewodnika [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Utwórz identyfikator logowania dla TheCEO

Utwórz nazwę logowania uwierzytelniania programu SQL Server w `master` bazy danych przy użyciu [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) dla "TheCEO".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-theceo-user-in-mysampledatawarehouse"></a>Utwórz w mySampleDataWarehouse TheCEO użytkownika

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier-for-theceo-with-high-importance"></a>Tworzenie klasyfikatora obciążenia dla TheCEO o wysokiej ważności

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Wyświetlanie istniejących klasyfikatorów

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Opłaty będą naliczane za jednostki magazynu danych i dane przechowywane w magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie korzystasz z magazynu danych. Po wstrzymaniu obliczeń, są naliczane tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, wznowić operacje obliczeniowe.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz swój magazyn danych.

    ![Oczyszczanie zasobów](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz **wstrzymać** przycisku. Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz **Start**.

3. Aby usunąć magazyn danych, więc nie są naliczane opłaty za obliczenia oraz magazynowanie, wybierz pozycję **Usuń**.

4. Aby usunąć utworzony serwer SQL, zaznacz **mynewserver-20180430.database.windows.net** w poprzednim obrazie, a następnie wybierz pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz **myResourceGroup**, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Kolejne kroki

Klasyfikator obciążenia został już utworzony. Uruchom kilka zapytań jako TheCEO, aby sprawdzać ich działanie. Zobacz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) służy do wyświetlania kwerend i ważność przypisana.

Aby uzyskać więcej informacji na temat zarządzania obciążenia usługa SQL Data Warehouse, zobacz [ważności obciążenia magazynu danych SQL](sql-data-warehouse-workload-importance.md) i [klasyfikacji obciążenia magazynu danych SQL](sql-data-warehouse-workload-classification.md).
