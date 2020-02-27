---
title: Warunki w zasadach dostępu warunkowego — Azure Active Directory
description: Co to są warunki w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: dff80d849268c770e4227ff8c99b8f4d133c4d78
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620730"
---
# <a name="conditional-access-conditions"></a>Dostęp warunkowy: warunki

W ramach zasad dostępu warunkowego administrator może używać sygnałów z warunków, takich jak ryzyko, platforma urządzenia lub lokalizacja, aby usprawnić decyzje dotyczące zasad. 

![Definiowanie zasad dostępu warunkowego i określanie warunków](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Można połączyć wiele warunków, aby utworzyć szczegółowe i konkretne zasady dostępu warunkowego.

Na przykład podczas uzyskiwania dostępu do poufnej aplikacji administrator może w pełni kontrolować informacje o ryzyku związanym z logowaniem z usługi Identity Protection, a także do innych kontrolek, takich jak uwierzytelnianie wieloskładnikowe.

## <a name="sign-in-risk"></a>Ryzyko związane z logowaniem

W przypadku klientów mających dostęp do usługi [Identity Protection](../identity-protection/overview-identity-protection.md)można ocenić ryzyko związane z logowaniem w ramach zasad dostępu warunkowego. Ryzyko związane z logowaniem reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie jest autoryzowane przez właściciela tożsamości. Więcej informacji na temat ryzyka związanego z logowaniem można znaleźć w artykułach, [co jest ryzykowne](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) i [jak: Konfigurowanie i Włączanie zasad ryzyka](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Platformy urządzeń

Platforma urządzenia jest scharakteryzowana przez system operacyjny, który działa na urządzeniu. Usługa Azure AD identyfikuje platformę przy użyciu informacji dostarczonych przez urządzenie, takich jak ciągi agenta użytkownika. Ponieważ ciągi agentów użytkownika mogą być modyfikowane, te informacje nie są weryfikowane. Platforma urządzeń powinna być używana w połączeniu z Microsoft Intune zasadami zgodności urządzeń lub jako część instrukcji blokowej. Wartość domyślna ma zastosowanie do wszystkich platform urządzeń.

Dostęp warunkowy usługi Azure AD obsługuje następujące platformy urządzeń:

- Android
- iOS
- Windows Phone
- System Windows
- macOS

W przypadku zablokowania starszego uwierzytelniania przy użyciu warunku **other clients** można również ustawić warunek platformy urządzenia.

## <a name="locations"></a>Lokalizacje

Podczas konfigurowania lokalizacji jako warunku organizacje mogą zdecydować się na dołączenie lub wykluczenie lokalizacji. Te nazwane lokalizacje mogą zawierać publiczne informacje o sieci IPv4, kraj lub region, a nawet nieznane obszary, które nie są mapowane na określone kraje lub regiony. Tylko zakresy adresów IP można oznaczyć jako zaufaną lokalizację.

W przypadku dołączenia **dowolnej lokalizacji**ta opcja obejmuje dowolny adres IP w Internecie, który nie jest właśnie skonfigurowany nazwanymi lokalizacjami. W przypadku wybrania **dowolnej lokalizacji**Administratorzy mogą zdecydować się na wykluczenie **wszystkich zaufanych** lub **wybranych lokalizacji**.

Na przykład niektóre organizacje mogą zrezygnować z uwierzytelniania wieloskładnikowego w przypadku, gdy użytkownicy są połączeni z siecią w zaufanej lokalizacji, takiej jak ich fizyczna siedziba. Administratorzy mogą utworzyć zasadę, która obejmuje dowolną lokalizację, ale wyklucza wybrane lokalizacje dla swoich sieci centralnych.

Więcej informacji na temat lokalizacji można znaleźć w artykule [co to jest warunek lokalizacji w Azure Active Directory dostęp warunkowy](location-condition.md).

## <a name="client-apps-preview"></a>Aplikacje klienckie (wersja zapoznawcza)

Zasady dostępu warunkowego są domyślnie stosowane do aplikacji i aplikacji opartych na przeglądarce, które korzystają z nowoczesnych protokołów uwierzytelniania. Oprócz tych aplikacji Administratorzy mogą dołączać klientów programu Exchange ActiveSync i innych klientów korzystających ze starszych protokołów.

- Przeglądarka
   - Obejmują one aplikacje oparte na sieci Web, które używają protokołów takich jak SAML, WS-Federation, OpenID Connect Connect lub usługi zarejestrowane jako klient z poufnym uwierzytelnianiem OAuth.
- Aplikacje mobilne i klienci stacjonarni
   - Nowoczesne komputery klienckie uwierzytelniania
      - Ta opcja obejmuje aplikacje, takie jak aplikacje dla komputerów stacjonarnych i telefonów biurowych.
   - Klienci programu Exchange ActiveSync
      - Domyślnie obejmuje to wszystkie zastosowania protokołu Exchange ActiveSync (EAS). Wybór opcji **Zastosuj zasady tylko do obsługiwanych platform** będzie ograniczać się do obsługiwanych platform, takich jak iOS, Android i Windows.
      - Gdy zasady blokują korzystanie z programu Exchange ActiveSync, dany użytkownik otrzyma pojedynczą wiadomość e-mail z kwarantanny. Ta wiadomość e-mail z informacjami o tym, dlaczego są blokowane i w razie możliwości zawiera instrukcje dotyczące korygowania.
   - Inni klienci
      - Ta opcja obejmuje klientów korzystających z protokołów uwierzytelniania Basic/starszej, które nie obsługują nowoczesnego uwierzytelniania.
         - Uwierzytelniony protokół SMTP używany przez klienta POP i IMAP do wysyłania wiadomości e-mail.
         - Wykrywanie automatyczne — używane przez klientów programu Outlook i EAS do znajdowania skrzynek pocztowych w usłudze Exchange Online i łączenia się z nimi.
         - Exchange Online PowerShell — służy do łączenia się z usługą Exchange Online przy użyciu zdalnego programu PowerShell. Jeśli zablokujesz uwierzytelnianie podstawowe dla programu Exchange Online PowerShell, musisz użyć modułu programu PowerShell w usłudze Exchange Online, aby nawiązać połączenie. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z programem Exchange Online PowerShell przy użyciu uwierzytelniania wieloskładnikowego](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Usługi sieci Web programu Exchange (EWS) — interfejs programowania używany przez program Outlook, program Outlook dla komputerów Mac i aplikacje innych firm.
         - IMAP4 — używany przez klientów poczty e-mail IMAP.
         - MAPI przez HTTP (MAPI/HTTP) — używany przez program Outlook 2010 i nowsze.
         - Książka adresowa w trybie offline (OAB) — kopia kolekcji listy adresów, które są pobierane i używane przez program Outlook.
         - Outlook w dowolnym miejscu (RPC przez HTTP) — używany przez program Outlook 2016 i jego starsze wersje.
         - Usługa Outlook — używana przez aplikację poczty i kalendarza dla systemu Windows 10.
         - POP3 — używany przez klientów POP poczty e-mail.
         - Usługi sieci Web raportowania — służy do pobierania danych raportu w usłudze Exchange Online.

Te warunki są często używane, gdy wymagane jest urządzenie zarządzane, blokowanie starszego uwierzytelniania i blokowanie aplikacji sieci Web, ale zezwalanie na aplikacje mobilne lub klasyczne.

### <a name="supported-browsers"></a>Obsługiwane przeglądarki

To ustawienie działa ze wszystkimi przeglądarkami. Jednak w celu spełnienia zasad dotyczących urządzeń, takich jak zgodne wymagania dotyczące urządzeń, obsługiwane są następujące systemy operacyjne i przeglądarki:

| System operacyjny | Przeglądarki |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Dlaczego widzę monit dotyczący certyfikatu w przeglądarce

W systemach Windows 7, iOS, Android i macOS usługa Azure AD identyfikuje urządzenie przy użyciu certyfikatu klienta, który jest inicjowany, gdy urządzenie jest zarejestrowane w usłudze Azure AD.  Gdy użytkownik po raz pierwszy zaloguje się za pomocą przeglądarki, użytkownik jest monitowany o wybranie certyfikatu. Użytkownik musi wybrać ten certyfikat przed użyciem przeglądarki.

#### <a name="chrome-support"></a>Obsługa programu Chrome

Aby uzyskać pomoc techniczną dla programu Chrome w **aktualizacji systemu Windows 10 dla twórców (wersja 1703)** lub nowszą, zainstaluj [rozszerzenie konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). To rozszerzenie jest wymagane, gdy zasady dostępu warunkowego wymagają szczegółowych informacji o urządzeniu.

Aby automatycznie wdrożyć to rozszerzenie w przeglądarkach programu Chrome, Utwórz następujący klucz rejestru:

|    |    |
| --- | --- |
| Ścieżka | HKEY_LOCAL_MACHINE \Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name (Nazwa) | 1 |
| Typ | REG_SZ (ciąg) |
| Dane | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/service/update2/crx |

Aby uzyskać pomoc techniczną dla programu Chrome w **Windows 8.1 i 7**, Utwórz następujący klucz rejestru:

|    |    |
| --- | --- |
| Ścieżka | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name (Nazwa) | 1 |
| Typ | REG_SZ (ciąg) |
| Dane | {"wzorzec": "https://device.login.microsoftonline.com", "filter": {"WYSTAWCa": {"CN": "MS-Organization-Access"}}} |

Te przeglądarki obsługują uwierzytelnianie urządzeń, co pozwala na identyfikację i weryfikację urządzenia względem zasad. Sprawdzenie urządzenia kończy się niepowodzeniem, jeśli przeglądarka działa w trybie prywatnym.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Obsługiwane aplikacje mobilne i klienci stacjonarni

Organizacje mogą wybierać **aplikacje mobilne i klientów stacjonarnych** jako aplikację kliencką.

To ustawienie ma wpływ na próby dostępu wykonane z następujących aplikacji mobilnych i klientów stacjonarnych:

| Aplikacje klienckie | Usługa docelowa | Platforma |
| --- | --- | --- |
| Aplikacja Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS i Android |
| Aplikacja poczty/kalendarza/osoby, Outlook 2016, Outlook 2013 (z nowoczesnego uwierzytelniania)| Office 365 Exchange Online | Windows 10 |
| Zasady usługi MFA i lokalizacji dla aplikacji. Zasady oparte na urządzeniach nie są obsługiwane.| Wszystkie aplikacje my App Service | Systemy Android i iOS |
| Usługi Microsoft Teams — kontroluje wszystkie usługi obsługujące Microsoft Teams i wszystkie jej aplikacje klienckie — Windows Desktop, iOS, Android, WP i klient sieci Web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android i macOS |
| Aplikacje pakietu Office 2016, pakiet Office 2013 (z nowoczesnego uwierzytelniania), [klient synchronizacji usługi OneDrive](https://docs.microsoft.com/onedrive/enable-conditional-access) | Pakiet Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplikacje pakietu Office 2016, uniwersalne aplikacje biurowe, Office 2013 (z nowoczesnego uwierzytelniania), [klient synchronizacji usługi OneDrive](https://docs.microsoft.com/onedrive/enable-conditional-access) | Pakiet Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, tylko OneNote). | Pakiet Office 365 SharePoint Online | macOS |
| Pakiet Office 2019| Pakiet Office 365 SharePoint Online | Windows 10, macOS |
| Aplikacje mobilne pakietu Office | Pakiet Office 365 SharePoint Online | Android, iOS |
| Aplikacja Yammera pakietu Office | Usługa Yammer pakietu Office 365 | Windows 10, iOS, Android |
| Outlook 2019 | Pakiet Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office for macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (z nowoczesnego uwierzytelniania), Skype dla firm (z nowoczesnego uwierzytelniania) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Aplikacja mobilna Outlook | Office 365 Exchange Online | Android, iOS |
| Aplikacja Power BI | usługa Power BI | Windows 10, Windows 8.1, Windows 7, Android i iOS |
| Skype dla firm | Office 365 Exchange Online| Android, iOS |
| Aplikacja Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS i Android |

### <a name="exchange-activesync-clients"></a>Klienci programu Exchange ActiveSync

- W przypadku przypisywania zasad do użytkowników lub grup organizacje mogą wybierać tylko klienci programu Exchange ActiveSync. Po wybraniu opcji **Wszyscy użytkownicy**, wszyscy użytkownicy z **użytkownikami zewnętrznymi**lub **rolami katalogu** spowodują, że wszyscy użytkownicy staną się Zablokowani.
- Podczas tworzenia zasad przypisanych do klientów programu Exchange ActiveSync usługa **Office 365 Exchange Online** powinna być jedyną aplikacją w chmurze przypisaną do zasad. 
- Organizacje mogą zawęzić zakres tych zasad do określonych platform przy użyciu warunku **platformy urządzeń** .

Jeśli funkcja kontroli dostępu przypisanej do zasad **wymaga zatwierdzonej aplikacji klienckiej**, użytkownik jest kierowany do instalacji i używania klienta mobilnego Outlook. W przypadku, gdy wymagane jest **uwierzytelnianie wieloskładnikowe** , których użytkownicy są Zablokowani, ponieważ uwierzytelnianie podstawowe nie obsługuje uwierzytelniania wieloskładnikowego.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Blokuj starsze uwierzytelnianie przy użyciu dostępu warunkowego](block-legacy-authentication.md)
- [Wymaganie zatwierdzonych aplikacji klienckich z dostępem warunkowym](app-based-conditional-access.md)

### <a name="other-clients"></a>Inni klienci

Wybierając **innych klientów**, można określić warunek, który ma wpływ na aplikacje używające uwierzytelniania podstawowego z protokołami pocztowymi, takimi jak IMAP, MAPI, pop, SMTP i starsze aplikacje pakietu Office, które nie używają nowoczesnego uwierzytelniania.

## <a name="device-state-preview"></a>Stan urządzenia (wersja zapoznawcza)

Warunek stanu urządzenia może służyć do wykluczenia urządzeń, które są przyłączone do hybrydowej usługi Azure AD i/lub urządzenia oznaczone jako zgodne z Microsoft Intune zasadami dostępu warunkowego w organizacji.

Na przykład *Wszyscy użytkownicy* uzyskują dostęp do aplikacji usługi *Microsoft Azure Management* w chmurze, w tym **wszystkich stanów urządzeń** , z wyłączeniem **przyłączonych do urządzeń hybrydowych usług Azure AD** i **urządzenia oznaczone jako zgodne** oraz dla *kontroli dostępu* **.** 
   - W tym przykładzie utworzysz zasady zezwalające na dostęp do Microsoft Azure zarządzania z urządzeń, które są przyłączone do hybrydowej usługi Azure AD i/lub urządzenia oznaczone jako zgodne.

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: Udziel](concept-conditional-access-grant.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
