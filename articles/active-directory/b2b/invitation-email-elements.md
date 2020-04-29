---
title: Elementy wiadomości e-mail z zaproszeniem B2B — Azure Active Directory | Microsoft Docs
description: Azure Active Directory szablonu wiadomości e-mail z zaproszeniem do współpracy B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81407212"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementy wiadomości e-mail z zaproszeniem do współpracy B2B — Azure Active Directory

Wiadomości e-mail z zaproszeniem są składnikiem krytycznym umożliwiającym partnerom korzystanie z partnerów w usłudze Azure AD. [Nie jest wymagane, aby wysłać wiadomość e-mail z prośbą o zaproszenie kogoś przy użyciu współpracy B2B](add-user-without-invite.md). dzięki temu użytkownik może wszystkie informacje potrzebne do podjęcia decyzji o zaakceptowaniu zaproszenia. Podaje im również link, który zawsze może odwoływać się w przyszłości, gdy muszą powrócić do zasobów.

![Zrzut ekranu przedstawiający wiadomość e-mail z zaproszeniem B2B](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Ten nowy szablon wiadomości e-mail jest nadal wdrażany dla wszystkich dzierżawców, więc niektórzy dzierżawcy nadal korzystają ze starszego projektu. Na koniec 2020 maja zaproszenia od wszystkich dzierżawców będą korzystać z tego szablonu.

## <a name="explaining-the-email"></a>Objaśnienie wiadomości e-mail

Przyjrzyjmy się kilku elementom wiadomości e-mail, aby dowiedzieć się, jak najlepiej wykorzystać swoje możliwości.

### <a name="subject"></a>Podmiot

Temat wiadomości e-mail jest następujący:

&lt;Nazwa&gt; użytkownika zaprosiła Cię do dostępu do aplikacji w swojej organizacji.

### <a name="from-address"></a>Adres od

Użyjemy wzorca przypominającego serwis LinkedIn dla adresu od. Ten wzorzec powinien wyjaśnić, że mimo że wiadomość e-mail pochodzi z invites@microsoft.com, zaproszenie pochodzi z innej organizacji. Format to <invites@microsoft.com> : zaproszenia firmy Microsoft lub zaproszenia firmy Microsoft w imieniu &lt;dzierżawcy.&gt; <invites@microsoft.com> 

### <a name="reply-to"></a>Odpowiedz na

Wiadomość e-mail z odpowiedzią jest ustawiana na adres e-mail osoby przesyłającej, gdy jest ona dostępna, aby odpowiedzieć na wiadomość e-mail z powrotem do osoby atakującej.

### <a name="phishing-warning"></a>Ostrzeżenie dotyczące wyłudzania informacji

Wiadomość e-mail rozpoczyna się od krótkiego ostrzeżenia dla użytkownika dotyczącego wyłudzania informacji, informując o tym, że powinni akceptować tylko zaproszenia, które oczekują. Dobrym sposobem jest upewnienie się, że partnerzy, którym zapraszasz, nie zostaną zaproszeni przez zaproszenie przez podanie ich przed czasem.

![Obraz ostrzeżenia dotyczącego wyłudzania informacji w wiadomości e-mail](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informacje osoby zapraszające

Wiadomość e-mail zawiera informacje o zapraszaniu i organizacji, z których są wysyłane zaproszenia. Obejmuje to nazwę i adres e-mail nadawcy, a także nazwę i domenę podstawową skojarzoną z organizacją. Wszystkie te informacje powinny pomóc uczestnikom zapraszać w podejmowaniu świadomej decyzji o zaakceptowaniu zaproszenia.

![Obraz informacji osoby zaproszonej w wiadomości e-mail](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Wiadomość z zaproszeniem

Jeśli zapraszający zawiera komunikat w ramach zaproszenia, gdy [zaprasza użytkownika-gościa do katalogu, grupy lub aplikacji](add-users-administrator.md) lub gdy [używają interfejsu API zaproszenia](customize-invitation-api.md), komunikat zostanie wyróżniony w sekcji głównej wiadomości e-mail. Jest również dołączony do nazwy i obrazu profilu osoby zapraszanej, jeśli je ustawili. Sam komunikat jest obszarem tekstu, dlatego ze względów bezpieczeństwa nie przetwarza tagów HTML.

![Obraz komunikatu zaproszenia w wiadomości e-mail](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Zaakceptuj przycisk i adres URL przekierowania

W następnej sekcji wiadomości e-mail znajdują się informacje o tym, gdzie zapraszanie zostanie wykonane po zaakceptowaniu zaproszenia, a także przycisku, aby to zrobić.  W przyszłości zapraszanie może zawsze używać tego linku do bezpośredniego powrotu do zasobów.

![Obraz przycisku Akceptuj i adresu URL przekierowania w wiadomości e-mail](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Sekcja Stopka

Stopka zawiera więcej informacji na temat wysyłanego zaproszenia. Zawsze istnieje opcja zapraszania do blokowania przyszłych zaproszeń. Jeśli organizacja [ustawi zasady zachowania poufności informacji](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area), w tym miejscu zostanie wyświetlony link do instrukcji.  W przeciwnym razie Uwaga wskazuje, że organizacja nie ustawił zasad zachowania poufności informacji.

![Obraz sekcji stopki w wiadomości e-mail](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>Sposób ustalania języka

Język prezentowany użytkownikowi gość w wiadomości e-mail z zaproszeniem jest określany na podstawie następujących ustawień. Te ustawienia są wymienione w kolejności pierwszeństwa. Jeśli ustawienie nie zostanie skonfigurowane, następne ustawienie na liście określi język.

- Właściwość **messageLanguage** obiektu [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) , jeśli jest używany interfejs API tworzenia zaproszenia
-   Właściwość **preferredLanguage** określona w [obiekcie użytkownika](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) gościa
-   **Język powiadomień** ustawiony we właściwościach dzierżawy głównej użytkownika-gościa (tylko dla dzierżawców usługi Azure AD)
-   **Język powiadomień** ustawiony we właściwościach dzierżawy zasobu

Jeśli żaden z tych ustawień nie zostanie skonfigurowany, język jest domyślnie ustawiony na angielski (Stany Zjednoczone).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi współpracy B2B w usłudze Azure AD:

- [Co to jest współpraca B2B w usłudze Azure AD](what-is-b2b.md)
- [Jak Administratorzy Azure Active Directory dodawać użytkowników współpracy B2B?](add-users-administrator.md)
- [Jak Pracownicy przetwarzający informacje dodają użytkowników współpracy B2B?](add-users-information-worker.md)
- [Realizacja zaproszenia do współpracy B2B](redemption-experience.md)
- [Dodawanie użytkowników współpracy B2B bez zaproszenia](add-user-without-invite.md)
