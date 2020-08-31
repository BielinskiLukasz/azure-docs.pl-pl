---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 3474a97f4b05fd1f944e18abebcef927232453e4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087798"
---
Jeśli używasz Eksploratora usługi Storage po raz pierwszy, wykonaj poniższe czynności.

1. Na górnym pasku poleceń przejdź do pozycji **edytuj > docelowy Azure Stack interfejsy API**.

    ![Konfigurowanie Eksplorator usługi Storage](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Uruchom ponownie Eksploratora usługi Storage, aby zmiany zaczęły obowiązywać.


Wykonaj poniższe czynności, aby nawiązać i zweryfikować połączenie z kontem magazynu.

1. W Eksplorator usługi Storage wybierz pozycję konta magazynu. Kliknij prawym przyciskiem myszy i wybierz opcję **Połącz z usługą Azure Storage** . 

    ![Konfigurowanie Eksplorator usługi Storage](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. W oknie dialogowym **Połącz z usługą Azure Storage** wybierz pozycję **Użyj nazwy i klucza konta magazynu**.

    ![Konfigurowanie Eksplorator usługi Storage](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. W oknie dialogowym **łączenie z nazwą i kluczem** wykonaj następujące czynności:

    1. Wprowadź nazwę wyświetlaną konta magazynu Edge. 
    2. Podaj nazwę konta magazynu Edge.
    3. Wklej klucz dostępu uzyskany od lokalnych interfejsów API urządzenia za pośrednictwem Azure Resource Manager.
    4. Wybierz opcję domena magazynu jako **inna (wprowadź poniżej)** , a następnie podaj sufiks punktu końcowego usługi BLOB w formacie: `<appliance name>.<DNSdomain>` . 
    5. Zaznacz opcję **Użyj protokołu HTTP** , ponieważ transfer jest przesyłany za pośrednictwem *protokołu HTTP*. 
    6. Wybierz pozycję **Dalej**.

    ![Konfigurowanie Eksplorator usługi Storage](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. Sprawdź informacje podane w oknie dialogowym **Podsumowanie połączenia**. Wybierz pozycję **Połącz**.

    ![Konfigurowanie Eksplorator usługi Storage](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. Pomyślnie dodane konto jest wyświetlane w lewym okienku Eksploratora usługi Storage z wartościami (Zewnętrzne, Inne) dołączonymi do jego nazwy. Wybierz **kontenery obiektów BLOB** , aby wyświetlić kontener.

    ![Wyświetlanie kontenerów obiektów BLOB](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

Następnym krokiem do sprawdzenia jest to, że transfer danych rzeczywiście działa prawidłowo nad tym połączeniem.

Wykonaj następujące kroki, aby załadować dane do konta magazynu brzegowego na urządzeniu i automatycznie uzyskać dostęp do zamapowanego konta usługi Azure Storage.

1. Wybierz kontener, do którego chcesz załadować dane z konta magazynu brzegowego. Wybierz pozycję **Przekaż** , a następnie wybierz pozycję **Przekaż pliki**.

    ![Weryfikuj transfer danych](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. W oknie dialogowym **przekazywanie plików** przejdź do i wybierz pliki, które chcesz przekazać. Wybierz pozycję **Dalej**.

    ![Weryfikuj transfer danych](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Sprawdź, czy pliki zostały przekazane. Przekazane pliki pojawią się w kontenerze.

    ![Weryfikuj transfer danych](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Następnie nawiążesz połączenie z kontem usługi Azure Storage, które zostało zamapowane na to konto magazynu Edge. Wszystkie dane przekazywane do konta magazynu brzegowego powinny być automatycznie warstwą dla konta usługi Azure Storage. 
    
    Aby uzyskać parametry połączenia dla konta usługi Azure Storage, przejdź do **konta usługi Azure storage > klucze dostępu** i skopiuj parametry połączenia.

    ![Weryfikuj transfer danych](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Użyj parametrów połączenia w celu dołączenia do konta usługi Azure Storage.  

    ![Weryfikuj transfer danych](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. Sprawdź informacje podane w oknie dialogowym **Podsumowanie połączenia**. Wybierz pozycję **Połącz**.

    ![Weryfikuj transfer danych](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Zobaczysz, że pliki przekazane na koncie magazynu brzegowego zostały przeniesione na konto usługi Azure Storage.

    ![Weryfikuj transfer danych](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)