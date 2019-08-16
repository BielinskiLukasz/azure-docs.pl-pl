---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Przydziel publiczne adresy IP
description: Opisuje sposób przydzielania publicznych adresów IP maszynom wirtualnym w środowisku chmury prywatnej
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 12a6af73dc0abc9598184f6c83e9d7652973a99e
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544450"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Przydzielanie publicznych adresów IP dla środowiska chmury prywatnej

Aby przydzielić publiczne adresy IP maszyn wirtualnych w środowisku chmury prywatnej, Otwórz na stronie sieć kartę publiczna IP.

1. [Uzyskaj dostęp do portalu CloudSimple](monitor-activity.md) i wybierz pozycję **Sieć** w menu po stronie.
2. Wybierz pozycję **publiczne adresy IP**.
3. Kliknij pozycję **Nowy publiczny adres IP**.

    ![Strona publiczne adresy IP](media/public-ips-page.png)

4. Wprowadź nazwę identyfikującą wpis adresu IP.
5. Zachowaj lokalizację domyślną.
6. Użyj suwaka, aby zmienić limit czasu bezczynności, jeśli jest to konieczne.
7. Wprowadź lokalny adres IP, dla którego chcesz przypisać publiczny adres IP.
8. Wprowadź skojarzoną nazwę DNS.
9. Kliknij przycisk **Submit** (Prześlij).

![Przydziel publiczne adresy IP](media/network-public-ip-allocate.png)

Zostanie rozpoczęte zadanie alokowania publicznego adresu IP. Stan zadania można sprawdzić na stronie **zadania > zadań** . Po zakończeniu alokacji nowy wpis zostanie wyświetlony na stronie publiczne adresy IP.
