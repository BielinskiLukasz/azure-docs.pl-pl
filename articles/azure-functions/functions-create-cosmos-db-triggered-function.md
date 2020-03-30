---
title: Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB
description: Utwórz za pomocą usługi Azure Functions funkcję niewymagającą użycia serwera wywoływaną w przypadku dodania danych do bazy danych w usłudze Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: quickstart
ms.date: 10/02/2018
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 830c7cdee247118ed24fc9b3a2a9efe8609c75d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75863297"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB

Dowiedz się, jak utworzyć funkcję wyzwalaną w przypadku dodania lub zmiany danych w usłudze Azure Cosmos DB. Aby uzyskać więcej informacji o usłudze Azure Cosmos DB, zobacz [Azure Cosmos DB: Serverless database computing using Azure Functions](../cosmos-db/serverless-computing-database.md) (Azure Cosmos DB: przetwarzanie danych w bazie danych bez użycia serwera dzięki usłudze Azure Functions).

![Wyświetlanie komunikatu w dziennikach.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

+ Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Do utworzenia wyzwalacza konieczne jest posiadanie konta usługi Azure Cosmos DB, które używa interfejsu API SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Tworzenie wyzwalacza usługi Azure Cosmos DB

1. Rozwiń aplikację funkcji **+** i kliknij przycisk obok **pozycji Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **W portalu**, a następnie opcję **Kontynuuj**. W przeciwnym razie przejdź do kroku trzeciego.

   ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Wybierz pozycję **Więcej szablonów**, a następnie pozycję **Zakończ i wyświetl szablony**.

    ![Wybieranie pozycji Więcej szablonów w przewodniku Szybki start usługi Functions](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. W polu wyszukiwania wpisz `cosmos`, a następnie wybierz szablon **Wyzwalacz usługi Azure Cosmos DB**.

1. Jeśli zostanie wyświetlony monit, wybierz **pozycję Zainstaluj,** aby zainstalować rozszerzenie usługi Azure Cosmos DB w aplikacji funkcji. Po pomyślnym zakończeniu instalacji wybierz pozycję **Kontynuuj**.

    ![Instalowanie rozszerzeń powiązania](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Skonfiguruj nowy wyzwalacz, wprowadzając ustawienia określone w tabeli znajdującej się poniżej obrazu.

    ![Tworzenie funkcji wyzwalanej przez usługę Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nazwa** | Domyślne | Użyj domyślnej nazwy funkcji sugerowanej przez szablon.|
    | **Połączenie konta usługi Azure Cosmos DB** | Nowe ustawienie | Wybierz pozycję **Nowy**, swoją **subskrypcję**, utworzone wcześniej **konto bazy danych** i polecenie **Wybierz**. Spowoduje to utworzenie ustawienia aplikacji na potrzeby połączenia konta. To ustawienie jest używane przez powiązanie do nawiązywania połączenia z bazą danych. |
    | **Nazwa kontenera** | Items | Nazwa kontenera, który ma być monitorowany. |
    | **Tworzenie kontenera dzierżawy, jeśli nie istnieje** | Zaznaczone | Kontener jeszcze nie istnieje, więc utwórz go. |
    | **Nazwa bazy danych** | Zadania | Nazwa bazy danych z kontenerem, który ma być monitorowany. |

1. Kliknij pozycję **Utwórz**, aby utworzyć funkcję wyzwalaną usługi Azure Cosmos DB. Po utworzeniu funkcji zostanie wyświetlony kod funkcji oparty na szablonie.  

    ![Szablon funkcji usługi Azure Cosmos DB w języku C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Ten szablon funkcji zapisuje w dziennikach liczbę dokumentów oraz identyfikator pierwszego dokumentu.

Następnie możesz połączyć się z kontem usługi `Items` Azure `Tasks` Cosmos DB i utworzyć kontener w bazie danych.

## <a name="create-the-items-container"></a>Tworzenie kontenera Elementy

1. Otwórz drugie wystąpienie witryny [Azure Portal](https://portal.azure.com) w nowej karcie przeglądarki.

1. Rozwiń pasek ikon po lewej stronie portalu, wpisz ciąg `cosmos` w polu wyszukiwania, a następnie wybierz pozycję **Azure Cosmos DB**.

    ![Wyszukiwanie usługi Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Wybierz konto usługi Azure Cosmos DB, a następnie wybierz pozycję **Eksplorator danych**. 

1. W obszarze **SQL API**wybierz pozycję Baza danych **zadań** i wybierz pozycję **Nowy kontener**.

    ![Tworzenie kontenera](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. W **obszarze Dodaj kontener**użyj ustawień pokazanych w tabeli pod obrazem. 

    ![Definiowanie kontenera Zadania](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Ustawienie|Sugerowana wartość|Opis |
    | ---|---|--- |
    | **Identyfikator bazy danych** | Zadania |Nazwa nowej bazy danych. Musi być zgodna z nazwą zdefiniowaną w powiązaniu funkcji. |
    | **Identyfikator kontenera** | Items | Nazwa nowego kontenera. Musi być zgodna z nazwą zdefiniowaną w powiązaniu funkcji.  |
    | **[Klucz partycji](../cosmos-db/partition-data.md)** | /category|Klucz partycji służący do równomiernego dystrybuowania danych do każdej partycji. Wybranie właściwego klucza partycji jest ważne przy tworzeniu kontenera performant. | 
    | **Przepływność** |400 RU| Użyj wartości domyślnej. Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę. |    

1. Kliknij **przycisk OK,** aby utworzyć kontener Elementy. Może upłynąć trochę czasu, aby uzyskać utworzony kontener.

Po kontener określony w powiązaniu funkcji istnieje, można przetestować funkcję, dodając elementy do tego nowego kontenera.

## <a name="test-the-function"></a>Testowanie funkcji

1. Rozwiń nowy kontener **Elementy** w Eksploratorze danych, wybierz pozycję **Elementy,** a następnie wybierz pozycję **Nowy element**.

    ![Tworzenie elementu w kontenerze Elementy](./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png)

1. Zastąp zawartość nowego elementu następującą zawartością, a następnie wybierz pozycję **Zapisz**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Przejdź do pierwszej karty przeglądarki, zawierającej funkcję w portalu. Rozwiń dzienniki funkcji i sprawdź, czy nowy dokument spowodował wyzwolenie funkcji. Zobaczysz, że wartość identyfikatora dokumentu `task1` jest zapisana w dziennikach. 

    ![Wyświetlanie komunikatu w dziennikach.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Opcjonalnie) Wróć do dokumentu, wprowadź zmianę i kliknij pozycję **Aktualizuj**. Następnie wróć do dzienników funkcji i sprawdź, czy aktualizacja również spowodowała wyzwolenie funkcji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono funkcję uruchamianą w przypadku dodania lub zmodyfikowania dokumentu w usłudze Azure Cosmos DB. Aby uzyskać więcej informacji na temat wyzwalaczy bazy danych Azure Cosmos DB, zobacz [Azure Cosmos DB bindings for Azure Functions](functions-bindings-cosmosdb.md) (Powiązania bazy danych Azure Cosmos DB na potrzeby usługi Azure Functions).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
