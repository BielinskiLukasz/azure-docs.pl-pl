---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205707"
---
## <a name="local-settings-file"></a>Plik ustawień lokalnych

Plik Local. Settings. JSON przechowuje ustawienia aplikacji, parametry połączenia i ustawienia używane przez lokalne narzędzia deweloperskie. Ustawienia w pliku Local. Settings. JSON są używane tylko w przypadku lokalnego uruchamiania projektów. Plik ustawień lokalnych ma następującą strukturę:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Te ustawienia są obsługiwane podczas lokalnego uruchamiania projektów:

| Ustawienie      | Opis                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Jeśli to ustawienie ma wartość `true`, wszystkie wartości są szyfrowane za pomocą klucza komputera lokalnego. Używany z poleceniami `func settings`. Wartość domyślna to `false`. |
| **`Values`** | Tablica ustawień aplikacji i parametrów połączenia używanych, gdy projekt jest uruchomiony lokalnie. Pary klucz-wartość (String-String) odpowiadają ustawieniom aplikacji w aplikacji funkcji na platformie Azure, takich jak [`AzureWebJobsStorage`]. Wiele wyzwalaczy i powiązań ma właściwość, która odwołuje się do ustawienia aplikacji parametrów połączenia, takiego jak `Connection` dla [wyzwalacza usługi BLOB Storage](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Dla tych właściwości potrzebne jest ustawienie aplikacji zdefiniowane w tablicy `Values`. <br/>[`AzureWebJobsStorage`] jest wymaganym ustawieniem aplikacji dla wyzwalaczy innych niż http. <br/>Wersja 2. x i nowsze środowisko uruchomieniowe funkcji wymaga ustawienia [`FUNCTIONS_WORKER_RUNTIME`], które jest generowane dla projektu przez podstawowe narzędzia. <br/> Jeśli [emulator usługi Azure Storage](../articles/storage/common/storage-use-emulator.md) jest zainstalowany lokalnie i ustawisz [`AzureWebJobsStorage`] na `UseDevelopmentStorage=true`, narzędzia podstawowe używają emulatora. Emulator jest przydatny podczas opracowywania, ale przed wdrożeniem należy przetestować rzeczywiste połączenie z magazynem.<br/> Wartości muszą być ciągami, a nie obiektami JSON ani tablicami. Nazwy ustawień nie mogą zawierać dwukropka (`:`) ani podwójnego podkreślenia (`__`). Te znaki są zarezerwowane przez środowisko uruchomieniowe.  |
| **`Host`** | Ustawienia w tej sekcji dostosowują proces hosta funkcji w przypadku uruchamiania projektów lokalnie. Te ustawienia są niezależne od ustawień pliku host. JSON, które są również stosowane podczas uruchamiania projektów na platformie Azure. |
| **`LocalHttpPort`** | Ustawia domyślny port używany podczas uruchamiania hosta funkcji lokalnych (`func host start` i `func run`). Opcja wiersza polecenia `--port` ma pierwszeństwo przed tym ustawieniem. |
| **`CORS`** | Definiuje źródła dozwolone dla [udostępniania zasobów między źródłami (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Źródła są dostarczane jako rozdzielana przecinkami lista bez spacji. Wartość symbolu wieloznacznego (\*) jest obsługiwana, co pozwala na żądania z dowolnego źródła. |
| **`CORSCredentials`** |  Po ustawieniu na `true`, zezwala na żądania `withCredentials`. |
| **`ConnectionStrings`** | Kolekcja. Nie używaj tej kolekcji dla parametrów połączenia używanych przez powiązania funkcji. Ta kolekcja jest używana tylko przez platformy, które zazwyczaj pobierają parametry połączenia z sekcji `ConnectionStrings` pliku konfiguracyjnego, takich jak [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Parametry połączenia w tym obiekcie są dodawane do środowiska z typem dostawcy [System. Data. SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Elementy w tej kolekcji nie są publikowane na platformie Azure przy użyciu innych ustawień aplikacji. Należy jawnie dodać te wartości do kolekcji `Connection strings` ustawień aplikacji funkcji. Jeśli tworzysz [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) w kodzie funkcji, należy przechowywać wartość parametrów połączenia z innymi połączeniami w **ustawieniach aplikacji** w portalu. |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
