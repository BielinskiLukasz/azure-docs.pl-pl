---
title: Tworzenie maszyny wirtualnej z systemem Windows w strefie przy użyciu Azure Portal | Microsoft Docs
description: Tworzenie maszyny wirtualnej z systemem Windows w strefie dostępności przy użyciu Azure Portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: af2295643869ba8022fe15cf1e1cef3f2f20a428
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849843"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Utwórz maszynę wirtualną z systemem Windows w strefie dostępności przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia maszyny wirtualnej w strefie dostępności platformy Azure przy użyciu Azure Portal. [Strefa dostępności](../../availability-zones/az-overview.md) to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby użyć strefy dostępność, utwórz maszynę wirtualną w [obsługiwanym regionie platformy Azure](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 

3. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu są używane na potrzeby logowania się do maszyny wirtualnej. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm). Wybierz lokalizację, na przykład Wschodnie stany USA 2, która obsługuje strefy dostępności. Po zakończeniu kliknij przycisk **OK**.

    ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Wybierz rozmiar maszyny wirtualnej. Wybierz zalecany rozmiar lub filtr oparty na funkcjach. Upewnij się, że rozmiar jest dostępny w strefie, której chcesz użyć.

    ![Wybierz rozmiar maszyny wirtualnej](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. W obszarze **Ustawienia** > **wysoka dostępność**wybierz jedną z numerowanych stref na liście rozwijanej **strefa dostępności** , Zachowaj pozostałe wartości domyślne i kliknij przycisk **OK**.

    ![Wybierz strefę dostępności](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na stronie Podsumowanie kliknij przycisk **Utwórz** , aby rozpocząć wdrażanie maszyny wirtualnej.

7. Maszyna wirtualna zostanie przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrażania zostanie automatycznie otwarte podsumowanie maszyny wirtualnej.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potwierdź strefę dla dysku zarządzanego i adresu IP

Po wdrożeniu maszyny wirtualnej w strefie dostępności dysk zarządzany dla maszyny wirtualnej jest tworzony w tej samej strefie dostępności. Domyślnie publiczny adres IP jest również tworzony w tej strefie.

Ustawienia strefy dla tych zasobów można potwierdzić w portalu.  

1. Kliknij pozycję **grupy zasobów** , a następnie nazwę grupy zasobów dla maszyny wirtualnej, na przykład grupę *zasobów.*

2. Kliknij nazwę zasobu dyskowego. Strona **Przegląd** zawiera szczegółowe informacje o lokalizacji i strefie dostępności zasobu.

    ![Strefa dostępności dla dysku zarządzanego](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Kliknij nazwę zasobu publicznego adresu IP. Strona **Przegląd** zawiera szczegółowe informacje o lokalizacji i strefie dostępności zasobu.

    ![Strefa dostępności dla adresu IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia maszyny wirtualnej w strefie dostępności. Dowiedz się więcej o [dostępności](availability.md) dla maszyn wirtualnych platformy Azure.
