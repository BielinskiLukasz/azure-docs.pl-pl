---
title: Ponowne uruchamianie maszyny wirtualnej lub zmienianie rozmiarów problemów na platformie Azure | Microsoft Docs
description: Rozwiązywanie problemów z Menedżer zasobów wdrażania przy ponownym uruchamianiu lub zmienianiu rozmiarów istniejącej maszyny wirtualnej na platformie Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd05f9f7db0db22759c5e19dbfb59cc377e63f4d
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155466"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Rozwiązywanie problemów z wdrażaniem w przypadku ponownego uruchamiania lub zmiany rozmiarów istniejącej maszyny wirtualnej z systemem Windows na platformie Azure
Podczas próby uruchomienia zatrzymanej maszyny wirtualnej platformy Azure lub zmiany rozmiaru istniejącej maszyny wirtualnej platformy Azure Wystąpił błąd alokacji. Ten błąd jest wynikiem sytuacji, gdy klaster lub region nie ma dostępnych zasobów lub nie obsługuje żądanego rozmiaru maszyny wirtualnej.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Zbieranie dzienników aktywności
Aby rozpocząć rozwiązywanie problemów, Zbierz dzienniki aktywności w celu zidentyfikowania błędu związanego z problemem. Następujące linki zawierają szczegółowe informacje dotyczące procesu:

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Błąd podczas uruchamiania zatrzymanej maszyny wirtualnej
Podjęto próbę uruchomienia zatrzymanej maszyny wirtualnej, ale wystąpił błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądanie uruchomienia zatrzymanej maszyny wirtualnej musi być podejmowane w oryginalnym klastrze, który hostuje usługę w chmurze. Jednak w klastrze nie ma wolnego miejsca dostępnego do zrealizowania żądania.

### <a name="resolution"></a>Rozwiązanie
* Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności, a następnie uruchom ponownie każdą maszynę wirtualną.
  
  1. Kliknij pozycję **grupy** > zasobów**zasoby** > grupyzasobów > *Twój zestaw dostępności*Virtual Machinesmaszyny > wirtualnej > *Zatrzymaj.*  > 
  2. Po zatrzymaniu wszystkich maszyn wirtualnych zaznacz wszystkie zatrzymane maszyny wirtualne, a następnie kliknij przycisk Uruchom.
* Ponów żądanie ponownego uruchomienia w późniejszym czasie.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Błąd podczas zmieniania rozmiarów istniejącej maszyny wirtualnej
Podjęto próbę zmiany rozmiaru istniejącej maszyny wirtualnej, ale wystąpił błąd alokacji.

### <a name="cause"></a>Przyczyna
Żądanie zmiany rozmiaru maszyny wirtualnej musi być podejmowane w oryginalnym klastrze, który hostuje usługę w chmurze. Jednak klaster nie obsługuje żądanego rozmiaru maszyny wirtualnej.

### <a name="resolution"></a>Rozwiązanie
* Spróbuj ponownie wykonać żądanie, używając mniejszego rozmiaru maszyny wirtualnej.
* Jeśli nie można zmienić rozmiaru żądanej maszyny wirtualnej:
  
  1. Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
     
     * Kliknij pozycję **grupy** > zasobów**zasoby** > grupyzasobów > *Twój zestaw dostępności*Virtual Machinesmaszyny > wirtualnej > *Zatrzymaj.*  > 
  2. Po zatrzymaniu wszystkich maszyn wirtualnych Zmień rozmiar żądanej maszyny wirtualnej na większy.
  3. Wybierz maszynę wirtualną o zmienionym rozmiarze, a następnie kliknij przycisk **Start**, a następnie uruchom wszystkie zatrzymane maszyny wirtualne.

## <a name="next-steps"></a>Następne kroki
Jeśli wystąpią problemy podczas tworzenia nowej maszyny wirtualnej z systemem Windows na platformie Azure, zobacz [Rozwiązywanie problemów z wdrażaniem w przypadku tworzenia nowej maszyny wirtualnej z systemem Windows na platformie Azure](../windows/troubleshoot-deployment-new-vm.md).

