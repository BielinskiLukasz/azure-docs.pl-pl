---
title: Tworzenie aplikacji mobilnych za pomocą platformy Xamarin i usługi Azure Cosmos DB | Microsoft Docs
description: Samouczek umożliwiający utworzenie aplikacji Xamarin iOS, Android lub Forms przy użyciu usługi Azure Cosmos DB. Azure Cosmos DB to szybka, działająca w skali globalnej, chmurowa baza danych dla aplikacji mobilnych.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 0187bf51d36f16001f0f7f67f4f72f5f3df2e05d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974140"
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Tworzenie aplikacji mobilnych za pomocą platformy Xamarin i usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Większość aplikacji mobilnych musi przechowywać dane w chmurze, a usługa Azure Cosmos DB jest bazą danych w chmurze dla aplikacji mobilnych. Zawiera wszystko, czego potrzebuje deweloper aplikacji mobilnych. Jest to w pełni zarządzana baza danych jako usługa ze skalowaniem na żądanie. Umożliwia dostarczanie użytkownikom danych do aplikacji w sposób niewidoczny w dowolnym miejscu na całym świecie. Korzystając z [zestawu .NET Core SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet-core.md), możesz umożliwić aplikacjom mobilnym platformy Xamarin bezpośrednią interakcję z usługą Azure Cosmos DB z pominięciem warstwy środkowej.

Ten artykuł zawiera samouczek tworzenia aplikacji mobilnych za pomocą platformy Xamarin i usługi Azure Cosmos DB. Pełny kod źródłowy samouczka, w tym informacje na temat sposobu zarządzania użytkownikami i uprawnieniami, możesz znaleźć w witrynie [platformy Xamarin i usługi Azure Cosmos DB w serwisie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Możliwości usługi Azure Cosmos DB w przypadku aplikacji mobilnych
Usługa Azure Cosmos DB oferuje następujące kluczowe możliwości dla deweloperów aplikacji mobilnych:

![Możliwości usługi Azure Cosmos DB w przypadku aplikacji mobilnych](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Zaawansowane zapytania dotyczące danych bez schematu. Usługa Azure Cosmos DB przechowuje dane jako dokumenty JSON bez schematu w kolekcjach heterogenicznych. Umożliwia wysyłanie [zaawansowanych i szybkich zapytań](sql-api-sql-query.md) bez konieczności martwienia się o schematy czy indeksy.
* Szybka przepływność. Odczytywanie i zapisywanie dokumentów za pomocą usługi Azure Cosmos DB trwa zaledwie kilka milisekund. Deweloperzy mogą określić potrzebną przepływność, a usługa Azure Cosmos DB zapewni ją w ramach umowy SLA gwarantującej dostępność na poziomie co najmniej 99,99% dla wszystkich kont w obrębie jednego regionu i wszystkich kont w wielu regionach w przypadku rozluźnionej spójności, a także dostępność do odczytu na poziomie co najmniej 99,999% dla wszystkich kont bazy danych w wielu regionach.
* Nieograniczone skalowanie. Kolekcje usługi Azure Cosmos DB [rosną razem z aplikacją](partition-data.md). Możesz zacząć od niewielkiego rozmiaru danych i przepływności liczonej w setkach żądań na sekundę. Twoje kolekcje lub bazy danych mogą rozrosnąć się do petabajtów danych i dowolnie dużej przepływności na poziomie setek milionów żądań na sekundę.
* Dystrybucja globalna. Użytkownicy aplikacji mobilnych podróżują, często po całym świecie. Usługa Azure Cosmos DB jest [globalnie rozproszoną bazą danych](distribute-data-globally.md). Kliknij mapę, aby umożliwić użytkownikom dostęp do danych.
* Wbudowana autoryzacja zaawansowana. W ramach usługi Azure Cosmos DB możesz łatwo implementować popularne wzorce, takie jak [dane dla poszczególnych użytkowników](https://aka.ms/documentdb-xamarin-todouser) czy dane udostępnione wielu użytkownikom, nie używając złożonego, niestandardowego kodu autoryzacji.
* Zapytania dotyczące danych geoprzestrzennych. Obecnie wiele aplikacji mobilnych zapewnia środowiska w kontekście geograficznym. Dzięki znakomitej obsłudze [typów danych geoprzestrzennych](geospatial.md) usługa Azure Cosmos DB ułatwia tworzenie tych środowisk.
* Załączniki binarne. Dane aplikacji często zawierają binarne obiekty blob. Natywna obsługa załączników ułatwia korzystanie z usługi Azure Cosmos DB jako centralnego miejsca do obsługi danych aplikacji.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Samouczek dotyczący usługi Azure Cosmos DB i platformy Xamarin
W poniższym samouczku pokazano sposób tworzenia aplikacji mobilnych za pomocą platformy Xamarin i usługi Azure Cosmos DB. Pełny kod źródłowy samouczka możesz znaleźć w witrynie [platformy Xamarin i usługi Azure Cosmos DB w serwisie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Rozpoczęcie pracy
Rozpoczęcie pracy z usługą Azure Cosmos DB jest łatwe. Przejdź do witryny Azure Portal i utwórz nowe konto usługi Azure Cosmos DB. Kliknij kartę **Szybki start**. Pobierz przykładową listę zadań do wykonania narzędzia Xamarin Forms, która jest już połączona z Twoim kontem usługi Azure Cosmos DB. 

![Azure Cosmos DB — Szybki start dla aplikacji mobilnych](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Jeśli masz istniejącą aplikację platformy Xamarin, możesz również dodać [pakiet NuGet usługi Azure Cosmos DB](sql-api-sdk-dotnet-core.md). Usługa Azure Cosmos DB obsługuje biblioteki udostępnione Xamarin.IOS, Xamarin.Android i Xamarin Forms.

### <a name="work-with-data"></a>Praca z danymi
Rekordy danych są przechowywane w usłudze Azure Cosmos DB jako dokumenty JSON bez schematu w kolekcjach heterogenicznych. W jednej kolekcji możesz przechowywać dokumenty o różnych strukturach:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

W projektach platformy Xamarin możesz używać zapytań o języku zintegrowanym dotyczących danych bez schematu:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Dodawanie użytkowników
Podobnie jak wiele przykładów rozpoczęcia pracy, pobrany przykład usługi Azure Cosmos DB uwierzytelnia się w usłudze przy użyciu klucza głównego zapisanego na stałe w kodzie aplikacji. To domyślne podejście nie jest dobrym rozwiązaniem w przypadku aplikacji, która ma być uruchamiana gdziekolwiek poza lokalnym emulatorem. Jeśli nieautoryzowany użytkownik uzyska klucz główny, wszystkie dane na Twoim koncie usługi Azure Cosmos DB mogą być zagrożone. Lepiej, żeby aplikacja uzyskiwała dostęp tylko do rekordów zalogowanego użytkownika. W usłudze Azure Cosmos DB deweloperzy mogą przyznawać aplikacji uprawnienia do odczytu lub do odczytu/zapisu do kolekcji, zestawu dokumentów pogrupowanych według klucza partycji lub określonego dokumentu. 

Wykonaj następujące kroki, aby zmienić aplikację listy zadań do wykonania w aplikację listy zadań do wykonania dla wielu użytkowników: 

  1. Dodaj do swojej aplikacji dane logowania za pomocą usługi Facebook, usługi Active Directory lub dowolnego innego dostawcy.

  2. Utwórz kolekcję UserItems usługi Azure Cosmos DB, używając parametru **/userId** jako klucza partycji. Określając klucz partycji dla swojej kolekcji, umożliwisz usłudze Azure Cosmos DB nieograniczone skalowanie w miarę wzrostu liczby użytkowników aplikacji z zachowaniem szybkości wykonywania zapytań.

  3. Dodaj brokera tokenu zasobów usługi Azure Cosmos DB. Ten prosty internetowy interfejs API uwierzytelnia użytkowników i wystawia zalogowanym użytkownikom krótkotrwałe tokeny umożliwiające dostęp tylko do dokumentów w ich partycji. W tym przykładzie broker tokenu zasobów znajduje się w usłudze App Service.

  4. Zmodyfikuj aplikację, aby uwierzytelniać się w brokerze tokenu zasobów za pomocą usługi Facebook, i zażądaj tokenów zasobów dla zalogowanych użytkowników usługi Facebook. W ten sposób uzyskasz dostęp do ich danych w kolekcji UserItems.  

Pełny kod przykładowy tego wzorca możesz znaleźć w witrynie [brokera tokenu zasobów w serwisie GitHub](http://aka.ms/documentdb-xamarin-todouser). Ten diagram przedstawia rozwiązanie:

![Broker użytkowników i uprawnień usługi Azure Cosmos DB](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Jeśli chcesz, aby dwóch użytkowników miało dostęp do tej samej listy zadań do wykonania, możesz dodać dodatkowe uprawnienia do tokenu dostępu w brokerze tokenu zasobów.

### <a name="scale-on-demand"></a>Skalowanie na żądanie
Azure Cosmos DB to zarządzana baza danych jako usługa. Gdy zwiększa się Twoja baza użytkowników, nie musisz troszczyć się o aprowizowanie maszyn wirtualnych czy zwiększanie liczby rdzeni. Musisz jedynie poinformować usługę Azure Cosmos DB, ilu operacji na sekundę (jakiej przepływności) potrzebuje Twoja aplikacja. Przepływność możesz określić za pomocą karty **Skala**, używając miary przepływności nazywanej jednostkami żądań (RU) na sekundę. Na przykład operacja odczytu dokumentu o rozmiarze 1 KB wymaga 1 jednostki żądania. Możesz także dodać alerty do metryki **Przepływność**, aby monitorować wzrost ruchu i programowo zmieniać przepływność po wystąpieniu alertu.

![Skalowanie przepływności na żądanie w usłudze Azure Cosmos DB](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Przejście do skali globalnej
W miarę zdobywania popularności przez Twoją aplikację możesz zyskać użytkowników na całym świecie. Możesz tez chcieć przygotować się na nieprzewidziane zdarzenia. Przejdź do witryny Azure Portal i otwórz swoje konto usługi Azure Cosmos DB. Kliknij mapę, aby Twoje dane były ciągle replikowane w dowolnej liczbie regionów na całym świecie. Ta funkcja udostępnia Twoje dane wszędzie tam, gdzie znajdują się użytkownicy. Możesz również dodać zasady trybu failover, aby przygotować się na awarie.

![Skalowanie w regionach geograficznych usługi Azure Cosmos DB](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Gratulacje. Rozwiązanie korzystające z platformy Xamarin i usługi Azure Cosmos DB zostało ukończone i masz aplikację mobilną. Wykonaj podobne kroki, aby utworzyć aplikacje środowiska Cordova za pomocą zestawu SDK JavaScript usługi Azure Cosmos DB i natywne aplikacje dla systemu iOS/Android za pomocą interfejsów API REST usługi Azure Cosmos DB.

## <a name="next-steps"></a>Następne kroki
* Wyświetl kod źródłowy [platformy Xamarin i usługi Azure Cosmos DB w serwisie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Pobierz [zestaw .NET Core SDK usługi Cosmos Azure DB](sql-api-sdk-dotnet-core.md).
* Znajdź więcej przykładów kodu dla [aplikacji .NET](sql-api-dotnet-samples.md).
* Dowiedz się więcej na temat [zaawansowanych funkcji zapytań usługi Azure Cosmos DB](sql-api-sql-query.md).
* Dowiedz się więcej na temat [obsługi danych geoprzestrzennych w usłudze Azure Cosmos DB](geospatial.md).



