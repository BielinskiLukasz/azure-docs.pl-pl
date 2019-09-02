---
title: 'Program Azure AD Connect: Bezproblemowe logowanie jednokrotne — często zadawane pytania | Microsoft Docs'
description: Odpowiedzi na często zadawane pytania dotyczące Azure Active Directory bezproblemowego logowania jednokrotnego.
services: active-directory
keywords: Co to jest Azure AD Connect, zainstaluj Active Directory, wymagane składniki usługi Azure AD, logowania jednokrotnego, rejestracji jednokrotnej
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709fb3be37850be37d6378652921ce26f4ff15fe
ms.sourcegitcommit: dcea3c1ab715a79ebecd913885fbf9bbee61606a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "60242230"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory bezproblemowe logowanie jednokrotne: Często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące Azure Active Directory bezproblemowego logowania jednokrotnego. Kontynuuj sprawdzanie pod kątem nowej zawartości.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Jakie metody logowania są bezproblemowo wykonywane z logowaniem jednokrotnym?

Bezproblemowe logowanie jednokrotne może być łączone z [synchronizacją skrótów haseł](how-to-connect-password-hash-synchronization.md) lub metodami logowania przy [użyciu uwierzytelniania przekazywanego](how-to-connect-pta.md) . Ta funkcja nie może być używana z usługą Active Directory Federation Services (AD FS).

## <a name="is-seamless-sso-a-free-feature"></a>Czy bezproblemowe logowanie jednokrotne jest bezpłatne?

Bezproblemowe logowanie jednokrotne jest bezpłatną funkcją i nie są potrzebne żadne płatne wersje usługi Azure AD do użycia.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Jest bezproblemowe dostęp do usługi SSO w [chmurze Microsoft Azure (Niemcy)](https://www.microsoft.de/cloud-deutschland) i w [chmurze Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Nie. Bezproblemowe logowanie jednokrotne jest dostępne tylko w świecie wystąpienia usługi Azure AD.

## <a name="what-applications-take-advantage-of-domain_hint-or-login_hint-parameter-capability-of-seamless-sso"></a>Jakie aplikacje wykorzystują `domain_hint` funkcję lub `login_hint` możliwość bezproblemowego logowania jednokrotnego?

Poniżej wymieniono niekompletną listę aplikacji, które mogą wysyłać te parametry do usługi Azure AD. w związku z tym użytkownicy uzyskują dyskretne środowisko logowania za pomocą bezproblemowego logowania jednokrotnego (tzn. nie ma potrzeby wprowadzania nazw użytkowników ani haseł przez użytkownika):

| Nazwa aplikacji | Adres URL aplikacji do użycia |
| -- | -- |
| Panel dostępu | https:\//myapps.Microsoft.com/contoso.com |
| Program Outlook w sieci Web | https:\//Outlook.office365.com/contoso.com |
| Portale pakietu Office 365 | https:\//Portal.Office.com? domain_hint = contoso. com, https:\//www.Office.com? domain_hint = contoso. com |

Ponadto użytkownicy uzyskują ciche czynności w przypadku, gdy aplikacja wysyła żądania logowania do punktów końcowych usługi Azure AD skonfigurowanych jako dzierżawcy — czyli https:\//login.microsoftonline.com/contoso.com/<.. > lub https:\//login.microsoftonline.com/<tenant_ID>/<.. > — zamiast wspólnego punktu końcowego usługi Azure AD, czyli https:\//login.microsoftonline.com/common/<... >. Poniżej wymieniono niekompletną listę aplikacji, które tworzą te typy żądań logowania.

| Nazwa aplikacji | Adres URL aplikacji do użycia |
| -- | -- |
| SharePoint Online | https:\//contoso.SharePoint.com |
| Azure Portal | https:\//Portal.Azure.com/contoso.com |

W powyższych tabelach Zastąp ciąg "contoso.com" nazwą domeny, aby uzyskać dostęp do odpowiednich adresów URL aplikacji dla dzierżawy.

Jeśli chcesz, aby inne aplikacje korzystali z naszych dyskretnych funkcji logowania, poinformuj nas o tym w sekcji opinii.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Czy nie jest to `Alternate ID` nazwa użytkownika, `userPrincipalName`która bezproblemowo obsługuje logowanie jednokrotne?

Tak. Bezproblemowe logowanie jednokrotne jest obsługiwane `Alternate ID` jako nazwa użytkownika po skonfigurowaniu w Azure AD Connect, jak pokazano [poniżej](how-to-connect-install-custom.md). Nie wszystkie aplikacje `Alternate ID`pakietu Office 365. Zapoznaj się z dokumentacją dotyczącą pomocy technicznej dotyczącej określonej aplikacji.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Czym różni się środowisko logowania jednokrotnego zapewniane przez [usługę Azure AD Join](../active-directory-azureadjoin-overview.md) i bezproblemowe logowanie jednokrotne?

[Usługa Azure AD Join](../active-directory-azureadjoin-overview.md) umożliwia logowanie jednokrotne dla użytkowników, jeśli ich urządzenia są zarejestrowane w usłudze Azure AD. Urządzenia te nie muszą być przyłączone do domeny. Logowanie jednokrotne jest dostarczane przy użyciu *podstawowych tokenów odświeżania* lub *PRTs*, a nie protokołu Kerberos. Środowisko użytkownika jest najbardziej optymalne na urządzeniach z systemem Windows 10. Logowanie jednokrotne odbywa się automatycznie w przeglądarce Microsoft Edge. Działa również w programie Chrome przy użyciu rozszerzenia przeglądarki.

W dzierżawie można używać zarówno sprzężenia usługi Azure AD, jak i bezproblemowego logowania jednokrotnego. Te dwie funkcje uzupełniają się. Jeśli obie funkcje są włączone, logowanie jednokrotne z usługi Azure AD ma pierwszeństwo przed bezproblemowym logowaniem jednokrotnym.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Chcę zarejestrować urządzenia z systemem innym niż Windows 10 w usłudze Azure AD bez używania AD FS. Czy zamiast tego można użyć bezproblemowego logowania jednokrotnego?

Tak, ten scenariusz wymaga wersji 2,1 lub nowszej klienta dołączania do [miejsca pracy](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Jak można wycofać klucz `AZUREADSSOACC` odszyfrowujący protokołu Kerberos konta komputera?

Ważne jest, aby często przetworzyć klucz `AZUREADSSOACC` odszyfrowujący protokołu Kerberos konta komputera (co reprezentuje usługę Azure AD) utworzony w lokalnym lesie usługi AD.

>[!IMPORTANT]
>Zdecydowanie zalecamy przeprowadzenie klucza odszyfrowywania Kerberos co najmniej co 30 dni.

Wykonaj następujące kroki na serwerze lokalnym, na którym jest uruchomiony program Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 1. Pobierz listę lasów usługi AD, w których włączono bezproblemowe logowanie jednokrotne

1. Najpierw pobierz i zainstaluj program [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj bezproblemowe moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.
4. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj `New-AzureADSSOAuthenticationContext`polecenie. To polecenie powinno umożliwić podręczne wprowadzenie poświadczeń administratora globalnego dzierżawy.
5. Wywołanie `Get-AzureADSSOStatus | ConvertFrom-Json`. To polecenie umożliwia wyświetlenie listy lasów usługi AD (Zobacz listę "domeny"), na której włączono tę funkcję.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Krok 2. Zaktualizuj klucz odszyfrowujący protokołu Kerberos w każdym lesie usługi AD, na którym został on skonfigurowany

1. Wywołanie `$creds = Get-Credential`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi AD.

   > [!NOTE]
   > Używamy nazwy użytkownika administratora domeny, która została podana w formacie głównej nazwy użytkownika (UPN)johndoe@contoso.com() lub kwalifikowanej nazwy konta sam (contoso\johndoe lub contoso. com\johndoe) zgodnej z domeną, aby znaleźć zamierzony Las usługi AD. Jeśli używana jest kwalifikowana nazwa konta sam, używana jest część domeny nazwy użytkownika do [lokalizowania kontrolera domeny administratora domeny przy użyciu systemu DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Jeśli zamiast tego używasz nazwy UPN, [przetłumaczmy ją na nazwę konta sam z kwalifikowaną domeną](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) .

2. Wywołanie `Update-AzureADSSOForest -OnPremCredentials $creds`. To polecenie aktualizuje klucz odszyfrowujący protokołu Kerberos dla `AZUREADSSOACC` konta komputera w tym określonym lesie usługi AD i aktualizuje go w usłudze Azure AD.
3. Powtórz powyższe kroki dla każdego lasu usługi AD, dla którego skonfigurowano funkcję.

>[!IMPORTANT]
>Upewnij się, że `Update-AzureADSSOForest` polecenie nie jest uruchamiane więcej niż raz. W przeciwnym razie funkcja przestanie działać do czasu wygaśnięcia biletów protokołu Kerberos użytkowników i zostanie ponownie wystawiona przez Active Directory lokalnych.

## <a name="how-can-i-disable-seamless-sso"></a>Jak mogę wyłączyć bezproblemowe logowanie jednokrotne?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Krok 1. Wyłącz funkcję w dzierżawie

#### <a name="option-a-disable-using-azure-ad-connect"></a>Opcja A: Wyłącz używanie Azure AD Connect

1. Uruchom Azure AD Connect, wybierz pozycję **Zmień stronę logowania użytkownika** , a następnie kliknij przycisk **dalej**.
2. Usuń zaznaczenie opcji **Włącz logowanie** jednokrotne. Kontynuuj pracę z kreatorem.

Po ukończeniu działania kreatora bezproblemowy Logowanie jednokrotne zostanie wyłączone w dzierżawie. Zobaczysz jednak komunikat na ekranie, który odczytuje w następujący sposób:

"Logowanie jednokrotne jest teraz wyłączone, ale istnieją dodatkowe czynności ręczne, które należy wykonać w celu ukończenia czyszczenia. Dowiedz się więcej "

Aby ukończyć proces oczyszczania, wykonaj kroki 2 i 3 na serwerze lokalnym, na którym jest uruchomiony program Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>Opcja B: Wyłączanie przy użyciu programu PowerShell

Na serwerze lokalnym, na którym jest uruchomiony program Azure AD Connect, wykonaj następujące czynności:

1. Najpierw pobierz i zainstaluj program [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj bezproblemowe moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.
4. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj `New-AzureADSSOAuthenticationContext`polecenie. To polecenie powinno umożliwić podręczne wprowadzenie poświadczeń administratora globalnego dzierżawy.
5. Wywołanie `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>Wyłączenie bezproblemowego logowania jednokrotnego przy użyciu programu PowerShell nie spowoduje zmiany stanu w Azure AD Connect. Bezproblemowe logowanie jednokrotne zostanie wyświetlone jako włączone na stronie **zmiany logowania użytkownika** .

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 2. Pobierz listę lasów usługi AD, w których włączono bezproblemowe logowanie jednokrotne

Wykonaj zadania od 1 do 4 poniżej, jeśli wyłączono bezproblemowe logowanie jednokrotne przy użyciu Azure AD Connect. Jeśli wyłączysz bezproblemowe logowanie jednokrotne przy użyciu programu PowerShell, przejdź do kolejnego zadania 5 poniżej.

1. Najpierw pobierz i zainstaluj program [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj bezproblemowe moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.
4. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj `New-AzureADSSOAuthenticationContext`polecenie. To polecenie powinno umożliwić podręczne wprowadzenie poświadczeń administratora globalnego dzierżawy.
5. Wywołanie `Get-AzureADSSOStatus | ConvertFrom-Json`. To polecenie umożliwia wyświetlenie listy lasów usługi AD (Zobacz listę "domeny"), na której włączono tę funkcję.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Krok 3. Ręcznie usuń `AZUREADSSOACCT` konto komputera z poszczególnych lasów usługi AD, które są widoczne na liście.

## <a name="next-steps"></a>Następne kroki

- [**Szybki Start**](how-to-connect-sso-quick-start.md) — Uzyskaj i korzystaj z bezproblemowego logowania jednokrotnego usługi Azure AD.
- [**Szczegółowee techniczne głębokie**](how-to-connect-sso-how-it-works.md) — zrozumienie, jak działa ta funkcja.
- [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — Dowiedz się, jak rozwiązywać typowe problemy z funkcją.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania nowych żądań funkcji.
