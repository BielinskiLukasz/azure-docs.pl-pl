---
title: Pomiary rzeczywistego użytkownika ze stronami sieci Web — Azure Traffic Manager
description: W tym artykule dowiesz się, jak skonfigurować strony sieci Web do wysyłania Pomiary rzeczywistego użytkownika do usługi Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 927d774ee30a291607a8a47fc2fd6878c1bc6fee
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938699"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Jak wysyłać Pomiary rzeczywistego użytkownika do usługi Azure Traffic Manager przy użyciu stron sieci Web

Strony sieci Web można skonfigurować do wysyłania Pomiary rzeczywistego użytkownika do Traffic Manager przez uzyskanie klucza Pomiary rzeczywistego użytkownika (RUM) i osadzenie wygenerowanego kodu na stronie sieci Web.

## <a name="obtain-a-real-user-measurements-key"></a>Uzyskaj klucz Pomiary rzeczywistego użytkownika

Pomiary wykonywane i wysyłane do Traffic Manager z aplikacji klienckiej są identyfikowane przez usługę przy użyciu unikatowego ciągu, zwanego **kluczem pomiary rzeczywistego użytkownika (rum)** . Klucz RUM można uzyskać przy użyciu Azure Portal, interfejsu API REST lub polecenia programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać klucz RUM przy użyciu Azure Portal:
1. W przeglądarce Zaloguj się do Azure Portal. Jeśli jeszcze nie masz konta, możesz zarejestrować się w celu uzyskania bezpłatnej miesięcznej wersji próbnej.
2. Na pasku wyszukiwania portalu Wyszukaj nazwę profilu Traffic Manager, która ma zostać zmodyfikowana, a następnie kliknij profil Traffic Manager w wyświetlonych wynikach.
3. W bloku profil Traffic Manager kliknij pozycję **pomiary rzeczywistego użytkownika** w obszarze **Ustawienia**.
4. Kliknij przycisk **Generuj klucz** , aby utworzyć nowy klucz rum.
 
   ![Generuj klucz Pomiary rzeczywistego użytkownika](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Rysunek 1: generowanie klucza Pomiary rzeczywistego użytkownika**

5. W bloku jest teraz wyświetlany wygenerowany klucz RUM i fragment kodu JavaScript, który musi zostać osadzony na stronie HTML.
 
    ![Kod JavaScript dla klucza Pomiary rzeczywistego użytkownika](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Rysunek 2: Pomiary rzeczywistego użytkownika klucz i pomiar JavaScript**
 
6. Kliknij przycisk **Kopiuj** , aby skopiować kod JavaScript. 

>[!IMPORTANT]
> Użyj wygenerowanej funkcji JavaScript do Pomiary rzeczywistego użytkownika, aby działać prawidłowo. Wszelkie zmiany w tym skrypcie lub skrypty używane przez Pomiary rzeczywistego użytkownika mogą prowadzić do nieprzewidywalnego zachowania.

## <a name="embed-the-code-to-an-html-web-page"></a>Osadź kod na stronie sieci Web HTML

Po uzyskaniu klucza RUM następnym krokiem jest osadzenie skopiowanego kodu JavaScript na stronę HTML, którą odwiedzają użytkownicy końcowi. Edytowanie HTML można wykonać na wiele sposobów i przy użyciu różnych narzędzi i przepływów pracy. Ten przykład pokazuje, jak zaktualizować stronę HTML, aby dodać ten skrypt. Możesz użyć tych wskazówek, aby dostosować je do przepływu pracy zarządzania źródłami HTML.

1.  Otwieranie strony HTML w edytorze tekstu
2.  Wklej kod JavaScript skopiowany we wcześniejszym kroku do sekcji BODY HTML (skopiowany kod jest w wierszu 8 & 9, patrz rysunek 3).
 
    ![Osadź kod JavaScript na stronie sieci Web dla Pomiary rzeczywistego użytkownika](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Rysunek 3. prosty kod HTML z osadzonym Pomiary rzeczywistego użytkownika JavaScript**

3.  Zapisz plik HTML i Host go na serwerze webpodłączonym do Internetu. 
4. Następnym razem, gdy ta strona jest renderowana w przeglądarce sieci Web, zostanie pobrane odwołanie do kodu JavaScript, a skrypt wykona operacje pomiarów i raportowania.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [pomiary rzeczywistego użytkownika](traffic-manager-rum-overview.md)
- Dowiedz się, [jak działa Traffic Manager](traffic-manager-overview.md)
- Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez Traffic Manager
- Dowiedz się, jak [utworzyć profil Traffic Manager](traffic-manager-create-profile.md)

