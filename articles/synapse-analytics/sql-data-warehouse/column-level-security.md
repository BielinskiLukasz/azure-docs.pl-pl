---
title: Co to jest zabezpieczenia na poziomie kolumny dla platformy Azure Synapse?
description: Zabezpieczenia na poziomie kolumny umożliwiają klientom kontrolowanie dostępu do kolumn tabel bazy danych na podstawie kontekstu wykonania lub członkostwa w grupie użytkownika, upraszczając projektowanie i kodowanie zabezpieczeń w aplikacji oraz umożliwiając wdrożenie ograniczeń dotyczących kolumny Dostęp.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 24ead458232b096a5c69ffe8b45c6298a9da9f75
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349092"
---
# <a name="column-level-security"></a>Zabezpieczenia na poziomie kolumny

Zabezpieczenia na poziomie kolumny umożliwiają klientom kontrolowanie dostępu do kolumn tabel na podstawie kontekstu wykonywania lub członkostwa w grupie użytkownika.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Ponieważ ten film wideo został opublikowany [poziom wiersza zabezpieczenia](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) stały się dostępne dla platformy Azure Synapse. 

Zabezpieczenia na poziomie kolumny upraszcza projektowanie i kodowanie zabezpieczeń w aplikacji, co pozwala ograniczyć dostęp do kolumn w celu ochrony poufnych danych. Na przykład zapewnienie, że określone użytkownicy mogą uzyskać dostęp tylko niektóre kolumny tabeli istotne dla ich działu. Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie z dala od danych w innej warstwie aplikacji. Baza danych stosuje ograniczenia dostępu za każdym razem, gdy próbowano uzyskać dostęp do danych z dowolnej warstwy. To ograniczenie sprawia, że bezpieczeństwo jest bardziej niezawodne i niezawodne dzięki zmniejszeniu powierzchni całego systemu zabezpieczeń. Ponadto zabezpieczenia na poziomie kolumny eliminują również konieczność wprowadzania widoków w celu odfiltrowania kolumn w celu nałożenia ograniczeń dostępu na użytkowników.

Można zaimplementować zabezpieczenia na poziomie kolumny za pomocą [instrukcji GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL. Dzięki temu mechanizmowi obsługiwane jest uwierzytelnianie SQL i Azure Active Directory (AAD).

![Cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Składnia

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Przykład
W poniższym przykładzie `TestUser` pokazano, `SSN` jak ograniczyć dostęp do kolumny `Membership` tabeli:

Utwórz `Membership` tabelę z kolumną SSN używaną do przechowywania numerów ubezpieczenia społecznego:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Zezwalaj na `TestUser` dostęp do wszystkich kolumn z wyjątkiem kolumny SSN, która ma poufne dane:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Kwerendy wykonywane `TestUser` zgodnie z niepowodzeniem, jeśli zawierają kolumnę SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Przypadki użycia

Oto kilka przykładów użycia zabezpieczeń na poziomie kolumny:

- Firma świadcząca usługi finansowe zezwala tylko menedżerom kont na dostęp do numerów ubezpieczenia społecznego klientów (SSN), numerów telefonów i innych danych osobowych.
- Dostawca opieki zdrowotnej umożliwia tylko lekarzom i pielęgniarkom dostęp do wrażliwej dokumentacji medycznej, jednocześnie uniemożliwiając członkom działu rozliczeniowego przeglądanie tych danych.
