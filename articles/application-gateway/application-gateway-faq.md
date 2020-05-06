---
title: Często zadawane pytania dotyczące usługi Azure Application Gateway
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 92011495f5f746b18a7706ed2f9583548cc51286
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836669"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Często zadawane pytania dotyczące Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniżej znajdują się często zadawane pytania dotyczące usługi Azure Application Gateway.

## <a name="general"></a>Ogólne

### <a name="what-is-application-gateway"></a>Co to jest usługa Application Gateway?

Usługa Azure Application Gateway udostępnia kontroler dostarczania aplikacji (ADC) jako usługę. Oferuje różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Ta usługa jest wysoce dostępna, skalowalna i w pełni zarządzana przez platformę Azure.

### <a name="what-features-does-application-gateway-support"></a>Jakie funkcje są Application Gateway obsługiwane?

Application Gateway obsługuje skalowanie automatyczne, odciążanie protokołu TLS i kompleksową transpozycję TLS, zaporę aplikacji sieci Web (WAF), koligację sesji na podstawie plików cookie, routing oparty na ścieżce URL, hosting w wielu lokacjach i inne funkcje. Aby uzyskać pełną listę obsługiwanych funkcji, zobacz [wprowadzenie do Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Jak różnią się Application Gateway i Azure Load Balancer?

Application Gateway jest modułem równoważenia obciążenia warstwy 7, co oznacza, że działa tylko z ruchem w sieci Web (HTTP, HTTPS, WebSocket i HTTP/2). Obsługuje ona funkcje, takie jak zakończenie protokołu TLS, koligacja sesji oparta na plikach cookie oraz działa Round Robin dla ruchu związanego z równoważeniem obciążenia. Load Balancer równoważenia obciążenia ruchem w warstwie 4 (TCP lub UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jakie protokoły Application Gateway obsługiwać?

Application Gateway obsługuje protokoły HTTP, HTTPS, HTTP/2 i WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Jak Application Gateway obsługuje protokołu HTTP/2?

Zobacz [Obsługa protokołu HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Jakie zasoby są obsługiwane w ramach puli zaplecza?

Zobacz [obsługiwane zasoby zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>W jakich regionach Application Gateway dostępne?

Application Gateway jest dostępna we wszystkich regionach globalnej platformy Azure. Jest ona również dostępna w [Chinach na platformie 21Vianet](https://www.azure.cn/) i [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Czy to wdrożenie jest przeznaczone dla mojej subskrypcji, czy też jest udostępniane przez klientów?

Application Gateway to dedykowane wdrożenie w sieci wirtualnej.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Czy Application Gateway obsługuje Przekierowywanie HTTP-to-HTTPS?

Przekierowanie jest obsługiwane. Zobacz [Omówienie przekierowania Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>W jakiej kolejności są przetwarzane odbiorniki?

Zobacz [kolejność przetwarzania odbiornika](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Gdzie mogę znaleźć Application Gateway IP i DNS?

Jeśli używasz publicznego adresu IP jako punktu końcowego, możesz znaleźć informacje dotyczące adresów IP i DNS w zasobie publicznego adresu IP. Lub znajdź je w portalu, na stronie Przegląd usługi Application Gateway. Jeśli używasz wewnętrznych adresów IP, Znajdź informacje na stronie Przegląd.

W przypadku jednostki SKU v2 Otwórz zasób publicznego adresu IP i wybierz pozycję **Konfiguracja**. Pole **etykieta nazwy DNS (opcjonalnie)** jest dostępne do skonfigurowania nazwy DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Jakie są ustawienia limitu czasu utrzymywania aktywności i limitu czasu bezczynności protokołu TCP?

*Limit czasu utrzymywania aktywności* określa, jak długo Application Gateway będzie czekać na wysłanie przez klienta kolejnego żądania HTTP przed ponownym użyciem lub zamknięciem. *Limit czasu bezczynności protokołu TCP* określa, jak długo połączenie TCP jest przechowywane w przypadku braku aktywności. 

*Limit czasu utrzymywania aktywności* w jednostce SKU Application Gateway v1 wynosi 120 sekund, a w jednostce SKU v2 jest 75 sekund. *Limit czasu bezczynności protokołu TCP* to domyślna wartość 4-minutowa w WIRTUALNYm adresie IP frontonu (VIP) zarówno w wersji 1, jak i w wersji 2 programu Application Gateway. Nie można zmienić tych wartości.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Czy nazwa adresu IP lub DNS jest zmieniana w okresie istnienia bramy aplikacji?

W przypadku jednostki SKU Application Gateway V1, adres VIP może ulec zmianie, jeśli zatrzymasz i uruchomisz bramę aplikacji. Jednak nazwa DNS skojarzona z bramą aplikacji nie zmienia się w okresie istnienia bramy. Ponieważ nazwa DNS nie zmienia się, należy użyć aliasu CNAME i wskazać adres DNS bramy aplikacji. W jednostkach SKU Application Gateway v2 można ustawić adres IP jako statyczny, aby nazwy IP i DNS nie zmieniły się w okresie istnienia bramy aplikacji. 

### <a name="does-application-gateway-support-static-ip"></a>Czy Application Gateway obsługiwać statyczny adres IP?

Tak, jednostka SKU Application Gateway v2 obsługuje statyczne publiczne adresy IP. Jednostka SKU V1 obsługuje statyczne wewnętrzne adresy IP.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Czy Application Gateway obsługiwać wiele publicznych adresów IP w bramie?

Brama aplikacji obsługuje tylko jeden publiczny adres IP.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak duże należy utworzyć podsieć dla Application Gateway?

Zobacz [zagadnienia dotyczące rozmiaru podsieci Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Czy można wdrożyć więcej niż jeden zasób Application Gateway w jednej podsieci?

Tak. Oprócz wielu wystąpień danego wdrożenia Application Gateway można zainicjować inny unikatowy zasób Application Gateway do istniejącej podsieci, która zawiera różne zasoby Application Gateway.

Pojedyncza podsieć nie może jednocześnie obsługiwać jednocześnie Standard_v2 i Application Gateway standardowych.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Czy Application Gateway v2 obsługuje trasy zdefiniowane przez użytkownika (UDR)?

Tak, ale tylko dla konkretnych scenariuszy. Aby uzyskać więcej informacji, zobacz [Omówienie konfiguracji Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Czy Application Gateway obsługuje nagłówki x-Forwarded-For?

Tak. Zobacz [modyfikacje żądania](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Jak długo trwa wdrażanie bramy aplikacji? Czy moja Brama aplikacji będzie działała w trakcie jej aktualizowania?

Nowe wdrożenia jednostki SKU Application Gateway V1 mogą potrwać do 20 minut. Zmiany rozmiaru lub liczby wystąpień nie zakłócają działania, a Brama pozostanie aktywna w tym czasie.

Większość wdrożeń, które używają jednostki SKU w wersji 2, zajmie około 6 minut. Może to jednak trwać dłużej w zależności od typu wdrożenia. Na przykład wdrożenia w wielu Strefy dostępności z wieloma wystąpieniami mogą trwać więcej niż 6 minut. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Czy mogę używać programu Exchange Server jako zaplecza z Application Gateway?

Nie. Application Gateway nie obsługuje protokołów poczty e-mail, takich jak SMTP, IMAP i POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Czy istnieją wskazówki dotyczące migracji z jednostki SKU w wersji 1 do wersji 2 SKU?

Tak. Aby uzyskać szczegółowe informacje, zobacz [Migrowanie usługi Azure Application Gateway i zapory aplikacji sieci Web z wersji 1 do wersji 2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Czy jednostka SKU Application Gateway V1 będzie nadal obsługiwana?

Tak. Jednostka SKU Application Gateway V1 będzie nadal obsługiwana. Jednak zdecydowanie zaleca się przejście do wersji 2 w celu skorzystania z aktualizacji funkcji w tej jednostce SKU. Aby uzyskać więcej informacji, zobacz Automatyczne [skalowanie i strefowo nadmiarowe Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Czy Application Gateway v2 obsługuje żądania proxy z uwierzytelnianiem NTLM?

Nie. Application Gateway v2 nie obsługuje jeszcze żądań proxy z uwierzytelnianiem NTLM.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Czy Application Gateway plik cookie koligacji jest obsługiwany?
Tak. [Aktualizacja V80](https://chromiumdash.appspot.com/schedule) [przeglądarki chromu](https://www.chromium.org/Home) wprowadziła upoważnienie dla plików cookie protokołu HTTP bez atrybutu SameSite, który ma być traktowany jako SameSite = swobodny. Oznacza to, że plik cookie koligacji Application Gateway nie będzie wysyłany przez przeglądarkę w kontekście innej firmy. 

Aby obsłużyć ten scenariusz, Application Gateway dodaje do istniejącego pliku cookie *ApplicationGatewayAffinity* inny plik cookie o nazwie *ApplicationGatewayAffinityCORS* .  Te pliki cookie są podobne, ale plik cookie *ApplicationGatewayAffinityCORS* ma dwa więcej atrybutów: *SameSite = none; Zabezpiecz*. Te atrybuty utrzymują sesje programu Sticky Notes nawet w przypadku żądań między źródłami. Aby uzyskać więcej informacji, zobacz [sekcję koligacja na podstawie plików cookie](configuration-overview.md#cookie-based-affinity) .

## <a name="performance"></a>Wydajność

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Jak Application Gateway zapewnia wysoką dostępność i skalowalność?

Jednostka SKU Application Gateway V1 obsługuje scenariusze wysokiej dostępności, gdy wdrożono dwa lub więcej wystąpień. Platforma Azure dystrybuuje te wystąpienia między domenami aktualizacji i błędów, aby upewnić się, że wystąpienia nie będą działać w tym samym czasie. Jednostka SKU V1 obsługuje skalowalność poprzez dodanie wielu wystąpień tej samej bramy w celu udostępnienia obciążenia.

Jednostka SKU v2 automatycznie zapewnia, że nowe wystąpienia są rozłożone w domenach błędów i domenach aktualizacji. W przypadku wybrania nadmiarowości strefy najnowsze wystąpienia są również rozproszone w różnych strefach dostępności, aby zaoferować odporność na awarie.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Jak mogę osiągnąć scenariusz odzyskiwania po awarii w centrach danych przy użyciu Application Gateway?

Użyj Traffic Manager do dystrybuowania ruchu między wieloma bramami aplikacji w różnych centrach danych.

### <a name="does-application-gateway-support-autoscaling"></a>Czy Application Gateway obsługuje skalowanie automatyczne?

Tak, jednostka SKU Application Gateway v2 obsługuje skalowanie automatyczne. Aby uzyskać więcej informacji, zobacz [Skalowanie automatyczne i strefowo nadmiarowe Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Czy ręczna lub automatyczna skalowanie w górę lub w dół powoduje przestoje?

Nie. Wystąpienia są dystrybuowane w domenach uaktualnień i domenach błędów.

### <a name="does-application-gateway-support-connection-draining"></a>Czy Application Gateway obsługuje opróżnianie połączenia?

Tak. Można skonfigurować opróżnianie połączenia w celu zmiany elementów członkowskich w puli zaplecza bez zakłóceń. Aby uzyskać więcej informacji, zobacz [sekcję opróżniania połączeń Application Gateway](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Czy mogę zmienić rozmiar wystąpienia z średniego na duży bez zakłóceń?

Tak.

## <a name="configuration"></a>Konfigurowanie

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Czy Application Gateway jest zawsze wdrożona w sieci wirtualnej?

Tak. Application Gateway jest zawsze wdrażana w podsieci sieci wirtualnej. Ta podsieć może zawierać tylko bramy aplikacji. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące sieci wirtualnej i podsieci](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Może Application Gateway komunikować się z wystąpieniami poza jego siecią wirtualną lub poza jego subskrypcją?

O ile masz połączenie IP, Application Gateway może komunikować się z wystąpieniami poza siecią wirtualną, w której się znajduje. Application Gateway może również komunikować się z wystąpieniami poza subskrypcją, w której się znajduje. Jeśli planujesz używać wewnętrznych adresów IP jako członków puli zaplecza, użyj [komunikacji równorzędnej sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md) lub [platformy Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Czy mogę wdrożyć dowolne inne elementy w podsieci bramy aplikacji?

Nie. Można jednak wdrożyć inne bramy aplikacji w podsieci.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Czy sieciowe grupy zabezpieczeń są obsługiwane w podsieci bramy aplikacji?

Zobacz [sieciowe grupy zabezpieczeń w podsieci Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Czy podsieć bramy Application Gateway obsługuje trasy zdefiniowane przez użytkownika?

Zobacz [trasy zdefiniowane przez użytkownika obsługiwane w podsieci Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jakie są limity Application Gateway? Czy mogę zwiększyć te limity?

Zobacz [limity Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Czy mogę jednocześnie używać Application Gateway zarówno dla ruchu zewnętrznego, jak i wewnętrznego?

Tak. Application Gateway obsługuje jeden wewnętrzny adres IP i jeden zewnętrzny adres IP na bramę aplikacji.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Czy Application Gateway obsługuje komunikację równorzędną sieci wirtualnej?

Tak. Komunikacja równorzędna sieci wirtualnych pomaga równoważyć obciążenie ruchu w innych sieciach wirtualnych.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Czy można komunikować się z serwerami lokalnymi, gdy są one połączone przez ExpressRoute lub tunele VPN?

Tak, o ile ruch jest dozwolony.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Czy jedna pula zaplecza może pracować wiele aplikacji na różnych portach?

Architektura mikrousług jest obsługiwana. Aby sondować różne porty, należy skonfigurować wiele ustawień protokołu HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Czy sondy niestandardowe obsługują symbole wieloznaczne i wyrażenia regularne dla danych odpowiedzi?

Nie. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Jak są przetwarzane reguły routingu w Application Gateway?

Zobacz [Kolejność reguł przetwarzania](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>W przypadku sond niestandardowych, co oznacza pole hosta?

Pole host Określa nazwę, do której ma zostać wysłana sonda po skonfigurowaniu wielu lokacjach na Application Gateway. W przeciwnym razie użyj "127.0.0.1". Ta wartość różni się od nazwy hosta maszyny wirtualnej. Jego format to \<protokół\>://\<hosta\>:\<ścieżka\>\<\>portu.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Czy mogę zezwolić Application Gateway na dostęp tylko do kilku źródłowych adresów IP?

Tak. Zobacz [ograniczanie dostępu do konkretnych źródłowych adresów IP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Czy można używać tego samego portu zarówno dla odbiorników publicznych, jak i prywatnych?

Nie.

### <a name="does-application-gateway-support-ipv6"></a>Czy Application Gateway obsługuje protokół IPv6?

Application Gateway v2 obecnie nie obsługuje protokołu IPv6. Może działać w sieci wirtualnej o podwójnej stercie tylko przy użyciu protokołu IPv4, ale podsieć bramy musi być tylko IPv4. Application Gateway V1 nie obsługuje podwójnego stosu sieci wirtualnych. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Jak mogę używać Application Gateway v2 tylko z prywatnym adresem IP frontonu?

Application Gateway v2 obecnie nie obsługuje trybu prywatnego adresu IP. Obsługuje następujące kombinacje
* Prywatny adres IP i publiczny adres IP
* Tylko publiczny adres IP

Jeśli jednak chcesz używać Application Gateway v2 tylko z prywatnym adresem IP, możesz wykonać poniższe czynności:
1. Utwórz Application Gateway z publicznym i prywatnym adresem IP frontonu
2. Nie należy tworzyć żadnych odbiorników dla publicznego adresu IP frontonu. Application Gateway nie nasłuchuje ruchu na publicznym adresie IP, jeśli dla niego nie zostały utworzone żadne odbiorniki.
3. Utwórz i Dołącz [grupę zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/security-overview) dla podsieci Application Gateway z następującą konfiguracją w kolejności priorytetu:
    
    a. Zezwalaj na ruch ze źródła jako tag usługi **gatewaymanager** i miejsce docelowe jako **dowolny** port docelowy AS **65200-65535**. Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure. Te porty są chronione (zablokowane) przez uwierzytelnianie przy użyciu certyfikatu. Jednostki zewnętrzne, w tym Administratorzy użytkowników bramy, nie mogą inicjować zmian w tych punktach końcowych bez odpowiednich certyfikatów
    
    b. Zezwalaj na ruch ze źródła jako tag usługi **AzureLoadBalancer** oraz port docelowy i docelowy jako **dowolne**
    
    c. Odrzuć cały ruch przychodzący ze źródła jako tag usługi **internetowej** oraz port docelowy i docelowy jako **dowolny**. Nadaj tej regule *minimalny priorytet* w regułach ruchu przychodzącego
    
    d. Zachowaj domyślne reguły, takie jak Zezwalanie na VirtualNetwork przychodzące, aby dostęp do prywatnego adresu IP nie był blokowany
    
    e. Nie można zablokować wychodzącej łączności z Internetem. W przeciwnym razie będziesz mieć problemy z rejestrowaniem, metrykami itp.

Przykładowa konfiguracja sieciowej grupy zabezpieczeń tylko dla prywatnego adresu IP ![: Application Gateway v2 sieciowej grupy zabezpieczeń Konfiguracja tylko dla prywatnego dostępu do adresu IP](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Konfiguracja — TLS

### <a name="what-certificates-does-application-gateway-support"></a>Jakie certyfikaty Application Gateway obsługiwać?

Application Gateway obsługuje certyfikaty z podpisem własnym, certyfikaty urzędu certyfikacji (CA), certyfikatów rozszerzonej weryfikacji (EV) i certyfikatów wieloznacznych.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Jakie mechanizmy szyfrowania Application Gateway obsługiwać?

Application Gateway obsługuje następujące mechanizmy szyfrowania. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Informacje o sposobach dostosowywania opcji protokołu TLS znajdują się [w temacie Konfigurowanie wersji zasad TLS i mechanizmów szyfrowania na Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Czy Application Gateway obsługuje reszyfrowanie ruchu do zaplecza?

Tak. Application Gateway obsługuje odciążanie protokołu TLS i kompleksową transpozycję TLS, która szyfruje ruch do zaplecza.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Czy można skonfigurować zasady protokołu TLS w celu kontrolowania wersji protokołu TLS?

Tak. Można skonfigurować Application Gateway, aby odmówić protokołów TLS 1.0, TLS 1.1 i TLS 1.2. Domyślnie protokoły SSL 2,0 i 3,0 są już wyłączone i nie można ich konfigurować.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Czy można skonfigurować mechanizmy szyfrowania i kolejność zasad?

Tak. W Application Gateway można [skonfigurować mechanizmy szyfrowania](application-gateway-ssl-policy-overview.md). Aby zdefiniować zasady niestandardowe, należy włączyć co najmniej jeden z następujących mechanizmów szyfrowania. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway używa programu SHA256 do zarządzania zapleczem.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Ile certyfikatów TLS/SSL obsługuje Application Gateway?

Application Gateway obsługuje do 100 certyfikatów TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Jak wiele certyfikatów uwierzytelniania do ponownego zaszyfrowania zaplecza Application Gateway obsługiwać?

Application Gateway obsługuje do 100 certyfikatów uwierzytelniania.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Czy Application Gateway natywnie integrowana z usługą Azure Key Vault?

Tak, jednostka SKU Application Gateway v2 obsługuje Key Vault. Aby uzyskać więcej informacji, zobacz temat [zakończenie protokołu TLS z certyfikatami Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Jak mogę skonfigurować odbiorniki HTTPS dla witryn com i .NET? 

W przypadku wielu routingu opartych na domenach (opartych na hostach) można utworzyć odbiorniki w wielu lokacjach, skonfigurować odbiorniki używające protokołu HTTPS jako protokołu i kojarzyć detektory z regułami routingu. Aby uzyskać więcej informacji, zobacz [hostowanie wielu witryn przy użyciu Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Czy mogę używać znaków specjalnych w haśle pliku PFX?

Nie, używaj tylko znaków alfanumerycznych w haśle pliku PFX.

## <a name="configuration---ingress-controller-for-aks"></a>Konfiguracja — kontroler ruchu przychodzącego dla AKS

### <a name="what-is-an-ingress-controller"></a>Co to jest kontroler transferu danych przychodzących?

Kubernetes umożliwia tworzenie `deployment` i `service` zasób w celu uwidocznienia grupy zasobów w klastrze. Aby udostępnić tę samą usługę zewnętrznie, jest [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) zdefiniowany zasób, który zapewnia Równoważenie obciążenia, zakończenie protokołu TLS i hosting wirtualny oparte na nazwach.
Aby spełnić ten `Ingress` zasób, wymagany jest kontroler transferu danych przychodzących, który nasłuchuje wszelkich zmian `Ingress` zasobów i konfiguruje zasady usługi równoważenia obciążenia.

Application Gateway kontroler transferu danych przychodzących umożliwia korzystanie z [platformy azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) jako ruchu przychodzącego dla [usługi Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) , znanej również jako klaster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Czy pojedyncze wystąpienie kontrolera ruchu przychodzącego może zarządzać wieloma bramami aplikacji?

Obecnie jedno wystąpienie kontrolera transferu danych przychodzących może być skojarzone tylko z jednym Application Gateway.

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Jakie typy dzienników zapewniają Application Gateway?

Application Gateway udostępnia trzy dzienniki: 

* **ApplicationGatewayAccessLog**: Dziennik dostępu zawiera wszystkie żądania przesłane do frontonu bramy aplikacji. Dane obejmują adres IP obiektu wywołującego, żądanie adresu URL, opóźnienie odpowiedzi, Kod powrotu i bajty. Zawiera jeden rekord na bramę aplikacji.
* **ApplicationGatewayPerformanceLog**: Dziennik wydajności przechwytuje informacje o wydajności dla każdej bramy aplikacji. Informacje obejmują przepływność w bajtach, Łączna Liczba obsłużonych żądań, liczbę żądań zakończonych niepowodzeniem oraz liczbę wystąpień zaplecza w dobrej kondycji
* **ApplicationGatewayFirewallLog**: w przypadku bram aplikacji skonfigurowanych za pomocą usługi WAF Dziennik zapory zawiera żądania, które są rejestrowane w trybie wykrywania lub w trybie zapobiegania.

Wszystkie dzienniki są zbierane co 60 sekund. Aby uzyskać więcej informacji, zobacz temat [kondycja zaplecza, dzienniki diagnostyczne i metryki dla Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Jak mogę wiedzieć, czy moje elementy członkowskie puli zaplecza są w dobrej kondycji?

Sprawdź kondycję przy użyciu polecenia cmdlet `Get-AzApplicationGatewayBackendHealth` programu PowerShell lub portalu. Aby uzyskać więcej informacji, zobacz [Application Gateway Diagnostics](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Jakie są zasady przechowywania dzienników diagnostycznych?

Dzienniki diagnostyczne przepływają do konta magazynu klienta. Klienci mogą ustawić zasady przechowywania na podstawie ich preferencji. Dzienniki diagnostyczne można także wysyłać do centrum zdarzeń lub dzienników Azure Monitor. Aby uzyskać więcej informacji, zobacz [Application Gateway Diagnostics](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Jak mogę pobrać dzienników inspekcji dla Application Gateway?

W portalu w bloku menu bramy aplikacji wybierz pozycję **Dziennik aktywności** , aby uzyskać dostęp do dziennika inspekcji. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Czy mogę ustawić alerty z Application Gateway?

Tak. W Application Gateway alerty są konfigurowane na metrykach. Aby uzyskać więcej informacji, zobacz [Application Gateway metryki](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) i [Odbieranie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Jak mogę analizować statystyki ruchu dla Application Gateway?

Dzienniki dostępu można wyświetlać i analizować na kilka sposobów. Użyj dzienników Azure Monitor, programu Excel, Power BI i tak dalej.

Można również użyć szablonu Menedżer zasobów, który służy do instalowania i uruchamiania popularnych analizatora dzienników [GoAccess](https://goaccess.io/) na potrzeby Application Gateway dzienników dostępu. GoAccess zapewnia cenne dane statystyczne dotyczące ruchu HTTP, takie jak unikatowych odwiedzających, żądanych plików, hostów, systemów operacyjnych, przeglądarek i kodów stanu HTTP. Aby uzyskać więcej informacji, zobacz [plik Readme w folderze szablonów Menedżer zasobów](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Co może spowodować, że kondycja zaplecza zwróci nieznany stan?

Zazwyczaj w przypadku zablokowania dostępu do zaplecza przez grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń), niestandardowy serwer DNS lub Routing zdefiniowany przez użytkownika (UDR) w podsieci bramy aplikacji jest wyświetlany nieznany stan. Aby uzyskać więcej informacji, zobacz temat [kondycja zaplecza, rejestrowanie diagnostyki i metryki dla Application Gateway](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Czy istnieją przypadki, w których dzienniki przepływu sieciowej grupy zabezpieczeń nie będą pokazywały dozwolonego ruchu?

Tak. Jeśli konfiguracja pasuje do poniższego scenariusza, w dziennikach przepływu sieciowej grupy zabezpieczeń nie będzie widoczny dozwolony ruch:
- Wdrożono Application Gateway v2
- Masz sieciowej grupy zabezpieczeń w podsieci bramy aplikacji
- Włączono dzienniki przepływu sieciowej grupy zabezpieczeń na tym sieciowej grupy zabezpieczeń

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Jak mogę używać Application Gateway v2 tylko z prywatnym adresem IP frontonu?

Application Gateway v2 obecnie nie obsługuje trybu prywatnego adresu IP. Obsługuje następujące kombinacje
* Prywatny adres IP i publiczny adres IP
* Tylko publiczny adres IP

Jeśli jednak chcesz używać Application Gateway v2 tylko z prywatnym adresem IP, możesz wykonać poniższe czynności:
1. Utwórz Application Gateway z publicznym i prywatnym adresem IP frontonu
2. Nie twórz żadnych odbiorników dla publicznego adresu IP frontonu. Application Gateway nie nasłuchuje ruchu na publicznym adresie IP, jeśli dla niego nie zostały utworzone żadne odbiorniki.
3. Utwórz i Dołącz [grupę zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/security-overview) dla podsieci Application Gateway z następującą konfiguracją w kolejności priorytetu:
    
    a. Zezwalaj na ruch ze źródła jako tag usługi **gatewaymanager** i miejsce docelowe jako **dowolny** port docelowy AS **65200-65535**. Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure. Te porty są chronione (zablokowane) przez uwierzytelnianie przy użyciu certyfikatu. Jednostki zewnętrzne, w tym Administratorzy użytkowników bramy, nie mogą inicjować zmian w tych punktach końcowych bez odpowiednich certyfikatów
    
    b. Zezwalaj na ruch ze źródła jako tag usługi **AzureLoadBalancer** i port docelowy jako **dowolne**
    
    c. Odrzuć cały ruch przychodzący ze źródła jako tag usługi **internetowej** i port docelowy jako **dowolny**. Nadaj tej regule *minimalny priorytet* w regułach ruchu przychodzącego
    
    d. Zachowaj domyślne reguły, takie jak Zezwalanie na VirtualNetwork przychodzące, aby dostęp do prywatnego adresu IP nie był zablokowany
    
    e. Nie można zablokować wychodzącej łączności z Internetem. W przeciwnym razie będziesz mieć problemy z rejestrowaniem, metrykami i tak dalej.

Przykładowa konfiguracja sieciowej grupy zabezpieczeń tylko dla prywatnego adresu IP ![: Application Gateway v2 sieciowej grupy zabezpieczeń Konfiguracja tylko dla prywatnego dostępu do adresu IP](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Czy Application Gateway plik cookie koligacji jest obsługiwany?
Tak. [Aktualizacja V80](https://chromiumdash.appspot.com/schedule) [przeglądarki chromu](https://www.chromium.org/Home) wprowadziła upoważnienie dla plików cookie protokołu HTTP bez atrybutu SameSite, który ma być traktowany jako SameSite = swobodny. Oznacza to, że plik cookie koligacji Application Gateway nie będzie wysyłany przez przeglądarkę w kontekście innej firmy. Aby obsłużyć ten scenariusz, Application Gateway dodaje do istniejącego pliku cookie *ApplicationGatewayAffinity* inny plik cookie o nazwie *ApplicationGatewayAffinityCORS* .  Te pliki cookie są podobne, ale plik cookie *ApplicationGatewayAffinityCORS* ma dwa więcej atrybutów: *SameSite = none; Zabezpiecz*. Te atrybuty utrzymują sesje programu Sticky Notes nawet w przypadku żądań między źródłami. Aby uzyskać więcej informacji, zobacz [sekcję koligacja na podstawie plików cookie](configuration-overview.md#cookie-based-affinity) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Application Gateway, zobacz [co to jest usługa Azure Application Gateway?](overview.md).
