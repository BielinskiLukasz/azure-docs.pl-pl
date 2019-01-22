---
title: Konfigurowanie aplikacji uwierzytelniania OpenID/OAuth z galerii aplikacji usługi Azure AD | Microsoft Docs
description: Kroki konfigurowania aplikacji uwierzytelniania OpenID/OAuth z galerii aplikacji usługi Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jeedes
ms.openlocfilehash: 1215e0060662e0d5261d3aeac4874e5833131ba3
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260843"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurowanie aplikacji uwierzytelniania OpenID/OAuth z galerii aplikacji usługi Azure AD

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Proces dodawania aplikacji uwierzytelniania OpenID z galerii

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**. 

    ![Przycisk Azure Active Directory](common/select-azuread.png))

2. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Wybierz pozycję **Nowa aplikacja** u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz nazwę aplikacji. Wybierz żądaną aplikację z panelu wyników i zarejestruj ją.

    ![Pozycja Openid na liście wyników](common/search-new-app.png)

    > [!NOTE]
    > W przypadku aplikacji uwierzytelniania OpenID Connect i OAuth przycisk **Dodaj** jest domyślnie wyłączony. W tym miejscu administrator dzierżawy powinien wybrać przycisk rejestrowania i wyrazić zgodę na aplikację. Aplikacja zostanie wtedy dodana do dzierżawy klienta, gdzie można ją skonfigurować. Jawne dodawanie aplikacji nie jest potrzebne.

    ![Przycisk dodawania](./media/openidoauth-tutorial/addbutton.png)

5. Po wybraniu linku rejestracji nastąpi przekierowanie do strony usługi Azure Active Directory (Azure AD) w celu podania poświadczeń logowania.

6. Po pomyślnym uwierzytelnieniu należy zaakceptować zgodę na stronie zgody. Aplikacja zostanie wtedy wyświetlona na stronie głównej.

    > [!NOTE]
    > Istnieje możliwość dodania tylko jednego wystąpienia aplikacji. Jeśli wystąpienie zostało już dodane, to próba ponownego wyrażenia zgody nie spowoduje ponownego dodania aplikacji do dzierżawy. Dlatego logicznie można używać tylko jednego wystąpienia aplikacji w dzierżawie.

## <a name="authentication-flow-using-openid-connect"></a>Przepływ uwierzytelniania w przypadku protokołu OpenID Connect

Najbardziej podstawowy przepływ logowania zawiera następujące kroki:

![Przepływ uwierzytelniania w przypadku protokołu OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Aplikacja wielodostępna 
Aplikacje wielodostępne są przeznaczona do użytku w wielu (a nie tylko jednej) organizacjach. Są to zazwyczaj aplikacje typu SaaS (software-as-a-service) napisane przez niezależnych dostawców oprogramowania (ISV). 

Aplikację wielodostępną należy aprowizować w każdym katalogu, w którym będzie używana. Ich zarejestrowanie wymaga zgody użytkownika lub administratora. Proces wyrażania zgody rozpoczyna się po zarejestrowaniu aplikacji w katalogu i udzieleniu jej dostępu do interfejsu API programu Graph lub innego internetowego interfejsu API. Gdy użytkownik lub administrator z innej organizacji zarejestruje się w celu korzystania z aplikacji, zostanie wyświetlone okno dialogowe z uprawnieniami wymaganymi przez aplikację. 

Użytkownik lub administrator może wtedy wyrazić zgodę na aplikację. Zgoda umożliwia aplikacji dostęp do określonych danych oraz ostatecznie rejestruje aplikację w katalogu.

> [!NOTE]
> Jeśli udostępniasz aplikację użytkownikom w wielu katalogach, potrzebujesz mechanizmu określania dzierżawy, w której się znajduje. Aplikacja jednodostępna musi szukać użytkownika tylko w swoim własnym katalogu. Aplikacja wielodostępna musi zidentyfikować określonego użytkownika na podstawie wszystkich katalogów w usłudze Azure AD.
> 
> Aby to umożliwić, usługa Azure AD udostępnia wspólny punkt końcowy uwierzytelniania (zamiast punktów końcowych specyficznych dla dzierżawy), gdzie dowolna aplikacja wielodostępna może kierować żądania logowania. Ten punkt końcowy to [https://login.microsoftonline.com/common](https://login.microsoftonline.com/common) dla wszystkich katalogów w usłudze Azure AD. Punkt końcowy specyficzny dla dzierżawy to na przykład [https://login.microsoftonline.com/contoso.onmicrosoft.com](https://login.microsoftonline.com/contoso.onmicrosoft.com). 
>
> Wspólny punkt końcowy to ważne zagadnienie do uwzględnienia podczas opracowywania aplikacji. Potrzebna będzie logika wymagana do obsługi wielu dzierżaw podczas logowania, wylogowania i walidacji tokenów.

Domyślnie usługa Azure AD promuje aplikacje wielodostępne. Łatwo jest uzyskać do nich dostęp z wielu organizacji i używać po zaakceptowaniu zgody.

## <a name="consent-framework"></a>Platforma wyrażania zgody

Struktura wyrażania zgody w usłudze Azure AD umożliwia opracowywanie wielodostępnych aplikacji internetowych i natywnych aplikacji klienckich. Te aplikacje umożliwiają logowanie za pomocą kont użytkownika z dzierżawy usługi Azure AD innej niż dzierżawa, w której aplikacja jest zarejestrowana. Mogą ona także potrzebować dostępu do internetowych interfejsów API, takich jak:
- Interfejs API programu Microsoft Graph — na potrzeby dostępu do usług Azure AD, Intune i usług w ramach platformy Office 365. 
- Inne interfejsy API usług firmy Microsoft.
- Twoje własne internetowe interfejsy API. 

Struktura opiera się na zgodzie wyrażanej przez użytkownika lub administratora w odpowiedzi na żądanie zarejestrowania w katalogu kierowane przez aplikację. Rejestracja może obejmować uzyskiwanie dostępu do danych katalogu. Po wyrażeniu zgody aplikacja kliencka może wywoływać interfejs API programu Microsoft Graph w imieniu użytkownika i używać informacji zgodnie z potrzebami.

[Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph/) zapewnia dostęp do danych w usłudze Office 365, takich jak:

- Kalendarze i komunikaty z programu Exchange
- Witryny i listy z programu SharePoint
- Dokumenty z usługi OneDrive
- Notesy z programu OneNote
- Zadania z programu Planner
- Skoroszyty z programu Excel

Interfejs API programu Graph umożliwia również dostęp do użytkowników i grup z usługi Azure AD oraz innych obiektów danych z różnych usług w chmurze firmy Microsoft.

Poniższe kroki pokazują, jak środowisko wyrażania zgody działa dla deweloperów aplikacji i użytkownika:

1. Załóżmy, że masz internetową aplikację kliencką, który wymaga zażądania konkretnego uprawnienia na potrzeby dostępu do zasobu lub interfejsu API. Witryna Azure Portal jest używana do deklarowania żądań dotyczących uprawnień podczas konfigurowania. Podobnie jak inne ustawienia konfiguracji, staną się one częścią rejestracji aplikacji w usłudze Azure AD:

    ![Interfejs API programu Graph](./media/openidoauth-tutorial/graphapi.png)

2. Rozważ sytuację, w której uprawnienia aplikacji zostały zaktualizowane. Aplikacja jest uruchamiana i użytkownik użyje jej po raz pierwszy. Najpierw aplikacja musi pobrać kod autoryzacji z punktu końcowego autoryzacji / usługi Azure AD. Może wtedy użyć kodu autoryzacji do uzyskania nowego tokenu dostępu i odświeżania.

3. Jeśli użytkownik nie jest jeszcze uwierzytelniony, punkt końcowy autoryzacji / usługa Azure AD wyświetli monit logowania.

    ![Authentication](./media/openidoauth-tutorial/authentication.png)

4. Gdy użytkownik jest już zalogowany, usługa Azure AD określa, czy należy mu pokazać stronę wyrażenia zgody. Jest to uzależnione do tego, czy użytkownik (lub administrator w jego organizacji) już wyraził zgodę na aplikację.

   Jeśli nie wyrażono zgody, usługa Azure AD monituje o nią użytkownika i wyświetla uprawnienia wymagane przez aplikację do działania. Uprawnienia wyświetlane w oknie dialogowym zgody odpowiadają uprawnieniom wybranym w ramach delegowanych uprawnień w witrynie Azure Portal.

    ![Strona zgody](./media/openidoauth-tutorial/consentpage.png)

Zwykły użytkownik może wyrazić zgodę na niektóre uprawnienia. Pozostałe uprawnienia wymagają zgody administratora dzierżawy.

## <a name="difference-between-admin-consent-and-user-consent"></a>Różnica między zgodą administratora i zgodą użytkownika

Jako administrator możesz także wyrazić zgodę na uprawnienia delegowane aplikacji w imieniu wszystkich użytkowników w dzierżawie. Zgoda administratora zapobiega wyświetlaniu okna dialogowego zgody dla każdego użytkownika w dzierżawie. Użytkownicy z rolą administratora mogą wyrazić zgodę w witrynie Azure Portal. Na stronie **Ustawienia** aplikacji wybierz pozycję **Wymagane uprawnienia** > **Udziel uprawnień**.

![Przycisk Udziel uprawnień](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Jawne wyrażenie zgody przez użycie przycisku **Udziel uprawnień** jest teraz wymagane dla aplikacji jednostronicowych, które używają pliku ADAL.js. W przeciwnym przypadku wystąpi błąd aplikacji przy żądaniu tokenu dostępu.

Uprawnienia dotyczące tylko aplikacji zawsze wymagają zgody administratora dzierżawy. Jeśli aplikacja żąda uprawnienia dotyczącego tylko aplikacji, a użytkownik spróbuje zalogować się do aplikacji, zostanie wyświetlony komunikat o błędzie. Komunikat informuje, że użytkownik nie jest w stanie wyrazić zgody.

Jeśli aplikacja używa uprawnień wymagających zgody administratora, potrzebujesz elementu takiego jak przycisk lub link, za pomocą którego administrator może uruchomić akcję. Żądanie wysyłane przez aplikację na potrzeby tej akcji to zwykłe żądanie autoryzacji OAuth2/OpenID Connect. To żądanie zawiera parametr ciągu zapytania *prompt=admin_consent*. 

Gdy administrator wyrazi zgodę i zostanie utworzona jednostka usługi w dzierżawie klienta, późniejsze żądania logowania nie będą wymagać parametru *prompt=admin_consent*. Ponieważ administrator zdecydował, że żądane uprawnienia są akceptowalne, od tego momentu żadni inni użytkownicy w dzierżawie nie będą monitowani o zgodę.

Administrator dzierżawy może wyłączyć możliwość wyrażania zgody na aplikacje przez zwykłych użytkowników. Jeśli ta funkcja jest wyłączona, zgoda administratora jest zawsze wymagana do używania aplikacji w dzierżawie. Jeśli chcesz przetestować aplikację z wyłączoną zgodą użytkownika końcowego, możesz znaleźć odpowiedni przełącznik konfiguracji w [witrynie Azure Portal](https://portal.azure.com/). Znajduje się on w sekcji [Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) w obszarze **Aplikacje dla przedsiębiorstw**.

Parametru *prompt=admin_consent* mogą też używać aplikacje wymagające uprawnień, dla których zgoda administratora nie jest konieczna. Przykładem jest aplikacja, która wymaga środowiska na potrzeby jednorazowej „rejestracji” przez administratora dzierżawy, a potem żaden inny użytkownik nie jest monitowany o zgodę.

Wyobraź sobie sytuację, w której aplikacja wymaga zgody administratora i administrator loguje się bez wysyłania parametru *prompt=admin_consent*. Gdy administrator pomyślnie wyrazi zgodę na aplikację, będzie ona mieć zastosowanie tylko dla jego konta użytkownika. Zwykli użytkownicy nadal nie będą mogli zalogować się ani wyrazić zgody na aplikację. Ta funkcja jest przydatna, jeśli chcesz dać administratorowi dzierżawy możliwość przeanalizowania aplikacji przed zezwoleniem na dostęp innych użytkowników.