---
title: Konfigurowanie zarządzania sesjami uwierzytelniania — Azure Active Directory
description: Dostosuj konfigurację sesji uwierzytelniania usługi Azure AD, w tym częstotliwość logowania użytkownika i trwałość sesji przeglądarki.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb094d04a7210d76a98f3e47af750e49b617e493
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77195066"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego

W złożonych wdrożeniach organizacje mogą wymagać ograniczenia sesji uwierzytelniania. Niektóre scenariusze mogą obejmować:

* Dostęp do zasobów z urządzenia niezarządzanego lub udostępnionego
* Dostęp do poufnych informacji z sieci zewnętrznej
* Użytkownicy o dużym wpływie
* Krytyczne aplikacje biznesowe

Kontrole dostępu warunkowego umożliwiają tworzenie zasad przeznaczonych dla konkretnych przypadków użycia w organizacji bez wywierania wpływu na wszystkich użytkowników.

Przed wprowadzeniem szczegółowych informacji na temat sposobu konfigurowania zasad Sprawdźmy konfigurację domyślną.

## <a name="user-sign-in-frequency"></a>Częstotliwość logowania użytkownika

Częstotliwość logowania określa czas, po którym użytkownik zostanie poproszony o ponowne zalogowanie przy próbie uzyskania dostępu do zasobu.

Domyślna konfiguracja Azure Active Directory (Azure AD) dla częstotliwości logowania użytkownika jest oknem kroczącym 90 dni. Monitowanie użytkowników o poświadczenia często wygląda na to, że jest to rozsądne działanie, ale może się to wiązać z atakami: Użytkownicy, którzy są przeszkoleni, aby wprowadzić swoje poświadczenia bez względu na to, że mogą przypadkowo dostarczyć je do złośliwego monitu o poświadczenia.

Może to spowodować, że dźwięk nie zostanie poproszony o ponowne zalogowanie się użytkownika, w rzeczywistości wszystkie zasady IT odwołują sesję. Niektóre przykłady obejmują (ale nie ograniczone do) zmianę hasła, niezgodne urządzenie lub wyłączone konto. Możesz również jawnie [odwołać sesje użytkowników przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Domyślna konfiguracja usługi Azure AD to "nie Monituj użytkowników o podanie swoich poświadczeń, jeśli zabezpieczenia stan ich sesji nie zostały zmienione".

Ustawienie częstotliwości logowania działa z aplikacjami, które mają wdrożone protokoły OAUTH2 lub OIDC zgodnie ze standardami. Większość aplikacji natywnych firmy Microsoft dla systemów Windows, Mac i Mobile, w tym z uwzględnieniem następujących aplikacji sieci Web, jest zgodnych z tym ustawieniem.

- Word, Excel, PowerPoint Online
- OneNote online
- Office.com
- Portal administracyjny usługi O365
- Exchange Online
- Program SharePoint i usługa OneDrive
- Klient sieci Web dla zespołów
- Dynamics CRM Online
- Portalu Azure

### <a name="user-sign-in-frequency-and-device-identities"></a>Częstotliwość logowania użytkownika i tożsamości urządzeń

W przypadku przyłączenia do usługi Azure AD hybrydowej usługi Azure AD lub zarejestrowanych urządzeń usługi Azure AD, gdy użytkownik odblokowuje urządzenie lub loguje się interaktywnie, to zdarzenie będzie również spełniało zasady częstotliwości logowania. W następującej 2 Przykładowa częstotliwość logowania użytkownika jest ustawiona na 1 godzinę:

Przykład 1:

- W dniu 00:00 użytkownik loguje się do urządzenia dołączonego do usługi Azure AD systemu Windows 10 i zaczyna pracę nad dokumentem przechowywanym w usłudze SharePoint Online.
- Użytkownik kontynuuje pracę nad tym samym dokumentem na swoim urządzeniu przez godzinę.
- W dniu 01:00 użytkownik jest monitowany o ponowne zalogowanie się na podstawie wymagań dotyczących częstotliwości logowania w zasadach dostępu warunkowego skonfigurowanych przez administratora.

Przykład 2:

- W dniu 00:00 użytkownik loguje się do urządzenia dołączonego do usługi Azure AD systemu Windows 10 i zaczyna pracę nad dokumentem przechowywanym w usłudze SharePoint Online.
- O godzinie 00:30 użytkownik otrzymuje i zablokuje zablokowanie urządzenia.
- O 00:45 użytkownik wraca z ich przerw i odblokowuje urządzenie.
- W dniu 01:45 użytkownik jest monitowany o ponowne zalogowanie się na podstawie wymagań dotyczących częstotliwości logowania w zasadach dostępu warunkowego skonfigurowanych przez ich administratora od momentu ostatniego zalogowania się o 00:45.

## <a name="persistence-of-browsing-sessions"></a>Trwałość sesji przeglądania

W trwałej sesji przeglądarki użytkownicy mogą pozostać zalogowani po zamknięciu i ponownym otwarciu okna przeglądarki.

Wartość domyślna usługi Azure AD na potrzeby trwałości sesji przeglądarki umożliwia użytkownikom na urządzeniach osobistych wybieranie, czy sesja ma być utrwalana, pokazując "pozostania w rejestracji". Monituj po pomyślnym uwierzytelnieniu. Jeśli trwałość przeglądarki jest skonfigurowana w AD FS przy użyciu wskazówek w artykule [AD FS ustawienia logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)jednokrotnego, firma Microsoft będzie przestrzegać tych zasad i utrzymywać również sesję usługi Azure AD. Możesz również określić, czy użytkownicy w dzierżawie zobaczą "pozostania w Twojej rejestracji"? Monituj o zmianę odpowiedniego ustawienia w okienku znakowanie firmowe w Azure Portal przy użyciu wskazówek w artykule [Dostosowywanie strony logowania usługi Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurowanie kontrolek sesji uwierzytelniania

Dostęp warunkowy jest możliwością Azure AD — wersja Premium i wymaga licencji Premium. Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostęp warunkowy w Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Jeśli używasz funkcji [okresowego istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md) , który jest obecnie w publicznej wersji zapoznawczej, pamiętaj, że nie obsługujemy tworzenia dwóch różnych zasad dla tej samej kombinacji użytkownika lub aplikacji: jeden z tą funkcją i inną z konfigurowalną funkcją okresu istnienia tokenu. Firma Microsoft planuje wycofać funkcję konfigurowalnego okresu istnienia tokenu 1 maja 2020 i zamienić ją na funkcję zarządzania sesjami uwierzytelniania dostępu warunkowego.  

### <a name="policy-1-sign-in-frequency-control"></a>Zasady 1: kontrola częstotliwości logowania

1. Tworzenie nowych zasad
1. Wybierz wszystkie wymagane warunki dla środowiska klienta, w tym docelowe aplikacje w chmurze.

   > [!NOTE]
   > Zaleca się skonfigurowanie równej częstotliwości monitów uwierzytelniania dla Microsoft Office najważniejszych aplikacji, takich jak Exchange Online i SharePoint Online, w celu zapewnienia najlepszego środowiska użytkownika.

1. Przejdź do obszaru **kontroli dostępu** > **sesji** , a następnie kliknij pozycję **częstotliwość logowania**
1. Wprowadź wymaganą wartość dni i godziny w pierwszym polu tekstowym
1. Wybierz wartość **godzin** lub **dni** z listy rozwijanej
1. Zapisywanie zasad

![Zasady dostępu warunkowego skonfigurowane dla częstotliwości logowania](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Na zarejestrowanych urządzeniach z systemem Windows w usłudze Azure AD Zaloguj się na urządzeniu jako monit. Jeśli na przykład skonfigurowano częstotliwość logowania na 24 godziny dla aplikacji pakietu Office, użytkownicy usługi Azure AD zarejestrowane urządzenia z systemem Windows będą spełniać zasady częstotliwości logowania, logując się na urządzeniu i nie będą monitowani ponownie podczas otwierania aplikacji pakietu Office.

Jeśli skonfigurowano inną częstotliwość logowania dla różnych aplikacji sieci Web, które są uruchomione w tej samej sesji przeglądarki, najwyższe zasady zostaną zastosowane do obu aplikacji, ponieważ wszystkie aplikacje uruchomione w tej samej sesji przeglądarki współdzielą token sesji.

### <a name="policy-2-persistent-browser-session"></a>Zasady 2: nietrwała sesja przeglądarki

1. Tworzenie nowych zasad
1. Wybierz wszystkie wymagane warunki.

   > [!NOTE]
   > Należy pamiętać, że ta kontrolka wymaga wybrania opcji "wszystkie aplikacje w chmurze" jako warunek. Trwałość sesji przeglądarki jest kontrolowana przez token sesji uwierzytelniania. Wszystkie karty w sesji przeglądarki współdzielą jeden token sesji i w związku z tym wszystkie muszą mieć stan trwałości.

1. Przejdź do obszaru **kontroli dostępu** > **sesji** , a następnie kliknij pozycję **trwała sesja przeglądarki**
1. Wybierz wartość z listy rozwijanej
1. Zapisz zasady

![Zasady dostępu warunkowego skonfigurowane dla trwałej przeglądarki](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Trwała konfiguracja sesji przeglądarki w usłudze Azure AD dostęp warunkowy spowoduje zastąpienie "pozostać zalogowanym?" ustawienie w okienku znakowanie firmowe w Azure Portal dla tego samego użytkownika, jeśli skonfigurowano obie zasady.

## <a name="validation"></a>Walidacja

Za pomocą narzędzia do wykonywania symulacji Zasymuluj Logowanie użytkownika do aplikacji docelowej i innych warunków w zależności od sposobu skonfigurowania zasad. Kontrolki zarządzanie sesjami uwierzytelniania są wyświetlane w wyniku tego narzędzia.

![Wyniki narzędzia What If dostępu warunkowego](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Wdrażanie zasad

Aby upewnić się, że zasady działają zgodnie z oczekiwaniami, zalecanym najlepszym rozwiązaniem jest przetestowanie go przed wycofaniem do produkcji. Najlepiej użyć dzierżawy testowej, aby sprawdzić, czy nowe zasady działają zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zapoznaj się z artykułem [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
* Aby skonfigurować zasady dostępu warunkowego dla środowiska, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w Azure Active Directory](best-practices.md).
