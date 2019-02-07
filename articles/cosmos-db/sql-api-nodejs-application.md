---
title: 'Samouczek: Kompilowanie aplikacji internetowej Node.js przy użyciu zestawu SDK dla języka JavaScript na potrzeby zarządzania danymi interfejsu API SQL usługi Azure Cosmos DB'
description: W tym samouczku środowiska Node.js przedstawiono, jak przy użyciu usługi Microsoft Azure Cosmos DB przechowywać dane i uzyskiwać do nich dostęp z poziomu aplikacji internetowej Node.js Express hostowanej w funkcji Web Apps usługi Microsoft Azure App Service.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/10/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Node.js web application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 47a4b3bc1e71c9dacc53ca3ff507768d4a0f20e9
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510582"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Samouczek: Kompilowanie aplikacji internetowej Node.js przy użyciu zestawu SDK dla języka JavaScript na potrzeby zarządzania kontem interfejsu API SQL w usłudze Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Jako deweloper być może masz aplikacje, które używają danych dokumentów NoSQL. Konto interfejsu API SQL w usłudze Azure Cosmos DB umożliwia przechowywanie tych danych dokumentów i uzyskiwanie do nich dostępu. W tym samouczku środowiska Node.js pokazano, jak przechowywać dane z konta interfejsu API SQL w usłudze Azure Cosmos DB i uzyskiwać do nich dostęp przy użyciu aplikacji Node.js Express hostowanej w funkcji Web Apps usługi Microsoft Azure App Service. W tym samouczku utworzysz aplikację internetową (z listą zadań do wykonania), która umożliwia tworzenie, pobieranie i wykonywanie zadań. Zadania są przechowywane jako dokumenty JSON w usłudze Azure Cosmos DB. 

W tym samouczku przedstawiono sposób tworzenia konta interfejsu API SQL w usłudze Azure Cosmos DB za pomocą witryny Azure Portal. Następnie skompilujesz i uruchomisz aplikację internetową bazującą na zestawie SDK środowiska Node.js, utworzysz bazę danych i kontener oraz dodasz elementy do tego kontenera. W tym samouczku użyto zestawu JavaScript SDK w wersji 2.0.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Cosmos DB
> * Tworzenie nowej aplikacji Node.js
> * Łączenie aplikacji z usługą Azure Cosmos DB
> * Uruchamianie i wdrażanie aplikacji na platformie Azure

## <a name="_Toc395783176"></a>Wymagania wstępne

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz następujące zasoby:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] wersja 6.10 lub nowsza.
* [Generator Express](https://www.expressjs.com/starter/generator.html) (możesz zainstalować platformę Express za pomocą polecenia `npm install express-generator -g`)
* Zainstaluj oprogramowanie [Git][Git] na lokalnej stacji roboczej.

## <a name="_Toc395637761"></a>Tworzenie konta usługi Azure Cosmos DB
Zacznijmy od utworzenia konta usługi Azure Cosmos DB. Jeśli masz już konto lub jeśli korzystasz z emulatora usługi Azure Cosmos DB na potrzeby tego samouczka, możesz od razu przejść do sekcji [Krok 2. Tworzenie nowej aplikacji Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Tworzenie nowej aplikacji Node.js
Teraz nauczysz się, jak utworzyć podstawowy projekt aplikacji Hello World w środowisku Node.js przy użyciu platformy Express.

1. Otwórz swój ulubiony terminal, na przykład wiersz polecenia środowiska Node.js.

1. Przejdź do katalogu, w którym chcesz przechowywać nową aplikację.

1. Użyj generatora platformy Express, aby wygenerować nową aplikację o nazwie **todo**.

   ```bash
   express todo
   ```

1. Otwórz katalog **todo** i zainstaluj zależności.

   ```bash
   cd todo
   npm install
   ```

1. Uruchom nową aplikację.

   ```bash
   npm start
   ```

1. Swoją nową aplikację możesz wyświetlić, przechodząc w przeglądarce na adres [http://localhost:3000](http://localhost:3000).
   
   ![Poznaj środowisko Node.js — zrzut ekranu aplikacji Hello World w oknie przeglądarki](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Zatrzymaj aplikację, naciskając klawisze CTRL+C w oknie terminalu, i naciśnij klawisz **y** w celu zakończenia zadania wsadowego.

## <a name="_Toc395783179"></a>Instalowanie wymaganych modułów

Plik **package.json** jest jednym z plików utworzonych w folderze głównym projektu. Ten plik zawiera listę dodatkowych modułów, które są wymagane dla aplikacji Node.js. Podczas wdrażania tej aplikacji na platformie Azure ten plik służy do określania, które moduły mają być zainstalowane na platformie Azure w celu obsługi tej aplikacji. Zainstaluj jeszcze 2 pakiety na potrzeby tego samouczka.

1. Otwórz terminal i zainstaluj moduł **async** za pośrednictwem menedżera npm.

   ```bash
   npm install async --save
   ```

2. Zainstaluj moduł **@azure/cosmos** za pomocą menedżera npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Łączenie aplikacji Node.js z usługą Azure Cosmos DB
Teraz, po wykonaniu wstępnej instalacji i konfiguracji, napiszesz kod, który jest wymagany przez aplikację z listą zadań do wykonania do komunikowania się z usługą Azure Cosmos DB.

### <a name="create-the-model"></a>Tworzenie modelu
1. W katalogu głównym projektu utwórz nowy katalog o nazwie **models**.  

2. W katalogu **models** utwórz nowy plik o nazwie **taskDao.js**. Ten plik zawiera kod wymagany do utworzenia bazy danych i kontenera. Definiuje także metody odczytu, aktualizacji, tworzenia i znajdowania zadań w usłudze Azure Cosmos DB. 

3. Skopiuj następujący kod do pliku **taskDao.js**:

   ```javascript
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Zapisz i zamknij plik **taskDao.js**.  

### <a name="create-the-controller"></a>Tworzenie kontrolera

1. W katalogu **routes** projektu utwórz nowy plik o nazwie **tasklist.js**.  

2. Dodaj następujący kod do pliku **tasklist.js**. Ten kod służy do ładowania modułów CosmosClient i async, które są używane przez plik **tasklist.js**. Ten kod definiuje również klasę **TaskList**, która jest przekazywana jako wystąpienie zdefiniowanego wcześniej obiektu **TaskDao**:
   
   ```javascript
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Zapisz i zamknij plik **tasklist.js**.

### <a name="add-configjs"></a>Dodawanie pliku config.js

1. W katalogu głównym projektu utwórz nowy plik o nazwie **config.js**. 

2. Dodaj następujący kod do pliku **config.js**. Ten kod służy do definiowania ustawień konfiguracji i wartości potrzebnych dla aplikacji.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. W pliku **config.js** zaktualizuj wartości kluczy HOST i AUTH_KEY przy użyciu wartości znajdujących się na stronie Klucze Twojego konta usługi Azure Cosmos DB w witrynie [Azure Portal](https://portal.azure.com). 

4. Zapisz i zamknij plik **config.js**.

### <a name="modify-appjs"></a>Modyfikowanie pliku app.js

1. W katalogu projektu otwórz plik **app.js**. Ten plik został utworzony wcześniej podczas tworzenia aplikacji internetowej platformy Express.  

2. Dodaj następujący kod do pliku **app.js**. Ten kod definiuje plik konfiguracji, który ma być używany, i ładuje wartości do niektórych zmiennych, które będą używane w następnych sekcjach. 
   
   ```javascript
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error setting up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Na koniec zapisz i zamknij plik **app.js**.

## <a name="_Toc395783181"></a>Tworzenie interfejsu użytkownika

Teraz utwórzmy interfejs użytkownika, aby użytkownik mógł korzystać z aplikacji. Utworzona w poprzednich sekcjach aplikacja Express używa aparatu widoku **Jade**.

1. Plik **layout.jade** w katalogu **views** jest używany jako szablon globalny dla innych plików **jade**. W tym kroku zmodyfikujesz go w celu używania platformy Twitter Bootstrap, która jest zestawem narzędzi służącym do projektowania witryn internetowych.  

2. Otwórz plik **layout.jade** znajdujący się w folderze **views** i zastąp jego zawartość następującym kodem:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Ten kod określa, że aparat **Jade** ma renderować kod HTML dla aplikacji, i tworzy **blok** o nazwie **content**, w którym można udostępnić układ dla stron zawartości. Zapisz i zamknij plik **layout.jade**.

3. Teraz otwórz plik **index.jade** — widok, który będzie używany przez naszą aplikację — i zastąp zawartość pliku następującym kodem:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Ten kod rozszerza układ i udostępnia zawartość dla symbolu zastępczego **content**, który wcześniej widzieliśmy w pliku **layout.jade**. W tym układzie utworzyliśmy dwa formularze HTML.

Pierwszy formularz zawiera tabelę danych i przycisk umożliwiający aktualizowanie elementów przez publikowanie do metody **/completeTask** kontrolera.
    
Drugi formularz zawiera dwa pola wejściowe i przycisk umożliwiający utworzenie nowego elementu przez publikowanie do metody **/addtask** kontrolera. To wszystko, czego potrzeba, aby aplikacja działała.

## <a name="_Toc395783181"></a>Uruchamianie aplikacji lokalnie

Teraz, gdy masz utworzoną aplikację, możesz uruchomić ją lokalnie, wykonując następujące kroki:  

1. Aby przetestować aplikację na komputerze lokalnym, w terminalu uruchom polecenie `npm start`, aby uruchomić aplikację, a następnie odśwież stronę przeglądarki [http://localhost:3000](http://localhost:3000). Strona powinna teraz wyglądać jak pokazano na poniższym zrzucie ekranu:
   
    ![Zrzut ekranu aplikacji MyTodo List w oknie przeglądarki](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > W przypadku wystąpienia błędu dotyczącego wcięcia w pliku layout.jade bądź index.jade upewnij się, że dwa pierwsze wiersze w obu plikach są wyrównane do lewej, bez spacji. Jeśli przed dwoma pierwszymi wierszami występują spacje, usuń je, zapisz oba pliki, a następnie odśwież okno przeglądarki. 

2. Wprowadź nowe zadanie przy użyciu pól Item (Element), Item Name (Nazwa elementu) i Category (Kategoria), a następnie wybierz pozycję **Add Item** (Dodaj element). Spowoduje to utworzenie w usłudze Azure Cosmos DB dokumentu z tymi właściwościami. 

3. Ta strona powinna zostać zaktualizowana w celu wyświetlenia nowo utworzonego elementu na liście ToDo.
   
    ![Zrzut ekranu aplikacji z nowym elementem na liście ToDo](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. Aby zakończyć zadanie, zaznacz pole wyboru w kolumnie Complete (Zakończ), a następnie wybierz pozycję **Update tasks** (Aktualizuj zadania). Spowoduje to zaktualizowanie utworzonego już dokumentu i usunięcie go z widoku.

5. Aby zatrzymać aplikację, naciśnij klawisze CTRL+C w oknie terminalu, po czym naciśnij klawisz **Y** w celu zakończenia zadania wsadowego.

## <a name="_Toc395783182"></a>Wdrażanie aplikacji w usłudze Web Apps

Gdy aplikacja działa prawidłowo w środowisku lokalnym, możesz wdrożyć ją na platformie Azure, wykonując następujące kroki:

1. Jeśli jeszcze tego nie zrobiono, włącz repozytorium Git dla aplikacji Web Apps.

2. Dodaj aplikację Web Apps jako element zdalny narzędzia Git.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Wdróż aplikację, wypychając ją do lokalizacji zdalnej.
   
   ```bash
   git push azure master
   ```

4. W ciągu kilku sekund aplikacja internetowa zostanie opublikowana i uruchomiona w przeglądarce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć grupę zasobów, konto usługi Azure Cosmos DB oraz wszystkie powiązane zasoby, gdy nie będą już potrzebne. Aby to zrobić, wybierz grupę zasobów używaną w przypadku konta usługi Azure Cosmos DB, wybierz pozycję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="_Toc395637775"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji mobilnych za pomocą platformy Xamarin i usługi Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

