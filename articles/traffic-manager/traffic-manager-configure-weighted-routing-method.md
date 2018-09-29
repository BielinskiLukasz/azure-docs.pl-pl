---
title: Konfigurowanie metody routingu ruchu w ważone działanie okrężne przy użyciu usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak Równoważenie obciążenia ruchem przy użyciu metody działanie okrężne w usłudze Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 6637132481ee33d43ec2b747ba89a56983205ff2
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432447"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Konfigurowanie metody routingu ważonego ruchu w usłudze Traffic Manager

Typowym wzorcem metody routingu ruchu jest udostępniają zestaw identyczne punkty końcowe, które obejmują usług w chmurze i witryn sieci Web, i przesyłać dane do każdego w działaniu okrężnym. Następujące kroki przedstawiają sposób konfigurowania tego rodzaju metody routingu ruchu.

> [!NOTE]
> Usługa Azure Web Apps zapewnia już funkcji dla witryn sieci Web w obrębie regionu platformy Azure (zawiera wiele centrów danych) równoważenia obciążenia działania okrężnego. Usługa Traffic Manager umożliwia określanie metody routingu ruchu działania okrężnego dla witryn sieci Web w różnych centrach danych.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Aby skonfigurować metody routingu ważonego ruchu

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](http://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/). 
2. Na pasku wyszukiwania portalu, wyszukaj **profile usługi Traffic Manager** a następnie kliknij nazwę profilu, który chcesz skonfigurować metody routingu dla.
3. W **profilu usługi Traffic Manager** bloku, sprawdź, czy obecne są usługami w chmurze i witryn sieci Web, które mają zostać uwzględnione w konfiguracji.
4. W **ustawienia** kliknij **konfiguracji**, a następnie w **konfiguracji** bloku, podaj następujące:
    1. Aby uzyskać **ustawienia metody routingu ruchu**, sprawdź, czy metodę routingu ruchu **ważona**. Jeśli nie, kliknij pozycję **ważona** z listy rozwijanej.
    2. Ustaw **ustawienia monitora punktu końcowego** identyczne dla wszystkich każdego punktu końcowego w ramach tego profilu, w następujący sposób:
        1. Wybierz odpowiedni **protokołu**, a następnie określ **portu** numer. 
        2. Aby uzyskać **ścieżki** wpisz ukośnik */*. Monitorowanie punktów końcowych, należy określić ścieżkę i nazwę pliku. A ukośnika "/" jest prawidłowym wpisem ścieżki względnej i oznacza, że plik znajduje się w katalogu głównym (ustawienie domyślne).
        3. W górnej części strony kliknij **Zapisz**.
5. Przetestuj zmiany w konfiguracji w następujący sposób:
    1.  Paska wyszukiwania portalu, wyszukaj nazwę profilu usługi Traffic Manager, a następnie kliknij pozycję profil usługi Traffic Manager w taki sposób, w wynikach, wyświetlana.
    2.  W **usługi Traffic Manager** bloku profilu, kliknij przycisk **Przegląd**.
    3.  **Profilu usługi Traffic Manager** bloku Wyświetla nazwę DNS nowo utworzonego profilu usługi Traffic Manager. Może to służyć przez dowolnego klienta (na przykład, przechodząc do niego w przeglądarce sieci web) Pobierz kierowane do prawego punktu końcowego jako ustalany na podstawie typu routingu. W takim przypadku wszystkie żądania są kierowane każdego punktu końcowego w działaniu okrężnym.
6. Po profilu usługi Traffic Manager działa, należy edytować rekord DNS na autorytatywny serwer DNS, aby wskazywała nazwę domeny firmowej na nazwę domeny usługi Traffic Manager.

![Konfigurowanie metody routingu ważonego ruchu przy użyciu usługi Traffic Manager][1]

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [priorytetu metody routingu ruchu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [wydajności metodę routingu ruchu](traffic-manager-configure-performance-routing-method.md).
- Dowiedz się więcej o [metody routingu geograficznego](traffic-manager-configure-geographic-routing-method.md).
- Dowiedz się, jak [testowanie ustawień usługi Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
