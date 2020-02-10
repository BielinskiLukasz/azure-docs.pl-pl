---
title: Włączanie dostępu zdalnego do Power BI przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD
description: Obejmuje podstawowe informacje na temat sposobu integrowania Power BI lokalnego z usługą Azure serwer proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111573"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Włączanie dostępu zdalnego do Power BI Mobile przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD w celu włączenia Power BI aplikacji mobilnej do programu Serwer raportów usługi Power BI (PBIRS) i SQL Server Reporting Services (SSRS) 2016 i nowszych. Dzięki tej integracji użytkownicy, którzy znajdują się poza siecią firmową, mogą uzyskiwać dostęp do raportów Power BI z aplikacji mobilnej Power BI i być chroniona za pomocą uwierzytelniania usługi Azure AD. Ta ochrona obejmuje [korzyści z zabezpieczeń](application-proxy-security.md#security-benefits) , takie jak dostęp warunkowy i uwierzytelnianie wieloskładnikowe.  

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że już wdrożono usługi raportów i [włączono serwer proxy aplikacji](application-proxy-add-on-premises-application.md).

- Włączenie serwera proxy aplikacji wymaga zainstalowania łącznika w systemie Windows Server i wypełnienia jego [wymagań wstępnych](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) , aby łącznik mógł komunikować się z usługami Azure AD.  
- W przypadku publikowania Power BI zalecamy używanie tych samych wewnętrznych i zewnętrznych domen. Aby dowiedzieć się więcej o domenach niestandardowych, zobacz [Praca z domenami niestandardowymi w serwerze proxy aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Ta integracja jest dostępna dla aplikacji **Power BI Mobile dla systemów iOS i Android** .

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1. Konfigurowanie ograniczonego delegowania protokołu Kerberos (KCD)

Aplikacje lokalne, które korzystają z uwierzytelniania Windows można osiągnąć logowania jednokrotnego (SSO) przy użyciu protokołu uwierzytelniania Kerberos i funkcję ograniczonego delegowania protokołu Kerberos (KCD). Po skonfigurowaniu KCD umożliwia łącznikowi serwera proxy aplikacji uzyskanie tokenu systemu Windows dla użytkownika, nawet jeśli użytkownik nie zalogował się bezpośrednio do systemu Windows. Aby dowiedzieć się więcej o KCD, zobacz [Omówienie delegowania ograniczonego protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx) i [ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego do aplikacji przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

Nie ma dużo do skonfigurowania po stronie usług Reporting Services. Upewnij się, że masz prawidłową główną nazwę usługi (SPN), aby umożliwić prawidłowe uwierzytelnianie Kerberos. Upewnij się również, że serwer usług Reporting Services obsługuje uwierzytelnianie negocjowane.

Aby skonfigurować program KCD dla usług Reporting Services, wykonaj następujące czynności.

### <a name="configure-the-service-principal-name-spn"></a>Konfigurowanie głównej nazwy usługi (SPN)

Nazwa SPN jest unikatowym identyfikatorem dla usługi korzystającej z uwierzytelniania Kerberos. Musisz upewnić się, że masz prawidłową nazwę SPN protokołu HTTP obecną dla serwera raportów. Aby uzyskać informacje dotyczące sposobu konfigurowania właściwej głównej nazwy usługi (SPN) dla serwera raportów, zobacz [Rejestrowanie głównej nazwy usługi (SPN) dla serwera raportów](https://msdn.microsoft.com/library/cc281382.aspx).
Aby sprawdzić, czy nazwa SPN została dodana, można uruchomić polecenie Setspn z opcją-L. Aby dowiedzieć się więcej na temat tego polecenia, zobacz [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Włącz uwierzytelnianie negocjowane

Aby umożliwić serwerowi raportów używanie uwierzytelniania Kerberos, należy skonfigurować typ uwierzytelniania serwera raportów jako RSWindowsNegotiate. Skonfiguruj to ustawienie przy użyciu pliku RSReportServer. config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Aby uzyskać więcej informacji, zobacz [Modyfikowanie pliku konfiguracji usług Reporting Services](https://msdn.microsoft.com/library/bb630448.aspx) i [Konfigurowanie uwierzytelniania systemu Windows na serwerze raportów](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Upewnij się, że łącznik jest zaufany do delegowania do nazwy SPN dodanej do konta puli aplikacji usług Reporting Services
Skonfiguruj KCD tak, aby usługa Azure serwer proxy aplikacji usługi Azure AD mogła delegować tożsamości użytkowników do konta puli aplikacji usług Reporting Services. Konfigurowanie ograniczonego delegowania protokołu Kerberos, włączając łącznik serwera Proxy aplikacji pobrać bilety protokołu Kerberos dla użytkowników, którzy zostali uwierzytelnieni w usłudze Azure AD. Następnie serwer przekazuje kontekst do aplikacji docelowej lub usług Reporting Services w tym przypadku.

Aby skonfigurować KCD, powtórz następujące kroki dla każdej maszyny łącznika:

1. Zaloguj się do kontrolera domeny jako administrator domeny, a następnie otwórz **Active Directory Użytkownicy i komputery**.
2. Znajdź komputera, na którym jest zasilany z łącznika.  
3. Kliknij dwukrotnie komputer, a następnie wybierz kartę **delegowanie** .
4. Ustaw ustawienia delegowania, aby **ufać temu komputerowi w delegowaniu tylko do określonych usług**. Następnie wybierz opcję **Użyj dowolnego protokołu uwierzytelniania**.
5. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Użytkownicy lub komputery**.
6. Wprowadź konto usługi używane dla usług Reporting Services. Jest to konto, do którego dodano nazwę SPN w ramach konfiguracji usług Reporting Services.
7. Kliknij przycisk **OK**. Aby zapisać zmiany, kliknij przycisk **OK** ponownie.

Aby uzyskać więcej informacji, zobacz [ograniczone delegowanie protokołu Kerberos na potrzeby logowania jednokrotnego do aplikacji przy użyciu serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Krok 2. Publikowanie usług raportów za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD

Teraz możesz przystąpić do konfigurowania serwer proxy aplikacji usługi Azure AD platformy Azure.

1. Publikowanie usług raportów za pomocą serwera proxy aplikacji z następującymi ustawieniami. Aby uzyskać instrukcje krok po kroku dotyczące publikowania aplikacji za pomocą serwera proxy aplikacji, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Wewnętrzny adres URL**: wprowadź adres URL serwera raportów, który łącznik może dotrzeć do sieci firmowej. Upewnij się, że ten adres URL jest dostępny z serwera, na którym jest zainstalowany łącznik. Najlepszym rozwiązaniem jest użycie domeny najwyższego poziomu, takiej jak `https://servername/`, aby uniknąć problemów z podścieżkami opublikowanymi za pośrednictwem serwera proxy aplikacji. Na przykład użyj `https://servername/`, a nie `https://servername/reports/` lub `https://servername/reportserver/`.
     > [!NOTE]
     > Zalecamy używanie bezpiecznego połączenia HTTPS z serwerem raportów. Aby uzyskać informacje na temat sposobu, zobacz [Konfigurowanie połączeń SSL na serwerze raportów w trybie macierzystym](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) .
   - **Zewnętrzny adres URL**: wprowadź publiczny adres URL, z którym zostanie nawiązane połączenie Power BI aplikacji mobilnej. Na przykład może wyglądać tak jak `https://reports.contoso.com`, jeśli jest używana domena niestandardowa. Aby użyć domeny niestandardowej, Przekaż certyfikat dla domeny i wskaż rekord DNS jako domyślną domenę msappproxy.net dla aplikacji. Aby uzyskać szczegółowe instrukcje, zobacz [Praca z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

   - **Metoda wstępnego uwierzytelniania**: Azure Active Directory

2. Po opublikowaniu aplikacji pojedynczego ustawień logowania jednokrotnego należy skonfigurować następujące czynności:

   a. Na stronie aplikacji w portalu wybierz pozycję **Logowanie jednokrotne**.

   b. W przypadku **trybu logowania**jednokrotnego wybierz pozycję **zintegrowane uwierzytelnianie systemu Windows**.

   c. Ustaw **nazwę SPN aplikacji wewnętrznej** na ustawioną wcześniej wartość.  

   d. Wybierz **delegowaną tożsamość logowania** dla łącznika, który ma być używany w imieniu użytkowników. Aby uzyskać więcej informacji, zobacz [Praca z różnymi tożsamościami lokalnymi i w chmurze](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Kliknij przycisk **Zapisz** , aby zapisać zmiany.

Aby zakończyć konfigurowanie aplikacji, przejdź do sekcji **Użytkownicy i grupy** i przypisz użytkownikom dostęp do tej aplikacji.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Krok 3. Modyfikowanie identyfikatora URI odpowiedzi dla aplikacji

Aby aplikacja mobilna Power BI mogła łączyć się z usługami raportów i uzyskiwać do nich dostęp, musisz skonfigurować rejestrację aplikacji, która została automatycznie utworzona w kroku 2. 

1. Na stronie **przegląd** Azure Active Directory wybierz pozycję **rejestracje aplikacji**.
2. Na karcie **wszystkie aplikacje** Wyszukaj aplikację utworzoną w kroku 2.
3. Wybierz aplikację, a następnie wybierz pozycję **uwierzytelnianie**.
4. Dodaj następujące identyfikatory URI przekierowania na podstawie używanej platformy.

   Podczas konfigurowania aplikacji dla Power BI Mobile **iOS**Dodaj następujące identyfikatory URI przekierowania typu klient publiczny (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Podczas konfigurowania aplikacji dla Power BI Mobile **Android**Dodaj następujące identyfikatory URI przekierowania typu klient publiczny (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Aby aplikacja działała poprawnie, należy dodać identyfikatory URI przekierowania. W przypadku konfigurowania aplikacji do Power BI Mobile iOS i Android Dodaj następujący identyfikator URI przekierowania typu klient publiczny (Mobile & Desktop) do listy identyfikatorów URI przekierowania skonfigurowanych dla systemu iOS: `urn:ietf:wg:oauth:2.0:oob`.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Krok 4. nawiązanie połączenia z aplikacją mobilną Power BI

1. W aplikacji mobilnej Power BI Nawiąż połączenie z wystąpieniem usług Reporting Services. W tym celu wprowadź **zewnętrzny adres URL** aplikacji opublikowanej za pomocą serwera proxy aplikacji.

   ![Power BI aplikacji mobilnej z zewnętrznym adresem URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Wybierz przycisk **Połącz**. Nastąpi przekierowanie do Azure Active Directory stronie logowania.

3. Wprowadź prawidłowe poświadczenia dla użytkownika i wybierz pozycję **Zaloguj się**. Zobaczysz elementy z serwera usług Reporting Services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Krok 5. Konfigurowanie zasad usługi Intune dla urządzeń zarządzanych (opcjonalnie)

Za pomocą Microsoft Intune można zarządzać aplikacjami klienckimi używanymi przez pracowników firmy. Usługa Intune umożliwia korzystanie z takich funkcji, jak szyfrowanie danych i dodatkowe wymagania dotyczące dostępu. Aby dowiedzieć się więcej o zarządzaniu aplikacjami za poorednictwem usługi Intune, zobacz Zarządzanie aplikacjami w usłudze Intune. Aby umożliwić aplikacji mobilnej Power BI współpracują z zasadami usługi Intune, wykonaj następujące czynności.

1. Przejdź do **Azure Active Directory** , a następnie pozycję **rejestracje aplikacji**.
2. Wybierz aplikację skonfigurowaną w kroku 3 podczas rejestrowania natywnej aplikacji klienckiej.
3. Na stronie aplikacji wybierz pozycję **uprawnienia interfejsu API**.
4. Kliknij przycisk **Dodaj uprawnienie**. 
5. W obszarze interfejsy API, które są **wykorzystywane przez moją organizację**, wyszukaj frazę "Zarządzanie aplikacjami mobilnymi firmy Microsoft" i wybierz ją.
6. Dodaj uprawnienie **DeviceManagementManagedApps. ReadWrite** do aplikacji
7. Kliknij pozycję **Udziel zgody administratora** , aby udzielić uprawnienia dostępu do aplikacji.
8. Skonfiguruj odpowiednie zasady usługi Intune, odwołując się do [sposobu tworzenia i przypisywania zasad ochrony aplikacji](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli aplikacja zwróci stronę błędu po próbie załadowania raportu przez więcej niż kilka minut, może zajść potrzeba zmiany ustawienia limitu czasu. Domyślnie serwer proxy aplikacji obsługuje aplikacje, które w odpowiedzi na żądanie zajmują do 85 sekund. Aby wydłużyć to ustawienie na 180 sekund, wybierz limit czasu zaplecza na **stronie** ustawienia serwera proxy aplikacji dla aplikacji. Porady dotyczące tworzenia szybkich i niezawodnych raportów można znaleźć w temacie [Power BI raporty najlepsze rozwiązania](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Następne kroki

- [Włącz natywne aplikacje klienckie do współpracy z aplikacjami proxy](application-proxy-configure-native-client-application.md)
- [Wyświetlanie raportów i kluczowych wskaźników wydajności lokalnych serwerów raportów w Power BI aplikacjach mobilnych](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
