---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854652"
---
Teraz możesz użyć narzędzia Eksplorator danych w witrynie Azure Portal, aby utworzyć bazę danych i tabelę. 

1. Wybierz **Eksplorator danych** > **nową tabelę**. 
    
    Obszar **Dodaj tabelę** zostanie wyświetlony po prawej stronie. Aby go zobaczyć, być może trzeba będzie przewinąć w prawo.

    ![Eksplorator danych w witrynie Azure Portal](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Na stronie **Dodawanie tabeli** wprowadź ustawienia dla nowej tabeli.

    Ustawienie|Sugerowana wartość|Opis
    ---|---|---
    Identyfikator tabeli|sample-table|Identyfikator nowej tabeli. W przypadku nazw tabel obowiązują takie same wymagania dotyczące znaków, jak dla identyfikatorów baz danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/ \ # ?` ani mieć spacji na końcu.
    Przepływność|400 jednostek żądania|Zmień przepływność na 400 jednostek żądania na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.

3. Kliknij przycisk **OK**.

4. W Eksploratorze danych zostanie wyświetlona nowa baza danych i tabela.

   ![Eksplorator danych w witrynie Azure Portal z widoczną nową bazą danych i kolekcją](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)