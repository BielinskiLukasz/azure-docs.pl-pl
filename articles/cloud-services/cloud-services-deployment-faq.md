---
title: Problemy z wdrażaniem Microsoft Azure Cloud Services często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących wdrażania Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2ffa6d7b1cf0550c97a60614f3f00ddc4b955218
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154797"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemy z wdrażaniem Cloud Services platformy Azure: Często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące problemów z wdrażaniem [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informacje o rozmiarze można także znaleźć na [stronie Cloud Services rozmiaru maszyny wirtualnej](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Dlaczego wdrożenie usługi w chmurze w miejscu przejściowym czasami kończy się niepowodzeniem z powodu błędu alokacji zasobów, jeśli istnieje już istniejące wdrożenie w miejscu produkcyjnym?
Jeśli usługa w chmurze ma wdrożenie w dowolnym miejscu, cała usługa w chmurze jest przypięta do określonego klastra. Oznacza to, że jeśli wdrożenie już istnieje w gnieździe produkcyjnym, nowe wdrożenie przejściowe może być przydzielono tylko w tym samym klastrze, w którym znajduje się miejsce produkcyjne.

Błędy alokacji występują, gdy klaster, w którym znajduje się usługa w chmurze, nie ma wystarczającej ilości fizycznych zasobów obliczeniowych do zaspokojenia Twojego żądania wdrożenia.

Aby uzyskać pomoc w rozwiązywaniu takich błędów alokacji, [Zobacz niepowodzenie alokacji usługi w chmurze: Rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Dlaczego skalowanie w górę lub skalowanie wdrożenia usługi w chmurze czasami powoduje niepowodzenie alokacji?
Gdy usługa w chmurze jest wdrażana, zazwyczaj jest przypięta do określonego klastra. Oznacza to, że skalowanie w górę/w górę istniejącej usługi w chmurze musi przydzielić nowe wystąpienia w tym samym klastrze. Jeśli klaster zbliża się do pojemności lub żądany rozmiar i typ maszyny wirtualnej nie są dostępne, żądanie może zakończyć się niepowodzeniem.

Aby uzyskać pomoc w rozwiązywaniu takich błędów alokacji, [Zobacz niepowodzenie alokacji usługi w chmurze: Rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Dlaczego wdrożenie usługi w chmurze w grupie koligacji czasami powoduje niepowodzenie alokacji?
Nowe wdrożenie do pustej usługi w chmurze może być przydzielone przez sieć szkieletową w dowolnym klastrze w tym regionie, chyba że usługa w chmurze jest przypięta do grupy koligacji. W tym samym klastrze podjęto próbę wdrożenia w tej samej grupie koligacji. Jeśli klaster zbliża się do pojemności, żądanie może zakończyć się niepowodzeniem.

Aby uzyskać pomoc w rozwiązywaniu takich błędów alokacji, [Zobacz niepowodzenie alokacji usługi w chmurze: Rozwiązania](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Dlaczego zmiana rozmiaru maszyny wirtualnej lub dodanie nowej maszyny wirtualnej do istniejącej usługi w chmurze czasami powoduje niepowodzenie alokacji?
Klastry w centrum danych mogą mieć różne konfiguracje typów maszyn (na przykład serii, serii Av2, serii D, serii Dv2, serii G, serii H itp.). Ale nie wszystkie klastry muszą mieć wszystkie rodzaje maszyn wirtualnych. Na przykład jeśli spróbujesz dodać maszynę wirtualną z serii D do usługi w chmurze, która została już wdrożona w klastrze tylko z serii, wystąpi błąd alokacji. Taka sytuacja występuje również w przypadku próby zmiany rozmiarów jednostki SKU maszyny wirtualnej (na przykład przełączenia z serii do serii D).

Aby uzyskać pomoc w rozwiązywaniu takich błędów alokacji, [Zobacz niepowodzenie alokacji usługi w chmurze: Rozwiązania](cloud-services-allocation-failures.md#solutions).

Aby sprawdzić dostępne rozmiary w Twoim regionie, zobacz [Microsoft Azure: Dostępne produkty według regionów](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Dlaczego wdrożenie usługi w chmurze kończy się niepowodzeniem z powodu limitów/przydziałów/ograniczeń dotyczących mojej subskrypcji lub usługi?
Wdrożenie usługi w chmurze może zakończyć się niepowodzeniem, jeśli zasoby, które są wymagane do przydzielenia, przekraczają domyślny lub maksymalny limit przydziału dozwolony dla usługi na poziomie regionu/centrum danych. Aby uzyskać więcej informacji, zobacz [limity Cloud Services](../azure-subscription-service-limits.md#azure-cloud-services-limits).

Możesz również śledzić bieżące użycie/przydział dla subskrypcji w portalu: Azure Portal = > subscriptions = \<> odpowiednie subskrypcje > = > "użycie + przydział".

Informacje dotyczące użycia zasobów/zużycia można także pobrać za pośrednictwem interfejsy API rozliczeń platformy Azure. Zobacz [interfejs API użycia zasobów platformy Azure (wersja zapoznawcza)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Jak zmienić rozmiar wdrożonej maszyny wirtualnej usługi w chmurze bez jej ponownego wdrażania?
Nie można zmienić rozmiaru maszyny wirtualnej wdrożonej usługi w chmurze bez jej ponownego wdrożenia. Rozmiar maszyny wirtualnej jest wbudowany w CSDEF, który można zaktualizować tylko przy użyciu ponownego wdrożenia.

Aby uzyskać więcej informacji, zobacz [jak zaktualizować usługę w chmurze](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Dlaczego nie mogę wdrożyć Cloud Services za pośrednictwem interfejsów API zarządzania usługami lub programu PowerShell w przypadku korzystania z konta magazynu Azure Resource Manager? 

Ponieważ usługa w chmurze jest zasobem klasycznym, który nie jest bezpośrednio zgodny z modelem Azure Resource Manager, nie można go skojarzyć z kontami magazynu Azure Resource Manager. Oto kilka opcji: 
 
- Wdrażanie przy użyciu interfejsu API REST.

    Podczas wdrażania za pośrednictwem interfejsu API REST usługi Service Management można obejść ograniczenie, określając adres URL sygnatury dostępu współdzielonego dla magazynu obiektów blob, który będzie działał zarówno na klasycznym, jak i Azure Resource Manager koncie magazynu. Przeczytaj więcej na temat właściwości "PackageUrl" w [tym miejscu](/previous-versions/azure/reference/ee460813(v=azure.100)).
  
- Wdrażanie za [Azure Portal](https://portal.azure.com).

    Będzie to [Azure Portal](https://portal.azure.com) możliwe, gdy połączenie przechodzi przez serwer proxy/podkładkę, która umożliwia komunikację między zasobami Azure Resource Manager i klasycznymi. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Dlaczego Azure Portal wymaga podania konta magazynu na potrzeby wdrożenia? 

W portalu klasycznym pakiet został przekazany bezpośrednio do warstwy interfejsu API zarządzania, a następnie warstwa interfejsu API tymczasowo umieszcza pakiet w wewnętrznym koncie magazynu.  Ten proces powoduje problemy z wydajnością i skalowalnością, ponieważ warstwa interfejsu API nie została zaprojektowana jako usługa przekazywania plików.  W Azure Portal (Menedżer zasobów model wdrażania) pomijamy tymczasowy krok pierwszego przekazania do warstwy interfejsu API, co prowadzi do szybszego i bardziej niezawodnego wdrożenia. 

Podobnie jak w przypadku kosztu, jest to bardzo małe i można ponownie użyć tego samego konta magazynu we wszystkich wdrożeniach. [Kalkulator kosztów magazynu](https://azure.microsoft.com/pricing/calculator/#storage1) umożliwia określenie kosztu przekazania pakietu usługi (CSPKG), pobranie CSPKG, a następnie usunięcie CSPKG. 
