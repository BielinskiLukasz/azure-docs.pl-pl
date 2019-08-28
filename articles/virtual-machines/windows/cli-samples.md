---
title: Przykłady interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Okna przykładów interfejsu wiersza polecenia platformy Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8d4cfbb103313d1d13ec0e08a3be82b6a75b331
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089388"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Windows

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure, które wdrażają maszyny wirtualne z systemem Windows.

| | |
|---|---|
|**Tworzenie maszyn wirtualnych**||
| [Tworzenie maszyny wirtualnej](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną z systemem Windows z minimalną konfiguracją. |
| [Tworzenie w pełni skonfigurowanej maszyny wirtualnej](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.|
| [Tworzenie maszyn wirtualnych o wysokiej dostępności](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy kilka maszyn wirtualnych na platformie o wysokiej dostępności i konfiguracji ze zrównoważonym obciążeniem. |
| [Tworzenie maszyny wirtualnej i uruchamianie skryptu konfiguracyjnego](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia niestandardowego skryptu platformy Azure do zainstalowania usług IIS. |
| [Tworzenie maszyny wirtualnej i uruchamianie konfiguracji DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną i używa rozszerzenia konfiguracji żądanego stanu (DSC) platformy Azure w celu zainstalowania usług IIS. |
|**Zarządzanie magazynem**||
| [Tworzenie dysku zarządzanego na podstawie dysku VHD](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany na podstawie wyspecjalizowanego wirtualnego dysku twardego jako dysku systemu operacyjnego lub z wirtualnego dysku twardego jako dysku danych.  |
| [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dysk zarządzany na podstawie migawki. |
| [Kopiowanie dysku zarządzanego do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiuje dysk zarządzany do tej samej lub innej subskrypcji, ale w tym samym regionie co nadrzędny dysk zarządzany. 
| [Eksportowanie migawki jako dysku VHD do konta magazynu](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje zarządzaną migawkę jako dysk VHD do konta magazynu w innym regionie. |
| [Eksportowanie wirtualnego dysku twardego na dysk zarządzany do konta magazynu](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Eksportuje podstawowy dysk VHD dysku zarządzanego do konta magazynu w innym regionie. |
| [Kopiuj migawkę do tej samej lub innej subskrypcji](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopiuje migawkę do tej samej lub innej subskrypcji, ale w tym samym regionie co migawka nadrzędna. |
|**Sieciowe maszyny wirtualne**||
| [Zabezpieczanie ruchu sieciowego między maszynami wirtualnymi](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy dwie maszyny wirtualne, wszystkie powiązane zasoby i wewnętrzną i zewnętrzną grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń). |
|**Zabezpieczanie maszyn wirtualnych**||
| [Szyfrowanie maszyny wirtualnej i dysków z danymi](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy Azure Key Vault, klucz szyfrowania i nazwę główną usługi, a następnie szyfruje maszynę wirtualną. |
|**Monitorowanie maszyn wirtualnych**||
| [Monitorowanie maszyny wirtualnej za pomocą Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Tworzy maszynę wirtualną, instaluje agenta Log Analytics i rejestruje maszynę wirtualną w obszarze roboczym Log Analytics.  |
| | |
