---
title: Azure Disk Encryption w sieci izolowanej
description: Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z Microsoft Azure szyfrowaniem dysków dla maszyn wirtualnych z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: e91f4f1d1a16bec9d2da0365b185319aac8892be
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207721"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk Encryption w sieci izolowanej

Gdy łączność jest ograniczona przez zapory, wymagania serwera proxy lub ustawienia Sieciowej grupy zabezpieczeń sieci, może zostać przerwane możliwości rozszerzenia, aby wykonać niezbędne zadania. Ta przerw w działaniu może doprowadzić do komunikatów o stanie, takie jak "Nie jest dostępna na maszynie Wirtualnej stan rozszerzenia."

## <a name="package-management"></a>Zarządzanie pakietami

Azure Disk Encryption zależy od wielu składników, które zazwyczaj są instalowane w ramach włączania ADE, jeśli jeszcze nie istnieją. Te pakiety muszą być wstępnie zainstalowane lub dostępne lokalnie, gdy jest za zaporą lub w inny sposób odizolowany od Internetu.

Poniżej przedstawiono pakiety niezbędne do każdej dystrybucji. Aby zapoznać się z pełną listą obsługiwanych typów dystrybucje i woluminów, zobacz [obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14,04, 16,04, 18,04**: lsscsi, psmisc, at, cryptsetup-bin, Python-część, Python-sześć, procps
- **CentOS 7,2-7,7**: lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup, cryptsetup-reszyfrowania, pyparted, procps-ng, util-linux
- **CentOS 6,8**: lsscsi, psmisc, lvm2, UUID, at, cryptsetup-reszyfrowania, pyparted, Python — sześć
- **RedHat 7,2-7,7**: lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup, cryptsetup-reszyfrowania, procps-ng, util-linux
- **RedHat 6,8**: lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup-reszyfruj
- **openSUSE 42,3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

W systemie Red Hat Jeśli serwer proxy jest wymagane, należy się upewnić, że Menedżer subskrypcji i yum są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, zobacz [temat Rozwiązywanie problemów z usługą Subscription-Manager i yum](https://access.redhat.com/solutions/189533).  

Gdy pakiety są instalowane ręcznie, muszą być również ręcznie uaktualnione po wydaniu nowych wersji.

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Wszystkie ustawienia sieciowej grupy zabezpieczeń, które są stosowane, muszą nadal zezwalać, aby punkt końcowy spełniał udokumentowane wymagania wstępne konfiguracji sieci na potrzeby szyfrowania dysku.  Zobacz [Azure Disk Encryption: wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption z usługą Azure AD (poprzednia wersja)

W przypadku korzystania [z Azure Disk Encryption z usługą Azure AD (poprzednia wersja)](disk-encryption-overview-aad.md) [Biblioteka Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) będzie musiała zostać zainstalowana ręcznie dla wszystkich dystrybucje (oprócz pakietów odpowiednich dla dystrybucji, jak [wymieniono powyżej](#package-management)).

Gdy szyfrowanie jest włączane przy użyciu [poświadczeń usługi Azure AD](disk-encryption-linux-aad.md), docelowa maszyna wirtualna musi zezwalać na połączenie z punktami końcowymi Azure Active Directory i Key Vault punktów końcowych. Bieżące punkty końcowe uwierzytelniania Azure Active Directory są obsługiwane w sekcjach 56 i 59 w dokumentacji [pakietu Office 365 adresy URL i zakresy adresów IP](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) . Instrukcje Key Vault są dostępne w dokumentacji dotyczącej sposobu [uzyskiwania dostępu Azure Key Vault za zaporą](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Instance Metadata Service platformy Azure 

Maszyna wirtualna musi mieć dostęp do punktu końcowego [usługi metadanych wystąpienia platformy Azure](instance-metadata-service.md) , który używa dobrze znanego adresu IP bez obsługi routingu (`169.254.169.254`), do którego można uzyskać dostęp tylko z poziomu maszyny wirtualnej.  Konfiguracje serwera proxy, które modyfikują lokalny ruch HTTP na ten adres (na przykład dodanie nagłówka X-forwardd-for) nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki

- Zobacz więcej kroków [rozwiązywania problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
- [Szyfrowanie danych platformy Azure w spoczynku](../../security/fundamentals/encryption-atrest.md)
