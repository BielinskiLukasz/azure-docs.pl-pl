---
title: Usługi Azure Cosmos DB do przechowywania wartości klucza, opłat za jednostki żądań
description: Więcej informacji na temat opłat za jednostki żądań w usłudze Azure Cosmos DB dla prostych zapisu, a operacje odczytu, gdy jest ona używana jako magazyn klucz/wartość.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647508"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB jako magazyn wartości klucza — przegląd kosztów

Usługa Azure Cosmos DB to usługa globalnie dystrybuowanej, wielomodelowej bazy danych do tworzenia aplikacji o wysokiej dostępności, na dużą skalę w prosty sposób. Domyślnie Azure Cosmos DB automatycznie i efektywnie indeksuje wszystkie dane, które pobiera. Pozwala to na szybkie i spójne zapytania [SQL](how-to-sql-query.md) (i [JavaScript](stored-procedures-triggers-udfs.md)) dotyczące danych. 

W tym artykule opisano koszt usługi Azure Cosmos DB proste zapisu oraz operacje odczytu, gdy jest ona używana jako magazyn klucz/wartość. Operacje zapisu obejmują Wstawianie, zamienianie, usuwanie i upserts elementów danych. Oprócz zagwarantowania, że umowa SLA dostępności na 99,999% dla wszystkich kont wieloregionowych, Azure Cosmos DB oferuje gwarantowane < 10 ms dla operacji odczytu i dla (indeksowane) zapisów w 99 percentylu. 

## <a name="why-we-use-request-units-rus"></a>Dlaczego używamy jednostek żądań (ru)

Wydajność Azure Cosmos DB zależy od ilości alokowanej przepływności wyrażonej w [jednostkach żądania](request-units.md) (ru/s). Inicjowanie obsługi jest na drugim poziomie szczegółowości i jest kupowane w RU/s ([nie należy mylić z godzinową płatnością](https://azure.microsoft.com/pricing/details/cosmos-db/)). Jednostek ru należy traktować jako logiczne abstrakcyjne (walutowe), które upraszczają obsługę wymaganej przepływności dla aplikacji. Użytkownicy nie muszą myśleć o rozróżnieniu między przepływem operacji odczytu i zapisu. Model pojedynczej waluty (RUS) tworzy efektywność udostępnianie zaprowizowaną pojemnością między operacji odczytu i zapisu. Ten model pojemności zainicjowanej pozwala usłudze zapewnić **przewidywalną i spójną przepływność, gwarantowane małe opóźnienia i wysoką dostępność**. Na koniec model RU jest używany do przedstawiania przepływności, a każdy zainicjowany RU ma również określoną ilość zasobów (np. pamięci, rdzeni/procesora i operacji we/wy na sekundę).

Jako globalnie dystrybuowany system bazy danych, Cosmos DB jest jedyną usługą platformy Azure, która zapewnia kompleksową umowy slaę obejmującą opóźnienia, przepływność, spójność i wysoką dostępność. Wybrana przepustowość jest stosowana do każdego regionu skojarzonego z Twoim kontem Cosmos. W przypadku operacji odczytu Cosmos DB oferuje wiele dobrze zdefiniowanych [poziomów spójności](consistency-levels.md) , które można wybrać. 

W poniższej tabeli przedstawiono liczbę jednostek ru wymaganych do wykonania operacji odczytu i zapisu na podstawie elementu danych o rozmiarze 1 KB i 100 artykułów bazy wiedzy z wyłączonym domyślnym automatycznym indeksem. 

|Rozmiar elementu|Odczyt 1|1 zapisu|
|-------------|------|-------|
|1 KB|1 JEDNOSTKA ŻĄDANIA|5 jednostek ru|
|100 KB|10 jednostek RU|50 (RUS)|

## <a name="cost-of-reads-and-writes"></a>Koszt odczyty i zapisy

Jeśli zainicjujesz 1 000 RU/s, ta kwota jest równa 3 600 000 RU/godzina i będzie kosztować $0,08 za godzinę (w Stanach Zjednoczonych i Europie). W przypadku elementu danych o rozmiarze 1 KB oznacza to, że można korzystać z 3 600 000 odczytów lub 720 000 zapisów (3 600 000 RU/5) przy użyciu zainicjowanej przepływności. Znormalizowany do miliona odczytów i zapisów, koszty byłyby $0,022/mln od odczytu ($0,08/3,6) i $0.111/mln zapisów ($0,08/0,72). Koszt za mln staje się minimalny, jak pokazano w poniższej tabeli.

|Rozmiar elementu|Koszt operacji odczytu 1 000 000|Koszt zapisu 1 000 000|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Większość podstawowych obiektów blob lub obiekt Magazyny usług opłaty 0,40 USD za milion transakcji odczytu, a 5 USD za milion zapisu transakcji. Jeśli jest używana optymalnie, Cosmos DB może być do 98% tańsze niż te inne rozwiązania (dla transakcji 1 KB).

## <a name="next-steps"></a>Następne kroki

* Użyj [kalkulatora ru](https://cosmos.azure.com/capacitycalculator/) , aby oszacować przepływność dla obciążeń.

