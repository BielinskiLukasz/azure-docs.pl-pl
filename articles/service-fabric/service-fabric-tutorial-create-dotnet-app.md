---
title: Tworzenie aplikacji platformy .NET dla usługi Service Fabric | Microsoft Docs
description: W tym samouczku znajdziesz informacje o sposobie tworzenia aplikacji za pomocą frontonu platformy ASP.NET Core i niezawodnej stanowej usługi zaplecza, a także o sposobie wdrażania aplikacji w klastrze.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: a1197277b97c14e95bdab67f7c3d00b75a841f22
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267578"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Samouczek: tworzenie i wdrażanie aplikacji przy użyciu usługi frontonu platformy ASP.NET Core z internetowym interfejsem API oraz stanowej usługi zaplecza
Niniejszy samouczek jest pierwszą częścią serii.  Zostanie tutaj przedstawiony sposób tworzenia aplikacji usługi Azure Service Fabric za pomocą frontonu internetowego interfejsu API platformy ASP.NET Core i stanowej usługi zaplecza umożliwiającej przechowywanie danych. Po zakończeniu będziesz mieć aplikację do głosowania z usługą internetową frontonu ASP.NET Core, która zapisuje wyniki głosowania w stanowej usłudze zaplecza w klastrze. Jeśli nie chcesz ręcznie tworzyć aplikacji do głosowania, możesz [pobrać kod źródłowy](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) ukończonej aplikacji i przejść od razu do sekcji [Szczegółowe omówienie przykładowej aplikacji do głosowania](#walkthrough_anchor).  Jeśli wolisz, możesz też obejrzeć [przewodnik wideo](https://channel9.msdn.com/Events/Connect/2017/E100) dla tego samouczka.

![Diagram aplikacji](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi internetowego interfejsu API platformy ASP.NET Core jako niezawodnej stanowej usługi
> * Tworzenie usługi aplikacji internetowej platformy ASP.NET Core jako bezstanowej usługi internetowej
> * Używanie zwrotnego serwera proxy do komunikacji z usługą stanową

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji platformy .NET w usłudze Service Fabric
> * [Wdrażanie aplikacji w klastrze zdalnym](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Dodawanie punktu końcowego HTTPS do usługi frontonu platformy ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurowanie procesu CI/CD za pomocą usługi Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka:
- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zainstaluj program Visual Studio 2017](https://www.visualstudio.com/) w wersji 15.5 lub nowszej z obciążeniami **Programowanie na platformie Azure** i **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.
- [Zainstaluj zestaw SDK usługi Service Fabric.](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Tworzenie usługi internetowego interfejsu API platformy ASP.NET jako niezawodnej usługi
Najpierw utwórz fronton internetowy aplikacji do głosowania za pomocą platformy ASP.NET Core. ASP.NET Core to uproszczone międzyplatformowe środowisko programistyczne, którego można używać do tworzenia nowoczesnych internetowych interfejsów użytkownika i interfejsów API. Aby w pełni zrozumieć, w jaki sposób platforma ASP.NET Core integruje się z usługą Service Fabric, zdecydowanie zalecamy zapoznanie się z artykułem [ASP.NET Core in Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) (Platforma ASP.NET Core w niezawodnych usługach Service Fabric). Na tym etapie wystarczy wykonać czynności opisane w tym samouczku, aby szybko rozpocząć pracę. Aby uzyskać więcej informacji na temat platformy ASP.NET Core, przejrzyj [dokumentację platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

1. Uruchom program Visual Studio jako **administrator**.

2. Utwórz projekt, korzystając z opcji **Plik**->**Nowy**->**Projekt**.

3. W oknie dialogowym **Nowy projekt** wybierz kolejno pozycje **Chmura > Aplikacja usługi Service Fabric**.

4. Nazwij aplikację **Voting** i naciśnij przycisk **OK**.

   ![Okno dialogowe nowego projektu w programie Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Na stronie **Nowa usługa Service Fabric** wybierz pozycję **Bezstanowa usługa programu ASP.NET Core** i nadaj swojej usłudze nazwę **VotingWeb**.
   
   ![Wybieranie usługi internetowej programu ASP.NET w oknie dialogowym nowej usługi](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Na następnej stronie znajduje się zestaw szablonów projektów programu ASP.NET Core. Na potrzeby tego samouczka wybierz pozycję **Aplikacja internetowa (Model-View-Controller)**. 
   
   ![Wybieranie typu projektu programu ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Program Visual Studio utworzy projekt aplikacji i projekt usługi, a następnie wyświetli je w Eksploratorze rozwiązań.

   ![Eksplorator rozwiązań po utworzeniu aplikacji z usługą internetowego interfejsu API platformy ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Dodawanie modułu AngularJS do usługi VotingWeb
Dodaj moduł [AngularJS](http://angularjs.org/) do swojej usługi przy użyciu [programu Bower](/aspnet/core/client-side/bower). Najpierw do projektu dodaj plik ustawień *.bowerrc*.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy usługę **VotingWeb** i wybierz polecenie **Dodaj > Nowy element**. Wybierz pozycję **C#**, a następnie pozycję **plik JSON**.  Wprowadź wartość **.bowerrc** w polu *Nazwa*, a następnie kliknij przycisk **Dodaj**.

Otwórz plik *.bowerrc* i zastąp zawartość poniższą zawartością, która wskazuje, że program Bower zainstaluje zasoby pakietu do katalogu *wwwroot/lib*.

```json
{
 "directory": "wwwroot/lib"
}
```

Zapisz zmiany w pliku *.bowerrc*.  Spowoduje to utworzenie pliku *.bowerrc* w projekcie.  

Następnie do projektu dodaj plik konfiguracji programu Bower.  W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy usługę **VotingWeb** i wybierz polecenie **Dodaj > Nowy element**. Wybierz pozycję **C#**, a następnie pozycję **plik JSON**.  Wprowadź wartość **bower.json** w polu *Nazwa*, a następnie kliknij przycisk **Dodaj**.

Otwórz plik *bower.json* i zastąp zawartość poniższymi wpisami dla parametrów angular i angular-bootstrap, a następnie zapisz zmiany.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "3.2.1",
    "jquery-validation": "1.16.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.8",
    "angular-bootstrap": "v1.1.0"
  }
}
```

Po zapisaniu pliku *bower.json* obsługa aplikacji Bower w programie Visual Studio zainstaluje platformę Angular w folderze *wwwroot/lib* projektu. Ponadto będzie on też widoczny w folderze *Dependencies/Bower*.

### <a name="update-the-sitejs-file"></a>Aktualizowanie pliku site.js
Otwórz plik *wwwroot/js/site.js*.  Zastąp jego zawartość kodem JavaScript używanym przez widoki z folderu Home:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Aktualizowanie pliku Index.cshtml
Otwórz plik *Views/Home/Index.cshtml* dotyczący kontrolera głównego.  Zastąp zawartość tego pliku następującym kodem i zapisz zmiany.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Aktualizowanie pliku _Layout.cshtml
Otwórz plik *Views/Shared/_Layout.cshtml*, który jest widokiem domyślnym aplikacji ASP.NET.  Zastąp zawartość tego pliku następującym kodem i zapisz zmiany.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="~/lib/angular/angular.js"></script>
<script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Aktualizowanie pliku VotingWeb.cs
Otwórz plik *VotingWeb.cs*, który tworzy obiekt WebHost platformy ASP.NET Core wewnątrz usługi bezstanowej przy użyciu serwera internetowego WebListener.  

Na początku tego pliku dodaj dyrektywę `using System.Net.Http;`.  

Zastąp funkcję `CreateServiceInstanceListeners()` następującym kodem i zapisz zmiany.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Dodaj również metodę `GetVotingDataServiceName`, która zwraca nazwę usługi podczas sondowania:

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Dodawanie pliku VotesController.cs
Dodaj kontroler, który definiuje akcje głosowania. Kliknij prawym przyciskiem myszy folder **Controllers**, a następnie wybierz kolejno pozycje **Dodaj->Nowy element->Klasa**.  Nadaj plikowi nazwę „VotesController.cs” i kliknij przycisk **Dodaj**.  

Zastąp zawartość tego pliku następującym kodem i zapisz zmiany.  Dalej, w sekcji [Aktualizowanie pliku VotesController.cs](#updatevotecontroller_anchor) ten plik zostanie zmodyfikowany na potrzeby odczytywania danych głosowania z usługi zaplecza i ich zapisywania.  Na razie ten kontroler zwraca do widoku dane w postaci statycznego ciągu.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Konfigurowanie portu nasłuchującego
Po utworzeniu usługi frontonu VotingWeb program Visual Studio losowo wybiera port, na którym usługa będzie nasłuchiwała.  Usługa VotingWeb działa jako fronton dla tej aplikacji i akceptuje ruch zewnętrzny, dlatego warto powiązać ją z ustalonym i dobrze znanym portem.  [Manifest usługi](service-fabric-application-and-service-manifests.md) deklaruje punkty końcowe usługi. W Eksploratorze rozwiązań otwórz plik *VotingWeb/PackageRoot/ServiceManifest.xml*.  W sekcji **Resources** znajdź zasób **Endpoint** i zmień wartość elementu **Port** na 80 lub inny numer portu. Aby wdrożyć i uruchomić aplikację lokalnie, port nasłuchujący aplikacji musi być otwarty i dostępny na komputerze.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Zaktualizuj również właściwość Adres URL aplikacji w projekcie Voting, aby w przeglądarce internetowej został otwarty właściwy port podczas debugowania za pomocą klawisza F5.  W Eksploratorze rozwiązań wybierz projekt **Voting** i zaktualizuj właściwość **Adres URL aplikacji**.

![Adres URL aplikacji](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

### <a name="deploy-and-run-the-application-locally"></a>Wdrażanie i uruchamianie aplikacji lokalnie
Teraz możesz przystąpić do uruchomienia aplikacji. W programie Visual Studio naciśnij klawisz `F5`, aby wdrożyć aplikację do debugowania. Naciśnięcie klawisza `F5` nie spowoduje oczekiwanych rezultatów, jeśli program Visual Studio nie został wcześniej otwarty jako **administrator**.

> [!NOTE]
> Przy pierwszym uruchamianiu i wdrażaniu aplikacji w środowisku lokalnym program Visual Studio tworzy lokalny klaster na potrzeby debugowania.  Tworzenie klastra może trochę potrwać. Stan tworzenia klastra jest wyświetlany w oknie danych wyjściowych programu Visual Studio.

Na tym etapie Twoja aplikacja internetowa powinna wyglądać następująco:

![Fronton platformy ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Aby zatrzymać debugowanie aplikacji, wróć do programu Visual Studio i naciśnij klawisze **Shift+F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Dodawanie stanowej usługi zaplecza do aplikacji
Teraz, gdy usługa internetowego interfejsu API platformy ASP.NET działa w aplikacji, przejdź dalej i dodaj niezawodną usługę stanową na potrzeby przechowywania danych w aplikacji.

Usługa Service Fabric umożliwia spójne i niezawodne przechowywanie danych bezpośrednio w usłudze przy użyciu niezawodnych kolekcji. Niezawodne kolekcje to zestaw niezawodnych klas kolekcji wysokiej dostępności, które są znane wszystkim, którzy korzystali z kolekcji języka C#.

W tym samouczku utworzysz usługę, która w niezawodnej kolekcji przechowuje wartość licznika.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy węzeł **Usługi** w projekcie aplikacji i wybierz pozycję **Dodaj > Nowa usługa Service Fabric**.
    
2. W oknie dialogowym **Nowa usługa Service Fabric** wybierz usługę **Stateful ASP.NET Core**, nadaj jej nazwę **VotingData** i naciśnij przycisk **OK**.

    ![Okno dialogowe nowej usługi w programie Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Po utworzeniu projektu usługi w aplikacji będą dostępne dwie usługi. W miarę postępu tworzenia aplikacji możesz w ten sam sposób dodać więcej usług. Każda z nich może być niezależnie wersjonowana i uaktualniana.

3. Na następnej stronie znajduje się zestaw szablonów projektów programu ASP.NET Core. Na potrzeby tego samouczka wybierz szablon **Internetowy interfejs API**.

    ![Wybieranie typu projektu programu ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Program Visual Studio utworzy projekt usługi, a następnie wyświetli go w Eksploratorze rozwiązań.

    ![Eksplorator rozwiązań](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Dodawanie pliku VoteDataController.cs

W projekcie **VotingData** kliknij prawym przyciskiem myszy folder **Controllers**, a następnie wybierz kolejno pozycje **Dodaj->Nowy element->Klasa**. Nadaj plikowi nazwę „VoteDataController.cs” i kliknij przycisk **Dodaj**. Zastąp zawartość tego pliku następującym kodem i zapisz zmiany.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>Łączenie usług
W następnym kroku połączysz te dwie usługi i sprawisz, że aplikacja internetowa frontonu będzie pobierała z usługi zaplecza informacje o głosowaniu i ustawiała je.

Usługa Service Fabric zapewnia pełną elastyczność w sposobie komunikowania się z niezawodnymi usługami. W obrębie pojedynczej aplikacji mogą istnieć usługi, które są dostępne za pośrednictwem protokołu TCP. Inne usługi mogą być dostępne za pośrednictwem interfejsu API REST protokołu HTTP, a jeszcze inne za pośrednictwem gniazd internetowych. Aby zapoznać się z opisem dostępnych opcji i zastosowanych technologii, zobacz [Communicating with services](service-fabric-connect-and-communicate-with-services.md) (Komunikacja z usługami).

W tym samouczku użyj [internetowego interfejsu API platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) oraz [zwrotnego serwera proxy platformy Service Fabric](service-fabric-reverseproxy.md), aby usługa frontonu sieci Web mogła komunikować się z usługą danych zaplecza. Zwrotny serwer proxy jest zazwyczaj skonfigurowany do używania portu 19081. Port jest ustawiany w szablonie ARM używanym do konfigurowania klastra. Aby sprawdzić, który port jest używany, należy poszukać go w szablonie klastra w zasobie **Microsoft.ServiceFabric/clusters**:

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Aktualizowanie pliku VotesController.cs
W projekcie **VotingWeb** otwórz plik *Controllers/VotesController.cs*.  Zastąp zawartość definicji klasy `VotesController` następującym kodem i zapisz zmiany.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Szczegółowe omówienie przykładowej aplikacji do głosowania
Aplikacja do głosowania składa się z dwóch usług:
- Usługa internetowa frontonu (VotingWeb) — usługa internetowa frontonu platformy ASP.NET Core, obsługująca stronę internetową i ujawniająca interfejsy API sieci Web na potrzeby komunikacji z usługą zaplecza.
- Usługi zaplecza (VotingData) — usługa internetowa platformy ASP.NET Core, która uwidacznia interfejs API do przechowywania wyników głosowania w niezawodnym słowniku utrwalonym na dysku.

![Diagram aplikacji](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Podczas głosowania w aplikacji występują następujące zdarzenia:
1. Plik JavaScript wysyła żądanie głosowania do internetowego interfejsu API w usłudze internetowej frontonu jako żądanie HTTP PUT.

2. Usługa internetowa frontonu używa serwera proxy w celu zlokalizowania i przesłania żądania HTTP PUT do usługi zaplecza.

3. Usługa zaplecza przyjmuje żądanie przychodzące i przechowuje zaktualizowany wynik w niezawodnym słowniku, który jest replikowany do wielu węzłów w klastrze i utrwalany na dysku. Wszystkie dane aplikacji są przechowywane w klastrze, więc baza danych nie jest wymagana.

## <a name="debug-in-visual-studio"></a>Debugowanie w programie Visual Studio
Podczas debugowania aplikacji w programie Visual Studio używany jest lokalny klaster projektowy usługi Service Fabric. Możesz opcjonalnie dostosować środowisko debugowania do danego scenariusza. W tej aplikacji dane są przechowywane w usłudze zaplecza przy użyciu niezawodnego słownika. Program Visual Studio domyślnie usuwa aplikację po zatrzymaniu debugera. Usunięcie aplikacji spowoduje, że dane w usłudze zaplecza także zostaną usunięte. Aby zachować dane między sesjami debugowania, możesz zmienić **Tryb debugowania aplikacji** jako właściwość w projekcie **Voting (Głosowanie)** w programie Visual Studio.

Aby zobaczyć, co się stanie w kodzie, wykonaj następujące kroki:
1. Otwórz plik **VotesController.cs** i ustaw punkt przerwania w metodzie **Put** internetowego interfejsu API (wiersz 63) — możesz wyszukać ten plik w Eksploratorze rozwiązań w programie Visual Studio.

2. Otwórz plik **VoteDataController.cs** i ustaw punkt przerwania w metodzie **Put** tego internetowego interfejsu API (wiersz 53).

3. Wróć do przeglądarki i kliknij opcję głosowania lub dodaj nową opcję głosowania. Zostanie trafiony pierwszy punkt przerwania w kontrolerze interfejsu API frontonu internetowego.
    
    1. Jest to punkt, w którym skrypt JavaScript w przeglądarce wysyła żądanie do kontrolera internetowego interfejsu API w usłudze frontonu.
    
    ![Dodawanie usługi frontonu Vote (Głosowanie)](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Najpierw skonstruuj adres URL do elementu ReverseProxy dla usługi zaplecza **(1)**.
    3. Następnie wyślij żądanie HTTP PUT do elementu ReverseProxy **(2)**.
    4. Na koniec zwróć odpowiedź z usługi zaplecza do klienta **(3)**.

4. Naciśnij klawisz **F5**, aby kontynuować
    1. Jesteś teraz w punkcie przerwania w usłudze zaplecza.
    
    ![Dodawanie usługi zaplecza Vote](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. W pierwszym wierszu metody **(1)** użyj elementu `StateManager` do pobrania lub dodania niezawodnego słownika o nazwie `counts`.
    3. Wszystkie interakcje z wartościami w niezawodnym słowniku wymagają transakcji — ta instrukcja using **(2)** tworzy tę transakcję.
    4. W transakcji zaktualizuj wartość odpowiedniego klucza dla opcji głosowania i zatwierdź operację **(3)**. Po powrocie z metody zatwierdzania dane są aktualizowane w słowniku i replikowane do innych węzłów w klastrze. Dane są bezpiecznie przechowywane w klastrze, a usługa zaplecza może zostać przełączona w tryb failover do innych węzłów, które nadal mają dostępne dane.
5. Naciśnij klawisz **F5**, aby kontynuować

Aby zatrzymać sesję debugowania, naciśnij klawisze **Shift+F5**.


## <a name="next-steps"></a>Następne kroki
W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi internetowego interfejsu API platformy ASP.NET Core jako niezawodnej stanowej usługi
> * Tworzenie usługi aplikacji internetowej platformy ASP.NET Core jako bezstanowej usługi internetowej
> * Używanie zwrotnego serwera proxy do komunikacji z usługą stanową

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Wdrażanie aplikacji na platformie Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
