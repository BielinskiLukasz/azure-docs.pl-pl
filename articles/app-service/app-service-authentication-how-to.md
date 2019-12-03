---
title: Zaawansowane użycie AuthN/AuthO
description: Dowiedz się, jak dostosować funkcję uwierzytelniania i autoryzacji w App Service dla różnych scenariuszy oraz uzyskać oświadczenia użytkowników i inne tokeny.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672230"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Zaawansowane użycie uwierzytelniania i autoryzacji w Azure App Service

W tym artykule opisano sposób dostosowywania wbudowanego [uwierzytelniania i autoryzacji w programie App Service](overview-authentication-authorization.md)oraz do zarządzania tożsamościami z poziomu aplikacji. 

Aby szybko rozpocząć pracę, zobacz jeden z następujących samouczków:

* [Samouczek: uwierzytelnianie i Autoryzowanie użytkowników na zakończenie w Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Samouczek: uwierzytelnianie i Autoryzowanie użytkowników w Azure App Service dla systemu Linux](containers/tutorial-auth-aad.md)
* [Jak skonfigurować aplikację do używania logowania w usłudze Azure Active Directory](configure-authentication-provider-aad.md)
* [Jak skonfigurować aplikację do używania logowania usługi Facebook](configure-authentication-provider-facebook.md)
* [Jak skonfigurować aplikację do używania logowania usługi Google](configure-authentication-provider-google.md)
* [Jak skonfigurować aplikację do używania logowania za pomocą konta Microsoft](configure-authentication-provider-microsoft.md)
* [Jak skonfigurować aplikację do używania logowania usługi Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Korzystanie z wielu dostawców logowania

Konfiguracja portalu nie oferuje możliwości podkluczego sposobu prezentowania wielu dostawców logowania użytkownikom (na przykład Facebook i Twitter). Nie ma jednak trudności z dodaniem funkcjonalności do aplikacji. Kroki są opisane w następujący sposób:

Najpierw na stronie **uwierzytelnianie/autoryzacja** w Azure Portal Skonfiguruj każdy dostawca tożsamości, który ma zostać włączony.

W obszarze **Akcja do wykonania, gdy żądanie nie zostanie uwierzytelnione**, wybierz opcję **Zezwalaj na żądania anonimowe (bez akcji)** .

Na stronie logowania lub na pasku nawigacyjnym lub w dowolnej innej lokalizacji aplikacji Dodaj łącze logowania do każdego z włączonych dostawców (`/.auth/login/<provider>`). Na przykład:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Gdy użytkownik kliknie jedno z linków, na odpowiedniej stronie logowania zostanie otwarta strona logowania użytkownika.

Aby przekierować użytkownika po zalogowaniu się do niestandardowego adresu URL, użyj parametru ciągu zapytania `post_login_redirect_url` (nie należy mylić z identyfikatorem URI przekierowania w konfiguracji dostawcy tożsamości). Na przykład, aby nawigować do użytkownika `/Home/Index` po zalogowaniu, użyj następującego kodu HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Weryfikowanie tokenów od dostawców

W przypadku logowania po stronie klienta program automatycznie loguje użytkownika do dostawcy, a następnie przesyła token uwierzytelniania do App Service na potrzeby weryfikacji (zobacz [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow)). Takie sprawdzenie poprawności nie pozwala na uzyskanie dostępu do żądanych zasobów aplikacji, ale pomyślne sprawdzenie poprawności spowoduje użycie tokenu sesji umożliwiającego dostęp do zasobów aplikacji. 

Aby sprawdzić poprawność tokenu dostawcy, aplikacja App Service należy najpierw skonfigurować przy użyciu żądanego dostawcy. W czasie wykonywania, po pobraniu tokenu uwierzytelniania od dostawcy, Opublikuj token w celu `/.auth/login/<provider>` na potrzeby walidacji. Na przykład: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Format tokenu jest nieco różny w zależności od dostawcy. Szczegółowe informacje znajdują się w poniższej tabeli:

| Wartość dostawcy | Wymagane w treści żądania | Komentarze |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Właściwość `expires_in` jest opcjonalna. <br/>Żądając tokenu od usług Live, zawsze Żądaj zakresu `wl.basic`. |
| `google` | `{"id_token":"<id_token>"}` | Właściwość `authorization_code` jest opcjonalna. Po określeniu można również opcjonalnie dołączyć Właściwość `redirect_uri`. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Użyj prawidłowego [tokenu dostępu użytkownika](https://developers.facebook.com/docs/facebook-login/access-tokens) z serwisu Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

W przypadku pomyślnego zweryfikowania tokenu dostawcy interfejs API zwraca z `authenticationToken` w treści odpowiedzi, który jest tokenem sesji. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Gdy masz ten token sesji, możesz uzyskać dostęp do chronionych zasobów aplikacji, dodając nagłówek `X-ZUMO-AUTH` do żądań HTTP. Na przykład: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Wyloguj się z sesji

Użytkownicy mogą inicjować wylogowywanie, wysyłając żądanie `GET` do punktu końcowego `/.auth/logout` aplikacji. Żądanie `GET` wykonuje następujące czynności:

- Czyści pliki cookie uwierzytelniania z bieżącej sesji.
- Usuwa z magazynu tokenów tokeny bieżącego użytkownika.
- W przypadku Azure Active Directory i Google program wykonuje wylogowywanie po stronie serwera dla dostawcy tożsamości.

Oto proste łącze do wylogowywania na stronie sieci Web:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Domyślnie pomyślne wylogowanie przekierowuje klienta do adresu URL `/.auth/logout/done`. Można zmienić stronę przekierowywanie po wylogowaniu, dodając parametr `post_logout_redirect_uri` zapytania. Na przykład:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Zaleca się [zakodowanie](https://wikipedia.org/wiki/Percent-encoding) wartości `post_logout_redirect_uri`.

W przypadku korzystania z w pełni kwalifikowanych adresów URL musi być hostowana w tej samej domenie lub skonfigurowana jako dozwolony zewnętrzny adres URL przekierowania dla aplikacji. W poniższym przykładzie, aby przekierować do `https://myexternalurl.com`, które nie są hostowane w tej samej domenie:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Uruchom następujące polecenie w [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachowaj fragmenty adresów URL

Gdy użytkownicy logują się do aplikacji, zazwyczaj chcą być przekierowani do tej samej sekcji na tej samej stronie, na przykład `/wiki/Main_Page#SectionZ`. Jednak ze względu na to, że [fragmenty adresów URL](https://wikipedia.org/wiki/Fragment_identifier) (na przykład `#SectionZ`) nigdy nie są wysyłane do serwera, nie są domyślnie zachowywane po zakończeniu logowania przy użyciu protokołu OAuth i przekierowuje z powrotem do aplikacji. Następnie użytkownicy uzyskują nieoptymalny komfort, gdy chcą ponownie przejść do żądanego zakotwiczenia. To ograniczenie dotyczy wszystkich rozwiązań uwierzytelniania po stronie serwera.

W ramach uwierzytelniania App Service można zachować fragmenty adresów URL w ramach logowania za pomocą protokołu OAuth. W tym celu należy ustawić ustawienie aplikacji o nazwie `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` na `true`. Można to zrobić w [Azure Portal](https://portal.azure.com)lub po prostu uruchomić następujące polecenie w [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Uzyskiwanie dostępu do oświadczeń użytkowników

App Service przekazuje oświadczenia użytkownika do aplikacji przy użyciu specjalnych nagłówków. Żądania zewnętrzne nie mogą ustawiać tych nagłówków, dlatego są obecne tylko wtedy, gdy są ustawione przez App Service. Przykładowe nagłówki obejmują:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kod, który jest pisany w dowolnym języku lub platformie, może uzyskać informacje potrzebne z tych nagłówków. W przypadku aplikacji ASP.NET 4,6, **ClaimsPrincipal** jest automatycznie ustawiany z odpowiednimi wartościami. ASP.NET Core jednak nie oferuje oprogramowania pośredniczącego uwierzytelniania, które integruje się z App Service oświadczenia użytkownika. Aby obejść ten temat, zobacz [MaximeRouiller. Azure. appService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Aplikacja może również uzyskać dodatkowe szczegóły dotyczące uwierzytelnionego użytkownika, wywołując `/.auth/me`. Zestawy SDK serwera Mobile Apps zapewniają metody pomocnika do pracy z tymi danymi. Aby uzyskać więcej informacji, zobacz [jak używać zestawu SDK platformy azure Mobile Apps Node. js](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)i [współdziałać z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Pobieranie tokenów w kodzie aplikacji

W kodzie serwera tokeny specyficzne dla dostawcy są wstawiane do nagłówka żądania, dzięki czemu można łatwo uzyskać do nich dostęp. W poniższej tabeli przedstawiono możliwe nazwy nagłówków tokenów:

| Dostawca | Nazwy nagłówków |
|-|-|
| Usługa Active Directory systemu Azure | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Konto Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Serwis Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Na podstawie kodu klienta (takiego jak aplikacja mobilna lub JavaScript w przeglądarce) Wyślij żądanie `GET` HTTP, aby `/.auth/me`. Zwrócony kod JSON ma tokeny specyficzne dla dostawcy.

> [!NOTE]
> Tokeny dostępu są przeznaczone do uzyskiwania dostępu do zasobów dostawcy, dlatego są dostępne tylko w przypadku skonfigurowania dostawcy przy użyciu klucza tajnego klienta. Aby dowiedzieć się, jak uzyskać tokeny odświeżania, zobacz Odświeżanie tokenów dostępu.

## <a name="refresh-identity-provider-tokens"></a>Odśwież tokeny dostawcy tożsamości

Gdy token dostępu dostawcy (nie [token sesji](#extend-session-token-expiration-grace-period)) wygaśnie, należy ponownie uwierzytelnić użytkownika przed ponownym użyciem tego tokenu. Możesz uniknąć wygaśnięcia tokenu, wykonując `GET` wywołanie do punktu końcowego `/.auth/refresh` aplikacji. Gdy jest wywoływana, App Service automatycznie odświeża tokeny dostępu w magazynie tokenów dla uwierzytelnionego użytkownika. Kolejne żądania dotyczące tokenów przez kod aplikacji otrzymują odświeżone tokeny. Jednak aby odświeżanie tokenów działało, magazyn tokenów musi zawierać [tokeny odświeżania](https://auth0.com/learn/refresh-tokens/) dla dostawcy. Sposób uzyskania tokenów odświeżania jest udokumentowany przez każdego dostawcę, ale Poniższa lista zawiera krótkie podsumowanie:

- **Google**: Dołącz `access_type=offline` parametr ciągu zapytania do wywołania interfejsu API `/.auth/login/google`. W przypadku używania zestawu SDK Mobile Apps można dodać parametr do jednego z przeciążeń `LogicAsync` (zobacz [tokeny usługi Google Refresh](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: nie udostępnia tokenów odświeżania. Tokeny długotrwałe wygasają w ciągu 60 dni (zobacz temat [wygaśnięcie i rozszerzanie tokenów dostępu w serwisie Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: tokeny dostępu nie wygasną (zobacz [często zadawane pytania dotyczące usługi Twitter OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Konto Microsoft**: podczas [konfigurowania ustawień uwierzytelniania konta microsoft](configure-authentication-provider-microsoft.md)wybierz zakres `wl.offline_access`.
- **Azure Active Directory**: w [https://resources.azure.com](https://resources.azure.com)wykonaj następujące czynności:
    1. W górnej części strony wybierz pozycję **Odczyt/zapis**.
    2. W przeglądarce po lewej stronie przejdź do **subskrypcji** >  **_\<subskrypcja\_nazwa_**  > **resourceGroups** >  **_\<zasobów\__** \_nazwa > > **dostawców** > **witryną** **sieci Web > Microsoft.** 
    3. Kliknij pozycję **Edytuj**.
    4. Zmodyfikuj następującą właściwość. Zastąp _\<identyfikator\_aplikacji, >_ z identyfikatorem aplikacji Azure Active Directory usługi, do której chcesz uzyskać dostęp.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kliknij przycisk **Put**. 

Po skonfigurowaniu dostawcy można [znaleźć token odświeżania i czas wygaśnięcia tokenu dostępu](#retrieve-tokens-in-app-code) w magazynie tokenów. 

Aby odświeżyć token dostępu w dowolnym momencie, po prostu wywołaj `/.auth/refresh` w dowolnym języku. Poniższy fragment kodu używa platformy jQuery do odświeżania tokenów dostępu z klienta JavaScript.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Jeśli użytkownik odwołuje uprawnienia udzielone aplikacji, wywołanie do `/.auth/me` może zakończyć się niepowodzeniem z `403 Forbidden` odpowiedzią. Aby zdiagnozować błędy, Sprawdź dzienniki aplikacji, aby uzyskać szczegółowe informacje.

## <a name="extend-session-token-expiration-grace-period"></a>Okres karencji wygaśnięcia tokenu sesji

Sesja uwierzytelniona wygasa po 8 godzinach. Po wygaśnięciu sesji uwierzytelnionej domyślnie obowiązuje okres prolongaty 72 godzin. W ramach tego okresu prolongaty można odświeżyć token sesji z App Service bez ponownego uwierzytelniania użytkownika. Możesz tylko wywołać `/.auth/refresh`, gdy token sesji stanie się nieprawidłowy, i nie musisz śledzić wygaśnięcia tokenu. Po upływie 72-godzinnego okresu prolongaty użytkownik musi zalogować się ponownie, aby uzyskać prawidłowy token sesji.

Jeśli przez 72 godzin nie jest wystarczająco dużo czasu, możesz to zrobić. Przedłużenie czasu wygaśnięcia przez długi czas może mieć znaczący wpływ na zabezpieczenia (na przykład w przypadku przecieku lub kradzieży tokenu uwierzytelniania). W związku z tym należy pozostawić ją w domyślnych 72 godzinach lub ustawić okres przedłużenia na najmniejszą wartość.

Aby zwiększyć domyślne okno wygaśnięcia, uruchom następujące polecenie w [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Okres prolongaty dotyczy tylko sesji uwierzytelnionej App Service, a nie tokenów od dostawców tożsamości. Brak okresu prolongaty dla wygasłych tokenów dostawcy. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Ogranicz domenę kont logowania

Zarówno konto Microsoft, jak i Azure Active Directory umożliwiają logowanie się z wielu domen. Na przykład konto Microsoft umożliwia korzystanie z kont _Outlook.com_, _Live.com_i _hotmail.com_ . Usługa Azure AD umożliwia dowolna liczba domen niestandardowych dla kont logowania. Można jednak przyspieszyć użytkowników bezpośrednio do własnej, oznakowanej strony logowania do usługi Azure AD (np. `contoso.com`). Aby zasugerować nazwę domeny kont logowania, wykonaj następujące kroki.

W [https://resources.azure.com](https://resources.azure.com)przejdź do **subskrypcji** >  **_\< subskrypcja\_ nazwa_**  > **resourceGroups** >  **_\< zasobów_** \_\_ nazwa > > **dostawcy** >  > **authsettings**. 

Kliknij przycisk **Edytuj**, zmodyfikuj następującą właściwość, a następnie kliknij przycisk **Put**. Pamiętaj, aby zastąpić _nazwę domeny\<\__ z żądaną domeną.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

To ustawienie dołącza `domain_hint` parametr ciągu zapytania do adresu URL przekierowania logowania. 

> [!IMPORTANT]
> Możliwe jest, aby klient usunął parametr `domain_hint` po odebraniu adresu URL przekierowania, a następnie zalogowaniu się z inną domeną. Dlatego chociaż ta funkcja jest wygodna, nie jest funkcją zabezpieczeń.
>

## <a name="authorize-or-deny-users"></a>Autoryzuj lub Odmów użytkownikom

Chociaż App Service zajmuje się najprostszym przypadkiem autoryzacji (tj. odrzuca nieuwierzytelnione żądania), aplikacja może wymagać bardziej szczegółowych zachowań autoryzacji, takich jak ograniczanie dostępu tylko do określonej grupy użytkowników. W niektórych przypadkach należy napisać niestandardowy kod aplikacji, aby zezwolić na dostęp do zalogowanego użytkownika lub go zablokować. W innych przypadkach App Service lub dostawca tożsamości może pomóc bez konieczności wprowadzania zmian w kodzie.

- [Poziom serwera](#server-level-windows-apps-only)
- [Poziom dostawcy tożsamości](#identity-provider-level)
- [Poziom aplikacji](#application-level)

### <a name="server-level-windows-apps-only"></a>Poziom serwera (tylko aplikacje systemu Windows)

W przypadku dowolnej aplikacji systemu Windows można zdefiniować zachowanie autoryzacji serwera sieci Web usług IIS, edytując plik *Web. config* . Aplikacje systemu Linux nie używają usług IIS i nie można ich konfigurować za pomocą *pliku Web. config*.

1. Przejdź do `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. W Eksploratorze przeglądarki plików App Service przejdź do *lokalizacji site/wwwroot*. Jeśli plik *Web. config* nie istnieje, utwórz go, wybierając pozycję **+**  > **nowym pliku**. 

1. Wybierz ołówek dla *pliku Web. config* , aby go edytować. Dodaj następujący kod konfiguracji i kliknij przycisk **Zapisz**. Jeśli *plik Web. config* już istnieje, po prostu dodaj element `<authorization>` ze wszystkimi elementami w nim. Dodaj konta, które mają być dozwolone w elemencie `<allow>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Poziom dostawcy tożsamości

Dostawca tożsamości może zapewnić pewną autoryzację klucza. Na przykład:

- [Azure App Service](configure-authentication-provider-aad.md)można [zarządzać dostępem na poziomie przedsiębiorstwa](../active-directory/manage-apps/what-is-access-management.md) bezpośrednio w usłudze Azure AD. Aby uzyskać instrukcje, zobacz [Jak usunąć dostęp użytkownika do aplikacji](../active-directory/manage-apps/methods-for-removing-user-access.md).
- W przypadku usługi [Google](configure-authentication-provider-google.md)interfejsy API, które należą do [organizacji](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) , można skonfigurować tak, aby zezwalały na dostęp tylko użytkownikom w organizacji (zobacz sekcję [Konfigurowanie pomocy technicznej **OAuth 2,0** ](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Poziom aplikacji

Jeśli jeden z pozostałych poziomów nie zapewnia autoryzacji lub dostawca tożsamości nie jest obsługiwany, należy napisać kod niestandardowy w celu autoryzowania użytkowników na podstawie [oświadczeń użytkowników](#access-user-claims).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: uwierzytelnianie i Autoryzowanie użytkowników kompleksowe (Windows)](app-service-web-tutorial-auth-aad.md)
> [Samouczek: uwierzytelnianie i Autoryzowanie użytkowników na całym końcu (Linux)](containers/tutorial-auth-aad.md)
