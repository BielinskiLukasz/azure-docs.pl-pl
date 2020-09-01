---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/31/2019
ms.author: alkohli
ms.openlocfilehash: 509f141939001e672112c9ff32124402174c70d2
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084958"
---
1. W [Azure Portal](https://portal.azure.com/)wybierz Azure Stack zasobów brzegowych, a następnie przejdź do **omówienia**. Urządzenie powinno być w trybie online.

2. Wybierz pozycję **+ Dodaj konto magazynu** na pasku poleceń urządzenia. 

   ![Dodawanie konta magazynu](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. W okienku **Dodawanie konta magazynu Edge** określ następujące ustawienia:

    a. Unikatowa nazwa konta magazynu brzegowego na urządzeniu. Nazwy kont magazynu mogą zawierać tylko małe litery i cyfry. Znaki specjalne są niedozwolone. Nazwa konta magazynu musi być unikatowa w obrębie urządzenia (nie na urządzeniach).

    b. Opcjonalny opis informacji dotyczących danych, które są przechowywane na koncie magazynu.  
    
    c. Domyślnie konto magazynu brzegowego jest mapowane na konto usługi Azure Storage w chmurze, a dane z konta magazynu są automatycznie wypychane do chmury. Określ konto usługi Azure Storage, do którego jest zamapowana konto magazynu Edge.  

    d. Następnie utwórz nowy kontener lub wybierz go z istniejącego kontenera na koncie usługi Azure Storage. Wszystkie dane z urządzenia, które są zapisywane na koncie magazynu brzegowego, są automatycznie przekazywane do wybranego kontenera magazynu na mapowanym koncie usługi Azure Storage.

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. Po określeniu wszystkich opcji konta magazynu wybierz pozycję **Dodaj** , aby utworzyć konto magazynu Edge. Otrzymasz powiadomienie o pomyślnym utworzeniu konta magazynu Edge. Nowe konto magazynu Edge zostanie wyświetlone na liście kont magazynu w Azure Portal. 

    
4. W przypadku wybrania tego nowego konta magazynu i przejścia do opcji **klucze dostępu**można znaleźć punkt końcowy usługi BLOB i nazwę konta magazynu. Skopiuj te informacje wraz z kluczami dostępu, aby nawiązać połączenie z kontem magazynu Edge.

    ![Dodawanie konta magazynu](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    Klucze dostępu można uzyskać, łącząc się z [lokalnymi interfejsami API urządzenia przy użyciu Azure Resource Manager](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md). 