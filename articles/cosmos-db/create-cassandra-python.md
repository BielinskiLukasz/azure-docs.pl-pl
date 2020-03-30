---
title: 'Szybki start: interfejs API Cassandra z językiem Python — Azure Cosmos DB'
description: W tym przewodniku Szybki start przedstawiono używanie interfejsu API Apache Cassandra w usłudze Azure Cosmos DB do tworzenia aplikacji profilów przy użyciu języka Python.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 0b432653c452b6763e746f61b86e881c9cee62cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77134646"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Szybki start: tworzenie aplikacji Cassandra za pomocą zestawów SDK języka Python i usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

W tym przewodniku Szybki start utworzysz konto interfejsu API usługi Azure Cosmos DB Cassandra i użyj aplikacji Cassandra Python sklonowanej z usługi GitHub, aby utworzyć bazę danych i kontener Cassandra. Usługa Azure Cosmos DB to wielomodelowa usługa bazy danych, która umożliwia szybkie tworzenie i wykonywanie zapytań o bazy danych dokumentów, tabeli, wartości klucza i wykresów z możliwościami dystrybucji globalnej i skali poziomej.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Możesz [też bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure.
- [Python 2.7.14+ lub 3.4+](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Sterownik Pythona dla Apache Cassandra](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów musisz utworzyć konto bazy danych Cassandra z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API Apache Cassandra z usługi GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia. Utwórz nowy folder o nazwie `git-samples`. Następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ten krok jest opcjonalny. Jeśli chcesz dowiedzieć się, jak kod tworzy zasoby bazy danych, możesz przejrzeć poniższe fragmenty kodu. Wszystkie fragmenty kodu są pobierane z pliku *pyquickstart.py.* W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string). 

* Nazwa użytkownika i hasło zostały ustawione przy użyciu strony parametrów połączenia w witrynie Azure Portal. Element `path\to\cert` zawiera ścieżkę do certyfikatu X509. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* Element `cluster` jest inicjowany przy użyciu informacji contactPoint. Dane contactPoint są pobierane z witryny Azure Portal.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* Element `cluster` łączy się z interfejsem API bazy danych Cassandra w usłudze Azure Cosmos DB.

    ```python
    session = cluster.connect()
    ```

* Tworzona jest nowa przestrzeń kluczy.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Tworzona jest nowa tabela.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Wstawiane są jednostki klucz-wartość.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Zapytanie o pobranie wszystkich wartości kluczy.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Zapytanie o pobranie pary klucz-wartość.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Parametry połączenia umożliwiają aplikacji komunikowanie się z hostowaną bazą danych.

1. Na swoim koncie usługi Azure Cosmos DB w [portalu Azure](https://portal.azure.com/)wybierz pozycję **Parametry połączenia**. 

    Użyj przycisku ![Kopiuj](./media/create-cassandra-python/copy.png) po prawej stronie ekranu, aby skopiować górną wartość, PUNKT KONTAKTOWY.

    ![Wyświetlanie i kopiowanie nazwy użytkownika, hasła i punktu kontaktowego w witrynie Azure Portal, blok parametrów połączenia](./media/create-cassandra-python/keys.png)

2. Otwórz plik *config.py.* 

3. Wklej wartość PUNKT KONTAKTOWY z portalu do lokalizacji `<FILLME>` w wierszu 10.

    Wiersz 10 powinien teraz wyglądać podobnie do: 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

4. Skopiuj wartość NAZWA UŻYTKOWNIKA z portalu do lokalizacji `<FILLME>` w wierszu 6.

    Wiersz 6 powinien teraz wyglądać podobnie do: 

    `'username': 'cosmos-db-quickstart',`
    
5. Skopiuj wartość HASŁO z portalu do lokalizacji `<FILLME>` w wierszu 8.

    Wiersz 8 powinien teraz wyglądać podobnie do:

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Zapisz plik *config.py.*
    
## <a name="use-the-x509-certificate"></a>Używanie certyfikatu X509

1. Pobierz certyfikat Baltimore CyberTrust Root [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt)lokalnie z . Zmień nazwę pliku przy użyciu rozszerzenia pliku *.cer*.

   Certyfikat ma numer seryjny `02:00:00:b9` i odcisk palca SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Otwórz *pyquickstart.py* i zmień `path\to\cert` punkt na nowy certyfikat.

3. Zapisz *pyquickstart.py*.

## <a name="run-the-python-app"></a>Uruchamianie aplikacji w języku Python

1. Korzystając z polecenia cd w terminalu usługi Git, przejdź do folderu `azure-cosmos-db-cassandra-python-getting-started`. 

2. Uruchom następujące polecenia, aby zainstalować wymagane moduły:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Uruchom następujące polecenie, aby uruchomić aplikację języka Python:

    ```
    python pyquickstart.py
    ```

3. W wierszu polecenia sprawdź, czy wyniki są zgodne z oczekiwaniami.

    Naciśnij klawisze CTRL+C, aby zatrzymać wykonywanie programu i zamknąć okno konsoli. 

    ![Wyświetlanie i weryfikowanie danych wyjściowych](./media/create-cassandra-python/output.png)
    
4. W witrynie Azure Portal otwórz **Eksploratora danych**, aby wykonywać zapytania oraz modyfikować te nowe dane i pracować z nimi. 

    ![Wyświetlanie danych w Eksploratorze danych](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć konto usługi Azure Cosmos DB za pomocą interfejsu API Cassandra i uruchomić aplikację Cassandra Python, która tworzy bazę danych i kontener Cassandra. Teraz można importować dodatkowe dane do konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)

