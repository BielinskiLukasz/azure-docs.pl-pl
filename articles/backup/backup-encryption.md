---
title: Szyfrowanie w usłudze Azure Backup
description: Dowiedz się, jak funkcje szyfrowania w programie Azure Backup ułatwiają ochronę danych kopii zapasowych i zaspokajanie potrzeb firmy.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: f55deba58cd7b725bd030409296794e5de911c09
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017910"
---
# <a name="encryption-in-azure-backup"></a>Szyfrowanie w usłudze Azure Backup

Wszystkie dane kopii zapasowej są automatycznie szyfrowane, gdy są przechowywane w chmurze przy użyciu szyfrowania usługi Azure Storage, co pomaga sprostać zobowiązaniom dotyczącym zabezpieczeń i zgodności. Te dane przechowywane są szyfrowane przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Oprócz szyfrowania przechowywane dane kopii zapasowej są przesyłane za pośrednictwem protokołu HTTPS. Zawsze pozostaje w sieci szkieletowej platformy Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Poziomy szyfrowania w Azure Backup

Azure Backup obejmuje szyfrowanie na dwóch poziomach:

- **Szyfrowanie danych w magazynie Recovery Services**
  - **Przy użyciu kluczy zarządzanych przez platformę**: Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Aby włączyć szyfrowanie, nie trzeba podejmować żadnych działań jawnych. Ma to zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie Recovery Services.
  - **Przy użyciu kluczy zarządzanych przez klienta**: podczas tworzenia kopii zapasowej Virtual Machines platformy Azure możesz zaszyfrować dane przy użyciu kluczy szyfrowania należących do Ciebie i zarządzanych przez użytkownika. Azure Backup umożliwia korzystanie z kluczy RSA przechowywanych w Azure Key Vault na potrzeby szyfrowania kopii zapasowych. Klucz szyfrowania używany do szyfrowania kopii zapasowych może być inny niż użyty dla źródła. Dane są chronione przy użyciu klucza szyfrowania danych opartego na protokole AES 256, który jest z kolei chroniony przy użyciu kluczy. Zapewnia to pełną kontrolę nad danymi i kluczami. Aby umożliwić szyfrowanie, wymagane jest przyznanie magazynowi Recovery Services dostępu do klucza szyfrowania w Azure Key Vault. W razie konieczności można wyłączyć klucz lub odwołać dostęp. Należy jednak włączyć szyfrowanie przy użyciu kluczy przed podjęciem próby włączenia ochrony wszystkich elementów do magazynu. [Dowiedz się więcej tutaj](encryption-at-rest-with-cmk.md).
  - **Szyfrowanie na poziomie infrastruktury**: Oprócz szyfrowania danych w magazynie Recovery Services przy użyciu kluczy zarządzanych przez klienta, można również wybrać dodatkową warstwę szyfrowania skonfigurowaną w infrastrukturze magazynu. To szyfrowanie infrastruktury jest zarządzane przez platformę. Wraz z szyfrowaniem w stanie spoczynku przy użyciu kluczy zarządzanych przez klienta umożliwia szyfrowanie dwuwarstwowe danych kopii zapasowej. Szyfrowanie infrastruktury można skonfigurować tylko wtedy, gdy użytkownik najpierw zdecyduje się użyć własnych kluczy do szyfrowania w stanie spoczynku. Szyfrowanie infrastruktury używa kluczy zarządzanych przez platformę do szyfrowania danych.
- **Szyfrowanie specyficzne dla obciążenia, którego kopia zapasowa jest wykonywana**  
  - **Kopia zapasowa maszyny wirtualnej platformy Azure**: Azure Backup obsługuje tworzenie kopii zapasowych maszyn wirtualnych z dyskami szyfrowanymi przy użyciu [kluczy zarządzanych przez platformę](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#platform-managed-keys), a także [kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) i zarządzanych przez użytkownika. Ponadto można również utworzyć kopię zapasową maszyn wirtualnych platformy Azure, które mają zaszyfrowane dyski systemu operacyjnego lub danych przy użyciu [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). ADE używa funkcji BitLocker dla maszyn wirtualnych z systemem Windows, i DM-Crypt dla maszyn wirtualnych z systemem Linux, aby przeprowadzić szyfrowanie w gościu.

>[!NOTE]
>Szyfrowanie infrastruktury jest obecnie w ograniczonej wersji zapoznawczej i jest dostępne w regionach Wschodnie stany USA, Stany USA West2, Południowo-środkowe stany USA, US Gov Arizona i stany USA GOV Wirginia. Jeśli chcesz użyć funkcji w dowolnym z tych regionów, Wypełnij [ten formularz](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) i Wyślij wiadomość e-mail na adres [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Kolejne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)
- [Azure Backup często zadawane](backup-azure-backup-faq.md#encryption) pytania dotyczące niezwiązanych z szyfrowaniem
