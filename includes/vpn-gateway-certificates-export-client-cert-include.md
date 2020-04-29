---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059968"
---
Po wygenerowaniu certyfikatu klienta jest on automatycznie instalowany na komputerze użytym do jego wygenerowania. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, musisz wyeksportować wygenerowany certyfikat klienta.

1. Aby wyeksportować certyfikat klienta, otwórz okno **Zarządzaj certyfikatami użytkowników**. Wygenerowane certyfikaty klienta są domyślnie zlokalizowane w temacie "Certificates-Current User\Personal\Certificates". Kliknij prawym przyciskiem myszy certyfikat klienta, który chcesz wyeksportować, kliknij pozycję **wszystkie zadania**, a następnie kliknij pozycję **Eksportuj** , aby otworzyć **Kreatora eksportu certyfikatów**.

   ![Eksportowanie](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. W Kreatorze eksportu certyfikatów kliknij przycisk **dalej** , aby kontynuować.

   ![Następne kroki](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Wybierz opcję **tak, eksportuj klucz prywatny**, a następnie kliknij przycisk **dalej**.

   ![Eksportuj klucz prywatny](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stronie **Format pliku eksportu** pozostaw wybrane wartości domyślne. Upewnij się, że jest zaznaczona pozycja **Jeśli jest to możliwe, dołącz wszystkie certyfikaty do ścieżki certyfikacji**. To ustawienie dodatkowo eksportuje informacje o certyfikacie głównym, które są wymagane do pomyślnego uwierzytelnienia klienta. Bez tego uwierzytelnianie klienta nie powiedzie się, ponieważ klient nie ma zaufanego certyfikatu głównego. Następnie kliknij przycisk **dalej**.

   ![Format pliku eksportu](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stronie **Zabezpieczenia** należy włączyć ochronę klucza prywatnego. Jeśli wybierzesz opcję użycia hasła, zapisz lub zapamiętaj hasło ustawione dla tego certyfikatu. Następnie kliknij przycisk **dalej**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. W obszarze **Eksport pliku** wybierz pozycję **Przeglądaj**, aby przejść do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **dalej**.

   ![plik do wyeksportowania](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![zakończ](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)