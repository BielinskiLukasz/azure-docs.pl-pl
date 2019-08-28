---
title: Często zadawane pytania dotyczące maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące maszyn wirtualnych z systemem Linux utworzonych przy użyciu modelu Menedżer zasobów.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 0648b9b321aabc7c9e56997c320a63386542498c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083369"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Często zadawane pytania dotyczące Linux Virtual Machines
W tym artykule opisano niektóre często zadawane pytania dotyczące maszyn wirtualnych z systemem Linux utworzonych na platformie Azure przy użyciu modelu wdrażania Menedżer zasobów. Aby uzyskać informacje na temat wersji systemu Windows w tym temacie, zobacz [często zadawane pytania dotyczące Windows Virtual Machines](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz System [Linux w przypadku dystrybucji z zatwierdzeniem na platformie Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk z danymi może mieć do 32 767 GiB. Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Rozmiary maszyn wirtualnych).

Usługa Azure Managed Disks to zalecane oferty magazynu dyskowego do użycia z usługą Azure Virtual Machines na potrzeby trwałego magazynowania danych. W każdej maszynie wirtualnej możesz używać wielu funkcji Dyski zarządzane. Managed Disks oferuje dwa typy opcji trwałego magazynu: Managed Disks w warstwie Premium i standardowa. Aby uzyskać informacje o cenach, zobacz [Cennik usługi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks).

Konta usługi Azure Storage mogą również udostępniać magazyn dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak mogę uzyskać dostęp do mojej maszyny wirtualnej?
Nawiąż połączenie zdalne, aby zalogować się do maszyny wirtualnej przy użyciu Secure Shell (SSH). Zapoznaj się z instrukcjami dotyczącymi sposobu nawiązywania połączenia [z systemem Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [z systemów Linux i Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Domyślnie protokół SSH umożliwia maksymalnie 10 równoczesnych połączeń. Możesz zwiększyć tę liczbę, edytując plik konfiguracji.

Jeśli masz problemy, sprawdź [Rozwiązywanie problemów z połączeniami Secure Shell (SSH)](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Czy można używać dysku tymczasowego (/dev/sdb1) do przechowywania danych?
Nie używaj dysku tymczasowego (/dev/sdb1) do przechowywania danych. Tylko w przypadku magazynu tymczasowego. Ryzyko utraty danych, których nie można odzyskać.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Czy mogę skopiować lub sklonować istniejącą maszynę wirtualną platformy Azure?
Tak. Aby uzyskać instrukcje, zobacz [jak utworzyć kopię maszyny wirtualnej z systemem Linux w modelu wdrażania Menedżer zasobów](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę regionów Kanady środkowe i Kanada Wschodnia za poorednictwem Azure Resource Manager?
Dwa nowe regiony Kanady środkowe i Kanada Wschodnia nie są automatycznie rejestrowane na potrzeby tworzenia maszyn wirtualnych dla istniejących subskrypcji platformy Azure. Ta rejestracja jest wykonywana automatycznie, gdy maszyna wirtualna jest wdrażana za pośrednictwem Azure Portal w innym regionie przy użyciu Azure Resource Manager. Po wdrożeniu maszyny wirtualnej w innym regionie świadczenia usługi Azure nowe regiony powinny być dostępne dla kolejnych maszyn wirtualnych.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Czy mogę dodać kartę sieciową do mojej maszyny wirtualnej po jej utworzeniu?
Tak. teraz jest to możliwe. Najpierw należy zatrzymać cofniętą alokację maszyny wirtualnej. Następnie można dodać lub usunąć kartę sieciową (chyba że jest to ostatnia karta sieciowa na maszynie wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy istnieją jakieś wymagania dotyczące nazw komputerów?
Tak. Nazwa komputera może zawierać maksymalnie 64 znaków. Zobacz [reguły nazewnictwa i ograniczenia,](/azure/architecture/best-practices/naming-conventions) Aby uzyskać więcej informacji na temat nazywania zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy istnieją jakieś wymagania dotyczące nazw grup zasobów?
Tak. Nazwa grupy zasobów może zawierać maksymalnie 90 znaków. Aby uzyskać więcej informacji na temat grup zasobów [, zobacz Reguły nazewnictwa i ograniczenia](/azure/architecture/best-practices/naming-conventions) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny wirtualnej?

Nazwy użytkowników powinny mieć długość 1-32 znaków.

Następujące nazwy użytkowników są niedozwolone:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła podczas tworzenia maszyny wirtualnej?

Istnieją różne wymagania dotyczące długości hasła, w zależności od używanego narzędzia:
 - Portal — od 12-72 znaków
 - PowerShell — od 8-123 znaków
 - Interfejs wiersza polecenia — między 12-123
 

Hasła muszą również spełniać 3 z następujących wymagań dotyczących złożoności:

* Zawierać małe litery
* Zawierać wielkie litery
* Mieć cyfrę
* Mieć znak specjalny (dopasowanie wyrażenia regularnego [\W_])

Następujące hasła są niedozwolone:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P @ $ $w 0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Hasło!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
