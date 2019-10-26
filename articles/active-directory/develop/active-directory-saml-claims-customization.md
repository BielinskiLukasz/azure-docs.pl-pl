---
title: Dostosowywanie oświadczeń tokenów SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD
titleSuffix: Microsoft identity platform
description: Informacje o dostosowywaniu oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4307c9036db45145a7c0e95cb5e55a667c6851eb
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893386"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Instrukcje: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw

Obecnie usługa Azure Active Directory (Azure AD) obsługuje logowanie jednokrotne (SSO) z większością aplikacji przedsiębiorstwa, w tym zarówno aplikacje wstępnie zintegrowane w galerii aplikacji usługi Azure AD, jak i aplikacje niestandardowe. Gdy użytkownik uwierzytelnia się w aplikacji za pośrednictwem usługi Azure AD przy użyciu protokołu SAML 2,0, usługa Azure AD wysyła token do aplikacji (za pośrednictwem protokołu HTTP POST). Następnie aplikacja sprawdza poprawność i używa tokenu do zarejestrowania użytkownika w zamiast monitowania o podanie nazwy użytkownika i hasła. Te tokeny SAML zawierają informacje o użytkowniku znanym jako *oświadczenia*.

Jest *to* informacja, którą dostawca tożsamości informuje o użytkowniku w tokenie, który wystawia dla tego użytkownika. W [tokenie SAML](https://en.wikipedia.org/wiki/SAML_2.0)dane te są zazwyczaj zawarte w instrukcji języka SAML. Unikatowy identyfikator użytkownika jest zazwyczaj reprezentowany przez element SAML subject nazywany również identyfikatorem nazwy.

Domyślnie usługa Azure AD wystawia token języka SAML dla aplikacji, która zawiera `NameIdentifier`, z wartością nazwy użytkownika (znanej także jako główna nazwa użytkownika) w usłudze Azure AD, która może jednoznacznie identyfikować użytkownika. Token SAML zawiera również dodatkowe oświadczenia zawierające adres e-mail użytkownika, imię i nazwisko.

Aby wyświetlić lub edytować oświadczenia wystawione w tokenie SAML dla aplikacji, Otwórz aplikację w Azure Portal. Następnie otwórz sekcję **atrybuty użytkownika & oświadczenia** .

![Otwórz sekcję atrybuty użytkownika & oświadczenia w Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Istnieją dwie możliwe przyczyny, dla których może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:

* Aplikacja wymaga, aby wartość `NameIdentifier` lub NameID była inna niż nazwa użytkownika (lub główna nazwa użytkownika) przechowywana w usłudze Azure AD.
* Aplikacja została zapisywana, aby wymagać innego zestawu identyfikatorów URI lub wartości zgłoszeń.

## <a name="editing-nameid"></a>Edytowanie nameID

Aby edytować NameID (wartość identyfikatora nazwy):

1. Otwórz stronę **wartości identyfikatora nazwy** .
1. Wybierz atrybut lub przekształcenie, które chcesz zastosować do atrybutu. Opcjonalnie możesz określić format, który ma mieć NameID.

   ![Edytuj wartość NameID (identyfikator nazwy)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Format NameID

Jeśli żądanie SAML zawiera element NameIDPolicy o określonym formacie, usługa Azure AD będzie przestrzegać formatu żądania.

Jeśli żądanie SAML nie zawiera elementu NameIDPolicy, usługa Azure AD wystawia NameID z określonym formatem. Jeśli żaden format nie zostanie określony, usługa Azure AD będzie używać domyślnego formatu źródłowego skojarzonego z wybranym źródłem usługi.

Z listy rozwijanej **Wybierz format identyfikatora nazwy** można wybrać jedną z następujących opcji.

| Format NameID | Opis |
|---------------|-------------|
| **Domyślne** | Usługa Azure AD będzie używać domyślnego formatu źródła. |
| **Trwałe** | Usługa Azure AD będzie używać trwałego formatu NameID. |
| **EmailAddress** | Usługa Azure AD będzie używać EmailAddress jako formatu NameID. |
| **Nieokreślony** | Usługa Azure AD będzie używać nieokreślone jako formatu NameID. |

Przejściowa NameID jest również obsługiwana, ale nie jest dostępna na liście rozwijanej i nie można jej skonfigurować na stronie platformy Azure. Aby dowiedzieć się więcej o atrybucie NameIDPolicy, zobacz Logowanie jednokrotne [protokołu SAML](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atrybuty

Wybierz żądane źródło dla żądania `NameIdentifier` (lub NameID). Można wybrać jedną z następujących opcji.

| Nazwa | Opis |
|------|-------------|
| Adres e-mail | Adres e-mail użytkownika |
| userprincipalName | Główna nazwa użytkownika (UPN) użytkownika |
| onpremisessamaccount | Nazwa konta SAM, które zostało zsynchronizowane z lokalnej usługi Azure AD |
| obiektu | Identyfikator obiektu użytkownika w usłudze Azure AD |
| employeeid | IDENTYFIKATOR pracownika użytkownika |
| Rozszerzenia katalogów | Rozszerzenia katalogu [zsynchronizowane z Active Directory lokalnego przy użyciu synchronizacji Azure AD Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atrybuty rozszerzenia 1-15 | Atrybuty rozszerzenia lokalnego używane do rozszerzania schematu usługi Azure AD |

Aby uzyskać więcej informacji, zobacz [tabela 3: prawidłowe wartości identyfikatorów na źródło](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Możesz również przypisać dowolną stałą (statyczną) wartość do wszelkich oświadczeń zdefiniowanych w usłudze Azure AD. Postępuj zgodnie z poniższymi instrukcjami, aby przypisać stałą wartość:

1. W [Azure Portal](https://portal.azure.com/), w sekcji **atrybuty użytkownika & oświadczenia** , kliknij ikonę **Edytuj** , aby edytować oświadczenia.

1. Kliknij wymagane żądanie, które chcesz zmodyfikować.

1. Wprowadź wartość stałą bez cudzysłowów w **atrybucie Source** zgodnie z Twoją organizacją, a następnie kliknij przycisk **Zapisz**.

    ![Otwórz sekcję atrybuty użytkownika & oświadczenia w Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Wartość stała zostanie wyświetlona w następujący sposób.

    ![Otwórz sekcję atrybuty użytkownika & oświadczenia w Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Specjalne oświadczenia — przekształcenia

Można również użyć funkcji przekształcenia oświadczeń.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny z adresu e-mail lub głównej nazwy użytkownika. Spowoduje to wyodrębnienie tylko pierwszej części nazwy użytkownika, która jest przenoszona przez (na przykład "joe_smith", a nie joe_smith@contoso.com). |
| **Join ()** | Sprzęga atrybut z zweryfikowaną domeną. Jeśli wybrana wartość identyfikatora użytkownika ma domenę, Wyodrębnij nazwę użytkownika w celu dołączenia wybranej zweryfikowanej domeny. Na przykład w przypadku wybrania adresu e-mail (joe_smith@contoso.com) jako wartości identyfikatora użytkownika i wybrania opcji contoso.onmicrosoft.com jako zweryfikowanej domeny spowoduje to joe_smith@contoso.onmicrosoft.com. |
| **ToLower ()** | Konwertuje znaki wybranego atrybutu do małych liter. |
| **ToUpper ()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |

## <a name="adding-application-specific-claims"></a>Dodawanie oświadczeń specyficznych dla aplikacji

Aby dodać oświadczenia specyficzne dla aplikacji:

1. W polu **atrybuty użytkownika & oświadczenia**wybierz pozycję **Dodaj nowe oświadczenie** , aby otworzyć stronę **Zarządzanie oświadczeniami użytkowników** .
1. Wprowadź **nazwę** oświadczenia. Wartość nie musi być ściśle zgodna ze wzorcem URI dla specyfikacji SAML. Jeśli potrzebujesz wzorca URI, możesz go umieścić w polu **przestrzeń nazw** .
1. Wybierz **Źródło** , do którego ma zostać pobrana wartość. Można wybrać atrybut użytkownika z listy rozwijanej atrybutów źródłowych lub zastosować transformację do atrybutu użytkownika przed wyemitowaniem go jako roszczeń.

### <a name="claim-transformations"></a>Przekształcenia roszczeń

Aby zastosować transformację do atrybutu użytkownika:

1. W obszarze **Zarządzaj roszczeń**wybierz pozycję *transformacja* jako źródło roszczeń, aby otworzyć stronę **Zarządzanie transformacjęm** .
2. Wybierz funkcję z listy rozwijanej transformacja. W zależności od wybranej funkcji należy podać parametry i stałą wartość, aby obliczyć transformację. Więcej informacji o dostępnych funkcjach znajduje się w poniższej tabeli.
3. Aby zastosować wiele transformacji, kliknij pozycję **Dodaj transformację**. Można zastosować maksymalnie dwa przekształcenia do żądania. Na przykład można najpierw wyodrębnić prefiks wiadomości e-mail `user.mail`. Następnie wprowadź wielką literę w postaci ciągu.

   ![Edytuj wartość NameID (identyfikator nazwy)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Aby przekształcić oświadczenia, można użyć następujących funkcji.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny z adresu e-mail lub głównej nazwy użytkownika. Spowoduje to wyodrębnienie tylko pierwszej części nazwy użytkownika, która jest przenoszona przez (na przykład "joe_smith", a nie joe_smith@contoso.com). |
| **Join ()** | Tworzy nową wartość przez Sprzęganie dwóch atrybutów. Opcjonalnie można użyć separatora między dwoma atrybutami. W przypadku transformacji roszczeń NameID przyłączanie jest ograniczone do zweryfikowanej domeny. Jeśli wybrana wartość identyfikatora użytkownika ma domenę, Wyodrębnij nazwę użytkownika w celu dołączenia wybranej zweryfikowanej domeny. Na przykład w przypadku wybrania adresu e-mail (joe_smith@contoso.com) jako wartości identyfikatora użytkownika i wybrania opcji contoso.onmicrosoft.com jako zweryfikowanej domeny spowoduje to joe_smith@contoso.onmicrosoft.com. |
| **ToLower ()** | Konwertuje znaki wybranego atrybutu do małych liter. |
| **ToUpper ()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |
| **Zawiera ()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe pasują do określonej wartości. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma żadnego dopasowania.<br/>Na przykład jeśli chcesz emitować, gdzie wartość jest adresem e-mail użytkownika, jeśli zawiera on domenę "@contoso.com", w przeciwnym razie chcesz utworzyć dane wyjściowe głównej nazwy użytkownika. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (dane wejściowe)* : User. email<br/>*Wartość*: "@contoso.com"<br/>Parametr 2 (dane wyjściowe): User. email<br/>Parametr 3 (dane wyjściowe w przypadku braku dopasowania): User. userPrincipalName |
| **EndWith()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe kończą się określoną wartością. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma żadnego dopasowania.<br/>Na przykład, jeśli chcesz emitować, gdzie wartość jest identyfikator pracownika użytkownika, jeśli identyfikator pracownika zostanie zakończony znakiem "000", w przeciwnym razie chcesz wyprowadzić atrybut rozszerzenia. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (dane wejściowe)* : User. IDPracownika<br/>*Wartość*: "000"<br/>Parametr 2 (Output): User. IDPracownika<br/>Parametr 3 (dane wyjściowe w przypadku braku dopasowania): User. extensionAttribute1 |
| **StartWith()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe zaczynają się od określonej wartości. W przeciwnym razie można określić inne dane wyjściowe, jeśli nie ma żadnego dopasowania.<br/>Na przykład jeśli chcesz emitować, gdzie wartość jest identyfikator pracownika użytkownika, jeśli kraj/region zaczyna się od "US", w przeciwnym razie chcesz wyprowadzić atrybut rozszerzenia. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (wejście)* : User. Country<br/>*Wartość*: "US"<br/>Parametr 2 (Output): User. IDPracownika<br/>Parametr 3 (dane wyjściowe w przypadku braku dopasowania): User. extensionAttribute1 |
| **Wyodrębnij () — po dopasowaniu** | Zwraca podciąg po dopasowaniu określonej wartości.<br/>Na przykład, jeśli wartość wejściowa to "Finance_BSimon", odpowiadająca wartość to "Finance_", wówczas wynikiem żądania jest "BSimon". |
| **Extract () — przed dopasowaniem** | Zwraca podciąg, dopóki nie pasuje do określonej wartości.<br/>Na przykład, jeśli wartość wejściowa to "BSimon_US", odpowiadająca wartość to "_US", wówczas wynikiem żądania jest "BSimon". |
| **Extract () — między dopasowaniem** | Zwraca podciąg, dopóki nie pasuje do określonej wartości.<br/>Na przykład, jeśli wartością wejściową jest "Finance_BSimon_US", pierwszą zgodną wartością jest "Finance_", druga zgodna wartość to "_US", a następnie dane wyjściowe żądania to "BSimon". |
| **ExtractAlpha () — prefiks** | Zwraca wartość alfabetyczną prefiksu ciągu.<br/>Na przykład, jeśli wartość wejściowa to "BSimon_123", zwraca "BSimon". |
| **ExtractAlpha () — sufiks** | Zwraca alfabetyczną część ciągu.<br/>Na przykład, jeśli wartość wejściowa to "123_Simon", zwraca "Simon". |
| **ExtractNumeric () — prefiks** | Zwraca część liczbową prefiksu ciągu.<br/>Na przykład, jeśli wartość wejściowa to "123_BSimon", zwraca "123". |
| **ExtractNumeric () — sufiks** | Zwraca numeryczną część ciągu.<br/>Na przykład, jeśli wartość wejściowa to "BSimon_123", zwraca "123". |
| **IfEmpty()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe mają wartość null lub są puste.<br/>Na przykład jeśli chcesz, aby dane wyjściowe były przechowywane w atrybucie ExtensionAttribute, jeśli identyfikator pracownika danego użytkownika jest pusty. W tym celu należy skonfigurować następujące wartości:<br/>Parametr 1 (dane wejściowe): User. IDPracownika<br/>Parametr 2 (Output): User. extensionAttribute1<br/>Parametr 3 (dane wyjściowe w przypadku braku dopasowania): User. IDPracownika |
| **IfNotEmpty()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe nie mają wartości null ani nie są puste.<br/>Na przykład jeśli chcesz, aby dane wyjściowe były przechowywane w atrybucie ExtensionAttribute, jeśli identyfikator pracownika danego użytkownika nie jest pusty. W tym celu należy skonfigurować następujące wartości:<br/>Parametr 1 (dane wejściowe): User. IDPracownika<br/>Parametr 2 (Output): User. extensionAttribute1 |

Jeśli potrzebujesz dodatkowych transformacji, prześlij swój pomysł na [forum opinii w usłudze Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) w kategorii *aplikacji SaaS* .

## <a name="emitting-claims-based-on-conditions"></a>Emitowanie oświadczeń na podstawie warunków

Możesz określić źródło żądania na podstawie typu użytkownika i grupy, do której należy użytkownik. 

Typ użytkownika może:
- **Dowolny**: Wszyscy użytkownicy mogą uzyskiwać dostęp do aplikacji.
- **Członkowie**: natywny element członkowski dzierżawy
- **Wszyscy Goście**: użytkownik jest przełączany z zewnętrznej organizacji z usługą Azure AD lub bez niej.
- **Goście usługi AAD**: użytkownik-Gość należy do innej organizacji korzystającej z usług Azure AD.
- **Zewnętrzni Goście**: Użytkownik będący gościem należy do zewnętrznej organizacji, która nie ma usługi Azure AD.


Jeden z nich jest przydatny, gdy źródło roszczeń jest inne dla gościa, a pracownik uzyskujący dostęp do aplikacji. Można określić, że jeśli użytkownik jest pracownikiem, NameID pochodzi od użytkownika. poczta e-mail, ale jeśli użytkownik jest gościem, NameID pochodzi od użytkownika. extensionAttribute1.

Aby dodać warunek roszczeń:

1. W obszarze **Zarządzanie roszczeń**rozwiń warunki dotyczące roszczeń.
2. Wybierz typ użytkownika.
3. Wybierz grupy, do których należy użytkownik. Można wybrać maksymalnie 10 unikatowych grup dla wszystkich oświadczeń dla danej aplikacji. 
4. Wybierz **Źródło** , do którego ma zostać pobrana wartość. Można wybrać atrybut użytkownika z listy rozwijanej atrybutów źródłowych lub zastosować transformację do atrybutu użytkownika przed wyemitowaniem go jako roszczeń.

Kolejność, w której zostały dodane warunki, jest ważna. Usługa Azure AD szacuje warunki od góry do dołu, aby zdecydować, która wartość ma być emitowana w ramach tego żądania. 

Na przykład Brita Simon jest użytkownikiem-gościem w dzierżawie contoso. Należy do innej organizacji, która również korzysta z usługi Azure AD. Zgodnie z poniższą konfiguracją aplikacji Fabrikam, gdy Brita próbuje zalogować się do firmy Fabrikam, usługa Azure AD oceni warunki w następujący sposób.

Najpierw usługa Azure AD weryfikuje, czy typ użytkownika Brita jest `All guests`. Ponieważ jest to prawdziwe, usługa Azure AD przypisuje Źródło dla tego żądania do `user.extensionattribute1`. Następnie usługa Azure AD weryfikuje, czy typ użytkownika Brita jest `AAD guests`, ponieważ jest to również wartość true, usługa Azure AD przypisuje Źródło dla tego żądania `user.mail`. Na koniec zgłoszenie jest emitowane z wartością `user.email` dla Brita.

![Konfiguracja warunkowa oświadczeń](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacjami w usłudze Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurowanie logowania jednokrotnego w aplikacjach, które nie znajdują się w galerii aplikacji usługi Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Rozwiązywanie problemów z logowaniem jednokrotnym opartym na protokole SAML](howto-v1-debug-saml-sso-issues.md)
