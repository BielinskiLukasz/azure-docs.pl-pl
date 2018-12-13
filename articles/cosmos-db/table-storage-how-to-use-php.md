---
title: Jak korzystać z usługi Azure Table Storage lub interfejsu API tabel usługi Azure Cosmos DB przy użyciu języka PHP
description: Przechowywanie danych strukturalnych w chmurze za pomocą usługi Azure Table Storage lub interfejsu API tabel usługi Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 14cb684b0cc3e93992c6ed78c53e8794214c85a8
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879735"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>Jak korzystać z usługi Azure Table Storage lub interfejsu API tabel usługi Azure Cosmos DB przy użyciu języka PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku przedstawiono sposób wykonywania typowych zadań przy użyciu usługi Azure Storage Table oraz interfejsu API tabel usługi Azure Cosmos DB. Przykłady są napisane w języku PHP i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka PHP][download]. Przedstawione scenariusze obejmują **tworzenie i usuwanie tabel**, **wstawianie i usuwanie jednostek w tabeli oraz wykonywanie zapytań dotyczących jednostek**. Aby uzyskać więcej informacji na temat usługi Azure Table Storage, zobacz sekcję [Następne kroki](#next-steps).


## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP

Jedynym wymaganiem podczas tworzenia aplikacji języka PHP w celu uzyskania dostępu do usługi Azure Table Storage lub interfejsu API tabel usługi Azure Cosmos DB jest umieszczenie w kodzie odwołania do klas w zestawie SDK azure-storage-table dla języka PHP. Do utworzenia aplikacji możesz użyć dowolnych narzędzi programistycznych, w tym programu Notatnik.

W tym przewodniku wykorzystane zostaną funkcje usługi Azure Table Storage lub Azure Cosmos DB, które można wywołać lokalnie z poziomu aplikacji języka PHP lub z poziomu kodu uruchomionego w ramach roli internetowej, roli procesu roboczego platformy Azure lub witryny internetowej.

## <a name="get-the-client-library"></a>Pobieranie biblioteki klienta

1. Utwórz plik o nazwie composer.json w katalogu głównym projektu i dodaj do niego następujący kod:
```json
{
  "require": {
    "microsoft/azure-storage-table": "*"
  }
}
```
2. Pobierz plik [composer.phar](http://getcomposer.org/composer.phar) do katalogu głównego. 
3. Otwórz wiersz polecenia i uruchom następujące polecenie w katalogu głównym projektu:
```
php composer.phar install
```
Możesz też przejść do [biblioteki klienta usługi Azure Table Storage dla języka PHP](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) w witrynie GitHub i sklonować kod źródłowy.


## <a name="add-required-references"></a>Dodawanie wymaganych odwołań
Aby korzystać z usługi Azure Table Storage lub interfejsów API usługi Azure Cosmos DB, należy:

* Dodać odwołanie do pliku automatycznej ładowarki przy użyciu instrukcji [require_once] [require_once].
* Dodać odwołanie do wszystkich używanych klas.

Poniższy przykład pokazuje, w jaki sposób dołączyć plik automatycznej ładowarki i dodać odwołanie do klasy **TableRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

Poniższe przykłady zawsze zawierają instrukcję `require_once`, ale dodawane są odwołania wyłącznie do klas niezbędnych do wykonania tego przykładu.

## <a name="add-a-storage-table-service-connection"></a>Dodawanie połączenia z usługą Azure Table Storage
Aby zainicjować klienta usługi Azure Table Storage, wymagane są prawidłowe parametry połączenia. Format parametrów połączenia usługi Table Storage jest następujący:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Dodawanie połączenia z usługą Azure Cosmos DB
Aby zainicjować klienta usługi Azure Cosmos DB Table, wymagane są prawidłowe parametry połączenia. Format parametrów połączenia usługi Azure Cosmos DB jest następujący:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>Dodawanie połączenia z emulatorem usługi Azure Storage
Aby uzyskać dostęp do emulatora usługi Azure Storage:

```php
UseDevelopmentStorage = true
```

Aby utworzyć klienta usługi Azure Table Storage lub Azure Cosmos DB, należy użyć klasy **TableRestProxy**. Możesz:

* Przekazać parametry połączenia bezpośrednio.
* Użyć narzędzia **CloudConfigurationManager (CCM)**, aby sprawdzić wiele źródeł zewnętrznych dla parametrów połączenia:
  * Domyślnie obsługiwane jest jedno źródło zewnętrzne — zmienne środowiskowe.
  * Możesz dodać nowe źródła, rozszerzając klasę `ConnectionStringSource`.

W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Tworzenie tabeli
Obiekt **TableRestProxy** umożliwia utworzenie tabeli przy użyciu metody **createTable**. Tworząc tabelę, możesz ustawić limit czasu dla usługi Table Storage. Aby uzyskać więcej informacji na temat limitu czasu usługi Table Storage, zobacz [Setting Timeouts for Table Service Operations (Ustawianie limitu czasu dla operacji usługi Table Storage)][table-service-timeouts].

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Aby uzyskać szczegółowe informacje na temat ograniczeń dotyczących nazw tabel, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Storage)][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Aby dodać jednostkę do tabeli, utwórz nowy obiekt **Entity** i przekaż go do metody **TableRestProxy->insertEntity**. Pamiętaj, że podczas tworzenia jednostki należy określić wartości `PartitionKey` i `RowKey`. Są to unikatowe identyfikatory jednostki. Zapytania dotyczące tych wartości są wykonywane znacznie szybciej niż zapytania dotyczące innych właściwości jednostki. System używa wartości `PartitionKey`, aby automatycznie rozłożyć jednostki tabeli w wielu węzłach usługi Azure Storage. Jednostki z tą samą wartością `PartitionKey` są przechowywane w tym samym węźle. Operacje na wielu jednostkach przechowywanych w tym samym węźle działają wydajniej niż operacje na jednostkach przechowywanych w wielu węzłach. Wartość `RowKey` to unikatowy identyfikator jednostki w partycji.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Aby uzyskać szczegółowe informacje na temat właściwości i typów tabel, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Storage)][table-data-model].

Klasa **TableRestProxy** oferuje dwie inne metody wstawiania jednostek: **insertOrMergeEntity** i **insertOrReplaceEntity**. Aby użyć tych metod, utwórz nowy obiekt **Entity** i przekaż go jako parametr do jednej z tych metod. Każda z tych metod spowoduje wstawienie jednostki, o ile ta jednostka jeszcze nie istnieje. Jeśli jednostka już istnieje, metoda **insertOrMergeEntity** zaktualizuje wartości istniejących właściwości i doda te właściwości, które jeszcze nie istnieją, a metoda **insertOrReplaceEntity** całkowicie zastąpi istniejącą jednostkę. Następujący przykład pokazuje, w jaki sposób korzystać z metody **insertOrMergeEntity**. Jeśli jednostka z wartościami `PartitionKey`„tasksSeattle” `RowKey` i „1” jeszcze nie istnieje, zostanie wstawiona. Jeśli jednak została już wstawiona (jak pokazano na powyższym przykładzie), właściwość `DueDate` zostanie zaktualizowana, a właściwość `Status` zostanie dodana. Właściwości `Description` i `Location` również zostaną zaktualizowane, ale nowe wartości będą takie same. Gdyby te dwie ostatnie wartości nie zostały dodane tak, jak pokazano na przykładzie, a istniały w jednostce docelowej, ich istniejące wartości pozostałyby niezmienione.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki
Metoda **TableRestProxy->getEntity** umożliwia pobranie jednej jednostki przez wykonanie zapytania dotyczącego jej wartości `PartitionKey` i `RowKey`. W poniższym przykładzie klucz partycji `tasksSeattle` i klucz wiersza `1` są przekazywane do metody **getEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji
Zapytania dotyczące jednostek są konstruowane przy użyciu filtrów — aby uzyskać więcej informacji, zobacz [Querying Tables and Entities (Wykonywanie zapytań o tabele i jednostki)][filters]. Aby pobrać wszystkie jednostki w partycji, użyj filtru „PartitionKey eq *nazwa_partycji*”. Następujący przykład pokazuje, w jaki sposób pobrać wszystkie jednostki w partycji `tasksSeattle`, przekazując filtr do metody **queryEntities**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Pobieranie podzestawu jednostek w partycji
Schemat użyty w poprzednim przykładzie umożliwia również pobranie dowolnego podzestawu jednostek z partycji. Podzestaw jednostek do pobrania jest określany przy użyciu odpowiedniego filtru — aby uzyskać więcej informacji, zobacz [Querying Tables and Entities (Wykonywanie zapytań o tabele i jednostki)][filters]. Poniższy przykład przedstawia sposób użycia filtru w celu pobrania wszystkich jednostek z określoną wartością `Location` i datą `DueDate` wcześniejszą niż określona data.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Pobieranie podzestawu właściwości jednostki
Zapytanie umożliwia także pobranie podzestawu właściwości jednostki. Ta technika, nazywana *projekcją*, zmniejsza użycie przepustowości i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek. Aby określić właściwość do pobrania, należy przekazać nazwę właściwości do metody **Query->addSelectField**. Tę metodę można wywołać wiele razy, aby dodać więcej właściwości. Po wykonaniu polecenia **TableRestProxy->queryEntities** zwrócone zostaną tylko wybrane właściwości jednostek. Jeśli chcesz zwrócić podzestaw jednostek tabeli, użyj filtru tak, jak pokazano w powyższych zapytaniach.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Aktualizowanie jednostki
Możesz zaktualizować istniejącą jednostkę, używając metod **Entity->setProperty** i **Entity->addProperty** na tej jednostce, a następnie wywołując metodę **TableRestProxy->updateEntity**. W poniższym przykładzie po pobraniu jednostki jedna właściwość zostanie zmodyfikowana, inna usunięta, a do tego zostanie dodana nowa właściwość. Pamiętaj, że możesz usunąć właściwość, ustawiając jej wartość na **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Usuwanie jednostki
Aby usunąć jednostkę, przekaż nazwę tabeli oraz wartości `PartitionKey` i `RowKey` jednostki do metody **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Na potrzeby kontroli współbieżności możesz ustawić element Etag dla jednostki, która ma zostać usunięta, używając metody **DeleteEntityOptions->setEtag** i przekazując obiekt **DeleteEntityOptions** do metody **deleteEntity** jako czwarty parametr.

## <a name="batch-table-operations"></a>Zbiorcze operacje na tabeli
Metoda **TableRestProxy->batch** umożliwia wykonanie wielu operacji w ramach jednego żądania. Procedura polega na dodaniu operacji do obiektu **BatchRequest**, a następnie przekazaniu obiektu **BatchRequest** do metody **TableRestProxy->batch**. Aby dodać operację do obiektu **BatchRequest**, możesz wywołać dowolną z następujących metod (można to zrobić wiele razy):

* **addInsertEntity** (dodaje operację insertEntity)
* **addUpdateEntity** (dodaje operację updateEntity)
* **addMergeEntity** (dodaje operację mergeEntity)
* **addInsertOrReplaceEntity** (dodaje operację insertOrReplaceEntity)
* **addInsertOrMergeEntity** (dodaje operację insertOrMergeEntity)
* **addDeleteEntity** (dodaje operację deleteEntity)

Poniższy przykład przedstawia sposób wykonania operacji **insertEntity** i **deleteEntity** w ramach jednego żądania. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Aby uzyskać więcej informacji na temat tworzenia partii operacji, zobacz [Performing Entity Group Transactions (Wykonywanie transakcji w grupach jednostek)][entity-group-transactions].

## <a name="delete-a-table"></a>Usuwanie tabeli
Można usunąć tabelę, przekazując nazwę tabeli do metody **TableRestProxy->deleteTable**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy usług Azure Table Storage i Azure Cosmos DB, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.

* [PHP Developer Center (Centrum deweloperów języka PHP)](https://azure.microsoft.com/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: http://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
