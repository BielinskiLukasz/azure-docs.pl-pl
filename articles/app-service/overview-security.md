---
title: Przegląd zabezpieczeń — Azure App Service | Microsoft Docs
description: Dowiedz się więcej na temat sposobu, w jaki App Service pomaga w zabezpieczeniu aplikacji oraz jak można dodatkowo blokować aplikacje przed zagrożeniami.
keywords: Azure App Service, aplikacja sieci Web, aplikacja mobilna, aplikacja interfejsu API, aplikacja funkcji, zabezpieczenia, zabezpieczanie, zabezpieczanie, zgodność, zgodne, certyfikat, certyfikaty, https, FTPS, TLS, zaufanie, szyfrowanie, szyfrowanie, szyfrowanie, ograniczenie adresów IP, uwierzytelnianie, autoryzacja, authn, autho, MSI, tożsamość usługi zarządzanej, tożsamość zarządzana, wpisy tajne, sekret, poprawki, poprawki, poprawki, wersja, izolacja, izolacja sieci, DDoS, MITM
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b6f122abff1ac75bb1cb836f3389c96dfcdf60e0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074120"
---
# <a name="security-in-azure-app-service"></a>Zabezpieczenia w Azure App Service

W tym artykule opisano, jak [Azure App Service](overview.md) pomaga w zabezpieczeniu aplikacji sieci Web, zaplecza aplikacji mobilnych, aplikacji interfejsu API i [aplikacji funkcji](/azure/azure-functions/). Przedstawiono w nim również, jak można dodatkowo zabezpieczyć aplikację za pomocą wbudowanych funkcji App Service.

Składniki platformy App Service, w tym maszyny wirtualne platformy Azure, magazyn, połączenia sieciowe, platformy sieci Web, funkcje zarządzania i integracji, są aktywnie zabezpieczone i zaostrzone. App Service prowadzi przez silną kontrolę zgodności, aby upewnić się, że:

- Zasoby aplikacji są [zabezpieczone](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) z zasobów platformy Azure innych klientów.
- [Wystąpienia maszyn wirtualnych i oprogramowanie uruchomieniowe są regularnie aktualizowane](overview-patch-os-runtime.md) w celu rozwiązywania nowo odkrytych luk w zabezpieczeniach. 
- Komunikacja kluczy tajnych (takich jak parametry połączenia) między aplikacją i innymi zasobami platformy Azure (np. [SQL Database](https://azure.microsoft.com/services/sql-database/)) pozostaje w ramach platformy Azure i nie przekracza żadnych granic sieci. Wpisy tajne są zawsze szyfrowane, gdy są przechowywane.
- Cała komunikacja przez funkcje łączności App Service, na przykład [połączenie hybrydowe](app-service-hybrid-connections.md), jest zaszyfrowana. 
- Połączenia ze zdalnymi narzędziami do zarządzania, takimi jak Azure PowerShell, interfejs wiersza polecenia platformy Azure, zestawy SDK platformy Azure, interfejsy API REST, są szyfrowane.
- 24-godzinne zarządzanie zagrożeniami chroni infrastrukturę i platformę przed złośliwym oprogramowaniem, rozproszone odmowa usługi (DDoS), man-in-the-Middle (MITM) i inne zagrożenia.

Aby uzyskać więcej informacji na temat infrastruktury i zabezpieczeń platformy na platformie Azure, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/overview/trusted-cloud/).

W poniższych sekcjach pokazano, jak zabezpieczyć aplikację App Service przed zagrożeniami.

## <a name="https-and-certificates"></a>HTTPS i certyfikaty

App Service umożliwia Zabezpieczanie aplikacji przy użyciu [protokołu HTTPS](https://wikipedia.org/wiki/HTTPS). Po utworzeniu aplikacji jej domyślna nazwa domeny (\<APP_NAME >. azurewebsites. NET) jest już dostępna przy użyciu protokołu HTTPS. Jeśli [skonfigurujesz domenę niestandardową dla aplikacji](app-service-web-tutorial-custom-domain.md), należy ją również [zabezpieczyć za pomocą certyfikatu niestandardowego](app-service-web-tutorial-custom-ssl.md) , aby przeglądarki klienta mogły nawiązać bezpieczne połączenia HTTPS z domeną niestandardową. Istnieją dwa sposoby, aby to zrobić:

- **App Service certyfikat** — Tworzenie certyfikatu bezpośrednio na platformie Azure. Certyfikat jest zabezpieczony w [Azure Key Vault](/azure/key-vault/)i można go zaimportować do aplikacji App Service. Aby uzyskać więcej informacji, zobacz [kupowanie i Konfigurowanie certyfikatu SSL dla Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Certyfikat innej firmy** — Przekaż niestandardowy certyfikat SSL zakupionego od zaufanego urzędu certyfikacji i powiąż go z aplikacją App Service. App Service obsługuje zarówno certyfikaty z jedną domeną, jak i Certyfikaty wieloznaczne. Obsługuje również certyfikaty z podpisem własnym na potrzeby testowania. Aby uzyskać więcej informacji, zobacz [Powiązywanie istniejącego niestandardowego certyfikatu protokołu SSL z Azure App Service](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protokoły niezabezpieczone (HTTP, TLS 1,0, FTP)

Aby zabezpieczyć aplikację przed wszystkimi nieszyfrowanymi połączeniami (HTTP), App Service zapewnia konfigurację jednego kliknięcia, aby wymusić protokół HTTPS. Niezabezpieczone żądania są wyłączone, zanim będą nawet dotrzeć do kodu aplikacji. Aby uzyskać więcej informacji, zobacz wymuszanie [protokołu HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[Protokół TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1,0 nie jest już uznawany za bezpieczny przez standardy branżowe, takie jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service pozwala wyłączyć nieaktualne protokoły, wymuszając [protokół TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

App Service obsługuje zarówno protokół FTP, jak i FTPS do wdrażania plików. Należy jednak używać FTPS zamiast protokołu FTP, jeśli jest to możliwe. Jeśli jeden lub oba te protokoły nie są używane, należy [je wyłączyć](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Ograniczenia statycznych adresów IP

Domyślnie aplikacja App Service akceptuje żądania ze wszystkich adresów IP z Internetu, ale można ograniczyć ten dostęp do małego podzbioru adresów IP. App Service w systemie Windows umożliwia zdefiniowanie listy adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może obejmować pojedyncze adresy IP lub zakres adresów IP definiowanych przez maskę podsieci. Aby uzyskać więcej informacji, zobacz [Azure App Service ograniczeń statycznych adresów IP](app-service-ip-restrictions.md).

Aby uzyskać App Service w systemie Windows, można również dynamicznie ograniczyć adresy IP, konfigurując _plik Web. config_. Aby uzyskać więcej informacji, zobacz [dynamiczne IP \<Security dynamicIpSecurity >](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja klienta

Azure App Service zapewnia uwierzytelnianie klucza i autoryzację użytkowników lub aplikacji klienckich. Gdy ta funkcja jest włączona, może zalogować użytkowników i aplikacje klienckie z niewielkim kodem aplikacji lub bez niego. Możesz zaimplementować własne rozwiązanie uwierzytelniania i autoryzacji lub zezwolić na ich obsługę App Service zamiast tego. Moduł uwierzytelnianie i autoryzacja obsługuje żądania sieci Web przed przekazaniem ich do kodu aplikacji, a nie zezwala na nieautoryzowane żądania przed osiągnięciem kodu.

App Service uwierzytelnianie i autoryzacja obsługują wielu dostawców uwierzytelniania, w tym Azure Active Directory, konta Microsoft, Facebook, Google i Twitter. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami

Podczas uwierzytelniania w usłudze zaplecza App Service zapewnia dwa różne mechanizmy, w zależności od potrzeb:

- **Tożsamość usługi** — Zaloguj się do zasobu zdalnego przy użyciu tożsamości samej aplikacji. App Service pozwala łatwo utworzyć [tożsamość zarządzaną](overview-managed-identity.md), której można użyć do uwierzytelniania za pomocą innych usług, takich jak [Azure SQL Database](/azure/sql-database/) lub [Azure Key Vault](/azure/key-vault/). Aby uzyskać kompleksowy samouczek tego podejścia, zobacz [bezpieczny Azure SQL Database połączenie z App Service przy użyciu tożsamości zarządzanej](app-service-web-tutorial-connect-msi.md).
- **W imieniu (OBO)** — umożliwia delegowanie dostępu do zdalnych zasobów w imieniu użytkownika. Dzięki Azure Active Directory jako dostawca uwierzytelniania aplikacja App Service może przeprowadzić delegowane logowanie do usługi zdalnej, takiej jak [Azure Active Directory interfejs API programu Graph](../active-directory/develop/active-directory-graph-api.md) lub zdalna aplikacja interfejsu API w App Service. Aby zapoznać się z kompleksowym samouczkiem tego podejścia, zobacz temat [uwierzytelnianie i Autoryzowanie użytkowników na końcu w Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Łączność z zasobami zdalnymi

Istnieją trzy typy zasobów zdalnych, do których aplikacja może potrzebować dostępu: 

- [Zasoby platformy Azure](#azure-resources)
- [Zasoby wewnątrz Virtual Network platformy Azure](#resources-inside-an-azure-virtual-network)
- [Zasoby lokalne](#on-premises-resources)

W każdym z tych przypadków App Service zapewnia sposób nawiązywania bezpiecznych połączeń, ale nadal należy przestrzegać najlepszych rozwiązań w zakresie zabezpieczeń. Na przykład zawsze używaj szyfrowanych połączeń, nawet jeśli zasób zaplecza zezwala na nieszyfrowane połączenia. Ponadto upewnij się, że usługa platformy Azure zaplecza zezwala na minimalny zestaw adresów IP. Wychodzące adresy IP dla aplikacji można znaleźć w przychodzącym [i wychodzącym adresie IP w Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Zasoby platformy Azure

Gdy aplikacja nawiązuje połączenie z zasobami platformy Azure, takimi jak [SQL Database](https://azure.microsoft.com/services/sql-database/) i [Azure Storage](/azure/storage/), połączenie pozostaje na platformie Azure i nie przekracza żadnych granic sieci. Jednak połączenie odbywa się za pomocą sieci udostępnionej na platformie Azure, więc zawsze upewnij się, że połączenie jest zaszyfrowane. 

Jeśli aplikacja jest hostowana w [środowisku App Service](environment/intro.md), należy nawiązać [połączenie z obsługiwanymi usługami platformy Azure za pomocą Virtual Network punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Zasoby wewnątrz Virtual Network platformy Azure

Aplikacja może uzyskiwać dostęp do zasobów w usłudze [Azure Virtual Network](/azure/virtual-network/) za pomocą [integracji Virtual Network](web-sites-integrate-with-vnet.md). Integracja jest ustanawiana z Virtual Network przy użyciu sieci VPN typu punkt-lokacja. Następnie aplikacja może uzyskiwać dostęp do zasobów w Virtual Network przy użyciu ich prywatnych adresów IP. Połączenia punkt-lokacja, jednak nadal przechodzą przez sieci udostępnione na platformie Azure. 

Aby całkowicie wyizolować połączenie zasobów z udostępnionych sieci na platformie Azure, Utwórz aplikację w [środowisku App Service](environment/intro.md). Ponieważ środowisko App Service jest zawsze wdrażane w dedykowanej Virtual Network, łączność między aplikacją i zasobami w ramach Virtual Network jest w pełni izolowana. Aby poznać inne aspekty zabezpieczeń sieci w środowisku App Service, zobacz [izolacja sieci](#network-isolation).

### <a name="on-premises-resources"></a>Zasoby lokalne

Możesz bezpiecznie uzyskać dostęp do zasobów lokalnych, takich jak bazy danych, na trzy sposoby: 

- [Połączenia hybrydowe](app-service-hybrid-connections.md) — ustanawia połączenie punkt-punkt z zasobem zdalnym za pośrednictwem tunelu TCP. Tunel TCP jest ustanawiany przy użyciu protokołu TLS 1,2 z kluczami sygnatury dostępu współdzielonego (SAS).
- [Virtual Network integrację](web-sites-integrate-with-vnet.md) z siecią VPN typu lokacja-lokacja — zgodnie z opisem w obszarze [zasoby wewnątrz Virtual Network platformy Azure](#resources-inside-an-azure-virtual-network), ale Virtual Network można podłączyć do sieci lokalnej za pośrednictwem [sieci VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). W tej topologii sieci aplikacja może łączyć się z zasobami lokalnymi, takimi jak inne zasoby w Virtual Network.
- [Środowisko App Service](environment/intro.md) z siecią VPN typu lokacja-lokacja — zgodnie z opisem w obszarze [zasoby w ramach Virtual Network platformy Azure](#resources-inside-an-azure-virtual-network), ale Virtual Network może być podłączony do sieci lokalnej za pośrednictwem [sieci VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). W tej topologii sieci aplikacja może łączyć się z zasobami lokalnymi, takimi jak inne zasoby w Virtual Network.

## <a name="application-secrets"></a>Wpisy tajne aplikacji

Nie przechowuj wpisów tajnych aplikacji, takich jak poświadczenia bazy danych, tokeny interfejsu API i klucze prywatne w plikach kodu lub konfiguracji. Powszechnie akceptowanym podejściem jest uzyskanie dostępu do nich jako [zmiennych środowiskowych](https://wikipedia.org/wiki/Environment_variable) przy użyciu standardowego wzorca w wybranym języku. W App Service sposób definiowania zmiennych środowiskowych odbywa się za pomocą [ustawień aplikacji](configure-common.md#configure-app-settings) (i, szczególnie w przypadku aplikacji .NET, [parametrów połączenia](configure-common.md#configure-connection-strings)). Ustawienia aplikacji i parametry połączenia są przechowywane na platformie Azure i odszyfrowywane dopiero przed wprowadzeniem ich do pamięci procesu aplikacji podczas uruchamiania aplikacji. Klucze szyfrowania są regularnie obracane.

Alternatywnie możesz zintegrować swoją aplikację App Service z [Azure Key Vault](/azure/key-vault/) do zarządzania zaawansowanymi kluczami tajnymi. Dzięki [dostępowi do Key Vault za pomocą tożsamości zarządzanej](../key-vault/tutorial-web-application-keyvault.md)aplikacja App Service może bezpiecznie uzyskać dostęp do potrzebnych wpisów tajnych.

## <a name="network-isolation"></a>Izolacja sieci

Z wyjątkiem warstwy cenowej izolowanej wszystkie warstwy uruchamiają aplikacje w udostępnionej infrastrukturze sieci w App Service. Na przykład publiczne adresy IP i usługi równoważenia obciążenia frontonu są udostępniane innym dzierżawcom. Warstwa **izolowana** zapewnia pełną izolację sieci, uruchamiając aplikacje w dedykowanym [środowisku App Service](environment/intro.md). Środowisko App Service działa we własnym wystąpieniu [platformy Azure Virtual Network](/azure/virtual-network/). Umożliwia to: 

- Obsługuj swoje aplikacje za pomocą dedykowanego publicznego punktu końcowego z dedykowanymi frontonami.
- Obsłużyć wewnętrzną aplikację przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB), który umożliwia dostęp tylko z poziomu usługi Azure Virtual Network. ILB ma adres IP z podsieci prywatnej, który zapewnia całkowitą izolację aplikacji z Internetu.
- [Użyj ILB za zaporą aplikacji sieci Web (WAF)](environment/integrate-with-application-gateway.md). WAF oferuje ochronę na poziomie przedsiębiorstwa do aplikacji publicznych, takich jak ochrona DDoS, filtrowanie identyfikatorów URI i zapobieganie iniekcji SQL.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do środowisk Azure App Service](environment/intro.md). 
