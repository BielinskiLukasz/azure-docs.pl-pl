---
title: Dokumentacja dewelopera programu PowerShell dla Azure Functions
description: Dowiedz się, jak opracowywać funkcje przy użyciu programu PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357040"
---
# <a name="azure-functions-powershell-developer-guide"></a>Przewodnik dewelopera programu Azure Functions PowerShell

W tym artykule przedstawiono szczegółowe informacje dotyczące sposobu pisania Azure Functions przy użyciu programu PowerShell.

Funkcja PowerShell platformy Azure (funkcja) jest reprezentowana przez skrypt programu PowerShell, który jest wykonywany po wyzwoleniu. Każdy skrypt funkcji ma powiązany plik `function.json`, który definiuje sposób zachowania funkcji, na przykład sposób wyzwalania i parametry wejściowe i wyjściowe. Aby dowiedzieć się więcej, zobacz [artykuł wyzwalacze i powiązania](functions-triggers-bindings.md). 

Podobnie jak w przypadku innych rodzajów funkcji, funkcja skryptu programu PowerShell przyjmuje parametry, które pasują do nazw wszystkich powiązań wejściowych zdefiniowanych w pliku `function.json`. Przeszedł również parametr `TriggerMetadata`, który zawiera dodatkowe informacje na temat wyzwalacza, który uruchomił funkcję.

W tym artykule przyjęto założenie, że już odczytano [informacje dotyczące deweloperów Azure Functions](functions-reference.md). Należy również ukończyć funkcję [Szybki Start dla programu PowerShell](functions-create-first-function-powershell.md) , aby utworzyć pierwszą funkcję programu PowerShell.

## <a name="folder-structure"></a>Struktura folderów

Wymagana struktura folderów dla projektu programu PowerShell wygląda następująco. Tę wartość domyślną można zmienić. Aby uzyskać więcej informacji, zobacz sekcję [scriptFile](#configure-function-scriptfile) poniżej.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

W katalogu głównym projektu istnieje udostępniony plik [`host.json`](functions-host-json.md) , którego można użyć do skonfigurowania aplikacji funkcji. Każda funkcja ma folder z własnym plikiem kodu (. ps1) i plikiem konfiguracji powiązania (`function.json`). Nazwa katalogu funkcji Function. JSON jest zawsze nazwą funkcji.

Niektóre powiązania wymagają obecności pliku `extensions.csproj`. Rozszerzenia powiązań wymagane w [wersji 2. x i nowszych wersjach](functions-versions.md) środowiska uruchomieniowego funkcji są zdefiniowane w pliku `extensions.csproj`, z rzeczywistymi plikami biblioteki w folderze `bin`. Podczas programowania lokalnego należy [zarejestrować rozszerzenia powiązań](functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w Azure Portal Rejestracja jest wykonywana.

W aplikacjach funkcji programu PowerShell możesz opcjonalnie mieć `profile.ps1`, które są uruchamiane, gdy uruchamiana jest aplikacja funkcji (w przeciwnym razie jako *[zimny start](#cold-start)* ). Aby uzyskać więcej informacji, zobacz [profil programu PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definiowanie skryptu programu PowerShell jako funkcji

Domyślnie środowisko uruchomieniowe funkcji wyszukuje funkcję w `run.ps1`, w której `run.ps1` ma ten sam katalog nadrzędny co odpowiadający mu `function.json`.

Skrypt przeszedł wiele argumentów podczas wykonywania. Aby obsłużyć te parametry, Dodaj blok `param` na początku skryptu, tak jak w poniższym przykładzie:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata — parametr

Parametr `TriggerMetadata` jest używany do dostarczania dodatkowych informacji na temat wyzwalacza. Dodatkowe metadane różnią się od powiązań do powiązania, ale wszystkie zawierają Właściwość `sys`, która zawiera następujące dane:

```powershell
$TriggerMetadata.sys
```

| Właściwość   | Opis                                     | Typ     |
|------------|-------------------------------------------------|----------|
| utcNow     | Kiedy, w UTC, funkcja została wyzwolona        | DateTime |
| MethodName | Nazwa funkcji, która została wyzwolona     | ciąg   |
| RandGuid   | unikatowy identyfikator GUID dla tego wykonania funkcji | ciąg   |

Każdy typ wyzwalacza ma inny zestaw metadanych. Na przykład `$TriggerMetadata` dla `QueueTrigger` zawiera `InsertionTime`, `Id`, `DequeueCount`, między innymi. Aby uzyskać więcej informacji na temat metadanych wyzwalacza kolejki, przejdź do [oficjalnej dokumentacji wyzwalaczy kolejek](functions-bindings-storage-queue-trigger.md#message-metadata). Zapoznaj się z dokumentacją dotyczącą [wyzwalaczy](functions-triggers-bindings.md) , z którymi pracujesz, aby zobaczyć, co znajduje się w metadanych wyzwalacza.

## <a name="bindings"></a>Powiązania

W programie PowerShell [powiązania](functions-triggers-bindings.md) są konfigurowane i definiowane w funkcji Function. JSON. Funkcje współdziałają z powiązaniami na wiele sposobów.

### <a name="reading-trigger-and-input-data"></a>Odczytywanie wyzwalacza i danych wejściowych

Wyzwalacze i powiązania wejściowe są odczytywane jako parametry przesłane do funkcji. Powiązania wejściowe mają ustawioną `direction` `in` w funkcji Function. JSON. Właściwość `name` zdefiniowana w `function.json` jest nazwą parametru w bloku `param`. Ponieważ program PowerShell używa nazwanych parametrów do powiązania, kolejność parametrów nie ma znaczenia. Jednak najlepszym rozwiązaniem jest przestrzeganie kolejności powiązań zdefiniowanych w `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Zapisywanie danych wyjściowych

W funkcjach, powiązanie wyjściowe ma `direction` ustawione na `out` w funkcji Function. JSON. Możesz zapisywać do powiązania danych wyjściowych za pomocą polecenia cmdlet `Push-OutputBinding`, które jest dostępne dla środowiska uruchomieniowego usługi Functions. We wszystkich przypadkach Właściwość `name` powiązania, zgodnie z definicją w `function.json`, odpowiada parametrowi `Name` polecenia cmdlet `Push-OutputBinding`.

Poniżej przedstawiono sposób wywoływania `Push-OutputBinding` w skrypcie funkcji:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Możesz również przekazać wartość dla określonego powiązania za pomocą potoku.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` zachowuje się inaczej w zależności od wartości określonej dla `-Name`:

* Gdy nie można rozpoznać określonej nazwy jako prawidłowego powiązania danych wyjściowych, zostanie zgłoszony błąd.

* Gdy powiązanie danych wyjściowych akceptuje zbiór wartości, możesz wywoływać `Push-OutputBinding` wielokrotnie, aby wypchnąć wiele wartości.

* Gdy powiązanie danych wyjściowych akceptuje tylko pojedynczą wartość, wywołanie `Push-OutputBinding` drugiego czasu zgłasza błąd.

#### <a name="push-outputbinding-syntax"></a>Składnia `Push-OutputBinding`

Poniżej podano prawidłowe parametry wywołania `Push-OutputBinding`:

| Name (Nazwa) | Typ | Pozycja | Opis |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Ciąg | 1 | Nazwa powiązania danych wyjściowych, które chcesz ustawić. |
| **`-Value`** | Obiekt | 2 | Wartość powiązania danych wyjściowych, które ma zostać ustawione, które jest akceptowane z potoku ByValue. |
| **`-Clobber`** | SwitchParameter | Nazywany | Obowiązkowe Gdy jest określony, wymusza wartość ustawioną dla określonego powiązania danych wyjściowych. | 

Obsługiwane są również następujące typowe parametry: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Aby uzyskać więcej informacji, zobacz [Informacje o parametry](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Przykład polecenia push-OutputBinding: odpowiedzi HTTP

Wyzwalacz HTTP zwraca odpowiedź przy użyciu powiązania danych wyjściowych o nazwie `response`. W poniższym przykładzie powiązanie danych wyjściowych `response` ma wartość "Output #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Ponieważ dane wyjściowe to HTTP, który akceptuje tylko pojedynczą wartość, zostanie wygenerowany błąd, gdy `Push-OutputBinding` jest wywoływana po raz drugi.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

W przypadku danych wyjściowych, które akceptują tylko pojedyncze wartości, można użyć parametru `-Clobber`, aby zastąpić starą wartość zamiast próbować dodać do kolekcji. W poniższym przykładzie przyjęto założenie, że została już dodana wartość. Przy użyciu `-Clobber`, odpowiedź z następującego przykładu zastępuje istniejącą wartość, aby zwrócić wartość "Output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Przykład push-OutputBinding: powiązanie danych wyjściowych kolejki

`Push-OutputBinding` służy do wysyłania danych do powiązań wyjściowych, takich jak [powiązanie danych wyjściowych usługi Azure queue storage](functions-bindings-storage-queue-output.md). W poniższym przykładzie komunikat zapisany w kolejce ma wartość "Output #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Powiązanie danych wyjściowych dla kolejki magazynu akceptuje wiele wartości wyjściowych. W takim przypadku należy wywołać Poniższy przykład po pierwszym zapisie w kolejce listy z dwoma elementami: "Output #1" i "Output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Poniższy przykład, gdy wywoływany po poprzednich dwóch, dodaje do kolekcji wyjściowej dwie więcej wartości:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Po zapisaniu w kolejce komunikat zawiera te cztery wartości: "Output #1", "Output #2", "Output #3" i "Output #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` polecenie cmdlet

Za pomocą polecenia cmdlet `Get-OutputBinding` można pobrać wartości aktualnie ustawionych dla powiązań wyjściowych. To polecenie cmdlet pobiera tablicę skrótów, która zawiera nazwy powiązań wyjściowych z odpowiednimi wartościami. 

Poniżej znajduje się przykład użycia `Get-OutputBinding`, aby zwrócić bieżące wartości powiązania:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` również zawiera parametr o nazwie `-Name`, który może służyć do filtrowania zwracanego powiązania, jak w poniższym przykładzie:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Symbole wieloznaczne (*) są obsługiwane w `Get-OutputBinding`.

## <a name="logging"></a>Rejestrowanie

Logowanie w funkcjach programu PowerShell działa jak regularne rejestrowanie programu PowerShell. Polecenia cmdlet rejestrowania można użyć do zapisu w każdym strumieniu wyjściowym. Każde polecenie cmdlet mapuje na poziom rejestrowania używany przez funkcje.

| Poziom rejestrowania funkcji | Polecenie cmdlet rejestrowania |
| ------------- | -------------- |
| Błąd | **`Write-Error`** |
| Ostrzeżenie | **`Write-Warning`**  | 
| Informacje | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informacje | Zapisuje dane w dzienniku na poziomie _informacji_ . |
| Debugowanie | **`Write-Debug`** |
| Ślad | **`Write-Progress`** <br /> **`Write-Verbose`** |

Oprócz tych poleceń cmdlet wszystkie elementy zapisywane w potoku są przekierowywane do poziomu dziennika `Information` i wyświetlane przy użyciu domyślnego formatowania programu PowerShell.

> [!IMPORTANT]
> Używanie `Write-Verbose` lub `Write-Debug` poleceń cmdlet nie wystarcza do wyświetlenia pełnych informacji i rejestrowania na poziomie debugowania. Należy również skonfigurować próg poziomu dziennika, który deklaruje żądany poziom dzienników. Aby dowiedzieć się więcej, zobacz [Konfigurowanie poziomu dziennika aplikacji funkcji](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurowanie poziomu dziennika aplikacji funkcji

Azure Functions umożliwia zdefiniowanie poziomu progu, aby ułatwić kontrolowanie sposobu zapisu w dziennikach. Aby ustawić wartość progową dla wszystkich śladów, które są zapisywane w konsoli programu, użyj właściwości `logging.logLevel.default` w[odwołaniach pliku host. json] [`host.json`]. To ustawienie ma zastosowanie do wszystkich funkcji w aplikacji funkcji.

Poniższy przykład ustawia próg w celu włączenia pełnego rejestrowania dla wszystkich funkcji, ale ustawia próg, aby włączyć rejestrowanie debugowania dla funkcji o nazwie `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Aby uzyskać więcej informacji, zobacz [Dokumentacja pliku host. JSON].

### <a name="viewing-the-logs"></a>Wyświetlanie dzienników

Jeśli aplikacja funkcji działa na platformie Azure, można użyć Application Insights do monitorowania. Przeczytaj [Azure Functions monitorowania](functions-monitoring.md) , aby dowiedzieć się więcej o przeglądaniu i wysyłaniu zapytań o dzienniki funkcji.

W przypadku korzystania z aplikacja funkcji lokalnie na potrzeby programowania program domyślnie rejestruje w systemie plików. Aby wyświetlić dzienniki w konsoli programu, przed rozpoczęciem aplikacja funkcji należy ustawić zmienną środowiskową `AZURE_FUNCTIONS_ENVIRONMENT` na `Development`.

## <a name="triggers-and-bindings-types"></a>Wyzwalacze i typy powiązań

Istnieje wiele wyzwalaczy i powiązań dostępnych do użycia z aplikacją funkcji. Pełną listę wyzwalaczy i powiązań [można znaleźć tutaj](functions-triggers-bindings.md#supported-bindings).

Wszystkie wyzwalacze i powiązania są reprezentowane w kodzie jako kilka rzeczywistych typów danych:

* Elemencie
* ciąg
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Pierwsze pięć typów na tej liście są standardowymi typami .NET. Ostatnie dwa są używane tylko przez [wyzwalacz HttpTrigger](#http-triggers-and-bindings).

Każdy parametr powiązania w funkcjach musi być jednym z tych typów.

### <a name="http-triggers-and-bindings"></a>Wyzwalacze i powiązania HTTP

Wyzwalacze HTTP i webhook oraz powiązania wyjściowe HTTP używają obiektów Request i Response do reprezentowania komunikatów HTTP.

#### <a name="request-object"></a>Obiekt żądania

Obiekt żądania, który jest przesyłany do skryptu, jest typu `HttpRequestContext`, który ma następujące właściwości:

| Właściwość  | Opis                                                    | Typ                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Obiekt, który zawiera treść żądania. `Body` jest serializowany do najlepszego typu na podstawie danych. Na przykład, jeśli dane są w formacie JSON, są one przenoszone jako tablica skrótów. Jeśli dane są ciągami, są one przenoszone jako ciąg. | obiekt |
| **`Headers`** | Słownik zawierający nagłówki żądania.                | < Słownika, ciąg ><sup>*</sup> |
| **`Method`** | Metoda HTTP żądania.                                | ciąg                    |
| **`Params`**  | Obiekt, który zawiera parametry routingu żądania. | < Słownika, ciąg ><sup>*</sup> |
| **`Query`** | Obiekt, który zawiera parametry zapytania.                  | < Słownika, ciąg ><sup>*</sup> |
| **`Url`** | Adres URL żądania.                                        | ciąg                    |

<sup>*</sup> W przypadku wszystkich kluczy `Dictionary<string,string>` nie jest rozróżniana wielkość liter.

#### <a name="response-object"></a>Obiekt odpowiedzi

Obiekt odpowiedzi, który ma zostać wysłany z tyłu, ma typ `HttpResponseContext`, który ma następujące właściwości:

| Właściwość      | Opis                                                 | Typ                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Obiekt, który zawiera treść odpowiedzi.           | obiekt                    |
| **`ContentType`** | Krótkie ustawienie typu zawartości odpowiedzi. | ciąg                    |
| **`Headers`** | Obiekt, który zawiera nagłówki odpowiedzi.               | Słownik lub Hashtable   |
| **`StatusCode`**  | Kod stanu HTTP odpowiedzi.                       | ciąg lub int             |

#### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi

Podczas pracy z wyzwalaczami HTTP możesz uzyskać dostęp do żądania HTTP w taki sam sposób, jak w przypadku dowolnego innego powiązania danych wejściowych. Znajduje się w bloku `param`.

Użyj obiektu `HttpResponseContext`, aby zwrócić odpowiedź, jak pokazano na poniższej liście:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Wynikiem wywołania tej funkcji będzie:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Rzutowanie typu dla wyzwalaczy i powiązań

W przypadku niektórych powiązań, takich jak powiązanie obiektu BLOB, można określić typ parametru.

Na przykład aby dane z magazynu obiektów BLOB były dostarczane jako ciąg, Dodaj następujące rzutowanie typu do bloku my `param`:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil programu PowerShell

W programie PowerShell istnieje koncepcja profilu programu PowerShell. Jeśli nie znasz profilów programu PowerShell, zobacz [Informacje o profilach](/powershell/module/microsoft.powershell.core/about/about_profiles).

W funkcjach programu PowerShell skrypt profilu jest wykonywany, gdy uruchamiana jest aplikacja funkcji. Aplikacje funkcji są uruchamiane po pierwszym wdrożeniu i po przejściu w stan bezczynności ([zimny start](#cold-start)).

Po utworzeniu aplikacji funkcji przy użyciu narzędzi, takich jak Visual Studio Code i Azure Functions Core Tools, zostanie utworzony domyślny `profile.ps1`. Profil domyślny jest przechowywany [w repozytorium usługi podstawowe narzędzia usługi GitHub](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) i zawiera następujące funkcje:

* Automatyczne uwierzytelnianie MSI na platformie Azure.
* Możliwość włączenia Azure PowerShell `AzureRM` aliasów programu PowerShell, jeśli chcesz.

## <a name="powershell-version"></a>Wersja programu PowerShell

W poniższej tabeli przedstawiono wersję programu PowerShell używaną przez każdą główną wersję środowiska uruchomieniowego funkcji:

| Wersja funkcji | Wersja programu PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5,1 (zablokowany przez środowisko uruchomieniowe) |
| 2.x               | Program PowerShell Core 6                              |

Bieżącą wersję można zobaczyć, drukując `$PSVersionTable` z dowolnej funkcji.

## <a name="dependency-management"></a>Zarządzanie zależnościami

Funkcje umożliwiają używanie [Galerii programu PowerShell](https://www.powershellgallery.com) do zarządzania zależnościami. W przypadku włączenia zarządzania zależnością plik Requirements. psd1 służy do automatycznego pobierania wymaganych modułów. Aby włączyć to zachowanie, należy ustawić właściwość `managedDependency` na `true` w katalogu głównym [pliku host. JSON](functions-host-json.md), jak w poniższym przykładzie:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Podczas tworzenia nowego projektu funkcji programu PowerShell zarządzanie zależnościami jest domyślnie włączone z uwzględnieniem [modułu Azure`Az`](/powershell/azure/new-azureps-module-az) . Maksymalna obsługiwana liczba modułów to 10. Obsługiwana składnia jest _`MajorNumber`_ `.*` lub dokładnej wersji modułu, jak pokazano w następujących wymaganiach. psd1 przykład:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Po zaktualizowaniu pliku Requirements. psd1 zaktualizowane moduły zostaną zainstalowane po ponownym uruchomieniu.

> [!NOTE]
> Zarządzane zależności wymagają dostępu do www.powershellgallery.com do pobierania modułów. W przypadku uruchamiania lokalnego upewnij się, że środowisko uruchomieniowe ma dostęp do tego adresu URL, dodając wszystkie wymagane reguły zapory. 

Następujące ustawienia aplikacji mogą służyć do zmiany sposobu pobierania i instalowania zarządzanych zależności. Uaktualnienie aplikacji rozpocznie się w `MDMaxBackgroundUpgradePeriod`, a proces uaktualniania kończy się w przybliżeniu `MDNewSnapshotCheckPeriod`.

| Ustawienie aplikacja funkcji              | Wartość domyślna             | Opis                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 dni)     | Każdy proces roboczy programu PowerShell inicjuje sprawdzanie uaktualnień modułu na Galeria programu PowerShell podczas uruchamiania procesu i każdego `MDMaxBackgroundUpgradePeriod` po tym. Gdy w Galeria programu PowerShell jest dostępna nowa wersja modułu, zostanie ona zainstalowana w systemie plików i udostępniona dla procesów roboczych programu PowerShell. Zmniejszenie tej wartości pozwala aplikacji funkcji uzyskiwać nowsze wersje modułów wcześniej, ale również zwiększa użycie zasobów aplikacji (we/wy sieci, procesor CPU, magazyn). Zwiększenie tej wartości zmniejsza użycie zasobów aplikacji, ale może również opóźnić dostarczenie nowej wersji modułu do aplikacji. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 godzina)       | Po zainstalowaniu nowych wersji modułu w systemie plików należy ponownie uruchomić każdy proces roboczy programu PowerShell. Ponowne uruchomienie programu PowerShell wpływa na dostępność aplikacji, ponieważ może przerwać bieżące wykonywanie funkcji. Do momentu ponownego uruchomienia wszystkich procesów roboczych programu PowerShell wywołania funkcji mogą używać starych lub nowych wersji modułu. Ponowne uruchamianie wszystkich procesów roboczych programu PowerShell kończy się w `MDNewSnapshotCheckPeriod`. Zwiększenie tej wartości zmniejsza częstotliwość przerw, ale może również wydłużyć czas, w którym wywołania funkcji używają starej lub nowej wersji modułu w sposób Niedeterministyczny. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 dzień)     | Aby uniknąć nadmiernych uaktualnień modułów podczas częstego ponownego uruchamiania procesów roboczych, sprawdzanie uaktualnień modułów nie jest wykonywane po zainicjowaniu jakiegokolwiek procesu roboczego, który zaewidencjonuje ostatni `MDMinBackgroundUpgradePeriod`. |

Korzystanie z własnych modułów niestandardowych jest nieco inne niż w normalny sposób.

Na komputerze lokalnym moduł jest instalowany w jednym z dostępnych globalnie folderów w `$env:PSModulePath`. W przypadku uruchamiania na platformie Azure nie masz dostępu do modułów zainstalowanych na maszynie. Oznacza to, że `$env:PSModulePath` aplikacji funkcji programu PowerShell różni się od `$env:PSModulePath` w zwykłych skryptach programu PowerShell.

W funkcjach `PSModulePath` zawiera dwie ścieżki:

* Folder `Modules`, który istnieje w katalogu głównym aplikacji funkcji.
* Ścieżka do folderu `Modules`, który jest kontrolowany przez proces roboczy języka programu PowerShell.

### <a name="function-app-level-modules-folder"></a>Folder `Modules` na poziomie aplikacji funkcji

Aby korzystać z modułów niestandardowych, można umieścić moduły, na których funkcje są zależne od folderu `Modules`. Z tego folderu moduły są automatycznie dostępne dla środowiska uruchomieniowego usługi Functions. Każda funkcja w aplikacji funkcji może korzystać z tych modułów. 

> [!NOTE]
> Moduły określone w pliku Requirements. psd1 są automatycznie pobierane i uwzględniane w ścieżce, więc nie trzeba ich dołączać do folderu modules. Są one przechowywane lokalnie w folderze `$env:LOCALAPPDATA/AzureFunctions` i w folderze `/data/ManagedDependencies` w przypadku uruchamiania w chmurze.

Aby skorzystać z funkcji niestandardowego modułu, Utwórz folder `Modules` w katalogu głównym aplikacji funkcji. Skopiuj moduły, których chcesz używać w swoich funkcjach, do tej lokalizacji.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

W przypadku folderu `Modules` aplikacja funkcji powinna mieć następującą strukturę folderów:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Po uruchomieniu aplikacji funkcji proces roboczy języka PowerShell dodaje ten folder `Modules` do `$env:PSModulePath`, dzięki czemu można polegać na autoładowaniu modułu tak samo jak w przypadku zwykłego skryptu programu PowerShell.

### <a name="language-worker-level-modules-folder"></a>Folder `Modules` poziomów procesów roboczych języka

Proces roboczy języka programu PowerShell często korzysta z kilku modułów. Te moduły są zdefiniowane w ostatniej pozycji `PSModulePath`. 

Bieżąca lista modułów jest następująca:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): moduł używany do pracy z archiwami, takimi jak `.zip`, `.nupkg`i inne.
* **ThreadJob**: implementacja oparta na wątkach interfejsów API zadań programu PowerShell.

Domyślnie funkcje używają najnowszej wersji tych modułów. Aby użyć określonej wersji modułu, należy umieścić tę określoną wersję w folderze `Modules` aplikacji funkcji.

## <a name="environment-variables"></a>Zmienne środowiskowe

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień przy użyciu `$env:NAME_OF_ENV_VAR`, jak pokazano w następującym przykładzie:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

W przypadku uruchamiania lokalnego ustawienia aplikacji są odczytywane z pliku projektu [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="concurrency"></a>Współbieżność

Domyślnie środowisko uruchomieniowe programu PowerShell może przetwarzać tylko jedno wywołanie funkcji w danym momencie. Jednak ten poziom współbieżności może być niewystarczający w następujących sytuacjach:

* Gdy próbujesz obsłużyć dużą liczbę wywołań w tym samym czasie.
* Gdy masz funkcje, które wywołują inne funkcje w tej samej aplikacji funkcji.

Możesz zmienić to zachowanie, ustawiając następującą zmienną środowiskową na wartość całkowitą:

```
PSWorkerInProcConcurrencyUpperBound
```

Ta zmienna środowiskowa jest ustawiana w [ustawieniach aplikacji](functions-app-settings.md) aplikacja funkcji.

### <a name="considerations-for-using-concurrency"></a>Zagadnienia dotyczące korzystania z współbieżności

Domyślnie program PowerShell jest _wielowątkowym_ językiem skryptowym. Współbieżność można jednak dodać przy użyciu wielu obszarami działania programu PowerShell w tym samym procesie. Utworzona ilość obszarami działania będzie zgodna z ustawieniem aplikacji PSWorkerInProcConcurrencyUpperBound. Przepustowość będzie miała wpływ na ilość dostępnego procesora i pamięci w wybranym planie.

Azure PowerShell używa niektórych kontekstów i Stanów na _poziomie procesu_ , aby pomóc zaoszczędzić przed nadmiarowym wpisywaniem. Jeśli jednak włączysz współbieżność w aplikacji funkcji i wywołująsz akcje, które zmieniają stan, możesz zakończyć z użyciem warunków wyścigu. Te sytuacje wyścigu są trudne do debugowania, ponieważ jedno wywołanie jest zależne od pewnego stanu, a inne wywołanie zmieniło stan.

Ogromną wartość współbieżności z Azure PowerShell, ponieważ niektóre operacje mogą zająć dużo czasu. Należy jednak zachować ostrożność. Jeśli podejrzewasz, że masz sytuację wyścigu, ustaw dla ustawienia aplikacji PSWorkerInProcConcurrencyUpperBound wartość `1` i zamiast tego użyj [izolacji poziomu procesu roboczego języka](functions-app-settings.md#functions_worker_process_count) dla współbieżności.

## <a name="configure-function-scriptfile"></a>Konfiguruj `scriptFile` funkcji

Domyślnie funkcja programu PowerShell jest wykonywana z `run.ps1`, plik, który współużytkuje ten sam katalog nadrzędny co odpowiadający mu `function.json`.

Właściwość `scriptFile` w `function.json` może służyć do uzyskania struktury folderów, która wygląda podobnie do poniższego przykładu:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

W takim przypadku `function.json` dla `myFunction` zawiera właściwość `scriptFile` odwołującą się do pliku z wyeksportowaną funkcją do uruchomienia.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Używanie modułów programu PowerShell przez skonfigurowanie punktu wejścia

W tym artykule przedstawiono funkcje programu PowerShell w domyślnym pliku skryptu `run.ps1` wygenerowanym przez szablony.
Można jednak również dołączać funkcje w modułach programu PowerShell. Można odwołać się do określonego kodu funkcji w module przy użyciu pól `scriptFile` i `entryPoint` w pliku konfiguracyjnym Function. JSON.

W tym przypadku `entryPoint` jest nazwą funkcji lub polecenia cmdlet w module programu PowerShell, do którego odwołuje się `scriptFile`.

Weź pod uwagę następującą strukturę folderów:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Gdzie `PSFunction.psm1` zawiera:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

W tym przykładzie konfiguracja dla `myFunction` zawiera właściwość `scriptFile`, która odwołuje się do `PSFunction.psm1`, który jest modułem programu PowerShell w innym folderze.  Właściwość `entryPoint` odwołuje się do funkcji `Invoke-PSTestFunc`, która jest punktem wejścia w module.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

W przypadku tej konfiguracji `Invoke-PSTestFunc` są wykonywane dokładnie jako `run.ps1`.

## <a name="considerations-for-powershell-functions"></a>Zagadnienia dotyczące funkcji programu PowerShell

Podczas pracy z funkcjami programu PowerShell należy pamiętać o zagadnieniach w poniższych sekcjach.

### <a name="cold-start"></a>Zimne uruchomienie

Podczas opracowywania Azure Functions w [modelu hostingu bezserwerowego](functions-scale.md#consumption-plan)zimny start jest rzeczywistości. *Zimny start* odnosi się do okresu czasu potrzebnego na uruchomienie aplikacji funkcji w celu przetworzenia żądania. Zimny przebieg występuje częściej od planu zużycia, ponieważ aplikacja funkcji jest zamykana w okresach braku aktywności.

### <a name="bundle-modules-instead-of-using-install-module"></a>Moduły pakietu zamiast używania `Install-Module`

Skrypt jest uruchamiany na każdym wywołaniu. Unikaj używania `Install-Module` w skrypcie. Zamiast tego należy użyć `Save-Module` przed opublikowaniem, aby funkcja nie mogła tracić czasu na pobranie modułu. Jeśli zimne uruchomienie ma wpływ na funkcje, warto rozważyć wdrożenie aplikacji funkcji w planie [App servicem](functions-scale.md#app-service-plan) ustawionym na *zawsze włączony* lub do [planu Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)

[Dokumentacja pliku host. JSON]: functions-host-json.md
