---
title: plik dołączany
description: plik dołączany
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: f738daab7ddcf0403f546e7c9ffeaeccb66bc6b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355651"
---
## <a name="create-an-azure-signalr-service-instance"></a>Tworzenie wystąpienia usługi Azure SignalR Service

Twoja aplikacja nawiąże połączenie z wystąpieniem usługi SignalR na platformie Azure.

1. Wybierz przycisk Nowy znajdujący się w lewym górnym rogu witryny Azure Portal. Na ekranie Nowy wpisz nazwę *SignalR Service* w polu wyszukiwania, a następnie naciśnij klawisz Enter.

    ![Zrzut ekranu przedstawia wyszukiwanie usługi sygnalizującego w Azure Portal.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Wybierz pozycję **SignalR Service** w wynikach wyszukiwania, a następnie wybierz pozycję **Utwórz**.

1. Wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nowe wystąpienie usługi SignalR Service. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzone to nowe wystąpienie usługi SignalR Service. | 
    | **[Grupa zasobów](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzone wystąpienie usługi SignalR Service. | 
    | **Lokalizacja** | Zachodnie stany USA | Wybierz [region](https://azure.microsoft.com/regions/) blisko siebie. |
    | **Warstwa cenowa** | Bezpłatna | Wypróbuj bezpłatnie usługę Azure SignalR Service. |
    | **Liczba jednostek** |  Nie dotyczy | Liczba jednostek określa liczbę połączeń, które może akceptować wystąpienie usługi SignalR Service. Można to skonfigurować tylko w warstwie Standardowa. |
    | **Tryb usługi** |  Praca bezserwerowa | Do użytku z interfejsem API REST Azure Functions lub. |

    ![Zrzut ekranu przedstawia kartę podstawy sygnałów z wartościami.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie wystąpienia usługi SignalR Service.

1. Po wdrożeniu wystąpienia otwórz je w portalu i Znajdź jego stronę ustawień. Zmień ustawienie trybu usługi na *bezserwerowe* tylko wtedy, gdy korzystasz z usługi Azure Signal Service za pośrednictwem Azure Functions powiązania lub interfejsu API REST. Pozostaw je w trybie *klasycznym* lub *domyślnym* , w przeciwnym razie.