---
title: Dostosowywanie właściwości RDP przy użyciu programu PowerShell — Azure
description: Jak dostosować właściwości protokołu RDP dla pulpitu wirtualnego systemu Windows przy użyciu poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128068"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Dostosowywanie Remote Desktop Protocol właściwości dla puli hostów

Dostosowanie właściwości Remote Desktop Protocol puli hostów (RDP), takich jak środowisko monitorowania i przekierowania audio, zapewnia optymalne środowisko dla użytkowników zgodnie z ich potrzebami. Właściwości protokołu RDP można dostosować na pulpicie wirtualnym systemu Windows przy użyciu parametru **-CustomRdpProperty** w poleceniu cmdlet **Set-RdsHostPool** .

Zobacz [obsługiwane ustawienia plików RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) , aby uzyskać pełną listę obsługiwanych właściwości i ich wartości domyślne.

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono. Następnie uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Domyślne właściwości protokołu RDP

Domyślnie publikowane pliki RDP zawierają następujące właściwości:

|Właściwości RDP | Komputery stacjonarne | RemoteApps |
|---|---| --- |
| Tryb z obsługą kilku monitorów | Enabled (Włączony) | Nie dotyczy |
| Przekierowania dysków włączone | Dyski, schowek, drukarki, porty COM, urządzenia USB i karty inteligentne| Dyski, schowek i drukarki |
| Tryb zdalny audio | Odtwórz lokalnie | Odtwórz lokalnie |

Wszystkie właściwości niestandardowe zdefiniowane dla puli hostów przesłonią te wartości domyślne.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Dodaj lub Edytuj pojedynczą niestandardową Właściwość RDP

Aby dodać lub edytować pojedynczą niestandardową Właściwość RDP, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Dodawanie lub Edytowanie wielu niestandardowych właściwości RDP

Aby dodać lub edytować wiele niestandardowych właściwości RDP, uruchom następujące polecenia cmdlet programu PowerShell, dostarczając niestandardowe właściwości protokołu RDP jako ciąg rozdzielony średnikami:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Zresetuj wszystkie niestandardowe właściwości RDP

Możesz zresetować pojedyncze niestandardowe właściwości protokołu RDP do wartości domyślnych, postępując zgodnie z instrukcjami w temacie [Dodawanie lub edytowanie pojedynczej niestandardowej właściwości RDP](#add-or-edit-a-single-custom-rdp-property), albo Zresetuj wszystkie niestandardowe właściwości protokołu RDP dla puli hostów, uruchamiając następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Następne kroki

Teraz, po dostosowaniu właściwości RDP dla danej puli hostów, można zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować je w ramach sesji użytkownika. Te następne dwie porady informują Cię, jak nawiązać połączenie z sesją przy użyciu wybranego przez siebie klienta:

- [Nawiązywanie połączenia z klientem klasycznym systemu Windows](connect-windows-7-and-10.md)
- [Nawiązywanie połączenia z klientem sieci Web](connect-web.md)
