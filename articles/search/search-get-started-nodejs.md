---
title: 'Node. js — szybki start: Twórz, Ładuj i badaj indeksy przy użyciu Azure Search interfejsów API REST — Azure Search'
description: Przykład środowiska Node. js dla Azure Search, pokazujący, jak tworzyć, ładować dane do i wysyłać zapytania z języka JavaScript.
author: lobrien
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: laobri
ms.openlocfilehash: 3a0b5706b41bdc51a4fe6e49b20296d3824b717c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947109"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs"></a>Szybki start: Tworzenie indeksu Azure Search w programie Node. js
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Program PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Utwórz aplikację Node. js, która tworzy, ładuje i bada indeks Azure Search. W tym artykule przedstawiono sposób tworzenia aplikacji krok po kroku. Alternatywnie można [pobrać kod źródłowy i dane](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/) i uruchomić aplikację z wiersza polecenia.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki Start są używane następujące usługi, narzędzia i dane.

+ Środowisko [Node.js](https://nodejs.org).
+ [Npm](https://www.npmjs.com) powinny być instalowane przez program Node. js.
+ Przykładowa struktura indeksu i pasujące dokumenty są podane w tym artykule lub [repozytorium](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/).
+ [Utwórz usługę Azure Search](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start.

Rekomendowane

* [Program Visual Studio Code](https://code.visualstudio.com)
* Rozszerzenia [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) i [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) dla programu vscode.

<a name="get-service-info"></a>
## <a name="get-keys-and-urls"></a>Pobierz klucze i adresy URL

Wywołania usługi wymagają punktu końcowego adresu URL i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Pobierz nazwę usługi wyszukiwania. Nazwę usługi można potwierdzić, przeglądając adres URL punktu końcowego. Jeśli adres URL `https://mydemo.search.windows.net`punktu końcowego to, nazwa usługi to `mydemo`.

2. W obszarze **Ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

    Pobierz również klucz zapytania. Najlepszym rozwiązaniem jest wydawanie żądań zapytań z dostępem tylko do odczytu.

![Pobieranie nazwy usługi i administratora oraz kluczy zapytań](media/search-get-started-nodejs/service-name-and-keys.png)

Wszystkie żądania wymagają klucza API-Key w nagłówku każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia zaufanie dla każdego żądania, między aplikacją wysyłającą żądanie a usługą, która go obsługuje.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Zacznij od otwarcia konsoli programu PowerShell lub innego środowiska, w którym zainstalowano program Node. js.

1. Utwórz katalog deweloperski, nadając mu nazwę `quickstart` :

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Zainicjuj pusty projekt z NPM przez uruchomienie `npm init`. Zaakceptuj wartości domyślne, z wyjątkiem licencji, która powinna być ustawiona na wartość "MIT". 

1. Dodaj pakiety, które będą zależne od kodu i pomocy podczas opracowywania:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Upewnij się, że zostały skonfigurowane projekty i jego zależności, sprawdzając, czy plik **Package. JSON** wygląda podobnie do poniższego:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```
Utwórz plik **azure_search_config. JSON** , aby przechowywać dane usługi wyszukiwania:

```json
{
    "serviceName" : "[SERVICE_NAME]",
    "adminKey" : "[ADMIN_KEY]",
    "queryKey" : "[QUERY_KEY]",
    "indexName" : "hotels-quickstart"
}
```

Zastąp `[SERVICE_NAME]` wartość nazwą swojej usługi wyszukiwania. Zamień `[ADMIN_KEY]` i`[QUERY_KEY]` na zarejestrowane wcześniej wartości kluczy. 

## <a name="1---create-index"></a>1 — Tworzenie indeksu 

Utwórz plik **hotels_quickstart_index. JSON**.  Ten plik definiuje sposób, w jaki Azure Search współpracuje z dokumentami, które zostaną załadowane w następnym kroku. Każde pole zostanie zidentyfikowane przez `name` i ma określony. `type` Każde pole ma także serię atrybutów indeksu, które określają, czy Azure Search mogą przeszukiwać, filtrować, sortować i aspektować pola. Większość pól to proste typy danych, ale niektóre, takie jak `AddressType` złożone typy, które umożliwiają tworzenie rozbudowanych struktur danych w indeksie.  Więcej informacji o [obsługiwanych typach danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i atrybutach [indeksu](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)można znaleźć w części. 

Dodaj następujący kod do **hotels_quickstart_index. JSON** lub [Pobierz plik](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```
    

Dobrym sposobem jest oddzielenie szczegółowych informacji o konkretnym scenariuszu od kodu, który będzie szeroko stosowany. Klasa zdefiniowana w pliku **AzureSearchClient. js** będzie wiedzieć, jak konstruować adresy URL żądań, zgłaszać żądanie przy użyciu interfejsu API pobierania i reagować na kod stanu odpowiedzi. `AzureSearchClient`

Rozpocznij pracę nad **AzureSearchClient. js** przez zaimportowanie pakietu **węzłów do pobrania** i utworzenie prostej klasy. Izoluj elementy `AzureSearchClient` , które można zmieniać, przekazując do jego konstruktora różne wartości konfiguracji:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2019-05-06';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

Pierwszą odpowiedzialnością klasy jest znajomość sposobu konstruowania adresów URL, do których mają być wysyłane różne żądania. Kompiluj te adresy URL przy użyciu metod wystąpienia, które używają danych konfiguracyjnych przekazaną do konstruktora klasy. Należy zauważyć, że konstrukcja adresu URL jest specyficzna dla wersji interfejsu API i musi mieć argument określający tę wersję (w tej `2019-05-06`aplikacji). 

Dodaj następującą metodę w treści klasy:

```javascript
    getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

Kolejna odpowiedzialność polega na żądaniu asynchronicznym z interfejsem API pobierania. Asynchroniczna Metoda `request` statyczna przyjmuje adres URL, ciąg określający metodę http ("Get", "Put", "post", "Delete"), klucz, który ma być używany w żądaniu, oraz opcjonalny obiekt JSON. `headers` Zmienna mapuje(czykluczadministratoralubkluczzapytaniatylkodoodczytu)donagłówkażądaniaHTTP"API`queryKey` -Key". Opcje żądania zawsze zawierają `method` do użycia `headers`i. Jeśli `bodyJson` tak `null`nie jest, treść żądania HTTP jest ustawiana `bodyJson`na ciąg reprezentujący. `request` Zwraca obietnicę interfejsu API pobierania, aby wykonać żądanie HTTP.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
    if (bodyJson !== null) {
        console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
    }
    */

    const headers = {
        'content-type' : 'application/json',
        'api-key' : apiKey
    };
    const init = bodyJson === null ?
        { 
            method, 
            headers
        }
        : 
        {
            method, 
            headers,
            body : JSON.stringify(bodyJson)
        };
    return fetch(url, init);
}
```

W celach demonstracyjnych po prostu będziemy zgłosić wyjątek, jeśli żądanie HTTP nie zostało zakończone pomyślnie. W rzeczywistej aplikacji prawdopodobnie istnieje możliwość rejestrowania i diagnozowania kodu stanu HTTP w ramach `response` żądania usługi wyszukiwania. 
    
```javascript
    static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
    }
```

Na koniec Dodaj metody, które umożliwiają wykrywanie, usuwanie i tworzenie indeksu Azure Search. Wszystkie te metody mają tę samą strukturę:

* Pobierz punkt końcowy, do którego zostanie wykonane żądanie.
* Wygeneruj żądanie z odpowiednim punktem końcowym, czasownikiem HTTP, kluczem interfejsu API i treścią. `queryAsync()`używa klucza zapytania, w przeciwnym razie jest używany klucz administratora.
* `await`odpowiedź na żądanie.  
* Działanie na kodzie stanu odpowiedzi.
* Zwróć obietnicę nieprawidłowej wartości (wartości logicznej `this`, lub wyników zapytania). 

```javascript
    async indexExistsAsync() { 
        console.log("\n Checking if index exists...");
        const endpoint = this.getIndexUrl();
        const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
        // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
        const exists = response.status >= 200 && response.status < 300;
        return exists;
    }
    
    async deleteIndexAsync() {
        console.log("\n Deleting existing index...");
        const endpoint = this.getIndexUrl();
        const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
        AzureSearchClient.throwOnHttpError(response);
        return this;
    }
    
    async createIndexAsync(definition) {
        console.log("\n Creating index...");
        const endpoint = this.getIndexUrl();
        const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
        AzureSearchClient.throwOnHttpError(response);
        return this;
    }
```

Upewnij się, że metody znajdują się wewnątrz klasy i że eksportujesz klasę. Najbardziej zewnętrznym zakresem **AzureSearchClient. js** powinien być:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Klasa zorientowana obiektowo była dobrym wyborem dla potencjalnie wielokrotnego użytku modułu **AzureSearchClient. js** , ale nie jest konieczna dla programu głównego, który zostanie umieszczony w pliku o nazwie **index. js**. 

Utwórz **index. js** i zacznij od przełączenia:

* Pakiet **NConf** , który zapewnia elastyczność określania konfiguracji za pomocą notacji JSON, zmiennych środowiskowych lub argumentów wiersza polecenia.
* Dane z pliku **hotels_quickstart_index. JSON** .
* Moduł `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Pakiet [ **NConf** ](https://github.com/indexzero/nconf) umożliwia określenie danych konfiguracyjnych w różnych formatach, takich jak zmienne środowiskowe lub wiersz polecenia. Będziemy używać **NConf** w sposób podstawowy, aby odczytać plik **azure_search_config. JSON** i zwrócić zawartość tego pliku jako słownik. Przyużyciu `get(key)` funkcji NConf możemy szybko sprawdzić, czy informacje o konfiguracji zostały prawidłowo dostosowane. Na koniec zwracamy konfigurację:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME' ) {
    throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

Funkcja tworzy element, `Promise` który jest rozpoznawany po upływie określonego czasu. `sleep` Użycie tej funkcji umożliwia wstrzymywanie aplikacji podczas oczekiwania na ukończenie asynchronicznych operacji indeksowania i udostępnienie ich. Dodanie takiego opóźnienia jest zazwyczaj konieczne tylko w pokazach, testach i przykładowych aplikacjach.

```javascript
function sleep(ms)
{
    return(
        new Promise(function(resolve, reject)
        {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Na koniec Określ i Wywołaj główną funkcję `run` asynchroniczną. Ta funkcja wywołuje inne funkcje w kolejności, oczekując na to, aby rozwiązać problem `Promise`.

* Pobierz konfigurację z `getAzureConfiguration()` zapisaną wcześniej
* Utwórz nowe `AzureSearchClient` wystąpienie, przekazując wartości z konfiguracji
* Sprawdź, czy indeks istnieje, a jeśli go, Usuń
* Tworzenie indeksu przy użyciu `indexDefinition` załadowanego z **hotels_quickstart_index. JSON**
* Dodaj dokumenty dotyczące hoteli załadowane z **hoteli. JSON**
* Wykonywanie zapytania dotyczącego indeksu Azure Search `doQueriesAsync()` przy użyciu napisanej metody

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get["serviceName"]);
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        const indexDefinition = require('./hotels_quickstart_index.json');
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Nie zapomnij, że końcowe `run()`wywołanie! Jest to punkt wejścia do programu po uruchomieniu `node index.js` w następnym kroku.

Zwróć uwagę `AzureSearchClient.indexExistsAsync()` , `AzureSearchClient.deleteIndexAsync()` że nie przyjmują parametrów. Te funkcje są `AzureSearchClient.request()` wywoływane bez `bodyJson` argumentu. W `AzureSearchClient.request()`, ponieważ `bodyJson === null` jest `true`to, `init` struktura jest ustawiona jako tylko zlecenie http ("Get" dla `indexExistsAsync()` i "Delete" dla `deleteIndexAsync()`) i nagłówków, które określają klucz żądania.  

Z kolei `AzureSearchClient.createIndexAsync(indexDefinition)` Metoda przyjmuje parametr . Funkcja w `index.js`, przekazuje zawartość pliku **hotels_quickstart_index. JSON** do `AzureSearchClient.createIndexAsync(indexDefinition)` metody. `run` Metoda przekazuje tę definicję do `AzureSearchClient.request()`. `createIndexAsync()` W `AzureSearchClient.request()`, ponieważ `bodyJson === null` jest teraz `false`, `init` struktura zawiera nie tylko czasownik http ( `body` "Put") i nagłówki, ale ustawia do danych definicji indeksu.

### <a name="prepare-and-run-the-sample"></a>Przygotowywanie i uruchamianie przykładu

Użyj okna terminalu dla następujących poleceń.

1. Przejdź do folderu, który zawiera plik **Package. JSON** , i resztę kodu.
1. Zainstaluj pakiety dla przykładu za pomocą `npm install`.  To polecenie spowoduje pobranie pakietów, od których zależy kod.
1. Uruchom program za pomocą `node index.js`.

Powinna zostać wyświetlona seria komunikatów opisujących akcje podejmowane przez program. Jeśli chcesz zobaczyć więcej szczegółów na temat żądań, możesz usunąć komentarz z [wierszy na początku `AzureSearchClient.request()` metody](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/AzureSearchClient.js#LL20-LL26) w **AzureSearchClient. js**. 

Otwórz **Przegląd** usługi wyszukiwania w Azure Portal. Wybierz kartę **indeksy** . Powinna zostać wyświetlona zawartość podobna do tej:

![Zrzut ekranu przedstawiający Azure Portal, Search Service Omówienie, karta indeksy](media/search-get-started-nodejs/create-index-no-data.png)

W następnym kroku dodamy dane do indeksu. 

## <a name="2---load-documents"></a>2 — ładowanie dokumentów 

W Azure Search dokumenty są strukturami danych, które są danymi wejściowymi do indeksowania i wyjść z zapytań. Należy OPUBLIKOWAĆ takie dane w indeksie. Używa innego punktu końcowego niż operacje wykonywane w poprzednim kroku. Otwórz **AzureSearchClient. js** i Dodaj następującą metodę po `getIndexUrl()`:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Podobnie `AzureSearchClient.createIndexAsync(definition)`jak, potrzebna jest funkcja, która `AzureSearchClient.request()` wywołuje i przekazuje dane hotelu jako treść. W **AzureSearchClient. js** Dodaj `postDataAsync(hotelsData)` po `createIndexAsync(definition)`:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Dane wejściowe dokumentu mogą być wierszami w bazie danych, obiektami BLOB w usłudze BLOB Storage lub, jak w tym przykładzie, dokumentami JSON na dysku. Możesz pobrać pliki [hoteli. JSON](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/hotels.json) lub utworzyć własny plik **hoteli. JSON** o następującej zawartości:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}

```

Aby załadować te dane do programu, należy zmodyfikować **index. js** przez dodanie wiersza odwołującego `hotelData` się do najbliżej góry:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Teraz zmodyfikuj `run()` funkcję w **index. js**. Udostępnienie indeksu może potrwać kilka sekund, więc Dodaj 2-sekundowe wstrzymanie przed wywołaniem `AzureSearchClient.postDataAsync(hotelData)`:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Ponownie uruchom program z `node index.js`. Powinien pojawić się nieco inny zestaw komunikatów od tych, które zostały podane w kroku 1. Tym razem indeks istnieje i powinien zostać wyświetlony komunikat dotyczący usuwania usuniętych danych przed utworzeniem nowego indeksu przez aplikację. 

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Wróć do karty **indeksy** w **omówieniu** usługi wyszukiwania na Azure Portal. Indeks zawiera teraz cztery dokumenty i zużywa pewną ilość miejsca w magazynie (może to potrwać kilka minut, aby interfejs użytkownika prawidłowo odzwierciedlał podstawowy stan indeksu). Kliknij nazwę indeksu, który ma zostać przeniesiony do **Eksploratora wyszukiwania**. Na tej stronie można eksperymentować z kwerendami danych. Spróbuj wyszukać ciąg zapytania z `*&$count=true` i uzyskać wszystkie dokumenty oraz liczbę wyników. Spróbuj użyć ciągu `historic&highlight=Description&$filter=Rating gt 4` zapytania i odwrócić pojedynczy dokument z słowem "historyczna" `<em></em>` otoczonym tagami. Przeczytaj więcej na temat [tworzenia zapytania w Azure Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Odtwórz te zapytania w kodzie, otwierając program **index. js** i dodając ten kod w górnej części:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

W tym samym pliku **index. js** Napisz `doQueries()` funkcję przedstawioną poniżej. Ta funkcja przyjmuje `AzureSearchClient` obiekt i `AzureSearchClient.queryAsync` stosuje metodę do każdej wartości w `queries` tablicy. Używa `Promise.all()` funkcji do zwrócenia pojedynczej `Promise` , która jest rozpoznawana tylko wtedy, gdy wszystkie zapytania zostały rozwiązane. Wywołanie do `JSON.stringify(body, null, 4)` formatowania wyniku zapytania, aby było bardziej czytelne.

```javascript
async function doQueriesAsync(client) {
    return Promise.all(
        queries.map( async query => {
            const result = await client.queryAsync(query);
            const body = await result.json();
            const str = JSON.stringify( body, null, 4);
            console.log(`Query: ${query} \n ${str}`);
        })
    );
}
```

Zmodyfikuj funkcję w celu wstrzymania wystarczająco długo, aby indeksator działał, a następnie `doQueriesAsync(client)` wywołać funkcję: `run()`

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

Aby zaimplementować `AzureSearchClient.queryAsync(query)`, edytuj plik **AzureSearchClient. js**. Wyszukiwanie wymaga innego punktu końcowego, więc Dodaj funkcję `getSearchUrl(searchTerm)` `getIndexUrl()` obok metod i `getPostDataUrl()` , które zostały już zapisaną.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

Funkcja działa również w **AzureSearchClient. js** i ma taką samą strukturę jak `postDataAsync(data)` i inne funkcje zapytań: `queryAsync(searchTerm)` 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Wyszukiwanie jest wykonywane z czasownikiem "GET" i bez treści, ponieważ termin wyszukiwania jest częścią adresu URL. Należy zauważyć, że w przeciwieństwie do innych funkcji `this.adminKey`, `queryAsync(searchTerm)` które `this.queryKey`są używane, używa. Klucze zapytania, jak nazwa wskazuje, mogą być używane tylko do wykonywania zapytań względem indeksu i nie mogą być używane do modyfikowania indeksu w dowolny sposób. Klucze zapytań są w związku z tym bezpieczniejsze do dystrybucji do aplikacji klienckich.

Uruchom program za pomocą `node index.js`. Teraz, oprócz poprzednich kroków, zapytania będą wysyłane i wyniki zapisywane w konsoli.

### <a name="about-the-sample"></a>Informacje o przykładzie

Przykład korzysta z niewielkiej ilości danych hotelu, wystarczającej do zademonstrowania podstaw tworzenia i wykonywania zapytań dotyczących indeksu Azure Search.

Klasa **AzureSearchClient** hermetyzuje konfigurację, adresy URL i podstawowe żądania HTTP dla usługi wyszukiwania. Plik **index. js** ładuje dane konfiguracyjne usługi Azure Search, dane hotelowe, które zostaną przekazane do indeksowania, oraz, w `run` funkcji, zamówienia i wykonuje różne operacje.

Ogólnym zachowaniem `run` funkcji jest usunięcie indeksu Azure Search, jeśli istnieje, utworzenie indeksu, dodanie danych i wykonanie niektórych zapytań.  

## <a name="clean-up"></a>Czyszczenie 

Gdy pracujesz nad własną subskrypcją, dobrym pomysłem jest zakończenie projektu w celu ustalenia, czy nadal potrzebujesz utworzonych zasobów. Zasoby po lewej stronie mogą być kosztowne. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.
Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start Node. js Pracujesz przez serię zadań, aby utworzyć indeks, załadować go z dokumentami i uruchamiać zapytania. Wprowadziliśmy pewne czynności, takie jak odczytywanie konfiguracji i definiowanie zapytań w najprostszym możliwym sposobie. W rzeczywistej aplikacji warto umieścić te problemy w osobnych modułach, które zapewniają elastyczność i hermetyzację. 
 
Jeśli masz już jakieś doświadczenie z usługą Azure Search, możesz użyć tego przykładu jako punktu wyjścia do wypróbowania sugestorów (uzupełnianie przy wpisywaniu lub autouzupełnianie zapytań), filtrów i nawigacji aspektowej. Jeśli dopiero zaczynasz Azure Search, zalecamy podjęcie dalszych samouczków w celu opracowania informacji o tym, co można utworzyć. Aby znaleźć więcej zasobów, odwiedź naszą [stronę dokumentacji](https://azure.microsoft.com/documentation/services/search/). 

> [!div class="nextstepaction"]
> [Wywołaj Azure Search ze strony sieci Web przy użyciu języka JavaScript](https://github.com/liamca/azure-search-javascript-samples)
