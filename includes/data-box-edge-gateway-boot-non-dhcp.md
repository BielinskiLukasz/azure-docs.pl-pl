---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183899"
---
Jeśli uruchomisz się w środowisku nieobjętym dhcp, wykonaj następujące kroki, aby wdrożyć maszynę wirtualną dla bramy pola danych.

1. [Połącz się z interfejsem programu Windows PowerShell urządzenia](#connect-to-the-powershell-interface).
2. Użyj `Get-HcsIpAddress` polecenia cmdlet, aby wyświetlić listę interfejsów sieciowych włączonych na urządzeniu wirtualnym. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

    W poniższym przykładzie przedstawiono użycie tego polecenia cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Zobacz poniższy przykład:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

