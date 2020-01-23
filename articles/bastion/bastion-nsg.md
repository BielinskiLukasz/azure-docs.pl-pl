---
title: Praca z maszynami wirtualnymi i sieciowych grup zabezpieczeń na platformie Azure bastionu | Microsoft Docs
description: W tym artykule opisano sposób włączania dostępu sieciowej grupy zabezpieczeń za pomocą usługi Azure bastionu
services: bastion
author: ashjain
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ashishj
ms.openlocfilehash: 8fb48b3dbe0d727f92bd2e0fec91ac2dbf33338b
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547629"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Praca z usługami sieciowej grupy zabezpieczeń Access i Azure bastionu

Podczas pracy z usługą Azure bastionu można użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Aby uzyskać więcej informacji, zobacz [grupy zabezpieczeń](../virtual-network/security-overview.md). 

![Architektura](./media/bastion-nsg/nsg-architecture.png)

Na tym diagramie:

* Host bastionu jest wdrażany w sieci wirtualnej.
* Użytkownik nawiązuje połączenie z Azure Portal przy użyciu dowolnej przeglądarki HTML5.
* Użytkownik przechodzi do maszyny wirtualnej platformy Azure do protokołu RDP/SSH.
* Połącz integrację — pojedyncze kliknięcie sesji RDP/SSH wewnątrz przeglądarki
* Na maszynie wirtualnej platformy Azure nie jest wymagany publiczny adres IP.

## <a name="nsg"></a>Sieciowe grupy zabezpieczeń

W tej sekcji przedstawiono ruch sieciowy między użytkownikiem a usługą Azure bastionu oraz docelowymi maszynami wirtualnymi w sieci wirtualnej:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Usługa Azure bastionu jest wdrażana w odróżnieniu od AzureBastionSubnet.

* **Ruch przychodzący:**

   * **Ruch przychodzący z publicznej sieci Internet:** Usługa Azure bastionu utworzy publiczny adres IP, który wymaga, aby port 443 był włączony w publicznym adresie IP dla ruchu przychodzącego. NIE trzeba otwierać portu 3389/22 w AzureBastionSubnet.
   * **Ruch przychodzący z płaszczyzny kontroli usługi Azure bastionu:** W przypadku łączności z płaszczyzną kontroli Włącz port 443 przychodzące z tagu usługi **bramy** . Dzięki temu płaszczyzna kontroli, czyli Menedżer bramy, może komunikować się z usługą Azure bastionu.

* **Ruch wychodzący:**

   * **Ruch wychodzący do docelowych maszyn wirtualnych:** Usługa Azure bastionu będzie docierać do docelowych maszyn wirtualnych za pośrednictwem prywatnego adresu IP. Sieciowych grup zabezpieczeń musi zezwalać na ruch przychodzący do innych docelowych podsieci maszyn wirtualnych dla portów 3389 i 22.
   * **Ruch przychodzący do innych publicznych punktów końcowych na platformie Azure:** Usługa Azure bastionu musi mieć możliwość łączenia się z różnymi publicznymi punktami końcowymi na platformie Azure (na przykład do przechowywania dzienników diagnostycznych i dzienników zliczania). Z tego powodu usługa Azure bastionu potrzebuje ruchu wychodzącego do 443 do **AzureCloud** Service Tag.

* **Docelowa podsieć maszyny wirtualnej:** Jest to podsieć zawierająca docelową maszynę wirtualną, do której ma zostać zainstalowana protokół RDP/SSH.

   * **Ruch przychodzący z usługi Azure bastionu:** Usługa Azure bastionu będzie docierać do docelowej maszyny wirtualnej za pośrednictwem prywatnego adresu IP. Porty RDP/SSH (odpowiednio porty 3389/22) muszą być otwarte dla docelowej maszyny wirtualnej po stronie prywatnego adresu IP. Najlepszym rozwiązaniem jest dodanie w tej regule zakresu adresów IP podsieci usługi Azure bastionu, aby zezwolić tylko bastionu na otwieranie tych portów na docelowych maszynach wirtualnych w docelowej podsieci maszyn wirtualnych.

## <a name="apply"></a>Zastosuj sieciowych grup zabezpieczeń do AzureBastionSubnet

Jeśli utworzysz i zastosujesz sieciowej grupy zabezpieczeń do ***AzureBastionSubnet***, upewnij się, że dodano następujące reguły w sieciowej grupy zabezpieczeń. Jeśli nie dodasz tych reguł, sieciowej grupy zabezpieczeń tworzenia/aktualizacji nie powiedzie się:

* **Łączność płaszczyzny sterowania:** Przychodzące w dniu 443 z programu Gatewaymanager
* **Rejestrowanie diagnostyczne i inne:** Wychodzące od 443 do AzureCloud. Tagi regionalne w tym tagu usługi nie są jeszcze obsługiwane.
* **Docelowa maszyna wirtualna:** Wychodzące dla 3389 i 22 do VirtualNetwork

Przykład reguły sieciowej grupy zabezpieczeń jest dostępny dla odwołania w tym [szablonie szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [często zadawane pytania](bastion-faq.md).
