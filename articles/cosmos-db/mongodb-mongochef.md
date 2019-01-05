---
title: Łączenie do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB przy użyciu z programu Studio 3T
titleSuffix: Azure Cosmos DB
description: Dowiedz się, jak połączyć się z usługi Cosmos DB korzystanie z programu Studio 3T i interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: c679d2e6b7dc8998016e79d265f368504d6def3d
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038418"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Łączenie się z kontem Cosmos korzystanie z programu Studio 3T

Aby nawiązać połączenie konta usługi Cosmos przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, musisz mieć:

* Pobierz i zainstaluj [z programu Studio 3T](https://studio3t.com/)
* Masz usługi Cosmos DB [parametry połączenia](connect-mongodb-account.md) informacji

## <a name="create-the-connection-in-studio-3t"></a>Tworzenie połączenia w z programu Studio 3T
Aby dodać swoje konto Cosmos Menedżera połączeń z programu 3T Studio, wykonaj następujące kroki:

1. Pobierz informacje o połączeniu dla konta usługi Cosmos skonfigurowane za pomocą usługi Azure Cosmos DB interfejsu API MongoDB przy użyciu instrukcji w [połączyć aplikację MongoDB w usłudze Azure Cosmos DB](connect-mongodb-account.md) artykułu.

    ![Zrzut ekranu strony parametrów połączenia](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Kliknij przycisk **Connect** , aby otworzyć Menedżera połączeń, a następnie przycisk **nowe połączenie**

    ![Zrzut ekranu przedstawiający Menedżera połączeń z programu 3T Studio](./media/mongodb-mongochef/ConnectionManager.png)
3. W **nowe połączenie** okna na **serwera** wprowadź hosta (FQDN) konta usługi Azure Cosmos DB i numer portu.

    ![Zrzut ekranu przedstawiający kartę Studio z programu 3T połączenie Menedżera serwera](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. W **nowe połączenie** okna na **uwierzytelniania** karty, wybierz tryb uwierzytelniania **Basic (CR bazy danych MONGODB lub SCARM-SHA-1)** i wprowadź nazwę użytkownika i hasło.  Zaakceptuj uwierzytelniania domyślnej bazy danych (Administrator) lub podać własne wartości.

    ![Zrzut ekranu przedstawiający kartę uwierzytelnianie Menedżera połączeń z programu 3T Studio](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. W **nowe połączenie** okna na **SSL** karcie wyboru **Użyj protokołu SSL do łączenia z** pole wyboru i **akceptować certyfikaty SSL z podpisem własnym serwera**  przycisku radiowego.

    ![Zrzut ekranu: karta SSL Menedżera połączeń programu Studio z programu 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Kliknij przycisk **Testuj połączenie** przycisk, aby zweryfikować informacje o połączeniu, kliknij przycisk **OK** wróć do okna nowe połączenie, a następnie kliknij przycisk **Zapisz**.

    ![Zrzut ekranu przedstawiający okno połączenia testowego z programu 3T Studio](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Umożliwia tworzenie bazy danych, kolekcji i dokumentów z programu Studio 3T
Aby utworzyć bazę danych, kolekcję i dokumenty przy użyciu z programu Studio 3T, wykonaj następujące czynności:

1. W **Menedżera połączeń**, zaznacz połączenie i kliknij **Connect**.

    ![Zrzut ekranu przedstawiający Menedżera połączeń z programu 3T Studio](./media/mongodb-mongochef/ConnectToAccount.png)
2. Kliknij prawym przyciskiem myszy hosta, a następnie wybierz **Dodaj bazę danych**.  Podaj nazwę bazy danych, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający opcję Dodaj bazę danych z programu 3T Studio](./media/mongodb-mongochef/AddDatabase1.png)
3. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz **Dodaj kolekcję**.  Podaj nazwę kolekcji, a następnie kliknij przycisk **Utwórz**.

    ![Zrzut ekranu przedstawiający opcję Dodaj kolekcję z programu 3T Studio](./media/mongodb-mongochef/AddCollection.png)
4. Kliknij przycisk **kolekcji** menu elementów, następnie kliknij przycisk **Dodawanie dokumentu**.

    ![Zrzut ekranu przedstawiający element menu Dodaj dokument z programu 3T programu Studio](./media/mongodb-mongochef/AddDocument1.png)
5. W oknie dialogowym Dodawanie dokumentu, wklej następujący kod, a następnie kliknij przycisk **Dodawanie dokumentu**.

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
6. Dodawanie innego dokumentu, tym razem z następującą zawartością:

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
7. Wykonywanie przykładowego zapytania. Na przykład wyszukaj rodziny o nazwisku "Andersen" i zwrócić pól Stan i elementy nadrzędne.

    ![Zrzut ekranu przedstawiający wyniki zapytania programu Chef Mongo](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [korzystać z programu 3T Robo](mongodb-robomongo.md) przy użyciu interfejsu API usługi Azure Cosmos DB, bazy danych mongodb.
- Zapoznaj się z bazą danych MongoDB [przykłady](mongodb-samples.md) przy użyciu interfejsu API usługi Azure Cosmos DB, bazy danych mongodb.
