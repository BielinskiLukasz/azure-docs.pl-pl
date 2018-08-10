---
title: Rozwiązywanie problemów z wdrożenia maszyny Wirtualnej Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów dotyczących wdrożenia usługi Resource Manager, podczas tworzenia nowej maszyny wirtualnej Windows na platformie Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d406d6d8f6432b3555e34876854147c4945f7a8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39634584"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Rozwiązywanie problemów dotyczących wdrażania podczas tworzenia nowej maszyny Wirtualnej Windows na platformie Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Najczęstsze problemy
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Inne problemy z wdrażaniem maszyn wirtualnych i pytania można znaleźć [problemów wdrażanie Windows maszyny wirtualnej na platformie Azure](troubleshoot-deploy-vm.md).

## <a name="collect-activity-logs"></a>Dzienniki aktywności zbieranie
Aby rozpocząć rozwiązywanie problemów, zbieranie dzienników aktywności, aby zidentyfikować ten błąd, skojarzone z emisją. Poniższe linki zawierają szczegółowe informacje na temat procesu do wykonania.

[Wyświetlanie operacji wdrażania](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Wyświetlanie dzienników aktywności do zarządzania zasobami platformy Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Katalog** Jeśli systemu operacyjnego jest uogólniony, Windows i przekazać lub przechwycone z ustawieniem uogólniony, a następnie nie będzie żadnych błędów. Podobnie jeśli systemem operacyjnym jest Windows wyspecjalizowany, przekazany lub przechwycone z ustawieniem specjalistyczne, a następnie nie będzie żadnych błędów.

**Błędy przekazywania:**

**N<sup>1</sup>:** Jeśli systemem operacyjnym jest Windows uogólnione i jest przekazywany jako wyspecjalizowany, wystąpi błąd limitu czasu inicjowania obsługi administracyjnej z maszyną Wirtualną została zablokowana na ekranie OOBE.

**N<sup>2</sup>:** w przypadku systemu operacyjnego Windows wyspecjalizowane i przekazaniu jako uogólniony, otrzymasz błąd inicjowania obsługi administracyjnej z maszyny Wirtualnej została zablokowana na ekranie OOBE, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwę komputera Nazwa użytkownika i hasło.

**Rozdzielczość**

Aby usunąć oba te błędy, należy użyć [Add-AzureRmVhd, aby przekazać oryginalny dysk VHD](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd), dostępne lokalnie, za pomocą tego samego ustawienia, jak dla systemu operacyjnego (uogólniony/wyspecjalizowane). Można przekazać jako uogólniony, pamiętaj, aby najpierw uruchom program sysprep.

**Przechwytywania błędów:**

**N<sup>3</sup>:** Jeśli systemem operacyjnym jest Windows uogólniony, a jest przechwytywany jako wyspecjalizowany, otrzymasz błąd limitu czasu inicjowania obsługi administracyjnej ponieważ oryginalna maszyna wirtualna nie jest używany, ponieważ jest ona oznaczona jako uogólniona.

**N<sup>4</sup>:** w przypadku systemu operacyjnego Windows wyspecjalizowane i są przechwytywane, jako uogólniony, otrzymasz błąd inicjowania obsługi, ponieważ nowa maszyna wirtualna jest uruchomiona z oryginalną nazwę komputera, nazwę użytkownika i hasło. Ponadto oryginalnego maszyny Wirtualnej nie jest używany, ponieważ jest ona oznaczona jako specjalne.

**Rozdzielczość**

Usuń oba te błędy, Usuń bieżący obraz z portalu i [ponownie wykonaj operację przechwytywania przy bieżącym wirtualnych dysków twardych](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przy użyciu tego samego ustawienia, jak dla systemu operacyjnego (uogólniony/wyspecjalizowane).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problem: Niestandardowe/Galeria/obrazu portalu marketplace; Niepowodzenie alokacji
Ten błąd pojawia się w sytuacjach, gdy nowe żądanie maszyna wirtualna jest przypięta do klastra, który nie obsługuje żądanego rozmiaru maszyny Wirtualnej lub nie ma dostępnego wolnego miejsca, aby obsłużyć żądanie.

**Przyczyny 1:** klaster nie obsługuje żądanego rozmiaru maszyny Wirtualnej.

**Rozwiązanie nr 1.**

* Ponów żądanie, używając mniejszego rozmiaru maszyny Wirtualnej.
* Jeśli nie można zmienić rozmiar żądanej maszyny Wirtualnej:
  * Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności.
    Kliknij przycisk **grup zasobów** > *grupy zasobów* > **zasobów** > *poziomu dostępności zestawu*  >  **Maszyn wirtualnych** > *maszyny wirtualnej* > **zatrzymać**.
  * Po zatrzymania wszystkich maszyn wirtualnych, należy utworzyć nową maszynę Wirtualną w żądany rozmiar.
  * Najpierw należy uruchomić nową maszynę Wirtualną, a następnie wybierz poszczególne zatrzymanych maszyn wirtualnych i kliknij **Start**.

**Przyczyny 2:** klastra ma bezpłatnymi zasobami.

**Rozwiązanie nr 2.**

* Ponów żądanie w późniejszym czasie.
* Jeśli nowa maszyna wirtualna może być częścią zestawu dostępności różnych
  * Utwórz nową maszynę Wirtualną w innej zestaw dostępności (w tym samym regionie).
  * Dodaj nową maszynę Wirtualną do tej samej sieci wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
Jeśli wystąpią problemy podczas uruchamiania zatrzymanej maszyny Wirtualnej Windows lub zmienić rozmiar istniejącej maszyny Wirtualnej Windows na platformie Azure, zobacz [problemów dotyczących wdrożenia usługi Resource Manager Rozwiązywanie problemów przy ponownym uruchamianiu lub zmiany rozmiaru istniejącej maszyny wirtualnej Windows na platformie Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

