---
title: 'Szybki start: interfejs API Cassandra z platformą Node.js — Azure Cosmos DB'
description: W tym przewodniku Szybki start przedstawiono używanie interfejsu API bazy danych Apache Cassandra w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przy użyciu platformy Node.js
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: ffc2681e487a51ce630d9433d6ded86961b5276c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210371"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Szybki Start: Tworzenie aplikacji Cassandra przy użyciu zestawu SDK środowiska Node. js i Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

W tym przewodniku szybki start utworzysz konto usługi Azure Cosmos DB interfejs API Cassandra i używasz aplikacji node. js Cassandra, która została sklonowana z usługi GitHub w celu utworzenia bazy danych i kontenera Cassandra. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Można też [bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure — nie wymaga to opłat ani zobowiązań.

Potrzebne są też następujące elementy:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) w wersji 0.10.29 lub nowszej
* [Usługa Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów musisz utworzyć konto bazy danych Cassandra z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API Apache Cassandra z usługi GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia. Utwórz nowy folder o nazwie `git-samples`. Następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminalu usługi Git, na przykład git bash. Użyj polecenia `cd`, aby przejść do nowego folderu w celu zainstalowania przykładowej aplikacji.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię przykładowej aplikacji na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak kod tworzy zasoby bazy danych, możesz przejrzeć poniższe fragmenty kodu. Fragmenty kodu są pobierane z pliku `uprofile.js` z folderu `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string). 

* Nazwa użytkownika i hasło zostały ustawione przy użyciu strony parametrów połączenia w witrynie Azure Portal. Element `path\to\cert` zawiera ścieżkę do certyfikatu X509. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* Element `client` jest inicjowany przy użyciu informacji contactPoint. Dane contactPoint są pobierane z witryny Azure Portal.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* Element `client` łączy się z interfejsem API bazy danych Cassandra w usłudze Azure Cosmos DB.

    ```javascript
    client.connect(next);
    ```

* Tworzona jest nowa przestrzeń kluczy.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Tworzona jest nowa tabela.

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Wstawiane są jednostki klucz-wartość.

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* Zapytanie o pobranie wszystkich wartości kluczy.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  
    
* Zapytanie o pobranie pary klucz-wartość.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Parametry połączenia umożliwiają aplikacji komunikowanie się z hostowaną bazą danych.

1. Na koncie Azure Cosmos DB w [Azure Portal](https://portal.azure.com/)wybierz pozycję **Parametry połączenia**. 

    Użyj przycisku ![Kopiuj](./media/create-cassandra-nodejs/copy.png) po prawej stronie ekranu, aby skopiować górną wartość, PUNKT KONTAKTOWY.

    ![Wyświetlanie i kopiowanie wartości PUNKT KONTAKTOWY, NAZWA UŻYTKOWNIKA i HASŁO ze strony parametrów połączenia w witrynie Azure Portal](./media/create-cassandra-nodejs/keys.png)

2. Otwórz plik `config.js`. 

3. Wklej wartość PUNKT KONTAKTOWY z portalu do lokalizacji `<FillMEIN>` w wierszu 4.

    Wiersz 4 powinien teraz wyglądać podobnie do: 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Skopiuj wartość NAZWA UŻYTKOWNIKA z portalu do lokalizacji `<FillMEIN>` w wierszu 2.

    Wiersz 2 powinien teraz wyglądać podobnie do: 

    `config.username = 'cosmos-db-quickstart';`
    
5. Skopiuj wartość HASŁO z portalu do lokalizacji `<FillMEIN>` w wierszu 3.

    Wiersz 3 powinien teraz wyglądać podobnie do:

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Zapisz plik `config.js`.
    
## <a name="use-the-x509-certificate"></a>Używanie certyfikatu X509

1. Pobierz na komputer lokalny certyfikat Baltimore CyberTrust Root spod adresu [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Zmień rozszerzenie nazwy tego pliku na `.cer`.

   Certyfikat ma numer seryjny `02:00:00:b9` i odcisk palca SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Otwórz plik `uprofile.js` i zmień wartość parametru `path\to\cert`, aby wskazywał nowy certyfikat.

3. Zapisz plik `uprofile.js`.

> [!NOTE]
> Jeśli wystąpi błąd związany z certyfikatem w kolejnych krokach i jest uruchomiony na komputerze z systemem Windows, należy się upewnić, że wykonano proces prawidłowo przekonwertowane pliku CRT do formatu Microsoft. cer poniżej.
> 
> Kliknij dwukrotnie plik CRT, aby otworzyć go na ekranie certyfikatu. 
>
> ![Wyświetlanie i weryfikowanie danych wyjściowych](./media/create-cassandra-nodejs/crtcer1.gif)
>
> Naciśnij przycisk Dalej w Kreatorze certyfikatów. Wybierz pozycję Base-64 encoded X. 509 (. CER), a następnie kliknij przycisk Dalej.
>
> ![Wyświetlanie i weryfikowanie danych wyjściowych](./media/create-cassandra-nodejs/crtcer2.gif)
>
> Wybierz pozycję Przeglądaj (aby zlokalizować miejsce docelowe) i wpisz nazwę pliku.
> Wybierz pozycję Dalej, a następnie kliknij przycisk Zakończ.
>
> Powinien być teraz poprawnie sformatowany plik CER. Upewnij się, że ścieżka w `uprofile.js` wskazuje na ten plik.

## <a name="run-the-nodejs-app"></a>Uruchamianie aplikacji Node.js

1. W oknie terminalu usługi git upewnij się, że znajduje się w katalogu przykładowym, który został sklonowany wcześniej:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. Uruchom `npm install`, aby zainstalować wymagane moduły npm.

3. Uruchom polecenie `node uprofile.js`, aby uruchomić aplikację Node.

4. W wierszu polecenia sprawdź, czy wyniki są zgodne z oczekiwaniami.

    ![Wyświetlanie i weryfikowanie danych wyjściowych](./media/create-cassandra-nodejs/output.png)

    Naciśnij klawisze CTRL + C, aby zatrzymać wykonywanie programu i zamknąć okno konsoli. 

5. W witrynie Azure Portal otwórz **Eksploratora danych**, aby wykonywać zapytania oraz modyfikować te nowe dane i pracować z nimi. 

    ![Wyświetlanie danych w Eksploratorze danych](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB przy użyciu interfejs API Cassandra i uruchamiania aplikacji Cassandra Node. js, która tworzy bazę danych Cassandra i kontener. Teraz możesz zaimportować dodatkowe dane do konta Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)