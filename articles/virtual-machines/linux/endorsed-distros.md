---
title: Dystrybucje systemu Linux zatwierdzone na platformie Azure | Microsoft Docs
description: Dowiedz się więcej o dystrybucji z systemem Linux na platformie Azure, w tym wskazówki dotyczące Ubuntu, CentOS, Oracle i SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: szark
ms.openlocfilehash: c9537ec39e58d5703ed3bc1774520a12b25147b0
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615584"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Zatwierdzone dystrybucje systemu Linux na platformie Azure
Partnerzy udostępniają obrazy systemu Linux w portalu Azure Marketplace. Pracujemy z różnymi społecznościami systemu Linux w celu dodania jeszcze większej liczby wersji do listy rozpowszechnianych informacji. W międzyczasie w przypadku dystrybucji, które nie są dostępne w portalu Marketplace, zawsze można przenieść własne systemy Linux zgodnie z instrukcjami w temacie [Tworzenie i przekazywanie wirtualnego dysku twardego zawierającego system operacyjny Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Obsługiwane dystrybucje i wersje
W poniższej tabeli wymieniono dystrybucje systemu Linux i wersje, które są obsługiwane w systemie Azure. Zapoznaj się z [pomocą techniczną dla obrazów systemu Linux w Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) , aby uzyskać bardziej szczegółowe informacje na temat obsługi technologii dla systemów Linux i Open Source na platformie Azure.

Sterowniki systemu Linux Integration Services (LIS) dla funkcji Hyper-V i platformy Azure to moduły jądra, które firma Microsoft bezpośrednio współużytkuje z nadrzędnym jądrem systemu Linux.  Niektóre sterowniki LIS są domyślnie wbudowane w jądro dystrybucji. Starsze dystrybucje oparte na systemie Red Hat Enterprise (RHEL)/CentOS są dostępne jako osobne pobieranie w systemie [Linux Integration Services w wersji 4,2 dla funkcji Hyper-V i platformy Azure](https://www.microsoft.com/download/details.aspx?id=55106). Więcej informacji o sterownikach LIS można znaleźć w temacie [wymagania jądra systemu Linux](create-upload-generic.md#linux-kernel-requirements) .

Agent platformy Azure dla systemu Linux jest już wstępnie zainstalowany w obrazach portalu Azure Marketplace i jest zazwyczaj dostępny w repozytorium pakietu dystrybucji. Kod źródłowy można znaleźć w witrynie [GitHub](https://github.com/azure/walinuxagent).


| Dystrybucja | Wersja | Sterowniki | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 +, 7.0 +, 8.0 + |CentOS 6,3: [pobieranie lis](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: w jądrze |Pakiet: w [repozytorium](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0 + |W jądrze |Kod źródłowy: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7,9 +, 8.2 +, 9, 10 |W jądrze |Pakiet: w repozytorium w obszarze "waagent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4 +, 7.0 + |W jądrze |Pakiet: w repozytorium w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6,7 +, 7.1 +, 8.0 + |W jądrze |Pakiet: w repozytorium w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES dla SAP<br>11 SP4<br>12 SP1 +<br>15|W jądrze |Pakiet:<p> 11 w [chmurze: repozytorium narzędzi](https://build.opensuse.org/project/show/Cloud:Tools)<br>w przypadku 12 uwzględnionych w module "chmura publiczna" w obszarze "Python-Azure-Agent"<br/>Kod źródłowy: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE przestępny 42.2 + |W jądrze |Pakiet: w [chmurze: narzędzia](https://build.opensuse.org/project/show/Cloud:Tools) repozytorium w obszarze "Python-Azure-Agent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |W jądrze |Pakiet: w repozytorium w obszarze "walinuxagent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** informacje o rozszerzonej obsłudze Ubuntu 12,04 i 14,04 można znaleźć tutaj: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Erze aktualizacji obrazów
System Azure wymaga, aby wydawcy zatwierdzonych dystrybucji systemu Linux regularnie aktualizować swoje obrazy w portalu Azure Marketplace przy użyciu najnowszych poprawek i poprawek zabezpieczeń, co kwartał lub szybciej erze. Zaktualizowane obrazy w portalu Azure Marketplace są automatycznie dostępne dla klientów jako nowe wersje jednostki SKU obrazu. Więcej informacji o sposobach znajdowania obrazów systemu Linux: [Znajdowanie obrazów maszyn wirtualnych z systemem Linux w portalu Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Dodatkowe linki
 - [Cykl życia obrazu chmury publicznej SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Jądra dostosowane do platformy Azure

Platforma Azure współpracuje ściśle z różnymi rozpowszechnianymi dystrybucjami systemu Linux w celu optymalizacji obrazów opublikowanych w portalu Azure Marketplace. Jednym z aspektów tej współpracy jest opracowywanie "dostrojonych" jądra systemu Linux, które są zoptymalizowane pod kątem platformy Azure i dostarczane jako w pełni obsługiwane składniki dystrybucji w systemie Linux. Jądra dostosowane do platformy Azure zawierają nowe funkcje i ulepszenia wydajności oraz szybciej (zazwyczaj co kwartał) erze w porównaniu z domyślnymi lub ogólnymi jądrami, które są dostępne w dystrybucji.

W większości przypadków te jądra są wstępnie zainstalowane na domyślnych obrazach w portalu Azure Marketplace, dzięki czemu klienci platformy Azure będą natychmiast mogli korzystać z tych zoptymalizowanych jądra. Więcej informacji o tych jądrach dostrojonych na platformie Azure można znaleźć w następujących linkach:

 - CentOS z platformą Azure — dostępne dla jądra za pośrednictwem usługi CentOS Virtualization — [Informacje dodatkowe](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian jądro w chmurze — dostępne za pomocą obrazu Debian 10 i Debian 9 "dla portów" na platformie Azure — [więcej informacji](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES jądro systemu Azure — [więcej informacji](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu jądro systemu Azure — [więcej informacji](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partnerzy

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Z witryny sieci Web CoreOS:

*CoreOS jest zaprojektowana pod kątem bezpieczeństwa, spójności i niezawodności. Zamiast instalować pakiety za pośrednictwem yum lub apt, CoreOS używa kontenerów systemu Linux do zarządzania usługami na wyższym poziomie abstrakcji. Kod pojedynczej usługi i wszystkie zależności są umieszczane w kontenerze, który można uruchomić na jednym lub wielu maszynach CoreOS.*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ to niezależna firma konsultingowa i usług, która specjalizacje opracowywania i wdrażania profesjonalnych rozwiązań przy użyciu bezpłatnego oprogramowania. Jako wiodące specjaliści z branży Open Source credativ mają międzynarodowyą funkcję rozpoznawania wielu działów IT korzystających z pomocy technicznej. W połączeniu z firmą Microsoft credativ aktualnie przygotowuje odpowiednie obrazy Debian dla Debian 8 (Jessie) i Debian przed 7 (wheezy). Oba obrazy są specjalnie przeznaczone do uruchamiania na platformie Azure i mogą być łatwo zarządzane za pośrednictwem platformy. Credativ również będzie obsługiwał długoterminową konserwację i aktualizację obrazów Debian dla platformy Azure za pomocą Centrum pomocy technicznej typu open source.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Strategia firmy Oracle polega na zaoferowaniu szerokiego portfolio rozwiązań dla chmur publicznych i prywatnych. Strategia ta umożliwia klientom wybór i elastyczność wdrażania oprogramowania Oracle w chmurach Oracle i innych chmurach. Partnerstwo firmy Oracle z firmą Microsoft umożliwia klientom wdrażanie oprogramowania Oracle w chmurach publicznych i prywatnych firmy Microsoft przy zapewnieniu certyfikacji i wsparcia firmy Oracle.  Zobowiązania firmy Oracle i inwestycje w rozwiązania chmury publicznej i prywatnej w systemie Oracle nie są zmieniane.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Wiodący na świecie dostawca rozwiązań typu "open source", Red Hat pomaga więcej niż 90% listy Fortune 500 firmy, rozwiązywać wyzwania biznesowe, wyrównać swoje działania i strategie biznesowe oraz przygotować się na przyszłość technologii. Red Hat robi to, dostarczając bezpieczne rozwiązania poprzez otwarty model biznesowy i przystępny, przewidywalny model subskrypcji.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server na platformie Azure to sprawdzona platforma, która zapewnia najwyższą niezawodność i bezpieczeństwo w chmurze obliczeniowej. Uniwersalna platforma Linux zapewnia bezproblemowe integrację z usługami w chmurze platformy Azure w celu zapewnienia łatwego zarządzania środowiskiem w chmurze. W przypadku ponad 9 200 certyfikowanych aplikacji z ponad 1 800 niezależnych dostawców oprogramowania dla SUSE Linux Enterprise Server SUSE zapewnia, że obciążenia działające w centrum danych mogą być bezpiecznie wdrożone na platformie Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Inżynieria kanoniczna i otwarta społeczność zarządzania Wspólnotą Ubuntu sukces w zakresie rozwiązań klienta, serwera i chmury, w tym osobistych usług w chmurze dla klientów. Wykanoniczna funkcja ujednoliconej, wolnej platformy w Ubuntu, od telefonu do chmury, zapewnia rodzinę spójnych interfejsów dla telefonów, tabletów, programów telewizyjnych i komputerów stacjonarnych. Ta wizja sprawia, że Ubuntu pierwszy wybór dla różnorodnych instytucji od dostawców chmury publicznej do osób tworzących odbiorców elektronicznych i ulubionych między indywidualnym technologists.

W przypadku deweloperów i centrów inżynieryjnych na całym świecie, kanoniczny jest jednoznacznie umiejscowiony do partnera z markami sprzętowymi, dostawcami zawartości i programistami oprogramowania, aby Ubuntu rozwiązania na rynek dla komputerów, serwerów i urządzeń podręcznych.
