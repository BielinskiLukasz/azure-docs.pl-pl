---
title: Konfigurowanie równoważenia obciążenia systemu Windows Virtual Desktop — Azure
description: Jak skonfigurować metodę równoważenia obciążenia dla środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d5c39ff867add80833ee522ef173506fa1c642c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204350"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurowanie metody równoważenia obciążenia usługi Windows Virtual Desktop

Skonfigurowanie metody równoważenia obciążenia dla puli hostów umożliwia dostosowanie środowiska pulpitu wirtualnego systemu Windows w celu lepszego dopasowania do Twoich potrzeb.

>[!NOTE]
> Nie dotyczy to trwałej puli hostów komputerów stacjonarnych, ponieważ użytkownicy mają zawsze mapowanie 1:1 do hosta sesji w puli hostów.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że wykonano instrukcje opisane w temacie [Konfigurowanie modułu PowerShell pulpitu wirtualnego systemu Windows](powershell-module.md) w celu pobrania i zainstalowania modułu programu PowerShell oraz zalogowania się do konta platformy Azure.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurowanie równoważenia obciążenia w pierwszej kolejności

Pierwsze Równoważenie obciążenia jest konfiguracją domyślną dla nowych pul hostów nietrwałych. Równoważenie obciążenia w pierwszej kolejności dystrybuuje nowe sesje użytkowników między wszystkimi dostępnymi hostami sesji w puli hostów. Podczas konfigurowania równoważenia obciążenia w pierwszej kolejności można ustawić maksymalny limit sesji dla każdego hosta sesji w puli hostów.

Aby skonfigurować pulę hostów do wykonywania równoważenia obciążenia w pierwszej kolejności bez dostosowywania limitu liczby sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

Aby upewnić się, że ustawiono metodę równoważenia obciążenia pierwszej szerokości, uruchom następujące polecenie cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

Aby skonfigurować pulę hostów do wykonywania równoważenia obciążenia w pierwszej kolejności i użyć nowego limitu maksymalnej liczby sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurowanie pierwszej równoważenia obciążenia

Usługa Równoważenie obciążenia po raz pierwszy dystrybuuje nowe sesje użytkowników do dostępnego hosta sesji o największej liczbie połączeń, ale nie osiągnął progu limitu liczby sesji. Podczas konfigurowania równoważenia obciążenia najpierw należy ustawić maksymalny limit sesji dla każdego hosta sesji w puli hostów.

Aby skonfigurować pulę hostów do wykonywania równoważenia obciążenia w pierwszej kolejności, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

Aby upewnić się, że to ustawienie zostało zaktualizowane, uruchom następujące polecenie cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Konfigurowanie równoważenia obciążenia za pomocą Azure Portal

Można także skonfigurować równoważenie obciążenia za pomocą Azure Portal.

Aby skonfigurować równoważenie obciążenia:

1. Zaloguj się do Azure Portal pod adresem https://portal.azure.com .
2. Wyszukaj i wybierz pozycję **pulpit wirtualny systemu Windows** w obszarze usługi.
3. Na stronie pulpit wirtualny systemu Windows wybierz pozycję **Pule hostów**.
4. Wybierz nazwę puli hostów, którą chcesz edytować.
5. Wybierz pozycję **Właściwości**.
6. Wprowadź **maksymalną liczbę sesji** do pola i wybierz **algorytm równoważenia obciążenia** dla tej puli hostów w menu rozwijanym.
7. Wybierz pozycję **Zapisz**. Spowoduje to zastosowanie nowych ustawień równoważenia obciążenia.