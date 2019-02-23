---
title: Podręcznik publikowania oferty aplikacji zarządzanej aplikacji platformy Azure
description: W tym artykule opisano wymagania aby opublikować aplikację zarządzaną w witrynie Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: 432a65771727b68dab23fbaeee1e3a08f7759420
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733082"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Aplikacje platformy Azure: Oferty aplikacji zarządzanej Podręcznik publikowania

Aplikacji zarządzanej jest jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Użyj tego przewodnika, aby poznać wymagania dla tej oferty. 

Są to oferty transakcji, które są wdrożone i rozliczane za pośrednictwem portalu Marketplace. Wywołanie akcji, które użytkownik będzie widział jest "Pobierz teraz."

Korzystanie z aplikacji platformy Azure: typ oferty aplikacji zarządzanych, gdy wymagane są następujące warunki:
- Możesz wdrożyć albo rozwiązanie oparte na subskrypcji dla klienta przy użyciu maszyny Wirtualnej lub całego rozwiązania opartego na usłudze IaaS.
- Użytkownik lub klient wymaga, że rozwiązanie jest zarządzana przez partnera.

>[!NOTE]
>Na przykład partnera może być SI lub dostawcą usługi zarządzanej (MSP).  

## <a name="managed-application-offer"></a>Oferty aplikacji zarządzanych

|Wymagania |Szczegóły  |
|---------|---------|
|Wdrożone do subskrypcji platformy Azure dla klientów | Aplikacje zarządzane musi zostać wdrożony w subskrypcji klienta i mogą być zarządzane przez stronę 3 | 
|Rozliczeń oraz metod pomiarów    |  Zasoby będą aprowizowane w subskrypcji platformy Azure przez klienta. Płatność za rzeczywiste użycie (PŁATNICZEGO) maszyn wirtualnych zostanie wykonany w z klientem za pośrednictwem firmy Microsoft, które są rozliczane za pośrednictwem subskrypcji platformy Azure przez klienta (PŁATNICZEGO) 
W przypadku bring-your-own-license gdy firma Microsoft rozlicza koszty związane z infrastrukturą w subskrypcji klienta możesz będzie transact oprogramowanie opłaty dla klientów bezpośrednio        |
|Wirtualny dysk twardy (VHD) zgodnych z platformą Azure    |   Maszyny wirtualne muszą zostać skompilowane na systemie Windows lub Linux.<ul> <ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Linux, zobacz [dystrybucje systemu Linux zalecanych dla na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego Windows, zobacz [utworzyć wirtualny dysk twardy zgodnych z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Aplikacje zarządzane musi być możliwy do wdrożenia w portalu Marketplace. Jeśli komunikacji z klientami jest istotna, możesz powinni skontaktować się zainteresowanych klientów po włączeniu udostępniania.  


## <a name="next-steps"></a>Kolejne kroki
Jeśli użytkownik jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) w portalu marketplace.

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego

- [Zaloguj się do portalu Cloud Partner](https://cloudpartner.azure.com) do utworzenia lub zakończyć oferty.
