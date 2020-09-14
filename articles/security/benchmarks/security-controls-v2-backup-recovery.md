---
title: Azure Security test — wersja 2 — kopia zapasowa i odzyskiwanie
description: Usługa Azure Security test — tworzenie kopii zapasowych i odzyskiwanie
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fe6861a3319b9d9c0e6535ee3303c90f0a0f26c8
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059265"
---
# <a name="security-control-backup-and-recovery"></a>Kontrola zabezpieczeń: kopia zapasowa i odzyskiwanie

Tworzenie kopii zapasowych i odzyskiwanie obejmuje kontrolki, aby zapewnić, że kopie zapasowe danych i konfiguracji w różnych warstwach usług są wykonywane, weryfikowane i chronione.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zapewnianie zwykłych zautomatyzowanych kopii zapasowych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Upewnij się, że tworzysz kopię zapasową systemów i danych, aby zachować ciągłość działania po nieoczekiwanym zdarzeniu. Powinno to być wskazówkami dla celów punktu odzyskiwania (RPO) i celu czasu odzyskiwania (RTO).

Włącz Azure Backup i skonfiguruj Źródło kopii zapasowych (np. maszyny wirtualne platformy Azure, SQL Server, bazy danych HANA lub udziały plików), a także żądaną częstotliwość i okres przechowywania.  

W przypadku wyższego poziomu nadmiarowości można włączyć opcję magazynu geograficznie nadmiarowego, aby replikować dane kopii zapasowej do regionu pomocniczego i odzyskać przy użyciu operacji przywracania między regionami.

- [Jak włączyć Azure Backup](/azure/backup/)

- [Jak włączyć przywracanie między regionami](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Zasady i standardy](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: Szyfruj dane kopii zapasowej

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Upewnij się, że kopie zapasowe są chronione przed atakami. Powinno to obejmować szyfrowanie kopii zapasowych w celu ochrony przed utratą poufności.   

W przypadku lokalnego tworzenia kopii zapasowej przy użyciu Azure Backup szyfrowanie jest dostarczane przy użyciu podanego hasła. W przypadku zwykłej kopii zapasowej usługi Azure dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez platformę Azure. Możesz zaszyfrować kopię zapasową przy użyciu klucza zarządzanego przez klienta. W takim przypadku upewnij się, że ten klucz zarządzany przez klienta w magazynie kluczy należy również do zakresu kopii zapasowych. 

Użyj kontroli dostępu opartej na rolach w Azure Backup, Azure Key Vault lub innych zasobów, aby chronić kopie zapasowe i klucze zarządzane przez klienta. Dodatkowo można włączyć zaawansowane funkcje zabezpieczeń, aby wymagać uwierzytelniania wieloskładnikowego, zanim będzie można zmienić lub usunąć kopie zapasowe.

- [Omówienie funkcji zabezpieczeń w programie Azure Backup](/azure/backup/security-overview)

- [Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez klienta](/azure/backup/encryption-at-rest-with-cmk) 

- [Jak utworzyć kopię zapasową kluczy Key Vault na platformie Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Przygotowanie zdarzenia](/) Azure/Cloud-adopcj-Framework/Organizuj/Cloud-Security-incydent-preparat

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Weryfikuj wszystkie kopie zapasowe, w tym klucze zarządzane przez klienta

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Okresowe wykonywanie przywracania danych kopii zapasowej. Upewnij się, że można przywrócić kopie zapasowe kluczy zarządzanych przez klienta.

- [Jak odzyskać pliki z kopii zapasowej maszyny wirtualnej platformy Azure](/azure/backup/backup-azure-restore-files-from-vm)

- [Jak przywrócić klucze Key Vault na platformie Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: zmniejszanie ryzyka utraty kluczy

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| BR-4 | 10,4 | CP-9 |

Upewnij się, że masz środki, aby zapobiec utracie kluczy i je odzyskać. Włącz trwałe usuwanie i przeczyszczanie ochrony w Azure Key Vault, aby chronić klucze przed przypadkowym lub złośliwym usunięciem.  

- [Jak włączyć funkcję usuwania nietrwałego i przeczyszczania w programie Key Vault](/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Bezpieczeństwo danych](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

