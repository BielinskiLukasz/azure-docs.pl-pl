---
title: Często zadawane pytania dotyczące maszyn wirtualnych Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące maszyn wirtualnych Windows utworzonych za pomocą modelu usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: 65d4326763ef9754159e94c9426f3aee69f80ffd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253367"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Często zadawane pytania dotyczące maszyn wirtualnych Windows
W tym artykule opisano często zadawane pytania dotyczące maszyn wirtualnych Windows utworzone na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager. Wersja systemu Linux w tym temacie, zobacz [— często zadawane pytania dotyczące maszyn wirtualnych systemu Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Aby uzyskać informacji na temat zasad wsparcia dla uruchomione oprogramowanie serwerowe firmy Microsoft na platformie Azure, zobacz [pomoc techniczna dotycząca oprogramowania serwera firmy Microsoft dla usługi Azure Virtual Machines](https://support.microsoft.com/kb/2721672)

Określonych wersji systemu Windows 7, Windows 8.1 i Windows 10 są dostępne dla subskrybentów korzyści MSDN platformy Azure i subskrybentów MSDN: tworzenie i testowanie płatność za rzeczywiste użycie, dla zadań tworzenia i testowania. Aby uzyskać szczegółowe informacje, łącznie z instrukcjami i ograniczeniami, zobacz [Windows Client images for MSDN subscribers](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) (Obrazy klienta systemu Windows dla subskrybentów MSDN). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk danych może być do 4 TB (4095 GB). Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Rozmiary maszyn wirtualnych).

Usługa Azure Managed Disks są zalecane oferta magazynu na dysku do użycia z usługą Azure Virtual Machines do trwałego magazynowania danych. W każdej maszynie wirtualnej możesz używać wielu dysków Managed Disks. Usługa Managed Disks oferuje dwa typy opcji magazynu trwałego: dyski Managed Disks w warstwie Premium i Standardowa. Aby uzyskać informacje o cenach, zobacz [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

Konta usługi Azure storage oferuje również magazynu dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak można uzyskać dostęp Moja maszyna wirtualna?
Nawiąż połączenie zdalne za pomocą połączenia pulpitu zdalnego (RDP) dla maszyny Wirtualnej z systemem Windows. Aby uzyskać instrukcje, zobacz [jak połączyć i zaloguj się na maszynie wirtualnej platformy Azure, systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Obsługuje maksymalnie dwa równoczesne połączenia, chyba że serwer jest skonfigurowany jako host sesji usług pulpitu zdalnego.  

Jeśli występują problemy przy użyciu pulpitu zdalnego, zobacz [połączeń Rozwiązywanie problemów z pulpitu zdalnego na podstawie Windows maszyny wirtualnej platformy Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Jeśli znasz funkcję Hyper-V, możesz poszukać narzędzia podobnego do VMConnect. Platforma Azure nie oferuje podobnego narzędzia, ponieważ dostęp do konsoli maszyny wirtualnej nie jest obsługiwany.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Czy można używać dysku tymczasowego (dysku D: domyślnie), do przechowywania danych?
Nie należy używać dysku tymczasowego do przechowywania danych. Jest on tylko magazyn tymczasowy, czyli istnieje ryzyko utraty danych, których nie można odzyskać. Może wystąpić utrata danych, gdy maszyna wirtualna zostanie przeniesiona do innego hosta. Zmiana rozmiaru maszyny wirtualnej, aktualizowanie hosta lub awaria sprzętu na hoście to kilka przyczyn, które mogą spowodować przeniesienie maszyny wirtualnej.

Jeśli masz aplikację, która wymaga litery dysku D:, dzięki czemu będzie używać dysku tymczasowego coś innego niż D: można ponownie przypisać litery dysku. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak mogę zmienić literę dysku tymczasowego?
Możesz zmienić literę dysku, przenosząc plik stronicowania i ponowne przypisując literę dysku, ale należy upewnić się, że czynności zostały wykonane w określonej kolejności. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Do zestawu dostępności można dodać istniejącej maszyny Wirtualnej?
Nie. Jako część zestawu dostępności maszyny Wirtualnej, należy utworzyć maszynę Wirtualną w zestawie. Obecnie nie istnieje sposób, aby dodać Maszynę wirtualną do zestawu dostępności po jego utworzeniu.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Czy mogę przekazać maszynę wirtualną na platformie Azure?
Tak. Aby uzyskać instrukcje, zobacz [Migrowanie lokalnych maszyn wirtualnych na platformie Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Czy mogę zmienić rozmiar dysku systemu operacyjnego?
Tak. Aby uzyskać instrukcje, zobacz [sposobu rozszerzania dysku systemu operacyjnego maszyny wirtualnej w grupie zasobów Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Czy mogę skopiować lub klonowanie istniejących maszyn wirtualnych platformy Azure?
Tak. Przy użyciu obrazów zarządzanych, możesz utworzyć obraz maszyny wirtualnej i następnie tworzyć wiele nowych maszyn wirtualnych przy użyciu obrazu. Aby uzyskać instrukcje, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę Kanada Środkowa i Kanada Wschodnia za pośrednictwem usługi Azure Resource Manager?

Dwa nowe regiony, Kanada Środkowa i Kanada Wschodnia, nie są automatycznie zarejestrowani do tworzenia maszyny wirtualnej dla istniejących subskrypcji platformy Azure. Rejestracja odbywa się automatycznie po wdrożeniu maszyny wirtualnej w witrynie Azure portal do dowolnego innego regionu za pomocą usługi Azure Resource Manager. Po wdrożeniu maszyny wirtualnej do innego regionu platformy Azure, nowe regiony powinna być dostępna dla kolejnych maszyn wirtualnych.

## <a name="does-azure-support-linux-vms"></a>Czy system Azure obsługuje maszyny wirtualne systemu Linux?
Tak. Aby szybko utworzyć Maszynę wirtualną systemu Linux możesz wypróbować, zobacz [Utwórz Maszynę wirtualną systemu Linux na platformie Azure przy użyciu portalu](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Można dodać kartę Sieciową z maszyną Wirtualną po jej utworzeniu?
Tak, teraz jest to możliwe. Maszyna wirtualna najpierw musi zostać zatrzymane przydział zostanie cofnięty. Następnie można dodać lub usunąć karty Sieciowej (chyba że jest to ostatnia karty Sieciowej na maszynie Wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy istnieją jakiekolwiek wymagania nazwa komputera?
Tak. Nazwa komputera może zawierać maksymalnie 15 znaków. Zobacz [ograniczenia i reguły konwencji nazewnictwa](/azure/architecture/best-practices/naming-conventions#compute) Aby uzyskać więcej informacji dotyczących nazewnictwa zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy istnieją dowolnego zasobu wymagania dotyczące nazw grupy?
Tak. Nazwa grupy zasobów może zawierać maksymalnie 90 znaków długości. Zobacz [ograniczenia i reguły konwencji nazewnictwa](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) Aby uzyskać więcej informacji na temat grup zasobów.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny Wirtualnej?

Nazwy użytkowników może zawierać maksymalnie 20 znaków i nie może kończyć się kropką ("."). 


Następujące nazwy użytkowników nie są dozwolone:
<table>
    <tr>
        <td style="text-align:center">1</td><td style="text-align:center">123</td><td style="text-align:center">a</td><td style="text-align:center">actuser</td>
    </tr>
    <tr>
        <td style="text-align:center">Menedżer adm</td><td style="text-align:center">Administrator</td><td style="text-align:center">admin1</td><td style="text-align:center">admin2</td>
    </tr>   <tr>
        <td style="text-align:center">administrator</td><td style="text-align:center">aspnet</td><td style="text-align:center">kopia zapasowa</td><td style="text-align:center">console</td>
    </tr>
    <tr>
        <td style="text-align:center">David </td><td style="text-align:center">Gość</td><td style="text-align:center">Jan</td><td style="text-align:center">właściciel</td>
    </tr>
    <tr>
        <td style="text-align:center">Główny</td><td style="text-align:center">serwer</td><td style="text-align:center">sql</td><td style="text-align:center">pomoc techniczna</td>
    </tr>
    <tr>
        <td style="text-align:center">support_388945a0</td><td style="text-align:center">sys</td><td style="text-align:center">test</td><td style="text-align:center">test1</td>
    </tr>
    <tr>
        <td style="text-align:center">Test2</td><td style="text-align:center">test3</td><td style="text-align:center">Użytkownik</td><td style="text-align:center">Użytkownik1</td>
    </tr>
    <tr>
        <td style="text-align:center">UŻYTKOWNIK2</td><td style="text-align:center">UŻYTKOWNIK3</td><td style="text-align:center">user4</td><td style="text-align:center">user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła, podczas tworzenia maszyny Wirtualnej?
Hasło musi mieć długość od 12 do 123 znaków i spełniać 3 z następujących wymagań dotyczących złożoności 4:

* Niższe znaków
* Górny znaków
* Zawierać cyfrę
* Masz znaków specjalnych (wyrażenie regularne dopasowuje [\W_])

Następujące hasła nie są dozwolone:

<table>
    <tr>
        <td>abc@123</td>
        <td>ILOVEYOU!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Hasło!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
