---
title: Udostępniona baza danych
description: Usługa Azure Synapse Analytics oferuje współużytkowany model metadanych, przy użyciu którego tworzenie bazy danych w Apache Spark spowoduje udostępnienie jej z poziomu usług SQL na żądanie (wersja zapoznawcza) i aparatów puli SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 58c1aea944d89872a79d0672a925b1696791c1a8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260856"
---
# <a name="azure-synapse-analytics-shared-database"></a>Udostępniona baza danych usługi Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia różnym aparatom obszarów roboczych obliczeniowych udostępnianie baz danych i tabel między jej pulami usługi Spark (wersja zapoznawcza) i aparatem SQL na żądanie (wersja zapoznawcza).

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Baza danych utworzona za pomocą zadania platformy Spark będzie widoczna z tą samą nazwą dla wszystkich bieżących i przyszłych pul platformy Spark (wersja zapoznawcza) w obszarze roboczym, w tym aparatu SQL na żądanie.

Domyślna baza danych Spark, wywołana `default` , będzie również widoczna w kontekście SQL na żądanie jako baza danych o nazwie `default` .

Ze względu na to, że bazy danych są synchronizowane z usługą SQL na żądanie asynchronicznie, nastąpi opóźnienie do momentu ich wyświetlenia.

## <a name="manage-a-spark-created-database"></a>Zarządzanie bazą danych utworzoną przez platformę Spark

Użyj platformy Spark do zarządzania utworzonymi bazami danych platformy Spark. Można na przykład usunąć ją za pośrednictwem zadania puli platformy Spark i utworzyć w niej tabele z platformy Spark.

W przypadku tworzenia obiektów w bazie danych platformy Spark przy użyciu programu SQL na żądanie lub próby porzucenia bazy danych operacja zakończy się pomyślnie. Jednak oryginalna baza danych Spark nie zostanie zmieniona.

## <a name="how-name-conflicts-are-handled"></a>Jak są obsługiwane konflikty nazw

Jeśli nazwa bazy danych Spark powoduje konflikt z nazwą istniejącej bazy danych SQL na żądanie, sufiks jest dołączany na żądanie SQL do bazy danych Spark. Sufiks w SQL na żądanie ma wartość `_<workspace name>-ondemand-DefaultSparkConnector` .

Na przykład jeśli baza danych Spark o nazwie Get została `mydb` utworzona w obszarze roboczym usługi Azure Synapse, `myws` a baza danych SQL na żądanie o tej nazwie już istnieje, to baza danych Spark na żądanie będzie musiała zostać przywoływana przy użyciu nazwy `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Przestroga: nie należy podejmować zależności od tego zachowania.

## <a name="security-model"></a>Model zabezpieczeń

Bazy danych i tabele platformy Spark wraz z synchronizowanymi reprezentacjami w aparacie SQL zostaną zabezpieczone na podstawowym poziomie magazynu.

Podmiot zabezpieczeń, który tworzy bazę danych, jest traktowany jako właściciel tej bazy danych i ma wszystkie prawa do bazy danych i jej obiektów.

Aby udzielić podmiotu zabezpieczeń, takiego jak użytkownik lub grupa zabezpieczeń, uzyskać dostęp do bazy danych, należy udostępnić plik w formacie POSIX i uprawnienia do plików w katalogu i plikach `warehouse` . 

Na przykład, aby podmiot zabezpieczeń mógł odczytać tabelę w bazie danych, wszystkie foldery, które rozpoczynają się w folderze bazy danych w katalogu, `warehouse` muszą mieć `X` `R` przypisane uprawnienia i uprawnień do tego podmiotu zabezpieczeń. Ponadto pliki (takie jak bazowe pliki danych tabeli) wymagają `R` uprawnień. 

Jeśli podmiot zabezpieczeń wymaga możliwości tworzenia obiektów lub upuszczania obiektów w bazie danych, `W` wymagane są dodatkowe uprawnienia do folderów i plików w `warehouse` folderze.

## <a name="examples"></a>Przykłady

### <a name="create-and-connect-to-spark-database-with-sql-on-demand"></a>Tworzenie bazy danych Spark i nawiązywanie z nią połączenia na żądanie SQL

Najpierw utwórz nową bazę danych Spark o nazwie `mytestdb` przy użyciu klastra Spark, który został już utworzony w obszarze roboczym. Można to zrobić na przykład przy użyciu notesu platformy Spark w języku C# z następującą instrukcją programu .NET for Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Po krótkim opóźnieniu można zobaczyć bazę danych z usługi SQL na żądanie. Na przykład Uruchom poniższą instrukcję z poziomu usługi SQL na żądanie.

```sql
SELECT * FROM sys.databases;
```

Sprawdź, czy `mytestdb` jest uwzględniony w wynikach.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o udostępnionych metadanych usługi Azure Synapse Analytics](overview.md)
- [Dowiedz się więcej o udostępnionych tabelach metadanych usługi Azure Synapse Analytics](table.md)
