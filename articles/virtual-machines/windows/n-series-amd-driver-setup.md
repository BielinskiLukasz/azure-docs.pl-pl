---
title: Konfiguracja sterowników procesora GPU AMD na platformie Azure dla systemu Windows
description: Jak skonfigurować sterowniki AMD GPU dla maszyn wirtualnych serii N z systemem Windows Server lub Windows na platformie Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: b62095a3dc48480d8b1d33328d2d0dc25470d763
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461094"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalowanie sterowników AMD GPU na maszynach wirtualnych serii N z systemem Windows

Aby skorzystać z możliwości procesora GPU nowych maszyn wirtualnych z serii Azure NVv4 z systemem Windows, należy zainstalować sterowniki procesora GPU AMD. [Rozszerzenie sterownika procesora GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) instaluje sterowniki procesora GPU AMD na maszynie wirtualnej z serii NVv4. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika procesora GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) , aby poznać obsługiwane systemy operacyjne i kroki wdrażania.

W przypadku wybrania opcji ręcznego instalowania sterowników procesora GPU firmy AMD w tym artykule przedstawiono obsługiwane systemy operacyjne, sterowniki oraz czynności instalacyjne i weryfikacyjne.

Na maszynach wirtualnych NVv4 są obsługiwane tylko sterowniki procesora GPU opublikowane przez firmę Microsoft. NIE instaluj sterowników procesora GPU z innych źródeł.

Aby zapoznać się z podstawowymi danymi, pojemnościami magazynu i szczegółami dysku, zobacz [rozmiary maszyn wirtualnych w systemie Windows GPU](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

| System operacyjny | Sterownik |
| -------- |------------- |
| Wiele sesji dla systemu Windows 10 Enterprise — kompilacja 1903 <br/><br/>Windows 10 — kompilacja 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.17](https://download.microsoft.com/download/d/e/f/def0fb44-15ab-4b83-959a-8094eb9d0dfe/AMD-Azure-NVv4-Driver-20Q1-Hotfix3.exe) (. exe) |


## <a name="driver-installation"></a>Instalacja sterownika

1. Połącz się Pulpit zdalny z każdą maszyną wirtualną serii NVv4.

2. Jeśli chcesz odinstalować poprzednią wersję sterownika, Pobierz narzędzie do oczyszczania AMD w [tym miejscu](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) , nie używaj narzędzia dołączonego do poprzedniej wersji sterownika.

3. Pobierz i zainstaluj najnowszą wersję sterownika.

4. Uruchom ponownie maszynę wirtualną.

## <a name="verify-driver-installation"></a>Weryfikuj instalację sterownika

Instalację sterownika można sprawdzić w Menedżer urządzeń. W poniższym przykładzie przedstawiono pomyślną konfigurację karty Instinct MI25 na maszynie wirtualnej Azure NVv4.
<br />

![Zrzut ekranu przedstawiający pomyślną konfigurację karty Instinct Radeon MI25 na maszynie wirtualnej Azure NVv4.](./media/n-series-amd-driver-setup/device-manager.png)

Możesz użyć programu dxdiag do zweryfikowania właściwości wyświetlania procesora GPU, w tym pamięci RAM wideo. Poniższy przykład przedstawia partycję 1/2 karty Instinct Radeon MI25 na maszynie wirtualnej Azure NVv4.
<br />
![Zrzut ekranu przedstawiający 1/2ą partycję karty usługi Radeon Instinct MI25 na maszynie wirtualnej Azure NVv4.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Jeśli korzystasz z systemu Windows 10 Build 1903 lub nowszego, program DxDiag nie wyświetli żadnych informacji na karcie "Display" (wyświetlanie). Użyj opcji "Zapisz wszystkie informacje" u dołu, a w pliku wyjściowym zostaną wyświetlone informacje związane z procesorem GPU AMD MI25.

![Właściwości sterownika procesora GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)
