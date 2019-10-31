---
title: Uwierzytelnianie oparte na nagłówkach za pomocą PingAccess dla platformy Azure serwer proxy aplikacji usługi Azure AD | Microsoft Docs
description: Publikowanie aplikacji za pomocą PingAccess i serwera proxy aplikacji w celu obsługi uwierzytelniania opartego na nagłówkach.
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
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec115e0fa76e695809ba140202d5f13a319d33dd
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062703"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Uwierzytelnianie oparte na nagłówkach logowania jednokrotnego przy użyciu serwera proxy aplikacji i PingAccess

Serwer proxy aplikacji usługi Azure Active Directory (Azure AD) został partner PingAccess, dzięki czemu klienci usługi Azure AD mogą uzyskiwać dostęp do większej liczby aplikacji. PingAccess rozszerza [istniejące oferty serwera proxy aplikacji](application-proxy.md) w celu uwzględnienia dostępu logowania jednokrotnego do aplikacji, które używają nagłówków do uwierzytelniania.

## <a name="whats-pingaccess-for-azure-ad"></a>Co to jest PingAccess dla usługi Azure AD?

Dzięki usłudze PingAccess dla usługi Azure AD możesz udzielić użytkownikom dostępu i logowania jednokrotnego do aplikacji, które używają nagłówków do uwierzytelniania. Serwer proxy aplikacji traktuje te aplikacje, takie jak inne, za pomocą usługi Azure AD do uwierzytelniania dostępu, a następnie przekazywania ruchu przez usługę łącznika. PingAccess znajduje się przed aplikacjami i tłumaczy token dostępu z usługi Azure AD na nagłówek. Następnie aplikacja odbiera uwierzytelnianie w formacie, który może odczytać.

Użytkownicy nie będą zarejestrowani w przypadku logowania się do korzystania z aplikacji firmowych. Mogą oni nadal korzystać z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwera proxy aplikacji kierują ruch zdalny do wszystkich aplikacji bez względu na ich typ uwierzytelniania, dzięki czemu będą nadal zrównoważać obciążenia automatycznie.

## <a name="how-do-i-get-access"></a>Jak mogę uzyskać dostęp?

Ponieważ ten scenariusz pochodzi z partnerstwa między Azure Active Directory i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje Azure Active Directory — wersja Premium obejmują podstawową licencję PingAccess, która obejmuje maksymalnie 20 aplikacji. Jeśli musisz opublikować więcej niż 20 aplikacji opartych na nagłówkach, możesz zakupić dodatkową licencję z PingAccess.

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikowanie aplikacji na platformie Azure

Ten artykuł ma na celu opublikowanie aplikacji w tym scenariuszu po raz pierwszy. Oprócz szczegółów dotyczących etapów publikowania Przewodnik po wprowadzeniu do serwera proxy aplikacji i PingAccess. Jeśli skonfigurowano już obie usługi, ale chcesz, aby odświeżyć procedurę publikowania, przejdź do sekcji [Dodawanie aplikacji do usługi Azure AD za pomocą serwera proxy aplikacji](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Ponieważ ten scenariusz jest partnerstwem między usługą Azure AD i PingAccess, niektóre instrukcje znajdują się w witrynie ping Identity.

### <a name="install-an-application-proxy-connector"></a>Instalowanie łącznika serwera proxy aplikacji

Jeśli włączono serwer proxy aplikacji włączony i został już zainstalowany łącznik, możesz pominąć tę sekcję i przejść do sekcji [Dodawanie aplikacji do usługi Azure AD za pomocą serwera proxy aplikacji](#add-your-application-to-azure-ad-with-application-proxy).

Łącznik serwera proxy aplikacji to usługa systemu Windows Server, która kieruje ruch od pracowników zdalnych do opublikowanych aplikacji. Aby uzyskać bardziej szczegółowe instrukcje dotyczące instalacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego za poorednictwem serwera proxy aplikacji w Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji. Zostanie wyświetlona strona **Centrum administracyjne Azure Active Directory** .
1. Wybierz pozycję **Azure Active Directory** > **serwera proxy aplikacji** > **Pobierz usługę łącznika**. Zostanie wyświetlona strona **pobierania łącznika serwera proxy aplikacji** .

   ![Pobieranie łącznika serwera proxy aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Postępuj zgodnie z instrukcjami instalacji.

Pobranie łącznika powinno automatycznie włączyć serwer proxy aplikacji dla katalogu, ale jeśli nie, możesz wybrać opcję **Włącz serwer proxy aplikacji**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Dodawanie aplikacji do usługi Azure AD za pomocą serwera proxy aplikacji

Istnieją dwie akcje, które należy wykonać w Azure Portal. Najpierw musisz opublikować aplikację przy użyciu serwera proxy aplikacji. Następnie należy zebrać pewne informacje dotyczące aplikacji, których można użyć podczas kroków PingAccess.

#### <a name="publish-your-application"></a>Publikowanie aplikacji

Najpierw musisz opublikować aplikację. Ta akcja obejmuje:

- Dodawanie aplikacji lokalnej do usługi Azure AD
- Przypisywanie użytkownika na potrzeby testowania aplikacji i wybierania rejestracji jednokrotnej opartej na nagłówkach
- Konfigurowanie adresu URL przekierowania aplikacji
- Przyznawanie uprawnień użytkownikom i innym aplikacjom do korzystania z aplikacji lokalnej

Aby opublikować własną aplikację lokalną:

1. Jeśli nie ma w ostatniej sekcji, zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji.
1. Wybierz pozycję **aplikacje dla przedsiębiorstw** > **nową aplikację** > **Dodaj aplikację lokalną**. Zostanie wyświetlona strona **Dodawanie własnej aplikacji lokalnej** .

   ![Dodawanie własnej aplikacji lokalnej](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Wypełnij pola wymagane informacjami o nowej aplikacji. Skorzystaj z poniższych wskazówek dotyczących ustawień.

   > [!NOTE]
   > Aby zapoznać się z bardziej szczegółowym opisem tego kroku, zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Wewnętrzny adres URL**: zazwyczaj podajesz adres URL, który przeprowadzi Cię do strony logowania aplikacji, gdy jesteś w sieci firmowej. W tym scenariuszu łącznik musi traktować serwer proxy PingAccess jako stronę frontonu aplikacji. Użyj tego formatu: `https://<host name of your PingAccess server>:<port>`. Domyślnie port jest 3000, ale można go skonfigurować w PingAccess.

      > [!WARNING]
      > W przypadku tego typu logowania jednokrotnego wewnętrzny adres URL musi używać `https` i nie może używać `http`.

   1. **Metoda uwierzytelniania wstępnego**: Wybierz **Azure Active Directory**.
   1. **Tłumaczenie adresu URL w nagłówkach**: wybierz pozycję **nie**.

   > [!NOTE]
   > Jeśli jest to Twoja pierwsza aplikacja, użyj portu 3000 do rozpoczęcia i wróć do aktualizacji tego ustawienia, jeśli zmienisz konfigurację PingAccess. W przypadku kolejnych aplikacji port będzie musiał odpowiadać odbiornikowi skonfigurowanemu w PingAccess. Dowiedz się więcej o [odbiornikach w PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Wybierz pozycję **Dodaj**. Zostanie wyświetlona strona przegląd nowej aplikacji.

Teraz Przypisz użytkownika do testowania aplikacji i wybierz pozycję Logowanie jednokrotne oparte na nagłówkach:

1. Na pasku bocznym aplikacji wybierz pozycję **Użytkownicy i grupy** > **Dodaj użytkownika** > **użytkowników i grup (\<numer > zaznaczone)** . Zostanie wyświetlona lista użytkowników i grup do wyboru.

   ![Pokazuje listę użytkowników i grup](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Wybierz użytkownika do testowania aplikacji, a następnie wybierz pozycję **Wybierz**. Upewnij się, że to konto testowe ma dostęp do aplikacji lokalnej.
1. Wybierz opcję **Przypisz**.
1. Na pasku bocznym aplikacji wybierz pozycję **Logowanie** jednokrotne > **na podstawie nagłówka**.

   > [!TIP]
   > Jeśli używasz logowania jednokrotnego opartego na nagłówkach, musisz zainstalować PingAccess. Aby upewnić się, że subskrypcja platformy Azure jest automatycznie skojarzona z instalacją PingAccess, Użyj linku na tej stronie logowania jednokrotnego, aby pobrać PingAccess. Możesz otworzyć witrynę pobierania teraz lub wrócić do tej strony później.

   ![Pokazuje ekran logowania na podstawie nagłówka i PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Wybierz pozycję **Zapisz**.

Upewnij się, że adres URL przekierowania jest ustawiony na zewnętrzny adres URL:

1. Na pasku bocznym **Centrum administracyjnego Azure Active Directory** wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację.
1. Wybierz łącze obok **identyfikatorów URI przekierowania**, pokazując liczbę identyfikatorów URI przekierowania skonfigurowanych dla klientów sieci Web i publicznych. Zostanie wyświetlona strona **\<> Nazwa aplikacji — uwierzytelnianie** .
1. Sprawdź, czy zewnętrzny adres URL przypisany do aplikacji znajduje się na liście **identyfikatorów URI przekierowania** . Jeśli tak nie jest, Dodaj teraz zewnętrzny adres URL, używając typu identyfikatora URI przekierowania dla **sieci Web**, a następnie wybierz pozycję **Zapisz**.

Na koniec Skonfiguruj swoją aplikację lokalną, aby użytkownicy mieli dostęp do odczytu i do nich dostęp do odczytu i zapisu:

1. Na pasku bocznym **rejestracje aplikacji** aplikacji wybierz pozycję **uprawnienia interfejsu API** > **Dodaj uprawnienie** > **interfejsów API firmy Microsoft** > **Microsoft Graph**. Zostanie wyświetlona strona **uprawnienia interfejsu API żądania** dla **Microsoft Graph** , która zawiera interfejsy api dla systemu Windows Azure Active Directory.

   ![Pokazuje stronę uprawnień interfejsu API żądania](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Wybierz **uprawnienia delegowane** > **użytkownika** > **użytkownika. Odczytaj**.
1. Wybierz pozycję **uprawnienia aplikacji** > **Application** > **Application. ReadWrite. All**.
1. Wybierz pozycję **Dodaj uprawnienia**.
1. Na stronie **uprawnienia interfejsu API** wybierz pozycję **Udziel zgody administrator na \<nazwy katalogu >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Zbierz informacje dotyczące kroków PingAccess

Należy zebrać te trzy informacje (wszystkie identyfikatory GUID) w celu skonfigurowania aplikacji za pomocą PingAccess:

| Nazwa pola usługi Azure AD | Nazwa pola PingAccess | Format danych |
| --- | --- | --- |
| **Identyfikator aplikacji (klienta)** | **Client ID (Identyfikator klienta)** | IDENT |
| **Identyfikator katalogu (dzierżawcy)** | **Issuer** | IDENT |
| `PingAccess key` | **Client Secret (Wpis tajny klienta)** | Ciąg losowy |

Aby zebrać te informacje:

1. Na pasku bocznym **Centrum administracyjnego Azure Active Directory** wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację. Zostanie wyświetlona strona **rejestracje aplikacji** aplikacji.

   ![Przegląd rejestracji dla aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Obok pozycji wartość **identyfikatora aplikacji (klienta)** wybierz ikonę **Kopiuj do schowka** , a następnie skopiuj i Zapisz. Tę wartość można określić później jako identyfikator klienta PingAccess.
1. Następnie wybierz pozycję **Kopiuj do schowka**, a następnie skopiuj i Zapisz. Tę wartość można określić później jako wystawca PingAccess.
1. Na pasku bocznym **rejestracje aplikacji** aplikacji wybierz pozycję **Certyfikaty i wpisy tajne** > **nowy klucz tajny klienta**. Zostanie wyświetlona strona **Dodawanie wpisu tajnego klienta** .

   ![Pokazuje stronę Dodawanie wpisu tajnego klienta](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. W polu **Opis**wpisz `PingAccess key`.
1. W obszarze **wygaśnięcie**wybierz pozycję jak ustawić klucz PingAccess: **w 1 roku**, **w ciągu 2 lat**lub **nigdy**.
1. Wybierz pozycję **Dodaj**. Klucz PingAccess jest wyświetlany w tabeli wpisów tajnych klienta z losowym ciągiem, który jest wypełniany w polu **wartość** .
1. Obok pola **wartość** klucza PingAccess wybierz ikonę **Kopiuj do schowka** , a następnie skopiuj i Zapisz. Tę wartość można określić później jako klucz tajny klienta PingAccess.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Aktualizacja GraphAPI do wysyłania pól niestandardowych (opcjonalnie)

Jeśli potrzebujesz niestandardowego żądania, które wysyła inne tokeny w access_token używane przez PingAccess, ustaw `acceptMappedClaims` pole aplikacji na `True`. Aby wprowadzić tę zmianę, można użyć Eksploratora grafów lub manifestu aplikacji portalu usługi Azure AD.

**W tym przykładzie użyto Eksploratora wykresów:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**W tym przykładzie do aktualizowania pola `acceptMappedClaims` służy [Azure Active Directory Portal](https://aad.portal.azure.com/) :**

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji.
1. Wybierz **Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację.
1. Na pasku bocznym strony **rejestracje aplikacji** aplikacji wybierz pozycję **manifest**. Zostanie wyświetlony kod JSON manifestu dla rejestracji aplikacji.
1. Wyszukaj pole `acceptMappedClaims` i zmień wartość na `True`.
1. Wybierz pozycję **Zapisz**.

### <a name="use-of-optional-claims-optional"></a>Użycie opcjonalnych oświadczeń (opcjonalnie)

Opcjonalne oświadczenia umożliwiają dodawanie niestandardowych oświadczeń, które nie są uwzględniane przez wszystkich użytkowników i dzierżawców. Można skonfigurować opcjonalne oświadczenia dla aplikacji, modyfikując manifest aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł Omówienie manifestu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Przykład, aby uwzględnić adres e-mail w access_token, który będzie zużywał PingAccess:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Użycie zasad mapowania oświadczeń (opcjonalnie)

[Zasady mapowania oświadczeń (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) dla atrybutów, które nie istnieją w AzureAD. Mapowanie oświadczeń umożliwia migrowanie starych aplikacji Premium do chmury przez dodawanie dodatkowych oświadczeń niestandardowych, które są obsługiwane przez usługi ADFS lub obiekty użytkownika

Aby aplikacja korzystała z oświadczenia niestandardowego i zawierać dodatkowe pola, upewnij się, że utworzono również [niestandardowe zasady mapowania oświadczeń i przypisano je do aplikacji](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Aby można było użyć niestandardowego żądania, należy także mieć zasady niestandardowe zdefiniowane i przypisane do aplikacji. Te zasady powinny obejmować wszystkie wymagane atrybuty niestandardowe.
>
> Możesz wykonywać definicje zasad i przypisywać je za pomocą programu PowerShell, Eksploratora Azure AD Graph lub Microsoft Graph. Jeśli wykonujesz je w programie PowerShell, może być konieczne pierwsze użycie `New-AzureADPolicy`, a następnie przypisanie go do aplikacji przy użyciu `Add-AzureADServicePrincipalPolicy`. Aby uzyskać więcej informacji, zobacz [przypisywanie zasad mapowania oświadczeń](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Przykład:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Włącz PingAccess do korzystania z oświadczeń niestandardowych

Włączenie PingAccess do korzystania z oświadczeń niestandardowych jest opcjonalne, ale wymagane, jeśli oczekujesz, że aplikacja korzysta z dodatkowych oświadczeń.

Gdy skonfigurujesz PingAccess w poniższym kroku, sesja sieci Web, którą utworzysz (Ustawienia > dostępu > sesji sieci Web) musi mieć **profil żądania** , a następnie **Odśwież atrybuty użytkownika** ustawione na wartość **nie**

## <a name="download-pingaccess-and-configure-your-application"></a>Pobieranie PingAccess i Konfigurowanie aplikacji

Po wykonaniu wszystkich czynności konfiguracyjnych Azure Active Directory można przejść do sekcji Konfigurowanie PingAccess.

Szczegółowe instrukcje dotyczące części PingAccess tego scenariusza są kontynuowane w dokumentacji ping Identity. Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie PingAccess dla usługi Azure AD, aby chronić aplikacje opublikowane przy użyciu Microsoft Azure AD serwera proxy aplikacji](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) w witrynie sieci Web usługi ping Identity.

Te kroki ułatwiają zainstalowanie PingAccess i skonfigurowanie konta PingAccess (jeśli jeszcze go nie masz). Następnie w celu utworzenia połączenia usługi Azure AD OpenID Connect Connect (OIDC) można skonfigurować dostawcę tokenów z wartością **identyfikatora katalogu (dzierżawa)** skopiowaną z portalu usługi Azure AD. Następnie, aby utworzyć sesję sieci Web w usłudze PingAccess, użyj wartości **identyfikatora aplikacji (klienta)** i `PingAccess key`. Następnie można skonfigurować mapowanie tożsamości i utworzyć hosta wirtualnego, witryny i aplikacji.

### <a name="test-your-application"></a>Testowanie aplikacji

Po wykonaniu wszystkich powyższych czynności aplikacja powinna być uruchomiona. Aby go przetestować, Otwórz przeglądarkę i przejdź do zewnętrznego adresu URL, który został utworzony podczas publikowania aplikacji na platformie Azure. Zaloguj się przy użyciu konta testowego, które zostało przypisane do aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie PingAccess usługi Azure AD do ochrony aplikacji publikowanych za pomocą serwera proxy aplikacji Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md)
