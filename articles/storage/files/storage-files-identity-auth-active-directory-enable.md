---
title: Omówienie uwierzytelniania AD DS lokalnych w udziałach plików platformy Azure
description: Informacje o uwierzytelnianiu Active Directory Domain Services (AD DS) w udziałach plików platformy Azure. W tym artykule znajdują się informacje o scenariuszach pomocy technicznej, dostępności i wyjaśniono, jak działają uprawnienia między AD DS i usługą Azure Active Directory.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/12/2020
ms.author: rogarana
ms.openlocfilehash: 0f2f0def9a23437dff57dda2d1a57950cef86275
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496132"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Omówienie lokalnego uwierzytelniania Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure

[Azure Files](storage-files-introduction.md)   Obsługa uwierzytelniania opartego na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) przez dwa typy usług domenowych: Azure Active Directory Domain Services (AD DS platformy Azure) i Active Directory Domain Services lokalnych (AD DS). Przy użyciu metody uwierzytelniania opartej na tożsamościach można zintegrować proces uzyskiwania dostępu do udziałów plików platformy Azure przy użyciu istniejącego procesu uwierzytelniania opartego na tożsamościach, a nie konieczności osobnego zarządzania nim. W tej serii artykułów koncentruje się na włączaniu i konfigurowaniu AD DS lokalnych na potrzeby uwierzytelniania przy użyciu udziałów plików platformy Azure.

Jeśli dopiero zaczynasz korzystanie z udziałów plików platformy Azure, zalecamy zapoznanie się z naszym [przewodnikiem planowania](storage-files-planning.md) przed przeczytaniem następującej serii artykułów.

## <a name="supported-scenarios-and-restrictions"></a>Obsługiwane scenariusze i ograniczenia

- AD DS tożsamości używanych do Azure Files lokalnego uwierzytelniania AD DS należy synchronizować z usługą Azure AD. Synchronizacja skrótów haseł jest opcjonalna. 
- Obsługuje udziały plików platformy Azure zarządzane przez Azure File Sync.
- Obsługuje uwierzytelnianie Kerberos za pomocą usługi AD z szyfrowaniem RC4-HMAC. Szyfrowanie AES Kerberos nie jest jeszcze obsługiwane.
- Obsługuje logowanie jednokrotne.
- Obsługiwane tylko na klientach z systemem operacyjnym w wersji nowszej niż Windows 7 lub Windows Server 2008 R2.
- Obsługiwane tylko w przypadku lasu usługi AD, w którym zarejestrowano konto magazynu. Można uzyskać dostęp tylko do udziałów plików platformy Azure z poświadczeniami AD DS z pojedynczego lasu. Jeśli potrzebujesz dostępu do udziału plików platformy Azure z innego lasu, upewnij się, że skonfigurowano odpowiednie zaufanie lasu, aby uzyskać szczegółowe informacje, zobacz [często zadawane pytania](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) .
- Program nie obsługuje uwierzytelniania dla kont komputerów utworzonych w AD DS. 

Po włączeniu AD DS dla udziałów plików platformy Azure za pośrednictwem protokołu SMB komputery przyłączone do AD DS mogą instalować udziały plików platformy Azure przy użyciu istniejących poświadczeń AD DS. Tę możliwość można włączyć za pomocą środowiska AD DS hostowanego na maszynach Premium lub hostowanych na platformie Azure.

> [!NOTE]
> Aby ułatwić konfigurację Azure Files uwierzytelniania usługi AD w przypadku niektórych typowych przypadków użycia, opublikowano dwa filmy wideo z szczegółowymi wskazówkami krok po kroku dla następujących scenariuszy:
> - [Zastępowanie lokalnych serwerów plików Azure Files (łącznie z konfiguracją prywatnego linku do plików i uwierzytelniania w usłudze AD)](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [Używanie Azure Files jako kontenera profilu dla pulpitu wirtualnego systemu Windows (w tym Instalatora uwierzytelniania usługi AD i konfiguracji FsLogix)](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>Wymagania wstępne 

Przed włączeniem AD DS uwierzytelniania dla udziałów plików platformy Azure upewnij się, że zostały spełnione następujące wymagania wstępne: 

- Wybierz lub Utwórz [środowisko AD DS](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) i [zsynchronizuj je z usługą Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) z Azure AD Connect. 

    Tę funkcję można włączyć w nowym lub istniejącym lokalnym środowisku AD DS. Tożsamości używane na potrzeby dostępu muszą być synchronizowane z usługą Azure AD. Dzierżawa usługi Azure AD i udział plików, do których uzyskujesz dostęp, muszą być skojarzone z tą samą subskrypcją.

- Przyłącz do domeny maszynę lokalną lub maszynę wirtualną platformy Azure do AD DS lokalnych. Aby uzyskać informacje o sposobie przyłączania do domeny, zapoznaj się z tematem aby [przyłączyć komputer do domeny](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

- Wybierz lub Utwórz konto usługi Azure Storage.  W celu uzyskania optymalnej wydajności zalecamy wdrożenie konta magazynu w tym samym regionie, w którym znajduje się klient, z którego planujesz uzyskać dostęp do udziału. Następnie [Zainstaluj udział plików platformy Azure](storage-how-to-use-files-windows.md) przy użyciu klucza konta magazynu. Zainstalowanie z kluczem konta magazynu weryfikuje łączność.

    Upewnij się, że konto magazynu zawierające Twoje udziały plików nie zostało już skonfigurowane do uwierzytelniania za pomocą usługi Azure AD DS. Jeśli na koncie magazynu jest włączone uwierzytelnianie Azure Files Azure AD DS, należy je wyłączyć przed zmianą lokalnego AD DS. Oznacza to, że istniejące listy ACL skonfigurowane w środowisku usługi Azure AD DS należy ponownie skonfigurować do prawidłowego wymuszania uprawnień.

    Jeśli występują problemy z nawiązywaniem połączenia z usługą Azure Files, zapoznaj się z [narzędziem rozwiązywania problemów opublikowanym pod kątem Azure Files instalowania błędów w systemie Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Udostępniamy również [wskazówki](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) ułatwiające obejście tego problemu, gdy port 445 jest zablokowany. 

- Przed włączeniem i skonfigurowaniem uwierzytelniania AD DS w udziałach plików platformy Azure wprowadź odpowiednią konfigurację sieci. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące sieci Azure Files](storage-files-networking-overview.md) .

## <a name="regional-availability"></a>Dostępność regionalna

Azure Files uwierzytelnianie z AD DS jest dostępne we [wszystkich regionach usług Azure Public i gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Omówienie

Jeśli planujesz włączyć każdą konfigurację sieci w udziale plików, zalecamy zapoznanie się z artykułem [zagadnienia dotyczące sieci](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) i zakończenie konfiguracji powiązanej przed włączeniem AD DS uwierzytelniania.

Włączenie uwierzytelniania AD DS dla udziałów plików platformy Azure umożliwia uwierzytelnianie w udziałach plików platformy Azure przy użyciu poświadczeń usługi Premium AD DS. Ponadto umożliwia lepsze zarządzanie uprawnieniami w celu umożliwienia szczegółowej kontroli dostępu. Wymaga to synchronizacji tożsamości z Premium AD DS do usługi Azure AD z usługą AD Connect. Użytkownik kontroluje dostęp do poziomu udostępniania przy użyciu tożsamości zsynchronizowanych z usługą Azure AD podczas zarządzania dostępem na poziomie plików/udostępniania przy użyciu poświadczeń Premium AD DS.

Następnie wykonaj poniższe kroki, aby skonfigurować Azure Files na potrzeby uwierzytelniania AD DS: 

1. [Część 1: Włączanie uwierzytelniania AD DS na koncie magazynu](storage-files-identity-ad-ds-enable.md)

1. [Część druga: przypisywanie uprawnień dostępu dla udziału do tożsamości usługi Azure AD (użytkownika, grupy lub nazwy głównej usługi), która jest zsynchronizowana z docelową tożsamością usługi AD](storage-files-identity-ad-ds-assign-permissions.md)

1. [Część trzecia: Konfigurowanie list ACL systemu Windows za pośrednictwem protokołu SMB dla katalogów i plików](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Część czwarta: Instalowanie udziału plików platformy Azure na maszynie wirtualnej przyłączonej do AD DS](storage-files-identity-ad-ds-mount-file-share.md)

1. [Zaktualizuj hasło tożsamości konta magazynu w AD DS](storage-files-identity-ad-ds-update-password.md)

Na poniższym diagramie przedstawiono kompleksowy przepływ pracy służący do włączania uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB dla udziałów plików platformy Azure. 

![Diagram przepływu pracy usługi AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Tożsamości używane do uzyskiwania dostępu do udziałów plików platformy Azure muszą zostać zsynchronizowane z usługą Azure AD w celu wymuszenia uprawnień do pliku na poziomie udziału za pośrednictwem modelu [kontroli dostępu opartego na rolach (RBAC)](../../role-based-access-control/overview.md) . [Listy DACL w stylu systemu Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) dotyczące plików/katalogów przeprowadzonych z istniejących serwerów plików zostaną zachowane i wymuszone. Zapewnia to bezproblemową integrację ze środowiskiem AD DS przedsiębiorstwa. Gdy zastąpisz Premium serwery plików z udziałami plików platformy Azure, istniejący użytkownicy mogą uzyskiwać dostęp do udziałów plików platformy Azure od ich bieżących klientów przy użyciu funkcji logowania jednokrotnego, bez wprowadzania żadnych zmian w poświadczeniach w użyciu.  

## <a name="next-steps"></a>Następne kroki

Aby włączyć lokalne uwierzytelnianie AD DS dla udziału plików platformy Azure, przejdź do następnego artykułu:

[Część 1: Włączanie AD DS uwierzytelniania dla konta](storage-files-identity-ad-ds-enable.md)
