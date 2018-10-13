---
title: Stany użytkowników uwierzytelniania wieloskładnikowego Azure firmy Microsoft
description: Więcej informacji na temat stanów użytkowników w usłudze Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 302cf047ee1ffea685a939bddee84551de7042ec
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166767"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Jak, które wymuszają weryfikację dwuetapową dla użytkownika
Można wybrać jedno z dwóch metod do wymagania weryfikacji dwuetapowej, które wymagają przy użyciu konta administratora globalnego. Pierwszym z nich jest umożliwienie każdy użytkownik usługi Azure Multi-Factor Authentication (MFA). Gdy użytkownicy są włączone indywidualnie, wykonują weryfikacji dwuetapowej każdym logowaniu (z pewnymi wyjątkami, takie jak podczas logowania z zaufanego adresu IP adresów lub _zapamiętanych urządzeniach_ jest włączona funkcja). Drugą opcją jest, aby skonfigurować zasady dostępu warunkowego, które wymagają weryfikacji dwuetapowej pod pewnymi warunkami.

> [!TIP]
> Wybierz jedną z następujących metod, które wymuszają weryfikację dwuetapową, nie obydwa. Włączanie usługi Azure Multi-Factor Authentication użytkownik zastępuje wszystkie zasady dostępu warunkowego.

## <a name="choose-how-to-enable"></a>Wybierz sposób włączania

**Włączone przez zmianę stanu użytkownika** — to jest tradycyjnych metod wymagania weryfikacji dwuetapowej i jest omówiona w tym artykule. Działa z zarówno usługi Azure MFA w chmurze i serwera Azure MFA. Za pomocą tej metody wymaga od użytkowników weryfikacji dwuetapowej **za każdym razem, gdy** Zaloguj się i przesłania zasady dostępu warunkowego.

Obsługiwane przez zasady dostępu warunkowego — jest to najbardziej elastyczny sposób Włącz weryfikację dwuetapową dla użytkowników. Włączanie przy użyciu tylko zasad dostępu warunkowego działa w przypadku usługi Azure MFA w chmurze i jest funkcją premium usługi Azure AD. Więcej informacji na temat tej metody można znaleźć w [wdrażanie oparte na chmurze usługi Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

Obsługiwane przez usługę Azure AD Identity Protection — ta metoda używa zasad ryzyka usługi Azure AD Identity Protection do weryfikacji dwuetapowej oparte tylko na ryzyko logowania dla wszystkich aplikacji w chmurze. Ta metoda wymaga licencji usługi Azure Active Directory P2. Więcej informacji na temat tej metody można znaleźć w [usługi Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Więcej informacji na temat licencji i ceny można znaleźć na [usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) i [uwierzytelnianie wieloskładnikowe](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) stronach z cennikami.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Włącz usługę Azure MFA, zmieniając stanu użytkownika

Konta użytkowników w usłudze Azure Multi-Factor Authentication mają następujące trzy różne stany:

| Stan | Opis | Aplikacje korzystające z przeglądarki, których to dotyczy | Aplikacje przeglądarki, których to dotyczy | Nowoczesnego uwierzytelniania, których to dotyczy |
|:---:|:---:|:---:|:--:|:--:|
| Disabled (Wyłączony) |Stan domyślny dla nowego użytkownika, nie są zarejestrowane w usłudze Azure MFA. |Nie |Nie |Nie |
| Enabled (Włączony) |Użytkownik został zarejestrowany w usłudze Azure MFA, ale nie została zarejestrowana. Otrzyma monit o zarejestrować przy następnym logowaniu. |Nie.  One nadal działać do momentu zakończenia procesu rejestracji. | Tak. Po wygaśnięciu sesji, wymagana jest rejestracja usługi Azure MFA.| Tak. Po wygaśnięciu ważności tokenu dostępu, wymagana jest rejestracja usługi Azure MFA. |
| Enforced (Wymuszony) |Użytkownik został zarejestrowany i zakończeniu procesu rejestracji dla usługi Azure MFA. |Tak. Aplikacje wymagające wprowadzenia hasła aplikacji. |Tak. Usługa Azure MFA jest wymagana podczas logowania. | Tak. Usługa Azure MFA jest wymagana podczas logowania. |

Stan użytkownika wskazuje, czy administrator ma zarejestrowane je w usłudze Azure MFA i tego, czy ich ukończenie procesu rejestracji.

Wszyscy użytkownicy zaczynają *wyłączone*. Podczas rejestrowania się użytkowników w usłudze Azure MFA, jego stan zmieni się na *włączone*. Gdy włączonych użytkowników zalogować i dokończyć proces rejestracji, jego stan zmieni się na *wymuszone*.  

### <a name="view-the-status-for-a-user"></a>Wyświetlanie stanu użytkownika

Aby uzyskać dostęp do strony, w którym można wyświetlać i zarządzać stanów użytkowników, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
   ![Wybierz uwierzytelnianie wieloskładnikowe](./media/howto-mfa-userstates/selectmfa.png)
4. Zostanie otwarta nowa strona, wyświetlająca stanu użytkownika.
   ![Stan użytkownika usługi Multi-Factor authentication — zrzut ekranu](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Zmiana stanu użytkownika

1. Użyj powyższych kroków, aby uzyskać dostęp do usługi Azure Multi-Factor Authentication **użytkowników** strony.
2. Znajdź użytkownika, który chcesz włączyć dla usługi Azure MFA. Konieczne może być zmiana widoku w górnej części.
   ![Znajdź użytkownika — zrzut ekranu](./media/howto-mfa-userstates/enable1.png)
3. Zaznacz pole obok nazwy użytkownika.
4. Po prawej stronie w obszarze **Szybkie kroki**, wybierz **Włącz** lub **wyłączyć**.
   ![Włącz wybranego użytkownika — zrzut ekranu](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Włączone* użytkownicy są automatycznie przełączone do *wymuszone* podczas rejestrowania dla usługi Azure MFA. Wykonaj ręcznie nie zmiany stanu użytkownika do *wymuszone*.

5. Potwierdź wybór w otwartym oknie podręcznym.

Po włączeniu użytkowników, Powiadamiaj ich za pośrednictwem poczty e-mail. Poinformuj ich, że zostanie wyświetlony monit do zarejestrowania przy następnym logowaniu. Ponadto jeśli Twoja organizacja używa aplikacji niekorzystających z przeglądarki, które nie obsługują nowoczesnego uwierzytelniania, muszą utworzyć hasła aplikacji. Możesz również uwzględnić łącze do [przewodnik dla użytkowników końcowych usługi Azure MFA](../user-help/multi-factor-authentication-end-user.md) aby pomóc im rozpocząć pracę.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby zmienić stan użytkownika przy użyciu [usługi Azure AD PowerShell](/powershell/azure/overview), zmień `$st.State`. Istnieją trzy stany:

* Enabled (Włączony)
* Enforced (Wymuszony)
* Disabled (Wyłączony)  

Nie bezpośrednio do przenoszenia użytkowników *wymuszone* stanu. Jeśli to zrobisz, nie opartych na przeglądarce aplikacji przestają działać, ponieważ użytkownik nie przeszli rejestracja w usłudze Azure MFA i uzyskać [hasła aplikacji](howto-mfa-mfasettings.md#app-passwords).

Zainstaluj moduł najpierw, przy użyciu:

       Install-Module MSOnline
       
> [!TIP]
> Nie należy zapominać, połączyć się najpierw, przy użyciu **Connect-MsolService**


Przy użyciu programu PowerShell jest dobrym rozwiązaniem, gdy trzeba zbiorcze Włączanie użytkowników. Utwórz skrypt programu PowerShell, do listy użytkowników w pętli, która umożliwia im:

        Import-Module MSOnline
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Poniższy skrypt znajduje się przykład:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }
    
Aby wyłączone uwierzytelnianie wieloskładnikowe należy użyć tego skryptu:

    Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
    
lub może być również skracać:

    Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()

## <a name="next-steps"></a>Kolejne kroki

Dlaczego został użytkownika zostanie wyświetlony monit lub bez wyświetlania monitu o wykonać uwierzytelnianie wieloskładnikowe? Zobacz sekcję [raport logowania usługi Azure AD w raportach w dokumencie usługi Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Aby skonfigurować dodatkowe ustawienia, takie jak zaufane adresy IP, niestandardowe wiadomości głosowe i alertów oszustwa, zobacz artykuł [ustawienia skonfigurować uwierzytelnianie wieloskładnikowe systemu Azure](howto-mfa-mfasettings.md)

Informacje o zarządzaniu ustawienia użytkownika dla usługi Azure Multi-Factor Authentication można znaleźć w artykule [Zarządzanie ustawieniami użytkownika przy użyciu usługi Azure Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md)
