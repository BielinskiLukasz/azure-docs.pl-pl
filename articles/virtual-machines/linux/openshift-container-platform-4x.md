---
title: Wdrażanie OpenShift kontenerów platformy 4. x na platformie Azure | Microsoft Docs
description: Wdróż OpenShift kontenerów platformy 4. x na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 9f807823b1d0d8079c43b2ba0e074a1c8a91b458
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392685"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Wdrażanie OpenShift kontenera platformy 4. x na platformie Azure

Wdrożenie platformy kontenerów OpenShift (OCP) 4,2 jest teraz obsługiwane na platformie Azure za pośrednictwem modelu infrastruktury obsługi administracyjnej (IPI) Instalatora.  Strona docelowa dla próby OpenShift 4 ma [try.OpenShift.com](https://try.openshift.com/). Aby zainstalować OCP 4,2 na platformie Azure, odwiedź stronę [Menedżera klastra Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Do uzyskania dostępu do tej witryny są wymagane poświadczenia Red Hat.


## <a name="notes"></a>Uwagi 

 - Do zainstalowania i uruchomienia programu OCP 4. x na platformie Azure jest wymagana nazwa główna usługi (SP). Azure Active Directory
     - SP musi mieć przyznane uprawnienie API **Application. ReadWrite. OwnedBy** for Azure Active Directory Graph
     - Aby to uprawnienie API zaczęło obowiązywać, Administrator dzierżawy usługi AAD musi udzielić zgody administratora.
     - SP musi mieć przyznane **współautor** i role **administratora dostępu użytkowników** do subskrypcji
 - Model instalacji dla oprogramowania OCP 4. x jest inny niż 3. x i nie ma Azure Resource Manager szablonów dostępnych do wdrożenia OCP 4. x na platformie Azure
 - Jeśli podczas instalacji wystąpią problemy, skontaktuj się z odpowiednią firmą (Microsoft lub Red Hat)

| Opis problemu | Punkt kontaktowy |
|-------------------|---------------|
| Problemy związane z platformą Azure (AAD, SP, subskrypcja platformy Azure itp.)                              | Microsoft |
| Problemy związane z OpenShift (błędy/błędy instalacji, subskrypcja Red Hat itp.) |  Red Hat  |




## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do platformy kontenerów OpenShift](https://docs.openshift.com)
