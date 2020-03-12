---
title: Rozwiązywanie problemów z klientem Pulpit zdalny Windows Virtual Desktop — Azure
description: Jak rozwiązywać problemy podczas konfigurowania połączeń klienta w środowisku dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127510"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Rozwiązywanie problemów z klientem Pulpit zdalny

W tym artykule opisano typowe problemy z klientem Pulpit zdalny i sposoby ich naprawiania.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Klient Pulpit zdalny dla systemu Windows 7 lub Windows 10 przestaje odpowiadać lub nie może zostać otwarty

Użyj następujących poleceń cmdlet programu PowerShell, aby wyczyścić rejestry klientów poza pasmem (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Przejdź do **%AppData%\RdClientRadc** i Usuń całą zawartość.

Odinstaluj i ponownie zainstaluj program Pulpit zdalny Client dla systemu Windows 7 i Windows 10.

## <a name="web-client-wont-open"></a>Klient sieci Web nie zostanie otwarty

Najpierw Przetestuj połączenie internetowe, otwierając inną witrynę sieci Web w przeglądarce. na przykład [www.Bing.com](https://www.bing.com).

Aby potwierdzić, że serwer DNS może rozpoznać nazwę FQDN, użyj **polecenia nslookup** :

```cmd
nslookup rdweb.wvd.microsoft.com
```

Spróbuj nawiązać połączenie z innym klientem, takim jak Pulpit zdalny Client dla systemu Windows 7 lub Windows 10, i sprawdź, czy można otworzyć klienta sieci Web.

### <a name="opening-another-site-fails"></a>Otwieranie innej lokacji nie powiodło się

Jest to zazwyczaj spowodowane przez problemy z połączeniem sieciowym lub awarią sieci. Zalecamy skontaktowanie się z pomocą techniczną.

### <a name="nslookup-cannot-resolve-the-name"></a>Narzędzie Nslookup nie może rozpoznać nazwy

Jest to zazwyczaj spowodowane przez problemy z połączeniem sieciowym lub awarią sieci. Zalecamy skontaktowanie się z pomocą techniczną.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Klient nie może nawiązać połączenia, ale może nawiązać połączenie z innymi klientami w sieci

Jeśli przeglądarka zacznie działać lub kończy pracę w trakcie korzystania z klienta sieci Web, postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać ten problem:

1. Uruchom ponownie przeglądarkę.
2. Wyczyść pliki cookie przeglądarki. Zobacz [Jak usunąć pliki cookie w programie Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Wyczyść pamięć podręczną przeglądarki. Zobacz [Wyczyść pamięć podręczną przeglądarki w przeglądarce](https://binged.it/2RKyfdU).
4. Otwórz przeglądarkę w trybie prywatnym.

## <a name="web-client-stops-responding-or-disconnects"></a>Klient sieci Web przestaje odpowiadać lub rozłącza

Spróbuj połączyć się przy użyciu innej przeglądarki lub klienta.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Inne przeglądarki i klienci również działają nieprawidłowo lub nie mogą otworzyć

Jeśli problemy będą nadal występować nawet po przełączeniu przeglądarki, problem może nie być w przeglądarce, ale w sieci. Zalecamy skontaktowanie się z pomocą techniczną.

## <a name="web-client-keeps-prompting-for-credentials"></a>Klient sieci Web ciągle wyświetla monit o poświadczenia

Jeśli klient sieci Web wyświetla monit o podanie poświadczeń, wykonaj następujące instrukcje:

1. Upewnij się, że adres URL klienta sieci Web jest poprawny.
2. Upewnij się, że używane poświadczenia są przeznaczone dla środowiska pulpitu wirtualnego systemu Windows powiązanego z adresem URL.
3. Wyczyść pliki cookie przeglądarki. Aby uzyskać więcej informacji, zobacz [Jak usunąć pliki cookie w programie Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Wyczyść pamięć podręczną przeglądarki. Aby uzyskać więcej informacji, zobacz [Wyczyść pamięć podręczną przeglądarki dla przeglądarki](https://binged.it/2RKyfdU).
5. Otwórz przeglądarkę w trybie prywatnym.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).