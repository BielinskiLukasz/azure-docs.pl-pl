---
title: Łączenie z usługą Azure Cosmos DB przy użyciu Robo z programu 3T
description: Dowiedz się, jak połączyć się z usługi Azure Cosmos DB korzystanie z programu 3T Robo i interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ab066fef4bffe35408fdc5f3ad6d66796b4d0818
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443974"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Korzystać z programu 3T Robo przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB

Aby nawiązać połączenie konta usługi Cosmos korzystanie z programu 3T Robo, musisz mieć:

* Pobierz i zainstaluj [Robo z programu 3T](https://robomongo.org/)
* Masz usługi Cosmos DB [parametry połączenia](connect-mongodb-account.md) informacji

## <a name="connect-using-robo-3t"></a>Nawiązywanie połączenia przy użyciu Robo z programu 3T
Aby dodać konto usługi Cosmos do Menedżera połączeń z programu 3T Robo, wykonaj następujące czynności:

1. Pobierz informacje o połączeniu dla konta usługi Cosmos skonfigurowane za pomocą usługi Azure Cosmos DB interfejsu API MongoDB przy użyciu instrukcji [tutaj](connect-mongodb-account.md).

    ![Zrzut ekranu przedstawiający blok parametrów połączenia](./media/mongodb-robomongo/connectionstringblade.png)
2. Uruchom *Robomongo.exe*

3. Kliknij przycisk połączenie w obszarze **pliku** do zarządzania połączeniami. Następnie kliknij przycisk **Utwórz** w **połączenia bazy danych MongoDB** okno, które zostanie otwarty **ustawienia połączenia** okna.

4. W **ustawienia połączenia** okna, wybierz nazwę. Następnie, Znajdź **hosta** i **portu** z informacje o połączeniu w kroku 1, a następnie wprowadź je do **adres** i **portu**odpowiednio.

    ![Zrzut ekranu z narzędzia Robomongo Zarządzanie połączeniami](./media/mongodb-robomongo/manageconnections.png)
5. Na **uwierzytelniania** kliknij pozycję **Przeprowadź uwierzytelnianie**. Następnie wprowadź bazy danych (wartość domyślna to *administratora*), **nazwa_użytkownika** i **hasło**.
Zarówno **nazwa_użytkownika** i **hasło** znajdują się informacje o połączeniu w kroku 1.

    ![Zrzut ekranu przedstawiający kartę uwierzytelnianie z narzędzia Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na **SSL** karcie wyboru **Użyj protokołu SSL**, następnie zmienić **metodę uwierzytelniania** do **certyfikatu z podpisem własnym**.

    ![Zrzut ekranu przedstawiający kartę SSL z narzędzia Robomongo](./media/mongodb-robomongo/SSL.png)
7. Na koniec kliknij **testu** Aby zweryfikować, że jesteś w stanie nawiązać połączenie, następnie **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
