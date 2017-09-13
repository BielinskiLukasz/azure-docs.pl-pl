---
title: "Samouczek dotyczący bazy danych MongoDB, platformy React i języka Node.js na platformie Azure | Microsoft Docs"
description: "Dowiedz się, jak utworzyć aplikację bazy danych MongoDB na platformie React i Node.js w usłudze Azure Cosmos DB przy użyciu tych samych interfejsów API, co używane w usłudze MongoDB, oglądając tę serię samouczków opartą na filmach wideo."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 622a26da8b7d5f5c3a242c5c88a4ae479095bf6f
ms.contentlocale: pl-pl
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Tworzenie aplikacji bazy danych MongoDB przy użyciu platformy React i usługi Azure Cosmos DB  

W tym wieloczęściowym samouczku wideo przedstawiono sposób tworzenia głównej aplikacji do śledzenia przy użyciu zaplecza platformy React. Aplikacja używająca języka Node i usługi Express dla serwera łączy się z usługą Azure Cosmos DB przy użyciu [interfejsu API usługi MongoDB](mongodb-introduction.md), a następnie łączy zaplecze platformy React z częścią serwera w aplikacji. W samouczku przedstawiono również sposób skalowania typu „wskaż i kliknij” bazy usługi Azure Cosmos DB w witrynie Azure Portal oraz sposób wdrażania aplikacji w Internecie, tak aby wszyscy użytkownicy mogli śledzić swoje ulubione aplikacje główne. 

Usługa [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) obsługuje połączenia klienta bazy danych MongoDB, dlatego można używać usługi zamiast bazy danych MongoDB, ale stosując ten sam kod już używany w dla aplikacji bazy danych MongoDB. Zyskuje się w ten sposób dodatkowe korzyści, takie jak proste wdrażanie w chmurze, skalowanie i niezwykle szybkie operacje odczytu i zapisu.  

W tym wieloczęściowym samouczku opisano następujące zadania:

> [!div class="checklist"]
> * Wprowadzenie
> * Konfigurowanie projektu
> * Tworzenie interfejsu użytkownika na platformie React
> * Tworzenie konta usługi Azure Cosmos DB przy użyciu witryny Azure Portal
> * Łączenie z usługą Azure Cosmos DB przy użyciu wtyczki Mongoose
> * Dodawanie operacji platformy React, tworzenia, aktualizacji i usuwania do aplikacji

Chcesz skompilować tę samą aplikację przy użyciu usługi Angular? Zobacz [samouczek usługi Angular w postaci serii filmów wideo](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Zakończony projekt
Pobierz ukończoną aplikację [z serwisu GitHub](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Wprowadzenie 

W tym filmie Burke Holland przedstawia wprowadzenie do usługi Azure Cosmos DB i przeprowadzi Cię przez aplikację tworzoną w tej serii filmów wideo. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Konfiguracja projektu

W tym filmie wideo pokazano, jak skonfigurować usługę Express i platformę React w tym samym projekcie. Następnie Burke prezentuje przewodnik po kodzie w projekcie.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Tworzenie interfejsu użytkownika

W tym filmie wideo pokazano, jak utworzyć interfejs użytkownika aplikacji na platformie React. 

> [!NOTE]
> Arkusze CSS wspomniane w filmie znajdują się w [repozytorium serwisu GitHub react-cosmosdb](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Łączenie z usługą Azure Cosmos DB

W tym filmie pokazano, jak utworzyć konto usługi Azure Cosmos DB w witrynie Azure Portal, zainstalować pakiety bazy danych MongoDB i wtyczki Mongoose, a następnie połączyć aplikację z nowo utworzonym kontem przy użyciu parametrów połączenia usługi Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Odczytywanie i tworzenie elementów głównych w aplikacji

W tym filmie wideo pokazano, jak odczytywać elementy główne i tworzyć elementy główne w bazie danych usługi Cosmos DB, a także jak testować te metody przy użyciu interfejsu użytkownika platformy React i narzędzia Postman. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Usuwanie i aktualizowanie elementów głównych w aplikacji

W tym filmie wideo pokazano sposób usuwania i aktualizowania elementów głównych z poziomu aplikacji oraz wyświetlania aktualizacji w interfejsie użytkownika. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Kończenie aplikacji

W tym filmie wideo pokazano, jak ukończyć aplikację i zakończyć dołączanie interfejsu użytkownika do interfejsu API zaplecza. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz w przyszłości korzystać z tej aplikacji, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego samouczka. 

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji przy użyciu platformy React, języka Node, usługi Express oraz usługi Azure Cosmos DB 
> * Tworzenie konta usługi Azure Cosmos DB
> * Łączenie aplikacji z kontem usługi Azure Cosmos DB
> * Testowanie aplikacji przy użyciu narzędzia Postman
> * Uruchamianie aplikacji i dodawanie elementów głównych do bazy danych

Zajrzyj tu ponownie, aby sprawdzić, czy udostępniono dodatkowy film wideo dotyczący wdrażania aplikacji i globalnego replikowania danych.

Możesz przejść do następnego samouczka i dowiedzieć się, jak importować dane MongoDB do usługi Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](mongodb-migrate.md)
 

