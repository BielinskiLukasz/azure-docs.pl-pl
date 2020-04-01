---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 66d3397fae24ee2546dae4eb5d7c9d188f9ede99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944113"
---
> [!NOTE]
> Za pomocą usługi Azure DNS można skonfigurować niestandardową nazwę DNS usługi Azure App Service. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](../articles/dns/dns-custom-domain.md#app-service-web-apps) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).
>
>

Zaloguj się do witryny internetowej dostawcy domeny.

Znajdź stronę służącą do zarządzania rekordami DNS. Każdy dostawca domeny ma własny interfejs rekordów DNS, dlatego zapoznaj się z dokumentacją dostawcy. Poszukaj obszarów witryny z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerami nazw**. 

Często stronę rekordów DNS można znaleźć, wyświetlając informacje o koncie, a następnie szukając linków takich jak **Moja domena**. Przejdź do tej strony, a następnie poszukaj linku o nazwie podobnej do **Plik strefy**, **Rekordy DNS** lub **Konfiguracja zaawansowana**.

Poniższy zrzut ekranu przedstawia przykład strony rekordów DNS:

![Przykładowa strona rekordów DNS](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Na ekranie z przykładu należy wybrać pozycję **Dodaj**, aby utworzyć rekord. Niektórzy dostawcy udostępniają różne linki na potrzeby dodawania różnych typów rekordów. Zapoznaj się z dokumentacją dostawcy.

> [!NOTE]
> W przypadku niektórych dostawców, np. GoDaddy, zmiany rekordów DNS nie zaczynają obowiązywać, dopóki nie wybierzesz oddzielnego linku **Zapisz zmiany**. 
