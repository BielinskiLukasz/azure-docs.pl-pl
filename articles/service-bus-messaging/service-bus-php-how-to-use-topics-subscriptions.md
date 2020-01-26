---
title: Jak używać tematów Azure Service Bus w języku PHP
description: W ramach tego samouczka nauczysz się używać tematów i subskrypcji Azure Service Bus z aplikacji PHP.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 92f25f4bdac4942478c93f717c81eadd2c2f5b4a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760678"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Szybki Start: jak używać tematów Service Bus i subskrypcji w języku PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule pokazano, jak używać tematów i subskrypcji Service Bus. Przykłady są zapisywane w języku PHP i korzystają z [zestawu Azure SDK dla języka PHP](https://github.com/Azure/azure-sdk-for-php). Omówione scenariusze obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie komunikatów do tematu 
- Otrzymywanie komunikatów z subskrypcji
- Usuwanie tematów i subskrypcji

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md) w celu utworzenia **przestrzeni nazw** Service Bus i pobrania **parametrów połączenia**.

    > [!NOTE]
    > W tym przewodniku szybki start utworzysz **temat** i **subskrypcję** tematu przy użyciu języka **php** . 

## <a name="create-a-php-application"></a>Tworzenie aplikacji języka PHP
Jedynym wymaganiem do tworzenia aplikacji w języku PHP, która uzyskuje dostęp do Blob service platformy Azure, jest odwołanie do klas w [zestawie Azure SDK dla języka PHP](https://github.com/Azure/azure-sdk-for-php) z poziomu kodu. Możesz użyć dowolnych narzędzi programistycznych do utworzenia aplikacji lub Notatnika.

> [!NOTE]
> W instalacji PHP musi być zainstalowane i włączone [rozszerzenie OpenSSL](https://php.net/openssl) .
> 
> 

W tym artykule opisano sposób korzystania z funkcji usługi, które mogą być wywoływane lokalnie w aplikacji w języku PHP lub w kodzie uruchomionym w ramach roli sieci Web, roli procesu roboczego lub witryny internetowej platformy Azure.

## <a name="get-the-azure-client-libraries"></a>Pobierz biblioteki klienckie platformy Azure

### <a name="install-via-composer"></a>Instalowanie przez układacz
1. Utwórz plik o nazwie **Composer. JSON** w katalogu głównym projektu i Dodaj do niego następujący kod:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Pobierz **[Composer. PHAR] [Composer-PHAR]** w katalogu głównym projektu.
3. Otwórz wiersz polecenia i wykonaj następujące polecenie w katalogu głównym projektu
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Skonfiguruj aplikację do używania Service Bus
Aby używać interfejsów API Service Bus:

1. Odwołuje się do pliku automatycznej ładowarki przy użyciu instrukcji [require_once][require-once] .
2. Odwołuje się do dowolnych klas, które mogą być używane.

Poniższy przykład pokazuje, jak uwzględnić plik automatycznej ładowarki i odwołać się do klasy **ServiceBusService** .

> [!NOTE]
> W tym przykładzie (i innych przykładach w tym artykule) założono, że zainstalowano biblioteki klienckie PHP dla systemu Azure przez układacz. Jeśli biblioteki zostały zainstalowane ręcznie lub jako pakiet php, należy odwołać się do pliku automatycznej ładowarki **windowsazure. php** .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

W poniższych przykładach instrukcja `require_once` jest zawsze wyświetlana, ale przywoływane są tylko klasy niezbędne do wykonania tego przykładu.

## <a name="set-up-a-service-bus-connection"></a>Skonfiguruj połączenie Service Bus
Aby utworzyć wystąpienie klienta Service Bus, należy najpierw dysponować prawidłowymi parametrami połączenia w tym formacie:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Gdzie `Endpoint` jest zazwyczaj formatem `https://[yourNamespace].servicebus.windows.net`.

Aby utworzyć dowolnego klienta usługi platformy Azure, należy użyć klasy `ServicesBuilder`. Przekonaj się:

* Przekaż parametry połączenia bezpośrednio do niego.
* Użyj **CloudConfigurationManager (CCM)** , aby sprawdzić wiele zewnętrznych źródeł parametrów połączenia:
  * Domyślnie jest on dostarczany z obsługą jednego zewnętrznego źródła — zmienne środowiskowe.
  * Możesz dodać nowe źródła, rozszerzając klasę `ConnectionStringSource`.

W przedstawionych tutaj przykładach parametry połączenia są przekazywane bezpośrednio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Tworzenie tematu
Można wykonywać operacje zarządzania dla Service Bus tematów za pośrednictwem klasy `ServiceBusRestProxy`. Obiekt `ServiceBusRestProxy` jest konstruowany za pośrednictwem metody fabryki `ServicesBuilder::createServiceBusService` z odpowiednimi parametrami połączenia, które hermetyzują uprawnienia tokenu do zarządzania nim.

Poniższy przykład pokazuje, jak utworzyć wystąpienie `ServiceBusRestProxy` i wywołać `ServiceBusRestProxy->createTopic` w celu utworzenia tematu o nazwie `mytopic` w przestrzeni nazw `MySBNamespace`:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Możesz użyć metody `listTopics` na obiektach `ServiceBusRestProxy`, aby sprawdzić, czy temat o określonej nazwie już istnieje w przestrzeni nazw usługi.
> 
> 

## <a name="create-a-subscription"></a>Tworzenie subskrypcji
Subskrypcje tematu są również tworzone za pomocą metody `ServiceBusRestProxy->createSubscription`. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów przesyłany do wirtualnej kolejki subskrypcji.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Jeśli podczas tworzenia nowej subskrypcji nie zostanie określony żaden filtr, zostanie użyty filtr **MatchAll** (domyślnie). Gdy filtr **MatchAll** jest używany, wszystkie komunikaty opublikowane w temacie są umieszczane w kolejce wirtualnej subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie `mysubscription` i używa domyślnego filtru **MatchAll** .

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Można również ustawiać filtry pozwalające określić, które komunikaty wysyłane do tematu powinny znajdować się w subskrypcji określonego tematu. Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest obiekt [sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), który IMPLEMENTUJE podzestaw standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat elementu sqlfilters, zobacz [sqlfilter. Sqlexpression Property][sqlfilter].

> [!NOTE]
> Każda reguła w ramach subskrypcji przetwarza niezależnie wiadomości przychodzące, dodając do niej komunikaty wynikowe. Ponadto każda nowa subskrypcja ma obiekt **reguły** domyślnej z filtrem, który dodaje wszystkie komunikaty z tematu do subskrypcji. Aby odbierać tylko komunikaty zgodne z filtrem, należy usunąć regułę domyślną. Można usunąć regułę domyślną przy użyciu metody `ServiceBusRestProxy->deleteRule`.
> 
> 

Poniższy przykład tworzy subskrypcję o nazwie `HighMessages` przy użyciu elementu **sqlfilter** , który wybiera tylko komunikaty o właściwości niestandardowej `MessageNumber` większej niż 3. Zobacz [wysyłanie komunikatów do tematu,](#send-messages-to-a-topic) Aby uzyskać informacje na temat dodawania właściwości niestandardowych do komunikatów.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Ten kod wymaga użycia dodatkowej przestrzeni nazw: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Podobnie Poniższy przykład tworzy subskrypcję o nazwie `LowMessages` z `SqlFilter`, który wybiera tylko komunikaty, które mają właściwość `MessageNumber` mniejszą lub równą 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Teraz, gdy wiadomość jest wysyłana do tematu `mytopic`, jest zawsze dostarczana do odbiorników subskrybowanych do subskrypcji `mysubscription` i selektywnie dostarczana do odbiorców, którzy zasubskrybują `HighMessages` i `LowMessages` subskrypcje (w zależności od zawartości wiadomości).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać komunikat do Service Bus tematu, aplikacja wywołuje metodę `ServiceBusRestProxy->sendTopicMessage`. Poniższy kod przedstawia sposób wysyłania komunikatu do `mytopic` tematu wcześniej utworzonego w przestrzeni nazw usługi `MySBNamespace`.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Komunikaty wysyłane do tematów usługi Service Bus są wystąpieniami klasy [BrokeredMessage][BrokeredMessage]. Obiekty [BrokeredMessage][BrokeredMessage] mają zestaw standardowych właściwości i metod, a także właściwości, których można użyć do przechowywania niestandardowych właściwości specyficznych dla aplikacji. Poniższy przykład pokazuje, jak wysłać pięć komunikatów testowych do wcześniej utworzonego tematu `mytopic`. Metoda `setProperty` służy do dodawania niestandardowej właściwości (`MessageNumber`) do poszczególnych komunikatów. Wartość właściwości `MessageNumber` różni się w zależności od poszczególnych komunikatów (można użyć tej wartości, aby określić, które subskrypcje otrzymują, jak pokazano w sekcji [Tworzenie subskrypcji](#create-a-subscription) ):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Górny limit rozmiaru tematu wynosi 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Najlepszym sposobem odbierania komunikatów z subskrypcji jest użycie metody `ServiceBusRestProxy->receiveSubscriptionMessage`. Komunikaty mogą być odbierane w dwóch różnych trybach: [ *ReceiveAndDelete* i *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). Ustawienie domyślne to **PeekLock**.

W przypadku używania trybu [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) odbieranie jest operacją pojedynczego zrzutu. Oznacza to, że kiedy usługa Service Bus odbiera żądanie odczytu komunikatu w subskrypcji, oznacza komunikat jako wykorzystywany i zwraca go do aplikacji. Tryb [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * jest najprostszym modelem i najlepiej sprawdza się w scenariuszach, w których aplikacja może tolerować nieprzetwarzanie komunikatu w przypadku wystąpienia błędu. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznaczył komunikat jako używany, wtedy, gdy aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, zostanie pominięty komunikat, który był używany przed awarią.

W domyślnym trybie [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) otrzymywanie komunikatu staje się operacją dwuetapową, co umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niezawodny w przyszłości), kończy drugi etap procesu odbierania, przekazując odebrany komunikat do `ServiceBusRestProxy->deleteMessage`. Gdy Service Bus widzi wywołanie `deleteMessage`, oznacza komunikat jako używany i usunąć go z kolejki.

Poniższy przykład pokazuje, jak odbierać i przetwarzać komunikat przy użyciu trybu [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) (tryb domyślny). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Instrukcje: obsługa awarii aplikacji i nieczytelnych komunikatów
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać metodę `unlockMessage` dla odebranego komunikatu (zamiast metody `deleteMessage`). Powoduje Service Bus odblokowywanie komunikatu w kolejce i udostępnienie go do odebrania przez tę samą aplikację wykorzystującą lub przez inną aplikację, która korzysta z aplikacji.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli aplikacja ulega awarii), a następnie Service Bus odblokować komunikat automatycznie i uczynić go dostępne do ponownego odebrania.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed wystawieniem żądania `deleteMessage`, komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Ten typ przetwarzania jest często wywoływany *co najmniej raz podczas* przetwarzania; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach może zostać ponownie dostarczony ten sam komunikat. Jeśli w scenariuszu nie można tolerować zduplikowanego przetwarzania, deweloperzy aplikacji powinni dodać do aplikacji dodatkową logikę obsługującą duplikowanie komunikatów. Jest on często osiągany przy użyciu metody `getMessageId` komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Aby usunąć temat lub subskrypcję, użyj odpowiednio `ServiceBusRestProxy->deleteTopic` lub `ServiceBusRestProxy->deleteSubscripton` metod. Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem.

Poniższy przykład pokazuje, jak usunąć temat o nazwie `mytopic` i jego zarejestrowanych subskrypcjach.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Za pomocą metody `deleteSubscription` można usunąć subskrypcję niezależnie:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
