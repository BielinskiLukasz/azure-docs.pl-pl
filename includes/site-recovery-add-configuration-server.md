---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/06/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 2ca4916d48da6fe8a2c061056a1ea0fed9a78bb6
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44058346"
---
1. Uruchom plik instalacyjny ujednoliconego instalatora.
2. W **przed rozpoczęciem**, wybierz opcję **Zainstaluj serwer konfiguracji i serwer przetwarzania**.

    ![Przed rozpoczęciem](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. W obszarze **Licencja na oprogramowanie innej firmy** kliknij pozycję **Akceptuję**, aby pobrać i zainstalować program MySQL.

    ![Oprogramowanie innych producentów](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. W obszarze **Rejestracja** wybierz klucz rejestracji pobrany z magazynu.

    ![Rejestracja](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. W obszarze **Ustawienia internetowe** określ, jak dostawca działający na serwerze konfiguracji ma się łączyć z usługą Azure Site Recovery przez Internet. Upewnij się, że zezwolono wymaganego adresów URL.

    - Jeśli chcesz się połączyć z serwerem proxy, który jest obecnie skonfigurowane na maszynie, wybierz opcję **nawiązywanie połączenia z usługi Azure Site Recovery przy użyciu serwera proxy**.
    - Jeśli chcesz, aby dostawca łączył się bezpośrednio, wybierz opcję **Połącz bezpośrednio z usługi Azure Site Recovery bez serwera proxy**.
    - Jeśli istniejący serwer proxy wymaga uwierzytelniania lub chcesz użyć niestandardowego serwera proxy dla połączenia dostawcy, wybierz opcję **Połącz przy użyciu niestandardowych ustawień serwera proxy**, a następnie określ adres, port i poświadczenia.
     ![Zapora](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. W kroku **Sprawdzanie wymagań wstępnych** Instalator przeprowadza kontrolę w celu upewnienia się, że można uruchomić instalację. Jeśli zostanie wyświetlone ostrzeżenie dotyczące **kontroli synchronizacji czasu globalnego**, sprawdź, czy czas zegara systemowego (ustawienia **Data i godzina**) jest taki sam jak dla strefy czasowej.

    ![Wymagania wstępne](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. W obszarze **Konfiguracja programu MySQL** utwórz poświadczenia do logowania się do zainstalowanego wystąpienia serwera programu MySQL.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. W **szczegóły środowiska**, wybierz opcję nie, Jeśli replikujesz maszyny wirtualne platformy Azure Stack lub serwerów fizycznych. 
9. W obszarze **Lokalizacja instalacji** wybierz, gdzie mają zostać zainstalowane pliki binarne i gdzie ma być przechowywana pamięć podręczną. Na wybranym dysku musi być co najmniej 5 GB dostępnego miejsca, ale zalecamy dysk pamięci podręcznej z co najmniej 600 GB wolnego miejsca.

    ![Lokalizacja instalacji](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. W obszarze **Wybór sieci** określ odbiornik (kartę sieciową i port SSL), za pomocą którego serwer konfiguracji będzie wysyłać i odbierać dane replikacji. Port 9443 jest domyślnym portem używanym do wysyłania i odbierania ruchu związanego z replikacją, ale możesz zmienić ten numer portu tak, aby odpowiadał wymaganiom Twojego środowiska. Oprócz portu 9443 otwieramy też port 443, który jest używany przez serwer sieci Web do organizowania operacji replikacji. Nie należy używać portu 443 do wysyłania i odbierania ruchu związanego z replikacją.

    ![Wybór sieci](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. W obszarze **Podsumowanie** przejrzyj informacje i kliknij przycisk **Zainstaluj**. Po zakończeniu instalacji generowane jest hasło. Będzie ono potrzebne po włączeniu replikacji, dlatego skopiuj je i przechowuj w bezpiecznym miejscu.

    ![Podsumowanie](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Po zakończeniu rejestracji serwer jest wyświetlany w bloku **Ustawienia** > **Serwery** w magazynie.
