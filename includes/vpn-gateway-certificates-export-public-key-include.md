---
title: dołączanie pliku
description: dołączanie pliku
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f71703dcb6afb5ce7610bf1c5374de1cc98816b0
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606417"
---
Po utworzeniu certyfikatu głównego z podpisem własnym wyeksportuj plik CER klucza publicznego certyfikatu głównego (nie klucz prywatny). Później przekażesz ten plik do platformy Azure. Poniższe kroki ułatwiają wyeksportowanie pliku CER dla certyfikatu głównego z podpisem własnym:

1. Aby uzyskać plik cer z certyfikatu, otwórz okno **Zarządzaj certyfikatami użytkowników**. Zlokalizuj certyfikat główny z podpisem własnym — zwykle znajduje się w katalogu „Certyfikaty - bieżący użytkownik\Osobisty\Certyfikat” — a następnie kliknij go prawym przyciskiem myszy. Kliknij pozycję **Wszystkie zadania**, a następnie kliknij pozycję **Eksportuj**. Spowoduje to otwarcie **Kreatora eksportu certyfikatów**. Jeśli nie możesz znaleźć certyfikatu w ramach bieżącego User\Personal\Certificates, być może przypadkowo otwarto "certyfikaty — komputer lokalny", a nie "Certyfikaty — bieżący użytkownik"). Jeśli chcesz otworzyć Menedżera certyfikatów w bieżącym zakresie użytkownika przy użyciu programu PowerShell, wpisz *certmgr* w oknie konsoli.

   ![Zrzut ekranu przedstawia okno certyfikaty dla bieżącego użytkownika z wybranymi certyfikatami i menu kontekstowe ze wszystkimi zadaniami, a następnie eksportuje wybrane.](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. W Kreatorze kliknij pozycję **Dalej**.

   ![Eksportowanie certyfikatu](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Wybierz pozycję **Nie eksportuj klucza prywatnego**, a następnie kliknij pozycję **Dalej**.

   ![Nie Eksportuj klucza prywatnego](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Na stronie **Format pliku eksportu** wybierz pozycję **Certyfikat X.509 szyfrowany algorytmem Base-64 (.CER)**, a następnie kliknij pozycję **Dalej**.

   ![Base-64 — zakodowana](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. W polu **plik do wyeksportowania** **Przejdź** do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **dalej**.

   ![Zrzut ekranu pokazuje Kreatora eksportu certyfikatów z polem tekstowym Nazwa pliku i opcją przeglądania.](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![Zrzut ekranu przedstawia końcową stronę Kreatora eksportu certyfikatów z wybranymi ustawieniami.](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Certyfikat został pomyślnie wyeksportowany.

   ![Zrzut ekranu przedstawia komunikat informujący o tym, że eksport zakończył się pomyślnie.](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Wyeksportowany certyfikat wygląda podobnie do tego:

   ![Zrzut ekranu przedstawia ikonę certyfikatu i nazwę pliku z rozszerzeniem nazwy pliku c e r.](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Jeśli otworzysz wyeksportowany certyfikat przy użyciu Notatnika, zobaczysz coś podobnego do tego przykładu. Sekcja w kolorze niebieskim zawiera informacje przekazane do platformy Azure. Jeśli otworzysz certyfikat przy użyciu programu Notepad i nie będzie on wyglądać podobnie do tego, zazwyczaj oznacza to, że nie został on wyeksportowany przy użyciu Base-64 szyfrowanego X. 509 (. CER). Ponadto, jeśli chcesz użyć innego edytora tekstów, należy zrozumieć, że niektóre edytory mogą wprowadzić niezamierzone formatowanie w tle. Może to spowodować problemy podczas przekazywania tekstu z tego certyfikatu do platformy Azure.

   ![Otwórz za pomocą Notatnika](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
