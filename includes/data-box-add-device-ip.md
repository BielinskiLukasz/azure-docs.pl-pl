---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550358"
---
1. Zaloguj się do urządzenia Data Box. Upewnij się, że jest odblokowane.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Przejdź do pozycji **Ustaw interfejsy sieciowe**. Zanotuj adres IP urządzenia dla interfejsu sieciowego używanego do łączenia z klientem.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Przejdź do pozycji **Połącz i skopiuj** i kliknij przycisk **REST (wersja zapoznawcza)**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. W oknie dialogowym **Uzyskaj dostęp do konta magazynu i przekaż dane** skopiuj **Punkt końcowy usługi blob**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Uruchom program **Notatnik** jako administrator, a następnie otwórz plik **hosts** znajdujący się w folderze `C:\Windows\System32\Drivers\etc`.
6. Dodaj do pliku **hosts** następujący wpis: `<device IP address> <Blob service endpoint>`
7. Powinno to wyglądać podobnie jak na poniższej ilustracji. Zapisz plik **hosts**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
