---
title: Limity pojemności — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Maksymalna dozwolona dla różnych składników usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: sachinpMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 07/26/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 7c6445624b2c03497c881b0c34bac8256fa28a98
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302047"
---
# <a name="sql-data-warehouse-capacity-limits"></a>Usługa SQL Data Warehouse limity pojemności
Maksymalna dozwolona dla różnych składników usługi Azure SQL Data Warehouse.

## <a name="workload-management"></a>Zarządzanie obciążeniem
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| [Jednostki magazynu danych (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maksymalna liczba jednostek DWU w pojedynczej SQL Data Warehouse | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Jednostki magazynu danych (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Domyślna wartość DTU na serwer |54,000<br></br>Domyślnie każdy serwer SQL (na przykład myserver.database.windows.net) ma limit przydziału jednostek DTU równa 54 000, co pozwala maksymalnie DW6000c. Ten limit przydziału jest po prostu limitem bezpieczeństwa. Możesz zwiększyć limit przydziału przez [utworzeniem biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i wybierając polecenie *przydziału* jako typ żądania.  Do obliczania usługi jednostki DTU potrzebuje, należy pomnożyć 7,5 przez łączną DWU potrzebne lub mnożenia 9.0 cDWU całkowity, potrzebne. Na przykład:<br></br>DW6000 x w wersji 7.5 = 45 000 jednostek Dtu<br></br>DW6000c x 9.0 = równa 54 000 jednostek Dtu.<br></br>Aktualne użycie jednostek DTU, z opcji programu SQL server można wyświetlić w portalu. Limit przydziału jednostek DTU obejmuje zarówno wstrzymane, jak i niewstrzymane bazy danych. |
| Połączenie z bazą danych |Współbieżne otwarte sesje |1024<br/><br/>Każdy z 1024 aktywne sesje mogą przesyłać żądania do bazy danych SQL Data Warehouse, w tym samym czasie. Należy pamiętać, że istnieją ograniczenia dotyczące liczby zapytań, które mogą być wykonywane jednocześnie. Po przekroczeniu limitu współbieżności, żądanie przechodzi w stan kolejki wewnętrznej, gdzie oczekuje na przetworzenie. |
| Połączenie z bazą danych |Maksymalna ilość pamięci dla przygotowanej instrukcji |20 MB |
| [Zarządzanie obciążeniami](resource-classes-for-workload-management.md) |Maksymalna liczba jednoczesnych kwerend |128<br/><br/> Usługa SQL Data Warehouse można wykonywać maksymalnie 128 zapytań jednoczesnych lub kolejek, pozostałe zapytania.<br/><br/>Liczba równoczesnych zapytań można zmniejszyć, gdy użytkownicy są przypisane do wyższe klasy zasobów lub jeśli usługa SQL Data Warehouse ma mniejszy [data warehouse Unit, jednostka](memory-and-concurrency-limits.md) ustawienie. Niektórych kwerend, takich jak zapytania DMV, są zawsze może działać i czy nie wpływa na limit współbieżnych zapytania. Aby uzyskać szczegółowe informacje na temat wykonywania zapytań jednoczesnych, zobacz [maksymalne wartości współbieżności](memory-and-concurrency-limits.md#concurrency-maximums) artykułu. |
| [bazy danych tempdb](sql-data-warehouse-tables-temporary.md) |Maksymalna GB |399 GB na DW100. W związku z tym w DWU1000, bazy danych tempdb jest o rozmiarze do 3,99 TB. |

## <a name="database-objects"></a>Obiekty bazy danych
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Database (Baza danych) |Maks. rozmiar | Gen1: 240 TB skompresowane na dysku. Ta przestrzeń jest niezależna od miejsca na bazę danych tempdb lub dziennika, a w związku z tym ta przestrzeń jest dedykowany do tablic stałych.  Kompresja klastrowanego magazynu kolumn szacuje się na 5 X.  Kompresja ta umożliwia bazy danych do około 1 PB, w przypadku wszystkich tabel klastrowanego magazynu kolumn (domyślny typ tabeli). <br/><br/> Gen2: 240TB magazynu wierszy i nieograniczony magazyn tabel magazynu kolumn |
| Tabela |Maks. rozmiar |60 TB skompresowane na dysku |
| Tabela |Tabele dla bazy danych |10 000 |
| Tabela |Kolumn w tabeli |1024 kolumn |
| Tabela |Liczba bajtów na kolumnę |Zależne od kolumny [— typ danych](sql-data-warehouse-tables-data-types.md). Limit wynosi 8000 dla typów danych char, 4000 nvarchar, czy za 2 GB dla typów danych MAX. |
| Tabela |Bajtów na wiersz, zdefiniowanego rozmiaru |8060 bajtów<br/><br/>Liczba bajtów na wiersz jest obliczana w taki sam sposób, podobnie jak w przypadku programu SQL Server przy użyciu kompresji strony. Podobnie jak SQL Server, SQL Data Warehouse obsługuje magazynu przepełnienie wierszy, które umożliwia **kolumn o zmiennej długości** ma zostać wypchnięty pozawierszową. Gdy o zmiennej długości wierszy są przekazywane pozawierszową, tylko 24-bajtowy główny znajduje się w głównym rekordzie. Aby uzyskać więcej informacji, zobacz [przepełnienie wiersza danych przekraczających rozmiarze 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabela |Partycje na tabelę |15 000<br/><br/>Wydajna, firma Microsoft zaleca, minimalizując liczbę partycji potrzebne podczas przerywania obsługi wymagań biznesowych. Wraz z rozwojem liczby partycji koszty operacji języka definicji danych (DDL) i manipulowania języka DML (Data) zwiększa się i powoduje, że niższej wydajności. |
| Tabela |Liczba znaków na wartość graniczna partycji. |4000 |
| Indeks |Indeksy klastrowane inne niż na tabelę. |50<br/><br/>Dotyczy tylko tabele magazynu wierszy. |
| Indeks |Indeksy klastrowane na tabelę. |1<br><br/>Ma zastosowanie do tabel zarówno magazynu wierszy, jak i magazynu kolumn. |
| Indeks |Rozmiar klucza indeksu. |900 bajtów.<br/><br/>Dotyczy tylko indeksów magazynu wierszy.<br/><br/>Indeksy w kolumnach varchar o maksymalnym rozmiarze więcej niż 900 bajtów mogą być tworzone, jeśli podczas tworzenia indeksu, istniejące dane w kolumnach nie przekraczać 900 bajtów. Jednak później WSTAWIĆ lub akcji aktualizacji dla kolumn, które powodują całkowity rozmiar przekracza 900 bajtów zakończy się niepowodzeniem. |
| Indeks |Kolumny klucza indeksu. |16<br/><br/>Dotyczy tylko indeksów magazynu wierszy. Klastrowane indeksy magazynu kolumn, Uwzględnij wszystkie kolumny. |
| Statystyki |Rozmiar wartości w kolumnach połączone. |900 bajtów. |
| Statystyki |Kolumny dla każdego obiektu statystyk. |32 |
| Statystyki |Statystyka utworzone dla kolumn w tabeli. |30,000 |
| Procedury składowane |Maksymalnej liczby poziomów zagnieżdżenia. |8 |
| Widok |Kolumn w widoku |1,024 |

## <a name="loads"></a>Obciążenia
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Obciążenia funkcji Polybase |MB na wiersz |1<br/><br/>Program Polybase ładuje się tylko do wierszy, które są mniejsze niż 1 MB i nie można załadować do VARCHAR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX).<br/><br/> |

## <a name="queries"></a>Zapytania
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Zapytanie |Umieszczonych w kolejce kwerend w tabelach użytkownika. |1000 |
| Zapytanie |Zapytania jednoczesne w widokach systemu. |100 |
| Zapytanie |Umieszczonych w kolejce zapytań na widoki systemu |1000 |
| Zapytanie |Maksymalna parametrów |2098 |
| Batch |Maksymalny rozmiar |65,536*4096 |
| Wybierz wyniki |Kolumn w wierszach |4096<br/><br/>Nigdy nie może mieć więcej niż 4096 kolumn w wierszach, w wynikach wybierz. Nie ma żadnej gwarancji, że zawsze masz 4096. Jeśli w planie zapytania wymaga tabeli tymczasowej, 1024 kolumn dla tabeli maksymalna mogą być stosowane. |
| SELECT |Zagnieżdżonych podzapytań |32<br/><br/>Nigdy nie mogą istnieć więcej niż 32 zagnieżdżonych podzapytań w instrukcji SELECT. Nie ma żadnej gwarancji, że zawsze może mieć 32. Na przykład sprzężenia można wprowadzać podzapytania do planu zapytania. Liczbę podzapytania może być ona ograniczona przez ilość dostępnej pamięci. |
| SELECT |Kolumn na sprzężenia |1024 kolumn<br/><br/>Program może nigdy nie więcej niż 1024 kolumn sprzężenia. Nie ma żadnej gwarancji, że zawsze masz 1024. Jeśli plan sprzężenia wymaga tabelę tymczasową przy użyciu więcej kolumn niż wynik sprzężenia, limitu 1024 stosuje się do tabeli tymczasowej. |
| SELECT |Bajtów na grupy według kolumn. |8060<br/><br/>Kolumny w klauzuli GROUP BY nie może przekraczać 8060 bajtów. |
| SELECT |Liczba bajtów na kolumny w klauzuli ORDER BY |8060 bajtów<br/><br/>Kolumny w klauzuli ORDER BY nie może przekraczać 8060 bajtów |
| Identyfikatory na instrukcję |Liczba identyfikatorów odwołania |65,535<br/><br/>Usługa SQL Data Warehouse ogranicza liczbę identyfikatorów, które mogą być zawarte w jednym wyrażeniu zapytania. Przekroczenie tego numeru powoduje błąd programu SQL Server 8632. Aby uzyskać więcej informacji, zobacz [błąd wewnętrzny: Osiągnięto limit usług wyrażeń] [błąd wewnętrzny: Osiągnięto limit usług wyrażeń]. |
| Literały ciągu | Liczba literał ciągu w instrukcji | 20,000 <br/><br/>Usługa SQL Data Warehouse ogranicza liczbę stałych ciągów w jednym wyrażeniu zapytania. Przekroczenie tego numeru powoduje błąd programu SQL Server 8632.|

## <a name="metadata"></a>Metadane
| Widok systemowy | Maksymalna liczba wierszy |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Łączna liczba pracowników DMS dla ostatnich 1000 żądań SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Całkowita liczba kroków dla najnowszych żądań SQL 1000, które są przechowywane w sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Najnowsze żądania SQL 1000, które są przechowywane w sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać zalecenia dotyczące używania SQL Data Warehouse, zobacz [da się oszukać arkusza](cheat-sheet.md).
