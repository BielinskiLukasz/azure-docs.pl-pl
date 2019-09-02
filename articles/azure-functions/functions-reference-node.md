---
title: Dokumentacja dla deweloperów języka JavaScript dla Azure Functions | Microsoft Docs
description: Dowiedz się, jak opracowywać funkcje przy użyciu języka JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 62115dd519336c728b679e4e698182a50660a464
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "68949885"
---
# <a name="azure-functions-javascript-developer-guide"></a>Przewodnik dla deweloperów Azure Functions JavaScript

Ten przewodnik zawiera informacje na temat złożonego pisania Azure Functions przy użyciu języka JavaScript.

Funkcja języka JavaScript jest eksportowana `function` , która jest wykonywana po wywołaniu ([wyzwalacze są konfigurowane w funkcji Function. JSON](functions-triggers-bindings.md)). Pierwszym argumentem przekazywanym do każdej funkcji jest `context` obiekt, który jest używany do otrzymywania i wysyłania powiązań danych, rejestrowania i komunikacji z środowiskiem uruchomieniowym.

W tym artykule przyjęto założenie, że już odczytano [informacje dotyczące deweloperów Azure Functions](functions-reference.md). Ukończ Przewodnik Szybki Start, aby utworzyć pierwszą funkcję przy użyciu [Visual Studio Code](functions-create-first-function-vs-code.md) lub [w portalu](functions-create-first-azure-function.md).

Ten artykuł obsługuje także [Programowanie aplikacji języka TypeScript](#typescript).

## <a name="folder-structure"></a>Struktura folderów

Wymagana struktura folderów dla projektu JavaScript wygląda następująco. Tę wartość domyślną można zmienić. Aby uzyskać więcej informacji, zobacz sekcję [scriptFile](#using-scriptfile) poniżej.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

W katalogu głównym projektu znajduje się plik udostępnionego pliku [host. JSON](functions-host-json.md) , którego można użyć do skonfigurowania aplikacji funkcji. Każda funkcja ma folder z własnym plikiem kodu (. js) i plikiem konfiguracji powiązania (Function. JSON). Nazwa `function.json`katalogu nadrzędnego jest zawsze nazwą funkcji.

Rozszerzenia powiązań wymagane w [wersji 2. x](functions-versions.md) środowiska uruchomieniowego funkcji są zdefiniowane w `extensions.csproj` pliku z rzeczywistymi plikami `bin` biblioteki w folderze. Podczas programowania lokalnego należy [zarejestrować rozszerzenia powiązań](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w Azure Portal Rejestracja jest wykonywana.

## <a name="exporting-a-function"></a>Eksportowanie funkcji

Funkcje JavaScript muszą zostać wyeksportowane za [`exports`](https://nodejs.org/api/modules.html#modules_exports)pośrednictwem [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (lub). Wyeksportowana funkcja powinna być funkcją języka JavaScript, która jest wykonywana po wyzwoleniu.

Domyślnie środowisko uruchomieniowe funkcji wyszukuje funkcję w programie `index.js`, gdzie `index.js` jest w niej udostępniony ten sam katalog `function.json`nadrzędny. W przypadku domyślnego przypadku eksportowany funkcja powinna być jedynym eksportem z jego pliku lub eksportu o nazwie `run` lub. `index` Aby skonfigurować lokalizację pliku i nazwę eksportu funkcji, Przeczytaj o konfigurowaniu poniższego [punktu wejścia funkcji](functions-reference-node.md#configure-function-entry-point) .

Wyeksportowana funkcja przekazała wiele argumentów w wykonaniu. Pierwszym argumentem, który przyjmuje, jest `context` zawsze obiekt. Jeśli funkcja jest synchroniczna (nie zwraca obietnicy), należy przekazać `context` obiekt, ponieważ wywoływanie `context.done` jest wymagane do poprawnego użycia.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Eksportowanie funkcji asynchronicznej
W przypadku korzystania [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) z deklaracji lub zwykłego języka JavaScript [niesie obietnice zwiększenia](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) w wersji 2. x środowiska uruchomieniowego Functions nie trzeba jawnie wywoływać [`context.done`](#contextdone-method) wywołania zwrotnego, aby sygnalizować, że funkcja została ukończona. Funkcja kończy pracę po zakończeniu wyeksportowanej funkcji/obietnicy asynchronicznej. W przypadku funkcji przeznaczonych dla środowiska uruchomieniowego w wersji 1. x [`context.done`](#contextdone-method) należy nadal wywołać, gdy kod jest wykonywany.

Poniższy przykład to prosta funkcja, która rejestruje, że została wyzwolona, i natychmiast kończy wykonywanie.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Podczas eksportowania funkcji asynchronicznej można również skonfigurować powiązanie danych wyjściowych, aby przyjąć `return` wartość. Jest to zalecane, jeśli masz tylko jedno powiązanie danych wyjściowych.

Aby przypisać dane wyjściowe przy `return`użyciu polecenia, `name` Zmień właściwość `$return` na `function.json`wartość w.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

W takim przypadku funkcja powinna wyglądać podobnie do poniższego przykładu:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Powiązania 
W języku JavaScript [powiązania](functions-triggers-bindings.md) są konfigurowane i definiowane w funkcji Function. JSON. Funkcje współdziałają z powiązaniami na wiele sposobów.

### <a name="inputs"></a>Dane wejściowe
Dane wejściowe są podzielone na dwie kategorie w Azure Functions: jeden to dane wejściowe wyzwalacza, a drugi to dodatkowe dane wejściowe. Wyzwalacz i inne powiązania wejściowe (powiązania `direction === "in"`) mogą być odczytywane przez funkcję na trzy sposoby:
 - **_[Zalecane]_ Jako parametry przesłane do funkcji.** Są one przenoszone do funkcji w takiej samej kolejności, w jakiej są zdefiniowane w *funkcji Function. JSON*. Właściwość zdefiniowana w *funkcji Function. JSON* nie musi być zgodna z nazwą parametru, chociaż powinna być. `name`
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Jako elementy członkowskie [`context.bindings`](#contextbindings-property) obiektu.** Każdy element członkowski nosi nazwę `name` właściwości zdefiniowanej w *funkcji Function. JSON*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Jako dane wejściowe przy użyciu [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) obiektu JavaScript.** Jest to zasadniczo takie samo jak przekazywanie danych wejściowych jako parametrów, ale umożliwia dynamiczne obsługiwanie danych wejściowych.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>outputs
Dane wyjściowe (powiązania `direction === "out"`) mogą być zapisywane przez funkcję na wiele sposobów. We wszystkich przypadkach `name` właściwość powiązania, zgodnie z definicją w *Function. JSON* , odnosi się do nazwy składowej obiektu, która jest zapisywana w funkcji. 

Dane można przypisywać do powiązań wyjściowych w jeden z następujących sposobów (nie łącz tych metod):

- **_[Zalecane dla wielu wyjść]_ Zwracanie obiektu.** Jeśli używana jest funkcja zwracająca Asynchroniczność/Promise, można zwrócić obiekt z przypisanymi danymi wyjściowymi. W poniższym przykładzie powiązania wyjściowe mają nazwę "httpResponse" i "queueOutput" w *funkcji Function. JSON*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Jeśli używasz funkcji synchronicznej, możesz zwrócić ten obiekt przy użyciu [`context.done`](#contextdone-method) (Zobacz przykład).
- **_[Zalecane dla pojedynczego wyjścia]_ Zwracanie wartości bezpośrednio i przy użyciu nazwy powiązania $return.** Działa to tylko w przypadku funkcji zwracających dane asynchroniczne/Promise. Zobacz przykład [eksportowania funkcji asynchronicznej](#exporting-an-async-function). 
- **Przypisywanie wartości do `context.bindings`**  można przypisywać wartości bezpośrednio do kontekstu. powiązania.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Typ danych powiązań

Aby zdefiniować typ danych dla powiązania wejściowego, należy użyć `dataType` właściwości w definicji powiązania. Aby na przykład odczytać zawartość żądania HTTP w formacie binarnym, użyj typu `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Opcje dla `dataType` `binary`:, `stream`, i `string`.

## <a name="context-object"></a>obiekt kontekstu
Środowisko uruchomieniowe używa `context` obiektu do przekazywania danych do i z funkcji oraz do komunikowania się ze środowiskiem uruchomieniowym. Obiektu kontekstu można używać do odczytywania i ustawiania danych z powiązań, pisania dzienników i używania `context.done` wywołania zwrotnego, gdy eksportowana funkcja jest synchroniczna.

`context` Obiekt jest zawsze pierwszym parametrem funkcji. Powinna być uwzględniona, ponieważ ma ważne metody, takie `context.done` jak `context.log`i. Obiekt można nazwać w dowolny sposób (na przykład `ctx` lub `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Context. bindings — Właściwość

```js
context.bindings
```

Zwraca nazwany obiekt, który jest używany do odczytywania lub przypisywania danych powiązań. Dostęp do danych wejściowych i wyzwalaczy można uzyskać, odczytując właściwości w `context.bindings`. Dane wyjściowe powiązania mogą być przypisywane przez dodanie danych do`context.bindings`

Na przykład następujące definicje powiązań w funkcji. JSON pozwalają uzyskać dostęp do zawartości kolejki z `context.bindings.myInput` i przypisać dane wyjściowe do kolejki przy użyciu. `context.bindings.myOutput`

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Możesz zdefiniować dane wyjściowe powiązania przy użyciu `context.done` metody zamiast `context.binding` obiektu (patrz poniżej).

### <a name="contextbindingdata-property"></a>Context. bindingData, właściwość

```js
context.bindingData
```

Zwraca nazwany obiekt, który zawiera metadane wyzwalacza i dane wywołania funkcji (`invocationId`, `sys.methodName`, `sys.utcNow` `sys.randGuid`,). Przykład metadanych wyzwalacza zawiera przykład tego centrum [zdarzeń](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Context. done — Metoda

```js
context.done([err],[propertyBag])
```

Umożliwia, że środowisko uruchomieniowe wie, że Twój kod został ukończony. Gdy funkcja używa [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaracji, nie musisz używać `context.done()`. `context.done` Wywołanie zwrotne jest wywoływane niejawnie. Funkcje asynchroniczne są dostępne w węźle 8 lub w nowszej wersji, co wymaga wersji 2. x środowiska uruchomieniowego funkcji.

Jeśli funkcja nie jest funkcją asynchroniczną, **należy wywołać** `context.done` , aby poinformować środowisko uruchomieniowe, że funkcja została ukończona. Trwa limit czasu wykonywania, jeśli go nie ma.

`context.done` Metoda umożliwia przekazanie z powrotem błędu zdefiniowanego przez użytkownika do środowiska uruchomieniowego i obiektu JSON zawierającego dane powiązania danych wyjściowych. Właściwości przesłane w `context.done` celu zastąpienia dowolnego zestawu `context.bindings` dla obiektu.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context. log — Metoda  

```js
context.log(message)
```

Umożliwia zapis do dzienników funkcji przesyłania strumieniowego na domyślnym poziomie śledzenia. W `context.log`systemie dostępne są dodatkowe metody rejestrowania, które umożliwiają pisanie dzienników funkcji na innych poziomach śledzenia:


| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Zapisuje dane w dzienniku na poziomie błędu lub niższym.   |
| **warn(_message_)**    | Zapisuje dane w dzienniku na poziomie ostrzeżeń lub niższym. |
| **info(_message_)**    | Zapisuje dane w dzienniku lub niższym poziomie informacji.    |
| **verbose(_message_)** | Zapisuje dane w celu pełnego rejestrowania na poziomie.           |

Poniższy przykład zapisuje dziennik na poziomie śledzenia ostrzeżeń:

```javascript
context.log.warn("Something has happened."); 
```

Można [skonfigurować próg poziomu śledzenia na potrzeby rejestrowania](#configure-the-trace-level-for-console-logging) w pliku host. JSON. Aby uzyskać więcej informacji na temat pisania dzienników, zobacz [Zapisywanie wyników śledzenia](#writing-trace-output-to-the-console) poniżej.

Przeczytaj [Azure Functions monitorowania](functions-monitoring.md) , aby dowiedzieć się więcej o przeglądaniu i wysyłaniu zapytań o dzienniki funkcji.

## <a name="writing-trace-output-to-the-console"></a>Zapisywanie danych wyjściowych śledzenia w konsoli 

W obszarze Funkcje `context.log` używasz metod do zapisywania danych wyjściowych śledzenia w konsoli programu. W funkcjach w wersji 2. x wyniki `console.log` śledzenia przy użyciu są przechwytywane na poziomie aplikacja funkcji. Oznacza to, że dane `console.log` wyjściowe z nie są powiązane z konkretnym wywołaniem funkcji i nie są wyświetlane w dziennikach określonej funkcji. Są one jednak propagowane do Application Insights. W funkcjach v1. x nie można użyć `console.log` do zapisu w konsoli programu.

Po wywołaniu `context.log()`, wiadomość jest zapisywana w konsoli na domyślnym poziomie śledzenia, który jest poziomem śledzenia _informacji_ . Poniższy kod zapisuje w konsoli na poziomie śledzenia informacji:

```javascript
context.log({hello: 'world'});  
```

Ten kod jest odpowiednikiem powyższego kodu:

```javascript
context.log.info({hello: 'world'});  
```

Ten kod jest zapisywany w konsoli na poziomie błędu:

```javascript
context.log.error("An error has occurred.");  
```

Ponieważ _błąd_ to najwyższy poziom śledzenia, ten ślad jest zapisywana w danych wyjściowych na wszystkich poziomach śledzenia, o ile rejestrowanie jest włączone.

Wszystkie `context.log` metody obsługują ten sam format parametru, który jest obsługiwany przez metodę Node. js [util. format](https://nodejs.org/api/util.html#util_util_format_format). Rozważmy następujący kod, który zapisuje dzienniki funkcji przy użyciu domyślnego poziomu śledzenia:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Możesz również napisać ten sam kod w następującym formacie:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurowanie poziomu śledzenia dla rejestrowania konsoli

Funkcja 1. x umożliwia zdefiniowanie poziomu śledzenia progu do zapisu w konsoli, co ułatwia kontrolowanie sposobu zapisywania śladów w konsoli z funkcji. Aby ustawić wartość progową dla wszystkich śladów, które są zapisywane w `tracing.consoleLevel` konsoli programu, należy użyć właściwości w pliku host. JSON. To ustawienie ma zastosowanie do wszystkich funkcji w aplikacji funkcji. W poniższym przykładzie ustawiono próg śledzenia w celu włączenia pełnego rejestrowania:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Wartości **consoleLevel** odpowiadają nazwom `context.log` metod. Aby wyłączyć wszystkie rejestrowanie śledzenia w konsoli programu, należy ustawić **consoleLevel** na _wyłączone_. Aby uzyskać więcej informacji, zobacz [Dokumentacja pliku host. JSON](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Wyzwalacze i powiązania HTTP

Wyzwalacze HTTP i webhook oraz powiązania wyjściowe HTTP używają obiektów Request i Response do reprezentowania komunikatów HTTP.  

### <a name="request-object"></a>Obiekt żądania

Obiekt `context.req` (żądanie) ma następujące właściwości:

| Właściwość      | Opis                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Obiekt, który zawiera treść żądania.               |
| _nagłówka_     | Obiekt, który zawiera nagłówki żądania.                   |
| _method_      | Metoda HTTP żądania.                                |
| _originalUrl_ | Adres URL żądania.                                        |
| _Krocz_      | Obiekt, który zawiera parametry routingu żądania. |
| _query_       | Obiekt, który zawiera parametry zapytania.                  |
| _rawBody_     | Treść komunikatu jako ciąg.                           |


### <a name="response-object"></a>Obiekt odpowiedzi

Obiekt `context.res` (odpowiedź) ma następujące właściwości:

| Właściwość  | Opis                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Obiekt, który zawiera treść odpowiedzi.         |
| _nagłówka_ | Obiekt, który zawiera nagłówki odpowiedzi.             |
| _isRaw_   | Wskazuje, że formatowanie dla odpowiedzi jest pomijane.    |
| _status_  | Kod stanu HTTP odpowiedzi.                     |

### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi 

Podczas pracy z wyzwalaczami HTTP można uzyskiwać dostęp do obiektów żądania HTTP i odpowiedzi na wiele sposobów:

+ **Z `req` i `res` właściwości`context` obiektu.** W ten sposób można użyć wzorca konwencjonalnego do uzyskiwania dostępu do danych http z obiektu kontekstu, zamiast używać pełnego `context.bindings.name` wzorca. Poniższy przykład pokazuje, `req` jak uzyskać dostęp do obiektów i `res` w `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Z powiązań nazwanych wejściowych i wyjściowych.** W ten sposób wyzwalacz HTTP i powiązania działają tak samo jak inne powiązania. Poniższy przykład ustawia obiekt odpowiedzi przy użyciu nazwanego `response` powiązania: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Tylko odpowiedź]_ Przez wywołanie `context.res.send(body?: any)`metody.** Odpowiedź HTTP jest tworzona z danymi wejściowymi `body` jako treść odpowiedzi. `context.done()`jest wywoływana niejawnie.

+ **_[Tylko odpowiedź]_ Przez wywołanie `context.done()`metody.** Specjalny typ powiązania HTTP zwraca odpowiedź, która jest przesyłana do `context.done()` metody. Następujące powiązanie wyjściowe http definiuje `$return` parametr wyjściowy:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Wersja węzła

W poniższej tabeli przedstawiono wersję środowiska Node. js używaną przez każdą wersję główną funkcji Runtime:

| Wersja funkcji | Wersja środowiska Node. js | 
|---|---|
| 1.x | 6.11.2 (zablokowany przez środowisko uruchomieniowe) |
| 2.x  | _Active LTS_ i _Maintenance LTS_ wersje Node. js (zalecane 8.11.1 i 10.14.1). Ustaw wersję przy użyciu [Ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)WEBSITE_NODE_DEFAULT_VERSION.|

Bieżącą wersję, która jest używana przez środowisko uruchomieniowe, można zobaczyć, sprawdzając powyższe ustawienie aplikacji lub przez `process.version` wydrukowanie z dowolnej funkcji.

## <a name="dependency-management"></a>Zarządzanie zależnościami
Aby można było używać bibliotek społeczności w kodzie JavaScript, jak pokazano w poniższym przykładzie, należy się upewnić, że wszystkie zależności są zainstalowane na aplikacja funkcji na platformie Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Należy zdefiniować `package.json` plik w katalogu głównym aplikacja funkcji. Zdefiniowanie pliku pozwala wszystkim funkcjom w aplikacji współdzielić te same pakiety w pamięci podręcznej, co zapewnia najlepszą wydajność. Jeśli występuje konflikt wersji, można go rozwiązać, dodając `package.json` plik w folderze określonej funkcji.  

Podczas wdrażania aplikacji funkcji z kontroli źródła, każdy `package.json` plik obecny w repozytorium spowoduje `npm install` wyzwolenie w folderze podczas wdrażania. Ale podczas wdrażania za pośrednictwem portalu lub interfejsu wiersza polecenia trzeba będzie ręcznie zainstalować pakiety.

Istnieją dwa sposoby instalowania pakietów na aplikacja funkcji: 

### <a name="deploying-with-dependencies"></a>Wdrażanie z zależnościami
1. Zainstaluj wszystkie wymagane pakiety lokalnie, uruchamiając `npm install`program.

2. Wdróż swój kod i upewnij się, że `node_modules` folder jest uwzględniony we wdrożeniu. 


### <a name="using-kudu"></a>Korzystanie z kudu
1. Przejdź do pozycji `https://<function_app_name>.scm.azurewebsites.net` (Plik > Nowy > Inny).

2. Kliknij pozycję **Debuguj konsolę** > **cmd**.

3. Przejdź do `D:\home\site\wwwroot`, a następnie przeciągnij plik Package. JSON do folderu **wwwroot** w górnej połowie strony.  
    Możesz również przekazywać pliki do aplikacji funkcji w inny sposób. Aby uzyskać więcej informacji, zobacz [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). 

4. Po przekazaniu pliku Package. JSON Uruchom `npm install` polecenie w **konsoli wykonywania zdalnego kudu**.  
    Ta akcja spowoduje pobranie pakietów wskazanych w pliku Package. JSON i ponowne uruchomienie aplikacji funkcji.

## <a name="environment-variables"></a>Zmienne środowiskowe

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień `process.env`przy użyciu, jak pokazano poniżej w drugim i trzecim `context.log()` wywołaniach, gdzie rejestrujemy `WEBSITE_SITE_NAME` `AzureWebJobsStorage` zmienne środowiskowe i:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

W przypadku uruchamiania lokalnego ustawienia aplikacji są odczytywane z pliku projektu [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="configure-function-entry-point"></a>Konfiguruj punkt wejścia funkcji

`function.json` Właściwości imożna`entryPoint` użyć do skonfigurowania lokalizacji i nazwy wyeksportowanej funkcji. `scriptFile` Te właściwości mogą być ważne w przypadku transsterty języka JavaScript.

### <a name="using-scriptfile"></a>Użyciu`scriptFile`

Domyślnie funkcja języka JavaScript jest wykonywana z `index.js`, plik, który współużytkuje ten sam katalog nadrzędny co odpowiadający mu. `function.json`

`scriptFile`można go użyć, aby uzyskać strukturę folderów, która wygląda podobnie do poniższego przykładu:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Element `function.json` for`scriptFile` powinien zawierać właściwość wskazującą plik z wyeksportowaną funkcją do uruchomienia. `myNodeFunction`

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Użyciu`entryPoint`

W `scriptFile` (lub `index.js`) funkcja musi zostać wyeksportowana przy `module.exports` użyciu programu w celu znalezienia i uruchomienia. Domyślnie funkcja, która jest wykonywana, gdy wyzwalane jest jedynym eksportem z tego pliku, eksportem `run`o nazwie lub eksportem `index`o nazwie.

Tę konfigurację można skonfigurować przy `entryPoint` użyciu `function.json`programu w programie, jak w poniższym przykładzie:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

W funkcjach w wersji 2. x, `this` która obsługuje parametr w funkcjach użytkownika, kod funkcji może być następnie tak jak w poniższym przykładzie:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

W tym przykładzie należy zauważyć, że mimo że obiekt jest eksportowany, nie ma gwarancji, aby zachować stan między wykonaniami.

## <a name="local-debugging"></a>Debugowanie lokalne

Po uruchomieniu z `--inspect` parametrem proces Node. js nasłuchuje klienta debugowania na określonym porcie. W Azure Functions 2. x można określić argumenty do przekazania do procesu Node. js, który uruchamia kod przez dodanie zmiennej środowiskowej lub ustawienia `languageWorkers:node:arguments = <args>`aplikacji. 

Aby debugować lokalnie, Dodaj `"languageWorkers:node:arguments": "--inspect=5858"` `Values` element w pliku [Local. Settings. JSON](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) i Dołącz debuger do portu 5858.

Podczas debugowania przy użyciu vs Code `--inspect` parametr jest dodawany automatycznie `port` przy użyciu wartości w pliku Launch. JSON projektu.

W wersji 1. x ustawienie `languageWorkers:node:arguments` nie będzie działało. Port debugowania można wybrać z [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parametrem na Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Gdy element docelowy jest w wersji 2. x środowiska uruchomieniowego Functions, obie [Azure Functions dla Visual Studio Code](functions-create-first-function-vs-code.md) i [Azure Functions Core Tools](functions-run-local.md) umożliwiają tworzenie aplikacji funkcji przy użyciu szablonu, który obsługuje projekty aplikacji funkcji TypeScript. Szablon generuje i `package.json` `tsconfig.json` tworzy pliki projektu, które ułatwiają transkodowanie, uruchamianie i publikowanie funkcji języka JavaScript w kodzie TypeScript za pomocą tych narzędzi.

Wygenerowany `.funcignore` plik służy do wskazywania, które pliki są wykluczone, gdy projekt jest publikowany na platformie Azure.  

Pliki TypeScript (. TS) są transsterty do plików JavaScript (js) w `dist` katalogu wyjściowym. Szablony języka TypeScript używają [ `scriptFile` parametru](#using-scriptfile) w `function.json` , aby wskazać lokalizację odpowiedniego pliku js w `dist` folderze. Lokalizacja wyjściowa jest ustawiana przez szablon przy użyciu `outDir` parametru `tsconfig.json` w pliku. Jeśli zmienisz to ustawienie lub nazwę folderu, środowisko uruchomieniowe nie będzie w stanie znaleźć kodu do uruchomienia.

> [!NOTE]
> Eksperymentalna obsługa języka TypeScript istnieje w wersji 1. x środowiska uruchomieniowego funkcji. Wersja eksperymentalna transstertuje pliki TypeScript do plików JavaScript po wywołaniu funkcji. W wersji 2. x ta eksperymentalna pomoc techniczna została zastąpiona przez metodę opartą na narzędziu, która transpilation przed zainicjowaniem hosta i podczas procesu wdrażania.

Sposób, w jaki użytkownik jest opracowywany i wdrażany z projektu TypeScript, zależy od narzędzia deweloperskiego.

### <a name="visual-studio-code"></a>Visual Studio Code

[Azure Functions rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) umożliwia tworzenie funkcji przy użyciu języka TypeScript. Podstawowe narzędzia są wymaganiem rozszerzenia Azure Functions.

Aby utworzyć aplikację funkcji języka TypeScript w Visual Studio Code, wybierz `TypeScript` jako język podczas tworzenia aplikacji funkcji.

Po naciśnięciu klawisza **F5** w celu uruchomienia aplikacji lokalnie transpilation jest wykonywana przed zainicjowaniem hosta (Func. exe). 

Po wdrożeniu aplikacji funkcji na platformie Azure przy użyciu przycisku **Wdróż do funkcji App...** rozszerzenie Azure Functions najpierw generuje kompilację do użycia w środowisku produkcyjnym na podstawie plików źródłowych języka TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Istnieje kilka sposobów, w których projekt TypeScript różni się od projektu JavaScript podczas korzystania z podstawowych narzędzi.

#### <a name="create-project"></a>Tworzenie projektu

Aby utworzyć projekt aplikacji funkcji TypeScript przy użyciu podstawowych narzędzi, należy określić opcję języka TypeScript podczas tworzenia aplikacji funkcji. Można to zrobić na jeden z następujących sposobów:

- Uruchom polecenie, wybierz `node` jako stos języka, a następnie wybierz opcję `typescript`. `func init`

- Uruchom polecenie `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Uruchom lokalnie

Aby uruchomić kod aplikacji funkcji lokalnie za pomocą podstawowych narzędzi, użyj następujących poleceń zamiast `func host start`: 

```command
npm install
npm start
```

`npm start` Polecenie jest równoważne z następującymi poleceniami:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Przed użyciem [`func azure functionapp publish`] polecenia do wdrożenia na platformie Azure utworzysz gotowe do produkcji kompilację plików JavaScript z plików źródłowych TypeScript. 

Następujące polecenia przygotują i opublikują projekt TypeScript przy użyciu podstawowych narzędzi: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

W tym poleceniu Zastąp `<APP_NAME>` wartość nazwą aplikacji funkcji.

## <a name="considerations-for-javascript-functions"></a>Zagadnienia dotyczące funkcji języka JavaScript

Podczas pracy z funkcjami JavaScript należy pamiętać o zagadnieniach w poniższych sekcjach.

### <a name="choose-single-vcpu-app-service-plans"></a>Wybierz plany App Service pojedynczego vCPU

Po utworzeniu aplikacji funkcji, która korzysta z planu App Service, zalecamy wybranie planu pojedynczego vCPU zamiast planu z wieloma procesorów wirtualnych vCPU. Obecnie funkcje języka JavaScript działają wydajniej na maszynach wirtualnych z systemem Single-vCPU, a użycie większych maszyn wirtualnych nie daje oczekiwanych ulepszeń wydajności. W razie potrzeby można ręcznie skalować w poziomie, dodając więcej wystąpień maszyn wirtualnych o pojedynczej vCPU, lub można włączyć automatyczne skalowanie. Aby uzyskać więcej informacji, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Zimne uruchomienie

Podczas opracowywania Azure Functions w modelu hostingu bezserwerowego zimny start jest rzeczywistości. *Zimny start* odnosi się do faktu, że gdy aplikacja funkcji jest uruchamiana po raz pierwszy od czasu nieaktywności, trwa dłużej. W przypadku funkcji języka JavaScript z dużymi drzewami zależności w szczególności zimny start może być znaczący. Aby przyspieszyć zimny proces uruchamiania, [Uruchom funkcje jako plik pakietu](run-functions-from-deployment-package.md) , gdy jest to możliwe. Wiele metod wdrażania domyślnie korzysta z modelu "Uruchom z modelem", ale w przypadku wystąpienia dużej zimnego startu i nie działają w ten sposób, jednak ta zmiana może zapewnić znaczną poprawę.

### <a name="connection-limits"></a>Limity połączeń

W przypadku korzystania z klienta specyficznego dla usługi w aplikacji Azure Functions nie należy tworzyć nowego klienta przy każdym wywołaniu funkcji. Zamiast tego należy utworzyć pojedynczego, statycznego klienta w zakresie globalnym. Aby uzyskać więcej informacji, zobacz [Zarządzanie połączeniami w Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Użyj `async` i`await`

Podczas pisania Azure Functions w języku JavaScript, należy napisać kod przy użyciu `async` słów `await` kluczowych i. Pisanie kodu przy `async` użyciu `await` i zamiast wywołań zwrotnych `.then` lub `.catch` i z niesie obietnice zwiększeniaą pomaga uniknąć dwóch typowych problemów:
 - Zgłaszanie nieprzechwyconych wyjątków, które [uległy awarii procesu Node. js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), mogą mieć wpływ na wykonywanie innych funkcji.
 - Nieoczekiwane zachowanie, takie jak brakujące dzienniki z kontekstu. log, spowodowane przez wywołania asynchroniczne, które nie zostały prawidłowo oczekiwane.

W poniższym przykładzie metoda `fs.readFile` asynchroniczna jest wywoływana z funkcją wywołania zwrotnego pierwszego błędu jako drugi parametr. Ten kod powoduje wystąpienie obu problemów. Wyjątek, który nie został jawnie przechwycony w prawidłowym zakresie, uległ awarii cały proces (problem #1). Wywołanie `context.done()` poza zakresem funkcji wywołania zwrotnego oznacza, że wywołanie funkcji może kończyć się przed odczytaniem pliku (problem #2). W tym przykładzie wywoływanie `context.done()` zbyt wczesnych wyników w przypadku brakujących wpisów `Data from file:`dziennika rozpoczyna się od.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Używanie słów kluczowych `await`ipomaga uniknąć obydwu tych błędów. `async` Należy użyć funkcji [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) narzędziowej środowiska Node. js, aby przekształcić funkcje pierwszego stylu wywołania zwrotnego na funkcje oczekujące.

W poniższym przykładzie wszystkie Nieobsłużone wyjątki zgłoszone podczas wykonywania funkcji kończą się niepowodzeniem tylko przez pojedyncze wywołanie, które zgłosiło wyjątek. Słowo kluczowe oznacza, że `readFileAsync` kroki wykonywane tylko po `readFile` zakończeniu zostały wykonane. `await` W `async` przypadku `await`i ,`context.done()` nie trzeba również wywoływać wywołania zwrotnego.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    try {
        const data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące zasoby:

+ [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
+ [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
+ [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)

["Func Azure functionapp Publish"]: functions-run-local.md#project-file-deployment
