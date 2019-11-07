---
title: Nawiązywanie połączenia z interfejsem API Azure Cosmos DB MongoDB za pomocą programu Studio 3T
description: Dowiedz się, jak nawiązać połączenie z interfejsem API Azure Cosmos DB MongoDB przy użyciu programu Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/01/2019
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: c9be9893d28439fdcff9f33f0303a816bd94c7b8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584422"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Nawiązywanie połączenia z kontem usługi Azure Cosmos za pomocą programu Studio 3T

Aby nawiązać połączenie z interfejsem API Azure Cosmos DB MongoDB za pomocą programu Studio 3T, należy:

* Pobierz i zainstaluj program [Studio 3T](https://studio3t.com/).
* Zawierają informacje o [parametrach połączenia](connect-mongodb-account.md) konta usługi Azure Cosmos.

> [!NOTE]
> Obecnie Robo 3T v 1.2 i Lower są obsługiwane z interfejsem API Cosmos DB dla MongoDB.

## <a name="create-the-connection-in-studio-3t"></a>Utwórz połączenie w programie Studio 3T

Aby dodać konto usługi Azure Cosmos do Menedżera połączeń programu Studio 3T, wykonaj następujące czynności:

1. Pobierz informacje o połączeniu dla interfejsu API Azure Cosmos DB dla konta MongoDB, korzystając z instrukcji w temacie [Connect a MongoDB Application to Azure Cosmos DB](connect-mongodb-account.md) article.

    ![Zrzut ekranu strony parametrów połączenia](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Kliknij przycisk **Połącz** , aby otworzyć Menedżera połączeń, a następnie kliknij przycisk **nowe połączenie** .

    ![Zrzut ekranu Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. W oknie **nowe połączenie** na karcie **serwer** wprowadź nazwę hosta (FQDN) konta usługi Azure Cosmos i port.

    ![Zrzut ekranu przedstawiający kartę serwer Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. W oknie **nowe połączenie** na karcie **uwierzytelnianie** wybierz pozycję uwierzytelnianie **podstawowe (MONGODB-CR lub SCARM-SHA-1)** , a następnie wprowadź nazwę użytkownika i hasło.  Zaakceptuj domyślną bazę danych uwierzytelniania (Administrator) lub podaj własną wartość.

    ![Zrzut ekranu karty uwierzytelnianie Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. W oknie **nowe połączenie** na karcie **SSL** zaznacz pole wyboru **Użyj protokołu SSL do połączenia** i przycisk radiowy **Zaakceptuj certyfikaty SSL z podpisem własnym serwera** .

    ![Zrzut ekranu przedstawiający kartę SSL Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kliknij przycisk **Testuj połączenie** , aby sprawdzić poprawność informacji o połączeniu, kliknij przycisk **OK** , aby powrócić do okna nowe połączenie, a następnie kliknij przycisk **Zapisz**.

    ![Zrzut ekranu przedstawiający okno połączenia testowego programu Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Tworzenie bazy danych, kolekcji i dokumentów przy użyciu programu Studio 3T
Aby utworzyć bazę danych, kolekcję i dokumenty przy użyciu programu Studio 3T, wykonaj następujące czynności:

1. W **Menedżerze połączeń**zaznacz połączenie i kliknij pozycję **Połącz**.

    ![Zrzut ekranu Menedżera połączeń programu Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kliknij prawym przyciskiem myszy hosta i wybierz polecenie **Dodaj bazę danych**.  Podaj nazwę bazy danych i kliknij przycisk **OK**.

    ![Zrzut ekranu opcji Dodaj bazę danych programu Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Dodaj kolekcję**.  Podaj nazwę kolekcji, a następnie kliknij przycisk **Utwórz**.

    ![Zrzut ekranu przedstawiający opcję dodawania kolekcji programu Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Kliknij element menu **kolekcji** , a następnie kliknij pozycję **Dodaj dokument**.

    ![Zrzut ekranu przedstawiający element menu Dodawanie dokumentu programu Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. W oknie dialogowym Dodawanie dokumentu Wklej następujące elementy, a następnie kliknij pozycję **Dodaj dokument**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Dodaj kolejny dokument, tym razem z następującą zawartością:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Wykonaj przykładowe zapytanie. Na przykład wyszukaj rodziny o nazwie "Andersen" i zwróć pola nadrzędne i stanowe.

    ![Zrzut ekranu przedstawiający wyniki zapytania Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
