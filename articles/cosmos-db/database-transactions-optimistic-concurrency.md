---
title: Transakcje bazy danych i optymistyczna kontrola współbieżności w Azure Cosmos DB
description: W tym artykule opisano transakcje bazy danych i optymistyczną kontrolę współbieżności w Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b58255aa471fe78c84b5f6a7432c0f3d402f0875
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467903"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transakcje i optymistyczna kontrola współbieżności

Transakcje bazy danych zapewniają bezpieczny i przewidywalny model programowania do obsługi równoczesnych zmian w danych. Tradycyjne relacyjne bazy danych, takie jak SQL Server, umożliwiają pisanie logiki biznesowej za pomocą procedur składowanych i/lub wyzwalaczy, wysyłać je do serwera w celu wykonania bezpośrednio w aparacie bazy danych. W przypadku tradycyjnych relacyjnych baz danych wymagane jest zaradzenie sobie z dwoma różnymi językami programowania (nietransakcyjnymi) językami programowania aplikacji, takimi jak JavaScript, Python, C#Java itp. i transakcyjnym językiem programowania ( takie jak T-SQL), które są natywnie wykonywane przez bazę danych.

Aparat bazy danych w Azure Cosmos DB obsługuje transakcję zgodności z pełnym KWASem (niepodzielność, spójność, izolacja, trwałość) z izolacją migawki. Wszystkie operacje bazy danych w zakresie [partycji logicznej](partition-data.md) kontenera są transakcjami wykonywane w aparacie bazy danych hostowanym przez replikę partycji. Te operacje obejmują zarówno zapis (Aktualizacja co najmniej jednego elementu w ramach partycji logicznej), jak i operacje odczytu. W poniższej tabeli przedstawiono różne operacje i typy transakcji:

| **Operacja**  | **Typ operacji** | **Pojedyncza lub wieloelementowa transakcja** |
|---------|---------|---------|
| Insert (bez wyzwalacza wstępnego/końcowego) | Zapis | Transakcja pojedynczego elementu |
| Insert (z wyzwalaczem poprzedzającym/post) | Zapisz i przeczytaj | Transakcja Wieloelementowa |
| Replace (bez wyzwalacza wstępnego/końcowego) | Zapis | Transakcja pojedynczego elementu |
| Replace (z wyzwalaczem poprzedzającym/post) | Zapisz i przeczytaj | Transakcja Wieloelementowa |
| Upsert (bez wyzwalacza wstępnego/końcowego) | Zapis | Transakcja pojedynczego elementu |
| Upsert (z wyzwalaczem poprzedzającym/post) | Zapisz i przeczytaj | Transakcja Wieloelementowa |
| Usuń (bez wyzwalacza wstępnego/końcowego) | Zapis | Transakcja pojedynczego elementu |
| Usuń (z wyzwalaczem pre/post) | Zapisz i przeczytaj | Transakcja Wieloelementowa |
| Wykonaj procedurę składowaną | Zapisz i przeczytaj | Transakcja Wieloelementowa |
| System zainicjował wykonywanie procedury scalania | Zapis | Transakcja Wieloelementowa |
| System zainicjował wykonywanie usuwania elementów na podstawie wygaśnięcia (TTL) elementu | Zapis | Transakcja Wieloelementowa |
| Odczyt | Odczyt | Transakcja pojedynczego elementu |
| Zestawienie zmian | Odczyt | Transakcja Wieloelementowa |
| Odczyt podzielony na strony | Odczyt | Transakcja Wieloelementowa |
| Zapytanie z podziałem na strony | Odczyt | Transakcja Wieloelementowa |
| Wykonaj funkcję UDF w ramach zapytania z podziałem na strony | Odczyt | Transakcja Wieloelementowa |

## <a name="multi-item-transactions"></a>Transakcje wieloelementowe

Azure Cosmos DB pozwala pisać [procedury składowane, wyzwalacze pre/post, funkcje zdefiniowane przez użytkownika (UDF)](stored-procedures-triggers-udfs.md) i procedury scalania w języku JavaScript. Azure Cosmos DB natywnie obsługuje wykonywanie kodu JavaScript wewnątrz aparatu bazy danych. Można rejestrować procedury składowane, wyzwalacze pre/post, funkcje zdefiniowane przez użytkownika (UDF) oraz procedury scalania w kontenerze, a następnie wykonywać je w sposób niefunkcjonalny w aparacie bazy danych Cosmos Azure. Pisanie logiki aplikacji w języku JavaScript umożliwia naturalne wyrażenie przepływu sterowania, zmiennej określania zakresu, przydziału i integracji wyjątków dla elementów podstawowych w ramach transakcji bazy danych bezpośrednio w języku JavaScript.

Procedury składowane bazujące na języku JavaScript, wyzwalacze, UDF i procedury scalania są opakowane w obrębie transakcji otaczającej kwas z izolacją migawki dla wszystkich elementów w obrębie partycji logicznej. W trakcie wykonywania, jeśli program JavaScript zgłosi wyjątek, cała transakcja zostanie przerwana i wycofana. Model programowania wynikający z tego nie jest jeszcze zaawansowany. Deweloperzy języka JavaScript uzyskują trwały model programowania przy użyciu znanych konstrukcji językowych i elementów podstawowych biblioteki.

Możliwość wykonywania kodu JavaScript bezpośrednio w aparacie bazy danych zapewnia wydajność i transakcyjne wykonywanie operacji bazy danych na elementach kontenera. Ponadto, ponieważ aparat bazy danych Azure Cosmos Database natywnie obsługuje kod JSON i kod JavaScript, nie występuje niezgodność między systemami typów aplikacji a bazą danych.

## <a name="optimistic-concurrency-control"></a>Optymistyczna kontrola współbieżności 

Optymistyczna kontrola współbieżności umożliwia Zapobieganie utracie aktualizacji i usunięć. Współbieżne operacje powodujące konflikt są uzależnione od zwykłego blokowania pesymistycznego aparatu bazy danych hostowanego przez partycję logiczną będącą właścicielem elementu. Gdy dwie operacje współbieżne będą próbowały zaktualizować najnowszą wersję elementu w ramach partycji logicznej, jeden z nich zostanie wygrany, a drugie zakończy się niepowodzeniem. Jeśli jednak jedna lub dwie operacje próbujące zaktualizować ten sam element wcześniej odczytały starszą wartość elementu, baza danych nie wie, czy poprzednio odczytana wartość przez jedną lub obie operacje powodujące konflikt były rzeczywiście ostatnią wartością elementu. Na szczęście tę sytuację można wykryć przy użyciu optymistycznej **kontroli współbieżności (OCC)** przed umożliwieniem, aby dwie operacje mogły wprowadzić granicę transakcji wewnątrz aparatu bazy danych. OCC chroni dane przed przypadkowym zastąpieniem zmian wprowadzonych przez inne osoby. Uniemożliwia to innym osobom przypadkowe zastąpienie własnych zmian.

Współbieżne aktualizacje elementu są uzależnione od warstwy protokołu komunikacyjnego OCC przez Azure Cosmos DB. Usługa Azure Cosmos Database gwarantuje, że wersja elementu po stronie klienta aktualizowana (lub usuwana) jest taka sama jak wersja elementu w kontenerze usługi Azure Cosmos. Gwarantuje to, że zapisy są chronione przed przypadkowym zastąpieniem przez zapisy innych i na odwrót. W środowisku z obsługą kilku użytkowników optymistyczna kontrola współbieżności chroni przed przypadkowym usunięciem lub aktualizacją nieprawidłowej wersji elementu. W związku z tym elementy są chronione przed problemami z inFAMOUS "utraconej aktualizacji" lub "utracone usuwanie".

Każdy element przechowywany w kontenerze usługi Azure Cosmos ma właściwość zdefiniowaną przez `_etag` system. Wartość `_etag` jest automatycznie generowana i aktualizowana przez serwer za każdym razem, gdy element zostanie zaktualizowany. `_etag`może być używany z nagłówkiem żądania `if-match` dostarczonego przez klienta, aby umożliwić serwerowi podjęcie decyzji o tym, czy element może być aktualizowany warunkowo. Wartość `if-match` nagłówka jest zgodna z wartością `_etag` na serwerze, a następnie jest aktualizowana. Jeśli wartość `if-match` nagłówka żądania nie jest już aktualna, serwer odrzuca operację z komunikatem odpowiedzi "Niepowodzenie warunku wstępnego http 412". Następnie klient może ponownie pobrać element, aby uzyskać bieżącą wersję elementu na serwerze, lub zastąpić wersję elementu na serwerze własną `_etag` wartością dla tego elementu. Ponadto, można `_etag` użyć `if-none-match` z nagłówkiem, aby określić, czy konieczne jest ponowne pobranie zasobu. 

`_etag` Wartość elementu jest zmieniana za każdym razem, gdy element zostanie zaktualizowany. W przypadku operacji `if-match` Zamień element musi być jawnie wyrażona jako część opcji żądania. Aby zapoznać się z przykładem, zobacz przykładowy kod w usłudze [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag`wartości są niejawnie sprawdzane dla wszystkich elementów zapisanych przez procedurę składowaną. W przypadku wykrycia dowolnego konfliktu procedura składowana wycofa transakcję i zgłosi wyjątek. W przypadku tej metody wszystkie lub żadne zapisy w procedurze składowanej są stosowane niepodzielnie. Jest to sygnał do aplikacji w celu ponownego zastosowania aktualizacji i ponowienia próby oryginalnego żądania klienta.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o transakcjach bazy danych i optymistycznej kontroli współbieżności w następujących artykułach:

- [Praca z bazami danych usługi Azure Cosmos, kontenerami i elementami](databases-containers-items.md)
- [Poziomy spójności](consistency-levels.md)
- [Typy konfliktów i zasady rozwiązywania](conflict-resolution-policies.md)
- [Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md)
