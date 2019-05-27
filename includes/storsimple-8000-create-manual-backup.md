---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4fc92931979aa367bdead435c3d6fd758d66a397
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66118293"
---
#### <a name="to-create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Na tabelarycznej liście urządzeń wybierz urządzenie. Przejdź kolejno do pozycji **Ustawienia > Zarządzaj > Zasady dotyczące kopii zapasowych**.

2. Blok **Zasady dotyczące kopii zapasowych** zawiera listę wszystkich zasad dotyczących kopii zapasowych w formie tabeli, w tym zasad dla woluminu, którego kopię zapasową chcesz utworzyć. Wybierz zasady skojarzone z woluminem, którego kopię zapasową chcesz utworzyć, i kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Z listy rozwijanej wybierz pozycję **Utwórz kopię zapasową teraz**.

    ![Ręczne tworzenie kopii zapasowych](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. W bloku **Utwórz kopię zapasową teraz** wykonaj następujące czynności:

    1. Wybierz odpowiedni **typ migawki** z listy rozwijanej: **Lokalne** migawki lub **chmury** migawki. Wybierz migawkę lokalną, jeśli chcesz szybko tworzyć kopie zapasowe lub je przywracać, albo migawkę w chmurze, jeśli zależy Ci na odporności danych.

        ![Ręczne tworzenie kopii zapasowych](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Kliknij przycisk **OK**, aby uruchomić zadanie tworzenia migawki. Po pomyślnym utworzeniu zadania w górnej części strony zostanie wyświetlone powiadomienie.

        ![Ręczne tworzenie kopii zapasowych](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Aby monitorować zadanie, kliknij powiadomienie. Spowoduje to przejście do bloku **Zadania**, w którym można wyświetlić postęp zadania.


5. Po zakończeniu zadania tworzenia kopii zapasowej przejdź do karty **Katalog kopii zapasowej**.

6. Ustaw wybory filtrów do odpowiedniego urządzenia, odpowiednich zasad tworzenia kopii zapasowej i odpowiedniego zakresu czasu. Kopia zapasowa powinna pojawić się na liście zestawów kopii zapasowych, która jest wyświetlana w katalogu.

