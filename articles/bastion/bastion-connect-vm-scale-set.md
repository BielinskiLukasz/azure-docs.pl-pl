---
title: Nawiązywanie połączenia z zestawem skalowania maszyn wirtualnych z systemem Windows przy użyciu usługi Azure bastionu | Microsoft Docs
description: W tym artykule dowiesz się, jak nawiązać połączenie z zestawem skalowania maszyn wirtualnych platformy Azure przy użyciu usługi Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 446784155cf6f72cfaa80523ed3913eacc7e5cfc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513135"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Nawiązywanie połączenia z zestawem skalowania maszyn wirtualnych przy użyciu usługi Azure bastionu

W tym artykule opisano sposób bezpiecznego i bezproblemowego połączenia protokołu RDP z wystąpieniem zestawu skalowania maszyn wirtualnych z systemem Windows w sieci wirtualnej platformy Azure przy użyciu usługi Azure bastionu. Możesz połączyć się z wystąpieniem zestawu skalowania maszyn wirtualnych bezpośrednio z Azure Portal. Gdy używasz usługi Azure Bastion, maszyny wirtualne nie wymagają klienta, agenta ani dodatkowego oprogramowania. Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [Omówienie](bastion-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Upewnij się, że skonfigurowano hosta usługi Azure bastionu dla sieci wirtualnej, w której znajduje się zestaw skalowania maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [Tworzenie hosta usługi Azure bastionu](bastion-create-host-portal.md). Gdy usługa bastionu zostanie zainicjowana i wdrożona w sieci wirtualnej, można jej używać do nawiązywania połączenia z wystąpieniem zestawu skalowania maszyn wirtualnych w tej sieci wirtualnej. Bastionu zakłada, że używasz protokołu RDP, aby nawiązać połączenie z zestawem skalowania maszyn wirtualnych z systemem Windows, i SSH, aby nawiązać połączenie z zestawem skalowania maszyn wirtualnych z systemem Linux. Aby uzyskać informacje o połączeniu z maszyną wirtualną z systemem Linux, zobacz [nawiązywanie połączenia z maszyną wirtualną — Linux](bastion-connect-vm-ssh.md).

## <a name="rdp"></a>Nawiązywanie połączenia przy użyciu protokołu RDP

1. Otwórz [portal Azure](https://portal.azure.com). Przejdź do zestawu skalowania maszyn wirtualnych, z którym chcesz nawiązać połączenie.

   ![Nimi](./media/bastion-connect-vm-scale-set/1.png)
2. Przejdź do wystąpienia zestawu skalowania maszyn wirtualnych, z którym chcesz nawiązać połączenie, a następnie wybierz pozycję **Połącz**. W przypadku korzystania z połączenia RDP zestaw skalowania maszyn wirtualnych powinien być zestawem skalowania maszyn wirtualnych z systemem Windows.

   ![zestaw skalowania maszyn wirtualnych](./media/bastion-connect-vm-scale-set/2.png)
3. Po wybraniu opcji **Połącz**zostanie wyświetlony pasek boczny z trzema kartami — RDP, SSH i bastionu. Na pasku bocznym wybierz kartę **bastionu** . Jeśli nie zainicjowano obsługi administracyjnej usługi bastionu dla sieci wirtualnej, możesz wybrać link do konfiguracji bastionu. Instrukcje dotyczące konfiguracji znajdują się w temacie [Configure bastionu](bastion-create-host-portal.md).

   ![Karta bastionu](./media/bastion-connect-vm-scale-set/3.png)
4. Na karcie bastionu wprowadź nazwę użytkownika i hasło do zestawu skalowania maszyn wirtualnych, a następnie wybierz pozycję **Połącz**.

   ![nawiązywania połączenia](./media/bastion-connect-vm-scale-set/4.png)
5. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w Azure Portal (za pośrednictwem HTML5) przy użyciu portu 443 i usługi bastionu.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [bastionu często zadawanych pytań](bastion-faq.md).