---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183263"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Aby zainstalować regularne poprawki za pośrednictwem program Windows PowerShell dla usługi StorSimple
1. Nawiąż połączenie z konsolą szeregową urządzenia. Aby uzyskać więcej informacji, zobacz [krok 1. Nawiązywanie połączenia z konsolą szeregową](../articles/storsimple/storsimple-update-device.md#step1).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**. Wpisz hasło. Domyślne hasło to **Password1**.
3. W wierszu polecenia wpisz polecenie:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > To polecenie dotyczy tylko zwykłych poprawek. To polecenie jest uruchamiane tylko na jednym kontrolerze, ale oba kontrolery zostaną zaktualizowane.
    > Podczas procesu aktualizacji można zauważyć tryb failover kontrolera; Jednak w trybie failover nie wpłynie to na dostępność systemu ani działanie.

4. Po wyświetleniu monitu podaj ścieżkę do udostępnionego folderu sieciowego, który zawiera pliki poprawek.
5. Pojawi się monit o potwierdzenie. Wpisz **Y** , aby kontynuować instalację poprawki.

