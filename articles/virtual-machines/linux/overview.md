---
title: Omówienie maszyn wirtualnych z systemem Linux na platformie Azure
description: Omówienie maszyn wirtualnych systemu Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 69a9722436aea1cf794e6e3f3ce02ec79180cff3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159476"
---
# <a name="linux-virtual-machines-in-azure"></a>Maszyny wirtualne z systemem Linux na platformie Azure

Usługa Azure Virtual Machines (VM) to jeden z wielu [skalowalnych zasobów obliczeniowych dostępnych na żądanie](/azure/architecture/guide/technology-choices/compute-decision-tree), które są oferowane na platformie Azure. W większości przypadków maszynę wirtualną należy wybrać wtedy, gdy potrzebna jest większa kontrola nad środowiskiem obliczeniowym niż ta, jaką oferują inne opcje. Ten artykuł zawiera informacje o kwestiach, jakie należy rozważyć przed przystąpieniem do tworzenia maszyny wirtualnej. Opisano w nim także tworzenie maszyny wirtualnej i zarządzanie nią.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do jej działania. Nadal niezbędna jest jednak konserwacja maszyny wirtualnej — konfigurowanie i instalowanie jej oprogramowania, a także instalowanie poprawek tego oprogramowania.

Z maszyn wirtualnych na platformie Azure można korzystać na różne sposoby. Przykłady to:

* **Tworzenie i testowanie oprogramowania** — maszyny wirtualne umożliwiają szybkie i łatwe utworzenie komputera o określonej konfiguracji wymaganej do tworzenia kodu oraz testowania aplikacji.
* **Aplikacje w chmurze** — jako że zapotrzebowanie na aplikację może zmieniać się w czasie, uzasadnione ekonomicznie może okazać się uruchomienie jej na maszynie wirtualnej na platformie Azure. Jeśli zajdzie potrzeba użycia dodatkowych maszyn wirtualnych, wystarczy je opłacić. Gdy dodatkowe maszyny staną się zbędne, można je wyłączyć.
* **Rozszerzone centrum danych** — maszyny wirtualne w sieci wirtualnej na platformie Azure można zostać z łatwością połączyć z siecią organizacji.

Liczbę maszyn wirtualnych używanych przez aplikację można dowolnie i bez ograniczeń zwiększać odpowiednio do potrzeb.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co należy wziąć pod uwagę przed utworzeniem maszyny wirtualnej?
Podczas tworzenia infrastruktury aplikacji na platformie Azure należy zawsze wziąć pod uwagę wiele różnych [zagadnień projektowych](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm). Kwestie dotyczące maszyn wirtualnych, które należy rozważyć, to między innymi:

* nazwy zasobów aplikacji;
* lokalizacja, w której są przechowywane zasoby;
* rozmiar maszyny wirtualnej;
* maksymalna liczba maszyn wirtualnych, które można utworzyć;
* system operacyjny uruchomiony na maszynie wirtualnej;
* konfiguracja maszyny wirtualnej po jej uruchomieniu;
* powiązane zasoby niezbędne do działania maszyny wirtualnej.

### <a name="locations"></a>Lokalizacje
Wszystkie zasoby tworzone na platformie Azure są dystrybuowane w obrębie wielu [regionów geograficznych](https://azure.microsoft.com/regions/) na całym świecie. Zazwyczaj podczas tworzenia maszyny wirtualnej region określa się mianem **lokalizacji**. W przypadku maszyny wirtualnej lokalizacja określa miejsce, w którym są przechowywane wirtualne dyski twarde.

W poniższej tabeli przedstawiono wybrane metody uzyskania dostępu do listy dostępnych lokalizacji.

| Metoda | Opis |
| --- | --- |
| Portal Azure |Wybór lokalizacji z listy podczas tworzenia maszyny wirtualnej. |
| Azure PowerShell |Użyj polecenia [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation). |
| Interfejs API REST |Użycie operacji [wyświetlania listy lokalizacji](https://docs.microsoft.com/rest/api/resources/subscriptions). |
| Interfejs wiersza polecenia platformy Azure |Użyj operacji [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest). |

## <a name="availability"></a>Dostępność
Platforma Azure oferuje najlepszą w branży umowę dotyczącą poziomu usług gwarantującą dostępność pojedynczego wystąpienia maszyny wirtualnej na poziomie 99,9% pod warunkiem wdrożenia tej maszyny wirtualnej z magazynem Premium dla wszystkich dysków.  Aby wdrożenie kwalifikowało się do standardowej umowy dotyczącej poziomu usług na poziomie 99,95%, należy wdrożyć co najmniej dwie maszyny wirtualne obsługujące obciążenie w zestawie dostępności. Zestaw dostępności gwarantuje, że maszyny wirtualne są rozproszone w wielu domenach błędów w centrach danych platformy Azure oraz wdrożone na hostach z różnymi okresami konserwacji. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

## <a name="vm-size"></a>Rozmiar maszyny wirtualnej
[Rozmiar](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) maszyny wirtualnej zależy od obciążenia, które ma zostać uruchomione. Wybrany rozmiar ma więc wpływ na takie czynniki jak moc procesora, pamięć i przestrzeń dyskowa. W ramach platformy Azure dostępna jest szeroka gama rozmiarów umożliwiających wykorzystanie produktu do wielu różnych zastosowań.

Platforma Azure pobiera [cenę godzinową](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) na podstawie rozmiaru maszyny Wirtualnej i systemu operacyjnego. W przypadku rozpoczętych godzin opłaty są pobierane tylko za faktycznie wykorzystane minuty. Magazyn jest wyceniany oddzielnie; związane z nim opłaty są także pobierane osobno.

## <a name="vm-limits"></a>Limity maszyn wirtualnych
Subskrypcje mają domyślne [limity przydziałów](../../azure-resource-manager/management/azure-subscription-service-limits.md), które mogą mieć wpływ na wdrożenie wielu maszyn wirtualnych w projekcie. Aktualny limit dla każdej subskrypcji wynosi 20 maszyn wirtualnych na region. Limity można zwiększyć, [wypełniając odpowiednio bilet pomocy technicznej](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Dyski zarządzane

W przypadku funkcji Dyski zarządzane tworzenie konta usługi Azure Storage i zarządzanie nią odbywa się automatycznie w tle i nie trzeba martwić się o limity skalowalności konta magazynu. Wystarczy określić rozmiar dysku i warstwę wydajności (Standardowa/Premium), a platforma Azure utworzy dysk i będzie nim zarządzać. O używanym magazynie nie trzeba myśleć przy dodawaniu dysków albo skalowaniu maszyny wirtualnej w górę i w dół. Jeśli tworzysz nowe maszyny wirtualne, [użyj interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) lub witryny Azure Portal, aby utworzyć maszyny wirtualne z zarządzanymi dyskami systemu operacyjnego i dyskami danych. Jeśli na maszynie wirtualnej znajdują się niezarządzane dyski, możesz [przekonwertować maszyny wirtualne, tak aby korzystały z funkcji Dyski zarządzane](convert-unmanaged-to-managed-disks.md).

Można też zarządzać własnymi obrazami niestandardowymi na jednym koncie magazynu na region platformy Azure i używać ich do tworzenia setek maszyn wirtualnych w tej samej subskrypcji. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../linux/managed-disks-overview.md).

## <a name="distributions"></a>Dystrybucji 
Platforma Microsoft Azure umożliwia uruchamianie wielu popularnych dystrybucji systemu Linux dostarczanych i utrzymywanych przez kilku partnerów.  W portalu Azure Marketplace dostępne są takie dystrybucje jak Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD i wiele więcej. Firma Microsoft aktywnie współpracuje z wieloma społecznościami systemu Linux, aby dodać jeszcze więcej pozycji do listy [dystrybucji systemu Linux obsługiwanych na platformie Azure](endorsed-distros.md).

Jeśli Twoja preferowana dystrybucja systemu Linux nie jest w tej chwili dostępna w galerii, masz możliwość skorzystania z własnej maszyny wirtualnej systemu Linux po [utworzeniu i przekazaniu wirtualnego dysku twardego systemu Linux na platformie Azure](create-upload-generic.md).

Firma Microsoft blisko współpracuje z partnerami, aby zagwarantować, że dostępne obrazy są aktualizowane i optymalizowane dla środowiska uruchomieniowego platformy Azure.  Aby uzyskać więcej informacji na temat partnerów platformy Azure, zobacz następujące linki:

* System Linux na platformie Azure — [zatwierdzone dystrybucje](endorsed-distros.md)
* SUSE — [Azure Marketplace — SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat — [Azure Marketplace — Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical — [Azure Marketplace — Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian — [Azure Marketplace — Debian 8 „Jessie”](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD — [Azure Marketplace — FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace — CoreOS (wersja stabilna)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS — [Azure Marketplace — RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami — [Bitnami Library dla platformy Azure](https://azure.bitnami.com/)
* Mesosphere — [Azure Marketplace — Mesosphere DC/OS dla platformy Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker — [Azure Marketplace — usługa Azure Container Service z rozwiązaniem Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins — [Azure Marketplace — platforma CloudBees Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

Aby uzyskać właściwą kulturę DevOps, cała infrastruktura musi być zawarta w kodzie.  Gdy cała infrastruktura działa w kodzie można łatwo odtworzyć.  Platforma Azure współpracuje ze wszystkimi najważniejszymi narzędziami automatyzacji, takimi jak Ansible, Chef, SaltStack i Puppet.  Platforma Azure oferuje również własne narzędzia automatyzacji:

* [Szablony platformy Azure](create-ssh-secured-vm-from-template.md)
* [Rozszerzenie Azure VMAccess](using-vmaccess-extension.md)

Platforma Azure obsługuje [init chmury w](https://cloud-init.io/) większości dystrybucji systemu Linux, które go obsługują.  Aktywnie współpracujemy z naszymi zatwierdzonymi partnerami dystrybucji systemu Linux, aby obrazy z obsługą chmury były dostępne w portalu Azure marketplace. Te obrazy sprawią, że wdrożenia i konfiguracje typu cloud-init będą bezproblemowo współpracować z maszynami wirtualnymi i zestawami skalowania maszyn wirtualnych.

* [Korzystanie z pakietu cloud-init na maszynach wirtualnych platformy Azure z systemem Linux](using-cloud-init.md)

## <a name="storage"></a>Magazyn
* [Wprowadzenie do usługi Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Dodawanie dysku do maszyny wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure](add-disk.md)
* [Jak dołączyć dysk danych do maszyny wirtualnej w witrynie Azure Portal?](attach-disk-portal.md)

## <a name="networking"></a>Obsługa sieci
* [Omówienie usługi Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [Adresy IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Otwieranie portów dla maszyny wirtualnej systemu Linux na platformie Azure](nsg-quickstart.md)
* [Tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure Portal](portal-create-fqdn.md)


## <a name="next-steps"></a>Następne kroki

Stwórz swoją pierwszą maszynę wirtualną!

- [Portal](quick-create-portal.md)
- [Interfejs wiersza polecenia platformy Azure](quick-create-cli.md)
- [Powershell](quick-create-powershell.md)

