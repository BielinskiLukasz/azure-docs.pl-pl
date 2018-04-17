---
title: Tworzenie aplikacji internetowej środowiska Node.js i usługi MongoDB na platformie Azure | Microsoft Docs
description: Dowiedz się, jak uruchomić aplikację środowiska Node.js działającą na platformie Azure z użyciem połączenia z bazą danych usługi Cosmos DB i parametrów połączenia usługi MongoDB.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8fdad8d8e62365c33b47e67b483c929aaab0083e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-build-a-nodejs-and-mongodb-web-app-in-azure"></a>Samouczek: tworzenie aplikacji internetowej środowiska Node.js i usługi MongoDB na platformie Azure

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć aplikację w usłudze App Service w systemie _Linux_, zobacz [Build a Node.js and MongoDB web app in Azure App Service on Linux (Tworzenie aplikacji internetowej środowiska Node.js i usługi MongoDB w usłudze Azure App Service w systemie Linux)](./containers/tutorial-nodejs-mongodb-app.md).
>

Usługa Azure Web Apps oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. W tym samouczku pokazano, jak utworzyć aplikację internetową środowiska Node.js i połączyć ją z bazą danych MongoDB. Po zakończeniu aplikacja MEAN (MongoDB, Express, AngularJS i Node.js) będzie działać w usłudze [Azure App Service](app-service-web-overview.md). Dla uproszczenia przykładowa aplikacja używa [platformy internetowej MEAN.js](http://meanjs.org/).

![Aplikacja MEAN.js uruchomiona w usłudze Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Zawartość:

> [!div class="checklist"]
> * Tworzenie bazy danych MongoDB na platformie Azure
> * Łączenie aplikacji Node.js z usługą MongoDB
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
1. [Zainstaluj środowisko Node.js i menedżer NPM](https://nodejs.org/)
1. [Zainstaluj program Bower](https://bower.io/) (wymagany przez środowisko [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Zainstaluj środowisko Gulp.js](http://gulpjs.com/) (wymagane przez środowisko [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Zainstaluj i uruchom usługę MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 

## <a name="test-local-mongodb"></a>Testowanie lokalnej usługi MongoDB

Otwórz okno terminala i za pomocą polecenia `cd` przejdź do katalogu `bin` instalacji usługi MongoDB. W tym oknie terminala możesz uruchamiać wszystkie polecenia z tego samouczka.

Uruchom w terminalu polecenie `mongo`, aby nawiązać połączenie z lokalnym serwerem usługi MongoDB.

```bash
mongo
```

Jeśli połączenie zostanie pomyślnie nawiązane, baza danych MongoDB została już uruchomiona. Jeśli nie, upewnij się, że lokalna baza danych MongoDB została uruchomiona, postępując zgodnie z procedurą opisaną w artykule [Install MongoDB Community Edition (Instalowanie usługi MongoDB Community Edition)](https://docs.mongodb.com/manual/administration/install-community/). Często się zdarza, że usługa MongoDB jest zainstalowana, ale trzeba ją jeszcze uruchomić za pomocą polecenia `mongod`. 

Gdy skończysz testowanie bazy danych MongoDB, naciśnij w terminalu klawisze `Ctrl+C`. 

## <a name="create-local-nodejs-app"></a>Tworzenie lokalnej aplikacji Node.js

W tym kroku skonfigurujesz lokalny projekt Node.js.

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W oknie terminalu dodaj element `cd` do katalogu roboczego.  

Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

To przykładowe repozytorium zawiera kopię [repozytorium MEAN.js](https://github.com/meanjs/mean). Zostało ono zmodyfikowane pod kątem uruchamiania w usłudze App Service (aby uzyskać więcej informacji, zobacz [plik README](https://github.com/Azure-Samples/meanjs/blob/master/README.md) repozytorium MEAN.js).

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom następujące polecenia, aby zainstalować wymagane pakiety i uruchomić aplikację.

```bash
cd meanjs
npm install
npm start
```

Po całkowitym załadowaniu aplikacji zostanie wyświetlony komunikat podobny do następującego:

```console
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

W przeglądarce przejdź do adresu `http://localhost:3000`. Kliknij pozycję **Utwórz konto** w górnym menu i utwórz użytkownika testowego. 

Przykładowa aplikacja MEAN.js przechowuje dane użytkowników w bazie danych. Jeśli uda Ci się pomyślnie utworzyć użytkownika i zalogować, to znaczy, że aplikacja zapisuje dane w lokalnej bazie danych MongoDB.

![Pomyślne połączenie MEAN.js z MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Wybierz pozycję **Administrator -> Zarządzaj artykułami**, aby dodać artykuły.

Aby w dowolnym momencie zatrzymać środowisko Node.js, naciśnij w terminalu klawisze `Ctrl+C`. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Tworzenie produkcyjnej bazy danych MongoDB

W tym kroku utworzysz bazę danych MongoDB na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

W przypadku bazy danych MongoDB w tym samouczku jest używana baza danych [Azure Cosmos DB](/azure/documentdb/). Usługa Cosmos DB obsługuje połączenia klienckie usługi MongoDB.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Tworzenie konta usługi Cosmos DB

W usłudze Cloud Shell utwórz konto usługi Cosmos DB za pomocą polecenia [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_create).

W poniższym poleceniu zamień symbol zastępczy *\<cosmosdb_name>* na unikatową nazwę usługi Cosmos DB. Ta nazwa jest używana jako część punktu końcowego usługi Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, więc musi być unikatowa w obrębie wszystkich kont usługi Cosmos DB na platformie Azure. Nazwa może zawierać tylko małe litery, cyfry oraz znak łącznika (-) i musi się składać z 3–50 znaków.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

Parametr *--kind MongoDB* umożliwia tworzenie połączeń klienckich MongoDB.

Po utworzeniu konta usługi Cosmos DB w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Łączenie aplikacji z produkcyjną bazą danych MongoDB

W tym kroku połączysz swoją przykładową aplikację MEAN.js z nowo utworzoną bazą danych Cosmos DB przy użyciu parametrów połączenia MongoDB. 

### <a name="retrieve-the-database-key"></a>Pobieranie klucza bazy danych

Aby nawiązać połączenie z bazą danych Cosmos DB, niezbędny jest klucz bazy danych. W usłudze Cloud Shell pobierz klucz podstawowy przy użyciu polecenia [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_list_keys).

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

W interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następującego przykładu:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Skopiuj wartość `primaryMasterKey`. Ta informacja będzie potrzebna w następnym kroku.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurowanie parametrów połączenia w aplikacji Node.js

W lokalnym repozytorium MEAN.js utwórz w folderze _config/env/_ plik o nazwie _local-production.js_. Domyślnie plik _gitignore_ skonfigurowano w celu przechowywania tego pliku poza repozytorium. 

Skopiuj do niego poniższy kod. Pamiętaj o zastąpieniu dwóch symboli zastępczych *\<cosmosdb_name>* nazwą bazy danych Cosmos DB i zastąpieniu symbolu zastępczego *\<primary_master_key>* kluczem skopiowanym w poprzednim kroku.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

Opcja `ssl=true` jest wymagana, ponieważ [usługa Cosmos DB wymaga protokołu SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Zapisz zmiany.

### <a name="test-the-application-in-production-mode"></a>Testowanie aplikacji w trybie produkcyjnym 

Uruchom poniższe polecenie, aby zminifikować i dołączyć skrypty dla środowiska produkcyjnego. Ten proces generuje pliki wymagane przez środowisko produkcyjne.

```bash
gulp prod
```

Uruchom następujące polecenie, aby użyć parametrów połączenia skonfigurowanych w pliku _config/env/local-production.js_.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

Instrukcja `NODE_ENV=production` ustawia zmienną środowiskową, która informuje środowisko Node.js, aby działało w środowisku produkcyjnym.  Instrukcja `node server.js` uruchamia serwer Node.js przy użyciu pliku `server.js` w katalogu głównym repozytorium. W ten sposób aplikacja Node.js jest ładowana na platformie Azure. 

Po załadowaniu aplikacji upewnij się, że została ona uruchomiona w środowisku produkcyjnym:

```console
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

W przeglądarce przejdź do adresu `http://localhost:8443`. Kliknij pozycję **Utwórz konto** w górnym menu i utwórz użytkownika testowego. Jeśli uda Ci się pomyślnie utworzyć użytkownika i zalogować, to znaczy, że aplikacja zapisuje dane w bazie danych Cosmos DB na platformie Azure. 

W terminalu zatrzymaj środowisko Node.js, naciskając klawisze `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrożysz aplikację Node.js połączoną z bazą danych MongoDB w usłudze Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Konfigurowanie zmiennej środowiskowej

Domyślnie w projekcie MEAN.js plik _config/env/local-production.js_ jest przechowywany poza repozytorium Git. W przypadku aplikacji internetowej platformy Azure parametry połączenia bazy danych MongoDB określa się za pomocą ustawień aplikacji.

Aby określić ustawienia aplikacji, użyj polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) w usłudze Cloud Shell. 

W poniższym przykładzie ustawienie aplikacji `MONGODB_URI` jest konfigurowane w aplikacji internetowej platformy Azure. Zastąp symbole zastępcze *\<app_name>*, *\<cosmosdb_name>* i *\<primary_master_key>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

W kodzie Node.js dostęp do tego ustawienia aplikacji uzyskuje się za pomocą instrukcji `process.env.MONGODB_URI`, podobnie jak w przypadku uzyskiwania dostępu do zmiennej środowiskowej. 

W lokalnym repozytorium MEAN.js otwórz plik _config/env/production.js_ (nie _config/env/local-production.js_), w którym znajduje się konfiguracja specyficzna dla środowiska produkcyjnego. Domyślna aplikacja MEAN.js jest już skonfigurowana do używania utworzonej zmiennej środowiskowej `MONGODB_URI`.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

Możesz zauważyć, że w procesie wdrażania środowisko [Gulp](http://gulpjs.com/) jest uruchamiane po wykonaniu polecenia `npm install`. Usługa App Service nie uruchamia zadań Gulp ani Grunt podczas wdrażania, dlatego w katalogu głównym tego przykładowego repozytorium znajdują się 2 dodatkowe pliki włączające tę funkcję: 

- _deployment_ — ten plik informuje usługę App Service, aby uruchomiła skrypt `bash deploy.sh` jako skrypt wdrożenia niestandardowego.
- _deploy.sh_ — skrypt wdrożenia niestandardowego. Jeśli przejrzysz plik, zauważysz, że uruchamia on polecenie `gulp prod` po poleceniach `npm install` i `bower install`. 

Korzystając z tego podejścia, możesz dodać dowolny krok do wdrożenia opartego na usłudze Git. Jeśli w dowolnym momencie ponownie uruchomisz aplikację internetową platformy Azure, usługa App Service nie uruchomi ponownie tych zadań automatyzacji.

### <a name="browse-to-the-azure-web-app"></a>Przechodzenie do aplikacji internetowej platformy Azure 

Przejdź do wdrożonej aplikacji internetowej w przeglądarce internetowej. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Kliknij pozycję **Utwórz konto** w górnym menu i utwórz użytkownika fikcyjnego. 

Jeśli się to powiedzie i aplikacja automatycznie zaloguje się na konto utworzonego użytkownika, to znaczy, że aplikacja MEAN.js na platformie Azure ma łączność z interfejsem API z bazą danych MongoDB (Cosmos DB). 

![Aplikacja MEAN.js uruchomiona w usłudze Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Wybierz pozycję **Administrator -> Zarządzaj artykułami**, aby dodać artykuły. 

**Gratulacje!** Używasz opartej na danych aplikacji Node.js w usłudze Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Aktualizowanie modelu danych i ponowne wdrażanie

W tym kroku zmienisz model danych `article` i opublikujesz zmiany na platformie Azure.

### <a name="update-the-data-model"></a>Aktualizowanie modelu danych

Otwórz plik _modules/articles/server/models/article.server.model.js_.

W schemacie `ArticleSchema` dodaj typ `String` o nazwie `comment`. Gdy skończysz, kod schematu powinien wyglądać następująco:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Aktualizowanie kodu artykułów

Zaktualizuj pozostałą część kodu `articles`, aby używany był typ `comment`.

Należy zmodyfikować 5 plików: plik kontrolera serwera i 4 pliki widoków klienta. 

Otwórz plik _modules/articles/server/controllers/articles.server.controller.js_.

W funkcji `update` dodaj przypisanie dla zmiennej `article.comment`. W poniższym kodzie przedstawiono ukończoną funkcję `update`:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Otwórz plik _modules/articles/client/views/view-article.client.view.html_.

Tuż nad tagiem zamykającym `</section>` dodaj poniższy wiersz, aby wyświetlić typ `comment` i resztę danych artykułu:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Otwórz plik _modules/articles/client/views/list-articles.client.view.html_.

Tuż nad tagiem zamykającym `</a>` dodaj poniższy wiersz, aby wyświetlić typ `comment` i resztę danych artykułu:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Otwórz plik _modules/articles/client/views/admin/list-articles.client.view.html_.

Wewnątrz elementu `<div class="list-group">` i tuż nad tagiem zamykającym `</a>` dodaj poniższy wiersz, aby wyświetlić typ `comment` i resztę danych artykułu:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Otwórz plik _modules/articles/client/views/admin/form-article.client.view.html_.

Znajdź element `<div class="form-group">` zawierający przycisk przesyłania, który wygląda następująco:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Tuż nad tym tagiem dodaj kolejny element `<div class="form-group">` umożliwiający użytkownikom edytowanie pola `comment`. Nowy element powinien wyglądać następująco:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Lokalne testowanie zmian

Zapisz wszystkie zmiany.

W oknie lokalnego terminala ponownie przetestuj zmiany w trybie produkcyjnym.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Przejdź do adresu `http://localhost:8443` w przeglądarce i upewnij się, że użytkownik jest zalogowany.

Wybierz pozycję **Administrator > Zarządzaj artykułami** i dodaj artykuł, wybierając przycisk **+**.

Zostanie wyświetlone nowe pole tekstowe `Comment`.

![Dodane pole komentarza na stronie Artykuły](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

W terminalu zatrzymaj środowisko Node.js, naciskając klawisze `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

W oknie lokalnego terminala zatwierdź zmiany w usłudze Git i wypchnij zmiany kodu na platformę Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Po ukończeniu `git push` przejdź do aplikacji internetowej platformy Azure i wypróbuj nowe funkcje.

![Zmiany w modelu i bazie danych opublikowane na platformie Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Jeśli dodano wcześniej artykuły, nadal będą widoczne. Istniejące dane w bazie danych Cosmos DB nie zostaną utracone. Ponadto aktualizacje schematu danych pozostawiają istniejące dane bez zmian.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych 

Gdy aplikacja Node.js działa w usłudze Azure App Service, dzienniki konsoli można przesłać potokiem do terminala. W ten sposób można użyć komunikatów diagnostycznych w celu ułatwienia debugowania błędów aplikacji.

Aby rozpocząć przesyłanie strumieniowe dzienników, użyj polecenia [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) w usłudze Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

Po rozpoczęciu przesyłania strumieniowego dzienników odśwież aplikację internetową platformy Azure w przeglądarce, aby wywołać pewien ruch w Internecie. Teraz można zobaczyć dzienniki konsoli przesłane potokiem do terminala.

Przesyłanie strumieniowe dzienników można zatrzymać w dowolnym momencie, naciskając klawisze `Ctrl+C`. 

## <a name="manage-your-azure-web-app"></a>Zarządzanie aplikacją internetową platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację internetową.

W lewym menu kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji sieci Web platformy Azure.

![Nawigacja w portalu do aplikacji sieci Web platformy Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Domyślnie portal zawiera stronę **Omówienie** aplikacji internetowej. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Tworzenie bazy danych MongoDB na platformie Azure
> * Łączenie aplikacji Node.js z usługą MongoDB
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników z platformy Azure do terminala
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"] 
> [Map an existing custom DNS name to Azure Web Apps (Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure)](app-service-web-tutorial-custom-domain.md)
