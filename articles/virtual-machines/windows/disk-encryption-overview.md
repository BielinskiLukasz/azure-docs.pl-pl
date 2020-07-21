---
title: Włącz Azure Disk Encryption dla maszyn wirtualnych z systemem Windows
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1934470413ef1b0c757820e6539adcd43783acd2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500654"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption dla maszyn wirtualnych z systemem Windows 

Usługa Azure Disk Encryption pomaga chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. Korzysta ona z funkcji [BitLocker](https://en.wikipedia.org/wiki/BitLocker) systemu Windows w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych platformy Azure oraz jest zintegrowana z [Azure Key Vault](../../key-vault/index.yml) , aby pomóc w kontroli i zarządzaniu kluczami szyfrowania dysków i wpisami tajnymi. 

Jeśli używasz [Azure Security Center](../../security-center/index.yml), zostanie wyświetlony alert, jeśli masz maszyny wirtualne, które nie są zaszyfrowane. Alerty są wyświetlane jako o wysokiej ważności, a zalecenie polega na zaszyfrowaniu tych maszyn wirtualnych.

![Alert szyfrowania dysku Azure Security Center](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Jeśli usługa Azure AD została wcześniej Azure Disk Encryption użyta w celu zaszyfrowania maszyny wirtualnej, należy użyć tej opcji, aby zaszyfrować maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [Azure Disk Encryption w usłudze Azure AD (w poprzedniej wersji)](disk-encryption-overview-aad.md) . 
> - Niektóre zalecenia mogą zwiększyć użycie zasobów, sieci lub obliczeń, co skutkuje dodatkowymi kosztami licencji lub subskrypcji. Aby tworzyć zasoby na platformie Azure w obsługiwanych regionach, musisz mieć prawidłową aktywną subskrypcję platformy Azure.

Podstawowe informacje dotyczące Azure Disk Encryption dla systemu Windows można uzyskać w ciągu zaledwie kilku minut od [utworzenia i zaszyfrowania maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md) — szybki start lub [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu programu Azure PowerShell — szybki start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Obsługiwane maszyny wirtualne i systemy operacyjne

### <a name="supported-vms"></a>Obsługiwane maszyny wirtualne

Maszyny wirtualne z systemem Windows są dostępne w [różnych rozmiarach](../sizes-general.md). Azure Disk Encryption nie jest dostępna na [podstawowych maszynach wirtualnych serii A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)lub na maszynach wirtualnych z mniej niż 2 GB pamięci.

Azure Disk Encryption jest również dostępna dla maszyn wirtualnych z magazynem w warstwie Premium.

Azure Disk Encryption nie jest dostępna w przypadku [maszyn wirtualnych 2. generacji](generation-2.md#generation-1-vs-generation-2-capabilities) i [maszyn wirtualnych z serii Lsv2](../lsv2-series.md). Aby uzyskać więcej wyjątków, zobacz [Azure Disk Encryption: scenariusze nieobsługiwane](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Klient systemu Windows: system Windows 8 lub nowszy.
- Windows Server: system Windows Server 2008 R2 lub nowszy.  
 
> [!NOTE]
> System Windows Server 2008 R2 wymaga zainstalowania .NET Framework 4,5 do szyfrowania; Zainstaluj go z Windows Update z opcjonalną aktualizacją Microsoft .NET Framework 4.5.2 dla systemów Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Systemy Windows Server 2012 R2 Core i Windows Server 2016 Core wymagają zainstalowania składnika BdeHdCfg na maszynie wirtualnej w celu szyfrowania.


## <a name="networking-requirements"></a>Wymagania dotyczące sieci
Aby włączyć Azure Disk Encryption, maszyny wirtualne muszą spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:
  - Aby uzyskać token, aby połączyć się z magazynem kluczy, maszyna wirtualna z systemem Windows musi mieć możliwość nawiązania połączenia z punktem końcowym Azure Active Directory, \[ login.microsoftonline.com \] .
  - Aby można było napisać klucze szyfrowania do magazynu kluczy, maszyna wirtualna z systemem Windows musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna z systemem Windows musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Storage, który obsługuje repozytorium rozszerzeń platformy Azure i konto usługi Azure Storage, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozwiązać poprzedni identyfikator URI i skonfigurować określoną regułę, aby zezwolić na połączenia wychodzące z adresami IP. Aby uzyskać więcej informacji, zobacz [Azure Key Vault za zaporą](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Wymagania zasady grupy

Azure Disk Encryption używa funkcji ochrony klucza zewnętrznego funkcji BitLocker dla maszyn wirtualnych z systemem Windows. W przypadku maszyn wirtualnych przyłączonych do domeny nie wypychanie żadnych zasad grupy, które wymuszają funkcje ochrony modułu TPM. Aby uzyskać informacje o zasadach grupy dla "Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM", zobacz [BitLocker zasady grupy Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowymi zasadami grupy muszą zawierać następujące ustawienie: [Konfigurowanie magazynu użytkownika informacje odzyskiwania funkcji BitLocker — > Zezwalaj na 256-bitowy klucz odzyskiwania](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption zakończy się niepowodzeniem w przypadku niezgodności niestandardowych ustawień zasad grupy dla funkcji BitLocker. Na maszynach, które nie mają poprawnego ustawienia zasad, Zastosuj nowe zasady, Wymuś aktualizację nowych zasad (gpupdate.exe/Force), a następnie ponowne uruchomienie może być wymagane.

Azure Disk Encryption zakończy się niepowodzeniem, jeśli zasady grupy na poziomie domeny blokują algorytm AES-CBC, który jest używany przez funkcję BitLocker.

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące magazynu kluczy szyfrowania  

Azure Disk Encryption wymaga Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie i subskrypcji platformy Azure.

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
Poniższa tabela zawiera definicje typowych terminów używanych w dokumentacji usługi Azure Disk Encryption:

| Terminologia | Definicja |
| --- | --- |
| Azure Key Vault | Key Vault to kryptograficzna usługa zarządzania kluczami oparta na sprawdzonych modułach zabezpieczeń (FIPS) Te standardy pomagają chronić klucze kryptograficzne i poufne wpisy tajne. Aby uzyskać więcej informacji, zobacz dokumentację [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) i [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md). |
| Interfejs wiersza polecenia platformy Azure | [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest zoptymalizowany pod kątem zarządzania zasobami platformy Azure i administrowania nimi z wiersza poleceń.|
| Funkcja BitLocker |[Funkcja BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) jest rozpoznawaną w branży technologią szyfrowania woluminów systemu Windows, która służy do włączania szyfrowania dysków na maszynach wirtualnych z systemem Windows. |
| Klucz szyfrowania klucza (KEK) | Klucz asymetryczny (RSA 2048), którego można użyć do ochrony lub zawijania klucza tajnego. Można podać klucz chroniony przez sprzętowy moduł zabezpieczeń (HSM) lub klucz chroniony przez oprogramowanie. Aby uzyskać więcej informacji, zobacz dokumentację [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) i [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md). |
| Polecenia cmdlet programu PowerShell | Aby uzyskać więcej informacji, zobacz [polecenia cmdlet Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Następne kroki

- [Szybki Start — tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md)
- [Szybki Start — tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu programu Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows](disk-encryption-windows.md)
- [Skrypt interfejsu wiersza polecenia Azure Disk Encryption preinstalacji](https://github.com/ejarvi/ade-cli-getting-started)
- [Skrypt programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption](disk-encryption-key-vault.md)
