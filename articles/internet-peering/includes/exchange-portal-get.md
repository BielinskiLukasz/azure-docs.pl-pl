---
title: dołączanie pliku
titleSuffix: Azure
description: dołączanie pliku
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678520"
---
Po pomyślnym wdrożeniu zasobu **komunikacji równorzędnej** można go wyświetlić, wykonując następujące kroki.

1. Przejdź do pozycji **grupy zasobów**i wybierz grupę zasobów wybraną podczas tworzenia zasobu **komunikacji równorzędnej** . Użyj pola **Filtr** , jeśli masz zbyt wiele grup zasobów.

    > [!div class="mx-imgBorder"]
    > ![Grupy zasobów](../media/setup-direct-get-resourcegroup.png)

1. Wybierz utworzony zasób **komunikacji równorzędnej** .

    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-direct-get-open.png)

1. Na stronie **Przegląd** są wyświetlane informacje wysokiego poziomu, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Okienko przegląd zasobów komunikacji równorzędnej](../media/setup-exchange-get-overview.png)

1. Po lewej stronie wybierz pozycję **informacje ASN** , aby wyświetlić informacje przesłane podczas tworzenia PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Informacje o WYSYŁCE zasobu komunikacji równorzędnej](../media/setup-direct-get-asninfo.png)

1. Po lewej stronie wybierz pozycję **połączenia**. W górnej części ekranu zobaczysz podsumowanie połączeń komunikacji równorzędnej między numerem ASN i firmą Microsoft w różnych obiektach w linii metra. Możesz również uzyskać dostęp do podsumowania połączeń na stronie **Przegląd** , wybierając pozycję **połączenia** w środkowym okienku, jak pokazano.

    > [!div class="mx-imgBorder"]
    > ![Połączenia zasobów komunikacji równorzędnej](../media/setup-exchange-get-connectionssummary.png)

    * **Stan połączenia** odpowiada stanowi konfiguracji połączenia komunikacji równorzędnej. Stany wyświetlane w tym polu są zgodne z diagramem stanu widocznym w [przewodniku komunikacji równorzędnej programu Exchange](../walkthrough-exchange-all.md).
    * Stan **sesji IPv4** i **stan sesji IPv6** odpowiadają odpowiednio stanom sesji IPv4 i IPv6.  
    * Po zaznaczeniu wiersza w górnej części ekranu sekcja **połączenie** na dole pokazuje szczegóły dla każdego połączenia. Wybierz strzałki, aby rozwinąć **konfigurację**, **adres IPv4**i **adres IPv6**.

    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-exchange-get-connectionsipv4.png)
