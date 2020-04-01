---
title: Korzystanie z interfejsu API tabel usługi Azure Cosmos DB i usługi Azure Table Storage z usługą Ruby
description: Przechowywanie danych strukturalnych w chmurze za pomocą usługi Azure Table Storage lub interfejsu Table API usługi Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 04/05/2018
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 7994b478321c925b3eab73291a109d50b9066fef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76770874"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Jak korzystać z usługi Azure Table Storage i interfejsu Table API usługi Azure Cosmos DB przy użyciu języka Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługi Azure Table Storage oraz interfejsu Table API usługi Azure Cosmos DB. Przykłady są napisane w języku Ruby i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Przedstawione scenariusze obejmują **tworzenie i usuwanie tabel oraz wstawianie jednostek w tabeli i wykonywanie względem nich zapytań**.

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Dodawanie dostępu do usługi Storage lub Azure Cosmos DB
Aby użyć usługi Azure Storage lub Azure Cosmos DB, należy pobrać pakiet platformy Azure dla języka Ruby, który zawiera zestaw wygodnych bibliotek służących do komunikacji z usługami Table REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (system Windows), **Terminal** (system Mac) lub **Bash** (system Unix).
2. Wpisz polecenie **gem install azure-storage-table** w oknie polecenia, aby zainstalować rozwiązanie gem i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Użyj swojego ulubionego edytora tekstu, aby dodać następujący element na początku pliku w języku Ruby, w którym planujesz użyć usługi Storage:

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Dodawanie połączenia z usługą Azure Storage
Moduł usługi Azure Storage odczytuje zmienne środowiskowe **AZURE_STORAGE_ACCOUNT** i **AZURE_STORAGE_ACCESS_KEY**, aby uzyskać informacje wymagane do nawiązania połączenia z kontem usługi Azure Storage. Jeśli te zmienne środowiskowe nie są ustawione, należy za pomocą następującego kodu określić informacje o koncie przed użyciem obiektu **Azure::Storage::Table::TableService**:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Aby uzyskać te wartości z klasycznego konta magazynu lub konta magazynu menedżera zasobów w witrynie Azure Portal:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do konta magazynu, którego chcesz użyć.
3. W bloku Ustawienia po prawej stronie kliknij pozycję **Klucze dostępu**.
4. W wyświetlonym bloku Klucze dostępu widoczny będzie klucz dostępu 1 i klucz dostępu 2. Możesz użyć jednego z nich.
5. Kliknij ikonę kopiowania, aby skopiować klucz do schowka.

## <a name="add-an-azure-cosmos-db-connection"></a>Dodawanie połączenia z usługą Azure Cosmos DB
Aby nawiązać połączenie z usługą Azure Cosmos DB, skopiuj podstawowe parametry połączenia z witryny Azure Portal, a następnie utwórz obiekt **Client** za ich pomocą. Możesz przekazać obiekt **Client** podczas tworzenia obiektu **TableService**:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Tworzenie tabeli
Obiekt **Azure::Storage::Table::TableService** umożliwia pracę z tabelami i jednostkami. Aby utworzyć tabelę, użyj metody **create_table()**. W poniższym przykładzie zostanie utworzona tabela lub wyświetlony błąd, jeśli taki wystąpi.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Aby dodać jednostkę, najpierw utwórz obiekt skrótu, który definiuje właściwości jednostki. Należy pamiętać, że dla każdej jednostki konieczne jest ustawienie właściwości **PartitionKey** i **RowKey**. Są to unikatowe identyfikatory jednostek, będące wartościami, względem których można tworzyć zapytania znaczenie szybciej niż względem innych właściwości. Usługa Azure Storage używa właściwości **PartitionKey** do automatycznego dystrybuowania jednostek tabeli w wielu węzłach magazynu. Obiekty z tą samą wartością właściwości **PartitionKey** są przechowywane w tym samym węźle. Właściwość **RowKey** to unikatowy identyfikator jednostki w ramach partycji, do której należy.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Aktualizowanie jednostki
Istnieje kilka metod aktualizowania istniejącej jednostki:

* **update_entity():** aktualizuje istniejącą jednostkę przez zastąpienie jej.
* **merge_entity():** aktualizuje istniejącą jednostkę przez scalenie nowych wartości właściwości z istniejącą jednostką.
* **insert_or_merge_entity():** aktualizuje istniejącą jednostkę przez zastąpienie jej. Jeśli żadna jednostka nie istnieje, zostanie wstawiona nowa jednostka:
* **insert_or_replace_entity():** aktualizuje istniejącą jednostkę przez scalenie nowych wartości właściwości z istniejącą jednostką. Jeśli żadna jednostka nie istnieje, zostanie wstawiona nowa jednostka.

W poniższym przykładzie przedstawiono aktualizowanie jednostki przy użyciu metody **update_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Jeśli podczas używania metod **update_entity()** i **merge_entity()** aktualizowana jednostka nie istnieje, operacja aktualizacji zakończy się niepowodzeniem. W związku z tym jeśli chcesz przechowywać jednostki niezależnie od tego, czy już istnieją, należy zamiast tego użyć metody **insert_or_replace_entity()** lub **insert_or_merge_entity()**.

## <a name="work-with-groups-of-entities"></a>Praca z grupami jednostek
Czasami warto przesłać jednocześnie wiele operacji w partii, aby zapewnić niepodzielne przetwarzanie przez serwer. Aby to osiągnąć, należy najpierw utworzyć obiekt **Batch**, a następnie użyć metody **execute_batch()** względem obiektu **TableService**. W poniższym przykładzie przedstawiono przesyłanie dwóch jednostek w partii z właściwością RowKey o wartościach 2 i 3. Należy zauważyć, że działa to tylko dla jednostek o tej samej wartości właściwości PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Wykonywanie zapytania względem jednostki
Aby wykonać zapytanie względem jednostki w tabeli, użyj metody **get_entity()**, przekazując nazwę tabeli oraz właściwości **PartitionKey** i **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Wykonywanie zapytania względem zestawu jednostek
Aby wykonać zapytanie względem zestawu jednostek, utwórz obiekt skrótu zapytania, a następnie użyj metody **query_entities()**. W poniższym przykładzie przedstawiono pobieranie wszystkich jednostek o takiej samej wartości właściwości **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Jeśli zestaw wyników jest zbyt duży do zwrócenia w ramach pojedynczego zapytania, zwracany jest token kontynuacji, którego można użyć do pobrania kolejnych stron.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki
Za pomocą zapytania wykonywanego względem tabeli można pobrać tylko kilka właściwości z jednostki. Ta technika, zwana „projekcją”, zmniejsza przepustowość i może poprawić wydajność zapytań, zwłaszcza w przypadku dużych jednostek. Użyj klauzuli SELECT i wprowadź nazwy właściwości, które chcesz przekazać do klienta.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Usuwanie jednostki
Aby usunąć jednostkę, użyj metody **delete_entity()**. Przekaż nazwę tabeli zawierającą jednostkę oraz właściwości PartitionKey i RowKey jednostki.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Usuwanie tabeli
Aby usunąć tabelę, użyj metody **delete_table()** i przekaż nazwę tabeli, którą chcesz usunąć.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Następne kroki

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Centrum deweloperów języka Ruby](https://azure.microsoft.com/develop/ruby/)
* [Biblioteka klienta usługi Microsoft Azure Table Storage dla języka Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table) 

