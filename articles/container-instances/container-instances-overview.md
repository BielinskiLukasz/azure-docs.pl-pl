---
title: Kontenery bezserwerowe na platformie Azure
description: Usługa Azure Container Instances to najszybszy i najprostszy sposób uruchamiania wyizolowanych kontenerów na platformie Azure, bez konieczności zarządzania jakimikolwiek maszynami wirtualnymi ani korzystania z orkiestratora wyższego poziomu.
ms.topic: overview
ms.date: 04/25/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c871c09e29b64c4f0dcd107361154efdce306481
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240254"
---
# <a name="what-is-azure-container-instances"></a>Co to jest usługa Azure Container Instances?

Kontenery stają się preferowanym sposobem tworzenia pakietów aplikacji w chmurze, ich wdrażania oraz zarządzania nimi. Usługa Azure Container Instances to najszybszy i najprostszy sposób uruchomienia kontenera na platformie Azure, bez konieczności zarządzania jakimikolwiek maszynami wirtualnymi ani korzystania z usługi wyższego poziomu.

Usługa Azure Container Instances to doskonałe rozwiązanie dla wszystkich scenariuszy, które może działać w kontenerach izolowanych, w tym w przypadku prostych aplikacji, automatyzacji zadań i zadań kompilacji. W przypadku scenariuszy, w których potrzebujesz pełnej orkiestracji kontenerów, w tym opcji odnajdowania usług w wielu kontenerach, automatycznego skalowania i skoordynowanych uaktualnień aplikacji, zalecamy skorzystanie z usługi [Azure Kubernetes Service](../aks/index.yml) (AKS).

## <a name="fast-startup-times"></a>Krótki czas uruchamiania

Kontenery oferują znaczące korzyści związane z uruchamianiem w porównaniu do maszyn wirtualnych. Usługa Azure Container Instances umożliwia uruchamianie kontenera na platformie Azure w ciągu kilku sekund bez konieczności aprowizowania maszyn wirtualnych i zarządzania nimi.

## <a name="container-access"></a>Dostęp do kontenera

Azure Container Instances umożliwia Uwidacznianie grup kontenerów bezpośrednio w Internecie przy użyciu adresu IP i w pełni kwalifikowanej nazwy domeny (FQDN). Podczas tworzenia wystąpienia kontenera możesz określić niestandardową etykietę nazwy DNS, dzięki czemu aplikacja będzie dostępna pod adresem *etykieta_niestandardowa*.*region_świadczenia_usługi_Azure*.azurecontainer.io.

Azure Container Instances obsługuje również wykonywanie polecenia w działającym kontenerze, zapewniając interaktywną powłokę ułatwiającą Tworzenie aplikacji i rozwiązywanie problemów. Dostęp odbywa się za pośrednictwem protokołu HTTPS przy użyciu protokołu TLS do zabezpieczania połączeń klientów.

> [!IMPORTANT]
> Od 13 stycznia 2020, Azure Container Instances będą wymagały wszystkich bezpiecznych połączeń z serwerów i aplikacji do korzystania z protokołu TLS 1,2. Obsługa protokołu TLS 1,0 i 1,1 zostanie wycofana.

## <a name="hypervisor-level-security"></a>Zabezpieczenia na poziomie funkcji hypervisor

W przeszłości kontenery oferowały zarządzanie zasobami i izolację zależności aplikacji, ale nie były wystarczająco odporne na użycie wielu obcych dzierżaw. Usługa Azure Container Instances gwarantuje, że aplikacja jest izolowana w kontenerze w takim samym stopniu, w jakim byłaby na maszynie wirtualnej.


## <a name="custom-sizes"></a>Rozmiary niestandardowe

Kontenery są przeważnie optymalizowane do uruchamiania tylko jednej aplikacji, ale szczegółowe potrzeby poszczególnych aplikacji mogą się bardzo różnić. Usługa Azure Container Instances zapewnia optymalne wykorzystanie, zezwalając na korzystanie z dokładnych specyfikacji rdzeni procesora i pamięci. Opłaty są naliczane zgodnie z zapotrzebowaniem i za sekundę, dlatego można skutecznie dostosować wydatki w oparciu o potrzeby.

W przypadku zadań wykorzystujących znaczną moc obliczeniową, takich jak uczenie maszynowe, usługa Azure Container Instances może zaplanować używanie [zasobów procesorów GPU](container-instances-gpu.md) NVIDIA Tesla (wersja zapoznawcza) przez kontenery systemu Linux.

## <a name="persistent-storage"></a>Magazyn trwały

Aby można było pobrać i zachować stan z Azure Container Instances, oferujemy bezpośrednie [Instalowanie udziałów Azure Files udostępnionych](container-instances-mounting-azure-files-volume.md) przez usługę Azure Storage.

## <a name="linux-and-windows-containers"></a>Kontenery systemów Linux i Windows

Usługa Azure Container Instances umożliwia planowanie kontenerów systemów Windows i Linux przy użyciu tego samego interfejsu API. Podczas tworzenia [grup kontenerów](container-instances-container-groups.md) po prostu określ typ systemu operacyjnego.

Niektóre funkcje są obecnie ograniczone do kontenerów systemu Linux:

* Wiele kontenerów na grupę kontenerów
* Instalowanie woluminu ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [Secret](container-instances-volume-secret.md))
* [Metryki użycia zasobów](container-instances-monitor.md) z Azure monitor
* [Wdrażanie sieci wirtualnej](container-instances-vnet.md)
* [Zasoby procesora GPU](container-instances-gpu.md) (wersja zapoznawcza)

W przypadku wdrożeń kontenerów systemu Windows należy używać obrazów opartych na typowych [obrazach podstawowych systemu Windows](container-instances-faq.md#what-windows-base-os-images-are-supported).

> [!NOTE]
> Korzystanie z obrazów opartych na systemie Windows Server 2019 w Azure Container Instances jest w wersji zapoznawczej.

## <a name="co-scheduled-groups"></a>Grupy planowane wspólnie

Usługa Azure Container Instances obsługuje planowanie [grup wielu kontenerów](container-instances-container-groups.md), które współużytkują maszynę hosta, sieć lokalną, magazyn i cykl życia. Dzięki temu można łączyć główny kontener aplikacji z innymi kontenerami pełniącymi rolę pomocniczą, np. przyczepkami rejestrowania.

## <a name="virtual-network-deployment"></a>Wdrażanie sieci wirtualnej

Obecnie dostępne dla obciążeń produkcyjnych w podzbiorze regionów świadczenia usługi Azure, ta funkcja Azure Container Instances umożliwia [wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure](container-instances-vnet.md). Wdrożenie wystąpień kontenerów w podsieci w sieci wirtualnej umożliwia bezpieczną komunikację z innymi zasobami w sieci wirtualnej, w tym z zasobami lokalnymi (przy użyciu [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [usługi ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Następne kroki

Spróbuj wdrożyć kontener na platformie Azure za pomocą jednego polecenia, korzystając z naszego przewodnika Szybki start:

> [!div class="nextstepaction"]
> [Azure Container Instances — Szybki start](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
