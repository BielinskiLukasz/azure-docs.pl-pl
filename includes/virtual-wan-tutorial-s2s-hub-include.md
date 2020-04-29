---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73488938"
---
1. Zlokalizuj utworzoną wirtualną sieć WAN. Na stronie wirtualna sieć WAN w obszarze **łączność** wybierz pozycję **centra**.
2. Na stronie centra wybierz pozycję **+ nowe centrum** , aby otworzyć stronę **Tworzenie wirtualnego centrum** .

    ![Podstawy](./media/virtual-wan-tutorial-hub-include/basics.png "Podstawy")
3. Na karcie Tworzenie **podstawy** strony **wirtualnego centrum** wykonaj następujące pola:

    **Szczegóły projektu**

   * Region (wcześniej nazywany lokalizacją)
   * Nazwa
   * Prywatna przestrzeń adresowa centrum. Minimalną przestrzenią adresową jest/24, aby utworzyć centrum, co oznacza, że każdy zakres od/25 do/32 spowoduje wystąpienie błędu podczas tworzenia.
4. Wybierz pozycję **Dalej: lokacja-lokacja**.

    ![Lokacja-lokacja](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Lokacja-lokacja")

5. Na karcie **lokacja-lokacja** wykonaj następujące pola:

   * Wybierz pozycję **tak** , aby utworzyć sieć VPN typu lokacja-lokacja.
   * W tej chwili nie można edytować pola numer AS w koncentratorze wirtualnym.
   * Wybierz z listy rozwijanej wartość **jednostki skalowania bramy** . Jednostka skalowania umożliwia wybranie zagregowanej przepływności bramy sieci VPN tworzonej w koncentratorze wirtualnym w celu połączenia z lokacjami programu. Jeśli wybierzesz 1 jednostkę skalowania = 500 MB/s, oznacza to, że zostaną utworzone dwa wystąpienia nadmiarowości, z których każda będzie mieć maksymalną przepływność wynoszącą 500 MB/s. Na przykład jeśli masz pięć rozgałęzień, każda z nich wykonuje 10 MB/s w rozgałęzieniu, będzie potrzebna zagregowana liczba 50 MB/s na końcu. Planowanie zagregowanej pojemności bramy sieci VPN platformy Azure należy wykonać po ocenie pojemności wymaganej do obsługi liczby gałęzi do centrum.
6. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność.
7. Wybierz pozycję **Utwórz** , aby utworzyć centrum. Po 30 minutach **Odśwież** , aby wyświetlić centrum na stronie **centra** . Wybierz pozycję **Przejdź do zasobu** , aby przejść do zasobu.