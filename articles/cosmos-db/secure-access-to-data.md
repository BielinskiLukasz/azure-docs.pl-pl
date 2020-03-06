---
title: Dowiedz się, jak zabezpieczyć dostęp do danych w Azure Cosmos DB
description: Poznaj koncepcje kontroli dostępu w Azure Cosmos DB, w tym klucze główne, klucze tylko do odczytu, użytkowników i uprawnienia.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 448b14168e85e75b7ed19e189600186ce11c2902
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395494"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Bezpieczny dostęp do danych w Azure Cosmos DB

Ten artykuł zawiera omówienie zabezpieczania dostępu do danych przechowywanych w [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB używa dwóch typów kluczy do uwierzytelniania użytkowników i zapewnienia dostępu do jego danych i zasobów. 

|Typ klucza|Zasoby|
|---|---|
|[Klucze główne](#master-keys) |Używany do zasobów administracyjnych: konta bazy danych, bazy danych, użytkownicy i uprawnienia|
|[Tokeny zasobów](#resource-tokens)|Używany do zasobów aplikacji: kontenery, dokumenty, załączniki, procedury składowane, wyzwalacze i UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>Klucze główne

Klucze główne zapewniają dostęp do wszystkich zasobów administracyjnych dla konta bazy danych. Klucze główne:

- Zapewnianie dostępu do kont, baz danych, użytkowników i uprawnień. 
- Nie można użyć, aby zapewnić szczegółowy dostęp do kontenerów i dokumentów.
- Są tworzone podczas tworzenia konta.
- Można ją wygenerować w dowolnym momencie.

Każde konto składa się z dwóch kluczy głównych: klucza podstawowego i klucza pomocniczego. Dwa klucze polega na tym, że można generować lub przetwarzać klucze, zapewniając ciągły dostęp do konta i danych.

Oprócz dwóch kluczy głównych dla konta Cosmos DB istnieją dwa klucze tylko do odczytu. Te klucze tylko do odczytu zezwalają na operacje odczytu tylko na tym koncie. Klucze tylko do odczytu nie zapewniają dostępu do uprawnień do odczytu zasobów.

Klucze główne, pomocnicze, tylko do odczytu i odczyt-zapis można pobrać i ponownie wygenerować przy użyciu Azure Portal. Aby uzyskać instrukcje, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#regenerate-account-key).

![Kontrola dostępu (IAM) w Azure Portal-demonstrowanie zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Proces obracania klucza głównego jest prosty. Przejdź do Azure Portal, aby pobrać klucz pomocniczy, a następnie zastąp klucz podstawowy kluczem pomocniczym w aplikacji, a następnie obróć klucz podstawowy w Azure Portal.

![Rotacja klucza głównego w Azure Portal-demonstrowanie zabezpieczeń bazy danych NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Przykład kodu, aby użyć klucza głównego

Poniższy przykład kodu ilustruje, jak używać punktu końcowego konta Cosmos DB i klucza głównego do tworzenia wystąpienia DocumentClient i utworzenia bazy danych.

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

## Tokeny zasobów<a id="resource-tokens"></a>

Tokeny zasobów zapewniają dostęp do zasobów aplikacji w ramach bazy danych. Tokeny zasobów:

- Zapewnianie dostępu do określonych kontenerów, kluczy partycji, dokumentów, załączników, procedur składowanych, wyzwalaczy i UDF.
- Są tworzone, gdy [użytkownik](#users) uzyskuje [uprawnienia](#permissions) do określonego zasobu.
- Są ponownie tworzone, gdy zasób uprawnień jest podejmowany podczas działania przez POST, GET lub PUT.
- Użyj tokenu zasobu skrótu przeznaczonego dla użytkownika, zasobu i uprawnienia.
- Są powiązane z dostosowywanym okresem ważności. Domyślny prawidłowy zakres czasu wynosi godzinę. Okres istnienia tokenu można jednak określić jawnie, maksymalnie pięć godzin.
- Podaj bezpieczną alternatywę do wydawania klucza głównego.
- Umożliwianie klientom odczytywania, zapisywania i usuwania zasobów na koncie Cosmos DB zgodnie z przyznanymi uprawnieniami.

Możesz użyć tokenu zasobu (przez utworzenie Cosmos DB użytkowników i uprawnień), jeśli chcesz zapewnić dostęp do zasobów na koncie Cosmos DB do klienta, którego nie można zaufać z kluczem głównym.  

Tokeny zasobów Cosmos DB zapewniają bezpieczną alternatywę, która umożliwia klientom odczytywanie, zapisywanie i usuwanie zasobów na koncie Cosmos DB zgodnie z przyznanymi uprawnieniami i bez potrzeby dla klucza głównego lub tylko do odczytu.

Poniżej przedstawiono typowy Wzorzec projektowy, w którym tokeny zasobów mogą być żądane, generowane i dostarczane do klientów:

1. Usługa warstwy środkowej jest skonfigurowana do obsługi aplikacji mobilnej w celu udostępniania zdjęć użytkowników.
2. Usługa warstwy środkowej dysponuje kluczem głównym konta Cosmos DB.
3. Aplikacja Photo jest instalowana na urządzeniach przenośnych użytkowników końcowych.
4. W przypadku logowania Aplikacja Photo nawiązuje tożsamość użytkownika z usługą warstwy środkowej. Mechanizm tworzenia tożsamości jest całkowicie do aplikacji.
5. Po nawiązaniu tożsamości usługa warstwy średniej żąda uprawnień na podstawie tożsamości.
6. Usługa warstwy średniej wysyła token zasobu z powrotem do aplikacji na telefon.
7. Aplikacja na telefon może nadal używać tokenu zasobu do bezpośredniego dostępu do zasobów Cosmos DB z uprawnieniami zdefiniowanymi przez token zasobu i dla interwału dozwolonego przez token zasobu.
8. Po wygaśnięciu tokenu zasobu kolejne żądania otrzymują nieautoryzowany wyjątek 401.  W tym momencie Aplikacja telefoniczna przetworzy tożsamość i zażąda nowego tokenu zasobu.

    ![Przepływ pracy tokenów zasobów Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

Generowanie tokenów zasobów i zarządzanie nimi jest obsługiwane przez natywne biblioteki klienckie Cosmos DB. Jeśli jednak używasz REST, musisz utworzyć nagłówki żądania/uwierzytelniania. Aby uzyskać więcej informacji na temat tworzenia nagłówków uwierzytelniania dla usługi REST, zobacz [Access Control on Cosmos DB Resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) lub Code Source for the [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) lub [Node. js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Przykład usługi warstwy środkowej używanej do generowania lub brokera tokenów zasobów można znaleźć w [aplikacji ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

## Użytkownikowi<a id="users"></a>

Azure Cosmos DB użytkownicy są skojarzeni z bazą danych Cosmos.  Każda baza danych może zawierać co najmniej zero Cosmos DB użytkowników. Poniższy przykład kodu pokazuje, jak utworzyć użytkownika Cosmos DB przy użyciu [zestawu Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Każdy użytkownik Cosmos DB ma metodę ReadAsync (), która może być używana do pobierania listy [uprawnień](#permissions) skojarzonych z użytkownikiem.

## Uprawnienia<a id="permissions"></a>

Zasób uprawnienia jest skojarzony z użytkownikiem i przypisany do niego w kontenerze, a także na poziomie klucza partycji. Każdy użytkownik może zawierać co najmniej zero uprawnień. Zasób uprawnienia zapewnia dostęp do tokenu zabezpieczającego wymaganego przez użytkownika podczas próby dostępu do określonego kontenera lub danych w określonym kluczu partycji. Dostępne są dwa poziomy dostępu, które mogą być udostępniane przez zasób uprawnienia:

- Wszystko: użytkownik ma pełne uprawnienia do zasobu.
- Odczytaj: użytkownik może odczytać tylko zawartość zasobu, ale nie może wykonywać operacji zapisu, aktualizowania ani usuwania dla zasobu.

> [!NOTE]
> Aby można było uruchomić procedury składowane, użytkownik musi mieć uprawnienie wszystkie do kontenera, w którym zostanie uruchomiona procedura składowana.

### <a name="code-sample-to-create-permission"></a>Przykład kodu do utworzenia uprawnienia

Poniższy przykładowy kod przedstawia sposób tworzenia zasobu uprawnienia, odczytywania tokenu zasobu zasobu uprawnienia i kojarzenia uprawnień z utworzonym powyżej [użytkownikiem](#users) .

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Przykład kodu do odczytu dla użytkownika

Poniższy fragment kodu pokazuje, jak pobrać uprawnienia skojarzone z użytkownikiem utworzonym powyżej i utworzyć wystąpienie nowego CosmosClient w imieniu użytkownika, w zakresie do jednego klucza partycji.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Dodawanie użytkowników i przypisywanie ról

Aby dodać do konta użytkownika dostęp do czytnika konta Azure Cosmos DB, należy wykonać następujące czynności w Azure Portal.

1. Otwórz Azure Portal i wybierz swoje konto Azure Cosmos DB.
2. Kliknij kartę **Kontrola dostępu (IAM)** , a następnie kliknij pozycję **+ Dodaj przypisanie roli**.
3. W okienku **Dodaj przypisanie roli** w polu **rola** wybierz pozycję **Cosmos DB rolę czytelnika konta**.
4. W **polu Przypisz dostęp do**wybierz pozycję **użytkownik, Grupa lub aplikacja usługi Azure AD**.
5. Wybierz użytkownika, grupę lub aplikację w katalogu, do którego chcesz udzielić dostępu.  Katalog można wyszukać według nazwy wyświetlanej, adresu e-mail lub identyfikatorów obiektów.
    Wybrany użytkownik, Grupa lub aplikacja pojawi się na liście wybranych członków.
6. Kliknij przycisk **Save** (Zapisz).

Jednostka może teraz odczytywać Azure Cosmos DB zasoby.

## <a name="delete-or-export-user-data"></a>Usuwanie lub eksportowanie danych użytkownika

Azure Cosmos DB pozwala wyszukiwać, wybierać, modyfikować i usuwać dane osobowe znajdujące się w bazie danych lub kolekcjach. Azure Cosmos DB udostępnia interfejsy API do znajdowania i usuwania danych osobowych, jednak odpowiedzialność za korzystanie z interfejsów API i Definiowanie logiki wymaganej do wymazania danych osobowych. Każdy interfejs API z obsługą wielu modeli (SQL, MongoDB, Gremlin, Cassandra, tabela) oferuje różne zestawy SDK języka, które zawierają metody do wyszukiwania i usuwania danych osobowych. Możesz również włączyć funkcję [Time to Live (TTL)](time-to-live.md) , aby automatycznie usuwać dane po upływie określonego czasu, bez ponoszenia dodatkowych kosztów.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat zabezpieczeń bazy danych Cosmos, zobacz [Cosmos DB zabezpieczenia bazy danych](database-security.md).
- Aby dowiedzieć się, jak utworzyć tokeny autoryzacji Azure Cosmos DB, zobacz [Access Control dotyczące zasobów Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
- Przykłady zarządzania użytkownikami z użytkownikami i uprawnieniami, [przykłady zarządzania użytkownikami w programie .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
