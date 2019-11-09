---
title: Usługa Azure serwer proxy aplikacji usługi Azure AD często zadawane pytania | Microsoft Docs
description: Poznaj odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD w celu publikowania wewnętrznych aplikacji lokalnych dla użytkowników zdalnych.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 612b6caf47ec4764aa2bbef162592100198ed0c4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832208"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Serwer proxy aplikacji usługi Active Directory (Azure AD) — często zadawane pytania

Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące serwera proxy aplikacji Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Włączanie usługi Azure serwer proxy aplikacji usługi Azure AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Jaka licencja jest wymagana do korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD?

Aby korzystać z usługi Azure serwer proxy aplikacji usługi Azure AD, musisz mieć licencję na Azure AD — wersja Premium P1 lub P2. Aby uzyskać więcej informacji o licencjonowaniu, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Dlaczego przycisk "Włącz serwer proxy aplikacji jest wyszarzony?

Upewnij się, że masz co najmniej licencję na Azure AD — wersja Premium P1 lub P2 oraz zainstalowano łącznik usługi Azure serwer proxy aplikacji usługi Azure AD. Po pomyślnym zainstalowaniu pierwszego łącznika usługa Azure serwer proxy aplikacji usługi Azure AD zostanie włączona automatycznie.

## <a name="connector-configuration"></a>Konfiguracja łącznika

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Czy usługi łącznika serwera proxy aplikacji mogą działać w innym kontekście użytkownika niż domyślny?

Nie, ten scenariusz nie jest obsługiwany. Ustawienia domyślne to:

- Łącznik serwera proxy aplikacji usługi Microsoft AAD — WAPCSvc — usługa sieciowa
- Aktualizator łączników serwera proxy aplikacji usługi Microsoft AAD-WAPCUpdaterSvc-NT Nt\system

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Moja aplikacja zaplecza jest hostowana na wielu serwerach sieci Web i wymaga trwałości sesji użytkownika (lepkość). Jak można osiągnąć trwałość sesji? 

Zalecenia można znaleźć w temacie [wysoka dostępność i równoważenie obciążenia łączników i aplikacji serwera proxy aplikacji](application-proxy-high-availability-load-balancing.md).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Czy mogę umieścić urządzenie proxy przekazywania między serwerami łączników i serwerem aplikacji zaplecza?

Nie, ten scenariusz nie jest obsługiwany. Tylko łącznik i usługi aktualizacji można skonfigurować do korzystania z serwera proxy przesyłania dalej dla ruchu wychodzącego do platformy Azure. Zobacz temat [współpraca z istniejącymi lokalnymi serwerami proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-ssl-termination-sslhhtps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Czy w ruchu z serwerów łączników do platformy Azure jest obsługiwana przerwa w zakresie protokołu SSL (inspekcja SSL/HHTPS).

Łącznik serwera proxy aplikacji wykonuje uwierzytelnianie oparte na certyfikatach na platformie Azure. Zakończenie protokołu SSL (inspekcja lub przyspieszenie SSL/HHTPS) powoduje przerwanie tej metody uwierzytelniania i nie jest obsługiwana. Ruch z łącznika do platformy Azure musi pomijać wszystkie urządzenia, które wykonują zakończenie protokołu SSL.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Czy należy utworzyć dedykowane konto w celu zarejestrowania łącznika przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD?

Nie ma powodu. Zostaną wykonane wszystkie konta administratora globalnego lub administratora aplikacji. Poświadczenia wprowadzone podczas instalacji nie są używane po zakończeniu procesu rejestracji. Zamiast tego certyfikat jest wystawiony dla łącznika, który jest używany do uwierzytelniania z tego punktu w systemie.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Jak mogę monitorować wydajność łącznika usługi Azure serwer proxy aplikacji usługi Azure AD?

Istnieją liczniki Monitora wydajności, które są zainstalowane wraz z łącznikiem. Aby je wyświetlić:  

1. Wybierz pozycję **Start**, wpisz "perfmon" i naciśnij klawisz ENTER.
2. Wybierz pozycję **Monitor wydajności** i kliknij ikonę **+** zielony.
3. Dodaj liczniki **łącznika serwera proxy aplikacji usługi Microsoft AAD** , które chcesz monitorować.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Czy łącznik usługi Azure serwer proxy aplikacji usługi Azure AD musi znajdować się w tej samej podsieci co zasób?

Łącznik nie musi znajdować się w tej samej podsieci. Wymaga to jednak rozpoznawania nazw (DNS, hostowania pliku) do zasobu i niezbędnej łączności sieciowej (routingu do zasobu, portów otwartych w danym zasobie itp.). Aby uzyskać zalecenia, zobacz [zagadnienia dotyczące topologii sieci podczas korzystania z serwer proxy aplikacji usługi Azure Active Directory](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Jaka jest długość domyślnego i "długiego" limitu czasu zaplecza? Czy limit czasu można rozszerzyć?

Domyślna długość to 85 sekund. Ustawienie "Long" to 180 sekund. Nie można rozszerzyć limitu czasu.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Jak mogę zmienić strony docelowej załadowanej przez aplikację?

Na stronie rejestracje aplikacji można zmienić adres URL strony głównej na żądany zewnętrzny adres URL strony docelowej. Określona strona zostanie załadowana, gdy aplikacja zostanie uruchomiona z poziomu moich aplikacji lub portalu Office 365. Aby uzyskać instrukcje dotyczące konfiguracji, zobacz [Ustawianie niestandardowej strony głównej dla opublikowanych aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Czy można publikować tylko aplikacje oparte na usługach IIS? Co z aplikacjami sieci Web działającymi na serwerach z systemem innym niż Windows? Czy łącznik musi być zainstalowany na serwerze z zainstalowanymi usługami IIS?

Nie, nie ma wymagań dotyczących usług IIS dla opublikowanych aplikacji. Możesz publikować aplikacje sieci Web działające na serwerach innych niż system Windows Server. Nie można jednak używać uwierzytelniania wstępnego z serwerem innym niż Windows, w zależności od tego, czy serwer sieci Web obsługuje negocjowanie (uwierzytelnianie Kerberos). Program IIS nie jest wymagany na serwerze, na którym jest zainstalowany łącznik.

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Kiedy należy używać metody PrincipalsAllowedToDelegateToAccount podczas konfigurowania ograniczonego delegowania protokołu Kerberos (KCD)?

Metoda PrincipalsAllowedToDelegateToAccount jest używana, gdy serwery łączników znajdują się w innej domenie z konta usługi aplikacji sieci Web. Wymaga użycia ograniczonego delegowania opartego na zasobach.
Jeśli serwery łączników i konta usługi aplikacji sieci Web znajdują się w tej samej domenie, można użyć Active Directory Użytkownicy i komputery, aby skonfigurować ustawienia delegowania na poszczególnych kontach komputerów łączników, umożliwiając im delegowanie do docelowej nazwy SPN.

Jeśli serwery łączników i konta usługi aplikacji sieci Web znajdują się w różnych domenach, używane jest delegowanie oparte na zasobach. Uprawnienia delegowania są konfigurowane na docelowym serwerze sieci Web i na koncie usługi aplikacji sieci Web. Ta metoda delegowania ograniczonego jest stosunkowo nowa. Metoda została wprowadzona w systemie Windows Server 2012, która obsługuje delegowanie między domenami przez umożliwienie właścicielowi zasobu (usługi sieci Web) kontroli nad tym, którym maszynom i kontom usług mogą delegować. Nie ma interfejsu użytkownika umożliwiającego korzystanie z tej konfiguracji, dlatego należy użyć programu PowerShell.
Aby uzyskać więcej informacji, zobacz oficjalny dokument dotyczący [ograniczonego delegowania protokołu Kerberos z serwerem proxy aplikacji](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Czy można używać zasad dostępu warunkowego dla aplikacji publikowanych z uwierzytelnianiem przekazywanym?

Zasady dostępu warunkowego są wymuszane tylko dla pomyślnie wstępnie uwierzytelnionych użytkowników w usłudze Azure AD. Uwierzytelnianie przekazywane nie wyzwala uwierzytelniania usługi Azure AD, więc nie można wymusić zasad dostępu warunkowego. W przypadku uwierzytelniania przekazywanego zasady MFA muszą być zaimplementowane na serwerze lokalnym, jeśli jest to możliwe, lub przez włączenie wstępnego uwierzytelniania przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Czy mogę opublikować aplikację sieci Web z wymaganiem uwierzytelniania certyfikatu klienta?

Nie, ten scenariusz nie jest obsługiwany, ponieważ serwer proxy aplikacji zakończy ruch związany z protokołem TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publikowanie bramy Pulpit zdalny

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Jak publikować Pulpit zdalny Gateway za pośrednictwem usługi Azure serwer proxy aplikacji usługi Azure AD?

Zapoznaj się z tematem [publikowanie pulpit zdalny za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-in-the-remote-desktop-gateway-publishing-scenario"></a>Czy można użyć ograniczonego delegowania protokołu Kerberos w scenariuszu publikowania Pulpit zdalny bramy?

Nie, ten scenariusz nie jest obsługiwany.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Moi użytkownicy nie korzystają z programu Internet Explorer 11, a scenariusz przed uwierzytelnianiem nie działa. Czy jest to oczekiwane?

Tak, oczekiwano. Scenariusz przed uwierzytelnianiem wymaga formantu ActiveX, który nie jest obsługiwany w przeglądarkach innych firm.

### <a name="is-the-remote-desktop-web-client-supported"></a>Czy klient sieci Web Pulpit zdalny jest obsługiwany?

Nie, ten scenariusz nie jest obecnie obsługiwany. Aby uzyskać aktualizacje dotyczące tej funkcji, postępuj zgodnie z naszym forum opinii usługi [UserVoice](https://aka.ms/aadapuservoice) .

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Po skonfigurowaniu scenariusza wstępnego uwierzytelniania użytkownik stwierdził, że musi uwierzytelnić się dwa razy: najpierw w formularzu logowania do usługi Azure AD, a następnie w formularzu logowania RDWeb. Czy jest to oczekiwane? Jak można je zmniejszyć do jednego logowania?

Tak, oczekiwano. Jeśli komputer użytkownika jest przyłączony do usługi Azure AD, użytkownik loguje się automatycznie do usługi Azure AD. Użytkownik musi podać swoje poświadczenia tylko na formularzu logowania RDWeb.

## <a name="sharepoint-publishing"></a>Publikowanie programu SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Jak mogę opublikować program SharePoint za pośrednictwem usługi Azure serwer proxy aplikacji usługi Azure AD?

Zapoznaj się z tematem [Włączanie dostępu zdalnego do programu SharePoint przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md).

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publikowanie Active Directory Federation Services (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Czy mogę użyć usługi Azure serwer proxy aplikacji usługi Azure AD jako serwera proxy AD FS (na przykład serwera proxy aplikacji sieci Web)?

Nie. Usługa Azure serwer proxy aplikacji usługi Azure AD została zaprojektowana do pracy z usługą Azure AD i nie spełnia wymagań związanych z działaniem jako AD FS serwerze proxy.

## <a name="websocket"></a>Akceptowan

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Czy obsługa protokołu WebSocket działa w przypadku aplikacji innych niż QlikSense?

Obecnie obsługa protokołu WebSocket jest nadal w publicznej wersji zapoznawczej i może nie współpracować z innymi aplikacjami. Niektórzy klienci mają mieszany sukces przy użyciu protokołu WebSocket z innymi aplikacjami. Jeśli testujesz takie scenariusze, chcielibyśmy poznać Twoje wyniki. Prześlij nam swoją opinię na aadapfeedback@microsoft.com.

## <a name="link-translation"></a>Połącz tłumaczenie

### <a name="does-using-link-translation-affect-performance"></a>Czy używanie tłumaczenia linku ma wpływ na wydajność?

Tak. Konsolidacja łącza ma wpływ na wydajność. Usługa serwera proxy aplikacji skanuje aplikację pod kątem linków stałe i zastępuje je odpowiednimi, opublikowanymi zewnętrznymi adresami URL przed przedpisaniem ich użytkownikowi. 

Aby uzyskać najlepszą wydajność, zalecamy użycie identycznych wewnętrznych i zewnętrznych adresów URL przez skonfigurowanie [domen niestandardowych](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Jeśli nie jest możliwe korzystanie z domen niestandardowych, można poprawić wydajność tłumaczenia łącza za pomocą rozszerzenia moje aplikacje bezpieczne logowanie do konta lub przeglądarki Microsoft Edge na urządzeniu przenośnym. Zobacz [przekierowanie stałe linki dla aplikacji opublikowanych przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Symbole wieloznaczne

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Jak mogę użyć symboli wieloznacznych, aby opublikować dwie aplikacje z taką samą niestandardową nazwą domeny, ale z różnymi protokołami, jeden dla protokołu HTTP i jeden dla protokołu HTTPS?

Ten scenariusz nie jest obsługiwany bezpośrednio. W tym scenariuszu są dostępne następujące opcje:

1. Opublikuj adresy URL protokołu HTTP i HTTPS jako oddzielne aplikacje za pomocą symbolu wieloznacznego, ale nadaj każdej z nich inną domenę niestandardową. Ta konfiguracja będzie działała, ponieważ mają różne zewnętrzne adresy URL.

2. Opublikuj adres URL HTTPS za pomocą aplikacji wieloznacznej. Publikuj osobno aplikacje HTTP przy użyciu tych poleceń cmdlet programu PowerShell dla serwera proxy aplikacji:
   - [Zarządzanie aplikacjami serwera proxy aplikacji](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Zarządzanie łącznikiem serwera proxy aplikacji](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
