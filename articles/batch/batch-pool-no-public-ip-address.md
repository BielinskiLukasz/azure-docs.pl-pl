---
title: Utwórz pulę Azure Batch bez publicznych adresów IP
description: Dowiedz się, jak utworzyć pulę bez publicznych adresów IP
author: pkshultz
ms.topic: how-to
ms.date: 06/26/2020
ms.author: peshultz
ms.openlocfilehash: 30792314f5bffaf4d40fc4bf60a2706acdaad34b
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962445"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Utwórz pulę Azure Batch bez publicznych adresów IP

Podczas tworzenia puli Azure Batch można udostępnić pulę konfiguracji maszyny wirtualnej bez publicznego adresu IP. W tym artykule wyjaśniono, jak skonfigurować pulę wsadową bez publicznych adresów IP.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Dlaczego warto używać puli bez publicznych adresów IP?

Domyślnie wszystkie węzły obliczeniowe w puli konfiguracji Azure Batch maszyny wirtualnej mają przypisany publiczny adres IP. Ten adres jest używany przez usługę Batch do planowania zadań i komunikacji z węzłami obliczeniowymi, w tym dostęp wychodzący do Internetu. 

Aby ograniczyć dostęp do tych węzłów i zmniejszyć wykrywalność tych węzłów z Internetu, można zainicjować obsługę administracyjną puli bez publicznych adresów IP.

> [!IMPORTANT]
> Obsługa pul bez publicznych adresów IP w Azure Batch jest obecnie dostępna w publicznej wersji zapoznawczej w regionach zachodnie stany USA, Wschodnie stany USA, Południowo-środkowe stany USA, zachodnie stany USA 2, US Gov Wirginia i US Gov Arizona.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

- **Uwierzytelnianie**. Aby używać puli bez publicznych adresów IP w [sieci wirtualnej](./batch-virtual-network.md), interfejs API klienta usługi Batch musi korzystać z uwierzytelniania Azure Active Directory (AD). Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md). Jeśli nie tworzysz puli w sieci wirtualnej, może być używane uwierzytelnianie usługi Azure AD lub uwierzytelnianie oparte na kluczach.

- **Sieć wirtualna platformy Azure**. Jeśli tworzysz pulę w [sieci wirtualnej](batch-virtual-network.md), postępuj zgodnie z tymi wymaganiami i konfiguracjami. Aby przygotować sieć wirtualną z wyprzedzeniem z co najmniej jedną podsiecią, można użyć Azure Portal, Azure PowerShell, interfejsu wiersza polecenia (CLI) platformy Azure lub innych metod.
  - Sieć wirtualna musi znajdować się w tej samej subskrypcji i w tym samym regionie co konto usługi Batch użyte do utworzenia puli.
  - Podsieć określona dla puli musi mieć wystarczającą liczbę nieprzypisanych adresów IP do obsługi maszyn wirtualnych przeznaczony dla puli, czyli sumę właściwości puli `targetDedicatedNodes` i `targetLowPriorityNodes`. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, pula częściowo przydzieli węzły obliczeniowe, a następnie wystąpi błąd dotyczący zmiany rozmiaru.
  - Należy wyłączyć usługę link prywatny i zasady sieciowe punktu końcowego. Można to zrobić za pomocą interfejsu wiersza polecenia platformy Azure:```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```
  
> [!IMPORTANT]
> W przypadku każdego węzła dedykowanych 100 lub o niskim priorytecie usługa Batch przydziela jedną usługę łącza prywatnego i jeden moduł równoważenia obciążenia. Te zasoby są ograniczone przez [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. W przypadku dużych pul może być konieczne [zażądanie zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota) dla co najmniej jednego z tych zasobów. Ponadto nie należy stosować blokad zasobów do żadnych zasobów utworzonych w usłudze Batch, ponieważ uniemożliwia to Oczyszczanie zasobów w wyniku akcji inicjowanych przez użytkownika, takich jak usuwanie puli lub zmienianie rozmiarów na zero.

## <a name="current-limitations"></a>Bieżące ograniczenia

1. Pule bez publicznych adresów IP muszą używać konfiguracji maszyny wirtualnej, a nie Cloud Services konfiguracji.
1. [Konfiguracja niestandardowego punktu końcowego](pool-endpoint-configuration.md) na potrzeby wsadowych węzłów obliczeniowych nie działa z pulami bez publicznych adresów IP.
1. Ponieważ nie ma publicznych adresów IP, nie można [używać własnych określonych publicznych adresów IP](create-pool-public-ip.md) z tym typem puli.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Utwórz pulę bez publicznych adresów IP w Azure Portal

1. W witrynie Azure Portal przejdź do swojego konta usługi Batch. 
1. W oknie **Ustawienia** po lewej stronie wybierz pozycję **Pule**.
1. W oknie **Pule** wybierz pozycję **Dodaj**.
1. W oknie **Dodawanie puli** wybierz opcję, która ma zostać użyta z listy rozwijanej **Typ obrazu** .
1. Wybierz poprawny **wydawcę/ofertę/jednostkę SKU** obrazu.
1. Określ pozostałe wymagane ustawienia, w tym **rozmiar węzła**, **docelowe węzły dedykowane**i **węzły o niskim priorytecie**, a także wszystkie wymagane ustawienia opcjonalne.
1. Opcjonalnie wybierz sieć wirtualną i podsieć, której chcesz użyć. Ta sieć wirtualna musi znajdować się w tej samej grupie zasobów co tworzona Pula.
1. W polu **Typ aprowizacji adresów IP**wybierz pozycję **NoPublicIPAddresses**.

![Ekran dodawania puli z wybraną pozycją NoPublicIPAddresses](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Korzystanie z interfejsu API REST usługi Batch w celu utworzenia puli bez publicznych adresów IP

W poniższym przykładzie pokazano, jak za pomocą [interfejsu API REST Azure Batch](/rest/api/batchservice/pool/add) utworzyć pulę korzystającą z publicznych adresów IP.

### <a name="rest-api-uri"></a>Identyfikator URI interfejsu API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Treść żądania

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "maxTasksPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Dostęp wychodzący do Internetu

W puli bez publicznych adresów IP maszyny wirtualne nie będą mogły uzyskać dostępu do publicznej sieci Internet, o ile nie skonfigurowano odpowiedniej konfiguracji sieci, na przykład przy użyciu [translatora adresów sieciowych sieci wirtualnej](../virtual-network/nat-overview.md). Należy pamiętać, że translator adresów sieciowych zezwala tylko na dostęp wychodzący do Internetu z maszyn wirtualnych w sieci wirtualnej. Utworzone wsadowo węzły obliczeniowe nie będą dostępne publicznie, ponieważ nie mają skojarzonych publicznych adresów IP.

Innym sposobem zapewnienia łączności wychodzącej jest użycie trasy zdefiniowanej przez użytkownika (UDR). Pozwala to na kierowanie ruchu do komputera proxy, który ma publiczny dostęp do Internetu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [tworzeniu pul w sieci wirtualnej](batch-virtual-network.md).
- Dowiedz się, jak [używać prywatnych punktów końcowych przy użyciu kont wsadowych](private-connectivity.md).
