---
title: Kluczowe różnice dotyczące Machine Learning Services (wersja zapoznawcza)
description: W tym temacie opisano kluczowe różnice między Azure SQL Database Machine Learning Services (z R) i SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 1397f5d81ddf63740d733111b965a0517a2b917f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827467"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Kluczowe różnice między Machine Learning Services w Azure SQL Database (wersja zapoznawcza) i SQL Server

Funkcje Azure SQL Database Machine Learning Services (z językiem R) w (wersja zapoznawcza) są podobne do [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Poniżej przedstawiono niektóre kluczowe różnice.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Obsługa języków

SQL Server obsługuje język R i Python za pomocą [struktury rozszerzalności](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nie obsługuje obu języków. Kluczowe różnice są następujące:

- R jest jedynym obsługiwanym językiem w SQL Database. W tej chwili język Python nie jest obsługiwany.
- Wersja języka R to 3.4.4.
- Nie ma potrzeby konfigurowania `external scripts enabled` za pośrednictwem `sp_configure`. Po [zarejestrowaniu](sql-database-machine-learning-services-overview.md#signup)usługi Machine Learning jest włączona dla bazy danych SQL.

## <a name="package-management"></a>Zarządzanie pakietami

Zarządzanie pakietami i instalacją w języku R działa inaczej niż SQL Database i SQL Server. Te różnice są następujące:

- Pakiety języka R są instalowane za pośrednictwem [sqlmlutils](https://github.com/Microsoft/sqlmlutils) lub [Create External Library](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Pakiety nie mogą wykonywać wychodzących wywołań sieciowych. To ograniczenie jest podobne do [domyślnych reguł zapory dla Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) w SQL Server, ale nie można go zmienić w SQL Database.
- Pakiety, które są zależne od zewnętrznych środowisk uruchomieniowych (na przykład Java) lub potrzebują dostępu do interfejsów API systemu operacyjnego w celu instalacji lub użycia, nie są obsługiwane.

## <a name="writing-to-a-temporary-table"></a>Zapisywanie w tabeli tymczasowej

Jeśli używasz RODBC w Azure SQL Database, nie możesz zapisywać w tabeli tymczasowej, niezależnie od tego, czy jest ona utworzona w sesji `sp_execute_external_script`, czy poza nią. Obejście polega na użyciu [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) i [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (z zastępowaniem = false i dołączeniem = "wiersze") do zapisu w globalnej tabeli tymczasowej utworzonej przed kwerendą `sp_execute_external_script`.

## <a name="resource-governance"></a>Nadzór nad zasobami

Nie jest możliwe ograniczanie zasobów R przy użyciu [regulatora zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) i pul zasobów zewnętrznych.

W publicznej wersji zapoznawczej zasoby R są ustawione na maksymalnie 20% zasobów SQL Database i zależą od wybranej warstwy usług. Aby uzyskać więcej informacji, zobacz [Azure SQL Database modele zakupu](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Błąd niewystarczającej pamięci

Jeśli jest za mało dostępnej pamięci dla języka R, zostanie wyświetlony komunikat o błędzie. Typowe komunikaty o błędach to:

- Nie można nawiązać komunikacji ze środowiskiem uruchomieniowym dla skryptu "R" dla identyfikatora żądania: * * * * * * *. Sprawdź wymagania środowiska uruchomieniowego "R"
- Wystąpił błąd skryptu "R" podczas wykonywania elementu "sp_execute_external_script" z wartością HRESULT 0x80004004. ... Wystąpił zewnętrzny błąd skryptu: ".. nie można przydzielić pamięci (0 MB) w funkcji C "R_AllocStringBuffer" "
- Wystąpił błąd zewnętrzny skryptu: błąd: nie można przydzielić wektora rozmiaru.

Użycie pamięci zależy od tego, ile jest używane w skryptach języka R i liczby wykonywanych zapytań równoległych. Jeśli otrzymasz powyższe błędy, możesz skalować bazę danych do wyższej warstwy usług, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

- Zobacz Omówienie [Azure SQL Database Machine Learning Services w języku R (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md).
- Aby dowiedzieć się, jak używać języka R do wykonywania zapytań w Azure SQL Database Machine Learning Services (wersja zapoznawcza), zobacz [Przewodnik Szybki Start](sql-database-connect-query-r.md).
- Aby zacząć korzystać z niektórych prostych skryptów języka R, zobacz [Tworzenie i Uruchamianie prostych skryptów języka r w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md).
