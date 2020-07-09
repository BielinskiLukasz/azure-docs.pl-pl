---
title: Problemy z logowaniem do aplikacji federacyjnego logowania jednokrotnego w galerii
description: Wskazówki dotyczące konkretnych problemów, które mogą wystąpić podczas logowania się do aplikacji skonfigurowanej do federacyjnego logowania jednokrotnego opartego na protokole SAML za pomocą usługi Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e72ed9ef43fd1222592e7f88d4e3a6e998c59d2d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759083"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemy z logowaniem do aplikacji spoza galerii skonfigurowanej do federacyjnego logowania jednokrotnego

Aby rozwiązać problemy związane z logowaniem poniżej, zalecamy wykonanie poniższych sugestii w celu uzyskania lepszej diagnostyki i zautomatyzowania kroków rozwiązania:

- Zainstaluj [rozszerzenie moje aplikacje bezpieczne przeglądarki](access-panel-extension-problem-installing.md) , aby pomóc Azure Active Directory (Azure AD) w celu zapewnienia lepszej diagnozowania i rozwiązywania problemów podczas korzystania ze środowiska testowego w Azure Portal.
- Odtwórz błąd przy użyciu środowiska testowego na stronie Konfiguracja aplikacji w Azure Portal. Dowiedz się więcej na temat [debugowania aplikacji logowania jednokrotnego opartego na protokole SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Nie znaleziono aplikacji w katalogu

*Błąd AADSTS70001: `https://contoso.com` w katalogu nie znaleziono aplikacji o identyfikatorze*.

**Możliwa przyczyna**

Atrybut wystawcy wysyłany z aplikacji do usługi Azure AD w żądaniu SAML nie jest zgodny z wartością identyfikatora skonfigurowaną w aplikacji Azure AD.

**Rozdzielczość**

Upewnij się, że `Issuer` atrybut w ŻĄDANIU SAML jest zgodny z wartością identyfikatora skonfigurowaną w usłudze Azure AD. Jeśli używasz [środowiska testowego](../azuread-dev/howto-v1-debug-saml-sso-issues.md) w Azure Portal przy użyciu rozszerzenia moje aplikacje bezpieczne przeglądarki, nie musisz wykonywać tych czynności ręcznie.

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź, czy wartość w polu tekstowym identyfikatora jest zgodna z wartością identyfikatora wyświetlaną w błędzie.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adres odpowiedzi nie jest zgodny z adresami odpowiedzi skonfigurowanymi dla aplikacji. 

*Błąd AADSTS50011: adres odpowiedzi nie `https://contoso.com` pasuje do adresów odpowiedzi skonfigurowanych dla aplikacji* 

**Możliwa przyczyna** 

Wartość AssertionConsumerServiceURL w żądaniu SAML nie jest zgodna z wartością lub wzorcem adresu URL odpowiedzi w usłudze Azure AD. Wartość AssertionConsumerServiceURL w żądaniu SAML to adres URL wyświetlany w komunikacie o błędzie. 

**Rozdzielczość** 

Upewnij się, że `Issuer` atrybut w ŻĄDANIU SAML jest zgodny z wartością identyfikatora skonfigurowaną w usłudze Azure AD. Jeśli używasz [środowiska testowego](../azuread-dev/howto-v1-debug-saml-sso-issues.md) w Azure Portal przy użyciu rozszerzenia moje aplikacje bezpieczne przeglądarki, nie musisz wykonywać tych czynności ręcznie.
 
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.** 

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie. 

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** . 

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory. 

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji. 

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**
  
6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź lub zaktualizuj wartość w polu tekstowym adres URL odpowiedzi, aby dopasować `AssertionConsumerServiceURL` wartość w ŻĄDANIU SAML.    
    
Po zaktualizowaniu wartości adresu URL odpowiedzi w usłudze Azure AD, która jest zgodna z wartością wysyłaną przez aplikację w żądaniu SAML, powinno być możliwe zalogowanie się do aplikacji.

## <a name="user-not-assigned-a-role"></a>Użytkownik nie ma przypisanej roli

*Błąd AADSTS50105: zalogowany użytkownik `brian\@contoso.com` nie jest przypisany do roli dla aplikacji*

**Możliwa przyczyna**

Użytkownikowi nie udzielono dostępu do aplikacji w usłudze Azure AD.

**Rozdzielczość**

Aby bezpośrednio przypisać co najmniej jednego użytkownika do aplikacji, wykonaj poniższe czynności. Jeśli używasz [środowiska testowego](../azuread-dev/howto-v1-debug-saml-sso-issues.md) w Azure Portal przy użyciu rozszerzenia moje aplikacje bezpieczne przeglądarki, nie musisz wykonywać tych czynności ręcznie.

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9. Kliknij selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **użytkownikiem** na liście, aby odsłonić **pole wyboru**. Kliknij pole wyboru obok zdjęcia lub logo profilu użytkownika, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych użytkowników.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrane przez Ciebie użytkownicy będą mogli uruchamiać te aplikacje za pomocą metod opisanych w sekcji opis rozwiązania.

## <a name="not-a-valid-saml-request"></a>To nie jest prawidłowe żądanie SAML

*Błąd AADSTS75005: żądanie nie jest prawidłowym komunikatem protokołu SAML2.*

**Możliwa przyczyna**

Usługa Azure AD nie obsługuje żądania SAML wysłanego przez aplikację na potrzeby logowania jednokrotnego. Typowe problemy to:

-   Brak wymaganych pól w żądaniu SAML

-   Zakodowana metoda żądania SAML

**Rozdzielczość**

1.  Przechwyć żądanie SAML. Postępuj zgodnie z samouczkiem dotyczącym debugowania logowania jednokrotnego opartego na protokole [SAML do aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) , aby dowiedzieć się, jak przechwycić żądanie SAML.

2.  Skontaktuj się z dostawcą aplikacji i Udostępnij:

    -   Żądanie SAML

    -   [Wymagania dotyczące protokołu SAML logowania jednokrotnego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Dostawca aplikacji powinien sprawdzić, czy obsługują one implementację protokołu SAML usługi Azure AD w celu logowania jednokrotnego.

## <a name="misconfigured-application"></a>Błędna konfiguracja aplikacji

*Błąd AADSTS650056: nieprawidłowo skonfigurowana aplikacja. Może to być spowodowane jedną z następujących czynności: klient nie wystawił żadnych uprawnień w żądanych uprawnieniach w rejestracji aplikacji klienta. Lub administrator nie wyraził zgody na dzierżawę. Lub Sprawdź identyfikator aplikacji w żądaniu, aby upewnić się, że pasuje do skonfigurowanego identyfikatora aplikacji klienckiej. Skontaktuj się z administratorem, aby naprawić konfigurację lub zgodę w imieniu dzierżawy.*

**Możliwa przyczyna**

`Issuer`Atrybut wysłany z aplikacji do usługi Azure AD w ŻĄDANIU SAML nie jest zgodny z wartością identyfikatora skonfigurowaną dla aplikacji w usłudze Azure AD.

**Rozdzielczość**

Upewnij się, że `Issuer` atrybut w ŻĄDANIU SAML jest zgodny z wartością identyfikatora skonfigurowaną w usłudze Azure AD. Jeśli używasz [środowiska testowego](../azuread-dev/howto-v1-debug-saml-sso-issues.md) w Azure Portal z rozszerzeniem my Apps Secure Browser, nie musisz wykonać następujących czynności ręcznie:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator**.

1.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

1.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

1.  Wybierz pozycję **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie Azure Active Directory.

1.  Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje**.

1.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

1.  Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. Sprawdź, czy wartość w polu tekstowym identyfikatora jest zgodna z wartością identyfikatora wyświetlaną w błędzie.

## <a name="certificate-or-key-not-configured"></a>Nie skonfigurowano certyfikatu lub klucza

Błąd AADSTS50003: nie skonfigurowano klucza podpisywania.

**Możliwa przyczyna**

Obiekt aplikacji jest uszkodzony, a usługa Azure AD nie rozpoznaje certyfikatu skonfigurowanego dla aplikacji.

**Rozdzielczość**

Aby usunąć i utworzyć nowy certyfikat, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Kliknij pozycję **Utwórz nowy certyfikat** w sekcji **certyfikat podpisywania SAML** .

9. Wybierz datę wygaśnięcia. Następnie kliknij przycisk **Zapisz.**

10. Zaznacz pole wyboru **Utwórz nowy certyfikat jako aktywny** , aby zastąpić aktywny certyfikat. Następnie kliknij przycisk **Zapisz** w górnej części okienka i zaakceptuj, aby aktywować certyfikat przerzucania.

11. W sekcji **certyfikat podpisywania SAML** kliknij przycisk **Usuń** , aby usunąć **nieużywany** certyfikat.

## <a name="saml-request-not-present-in-the-request"></a>Żądanie SAML nie występuje w żądaniu

*Błąd AADSTS750054: element SAMLRequest lub SAMLResponse musi być obecny jako parametry ciągu zapytania w żądaniu HTTP dla powiązania SAML redirect.*

**Możliwa przyczyna**

Usługa Azure AD nie mogła zidentyfikować żądania SAML w parametrach adresu URL w żądaniu HTTP. Może się tak zdarzyć, jeśli aplikacja nie korzysta z powiązania przekierowania HTTP podczas wysyłania żądania SAML do usługi Azure AD.

**Rozdzielczość**

Aplikacja musi wysłać żądanie SAML zakodowane w nagłówku lokalizacji przy użyciu powiązania przekierowywania HTTP. Aby dowiedzieć się więcej o sposobie implementacji tego rozwiązania, zapoznaj się z sekcją powiązania przekierowania HTTP w [dokumencie specyfikacji protokołu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Usługa Azure AD wysyła token do nieprawidłowego punktu końcowego

**Możliwa przyczyna**

W trakcie logowania jednokrotnego, jeśli żądanie logowania nie zawiera jawnego adresu URL odpowiedzi (adresu URL usługi Konsumenckej potwierdzenia), usługa Azure AD wybierze dowolny ze skonfigurowanych adresów URL dla tej aplikacji. Nawet jeśli dla aplikacji jest skonfigurowany jawny adres URL odpowiedzi, użytkownik może mieć możliwość przekierowania https://127.0.0.1:444 . 

Podczas dodawania tej aplikacji jako aplikacji spoza galerii usługa Azure Active Directory utworzyła ten adres URL odpowiedzi jako wartość domyślną. To działanie zostało zmienione i usługa Azure Active Directory nie dodaje już domyślnie tego adresu URL. 

**Rozdzielczość**

Usuń nieużywane adresy URL odpowiedzi skonfigurowane dla aplikacji.

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator**.

2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Wybierz pozycję **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie Azure Active Directory.

5.  Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

    Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje**.

6.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

7.  Po załadowaniu aplikacji otwórz **podstawową konfigurację protokołu SAML**. W **adresie URL odpowiedzi (adres URL usługi konsumenckej potwierdzenia)** Usuń nieużywane lub domyślne adresy URL odpowiedzi utworzone przez system. Na przykład `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem podczas dostosowywania oświadczeń SAML wysyłanych do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutu SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) , aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
[Wymagania dotyczące protokołu SAML logowania jednokrotnego usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
