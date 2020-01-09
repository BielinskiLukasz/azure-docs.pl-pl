---
title: Interfejs API do dołączania aplikacji platformy Azure w komercyjnej witrynie Marketplace
description: Wymagania wstępne dotyczące interfejsu API dla aplikacji platformy Azure w komercyjnej witrynie Marketplace w centrum partnerskim firmy Microsoft.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: e0904169924aed7aeb1df5297a3a20f14cf7c6ae
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480608"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>Interfejs API do dołączania aplikacji platformy Azure w centrum partnerskim

Użyj *interfejsu API przesyłania Centrum partnerskiego* , aby programowo wykonywać zapytania, tworzyć zgłoszenia do i publikować oferty platformy Azure.  Ten interfejs API jest przydatny, jeśli Twoje konto zarządza wieloma ofertami i chcesz zautomatyzować i zoptymalizować proces tworzenia tych ofert.

## <a name="api-prerequisites"></a>Wymagania wstępne dotyczące interfejsu API

Istnieje kilka zasobów programistycznych, które są potrzebne do korzystania z interfejsu API Centrum partnerskiego dla produktów platformy Azure: 

- Aplikacja Azure Active Directory.
- token dostępu Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Krok 1. pełne wymagania wstępne dotyczące korzystania z interfejsu API przekazywania Centrum partnerskiego

Przed rozpoczęciem pisania kodu w celu wywołania interfejsu API przekazywania Centrum partnerskiego upewnij się, że zostały spełnione następujące wymagania wstępne.

- Użytkownik (lub Twoja organizacja) musi mieć katalog usługi Azure AD, a użytkownik musi mieć uprawnienia [administratora globalnego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) dla katalogu. Jeśli korzystasz już z pakietu Office 365 lub innych usług firmowych firmy Microsoft, masz już katalog usługi Azure AD. W przeciwnym razie można [utworzyć nową usługę Azure AD w centrum partnerskim](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) bez dodatkowych opłat.

- Musisz [skojarzyć aplikację usługi Azure AD z kontem Centrum partnerskiego](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) i uzyskać identyfikator dzierżawy, identyfikator klienta i klucz. Te wartości są potrzebne do uzyskania tokenu dostępu usługi Azure AD, który będzie używany w wywołaniach interfejsu API przekazywania Microsoft Store.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Jak skojarzyć aplikację usługi Azure AD z kontem Centrum partnerskiego

Aby korzystać z interfejsu API przekazywania Microsoft Store, należy skojarzyć aplikację usługi Azure AD z kontem Centrum partnerskiego, pobrać identyfikator dzierżawy i identyfikator klienta dla aplikacji, a następnie wygenerować klucz. Aplikacja usługi Azure AD reprezentuje aplikację lub usługę, z której chcesz wywołać interfejs API przekazywania Centrum partnerskiego. Potrzebujesz identyfikatora dzierżawy, identyfikatora klienta i klucza, aby uzyskać token dostępu usługi Azure AD przekazywany do interfejsu API.

>[!Note]
>Wystarczy wykonać to zadanie tylko raz. Jeśli masz identyfikator dzierżawy, identyfikator klienta i klucz, możesz użyć ich ponownie za każdym razem, gdy potrzebujesz utworzyć nowy token dostępu usługi Azure AD.

1. W centrum partnerskim [Skojarz konto Centrum partnerskiego organizacji z katalogiem usługi Azure AD Twojej organizacji](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Następnie na stronie **Użytkownicy** w sekcji **Ustawienia konta** w centrum partnerskim [Dodaj aplikację usługi Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) reprezentującą aplikację lub usługę, która będzie używana w celu uzyskania dostępu do przesłanych danych dla konta Centrum partnerskiego. Upewnij się, że przypiszesz tej aplikacji rolę **Menedżer** . Jeśli aplikacja nie istnieje jeszcze w katalogu usługi Azure AD, możesz [utworzyć nową aplikację usługi Azure AD w centrum partnerskim](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Wróć do strony **Użytkownicy** , kliknij nazwę aplikacji usługi Azure AD, aby przejść do ustawień aplikacji, a następnie skopiuj wartości **Identyfikator dzierżawy** i identyfikator **klienta** .
1. Kliknij przycisk **Dodaj nowy klucz**. Na poniższym ekranie skopiuj wartość **klucza** . Nie będzie można uzyskać dostępu do tych informacji ponownie po opuszczeniu tej strony. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dla aplikacji usługi Azure AD](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Krok 2. Uzyskiwanie tokenu dostępu usługi Azure AD

Przed wywołaniem dowolnej metody w interfejsie API przekazywania Centrum partnerskiego należy najpierw uzyskać token dostępu usługi Azure AD przekazany do nagłówka **autoryzacji** każdej metody w interfejsie API. Po uzyskaniu tokenu dostępu masz 60 minut, aby użyć go przed wygaśnięciem. Po wygaśnięciu tokenu można odświeżyć token, aby można było go nadal używać w przyszłych wywołaniach interfejsu API.

Aby uzyskać token dostępu, postępuj zgodnie z instrukcjami zawartymi w temacie [Service to Service Calls using przy użyciu poświadczeń klienta](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) , aby wysłać `HTTP POST` do punktu końcowego `https://login.microsoftonline.com/<tenant_id>/oauth2/token`. Oto przykładowe żądanie:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Dla wartości *tenant_id* w `POST URI` i *client_id* i *client_secret* parametry Określ identyfikator dzierżawy, identyfikator klienta i klucz aplikacji pobranej z Centrum partnerskiego w poprzedniej sekcji. Dla parametru *Resource* należy określić `https://api.partner.microsoft.com`.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Krok 3. Korzystanie z interfejsu API przekazywania Microsoft Store

Po uzyskaniu tokenu dostępu usługi Azure AD możesz wywoływać metody w interfejsie API przekazywania Centrum partnerskiego. W celu utworzenia lub zaktualizowania przesyłanych jest zwykle wiele metod w interfejsie API przesyłania Centrum partnerskiego w określonej kolejności. Aby uzyskać informacje o każdym scenariuszu i składni każdej z tych metod, zobacz temat Swagger API pozyskiwania.

https://apidocs.microsoft.com/services/partneringestion/
