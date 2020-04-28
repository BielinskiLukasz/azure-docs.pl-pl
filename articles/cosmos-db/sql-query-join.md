---
title: Zapytania SPRZĘŻENIa SQL dla Azure Cosmos DB
description: Dowiedz się, jak dołączać wiele tabel w Azure Cosmos DB, aby wykonywać zapytania dotyczące danych
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74871146"
---
# <a name="joins-in-azure-cosmos-db"></a>Sprzężenia w Azure Cosmos DB

W relacyjnej bazie danych sprzężenia między tabelami jest logicznym współrzutem do projektowania znormalizowanych schematów. W przeciwieństwie do interfejsu API SQL jest używany nieznormalizowany model danych elementów bez schematu, który jest logicznym odpowiednikiem *samosprzężenia*.

Sprzężenia wewnętrzne powodują pełny iloczyn skrzyżowania zestawów uczestniczących w sprzężeniu. Wynik sprzężenia N-kierunkowego jest zestawem N-elementowych krotek, gdzie każda wartość w spójnej kolekcji jest skojarzona z zestawem aliasów uczestniczącym w sprzężeniu i można uzyskać do niego dostęp, odwołując się do tego aliasu w innych klauzulach.

## <a name="syntax"></a>Składnia

Język obsługuje składnię `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. To zapytanie zwraca zestaw krotek z `N` wartościami. Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach. 

Przyjrzyjmy się następującej klauzuli FROM:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Zezwól na zdefiniowanie `input_alias1, input_alias2, …, input_aliasN`każdego źródła. Ta klauzula FROM zwraca zestaw N-krotek (krotka z N wartościami). Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach.  
  
**Przykładowe źródła 1** -2  
  
- Zezwalaj `<from_source1>` na zakres kontenera i reprezentuje zestaw {A, B, C}.  
  
- Zezwól `<from_source2>` na odwołujące się do zakresu dokumentu input_alias1 i reprezentowania zestawów:  
  
    {1, 2} dla`input_alias1 = A,`  
  
    {3}dla`input_alias1 = B,`  
  
    {4, 5} dla`input_alias1 = C,`  
  
- Klauzula `<from_source1> JOIN <from_source2>` from spowoduje powstanie następujących krotek:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Przykładowe źródła 2** -3  
  
- Zezwalaj `<from_source1>` na zakres kontenera i reprezentuje zestaw {A, B, C}.  
  
- Zezwól `<from_source2>` na odwoływanie się do `input_alias1` zakresu dokumentu i reprezentowanie zestawów:  
  
    {1, 2} dla`input_alias1 = A,`  
  
    {3}dla`input_alias1 = B,`  
  
    {4, 5} dla`input_alias1 = C,`  
  
- Zezwól `<from_source3>` na odwoływanie się do `input_alias2` zakresu dokumentu i reprezentowanie zestawów:  
  
    {100, 200} dla`input_alias2 = 1,`  
  
    {300}dla`input_alias2 = 3,`  
  
- Klauzula `<from_source1> JOIN <from_source2> JOIN <from_source3>` from spowoduje powstanie następujących krotek:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Brak krotek dla innych wartości `input_alias1`, `input_alias2`, dla których `<from_source3>` nie zwrócono żadnych wartości.  
  
**Przykładowe źródła 3** -3  
  
- Pozwól, aby <from_source1> mieć zakres kontenera i reprezentuje zestaw {A, B, C}.  
  
- Zezwalaj `<from_source1>` na zakres kontenera i reprezentuje zestaw {A, B, C}.  
  
- Zezwól <from_source2> być odwołujące się do zakresu dokumentu input_alias1 i reprezentowania zestawów:  
  
    {1, 2} dla`input_alias1 = A,`  
  
    {3}dla`input_alias1 = B,`  
  
    {4, 5} dla`input_alias1 = C,`  
  
- Zezwól `<from_source3>` na `input_alias1` zakres i reprezentowanie zestawów:  
  
    {100, 200} dla`input_alias2 = A,`  
  
    {300}dla`input_alias2 = C,`  
  
- Klauzula `<from_source1> JOIN <from_source2> JOIN <from_source3>` from spowoduje powstanie następujących krotek:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Spowodowało to przekroczenie iloczynu `<from_source2>` między `<from_source3>` programem i ponieważ oba zakresy są takie same `<from_source1>`.  Wynika to z 4 (2x2) krotek z wartością A, 0 krotek, które mają wartość B (1x0) i 2 (2x1) krotek mające wartość C.  
  
## <a name="examples"></a>Przykłady

W poniższych przykładach pokazano, jak działa klauzula JOIN. Przed uruchomieniem tych przykładów Przekaż przykładowe [dane rodziny](sql-query-getting-started.md#upload-sample-data). W poniższym przykładzie wynik jest pusty, ponieważ iloczyn między elementami źródłowymi i pustym jest pusty:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Wynik:

```json
    [{
    }]
```

W poniższym przykładzie sprzężenie jest iloczynem krzyżowym między dwoma obiektami JSON, katalogiem głównym `id` elementu a `children` podkatalogiem nadrzędnym. Fakt, że `children` tablica jest tablicą, nie obowiązuje w sprzężeniu, ponieważ zajmuje się pojedynczym elementem głównym, `children` który jest tablicą. Wynik zawiera tylko dwa wyniki, ponieważ iloczyn poprzeczny każdego elementu z tablicą daje tylko jeden element.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Wyniki są następujące:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Poniższy przykład przedstawia bardziej konwencjonalne sprzężenie:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Wyniki są następujące:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Źródłowa klauzula JOIN jest iteratorem. Dlatego przepływ w poprzednim przykładzie jest:  

1. Rozwiń każdy element `c` podrzędny w tablicy.
2. Zastosuj iloczyn krzyżowy z elementem głównym elementu `f` z każdym elementem `c` podrzędnym, który został spłaszczony w pierwszym kroku.
3. Na koniec Zaprojektuj tylko `f` `id` właściwość obiektu głównego.

Pierwszy element, `AndersenFamily`,, zawiera tylko jeden `children` element, więc zestaw wyników zawiera tylko jeden obiekt. Drugi element, `WakefieldFamily`,,,, `children`,,,,,,,, a więc iloczyn krzyżowy wytwarza dwa obiekty, jeden dla każdego `children` elementu. Główne pola w obu elementach są takie same, tak jak można oczekiwać w iloczynie wektorowym.

Prawdziwe narzędzie klauzuli JOIN polega na utworzeniu krotek od iloczynu krzyżowego w kształcie, który w przeciwnym razie trudno jest projektować. Poniższy przykład filtruje kombinację spójnej kolekcji, która umożliwia użytkownikowi wybranie warunku, który jest ogólny przez krotki.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Wyniki są następujące:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Poniższe rozszerzenie powyższego przykładu wykonuje podwójne sprzężenie. Można wyświetlić iloczyn krzyżowy jako następujący pseudo kod:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily`ma jeden element podrzędny, który ma jedną PET, więc iloczyn krzyżowy daje jeden wiersz (\*1\*1 1) z tej rodziny. `WakefieldFamily`ma dwa elementy podrzędne, tylko jeden z nich ma zwierzęta domowe, ale ten element podrzędny ma dwa zwierzęta domowe. Iloczyn krzyżowy dla tej rodziny daje 1\*1\*2 = 2 wiersze.

W następnym przykładzie istnieje dodatkowy filtr `pet`, który wyklucza wszystkie krotki, w których nie `Shadow`ma nazwy PET. Można tworzyć krotki z tablic, filtrować według dowolnego elementu krotki i projektować dowolną kombinację elementów.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Wyniki są następujące:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Podzapytania](sql-query-subquery.md)