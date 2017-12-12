---
title: "Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji z systemem innym niż galerii | Dokumentacja firmy Microsoft"
description: "Konfigurowanie federacyjnych rejestracji jednokrotnej dla niestandardowej aplikacji z systemem innym niż galerii, który chcesz zintegrować z usługą Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8e16e348630d0c94b3f531a84852e3f242458cab
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji z systemem innym niż galerii

Aby skonfigurować logowanie jednokrotne dla aplikacji z systemem innym niż galerii *bez pisania kodu*, musisz mieć subskrypcję lub Azure AD Premium oraz aplikacja muszą obsługiwać SAML 2.0. Aby uzyskać więcej informacji o wersji usługi Azure AD, odwiedź stronę [cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Omówienie kroków wymaganych
Poniżej znajduje się wysokiego poziomu omówienie kroków wymaganych do skonfigurowania federacyjnego logowania jednokrotnego SAML 2.0 z systemem innym niż galerii aplikacji (np. niestandardowe).

-   [Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (znak na adres URL odpowiedzi adres URL, identyfikator)](#_Configuring_single_sign-on)

-   [Wybierz identyfikator użytkownika i dodać atrybuty użytkownika mają być wysyłane do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych usługi Azure AD i certyfikatów](#download-the-azure-ad-metadata-or-certificate)

-   [Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (znak na adres URL, wystawcy, adres URL wylogowania i certyfikatu)](#_Configuring_single_sign-on)

-   [Przypisywanie użytkowników do aplikacji](#_Assign_users_to_the_application)

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurowanie rejestracji jednokrotnej do aplikacji z systemem innym niż galerii

Aby skonfigurować logowanie jednokrotne dla aplikacji, która nie znajduje się w galerii Azure AD, wykonaj następujące czynności:

1.  Otwórz [ **Azure Portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **więcej usług** u dołu menu nawigacji głównego po lewej stronie.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdującego się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** bloku.

6.  Kliknij przycisk **Non galerii aplikacji** w **dodać własną aplikację** sekcji

7.  Wprowadź nazwę aplikacji w **nazwa** pola tekstowego.

8.  Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

9.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

10. Wybierz **na języku SAML logowania jednokrotnego** w **tryb** listy rozwijanej.

11. Wprowadź wymagane wartości w **domeny i adres URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako SSO inicjowanych przez dostawców tożsamości, wprowadź adres URL odpowiedzi i identyfikator.

   2. **Opcjonalnie:** Konfigurowanie aplikacji jako logowanie Jednokrotne zainicjowane SP, zaloguj się na adres URL jest wymagana wartość.

12. W **atrybuty użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

13. **Opcjonalnie:** kliknij **widoku i edytować wszystkie atrybuty użytkowników** atrybuty przesyłany do aplikacji w tokenie SAML podczas logowania użytkownika.

   Aby dodać atrybutu:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **zapisać.** Zostanie wyświetlony nowy atrybut w tabeli.

14. Kliknij przycisk **Konfiguruj &lt;Nazwa aplikacji&gt;**  dokumentacji dostępu na temat konfigurowania rejestracji jednokrotnej w aplikacji. Ponadto ma Azure AD adresy URL i certyfikatu wymaganego dla aplikacji.

15. [Przypisywanie użytkowników do aplikacji.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i dodać atrybuty użytkownika mają być wysyłane do aplikacji

Aby wybrać identyfikator użytkownika lub Dodaj atrybuty użytkownika, wykonaj następujące czynności:

1.  Otwórz [ **Azure Portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **więcej usług** u dołu menu nawigacji głównego po lewej stronie.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich aplikacji.**

6.  Wybierz aplikację, skonfigurowaniu logowania jednokrotnego.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  W obszarze **atrybuty użytkownika** wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej. Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

 >[! Uwaga} usługi Azure AD wybierz format dla atrybutu NameID (identyfikator użytkownika) oparte na wybranej wartości lub format żądany przez aplikację w SAML AuthRequest. Aby uzyskać więcej informacji można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
 >
 >

9.  Aby dodać atrybuty użytkownika, kliknij przycisk **widoku i edytować wszystkie atrybuty użytkowników** atrybuty przesyłany do aplikacji w tokenie SAML podczas logowania użytkownika.

   Aby dodać atrybutu:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **zapisać.** Zostanie wyświetlony nowy atrybut w tabeli.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobieranie metadanych usługi Azure AD lub certyfikat

Aby pobrać metadanych aplikacji lub certyfikatu z usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [ **Azure Portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **więcej usług** u dołu menu nawigacji głównego po lewej stronie.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich aplikacji.**

6.  Wybierz aplikację, skonfigurowaniu logowania jednokrotnego.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Przejdź do **certyfikat podpisywania SAML** sekcji, a następnie kliknij przycisk **Pobierz** wartość w kolumnie. W zależności od tego, jakie aplikacji wymaga Konfigurowanie logowania jednokrotnego zobaczysz opcję, aby pobrać metadane XML albo certyfikatu.

Usługi Azure AD nie udostępnia adresu URL można pobrać metadanych. Można pobrać metadanych jako plik XML.

## <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby przypisać bezpośrednio co najmniej jednego użytkownika do aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **Azure Portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **więcej usług** u dołu menu nawigacji głównego po lewej stronie.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** w menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich aplikacji.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy, aby otworzyć **Dodaj przydziału** bloku.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** bloku.

10. Wpisz w **Pełna nazwa** lub **adres e-mail** użytkownika planuje się przypisanie do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Zaznacz pole wyboru obok zdjęcia profilu użytkownika lub logo, aby dodać użytkownika do **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typu w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwanie według nazwy lub adresu e-mail** polu wyszukiwania, a następnie kliknij przycisk wyboru, aby dodać użytkownika do **wybrane** listy.

13. Po zakończeniu wybierania użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** bloku, aby wybrać rolę można przypisać do użytkowników po wybraniu.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano mieć możliwość uruchamiania tych aplikacji za pomocą metod opisanych w sekcji Opis rozwiązania.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Dostosowywanie oświadczeń SAML wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczeń atrybutów SAML wysyłanych do aplikacji, zobacz [oświadczeń mapowanie w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
[Podaj logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji](active-directory-application-proxy-sso-using-kcd.md)
