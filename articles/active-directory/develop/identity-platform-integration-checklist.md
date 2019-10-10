---
title: Najlepsze rozwiązania dotyczące platformy tożsamości firmy Microsoft | Azure
description: Zapoznaj się z najlepszymi rozwiązaniami, zaleceniami i typowymi informacjami dotyczącymi integracji z platformą tożsamości firmy Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f7e9b738a55248678a207f0b298ef65e6c2761a4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240150"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Najlepsze rozwiązania i zalecenia dotyczące platformy tożsamości firmy Microsoft

W tym artykule przedstawiono najlepsze rozwiązania, zalecenia i szczegółowe informacje dotyczące integracji z platformą tożsamości firmy Microsoft.  Ta lista kontrolna przeprowadzi Cię do wysokiej jakości i bezpiecznej integracji. Przejrzyj tę listę w regularnych odstępach czasu, aby zapewnić zachowanie jakości i bezpieczeństwa integracji aplikacji z platformą tożsamości. Lista kontrolna nie ma na celu przejrzenia całej aplikacji. Zawartość listy kontrolnej może ulec zmianie w miarę wprowadzania ulepszeń na platformie.

Jeśli dopiero zaczynasz, zapoznaj się z [dokumentacją platformy tożsamości firmy Microsoft](index.yml) , aby poznać podstawowe informacje dotyczące uwierzytelniania, scenariusze aplikacji na platformie tożsamości firmy Microsoft i inne elementy.

Użyj poniższej listy kontrolnej, aby upewnić się, że aplikacja jest efektywnie zintegrowana z [platformą tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Podstawy

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przeczytaj i zapoznaj się z [zasadami platformy firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Upewnij się, że aplikacja jest zgodna z warunkami, które zostały zaprojektowane w celu ochrony użytkowników i platformy. |

## <a name="ownership"></a>Wyniku

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Upewnij się, że informacje skojarzone z kontem używanym do rejestrowania aplikacji i zarządzania nimi są aktualne. |

## <a name="branding"></a>Znakowania

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przestrzeganie [wytycznych dotyczących znakowania aplikacji](howto-add-branding-in-azure-ad-apps.md). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Podaj opisową nazwę i logo dla swojej aplikacji. Te informacje są wyświetlane w [monicie o zgodę aplikacji](application-consent-experience.md). Upewnij się, że Twoja nazwa i logo są reprezentatywne dla Twojej firmy/produktu, aby użytkownicy mogli podejmować świadome decyzje. Upewnij się, że nie są naruszane żadne znaki towarowe. |

## <a name="privacy"></a>Ochrona prywatności

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Podaj linki do warunków użytkowania i zasad zachowania poufności informacji aplikacji. |

## <a name="security"></a>Zabezpieczenia

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zarządzaj identyfikatorami URI przekierowania: <ul><li>Zachować własność wszystkich identyfikatorów URI przekierowania i zachować Aktualności rekordów DNS.</li><li>Nie używaj symboli wieloznacznych (*) w identyfikatorach URI.</li><li>W przypadku usługi Web Apps upewnij się, że wszystkie identyfikatory URI są bezpieczne i szyfrowane (na przykład przy użyciu schematów https).</li><li>W przypadku klientów publicznych Użyj identyfikatorów URI przekierowania specyficznych dla platformy, jeśli są odpowiednie (głównie dla systemów iOS i Android). W przeciwnym razie Użyj identyfikatorów URI przekierowania z dużą ilością losowości, aby zapobiec kolizjom podczas wywoływania z powrotem do aplikacji.</li><li>Jeśli Twoja aplikacja jest używana przez izolowanego agenta sieci Web, możesz użyć https://login.microsoftonline.com/common/oauth2/nativeclient.</li><li>Regularnie Przeglądaj i przycinaj wszystkie nieużywane lub niepotrzebne identyfikatory URI przekierowań.</li></ul> |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli Twoja aplikacja jest zarejestrowana w katalogu, Zminimalizuj i ręcznie Monitoruj listę właścicieli rejestracji aplikacji. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie włączaj obsługi [niejawnego przepływu OAuth2](v2-oauth2-implicit-grant-flow.md) , chyba że jest to wymagane jawnie. Zapoznaj się z prawidłowym [scenariuszem](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przenieś poza nazwę użytkownika/hasło. Nie używaj [przepływu poświadczeń hasła właściciela zasobu (ROPC)](v2-oauth-ropc.md), który bezpośrednio obsługuje hasła użytkowników. Ten przepływ wymaga wysokiego stopnia zaufania i zagrożeń użytkownika i powinien być używany tylko wtedy, gdy inne, bezpieczniejsze, nie można używać przepływów. Ten przepływ jest nadal wymagany w niektórych scenariuszach (na przykład DevOps), ale uważaj, że jego użycie spowoduje nałożenie ograniczeń dotyczących aplikacji.  Aby uzyskać bardziej nowoczesne podejścia, Przeczytaj [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md).|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Chroń poufne poświadczenia aplikacji dla aplikacji sieci Web, interfejsów API sieci Web i aplikacji demonów oraz zarządzaj nimi. Użyj [poświadczeń certyfikatu](active-directory-certificate-credentials.md), a nie poświadczeń hasła (kluczy tajnych klienta). Jeśli musisz użyć poświadczenia hasła, nie ustawiaj go ręcznie. Nie należy przechowywać poświadczeń w kodzie ani w konfiguracji, a nigdy nie zezwalać na ich obsługę przez człowieka. Jeśli to możliwe, użyj [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lub [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) do przechowywania i regularnego rotacji Twoich poświadczeń. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Upewnij się, że aplikacja żąda najniższych uprawnień. Tylko Poproś o uprawnienia wymagane przez aplikację i tylko wtedy, gdy są one potrzebne. Zapoznaj się z różnymi [rodzajami uprawnień](v1-permissions-and-consent.md#types-of-permissions). W razie potrzeby używaj uprawnień aplikacji; Użyj uprawnień delegowanych, jeśli jest to możliwe. Aby uzyskać pełną listę uprawnień Microsoft Graph, zobacz to [odwołanie do uprawnień](https://docs.microsoft.com/graph/permissions-reference). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | W przypadku zabezpieczania interfejsu API przy użyciu platformy tożsamości firmy Microsoft należy uważnie zastanowić się nad uprawnieniami, które powinien ujawnić. Weź pod uwagę, jaki jest poziom szczegółowości rozwiązania i jakie uprawnienia wymagają zgody administratora. Przed podjęciem decyzji o autoryzacji Sprawdź, czy w tokenach przychodzących są oczekiwane uprawnienia. |

## <a name="implementation"></a>Wdrażanie

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Używaj nowoczesnych rozwiązań uwierzytelniania (OAuth 2,0, [OpenID Connect Connect](v2-protocols-oidc.md)) do bezpiecznego logowania użytkowników. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |  Nie stosuj bezpośrednio do protokołów, takich jak OAuth 2,0 i Open ID. Zamiast tego Skorzystaj z [biblioteki Microsoft Authentication Library (MSAL)](msal-overview.md). Biblioteki MSAL bezpiecznie zawijają protokoły zabezpieczeń w łatwej w użyciu bibliotece i udostępniamy wbudowaną obsługę scenariuszy [dostępu warunkowego](/azure/active-directory/conditional-access/overview) , [logowania](/azure/active-directory/manage-apps/what-is-single-sign-on)jednokrotnego dla całego urządzenia oraz wbudowanej obsługi buforowania tokenów. Aby uzyskać więcej informacji, zobacz listę [bibliotek klienckich](reference-v2-libraries.md#microsoft-supported-client-libraries) obsługiwanych przez firmę Microsoft i [bibliotek oprogramowania pośredniczącego](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) oraz listę [zgodnych bibliotek klientów](reference-v2-libraries.md#compatible-client-libraries)innych firm.<br/><br/>Jeśli konieczne jest przekazanie kodu dla protokołów uwierzytelniania, należy postępować zgodnie z metodologią, taką jak [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Zwróć szczególną uwagę na kwestie dotyczące zabezpieczeń w specyfikacjach standardów dla każdego protokołu.|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migruj istniejące aplikacje z [biblioteki uwierzytelniania usługi Azure Active Directory (ADAL)](active-directory-authentication-libraries.md) do [biblioteki uwierzytelniania firmy Microsoft](msal-overview.md). MSAL to najnowsze rozwiązanie platformy tożsamości firmy Microsoft i jest preferowane dla biblioteki ADAL. Jest on dostępny w środowisku .NET i JavaScript, a także w publicznej wersji zapoznawczej dla systemów Android, iOS, Python i Java. Przeczytaj więcej na temat migrowania aplikacji [ADAL.NET](msal-net-migration.md), [ADAL. js](msal-compare-msal-js-and-adal-js.md)i [ADAL.NET i iOS Broker](msal-net-migration-ios-broker.md) .|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |  W przypadku aplikacji mobilnych skonfiguruj każdą platformę przy użyciu środowiska rejestracji aplikacji. Aby aplikacja mogła korzystać z Microsoft Authenticator lub Microsoft Portal firmy do logowania jednokrotnego, aplikacja wymaga skonfigurowanego "identyfikatora URI przekierowania brokera". Dzięki temu firma Microsoft może zwrócić kontrolę do aplikacji po uwierzytelnieniu. Podczas konfigurowania każdej platformy środowisko rejestracji aplikacji przeprowadzi Cię przez proces. Aby pobrać przykład pracy, Skorzystaj z przewodnika Szybki Start. W systemie iOS należy używać brokerów i systemu WebView, gdy jest to możliwe.|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |  W obszarze aplikacje sieci Web lub interfejsy API sieci Web Zachowaj jedną pamięć podręczną tokenów dla każdego konta.  W przypadku usługi Web Apps bufor tokenów powinien być poprzedzony IDENTYFIKATORem konta.  W przypadku interfejsów API sieci Web konto powinno być poprzedzone przez skrót tokenu używanego do wywoływania interfejsu API. MSAL.NET udostępnia niestandardową serializację pamięci podręcznej tokeny w podplatformach .NET Framework i .NET Core. Ze względu na bezpieczeństwo i wydajność nasze zalecenia polegają na serializacji jednej pamięci podręcznej na użytkownika. Aby uzyskać więcej informacji, Przeczytaj o [serializacji pamięci podręcznej tokenu](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli dane wymagane przez aplikację są dostępne za pośrednictwem [Microsoft Graph](https://developer.microsoft.com/graph), zażądaj uprawnień dla tych danych przy użyciu punktu końcowego Microsoft Graph, a nie do poszczególnych interfejsów API. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |Nie sprawdzaj wartości tokenu dostępu lub spróbuj przeanalizować ją jako klienta.  Mogą zmieniać wartości, formaty lub nawet być szyfrowane bez ostrzeżenia — zawsze używaj id_token, jeśli klient musi poznać coś o użytkowniku lub wywoływać Microsoft Graph.  Tylko interfejsy API sieci Web powinny analizować tokeny dostępu (ponieważ są one definiowane przy użyciu formatowania i ustawiania kluczy szyfrowania). |

## <a name="end-user-experience"></a>Środowisko użytkownika końcowego

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | [Zapoznaj się z](application-consent-experience.md) tematem zgody i skonfiguruj fragmenty monitu o zgodę aplikacji, aby użytkownicy końcowi i administratorzy mieli wystarczającą ilość informacji, aby określić, czy ufają aplikacji. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zminimalizuj, ile razy użytkownik musi wprowadzić poświadczenia logowania podczas korzystania z aplikacji, próbując przeprowadzić uwierzytelnianie dyskretne (dyskretny token) przed interaktywnymi przepływami. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie używaj "Prompt = zgody" przy każdym logowaniu. Należy używać monitu = wyrażanie zgody, jeśli określono, że należy zażądać zgody na dodatkowe uprawnienia (na przykład jeśli zmieniono wymagane uprawnienia aplikacji). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli ma to zastosowanie, Wzbogacaj aplikację o dane użytkownika. Korzystanie z [interfejsu API Microsoft Graph](https://developer.microsoft.com/graph) to prosty sposób. Narzędzie [Eksplorator grafów](https://developer.microsoft.com/graph/graph-explorer) ułatwiające rozpoczęcie pracy. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zarejestruj pełny zestaw uprawnień wymaganych przez aplikację, aby administratorzy mogli łatwo udzielić zgody na dostęp do swojej dzierżawy. Używaj [przyrostowej zgody](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) w czasie wykonywania, aby ułatwić użytkownikom zrozumienie, Dlaczego aplikacja żąda uprawnień, które mogą mieć problemy lub mylić użytkowników, gdy są żądane podczas pierwszego uruchomienia. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zaimplementuj [czyste środowisko logowania](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)jednokrotnego. Jest to ochrona prywatności i wymagania dotyczące zabezpieczeń oraz zapewnia dobre środowisko użytkownika. |

## <a name="testing"></a>Testowanie

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przetestuj [zasady dostępu warunkowego](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , które mogą mieć wpływ na możliwość korzystania z aplikacji przez użytkowników. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przetestuj swoją aplikację ze wszystkimi możliwymi kontami, które planujesz obsługiwać (na przykład konta służbowe, osobiste konta Microsoft, konta podrzędne i suwerenne konta). |

## <a name="additional-resources"></a>Zasoby dodatkowe

Uzyskaj szczegółowe informacje na temat wersji 2.0:

* [Microsoft Identity platform (wersja 2.0 — omówienie)](v2-overview.md)
* [Informacje o protokołach Microsoft Identity platform](active-directory-v2-protocols.md)
* [Dokumentacja tokenów dostępu](access-tokens.md)
* [Dokumentacja tokenów identyfikatorów](id-tokens.md)
* [Dokumentacja bibliotek uwierzytelniania](reference-v2-libraries.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](v2-permissions-and-consent.md)
* [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph)
