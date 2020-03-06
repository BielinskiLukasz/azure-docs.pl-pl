---
title: Wymagania wstępne dotyczące Azure AD Connect aprowizacji w chmurze w usłudze Azure AD
description: W tym artykule opisano warunki wstępne i wymagania sprzętowe wymagane do aprowizacji chmury.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 382c588ca005f95f4ae38e7506c0e3e8d842bd2c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298653"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Wymagania wstępne dotyczące Azure AD Connect aprowizacji w chmurze
Ten artykuł zawiera wskazówki dotyczące sposobu wybierania i używania usługi Azure Active Directory (Azure AD) w celu nawiązania połączenia z chmurą jako rozwiązania do obsługi tożsamości.



## <a name="cloud-provisioning-agent-requirements"></a>Wymagania dotyczące agenta aprowizacji w chmurze
Aby skorzystać z Azure AD Connect aprowizacji w chmurze, potrzebne są następujące elementy:
    
- Konto administratora globalnego dla dzierżawy usługi Azure AD, który nie jest użytkownikiem-gościem.
- Serwer lokalny dla agenta aprowizacji z systemem Windows 2012 R2 lub nowszym.
- Lokalne konfiguracje zapory.

>[!NOTE]
>Agenta aprowizacji można obecnie zainstalować tylko na serwerach w języku angielskim. Zainstalowanie pakietu językowego w języku angielskim na serwerze innym niż angielski nie jest prawidłowym obejściem problemu i spowoduje niepowodzenie instalacji agenta. 

Pozostała część dokumentu zawiera instrukcje krok po kroku dotyczące tych wymagań wstępnych.

### <a name="in-the-azure-active-directory-admin-center"></a>W centrum administracyjnym Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób możesz zarządzać konfiguracją dzierżawy, jeśli usługi lokalne zakończą się niepowodzeniem lub staną się niedostępne. Dowiedz się [, jak dodać konto administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Zakończenie tego kroku ma na celu upewnienie się, że dzierżawa nie została zablokowana.
1. Dodaj co najmniej jedną [niestandardową nazwę domeny](../active-directory-domains-add-azure-portal.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się przy użyciu jednej z tych nazw domen.

### <a name="in-your-directory-in-active-directory"></a>W katalogu w Active Directory

Uruchom [Narzędzie IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) , aby przygotować atrybuty katalogu do synchronizacji.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Zidentyfikuj przyłączony do domeny serwer hosta z systemem Windows Server 2012 R2 lub nowszym z co najmniej 4 GB pamięci RAM i środowiskiem .NET 4.7.1 + Runtime.

1. Jeśli istnieje Zapora między serwerami i usługą Azure AD, skonfiguruj następujące elementy:
   - Upewnij się, że agenci mogą wykonywać żądania *wychodzące* do usługi Azure AD za pośrednictwem następujących portów:

        | Numer portu | Zastosowanie |
        | --- | --- |
        | **80** | Pobiera listy odwołania certyfikatów (CRL) podczas weryfikacji certyfikatu SSL.  |
        | **443** | Obsługuje całą komunikację wychodzącą z usługą. |
        | **8080** (opcjonalnie) | Agenci raportują swój stan co 10 minut przez port 8080, jeśli port 443 jest niedostępny. Ten stan jest wyświetlany w portalu usługi Azure AD. |
     
   - Jeśli Zapora wymusza reguły zależne od użytkowników inicjujących, należy otworzyć te porty dla ruchu z usług systemu Windows, które działają jako usługa sieciowa.
   - Jeśli zapora lub serwer proxy umożliwia określenie bezpiecznych sufiksów, Dodaj połączenia do \*. msappproxy.net i \*. servicebus.windows.net. W przeciwnym razie Zezwól na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień.
   - Twoje Agenci muszą mieć dostęp do login.windows.net i login.microsoftonline.com na potrzeby rejestracji wstępnej. Należy również otworzyć Zaporę dla tych adresów URL.
   - Aby sprawdzić poprawność certyfikatu, Odblokuj następujące adresy URL: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 i www\.microsoft.com:80. Te adresy URL są używane do sprawdzania poprawności certyfikatu z innymi produktami firmy Microsoft, dzięki czemu te adresy URL mogą już być odblokowane.

### <a name="verify-the-port"></a>Weryfikowanie portu
Aby sprawdzić, czy platforma Azure nasłuchuje na porcie 443 i czy Agent może się z nim komunikować, użyj następującego adresu URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ten test sprawdza, czy agenci mogą komunikować się z platformą Azure przez port 443. Otwórz przeglądarkę i przejdź do poprzedniego adresu URL z serwera, na którym zainstalowano agenta.

![Weryfikacja osiągalności portów](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Wymagania dodatkowe
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Wymagania protokołu TLS

>[!NOTE]
>Transport Layer Security (TLS) to protokół, który zapewnia bezpieczną komunikację. Zmiana ustawień protokołu TLS ma wpływ na cały las. Aby uzyskać więcej informacji, zobacz [Aktualizacja umożliwiająca włączenie protokołu tls 1,1 i tls 1,2 jako domyślnych protokołów Secure w usłudze WinHTTP w systemie Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

W systemie Windows Server, który jest hostem agenta aprowizacji Azure AD Connect w chmurze, należy włączyć protokół TLS 1,2 przed jego zainstalowaniem.

Aby włączyć protokół TLS 1,2, wykonaj następujące kroki.

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Uruchom ponownie serwer.


## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)

