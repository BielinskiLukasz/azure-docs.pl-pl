---
title: Przejęcie przez administratora niezarządzanego katalogu — Azure AD | Microsoft Docs
description: Jak przejąć nazwę domeny DNS w niezarządzanej organizacji usługi Azure AD (dzierżawa w tle).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a0697e151c50b9722fef908eeb2c7498503b8c0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027369"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Przejmowanie niezarządzanego katalogu jako administrator w Azure Active Directory

W tym artykule opisano dwa sposoby przejęcia nazwy domeny DNS w niezarządzanym katalogu w Azure Active Directory (Azure AD). Gdy użytkownik samoobsługi rejestruje się w usłudze w chmurze, która korzysta z usługi Azure AD, jest dodawany do niezarządzanego katalogu usługi Azure AD na podstawie swojej domeny poczty e-mail. Aby uzyskać więcej informacji na temat rejestracji samoobsługowej lub "wirusa" dla usługi, zobacz [co to jest rejestracja samoobsługowa w celu Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Zdecyduj, jak chcesz przejąć niezarządzany katalog
Podczas procesu przejęcia przez administratora możesz udowodnić własność w sposób opisany w artykule [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md). W kolejnych sekcjach objaśniono środowisko pracy administratora bardziej szczegółowo, ale w tym miejscu znajduje się podsumowanie:

* W przypadku wykonania [„wewnętrznego” przejęcia przez administratora](#internal-admin-takeover) niezarządzanego katalogu platformy Azure użytkownik jest dodawany jako administrator globalny katalogu niezarządzanego. Żadni inni użytkownicy, domeny ani plany usługi nie są migrowane do żadnego innego katalogu, którym administruje.

* W przypadku wykonania [„zewnętrznego” przejęcia przez administratora](#external-admin-takeover) niezarządzanego katalogu platformy Azure dodajesz nazwę domeny DNS katalogu niezarządzanego do swojego zarządzanego katalogu platformy Azure. W przypadku dodania nazwy domeny mapowanie użytkowników do zasobów jest tworzone w Twoim zarządzanym katalogu usługi Azure, dzięki czemu użytkownicy mogą nadal bez przeszkód uzyskiwać dostęp do usług. 

## <a name="internal-admin-takeover"></a>Przejęcie przez administratora wewnętrznego

Niektóre produkty, które obejmują programy SharePoint i OneDrive, takie jak Office 365, nie obsługują przejęcia zewnętrznego. Jeśli jesteś Twoim scenariuszem lub jesteś administratorem i chcesz przejąć niezarządzaną dzierżawę lub "w tle" utworzoną przez użytkowników, którzy korzystali z rejestracji samoobsługowej, możesz to zrobić przy użyciu wewnętrznego przejęcia administratora.

1. Utwórz kontekst użytkownika w niezarządzanej dzierżawie za pomocą rejestracji w usłudze Power BI. W przypadku wygody przykładu te kroki zakładają tę ścieżkę.

2. Otwórz [witrynę Power BI](https://powerbi.com) i wybierz pozycję **Rozpocznij bezpłatnie**. Wprowadź konto użytkownika, które używa nazwy domeny dla organizacji; na przykład `admin@fourthcoffee.xyz`. Po wprowadzeniu kodu weryfikacyjnego Sprawdź swój adres e-mail, aby uzyskać kod potwierdzający.

3. W wiadomości e-mail z potwierdzeniem z Power BI wybierz pozycję **tak**.

4. Zaloguj się do [Centrum administracyjnego Microsoft 365](https://portal.office.com/admintakeover) przy użyciu konta użytkownika Power BI. Zostanie wyświetlony komunikat z instrukcjami, aby zostać **administratorem** nazwy domeny, która została już zweryfikowana w niezarządzanej dzierżawie. Wybierz pozycję **tak, chcę być administratorem**.
  
   ![pierwszy zrzut ekranu, który ma być administratorem](./media/domains-admin-takeover/become-admin-first.png)
  
5. Dodaj rekord TXT, aby udowodnić, że jesteś posiadaczem nazwy domeny **fourthcoffee. xyz** w rejestratorze nazw domen. W tym przykładzie jest to GoDaddy.com.
  
   ![Dodawanie rekordu TXT dla nazwy domeny](./media/domains-admin-takeover/become-admin-txt-record.png)

Gdy rekordy TXT DNS są weryfikowane na rejestratorze nazw domen, można zarządzać dzierżawą usługi Azure AD.

Po wykonaniu powyższych kroków jesteś teraz administratorem globalnym z czwartej dzierżawy kawowej w pakiecie Office 365. Aby zintegrować nazwę domeny z innymi usługami platformy Azure, możesz usunąć ją z pakietu Office 365 i dodać ją do innej zarządzanej dzierżawy na platformie Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Dodawanie nazwy domeny do zarządzanej dzierżawy w usłudze Azure AD

1. Otwórz [Centrum administracyjne Microsoft 365](https://admin.microsoft.com).
2. Wybierz kartę **Użytkownicy** , a następnie utwórz nowe konto użytkownika o nazwie *User\@fourthcoffeexyz.onmicrosoft.com* , która nie używa niestandardowej nazwy domeny. 
3. Upewnij się, że nowe konto użytkownika ma uprawnienia administratora globalnego dla dzierżawy usługi Azure AD.
4. Otwórz kartę **domeny** w centrum administracyjnym Microsoft 365, wybierz nazwę domeny i wybierz pozycję **Usuń**. 
  
   ![Usuń nazwę domeny z pakietu Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Jeśli w pakiecie Office 365 istnieją wszyscy użytkownicy lub grupy, które odwołują się do usuniętej nazwy domeny, należy zmienić ich nazwy na domenę. onmicrosoft.com. Jeśli wymusisz usunięcie nazwy domeny, wszyscy użytkownicy będą automatycznie zmieniać nazwy, w tym przykładzie do *user\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta, które jest administratorem globalnym dzierżawy usługi Azure AD.
  
7. Wybierz opcję **niestandardowe nazwy domen**, a następnie Dodaj nazwę domeny. Musisz wprowadzić rekordy TXT DNS, aby zweryfikować własność nazwy domeny. 
  
   ![domena została zweryfikowana jako dodana do usługi Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Wszyscy użytkownicy Power BI lub usługi Azure Rights Management, którzy mają licencje przypisane do dzierżawy pakietu Office 365, muszą zapisać swoje pulpity nawigacyjne, jeśli nazwa domeny zostanie usunięta. Muszą oni zalogować się przy użyciu nazwy użytkownika, takiej jak *user\@fourthcoffeexyz.onmicrosoft.com* , a nie *użytkownika\@fourthcoffee. xyz*.

## <a name="external-admin-takeover"></a>Przejęcie przez administratora zewnętrznego

Jeśli dzierżawa jest już zarządzana przy użyciu usług platformy Azure lub pakietu Office 365, nie można dodać niestandardowej nazwy domeny, jeśli została ona już zweryfikowana w innej dzierżawie usługi Azure AD. Jednak z poziomu zarządzanej dzierżawy w usłudze Azure AD można przejąć niezarządzaną dzierżawę jako przejęcie zewnętrzną przez administratora. Ogólna procedura jest następująca: [Dodawanie domeny niestandardowej do usługi Azure AD](../fundamentals/add-custom-domain.md).

Po sprawdzeniu własności nazwy domeny usługa Azure AD usuwa nazwę domeny z niezarządzanej dzierżawy i przenosi ją do istniejącej dzierżawy. Przejęcie zewnętrzną przez administratora niezarządzanego katalogu wymaga tego samego procesu weryfikacji nazw DNS w ramach przejęcia przez administratora wewnętrznego. Różnica polega na tym, że następujące są również przenoszone za pomocą nazwy domeny:

- Użytkownicy
- Subskrypcje
- Przypisania licencji

### <a name="support-for-external-admin-takeover"></a>Obsługa przejęcia przez administratora zewnętrznego
Przejęcie przez administratora zewnętrznego jest obsługiwane przez następujące Usługi online:

- Azure Rights Management
- Exchange Online

Obsługiwane plany usługi obejmują:

- Usługa PowerApps bezpłatnie
- PowerFlow bezpłatnie
- Usługi RMS dla użytkowników indywidualnych
- Microsoft Stream
- Dynamics 365 — bezpłatna wersja próbna

Przejęcie zewnętrznych administratorów nie jest obsługiwane dla żadnej usługi, która ma plany usług, w tym SharePoint, OneDrive lub Skype dla firm; na przykład za poorednictwem bezpłatnej subskrypcji pakietu Office. 

Opcjonalnie można użyć [opcji **ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) , aby usunąć nazwę domeny z niezarządzanej dzierżawy i sprawdzić ją w żądanej dzierżawie. 

#### <a name="more-information-about-rms-for-individuals"></a>Więcej informacji na temat usługi RMS dla użytkowników indywidualnych

W przypadku usługi [RMS dla użytkowników indywidualnych](/azure/information-protection/rms-for-individuals), gdy niezarządzana dzierżawa znajduje się w tym samym regionie co posiadana dzierżawa, automatycznie utworzona [Azure Information Protection klucz dzierżawy](/azure/information-protection/plan-implement-tenant-key) i [domyślne szablony ochrony](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) są również przenoszone przy użyciu nazwy domeny.

Klucza i szablonów nie są przenoszone, gdy niezarządzana dzierżawa znajduje się w innym regionie. Jeśli na przykład niezarządzana dzierżawa znajduje się w Europie, a posiadana organizacja jest w Ameryka Północna.

Mimo że usługi RMS dla użytkowników indywidualnych są przeznaczone do obsługi uwierzytelniania usługi Azure AD w celu otwierania chronionej zawartości, nie uniemożliwiają one również ochrony zawartości. Jeśli użytkownicy przeprowadzili ochronę zawartości za pomocą subskrypcji usługi RMS dla użytkowników indywidualnych, a klucze i szablony nie zostały przeniesione przez program, zawartość nie jest dostępna po przejęciu domeny.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Polecenia cmdlet programu PowerShell dla usługi Azure AD dla opcji ForceTakeover
Można wyświetlić te polecenia cmdlet używane w [przykładowym programie PowerShell](#powershell-example).

parametr | Sposób użycia
------- | -------
`connect-msolservice` | Po wyświetleniu monitu zaloguj się do zarządzanej dzierżawy.
`get-msoldomain` | Wyświetla nazwy domen skojarzone z bieżącą dzierżawą.
`new-msoldomain –name <domainname>` | Dodaje nazwę domeny do dzierżawy jako niezweryfikowane (nie przeprowadzono jeszcze weryfikacji usługi DNS).
`get-msoldomain` | Nazwa domeny jest teraz uwzględniona na liście nazw domen skojarzonych z zarządzaną dzierżawą, ale jest wymieniona jako **niezweryfikowana**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Zawiera informacje, które mają zostać umieszczone w nowym rekordzie TXT systemu DNS dla domeny (MS = XXXXX). Weryfikacja może nie nastąpić natychmiast, ponieważ trwa pewien czas na propagację rekordu TXT, więc poczekaj kilka minut przed rozważeniem opcji **-ForceTakeover** . 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Jeśli nazwa domeny nie jest jeszcze zweryfikowana, możesz kontynuować z opcją **-ForceTakeover** . Sprawdza, czy rekord TXT został utworzony i rozpoczyna proces przejęcia.<li>Opcja **-ForceTakeover** należy dodać do polecenia cmdlet tylko w przypadku wymuszenia przejęcia przez administratora zewnętrznego, na przykład gdy niezarządzana dzierżawa ma zablokowanie przejęcia przez usługę Office 365.
`get-msoldomain` | Na liście domen jest teraz wyświetlana nazwa domeny, która została **zweryfikowana**.

> [!NOTE]
> Niezarządzana organizacja usługi Azure AD jest usuwana 10 dni po skorzystaniu z opcji zewnętrznego wymuszenia przejęcia.

### <a name="powershell-example"></a>Przykład programu PowerShell

1. Połącz się z usługą Azure AD przy użyciu poświadczeń, które zostały użyte do udzielenia odpowiedzi na ofertę samoobsługi:
   ```powershell
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
   ```
2. Pobierz listę domen:
  
   ```powershell
    Get-MsolDomain
   ```
3. Uruchom polecenie cmdlet Get-MsolDomainVerificationDns, aby utworzyć wyzwanie:
   ```powershell
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Skopiuj wartość (wyzwanie) zwracaną z tego polecenia. Na przykład:
   ```powershell
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. W publicznej przestrzeni nazw DNS Utwórz rekord TXT DNS zawierający wartość skopiowaną w poprzednim kroku. Nazwa tego rekordu to nazwa domeny nadrzędnej, więc jeśli ten rekord zasobu zostanie utworzony przy użyciu roli DNS z systemu Windows Server, pozostaw pustą nazwę rekordu i po prostu wklej wartość w polu tekstowym.
6. Uruchom polecenie cmdlet Confirm-MsolDomain, aby zweryfikować wyzwanie:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
   ```
  
   Na przykład:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
   ```

Pomyślne wyzwanie powraca do monitu bez błędu.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
