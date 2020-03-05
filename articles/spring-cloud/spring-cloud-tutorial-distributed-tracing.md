---
title: Samouczek — używanie rozproszonego śledzenia z chmurą Azure wiosennej
description: W tym samouczku pokazano, jak używać śledzenia rozproszonego chmury wiosennej za pomocą usługi Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273209"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Korzystanie z rozproszonego śledzenia w chmurze Azure wiosennej

Dzięki narzędziom do śledzenia rozproszonym w chmurze Azure wiosennej można łatwo debugować i monitorować złożone problemy. Chmura ze sprężyną Azure integruje się z [chmurą Sleuth](https://spring.io/projects/spring-cloud-sleuth) z platformą [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)Azure. Ta integracja zapewnia zaawansowane możliwości śledzenia rozproszonego na podstawie Azure Portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Włącz śledzenie rozproszone w Azure Portal.
> * Dodaj wiosenną Sleuth chmurową do swojej aplikacji.
> * Wyświetl mapy zależności dla aplikacji mikrousług.
> * Przeszukaj dane śledzenia przy użyciu różnych filtrów.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest usługa w chmurze Azure wiosny, która jest już zainicjowana i uruchomiona. Ukończ [Przewodnik Szybki Start dotyczący wdrażania aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure](spring-cloud-quickstart-launch-app-cli.md) w celu aprowizacji i uruchamiania usługi w chmurze Azure wiosennej.
    
## <a name="add-dependencies"></a>Dodaj zależności

1. Dodaj następujący wiersz do pliku Application. Properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Po tej zmianie nadawca Zipkin może wysłać do sieci Web.

1. Pomiń ten krok, jeśli korzystasz [z naszego przewodnika przygotowującego aplikację w chmurze platformy Azure](spring-cloud-tutorial-prepare-app-deployment.md). W przeciwnym razie przejdź do lokalnego środowiska deweloperskiego i edytuj plik pliku pom. XML w celu uwzględnienia następującej zależności Sleuth w chmurze:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Skompiluj i Wdróż ponownie dla usługi w chmurze Azure wiosny, aby odzwierciedlić te zmiany.

## <a name="modify-the-sample-rate"></a>Modyfikuj częstotliwość próbkowania

Możesz zmienić częstotliwość zbierania danych telemetrycznych, modyfikując częstotliwość próbkowania. Na przykład jeśli chcesz próbkować bardzo często, Otwórz plik Application. Properties i zmień następujący wiersz:

```xml
spring.sleuth.sampler.probability=0.5
```

Jeśli aplikacja została już skompilowana i wdrożona, można zmodyfikować częstotliwość próbkowania. Aby to zrobić, Dodaj poprzedni wiersz jako zmienną środowiskową w interfejsie wiersza polecenia platformy Azure lub w Azure Portal.

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal.
1. Na stronie **monitorowanie** wybierz opcję **śledzenie rozproszone**.
1. Wybierz pozycję **Edytuj ustawienie** , aby edytować lub dodać nowe ustawienie.
1. Utwórz nowe zapytanie Application Insights lub Wybierz istniejące.
1. Wybierz kategorię rejestrowania, którą chcesz monitorować, a następnie określ czas przechowywania w dniach.
1. Wybierz pozycję **Zastosuj** , aby zastosować nowe śledzenie.

## <a name="view-the-application-map"></a>Wyświetlanie mapy aplikacji

Wróć do strony **śledzenie rozproszone** i wybierz pozycję **Wyświetl mapę aplikacji**. Przejrzyj wizualną reprezentację ustawień aplikacji i monitorowania. Aby dowiedzieć się, jak używać mapy aplikacji, zobacz Application [map: Klasyfikacja Distributed Applications](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Użyj wyszukiwania

Funkcja Search umożliwia wykonywanie zapytań dotyczących innych określonych elementów telemetrii. Na stronie **śledzenie rozproszone** wybierz pozycję **Wyszukaj**. Aby uzyskać więcej informacji na temat korzystania z funkcji wyszukiwania, zobacz [Używanie wyszukiwania w Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Użyj Application Insights

Application Insights udostępnia funkcje monitorowania oprócz mapy aplikacji i funkcji wyszukiwania. Wyszukaj w Azure Portal nazwę aplikacji, a następnie otwórz stronę Application Insights, aby znaleźć informacje dotyczące monitorowania. Aby uzyskać więcej wskazówek na temat korzystania z tych narzędzi, zapoznaj się z tematem [Azure monitor zapytania dziennika](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Wyłącz Application Insights

1. Przejdź do strony usługi w chmurze ze sprężyną Azure w Azure Portal.
1. W obszarze **monitorowanie**wybierz pozycję **śledzenie rozproszone**.
1. Wybierz pozycję **Wyłącz** , aby wyłączyć Application Insights.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób włączania i zrozumienia śledzenia rozproszonego w chmurze Azure wiosennej. Aby dowiedzieć się, jak powiązać aplikację z bazą danych Azure Cosmos DB, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Dowiedz się, jak powiązać z bazą danych Azure Cosmos DB](spring-cloud-tutorial-bind-cosmos.md)
