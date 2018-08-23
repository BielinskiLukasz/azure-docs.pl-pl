---
title: Parametry połączenia bazy danych MongoDB dla konta usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć aplikację bazy danych MongoDB na konto usługi Azure Cosmos DB przy użyciu parametrów połączenia MongoDB.
keywords: Parametry połączenia bazy danych mongodb
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sclyon
ms.openlocfilehash: ad8d6fe36c289c4c9e37689e1c7d755dc3bf9048
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054956"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Łączenie aplikacji bazy danych MongoDB w usłudze Azure Cosmos DB
Dowiedz się, jak połączyć aplikację bazy danych MongoDB na konto usługi Azure Cosmos DB przy użyciu parametrów połączenia MongoDB. Bazę danych usługi Azure Cosmos DB można następnie użyć jako danych magazynu dla aplikacji MongoDB. 

Ten samouczek zawiera dwa sposoby, aby pobrać informacje o parametrach połączenia:

- [Szybki start — metoda](#QuickstartConnection), do użytku z programem .NET, Node.js, powłoki bazy danych MongoDB, Java i Python sterowników
- [Metoda ciąg połączenia niestandardowego](#GetCustomConnection), do użytku z innych sterowników

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta platformy Azure, Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) teraz. 
- Konto usługi Azure Cosmos DB. Aby uzyskać instrukcje, zobacz [tworzenie aplikacji internetowej interfejsu API usługi MongoDB przy użyciu platformy .NET i witryny Azure portal](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Pobieranie parametrów połączenia bazy danych MongoDB przy użyciu szybkiego startu
1. W przeglądarce internetowej, zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W **usługi Azure Cosmos DB** bloku wybierz interfejs API dla konta bazy danych MongoDB. 
3. W okienku po lewej stronie bloku konta kliknij **— szybki start**. 
4. Wybierz platformę (**.NET**, **Node.js**, **powłoka MongoDB**, **Java**, **Python**). Jeśli nie widzisz, sterownik lub narzędzie na liście, nie martw się — stale dokumentujemy kolejne fragmentu kodu połączenia. Skomentuj poniżej na chcesz zobaczyć. Aby dowiedzieć się, jak tworzyć własne połączenia, przeczytaj [uzyskać informacje o parametrach połączenia dla konta](#GetCustomConnection).
5. Skopiuj i Wklej fragment kodu w aplikacji bazy danych MongoDB.

    ![Bloku szybki start](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Pobieranie parametrów połączenia bazy danych MongoDB do dostosowywania
1. W przeglądarce internetowej, zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W **usługi Azure Cosmos DB** bloku wybierz interfejs API dla konta bazy danych MongoDB. 
3. W okienku po lewej stronie bloku konta kliknij **parametry połączenia**. 
4. **Parametry połączenia** zostanie otwarty blok. Ma on wszystkie informacje niezbędne do połączenia do konta za pośrednictwem sterownika dla bazy danych MongoDB, w tym ciągu połączenia preconstructed.

    ![Blok Parametry połączenia](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Wymagania dotyczące parametrów połączenia
> [!Important]
> Usługa Azure Cosmos DB ma rygorystyczne zabezpieczenia wymagania i standardy. Konta usługi Azure Cosmos DB wymaga uwierzytelniania i bezpiecznej komunikacji za pośrednictwem *SSL*. 
>
>

Usługa Azure Cosmos DB obsługuje standardowe bazy danych MongoDB format ciągu połączenia identyfikatora URI, przy użyciu kilku określonych wymagań: konta usługi Azure Cosmos DB wymaga uwierzytelniania i bezpiecznej komunikacji SSL. Dlatego jest format parametrów połączenia:

    mongodb://username:password@host:port/[database]?ssl=true

Wartości tych parametrów są dostępne w **parametry połączenia** bloku przedstawionej wcześniej:

* Nazwa użytkownika (wymagana): Nazwa konta usługi Azure Cosmos DB.
* Hasło (wymagane): hasło konta usługi Azure Cosmos DB.
* Hosta (wymagane): nazwa FQDN usługi Azure Cosmos DB.
* Port (wymagane): 10255.
* Bazy danych (opcjonalnie): bazy danych, która korzysta z połączenia. Jeśli żadna baza danych zostanie podany, domyślna baza danych jest "test".
* Protokół SSL = true (wymagane)

Rozważmy na przykład konta pokazanego na **parametry połączenia** bloku. Jest prawidłowe parametry połączenia:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [korzystać z programu Studio 3T (z programu MongoChef)](mongodb-mongochef.md) z interfejsem API usługi Azure Cosmos DB dla konta bazy danych MongoDB.
* Poznaj interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB, wyświetlając [przykłady](mongodb-samples.md).
