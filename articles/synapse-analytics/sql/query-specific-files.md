---
title: Korzystanie z metadanych plików w zapytaniach
description: Funkcja OPENROWSET udostępnia informacje o pliku i ścieżce dotyczące każdego pliku użytego w zapytaniu do filtrowania lub analizowania danych na podstawie nazwy pliku i/lub ścieżki folderu.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 9c573e6ea3f80369f9b5cced66302caf12d727a4
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206364"
---
# <a name="using-file-metadata-in-queries"></a>Korzystanie z metadanych plików w zapytaniach

Usługa zapytań na żądanie SQL może rozdzielić wiele plików i folderów zgodnie z opisem w artykule [foldery zapytań i wiele plików](query-folders-multiple-csv-files.md) . W tym artykule dowiesz się, jak używać informacji metadanych o nazwach plików i folderów w zapytaniach.

Czasami może być konieczne sprawdzenie, który plik lub źródło folderu jest skorelowane z określonym wierszem w zestawie wyników.

Można użyć funkcji `filepath` i `filename` zwrócić nazwy plików i/lub ścieżki w zestawie wyników. Można też użyć ich do filtrowania danych na podstawie nazwy pliku i/lub ścieżki folderu. Te funkcje są opisane w sekcji składnia [Nazwa](develop-storage-files-overview.md#filename-function) i [ścieżka funkcji](develop-storage-files-overview.md#filepath-function). Poniżej znajdziesz krótkie opisy dotyczące przykładów.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest **utworzenie bazy danych** ze źródłem danych, które odwołuje się do konta magazynu. Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny spowoduje utworzenie źródeł danych, poświadczeń z zakresem bazy danych i zewnętrznych formatów plików, które są używane w tych przykładach.

## <a name="functions"></a>Funkcje

### <a name="filename"></a>Nazwa pliku

Ta funkcja zwraca nazwę pliku, z którego pochodzi wiersz.

Poniższy przykład odczytuje pliki danych NYC żółtej taksówki dla ostatnich trzech miesięcy 2017 i zwraca liczbę kolarstwu na plik. Część OPENROWSET zapytania określa, które pliki zostaną odczytane.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

Poniższy przykład pokazuje, jak *Nazwa pliku ()* może być używana w klauzuli WHERE do filtrowania plików do odczytu. Uzyskuje dostęp do całego folderu w części OPENROWSET zapytania i filtruje pliki w klauzuli WHERE.

Wyniki będą takie same jak w poprzednim przykładzie.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Parametr

Funkcja FilePath zwraca pełną lub częściową ścieżkę:

- Gdy wywoływana bez parametru, zwraca pełną ścieżkę pliku, z której pochodzi wiersz.
- Gdy wywoływana z parametrem, zwraca część ścieżki, która pasuje do symbolu wieloznacznego na pozycji określonej w parametrze. Na przykład wartość parametru 1 zwróci część ścieżki, która pasuje do pierwszego symbolu wieloznacznego.

Poniższy przykład odczytuje NYC żółte pliki danych z taksówką dla ostatnich trzech miesięcy 2017. Zwraca liczbę kolarstwu na ścieżkę pliku. Część OPENROWSET zapytania określa, które pliki zostaną odczytane.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

Poniższy przykład pokazuje, jak w klauzuli WHERE można użyć *ścieżki FilePath ()* w celu przefiltrowania plików, które mają być odczytywane.

Możesz użyć symboli wieloznacznych w składniku OPENROWSET zapytania i filtrować pliki w klauzuli WHERE. Wyniki będą takie same jak w poprzednim przykładzie.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Następne kroki

W następnym artykule dowiesz się, jak [wykonywać zapytania dotyczące plików Parquet](query-parquet-files.md).
