---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619286"
---
1. Aby utworzyć nowy magazyn konfiguracji aplikacji, zaloguj się do [Azure Portal](https://portal.azure.com). W lewym górnym rogu strony głównej wybierz pozycję **Utwórz zasób**. W polu **Wyszukaj w witrynie Marketplace** wprowadź wartość **Konfiguracja aplikacji** , a następnie wybierz pozycję Wprowadź.

    ![Wyszukiwanie usługi App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Wybierz pozycję **Konfiguracja aplikacji** z wyników wyszukiwania, a następnie wybierz pozycję **Utwórz**.

    ![Wybierz pozycję Utwórz](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. W okienku **Konfiguracja aplikacji** > **Utwórz** wprowadź następujące ustawienia:

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Wprowadź unikatową nazwę zasobu, która ma być używana dla zasobu magazynu konfiguracji aplikacji. Nazwa musi być ciągiem od 5 do 50 znaków i zawierać tylko cyfry, litery i znak `-`. Nazwa nie może rozpoczynać się ani kończyć znakiem `-`.  |
    | **Subskrypcja** | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi App Configuration. Jeśli konto ma tylko jedną subskrypcję, zostanie automatycznie zaznaczone, a lista **subskrypcji** nie jest wyświetlana. |
    | **Grupa zasobów** | *AppConfigTestResources* | Wybierz lub Utwórz grupę zasobów dla zasobu magazynu konfiguracji aplikacji. Ta grupa jest przydatna do organizowania wielu zasobów, które mogą zostać usunięte w tym samym czasie przez usunięcie grupy zasobów. Więcej informacji można znaleźć w temacie [Używanie grup zasobów do zarządzania zasobami platformy Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Lokalizacja** | *Środkowe stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany magazyn konfiguracji aplikacji. Aby uzyskać najlepszą wydajność, należy utworzyć zasób w tym samym regionie, w którym są inne składniki aplikacji. |
    | **Warstwa cenowa** | *Bezpłatna* | Wybierz odpowiednią warstwę cenową. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem konfiguracji aplikacji](https://azure.microsoft.com/pricing/details/app-configuration/).

    ![Tworzenie zasobu magazynu konfiguracji aplikacji](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Wybierz pozycję **Utwórz**. Wdrożenie może potrwać kilka minut.

1. Po zakończeniu wdrażania wybierz pozycję **ustawienia** > **klucze dostępu**. Zanotuj podstawowe parametry połączenia klucza tylko do odczytu. Te parametry połączenia będą używane później, aby skonfigurować aplikację do komunikacji z utworzonym magazynem konfiguracji aplikacji.
