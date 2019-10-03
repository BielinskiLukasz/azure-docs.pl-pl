---
title: Włączanie Azure Disk Encryption dla maszyn wirtualnych z systemem Linux
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 14e82bc5f58383c37bfd82d793851fc7e927b2f4
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828440"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption dla maszyn wirtualnych z systemem Linux 

Azure Disk Encryption pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Używa funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych platformy Azure, a ponadto jest zintegrowane z [Azure Key Vault](../../key-vault/index.yml) , aby pomóc w kontroli i zarządzaniu kluczami szyfrowania dysków i wpisami tajnymi. 

Jeśli używasz [Azure Security Center](../../security-center/index.yml), zostanie wyświetlony alert, jeśli masz maszyny wirtualne, które nie są zaszyfrowane. Alerty są wyświetlane jako o wysokiej ważności, a zalecenie polega na zaszyfrowaniu tych maszyn wirtualnych.

![Alert szyfrowania dysku Azure Security Center](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Jeśli usługa Azure AD została wcześniej Azure Disk Encryption użyta w celu zaszyfrowania maszyny wirtualnej, należy użyć tej opcji, aby zaszyfrować maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [Azure Disk Encryption w usłudze Azure AD (w poprzedniej wersji)](disk-encryption-overview-aad.md) . 
> - Niektóre zalecenia mogą zwiększyć użycie zasobów, sieci lub obliczeń, co skutkuje dodatkowymi kosztami licencji lub subskrypcji. Aby tworzyć zasoby na platformie Azure w obsługiwanych regionach, musisz mieć prawidłową aktywną subskrypcję platformy Azure.

Podstawowe informacje o Azure Disk Encryption dla systemu Linux można uzyskać w ciągu kilku minut od [utworzenia i zaszyfrowania maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md) z przewodnikiem Szybki start lub [tworzenia i szyfrowania maszyny wirtualnej z systemem Linux przy użyciu programu Azure PowerShell — szybki start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Obsługiwane maszyny wirtualne i systemy operacyjne

### <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

Maszyny wirtualne z systemem Linux są dostępne w [różnych rozmiarach](sizes.md). Azure Disk Encryption nie jest dostępna na [podstawowych maszynach wirtualnych serii A](/pricing/details/virtual-machines/series/)lub na maszynach wirtualnych, które nie spełniają minimalnych wymagań dotyczących pamięci:

| Maszyna wirtualna | Minimalne wymagania dotyczące pamięci |
|--|--|
| Maszyny wirtualne z systemem Linux, w których są szyfrowane tylko woluminy danych| 2 GB |
| Maszyny wirtualne z systemem Linux podczas szyfrowania woluminów danych i systemu operacyjnego oraz miejsce użycia systemu plików głównego (/) jest 4 GB lub mniej | 8 GB |
| Maszyny wirtualne z systemem Linux podczas szyfrowania woluminów danych i systemu operacyjnego oraz miejsce użycia systemu plików głównego (/) o wartości większej niż 4 GB | Użycie głównego systemu plików * 2. Na przykład użycie 16 GB z głównego systemu plików wymaga co najmniej 32 GB pamięci RAM |

Po zakończeniu procesu szyfrowania dysku systemu operacyjnego na maszynach wirtualnych z systemem Linux można skonfigurować maszynę wirtualną do uruchamiania z mniejszą ilością pamięci. 

Azure Disk Encryption jest również dostępna dla maszyn wirtualnych z magazynem w warstwie Premium. 

### <a name="supported-operating-systems"></a>Supported operating systems

Azure Disk Encryption jest obsługiwane w podzestawie [dystrybucji systemu Linux z zatwierdzona przez platformę Azure](endorsed-distros.md), które jest samym podzbiorem wszystkich możliwych dystrybucji serwerów z systemem Linux.

![Diagram Venna dystrybucji serwerów z systemem Linux, który obsługuje Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Dystrybucje serwera z systemem Linux, które nie są zatwierdzone przez platformę Azure, nie obsługują Azure Disk Encryption; z tych, które są zatwierdzone, obsługiwane są tylko następujące dystrybucje i wersje Azure Disk Encryption:

| Dystrybucja systemu Linux | Wersja | Typ woluminu obsługiwany na potrzeby szyfrowania|
| --- | --- |--- |
| Ubuntu | 18,04| System operacyjny i dysk z danymi |
| Ubuntu | 16,04| System operacyjny i dysk z danymi |
| Ubuntu | 14.04.5</br>[Dzięki dostrojeniu jądra platformy Azure do wersji 4,15 lub nowszej](disk-encryption-troubleshooting.md) | System operacyjny i dysk z danymi |
| RHEL | 7,6 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7,5 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7,4 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7,3 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7,2 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 6,8 | Dysk danych (patrz Uwaga poniżej) |
| RHEL | 6,7 | Dysk danych (patrz Uwaga poniżej) |
| CentOS | 7,6 | System operacyjny i dysk z danymi |
| CentOS | 7,5 | System operacyjny i dysk z danymi |
| CentOS | 7,4 | System operacyjny i dysk z danymi |
| CentOS | 7,3 | System operacyjny i dysk z danymi |
| CentOS | 7.2 n | System operacyjny i dysk z danymi |
| CentOS | 6,8 | Dysk danych |
| openSUSE | 42,3 | Dysk danych |
| SLES | 12 — SP4 | Dysk danych |
| SLES | 12 — SP3 | Dysk danych |

> [!NOTE]
> Nowa implementacja elementu ADE jest obsługiwana w przypadku systemu operacyjnego RHEL i dysku danych dla obrazów z opcją płatność zgodnie z rzeczywistym użyciem. ADE nie jest obecnie obsługiwane w przypadku obrazów RHEL z własnymi subskrypcjami (BYOS). 

## <a name="additional-vm-requirements"></a>Dodatkowe wymagania dotyczące maszyn wirtualnych

Azure Disk Encryption wymaga obecności modułów dm-crypt i vfat w systemie. Usunięcie lub wyłączenie vfat z obrazu domyślnego uniemożliwi systemowi odczytywanie woluminu klucza i Uzyskiwanie klucza wymaganego do odblokowania dysków podczas kolejnych ponownych uruchomień. Kroki ograniczania systemu, które usuwają moduł vfat z systemu, nie są zgodne z Azure Disk Encryption. 

Przed włączeniem szyfrowania, dyski danych, które mają być szyfrowane, muszą być poprawnie wymienione w katalogu/etc/fstab. Użyj trwałej nazwy urządzenia blokowego dla tego wpisu, ponieważ nazwy urządzeń w formacie "/dev/sdX" nie mogą być używane do skojarzenia z tym samym dyskiem w ramach ponownych uruchomień, szczególnie po zastosowaniu szyfrowania. Aby uzyskać więcej szczegółów dotyczących tego zachowania, zobacz: [Rozwiązywanie problemów z nazwami urządzeń maszyny wirtualnej z systemem Linux zmiany nazwy](troubleshoot-device-names-problems.md)

Upewnij się, że ustawienia/etc/fstab są prawidłowo skonfigurowane do zainstalowania. Aby skonfigurować te ustawienia, uruchom polecenie instalacji-a lub Uruchom ponownie maszynę wirtualną i Wyzwól w ten sposób ponowne zainstalowanie. Po zakończeniu Sprawdź dane wyjściowe polecenia lsblk, aby sprawdzić, czy dysk jest nadal zainstalowany. 
- Jeśli plik/etc/fstab nie instaluje dysku prawidłowo przed włączeniem szyfrowania, Azure Disk Encryption nie będzie w stanie zainstalować go prawidłowo.
- Proces Azure Disk Encryption spowoduje przeniesienie informacji o instalacji z/etc/fstab i do własnego pliku konfiguracji w ramach procesu szyfrowania. Nie można sprawdzić, czy nie ma wpisu w/etc/fstab po zakończeniu szyfrowania dysku danych.
- Przed rozpoczęciem szyfrowania należy zatrzymać wszystkie usługi i procesy, które mogą być zapisywane na zainstalowanych dyskach danych i je wyłączyć, aby nie uruchamiały się automatycznie po ponownym uruchomieniu komputera. Może to spowodować, że pliki będą otwierane na tych partycjach, co uniemożliwia procedurę szyfrowania w celu ich ponownego zainstalowania, powodując niepowodzenie szyfrowania. 
- Po ponownym uruchomieniu proces Azure Disk Encryption będzie miał czas na zainstalowanie nowo zaszyfrowanych dysków. Po ponownym uruchomieniu nie będą one natychmiast dostępne. Proces wymaga czasu uruchomienia, odblokowania, a następnie zainstalowania szyfrowanych dysków przed udostępnieniem ich innym procesom. Ten proces może potrwać kilka minut po ponownym uruchomieniu w zależności od charakterystyki systemu.

Przykładem poleceń, których można użyć do zainstalowania dysków danych i utworzenia niezbędnych wpisów/etc/fstab, można znaleźć w [skrypcie interfejsu wiersza polecenia Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) (wiersze 244-248) i programie [PowerShell wymagania wstępne Azure Disk Encryption skrypt](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Aby włączyć funkcję Azure Disk Encryption, maszyny wirtualne z systemem Linux muszą spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:
  - Aby uzyskać token, aby połączyć się z magazynem kluczy, maszyna wirtualna z systemem Linux musi mieć możliwość nawiązania połączenia z punktem końcowym Azure Active Directory, @no__t -0login. microsoftonline. com @ no__t-1.
  - Aby można było napisać klucze szyfrowania do magazynu kluczy, maszyna wirtualna z systemem Linux musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna z systemem Linux musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Storage, który obsługuje repozytorium rozszerzeń platformy Azure i konto usługi Azure Storage, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozwiązać poprzedni identyfikator URI i skonfigurować określoną regułę, aby zezwolić na połączenia wychodzące z adresami IP. Aby uzyskać więcej informacji, zobacz [Azure Key Vault za zaporą](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące magazynu kluczy szyfrowania  

Azure Disk Encryption wymaga Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie i subskrypcji platformy Azure.

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
Poniższa tabela zawiera definicje typowych terminów używanych w dokumentacji usługi Azure Disk Encryption:

| Terminologia | Definicja |
| --- | --- |
| Azure Key Vault | Key Vault to kryptograficzna usługa zarządzania kluczami oparta na sprawdzonych modułach zabezpieczeń (FIPS) Te standardy pomagają chronić klucze kryptograficzne i poufne wpisy tajne. Aby uzyskać więcej informacji, zobacz dokumentację [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) i [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md). |
| Interfejs wiersza polecenia platformy Azure | [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest zoptymalizowany pod kątem zarządzania zasobami platformy Azure i administrowania nimi z wiersza poleceń.|
| DM — Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) to oparty na systemie Linux, przezroczysty podsystem szyfrowania dysków używany do włączania szyfrowania dysków na maszynach wirtualnych z systemem Linux. |
| Klucz szyfrowania klucza (KEK) | Klucz asymetryczny (RSA 2048), którego można użyć do ochrony lub zawijania klucza tajnego. Można podać klucz chroniony przez sprzętowy moduł zabezpieczeń (HSM) lub klucz chroniony przez oprogramowanie. Aby uzyskać więcej informacji, zobacz dokumentację [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) i [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md). |
| Polecenia cmdlet programu PowerShell | Aby uzyskać więcej informacji, zobacz [polecenia cmdlet Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Następne kroki

- [Szybki Start — tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md)
- [Szybki Start — tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu programu Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Scenariusze Azure Disk Encryption na maszynach wirtualnych z systemem Linux](disk-encryption-linux.md)
- [Skrypt interfejsu wiersza polecenia Azure Disk Encryption preinstalacji](https://github.com/ejarvi/ade-cli-getting-started)
- [Skrypt programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md)


