---
title: Azure Active Directory kody błędów uwierzytelniania i autoryzacji | Microsoft Docs
description: Dowiedz się więcej o kodach błędów AADSTS zwracanych z usługi tokenu zabezpieczeń (STS) usługi Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/13/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e62a0c1363acfedc1f059ac0e56fce0df68298f8
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532360"
---
# <a name="authentication-and-authorization-error-codes"></a>Kody błędów uwierzytelniania i autoryzacji

Szukasz informacji o kodach błędów AADSTS, które są zwracane z usługi tokenu zabezpieczającego (STS) Azure Active Directory (Azure AD)? Przeczytaj ten dokument, aby znaleźć AADSTS opisów błędów, poprawek i niektórych sugerowanych obejść.

> [!NOTE]
> Te informacje mają charakter wstępny i mogą ulec zmianie. Masz pytanie lub nie możesz znaleźć tego, czego szukasz? Utwórz problem w usłudze GitHub lub zapoznaj się z [pomocą techniczną i opcjami pomocy dla deweloperów](active-directory-develop-help-support.md) , aby dowiedzieć się więcej na temat innych sposobów uzyskiwania pomocy i obsługi technicznej.
>
> Ta dokumentacja jest dostępna dla wskazówek dotyczących deweloperów i administratorów, ale nigdy nie powinna być używana przez klienta. Kody błędów mogą ulec zmianie w dowolnym momencie w celu zapewnienia bardziej szczegółowych komunikatów o błędach, które są przeznaczone do ułatwienia deweloperom podczas kompilowania aplikacji. Aplikacje, które są zależne od tekstu lub numerów kodów błędów, zostaną złamane z upływem czasu.  

## <a name="aadsts-error-codes"></a>Kody błędów AADSTS

| Błąd | Opis |
|---|---|
| AADSTS16000 | SelectUserAccount — jest to przerwanie zgłoszone przez usługę Azure AD, które powoduje, że użytkownik może wybrać spośród wielu prawidłowych sesji logowania jednokrotnego. Ten błąd jest dość powszechny i może zostać zwrócony do aplikacji, `prompt=none` jeśli jest określony. |
| AADSTS16001 | UserAccountSelectionInvalid — ten błąd zostanie wyświetlony, jeśli użytkownik kliknie kafelek, który został odrzucony przez sesję Selecting Logic. Po wyzwoleniu ten błąd umożliwia użytkownikowi odzyskanie przez pobranie ze zaktualizowanej listy kafelków/sesji lub wybranie innego konta. Ten błąd może wystąpić ze względu na defekt kodu lub sytuację wyścigu. |
| AADSTS16002 | AppSessionSelectionInvalid — nie spełniono wymagania dotyczącego identyfikatora SID określonego przez aplikację.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant — wskazuje, że użytkownik nie został jawnie dodany do dzierżawy. |
| AADSTS17003 | CredentialKeyProvisioningFailed — usługa Azure AD nie może udostępnić klucza użytkownika. |
| AADSTS20001 | WsFedSignInResponseError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS20012 | WsFedMessageInvalid — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS20033 | FedMetadataInvalidTenantName — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40008 | OAuth2IdPUnretryableServerError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40010 | OAuth2IdPRetryableServerError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS50000 | TokenIssuanceError — wystąpił problem z usługą logowania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby rozwiązać ten problem. |
| AADSTS50001 | InvalidResource — zasób jest wyłączony lub nie istnieje. Sprawdź kod aplikacji, aby upewnić się, że został określony dokładny adres URL zasobu dla zasobu, do którego próbujesz uzyskać dostęp.  |
| AADSTS50002 | NotAllowedTenant — logowanie nie powiodło się z powodu ograniczonego dostępu do serwera proxy w dzierżawie. Jeśli jesteś własnymi zasadami dzierżawy, możesz zmienić ustawienia dzierżawy z ograniczeniami, aby rozwiązać ten problem. |
| AADSTS50003 | MissingSigningKey — logowanie nie powiodło się z powodu braku klucza podpisywania lub certyfikatu. Może to być spowodowane tym, że w aplikacji nie został skonfigurowany klucz podpisywania. Sprawdź rozwiązania opisane tutaj: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Jeśli nadal widzisz problemy, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji. |
| AADSTS50005 | DevicePolicyError — użytkownik próbował zalogować się na urządzeniu z platformy, która nie jest obecnie obsługiwana przez zasady dostępu warunkowego. |
| AADSTS50006 | InvalidSignature — weryfikacja podpisu nie powiodła się z powodu nieprawidłowej sygnatury. |
| AADSTS50007 | PartnerEncryptionCertificateMissing — certyfikat szyfrowania partnera nie został znaleziony dla tej aplikacji. Aby uzyskać ten problem [, Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) w firmie Microsoft. |
| AADSTS50008 | InvalidSamlToken — brak potwierdzenia SAML lub został on nieprawidłowo skonfigurowany w tokenie. Skontaktuj się z dostawcą federacji. |
| AADSTS50010 | AudienceUriValidationFailed — weryfikacja identyfikatora URI odbiorców dla aplikacji nie powiodła się, ponieważ nie skonfigurowano żadnych odbiorców tokenu. |
| AADSTS50011 | InvalidReplyTo — brak adresu odpowiedzi, jest on niepoprawnie skonfigurowany lub nie jest zgodny z adresami odpowiedzi skonfigurowanymi dla aplikacji.  Ponieważ w ramach rozwiązania Zadbaj o dodanie tego brakującego adresu zwrotnego do aplikacji Azure Active Directory lub skontaktuj się z osobą z uprawnieniami do zarządzania aplikacją w Active Directory zrób to za Ciebie.|
| AADSTS50012 | AuthenticationFailed — uwierzytelnianie nie powiodło się z jednego z następujących powodów:<ul><li>Nazwa podmiotu certyfikatu podpisywania nie jest autoryzowana</li><li>Nie znaleziono zgodnych zasad zaufanych urzędów dla autoryzowanej nazwy podmiotu</li><li>Łańcuch certyfikatów jest nieprawidłowy</li><li>Certyfikat podpisywania jest nieprawidłowy</li><li>Zasady nie są skonfigurowane w dzierżawie</li><li>Odcisk palca certyfikatu podpisywania nie jest autoryzowany</li><li>Potwierdzenie klienta zawiera nieprawidłową sygnaturę</li></ul> |
| AADSTS50013 | InvalidAssertion-Assertion jest nieprawidłowy z powodu różnych powodów — Wystawca tokenu nie jest zgodny z wersją interfejsu API w ramach prawidłowego zakresu czasu — wygasły token odświeżania w potwierdzeniu nie jest podstawowym tokenem odświeżania. |
| AADSTS50014 | GuestUserInPendingState — wykup użytkownika jest w stanie oczekiwania. Konto użytkownika-gościa nie jest jeszcze w pełni utworzone. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState — użytkownik wymaga zgody na zgodę grupy wiekowej. |
| AADSTS50017 | CertificateValidationFailed — weryfikacja certyfikacji nie powiodła się, przyczyny z następujących powodów:<ul><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Nie można odnaleźć oczekiwanego elementu CrlSegment</li><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Punkt dystrybucji różnicowej listy CRL jest skonfigurowany bez odpowiedniego punktu dystrybucji listy CRL</li><li>Nie można pobrać prawidłowych segmentów listy CRL z powodu problemu z przekroczeniem limitu czasu</li><li>Nie można pobrać listy CRL</li></ul>Skontaktuj się z administratorem dzierżawy. |
| AADSTS50020 | UserUnauthorized — użytkownicy nie mają autoryzacji do wywołania tego punktu końcowego. |
| AADSTS50027 | InvalidJwtToken — nieprawidłowy token JWT z następujących powodów:<ul><li>nie zawiera oświadczenia nonce, oświadczenia sub</li><li>niezgodność identyfikatora podmiotu</li><li>zduplikowane oświadczenie w oświadczeniach idToken</li><li>nieoczekiwany wystawca</li><li>nieoczekiwani odbiorcy</li><li>nie mieści się w prawidłowym zakresie czasu </li><li>format tokenu nie jest prawidłowy</li><li>weryfikacja tokenu zewnętrznego identyfikatora od wystawcy nie powiodła się.</li></ul> |
| AADSTS50029 | Nieprawidłowy identyfikator URI — nazwa domeny zawiera nieprawidłowe znaki. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50032 | WeakRsaKey-wskazuje błędnego użytkownika próbującego użyć słabego klucza RSA. |
| AADSTS50033 | RetryableError-wskazuje przejściowy błąd, który nie jest związany z operacjami bazy danych. |
| AADSTS50034 | UserAccountNotFound — aby zalogować się do tej aplikacji, należy dodać konto do katalogu. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt — w zasadzie brakuje soli wymaganej do wygenerowania identyfikatora parowania. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer — niezgodność podmiotu wystawcy w potwierdzeniu klienta. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50049 | NoSuchInstanceForDiscovery — nieznane lub nieprawidłowe wystąpienie. |
| AADSTS50050 | MalformedDiscoveryRequest — żądanie jest źle sformułowane. |
| AADSTS50053 | IdsLocked — konto jest zablokowane, ponieważ użytkownik próbował zalogować się zbyt wiele razy przy użyciu niepoprawnego identyfikatora użytkownika lub hasła. |
| AADSTS50055 | InvalidPasswordExpiredPassword — hasło wygasło. |
| AADSTS50056 | Nieprawidłowe lub puste hasło — hasło nie istnieje w sklepie dla tego użytkownika. |
| AADSTS50057 | UserDisabled — konto użytkownika jest wyłączone. Konto zostało wyłączone przez administratora. |
| AADSTS50058 | UserInformationNotProvided — oznacza to, że użytkownik nie jest zalogowany. Jest to typowy błąd oczekiwany w sytuacji, gdy użytkownik jest nieuwierzytelniony i jeszcze nie jest zalogowany.</br>Jeśli ten błąd jest zalecany w kontekście logowania jednokrotnego, w którym użytkownik zalogował się wcześniej, oznacza to, że nie można odnaleźć sesji logowania jednokrotnego lub jest ona nieprawidłowa.</br>Ten błąd może zostać zwrócony do aplikacji, jeśli jest wyświetlany monit = none. |
| AADSTS50059 | Nie znaleziono informacji identyfikacyjnych MissingTenantRealmAndNoUserInformationProvided-dzierżawców w żądaniu ani IMPLIKOWANYCH przez żadne podane poświadczenia. Użytkownik może skontaktować się z administratorem dzierżawy, aby pomóc w rozwiązaniu problemu. |
| AADSTS50061 | SignoutInvalidRequest — żądanie wylogowania jest nieprawidłowe. |
| AADSTS50064 | CredentialAuthenticationError — weryfikacja poświadczeń dla nazwy użytkownika lub hasła zakończyła się niepowodzeniem. |
| AADSTS50068 | SignoutInitiatorNotParticipant-wylogowaniu nie powiodło się. Aplikacja, która zainicjowała wylogowaniu, nie jest uczestnikiem bieżącej sesji. |
| AADSTS50070 | SignoutUnknownSessionIdentifier-wylogowaniu nie powiodło się. Żądanie wylogowaniu określiło identyfikator nazwy, który nie jest zgodny z istniejącymi sesjami. |
| AADSTS50071 | SignoutMessageExpired — żądanie wylogowania wygasło. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt — użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (Interactive). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt — jest wymagane silne uwierzytelnianie, a użytkownik nie przeszedł z wyzwania usługi MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired — ze względu na zmianę konfiguracji podjętą przez administratora lub przeniesiono ją do nowej lokalizacji, użytkownik musi użyć uwierzytelniania wieloskładnikowego w celu uzyskania dostępu do zasobu. Ponów próbę, używając nowego autoryzowanego żądania dla zasobu. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired — z powodu zmiany konfiguracji wprowadzonej przez administratora lub przenoszonej przez użytkownika do nowej lokalizacji użytkownik musi korzystać z uwierzytelniania wieloskładnikowego. |
| AADSTS50085 | Token odświeżania wymaga danych logowania dostawy tożsamości z serwisów społecznościowych. Użytkownik musi zalogować się ponownie przy użyciu nazwy użytkownika i hasła |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError — usługa jest tymczasowo niedostępna. Spróbuj ponownie. |
| AADSTS50089 | Token uwierzytelniania wygasł — uwierzytelnianie nie powiodło się. Użytkownik spróbuje ponownie zalogować się przy użyciu nazwy użytkownika i hasła. |
| AADSTS50097 | DeviceAuthenticationRequired — wymagane jest uwierzytelnianie urządzenia. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized — podpis JWT jest nieprawidłowy. |
| AADSTS50105 | EntitlementGrantsNotFound — zalogowany użytkownik nie jest przypisany do roli dla zalogowanej aplikacji. Przypisz użytkownika do aplikacji. Aby uzyskać więcej informacji[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role):. |
| AADSTS50107 | InvalidRealmUri — żądany obiekt Federacji nie istnieje. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat — wystaw z nagłówkiem JWT. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter — transformacja oświadczeń zawiera nieprawidłowy parametr wejściowy. Skontaktuj się z administratorem dzierżawy, aby zaktualizować zasady. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt — logowanie zostało przerwane z powodu resetowania hasła lub wpisu rejestracji hasła. |
| AADSTS50126 | InvalidUserNameOrPassword — błąd podczas walidacji poświadczeń z powodu nieprawidłowej nazwy użytkownika lub hasła. |
| AADSTS50127 | BrokerAppNotInstalled — użytkownik musi zainstalować aplikację brokera, aby uzyskać dostęp do tej zawartości. |
| AADSTS50128 | Nieprawidłowa nazwa domeny — nie znaleziono informacji identyfikacyjnych dzierżawy w żądaniu lub IMPLIKOWANYCH przez żadne podane poświadczenia. |
| AADSTS50129 | Do zarejestrowania urządzenia wymagane jest dołączenie do DeviceIsNotWorkplaceJoined. |
| AADSTS50131 | ConditionalAccessFailed-wskazuje różne błędy dostępu warunkowego, takie jak zły stan urządzenia z systemem Windows, żądanie zablokowane z powodu podejrzanych działań, zasad dostępu lub decyzji dotyczących zasad zabezpieczeń. |
| AADSTS50132 | SsoArtifactInvalidOrExpired — sesja nie jest prawidłowa z powodu wygaśnięcia hasła lub ostatniej zmiany hasła. |
| AADSTS50133 | SsoArtifactRevoked — sesja nie jest prawidłowa z powodu wygaśnięcia hasła lub ostatniej zmiany hasła. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter — niewłaściwe centrum danych. Aby autoryzować żądanie zainicjowane przez aplikację w przepływie urządzeń OAuth 2,0, osoba autoryzowana musi znajdować się w tym samym centrum danych, w którym znajduje się oryginalne żądanie. |
| AADSTS50135 | PasswordChangeCompromisedPassword — zmiana hasła jest wymagana ze względu na ryzyko związane z kontem. |
| AADSTS50136 | RedirectMsaSessionToApp — wykryto pojedynczą sesję MSA. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken — sesja jest nieprawidłowa z powodu braku zewnętrznego tokenu odświeżania. |
| AADSTS50140 | KmsiInterrupt — ten błąd wystąpił z powodu przerwania "nie wylogowuj mnie" podczas logowania użytkownika. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji. |
| AADSTS50143 | Niezgodność sesji — sesja jest nieprawidłowa, ponieważ dzierżawca użytkownika nie jest zgodny z wskazówką domeny z powodu innego zasobu. Aby uzyskać więcej informacji,  [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) o identyfikatorze korelacji, identyfikatorze żądania i kodzie błędu. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword hasło użytkownika Active Directory wygasło. Wygeneruj nowe hasło dla użytkownika lub użyj narzędzia resetowania samoobsługowego do zresetowania hasła. |
| AADSTS50146 | MissingCustomSigningKey — ta aplikacja jest wymagana do skonfigurowania klucza podpisywania specyficznego dla aplikacji. Albo nie została skonfigurowana przy jego użyciu, albo klucz wygasł lub nie jest jeszcze ważny. |
| AADSTS50147 | MissingCodeChallenge — rozmiar parametru wezwania kodu jest nieprawidłowy. |
| AADSTS50155 | DeviceAuthenticationFailed — uwierzytelnianie urządzenia nie powiodło się dla tego użytkownika. |
| AADSTS50158 | ExternalSecurityChallenge — zewnętrzny test zabezpieczeń nie został spełniony. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration — oświadczenia wysyłane przez dostawcę zewnętrznego nie są wystarczające lub brakujące oświadczenie zażądało dostawcy zewnętrznemu. |
| AADSTS50166 | ExternalClaimsProviderThrottled — nie można wysłać żądania do dostawcy oświadczeń. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired — klient może uzyskać token logowania jednokrotnego za pomocą rozszerzenia kont systemu Windows 10, ale nie znaleziono tokenu w żądaniu lub dostarczony token wygasł. |
| AADSTS50169 | InvalidRequestBadRealm — obszar nie jest skonfigurowanym obszarem przestrzeni nazw bieżącej usługi. |
| AADSTS50170 | MissingExternalClaimsProviderMapping — Brak mapowania formantów zewnętrznych. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers — zewnętrzny test nie jest obsługiwany w przypadku użytkowników przekazujących. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers — kontrola sesji nie jest obsługiwana w przypadku użytkowników przekazujących. |
| AADSTS50180 | Wymagana jest WindowsIntegratedAuthMissing zintegrowane uwierzytelnianie systemu Windows. Włącz dzierżawę dla bezproblemowego logowania jednokrotnego. |
| AADSTS50187 | DeviceInformationNotProvided — usługa nie może wykonać uwierzytelniania urządzenia. |
| AADSTS51000 | RequiredFeatureNotEnabled — funkcja jest wyłączona. |
| AADSTS51001 | DomainHintMustbePresent — Wskazówka domeny musi być obecna przy użyciu lokalnego identyfikatora zabezpieczeń lub lokalnej nazwy UPN. |
| AADSTS51004 | UserAccountNotInDirectory — konto użytkownika nie istnieje w katalogu. |
| AADSTS51005 | TemporaryRedirect — odpowiednik stanu HTTP 307, który wskazuje, że żądane informacje znajdują się w identyfikatorze URI określonym w nagłówku lokalizacji. Po otrzymaniu tego stanu postępuj zgodnie z nagłówkiem lokalizacji skojarzonym z odpowiedzią. Gdy oryginalna Metoda żądania została OPUBLIKOWANa, przekierowane żądanie będzie również używać metody POST. |
| AADSTS51006 | Wymagana jest ForceReauthDueToInsufficientAuth zintegrowane uwierzytelnianie systemu Windows. Użytkownik zalogował się przy użyciu tokenu sesji, w którym brakuje zintegrowanego uwierzytelniania systemu Windows. Poproś użytkownika o ponowne zalogowanie. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn — użytkownik nie podał zgody na dostęp do zasobów serwisu LinkedIn. |
| AADSTS53000 | Zasady dostępu warunkowego DeviceNotCompliant wymagają zgodnego urządzenia, a urządzenie nie jest zgodne. Użytkownik musi zarejestrować urządzenie przy użyciu zatwierdzonego dostawcy MDM, takiego jak usługa Intune. |
| AADSTS53001 | DeviceNotDomainJoined — zasady dostępu warunkowego wymagają urządzenia przyłączonego do domeny, a urządzenie nie jest przyłączone do domeny. Użytkownik musi korzystać z urządzenia przyłączonego do domeny. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp — używana aplikacja nie jest zatwierdzoną aplikacją dla dostępu warunkowego. Użytkownik musi użyć jednej z aplikacji z listy zatwierdzonych aplikacji do użycia w celu uzyskania dostępu. |
| AADSTS53003 | BlockedByConditionalAccess — dostęp został zablokowany przez zasady dostępu warunkowego. Zasady dostępu nie zezwalają na wystawianie tokenów. |
| AADSTS53004 | ProofUpBlockedDueToRisk — użytkownik musi ukończyć proces rejestracji w usłudze MFA, aby uzyskać dostęp do tej zawartości. Użytkownik powinien zarejestrować się w celu uwierzytelniania wieloskładnikowego. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist — użytkownik lub administrator nie wyraził zgody na używanie aplikacji z IDENTYFIKATORem X. Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu. |
| AADSTS65004 | UserDeclinedConsent — użytkownik odrzucił zgodę na dostęp do aplikacji. Użytkownik musi ponowić próbę zalogowania się i wyrazić zgodę na aplikację|
| AADSTS65005 | MisconfiguredApplication — lista dostępu do zasobów wymagana przez aplikację nie zawiera aplikacji odnajdowanych przez zasób lub aplikacja kliencka zażądała dostępu do zasobu, który nie został określony w wymaganej liście dostępu do zasobów lub usługa grafu zwróciła zły nie znaleziono żądania lub zasobu. Jeśli aplikacja obsługuje protokół SAML, być może skonfigurowano aplikację z nieprawidłowym identyfikatorem (jednostką). Wypróbuj rozwiązania dla języka SAML, korzystając z linku poniżej: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant — uwierzytelnianie nie powiodło się. Token odświeżania jest nieprawidłowy. Błąd może być spowodowany następującymi przyczynami:<ul><li>Nagłówek powiązania tokenu jest pusty</li><li>Wartość skrótu powiązania tokenu nie jest zgodna</li></ul> |
| AADSTS70001 | UnauthorizedClient — aplikacja jest wyłączona. |
| AADSTS70002 | InvalidClient — błąd podczas walidacji poświadczeń. Określony client_secret nie jest zgodny z oczekiwaną wartością tego klienta. Popraw client_secret i spróbuj ponownie. Aby uzyskać więcej informacji, zobacz [Używanie kodu autoryzacji do żądania tokenu dostępu](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType — aplikacja zwróciła nieobsługiwany typ dotacji. |
| AADSTS70004 | InvalidRedirectUri — aplikacja zwróciła nieprawidłowy identyfikator URI przekierowania. Adres przekierowania określony przez klienta nie pasuje do żadnych skonfigurowanych adresów ani żadnych adresów na liście zatwierdzonych dostawców OIDC. |
| AADSTS70005 | UnsupportedResponseType — aplikacja zwróciła nieobsługiwany typ odpowiedzi z następujących powodów:<ul><li>typ odpowiedzi "token" nie jest włączony dla aplikacji</li><li>typ odpowiedzi „id_token” wymaga zakresu OpenID — zawiera nieobsługiwaną wartość parametru OAuth w zakodowanym kanale wctx</li></ul> |
| AADSTS70007 | UnsupportedResponseMode — aplikacja zwróciła nieobsługiwaną wartość dla `response_mode` żądania tokenu.  |
| AADSTS70008 | ExpiredOrRevokedGrant — token odświeżania wygasł z powodu braku aktywności. Token został wystawiony w dniu XXX i był nieaktywny przez określony czas. |
| AADSTS70011 | InvalidScope — zakres żądany przez aplikację jest nieprawidłowy. |
| AADSTS70012 | MsaServerError — Wystąpił błąd serwera podczas uwierzytelniania użytkownika MSA (konsumenta). Spróbuj ponownie. Jeśli to się nie powiedzie, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | Błąd przepływu urządzenia AuthorizationPending-OAuth 2,0. Autoryzacja jest w stanie oczekiwania. Urządzenie ponowi próbę sondowania żądania. |
| AADSTS70018 | BadVerificationCode — nieprawidłowy kod weryfikacyjny ze względu na to, że użytkownik pisze nieprawidłowy kod użytkownika dla przepływu kodu urządzenia. Autoryzacja nie jest zatwierdzona. |
| AADSTS70019 | CodeExpired — upłynął kod weryfikacyjny. Ponowienie próby logowania przez użytkownika. |
| AADSTS75001 | BindingSerializationError — Wystąpił błąd podczas wiązania komunikatu SAML. |
| AADSTS75003 | UnsupportedBindingError — aplikacja zwróciła błąd związany z nieobsługiwanym powiązaniem (nie można wysłać odpowiedzi protokołu SAML za pośrednictwem powiązań innych niż POST protokołu HTTP). |
| AADSTS75005 | Saml2MessageInvalid — usługa Azure AD nie obsługuje żądania SAML wysyłanego przez aplikację na potrzeby logowania jednokrotnego. |
| AADSTS75008 | RequestDeniedError — żądanie od aplikacji zostało odrzucone, ponieważ żądanie SAML miało nieoczekiwane miejsce docelowe. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims — Metoda uwierzytelniania, za pomocą której użytkownik uwierzytelniony przy użyciu usługi nie jest zgodna z żądaną metodą uwierzytelniania. |
| AADSTS75016 | Żądanie uwierzytelniania Saml2AuthenticationRequestInvalidNameIDPolicy-SAML2 ma nieprawidłowe NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable — Agent uwierzytelniania nie może nawiązać połączenia z Active Directory. Upewnij się, że serwery agentów są członkami tego samego lasu usługi AD co użytkownicy, których hasła muszą zostać sprawdzone i mogą łączyć się z Active Directory. |
| AADSTS80002 | Przekroczono limit czasu żądania weryfikacji hasła OnPremisePasswordValidatorRequestTimedout. Upewnij się, że Active Directory jest dostępny i odpowiada na żądania od agentów. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException — wystąpił nieznany błąd podczas przetwarzania odpowiedzi z agenta uwierzytelniania. Ponów żądanie. Jeśli problem nie zniknie, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) , aby uzyskać więcej szczegółowych informacji o błędzie. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem — Agent uwierzytelniania nie może zweryfikować hasła użytkownika. Sprawdź dzienniki agenta, aby uzyskać więcej informacji i sprawdź, czy Active Directory działa zgodnie z oczekiwaniami. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException — Agent uwierzytelniania nie może odszyfrować hasła. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours — użytkownicy próbujący się zalogować poza dozwolonymi godzinami (określono w usłudze AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew — próba uwierzytelnienia nie mogła zostać zakończona z powodu przesunięcia czasu między komputerem, na którym jest uruchomiony Agent uwierzytelniania i AD. Rozwiązywanie problemów z synchronizacją czasu. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated — próba uwierzytelniania przy użyciu protokołu Kerberos nie powiodła się. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported — pakiet uwierzytelniania nie jest obsługiwany. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader — nie znaleziono nagłówka autoryzacji. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn — dzierżawa nie jest włączona na potrzeby bezproblemowego logowania jednokrotnego. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat — nie można zweryfikować biletu Kerberos użytkownika. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid-bezproblemowe logowanie jednokrotne nie powiodło się, ponieważ bilet protokołu Kerberos użytkownika wygasł lub jest nieprawidłowy. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed — nie można znaleźć obiektu użytkownika na podstawie informacji w biletach protokołu Kerberos użytkownika. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn — użytkownik próbujący zalogować się do usługi Azure AD różni się od użytkownika zalogowanego na urządzeniu. |
| AADSTS90002 | InvalidTenantName — nie można odnaleźć nazwy dzierżawy w magazynie danych. Upewnij się, że masz prawidłowy identyfikator dzierżawy. |
| AADSTS90004 | InvalidRequestFormat — żądanie nie jest prawidłowo sformatowane. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements — nie można zakończyć żądania. Żądanie jest nieprawidłowe, ponieważ nie można jednocześnie używać identyfikatora i wskazówki logowania.  |
| AADSTS90006 | ExternalServerRetryableError — usługa jest tymczasowo niedostępna.|
| AADSTS90007 | InvalidSessionId — Nieprawidłowe żądanie. Nie można przeanalizować identyfikatora przesłanej sesji. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission — użytkownik lub administrator nie wyraził zgody na korzystanie z aplikacji. Co najmniej aplikacja wymaga dostępu do usługi Azure AD, określając uprawnienie Logowanie i odczyt profilu użytkownika. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier — aplikacja żąda tokenu dla siebie. Ten scenariusz jest obsługiwany tylko wtedy, gdy określony zasób używa identyfikatora aplikacji opartego na identyfikatorze GUID. |
| AADSTS90010 | NotSupported — nie można utworzyć algorytmu. |
| AADSTS90012 | RequestTimeout — upłynął limit czasu żądania. |
| AADSTS90013 | InvalidUserInput — dane wejściowe użytkownika są nieprawidłowe. |
| AADSTS90014 | MissingRequiredField — ten kod błędu może pojawić się w różnych przypadkach, gdy w poświadczeniu nie ma oczekiwanego pola. |
| AADSTS90015 | QueryStringTooLong — ciąg zapytania jest zbyt długi. |
| AADSTS90016 | MissingRequiredClaim — token dostępu jest nieprawidłowy. Brak wymaganego żądania. |
| AADSTS90019 | MissingTenantRealm — usługa Azure AD nie mogła określić identyfikatora dzierżawy z żądania. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat — format nazwy głównej jest nieprawidłowy lub nie jest zgodny z oczekiwanym `name[/host][@realm]` formatem. Nazwa główna jest wymagana, Host i obszar są opcjonalne i mogą być ustawione na wartość null. |
| AADSTS90023 | InvalidRequest — żądanie usługi uwierzytelniania jest nieprawidłowe. |
| AADSTS90024 | RequestBudgetExceededError — Wystąpił błąd przejściowy. Spróbuj ponownie. |
| AADSTS90033 | MsodsServiceUnavailable — usługa Katalog online firmy Microsoft (MSODS) nie jest dostępna. |
| AADSTS90036 | MsodsServiceUnretryableFailure — wystąpił nieoczekiwany błąd, którego nie można ponownie ponowić z usługi WCF hostowanej przez MSODS. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu. |
| AADSTS90038 | NationalCloudTenantRedirection — określona dzierżawa "Y" należy do chmury krajowej "X". Bieżące wystąpienie w chmurze "Z" nie sfederować z X. Zwracany jest błąd przekierowania w chmurze. |
| AADSTS90043 | NationalCloudAuthCodeRedirection — funkcja jest wyłączona. |
| AADSTS90051 | InvalidNationalCloudId — identyfikator chmury krajowej zawiera nieprawidłowy identyfikator chmury. |
| AADSTS90055 | TenantThrottlingError — zbyt wiele żądań przychodzących. Ten wyjątek jest zgłaszany dla zablokowanych dzierżawców. |
| AADSTS90056 | BadResourceRequest — aby zrealizować kod dla tokenu dostępu, aplikacja powinna wysłać żądanie post do `/token` punktu końcowego. Ponadto przed tym należy podać kod autoryzacji i wysłać go w żądaniu post do `/token` punktu końcowego. Zapoznaj się z tym artykułem, aby uzyskać omówienie przepływu kodu autoryzacji [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)OAuth 2,0:. Kierowanie użytkownika do `/authorize` punktu końcowego, który zwróci authorization_code. Po opublikowaniu żądania do `/token` punktu końcowego użytkownik otrzymuje token dostępu. Zaloguj się do Azure Portal i sprawdź **punkty końcowe > rejestracje aplikacji** , aby upewnić się, że dwa punkty końcowe zostały prawidłowo skonfigurowane. |
| AADSTS90072 | PassThroughUserMfaError — konto zewnętrzne, za pomocą którego loguje się użytkownik, nie istnieje w dzierżawie, do której się zarejestrował; Dlatego użytkownik nie może spełnić wymagań usługi MFA dla dzierżawy. Najpierw należy dodać konto jako użytkownika zewnętrznego w dzierżawie. Wyloguj się i zaloguj się przy użyciu innego konta użytkownika usługi Azure AD. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid — Wystąpił błąd, gdy usługa podjęła próbę przetworzenia komunikatu protokołu WS-Federation. Komunikat jest nieprawidłowy. |
| AADSTS90082 | OrgIdWsFederationNotSupported — wybrane zasady uwierzytelniania dla żądania nie są obecnie obsługiwane. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed — konta gościa nie są dozwolone w tej witrynie. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed — usługa nie może wystawić tokenu, ponieważ obiekt firmy nie został jeszcze zainicjowany. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired — token użytkownika DA ważność. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed — Wystąpił błąd podczas tworzenia komunikatu protokołu WS-Federation z identyfikatora URI. |
| AADSTS90090 | GraphRetryableError — usługa jest tymczasowo niedostępna. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized-Graph zwrócił z niedozwolonym kodem błędu dla żądania. |
| AADSTS90094 | AdminConsentRequired — wymagana jest zgoda administratora. |
| AADSTS90100 | InvalidRequestParameter — parametr jest pusty lub nieprawidłowy. |
| AADSTS901002 | AADSTS901002: Parametr żądania "Resource" nie jest obsługiwany. |
| AADSTS90101 | InvalidEmailAddress — podane dane nie są prawidłowym adresem e-mail. Adres e-mail musi mieć format `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter — wartość musi być prawidłowym bezwzględnym identyfikatorem URI. |
| AADSTS90107 | InvalidXml — żądanie jest nieprawidłowe. Upewnij się, że dane nie zawierają nieprawidłowych znaków.|
| AADSTS90114 | InvalidExpiryDate — sygnatura czasowa wygaśnięcia tokenu zbiorczego spowoduje wystawienie wygasłego tokenu. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode — kod użytkownika ma wartość null lub jest pusty.|
| AADSTS90120 | InvalidDeviceFlowRequest — żądanie zostało już autoryzowane lub odrzucone. |
| AADSTS90121 | InvalidEmptyRequest — nieprawidłowe puste żądanie.|
| AADSTS90123 | IdentityProviderAccessDenied — nie można wystawić tokenu, ponieważ dostawca wystawiania dla tożsamości lub żądania odrzucił żądanie. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported — zasób nie jest obsługiwany w `/common` punktach końcowych lub. `/consumers` Zamiast tego `/organizations` Użyj punktu końcowego określonego dla dzierżawy. |
| AADSTS90125 | DebugModeEnrollTenantNotFound — użytkownik nie jest w systemie. Upewnij się, że nazwa użytkownika została wprowadzona prawidłowo. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred — typ użytkownika nie jest obsługiwany w tym punkcie końcowym. System nie może wywnioskować dzierżawy użytkownika z nazwy użytkownika. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported — aplikacja nie jest obsługiwana w przypadku `/common` punktów końcowych lub. `/consumers` Zamiast tego `/organizations` Użyj punktu końcowego określonego dla dzierżawy. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated — Wystąpił błąd niepowodujący ponowienia próby.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound — podmiot zabezpieczeń użytkownika nie ma skonfigurowanego klucza identyfikatora NGC. |
| AADSTS130005 | Weryfikowanie sygnatury klucza NgcInvalidSignature-NGC nie powiodło się.|
| AADSTS130006 | NgcTransportKeyNotFound — klucz transportu NGC nie jest skonfigurowany na urządzeniu. |
| AADSTS130007 | NgcDeviceIsDisabled — urządzenie jest wyłączone. |
| AADSTS130008 | NgcDeviceIsNotFound — nie znaleziono urządzenia, do którego odwołuje się klucz NGC. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce — nie podano identyfikatora żądania. |
| AADSTS140001 | InvalidSessionKey — klucz sesji jest nieprawidłowy.|
| AADSTS165900 | InvalidApiRequest — Nieprawidłowe żądanie. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion — wersja programu Chrome WebView nie jest obsługiwana. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource — zasób nie jest skonfigurowany do akceptowania tokenów tylko dla urządzeń. |
| AADSTS240001 | BulkAADJTokenUnauthorized — użytkownik nie ma uprawnień do rejestrowania urządzeń w usłudze Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing — id_token nie może być używana jako `urn:ietf:params:oauth:grant-type:jwt-bearer` Grant.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy — Administrator dzierżawy skonfigurował zasadę zabezpieczeń, która blokuje to żądanie. Sprawdź zasady zabezpieczeń zdefiniowane na poziomie dzierżawy, aby określić, czy żądanie spełnia wymagania zasad. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest — aplikacja nie została znaleziona w katalogu/dzierżawie. Taka sytuacja może wystąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub nie została wysłana przez żadnego użytkownika w dzierżawie. Być może nieprawidłowo skonfigurowano wartość identyfikatora dla aplikacji lub wysłano żądanie uwierzytelnienia do niewłaściwej dzierżawy. |
| AADSTS700020 | InteractionRequired — udzielanie dostępu wymaga interakcji. |
| AADSTS700022 | InvalidMultipleResourcesScope — podana wartość zakresu parametru wejściowego jest nieprawidłowa, ponieważ zawiera więcej niż jeden zasób. |
| AADSTS700023 | InvalidResourcelessScope — podana wartość dla zakresu parametru wejściowego jest nieprawidłowa podczas żądania tokenu dostępu. |
| AADSTS1000000 | UserNotBoundError — interfejs API bind wymaga, aby użytkownik usługi Azure AD mógł także uwierzytelniać się przy użyciu zewnętrznego dostawcy tożsamości, który jeszcze nie wystąpił. |
| AADSTS1000002 | BindCompleteInterruptError — powiązanie zostało wykonane pomyślnie, ale użytkownik musi zostać poinformowany. |

## <a name="next-steps"></a>Następne kroki

* Masz pytanie lub nie możesz znaleźć tego, czego szukasz? Utwórz problem w usłudze GitHub lub zapoznaj się z [pomocą techniczną i opcjami pomocy dla deweloperów](active-directory-develop-help-support.md) , aby dowiedzieć się więcej na temat innych sposobów uzyskiwania pomocy i obsługi technicznej.
