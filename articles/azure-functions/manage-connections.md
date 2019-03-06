---
title: Zarządzanie połączeniami w usłudze Azure Functions
description: Dowiedz się, jak uniknąć problemów z wydajnością w usłudze Azure Functions przy użyciu statycznych połączenia klientów.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 965fa1e82be3fb87bf58a0114f97091bad212738
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450740"
---
# <a name="manage-connections-in-azure-functions"></a>Zarządzanie połączeniami w usłudze Azure Functions

Funkcje w aplikacji funkcji udostępniania zasobów. Wśród tych zasoby udostępnione są połączenia: Połączenia HTTP, połączenia z bazą danych i połączeń z usługami, takimi jak usługi Azure Storage. Wiele funkcji są uruchomione równocześnie, prawdopodobnie brak dostępnych połączeń. W tym artykule wyjaśniono, jak kod funkcji w celu uniknięcia przy użyciu więcej połączeń niż jest to wymagane.

## <a name="connection-limit"></a>Limit połączeń

Liczba dostępnych połączeń jest ograniczona, częściowo, ponieważ aplikacja funkcji jest uruchamiana w [środowisku piaskownicy](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). On ograniczenia, które piaskownicy nakłada się na kodzie [dzienny limit liczby połączeń (obecnie na 600 aktywnych połączeń i 1200 łączna liczba połączeń)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) dla każdego wystąpienia. Po osiągnięciu tego limitu, środowisko uruchomieniowe usługi functions tworzy dziennik z następującym komunikatem: `Host thresholds exceeded: Connections`.

To ograniczenie jest dla każdego wystąpienia.  Podczas [kontrolera skalowania dodaje wystąpień aplikacji funkcji](functions-scale.md#how-the-consumption-plan-works) do obsługi więcej żądań, każde wystąpienie ma limit połączeń niezależne. Oznacza to, nie ma żadnego limitu połączenia globalnego i może mieć znacznie więcej niż 600 aktywnych połączeń ze wszystkich aktywnych wystąpień.

## <a name="static-clients"></a>Klientów statycznych

Aby uniknąć, zawierający więcej połączeń niż to konieczne, ponownie użyć wystąpienia klienta, zamiast tworzyć nowe przy każdym wywołaniu funkcji. Firma Microsoft zaleca ponowne użycie połączenia klienta dla dowolnego języka, który może zapisać funkcji w. Na przykład, takich jak klientów programu .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), i zarządzać połączeń klientów usługi Azure Storage, jeśli za pomocą pojedynczej, statycznej klienta.

Poniżej przedstawiono wskazówki, które należy wykonać podczas korzystania z klienta specyficzne dla usługi w aplikacji usługi Azure Functions:

- *Nie* tworzenia nowego klienta przy użyciu każdego wywołania funkcji.
- *Czy* Tworzenie statycznej, pojedynczego klienta używanego dla każdego wywołania funkcji.
- *Należy wziąć pod uwagę* tworzenia klienta pojedynczego, statycznej klasy pomocnika udostępnionych, jeśli różne funkcje korzystają z tej samej usługi.

## <a name="client-code-examples"></a>Przykłady kodu klienta

W tej sekcji przedstawiono najlepsze rozwiązania dotyczące tworzenia i używania klientów z kodu funkcji.

### <a name="httpclient-example-c"></a>HttpClient przykładzie (C#)

Oto przykład C# działa kod, który tworzy statycznego [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) wystąpienie:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Często zadawane pytania dotyczące [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) na platformie .NET jest "Powinien I usunięcia danych klienta?" Ogólnie rzecz biorąc, usuwania obiektów, które implementują `IDisposable` po zakończeniu korzystania z nich. Ale nie dispose statyczne klienta, ponieważ nie są wykonywane przy użyciu jej, gdy funkcja skończy działanie. Kliencki statyczny czas trwania aplikacji na żywo.

### <a name="http-agent-examples-nodejs"></a>Przykłady agenta HTTP (Node.js)

Ponieważ zapewnia lepsze połączenia opcji zarządzania, należy użyć natywnych [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) klasy zamiast obce metod, takich jak `node-fetch` modułu. Parametry połączenia są skonfigurowane za pomocą opcji na `http.agent` klasy. Aby uzyskać szczegółowe opcje dostępne za pośrednictwem protokołu HTTP agenta, zobacz [nowy Agent (\[opcje\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Globalna `http.globalAgent` używane przez klasy `http.request()` znajdują się wszystkie te wartości, ustaw odpowiednie wartości domyślne. Zalecanym sposobem Konfigurowanie limitów połączeń w funkcjach jest aby ustawić maksymalną liczbę globalnie. W poniższym przykładzie ustawiono maksymalną liczbę gniazd dla aplikacji funkcji:

```js
http.globalAgent.maxSockets = 200;
```

 Poniższy przykład tworzy nowe żądanie HTTP przy użyciu niestandardowych agenta HTTP tylko dla tego żądania:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Przykładowy kod DocumentClient (C#)

[Klient DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) łączy się z wystąpieniem usługi Azure Cosmos DB. Dokumentacja usługi Azure Cosmos DB zaleca, aby użytkownik [okres istnienia aplikacji za pomocą klienta usługi Azure Cosmos DB pojedyncze](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Jednym ze wzorców tą operacją w funkcji można znaleźć w poniższym przykładzie:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

## <a name="sqlclient-connections"></a>Połączeń klient SQL

Kod funkcji, można użyć dostawcy danych .NET Framework dla programu SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) aby nawiązywać połączenia z relacyjnej bazy danych SQL. Jest to również źródłowy dostawca dla struktur danych, korzystające z programu ADO.NET, takich jak [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). W odróżnieniu od [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) i [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) połączeń ADO.NET implementuje buforowanie połączeń, domyślnie. Jednak ponieważ nadal można uruchomić z połączeń, należy zoptymalizować połączeń z bazą danych. Aby uzyskać więcej informacji, zobacz [programu SQL Server połączenia puli (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Niektóre struktur danych, takim jak Entity Framework, zazwyczaj pobrać parametry połączenia z **ConnectionStrings** sekcję pliku konfiguracji. W takim przypadku należy jawnie dodać parametry połączenia bazy danych SQL do **parametry połączenia** kolekcję ustawień aplikacji funkcji, a w [pliku local.settings.json](functions-run-local.md#local-settings-file) w projekcie lokalnym. Jeśli tworzysz wystąpienia [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) w kodzie funkcji należy przechowywać wartość parametrów połączenia w **ustawienia aplikacji** przy użyciu innych połączeń.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat Dlaczego firma Microsoft zaleca klientom statyczne zobacz [antywzorzec niewłaściwy sposób tworzenia wystąpień](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Aby uzyskać więcej porad dotyczących wydajności usługi Azure Functions, zobacz [zoptymalizować wydajność i niezawodność usługi Azure Functions](functions-best-practices.md).
