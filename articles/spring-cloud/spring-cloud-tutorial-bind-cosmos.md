---
title: Samouczek — Powiązywanie Azure Cosmos DB ze swoją aplikacją w chmurze platformy Azure
description: W tym samouczku dowiesz się, jak powiązać Azure Cosmos DB ze swoją aplikacją w chmurze platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 8eeb67419d2da90ff1e2d2beb8cb59a85c3bbacb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461620"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Powiązywanie bazy danych Azure Cosmos DB z aplikacją w chmurze platformy Azure

Zamiast ręcznego konfigurowania aplikacji do rozruchu sprężynowego można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami za pomocą chmury sieci platformy Azure. W tym artykule pokazano, jak powiązać aplikację z bazą danych Azure Cosmos DB.

Wymagania wstępne:

* Wdrożone wystąpienie chmury Azure wiosennej. Aby rozpocząć, Skorzystaj z naszego [przewodnika Szybki Start dotyczącego wdrażania za pośrednictwem interfejsu wiersza polecenia platformy Azure](spring-cloud-quickstart-launch-app-cli.md) .
* Konto Azure Cosmos DB z minimalnym poziomem uprawnień współautor.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB powiązania

Azure Cosmos DB ma pięć różnych typów interfejsów API, które obsługują powiązania. W poniższej procedurze pokazano, jak z nich korzystać:

1. Tworzy bazę danych usługi Azure Cosmos DB. Zapoznaj się z przewodnikiem Szybki Start dotyczącym [tworzenia bazy danych](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) w celu uzyskania pomocy. 

1. Zapisz nazwę bazy danych. W przypadku tej procedury Nazwa bazy danych to **TestDB**.

1. Dodaj jedną z następujących zależności do pliku pliku pom. XML aplikacji chmurowej ze sprężyną Azure. Wybierz zależność, która jest odpowiednia dla typu interfejsu API.

    * Typ interfejsu API: rdzeń (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Typ interfejsu API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Typ interfejsu API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Typ interfejsu API: Gremlin (Graph)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Typ interfejsu API: tabela platformy Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Użyj `az spring-cloud app update`, aby zaktualizować bieżące wdrożenie, lub użyj `az spring-cloud app deployment create`, aby utworzyć nowe wdrożenie. Te polecenia spowodują aktualizację lub utworzenie aplikacji z nową zależnością.

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal. Przejdź do **pulpitu nawigacyjnego aplikacji** i wybierz aplikację, do której ma zostać utworzone powiązanie Azure Cosmos DB. Ta aplikacja jest taka sama, która została zaktualizowana lub wdrożona w poprzednim kroku.

1. Wybierz pozycję **powiązanie usługi**, a następnie wybierz pozycję **Utwórz powiązanie usługi**. Aby wypełnić formularz, wybierz:
   * Wartość **typu powiązania** **Azure Cosmos DB**.
   * Typ interfejsu API.
   * Nazwa bazy danych.
   * Konto Azure Cosmos DB.

    > [!NOTE]
    > Jeśli używasz Cassandra, użyj przestrzeni kluczy dla nazwy bazy danych.

1. Uruchom ponownie aplikację, wybierając pozycję **Uruchom ponownie** na stronie aplikacji.

1. Aby upewnić się, że usługa jest powiązana prawidłowo, wybierz nazwę powiązania i sprawdź jej szczegóły. Pole `property` powinno wyglądać podobnie do tego przykładu:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób powiązania aplikacji w chmurze platformy Azure z bazą danych Azure Cosmos DB. Aby dowiedzieć się, jak powiązać aplikację z pamięcią podręczną platformy Azure dla pamięci podręcznej Redis, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak utworzyć powiązanie z pamięcią podręczną Azure dla pamięci podręcznej Redis](spring-cloud-tutorial-bind-redis.md)
