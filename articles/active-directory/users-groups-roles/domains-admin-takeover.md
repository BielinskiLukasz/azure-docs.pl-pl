---
title: Przejęcia przez administratora w katalogu niezarządzanego lub w tle dzierżawy usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak przejęcie nazwy domeny DNS w katalogu niezarządzanego (dzierżawy w tle) w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 8e11d370f58028ee4c1665d84d0e48541e7d3344
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121196"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Przejąć niezarządzanego katalogu jako administrator usługi Azure Active Directory
W tym artykule opisano dwa sposoby na przejęcie nazwy domeny DNS w niezarządzanego katalogu usługi Azure Active Directory (Azure AD). Gdy użytkownik samoobsługi rejestruje się w usłudze w chmurze, która korzysta z usługi Azure AD, jest dodawany do niezarządzanego katalogu usługi Azure AD na podstawie swojej domeny poczty e-mail. Więcej informacji na temat samoobsługowego lub "wirusowego" rejestracji dla usługi, zobacz [co to jest Samoobsługowe tworzenie konta usługi Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Decyzja w sprawie sposobu przejmowanie katalogu niezarządzanego
Podczas procesu przejęcia przez administratora możesz udowodnić własność w sposób opisany w artykule [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md). W kolejnych sekcjach objaśniono środowisko pracy administratora bardziej szczegółowo, ale w tym miejscu znajduje się podsumowanie:

* W przypadku wykonania [„wewnętrznego” przejęcia przez administratora](#internal-admin-takeover) niezarządzanego katalogu platformy Azure użytkownik jest dodawany jako administrator globalny katalogu niezarządzanego. Żadni inni użytkownicy, domeny ani plany usługi nie są migrowane do żadnego innego katalogu, którym administruje.

* W przypadku wykonania [„zewnętrznego” przejęcia przez administratora](#external-admin-takeover) niezarządzanego katalogu platformy Azure dodajesz nazwę domeny DNS katalogu niezarządzanego do swojego zarządzanego katalogu platformy Azure. W przypadku dodania nazwy domeny mapowanie użytkowników do zasobów jest tworzone w Twoim zarządzanym katalogu usługi Azure, dzięki czemu użytkownicy mogą nadal bez przeszkód uzyskiwać dostęp do usług. 

## <a name="internal-admin-takeover"></a>Przejęcia przez administratora wewnętrznego

Niektóre produkty, które zawierają SharePoint i OneDrive, takich jak Office 365 nie obsługuje zewnętrzne przejęcie. Przypadku danego scenariusza, czy jesteś administratorem i chcesz przejąć niezarządzanych, lub dzierżawy "cienia" Utwórz przez użytkowników, którzy użył rejestracji samoobsługowej, można to zrobić za pomocą przejęcia przez administratora wewnętrznego.

1. Utwórz kontekst użytkownika w niezarządzanej dzierżawy za pośrednictwem rejestracja za pomocą, takie jak Power BI. Dla wygody przykładowy procedura zakłada, że tej ścieżki.

2. Otwórz [witrynie usługi Power BI](https://powerbi.com) i wybierz **Rozpocznij za darmo**. Wprowadź konto użytkownika, który używa nazwy domeny organizacji; na przykład `admin@fourthcoffee.xyz`. Po wprowadzeniu w kod weryfikacyjny sprawdzenie poczty e-mail dla kodu potwierdzenia.

3. Wiadomość e-mail z potwierdzeniem z usługi Power BI, wybierz **tak, to ja**.

4. Zaloguj się do [Centrum administracyjnego usługi Office 365](https://portal.office.com/admintakeover) przy użyciu konta użytkownika usługi Power BI. Pojawi się komunikat z monitem o **Zostań administratorem** nazwy domeny, która została już zweryfikowana w niezarządzanej dzierżawy. Wybierz **tak, chcę być administratorem**.
  
  ![pierwszy zrzut ekranu przedstawiający Zostań administratorem](./media/domains-admin-takeover/become-admin-first.png)
  
5. Dodaj rekord TXT, aby potwierdzić, że jesteś właścicielem nazwy domeny **fourthcoffee.xyz** u rejestratora nazw domen. W tym przykładzie jest GoDaddy.com.
  
  ![Dodaj rekord txt dla nazwy domeny](./media/domains-admin-takeover/become-admin-txt-record.png)

Po zweryfikowaniu rekordy DNS TXT u rejestratora nazw domen można zarządzać dzierżawy usługi Azure AD.

Po ukończeniu powyższych kroków, jesteś teraz administratorem globalnym dzierżawy Fourth Coffee w usłudze Office 365. Aby zintegrować nazwę domeny z innymi usługami Azure, możesz usunąć go z usługi Office 365 i dodać go do innej dzierżawy zarządzanych na platformie Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Dodawanie nazwy domeny do zarządzanej dzierżawy w usłudze Azure AD

1. Otwórz [Centrum administracyjnego usługi Office 365](https://portal.office.com/admintakeover).
2. Wybierz **użytkowników** , a następnie utworzyć nowe konto użytkownika o nazwie, takich jak *user@fourthcoffeexyz.onmicrosoft.com* nie używa nazwy domeny niestandardowej. 
3. Upewnij się, że nowe konto użytkownika ma uprawnienia administratora globalnego dla dzierżawy usługi Azure AD.
4. Otwórz **domen** kartę w Centrum administracyjnym usługi Office 365, wybierz nazwę domeny i wybierz **Usuń**. 
  
  ![usunąć nazwę domeny w usłudze Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. W przypadku jakichkolwiek użytkowników lub grup w usłudze Office 365, odwołujące się do nazwy domeny usunięte, musi zostać zmieniona na. domeny onmicrosoft.com. Jeśli wymusisz usunięcie nazwy domeny, wszyscy użytkownicy są automatycznie zmieniono jego nazwę, w tym przykładzie *user@fourthcoffeexyz.onmicrosoft.com*.
  
6. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu konta administratora globalnego dla dzierżawy usługi Azure AD.
  
7. Wybierz **niestandardowe nazwy domen**, następnie dodać nazwę domeny. Musisz wprowadzić rekordy DNS TXT, aby zweryfikować prawa własności nazwy domeny. 
  
  ![Domena dodana do usługi Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Użytkowników usługi Power BI lub usługi Azure Rights Management, którzy mają licencje przypisane w ramach dzierżawy usługi Office 365 należy zapisać swoje pulpity nawigacyjne, jeśli nazwa domeny została usunięta. Muszą się zalogować się przy użyciu nazwy użytkownika, takich jak *user@fourthcoffeexyz.onmicrosoft.com* zamiast *user@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Przejęcia przez administratora zewnętrznych

Jeśli zarządzasz już dzierżawcy z usług platformy Azure lub usługi Office 365, nie można dodać niestandardową nazwę domeny, jeśli została już zweryfikowana w innej dzierżawie usługi Azure AD. Jednak z dzierżawy usługi zarządzanej w usłudze Azure AD możesz korzystać za pośrednictwem niezarządzanej dzierżawy jako przejęcia zewnętrznego administratora. Ogólna procedura jest zgodna artykuł [Dodawanie domeny niestandardowej z usługą Azure AD](../fundamentals/add-custom-domain.md).

Podczas weryfikowania własności nazwy domeny usługi Azure AD usuwa nazwę domeny z niezarządzanej dzierżawy i przenosi je do istniejącej dzierżawy. Przejęcia przez administratora zewnętrznych niezarządzanego katalogu wymaga tego samego procesu weryfikacji DNS TXT jako przejęcia przez administratora wewnętrznego. Różnica polega na tym, że następujące czynności są również przenoszone za pośrednictwem z nazwą domeny:

- Użytkownicy
- Subskrypcje
- Przypisań licencji

### <a name="support-for-external-admin-takeover"></a>Obsługa przejęcia przez administratora zewnętrznych
Przejęcia przez administratora zewnętrznych jest obsługiwana przez następujących usług online:

- Power BI
- Azure Rights Management
- Exchange Online

Plany obsługiwanej usługi obejmują:

- Bezpłatne usługi Power BI
- Power BI Pro
- Bezpłatne usługi PowerApps
- Bezpłatne PowerFlow
- Usługa RMS dla użytkowników indywidualnych
- Microsoft Stream
- Bezpłatna wersja próbna Dynamics 365

Przejęcia zewnętrznego administratora nie jest obsługiwana dla dowolnej usługi, która ma planów usług, które obejmują programu SharePoint, OneDrive lub Skype dla firm; na przykład za pomocą bezpłatnej subskrypcji pakietu Office lub podstawowej jednostki SKU pakietu Office. Opcjonalnie możesz skorzystać z [ **ForceTakeover** opcji](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) usuwanie nazwy domeny z niezarządzanej dzierżawy i weryfikowania je w żądanej dzierżawy. Ta opcja ForceTakeover nie będzie przenieśliśmy użytkowników, czy zachować dostęp do subskrypcji. Zamiast tego ta opcja dotyczy tylko przeniesienia nazwy domeny. 

#### <a name="more-information-about-rms-for-individuals"></a>Więcej informacji o usłudze RMS dla użytkowników indywidualnych

Dla [RMS dla użytkowników indywidualnych](/azure/information-protection/rms-for-individuals), gdy dzierżawa niezarządzana jest w tym samym regionie, co dzierżawy, których jesteś właścicielem, utworzone automatycznie [klucza dzierżawy usługi Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) i [domyślne Szablony ochrony](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) są dodatkowo przeniesiony z nazwą domeny. 

Klucz i szablony nie są przenoszone, gdy dzierżawa niezarządzana jest w innym regionie. Na przykład niezarządzanej dzierżawy jest w Europie i dzierżawy, do której jesteś właścicielem, znajduje się w Ameryce Północnej. 

Mimo że usługa RMS dla użytkowników indywidualnych jest przeznaczona do obsługi uwierzytelniania usługi Azure AD, aby otworzyć chronionej zawartości, nie uniemożliwia użytkownikom, z również ochrony zawartości. Jeśli użytkownicy chronić zawartość przy użyciu subskrypcji usługi RMS dla użytkowników indywidualnych, a klucz i szablony nie zostało przeniesione, za pośrednictwem, tę zawartość nie będzie dostępna po przejęciu domeny.

#### <a name="more-information-about-power-bi"></a>Więcej informacji na temat usługi Power BI

Podczas wykonywania zewnętrznych przejęcia, zawartość usługi Power BI, który został utworzony przed przejęcia jest umieszczany w [obszar roboczy usługi Power BI zarchiwizowane](/power-bi/service-admin-power-bi-archived-workspace). Należy ręcznie przeprowadzić migrację żadnej zawartości, którego chcesz użyć w nowym dzierżawcą.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Usługa Azure AD poleceń cmdlet programu PowerShell dla opcji ForceTakeover
Możesz zobaczyć te polecenia cmdlet użyte w [przykład programu PowerShell](#powershell-example).


Polecenia cmdlet | Sposób użycia 
------- | -------
`connect-msolservice` | Po wyświetleniu monitu zaloguj się do zarządzanej dzierżawy.
`get-msoldomain` | Pokazuje nazwy domeny skojarzone z bieżącym dzierżawcą.
`new-msoldomain –name <domainname>` | Dodaje nazwę domeny do dzierżawy jako niezweryfikowane (weryfikacja serwera DNS, nie przeprowadzono jeszcze).
`get-msoldomain` | Nazwa domeny znajduje się teraz na liście nazw domeny skojarzonych z zarządzanej dzierżawy, ale jest wymieniony jako **niezweryfikowane**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Zawiera informacje, aby umieścić w nowy rekord DNS TXT dla domeny (MS = xxxxx). Weryfikacja może być nie możliwe natychmiast ponieważ dopiero po pewnym czasie dla rekordu TXT Propagacja, więc Poczekaj kilka minut zanim będzie można uznać **- ForceTakeover** opcji. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Jeśli nazwa domeny nadal nie jest weryfikowany, możesz kontynuować **- ForceTakeover** opcji. Sprawdza, czy rekord TXT został utworzony i dotyczącego przejęcia procesu.<li>**- ForceTakeover** opcja powinna być dodana do polecenia cmdlet, tylko wtedy, gdy wymuszania przejęcia przez administratora zewnętrznych, na przykład w przypadku niezarządzanej dzierżawy usługi Office 365, blokuje przejęcia.
`get-msoldomain` | Lista domen jest teraz wyświetlana nazwa domeny jako **Verified**.

### <a name="powershell-example"></a>Przykład programu PowerShell

1. Połącz z usługą Azure AD przy użyciu poświadczeń użytych do odpowiadanie na oferty samoobsługowego:
  ````
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. Pobierz listę domen:
  
  ````
    Get-MsolDomain
  ````
3. Uruchom polecenie cmdlet Get-MsolDomainVerificationDns, aby utworzyć żądanie:
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. Skopiuj wartość (żądanie), która jest zwracana z tego polecenia. Na przykład:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. W publicznej przestrzeni nazw DNS należy utworzyć rekord txt DNS, który zawiera wartości, który został skopiowany w poprzednim kroku. Nazwa tego rekordu jest nazwa domeny nadrzędnej, więc jeśli utworzysz ten rekord zasobu za pomocą roli DNS z systemem Windows Server, pozostaw Wklej puste i po prostu nazwę rekordu wartość w polu tekstowym.
6. Uruchom polecenie cmdlet Confirm-MsolDomain, aby zweryfikować wyzwania:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  Na przykład:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

Pomyślne żądania powrót do wiersza polecenia bez błędów.

## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../fundamentals/add-custom-domain.md)
* [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Dokumentacja poleceń cmdlet platformy Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
