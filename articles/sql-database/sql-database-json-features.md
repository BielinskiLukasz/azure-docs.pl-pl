---
title: Praca z danymi w formacie JSON
description: Azure SQL Database umożliwia analizowanie, wykonywanie zapytań i formatowanie danych w notacji JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 01/15/2019
ms.openlocfilehash: 958d937ad85fd62249c7ce3f0e0ab2f8cc1d1b80
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819943"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Wprowadzenie do funkcji JSON w Azure SQL Database
Azure SQL Database umożliwia analizowanie i wykonywanie zapytań o dane reprezentowane w formacie JavaScript Object Notation [(JSON)](https://www.json.org/) i eksportowanie danych relacyjnych jako tekstu JSON. W Azure SQL Database są dostępne następujące scenariusze JSON:
- [Formatowanie danych relacyjnych w formacie JSON](#formatting-relational-data-in-json-format) przy użyciu klauzuli `FOR JSON`.
- [Praca z danymi JSON](#working-with-json-data)
- [Wykonywanie zapytań dotyczących danych JSON](#querying-json-data) przy użyciu funkcji skalarnych JSON.
- [Przekształcanie kodu JSON w format tabelaryczny](#transforming-json-into-tabular-format) za pomocą funkcji `OPENJSON`.

## <a name="formatting-relational-data-in-json-format"></a>Formatowanie danych relacyjnych w formacie JSON
Jeśli masz usługę sieci Web, która pobiera dane z warstwy bazy danych i zawiera odpowiedź w formacie JSON lub struktury lub biblioteki JavaScript po stronie klienta, które akceptują dane sformatowane jako JSON, możesz sformatować zawartość bazy danych jako plik JSON bezpośrednio w zapytaniu SQL. Nie trzeba już pisać kodu aplikacji, który formatuje wyniki z Azure SQL Database jako JSON, lub dołączać bibliotekę serializacji JSON, aby konwertować wyniki zapytania tabelarycznego, a następnie serializować obiekty do formatu JSON. Zamiast tego można użyć klauzuli FOR JSON, aby sformatować wyniki zapytania SQL jako dane JSON w Azure SQL Database i używać ich bezpośrednio w aplikacji.

W poniższym przykładzie wiersze z tabeli Sales. Customer są sformatowane jako JSON przy użyciu klauzuli FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Klauzula FOR JSON PATH formatuje wyniki zapytania jako tekst JSON. Nazwy kolumn są używane jako klucze, podczas gdy wartości komórek są generowane jako wartości JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Zestaw wyników jest sformatowany jako tablica JSON, w której każdy wiersz jest sformatowany jako oddzielny obiekt JSON.

ŚCIEŻKA wskazuje, że można dostosować format danych wyjściowych wyniku JSON przy użyciu notacji kropkowej w aliasach kolumn. Następujące zapytanie zmienia nazwę klucza "CustomerName" w wyjściowym formacie JSON i umieszcza numery telefonów i faksów w podobiektie "Contact":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Dane wyjściowe tego zapytania wyglądają następująco:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

W tym przykładzie został zwrócony pojedynczy obiekt JSON zamiast tablicy przez określenie opcji [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) . Możesz użyć tej opcji, Jeśli wiesz, że zwracasz pojedynczy obiekt w wyniku zapytania.

Główną wartością klauzuli FOR JSON jest możliwość zwrócenia złożonych danych hierarchicznych z bazy danych sformatowane jako zagnieżdżone obiekty JSON lub tablice. Poniższy przykład pokazuje, jak uwzględnić wiersze z tabeli `Orders` należącej do `Customer` jako zagnieżdżoną tablicę `Orders`:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Zamiast wysyłać oddzielne zapytania w celu uzyskania danych klienta, a następnie pobierania listy powiązanych zamówień, można uzyskać wszystkie niezbędne dane za pomocą pojedynczego zapytania, jak pokazano w następujących przykładowych danych wyjściowych:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Praca z danymi w formacie JSON
Jeśli nie masz ściśle uporządkowanych danych, jeśli masz złożone obiekty podrzędne, tablice lub dane hierarchiczne lub jeśli Twoje struktury danych są rozwijane w czasie, format JSON może pomóc w zaprezentowaniu dowolnej złożonej struktury danych.

JSON jest formatem tekstowym, który może być używany jak każdy inny typ ciągu w Azure SQL Database. Dane JSON można wysyłać lub przechowywać jako standardowy NVARCHAR:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Dane JSON używane w tym przykładzie są reprezentowane przy użyciu typu NVARCHAR (MAX). KOD JSON można wstawić do tej tabeli lub podać jako argument procedury składowanej przy użyciu standardowej składni języka Transact-SQL, jak pokazano w następującym przykładzie:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Wszystkie języki lub biblioteki po stronie klienta, które współdziałają z danymi ciągu w Azure SQL Database, również będą działać z danymi JSON. Dane JSON mogą być przechowywane w dowolnej tabeli, która obsługuje typ NVARCHAR, taki jak tabela zoptymalizowana pod kątem pamięci lub tabela z systemową obsługą wersji. Kod JSON nie wprowadza żadnego ograniczenia w kodzie po stronie klienta lub w warstwie bazy danych.

## <a name="querying-json-data"></a>Wykonywanie zapytania dotyczącego danych JSON
Jeśli dane są sformatowane w formacie JSON przechowywanym w tabelach SQL platformy Azure, funkcja JSON pozwala używać tych danych w dowolnym zapytaniu SQL.

Funkcje JSON, które są dostępne w usłudze Azure SQL Database, pozwalają traktować dane sformatowane jako kod JSON jako każdy inny typ danych SQL. Można łatwo wyodrębnić wartości z tekstu JSON i użyć danych JSON w dowolnych zapytaniach:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkcja JSON_VALUE wyodrębnia wartość z tekstu JSON przechowywanego w kolumnie dane. Ta funkcja używa ścieżki podobnej do języka JavaScript, aby odwołać wartość w tekście JSON do wyodrębnienia. Wyodrębnionej wartości można użyć w dowolnej części zapytania SQL.

Funkcja JSON_QUERY jest podobna do JSON_VALUE. W przeciwieństwie do JSON_VALUE, ta funkcja wyodrębnia złożone obiekty podrzędne, takie jak tablice lub obiekty, które są umieszczane w tekście JSON.

Funkcja JSON_MODIFY pozwala określić ścieżkę wartości w tekście JSON, która ma zostać zaktualizowana, a także nową wartość, która spowoduje zastąpienie Starego. W ten sposób można łatwo aktualizować tekst JSON bez konieczności przeanalizowania całej struktury.

Ponieważ kod JSON jest przechowywany w tekście standardowym, nie ma gwarancji, że wartości przechowywane w kolumnach tekstowych są poprawnie sformatowane. Można sprawdzić, czy tekst przechowywany w kolumnie JSON jest poprawnie sformatowany przy użyciu standardowych ograniczeń check Azure SQL Database i funkcji isjson:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Jeśli tekst wejściowy jest poprawnie sformatowany w formacie JSON, funkcja isjson zwraca wartość 1. W każdym wstawieniu lub aktualizacji kolumny JSON to ograniczenie sprawdzi, czy nowa wartość tekstowa nie jest źle sformułowanym kodem JSON.

## <a name="transforming-json-into-tabular-format"></a>Przekształcanie kodu JSON w format tabelaryczny
Azure SQL Database umożliwia również przekształcanie kolekcji JSON w formacie tabelarycznym oraz ładowanie i wykonywanie zapytań dotyczących danych JSON.

OPENJSON to funkcja Table-Value, która analizuje tekst JSON, lokalizuje tablicę obiektów JSON, iteruje elementy tablicy i zwraca jeden wiersz w wyniku danych wyjściowych dla każdego elementu tablicy.

![Tabelaryczny JSON](./media/sql-database-json-features/image_2.png)

W powyższym przykładzie możemy określić, gdzie należy zlokalizować tablicę JSON, która ma zostać otwarta (w $. Ścieżka Orders), jakie kolumny należy zwrócić jako wynik i gdzie można znaleźć wartości JSON, które będą zwracane jako komórki.

Możemy przekształcić tablicę JSON w zmiennej @orders na zestaw wierszy, analizować ten zestaw wyników lub wstawiać wiersze do tabeli standardowej:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
Kolekcja zamówień sformatowana jako tablica JSON i podana jako parametr procedury składowanej można analizować i wstawiać do tabeli Orders.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak zintegrować kod JSON z aplikacją, zapoznaj się z następującymi zasobami:

* [Blog TechNet](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [Dokumentacja MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Wideo Channel 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Aby dowiedzieć się więcej na temat różnych scenariuszy integracji kodu JSON z aplikacją, zobacz pokazy w tym [filmie wideo kanału 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) lub Znajdź scenariusz pasujący do przypadku użycia w [wpisach w blogu JSON](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

