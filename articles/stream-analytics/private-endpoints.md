---
title: Tworzenie i usuwanie prywatnych punktów końcowych w klastrze Azure Stream Analytics
description: Dowiedz się, jak zarządzać prywatnymi punktami końcowymi w klastrze Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: baf80e3d543bee455dd8dfa5bc09bc5bf43c3453
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947366"
---
# <a name="create-and-delete-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Tworzenie i usuwanie prywatnych punktów końcowych w klastrze Azure Stream Analytics

Zadania Azure Stream Analytics uruchomione w klastrze można połączyć z zasobami wejściowymi i wyjściowymi, które znajdują się za zaporą lub Virtual Network platformy Azure. Najpierw należy utworzyć prywatny punkt końcowy dla zasobu, takiego jak usługa Azure Event Hub lub Azure SQL Database, w klastrze Stream Analytics. Następnie zatwierdź połączenie prywatnego punktu końcowego z danych wejściowych lub wyjściowych.

Po zatwierdzeniu połączenia każde zadanie uruchomione w klastrze Stream Analytics ma dostęp do zasobu za pomocą prywatnego punktu końcowego. W tym artykule opisano sposób tworzenia i usuwania prywatnych punktów końcowych w klastrze Stream Analytics.

## <a name="create-private-endpoint-in-stream-analytics-cluster"></a>Tworzenie prywatnego punktu końcowego w klastrze Stream Analytics

W tej sekcji dowiesz się, jak utworzyć prywatny punkt końcowy w klastrze Stream Analytics.

1. W Azure Portal zlokalizuj i wybierz klaster Stream Analytics.

1. W obszarze **Ustawienia**wybierz pozycję **prywatne punkty końcowe**.

1. Wybierz pozycję **Dodaj prywatny punkt końcowy** i wprowadź poniższe informacje, aby wybrać zasób do bezpiecznego dostępu za pomocą prywatnego punktu końcowego.

   |Ustawienie|Wartość|
   |---|---|
   |Nazwa|Wprowadź dowolną nazwę prywatnego punktu końcowego. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
   |Metoda połączenia|Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**.<br><br>Możesz wybrać jeden z zasobów, aby bezpiecznie połączyć się z usługą za pomocą prywatnego punktu końcowego, lub połączyć się z zasobem innej osoby przy użyciu identyfikatora zasobu lub aliasu, który zostały Ci udostępnione.|
   |Subskrypcja|Wybierz subskrypcję.|
   |Typ zasobu|Wybierz [Typ zasobu, który jest mapowany na zasób](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Zasób|Wybierz zasób, z którym chcesz nawiązać połączenie za pomocą prywatnego punktu końcowego.|
   |Podzasób docelowy|Typ zasobu zasobu wybranego powyżej, który będzie mógł uzyskać dostęp do prywatnego punktu końcowego.|

   ![Środowisko tworzenia prywatnego punktu końcowego](./media/private-endpoints/create-private-endpoint.png)

1. Zatwierdź połączenie z zasobu docelowego. Jeśli na przykład w poprzednim kroku został utworzony prywatny punkt końcowy do wystąpienia Azure SQL Database, należy przejść do tego wystąpienia SQL Database i zobaczyć oczekujące połączenie, które ma zostać zatwierdzone. Wyświetlenie żądania połączenia może potrwać kilka minut.

    ![Zatwierdź prywatny punkt końcowy](./media/private-endpoints/approve-private-endpoint.png)

1. Można wrócić do klastra Stream Analytics, aby zobaczyć zmianę stanu od **oczekującej zgody klienta** na **OCZEKUJĄCą konfigurację DNS** w celu **skonfigurowania** jej w ciągu kilku minut.

## <a name="delete-a-private-endpoint-in-a-stream-analytics-cluster"></a>Usuwanie prywatnego punktu końcowego w klastrze Stream Analytics

1. W Azure Portal zlokalizuj i wybierz klaster Stream Analytics.

1. W obszarze **Ustawienia**wybierz pozycję **prywatne punkty końcowe**.

1. Wybierz prywatny punkt końcowy, który chcesz usunąć, i wybierz pozycję **Usuń**.

   ![Usuń prywatny punkt końcowy](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Następne kroki

Teraz masz przegląd sposobu zarządzania prywatnymi punktami końcowymi w klastrze Azure Stream Analytics. Następnie można dowiedzieć się, jak skalować klastry i uruchamiać zadania w klastrze:

* [Skalowanie klastra Azure Stream Analytics](scale-cluster.md)
* [Zarządzanie zadaniami Stream Analytics w klastrze Stream Analytics](manage-jobs-cluster.md)