---
title: "Zarządzanie obszarami roboczymi w usłudze Azure Log Analytics i portalu pakietu OMS | Microsoft Docs"
description: "Możesz zarządzać obszarami roboczymi w usłudze Azure Log Analytics i portalu pakietu OMS przy użyciu różnych zadań administracyjnych dotyczących użytkowników, kont, obszarów roboczych i kont platformy Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 0a6b5115a4eebfcce14094d82cdcc9579f80def6
ms.lasthandoff: 04/15/2017


---
# <a name="manage-workspaces"></a>Zarządzanie obszarami roboczymi

Dostępem do usługi Log Analytics można zarządzać, wykonując różne zadania administracyjne dotyczące obszarów roboczych. Ten artykuł zawiera opis najlepszych rozwiązań i procedur z zakresu zarządzania obszarami roboczymi. Obszar roboczy to kontener, który zawiera informacje o koncie i podstawowe informacje o konfiguracji konta. Ty i inni członkowie organizacji możecie używać wielu obszarów roboczych, aby zarządzać różnymi zestawami danych zebranymi z całej infrastruktury IT lub jej części.

Aby utworzyć obszar roboczy, trzeba:

1. Mieć subskrypcję platformy Azure.
2. Wybrać nazwę obszaru roboczego.
3. Skojarzyć obszar roboczy z subskrypcją.
4. Wybrać lokalizację geograficzną.

## <a name="determine-the-number-of-workspaces-you-need"></a>Określanie wymaganej liczby obszarów roboczych
Obszar roboczy jest zasobem platformy Azure w postaci kontenera, w którym dane są zbierane, agregowane, analizowane i przedstawiane w witrynie Azure Portal.

Możesz mieć wiele obszarów roboczych na subskrypcję platformy Azure i dostęp do więcej niż jednego obszaru roboczego. Minimalizacja liczby obszarów roboczych pozwala na wykonywanie zapytań dotyczących większości danych i ich korelowanie, ponieważ nie można wykonywać zapytań w wielu obszarach roboczych. W tej sekcji opisano sytuacje, w których pomocne może być utworzenie więcej niż jednego obszaru roboczego.

Obecnie obszar roboczy oferuje następujące możliwości:

* Lokalizacja geograficzna do przechowywania danych
* Poziom szczegółowości rozliczeń
* Izolacja danych
* Możliwość konfiguracji

Na podstawie powyższych właściwości można wskazać następujące sytuacje, w których warto utworzyć wiele obszarów roboczych:

* Globalna firma wymaga, aby jej dane były przechowywane w określonych regionach w celu zachowania poufności danych i ich zgodności z przepisami.
* Korzystasz z platformy Azure i chcesz uniknąć naliczania opłat za transfer danych wychodzących przez umieszczenie obszaru roboczego w tym samym regionie co zarządzane przez niego zasoby platformy Azure.
* Chcesz przydzielić opłaty do różnych działów lub grup biznesowych na podstawie ich użycia. Gdy utworzysz obszar roboczy dla każdego działu lub każdej grupy biznesowej, na rachunku dotyczącym platformy Azure i w zestawieniu użycia będą widoczne osobne opłaty dla poszczególnych obszarów roboczych.
* Jesteś dostawcą usługi zarządzanej i chcesz, aby dane analizy dziennika dla klientów, którymi zarządzasz, były odizolowane od danych innych klientów.
* Zarządzasz wieloma klientami i chcesz, aby dla poszczególnych klientów, działów i grup biznesowych były widoczne ich własne dane, ale nie dane innych klientów, działów czy grup biznesowych.

Kiedy zbierasz dane przy użyciu agenta, możesz [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](log-analytics-windows-agents.md).

Jeśli używasz programu System Center Operations Manager, jedna grupa zarządzania programu Operations Manager może być połączona tylko z jednym obszarem roboczym. Możesz zainstalować program Microsoft Monitoring Agent na komputerach zarządzanych przez program Operations Manager oraz skonfigurować agenta w celu raportowania do programu Operations Manager i innego obszaru roboczego usługi Log Analytics.

### <a name="workspace-information"></a>Informacje o obszarze roboczym

Szczegółowe informacje o obszarze roboczym można wyświetlić w witrynie Azure Portal. Szczegółowe informacje można również wyświetlić w portalu pakietu OMS.

#### <a name="view-workspace-information-in-the-azure-portal"></a>Wyświetlanie informacji o obszarze roboczym w witrynie Azure Portal

1. Jeśli nie zostało to jeszcze zrobione, zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu subskrypcji platformy Azure.
2. W menu **Centrum** kliknij pozycję **Więcej usług** i na liście zasobów wpisz ciąg **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Kliknij pozycję **Log Analytics**.  
    ![Centrum platformy Azure](./media/log-analytics-manage-access/hub.png)  
3. Wybierz obszar roboczy w bloku subskrypcji usługi Log Analytics.
4. Blok obszaru roboczego zawiera szczegółowe dane dotyczące obszaru roboczego i linki do dodatkowych informacji.  
    ![szczegóły obszaru roboczego](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Zarządzanie kontami i użytkownikami
Z obszarem roboczym może być skojarzonych wiele kont, a każde konto (konto Microsoft lub konto organizacyjne) może uzyskiwać dostęp do wielu obszarów roboczych.

Domyślnie konto Microsoft lub konto organizacyjne używane do utworzenia obszaru roboczego jest kontem administratora obszaru roboczego.

Istnieją dwa modele uprawnień, które kontrolują dostęp do obszaru roboczego usługi Log Analytics:

1. Starsze role użytkownika usługi Log Analytics
2. [Dostęp oparty na rolach na platformie Azure](../active-directory/role-based-access-control-configure.md)

Poniższa tabela zawiera podsumowanie dostępu, który można ustawić za pomocą każdego modelu uprawnień:

|                          | Portal usługi Log Analytics | Azure Portal | Interfejs API (w tym program PowerShell) |
|--------------------------|----------------------|--------------|----------------------------|
| Role użytkownika usługi Log Analytics | Tak                  | Nie           | Nie                         |
| Dostęp oparty na rolach na platformie Azure  | Tak                  | Tak          | Tak                        |

> [!NOTE]
> Model uprawnień usługi Log Analytics jest zmieniany na dostęp oparty na rolach platformy Azure, który zastępuje role użytkownika usługi Log Analytics.
>
>

Starsze role użytkownika usługi Log Analytics mogą kontrolować tylko dostęp do działań wykonywanych w [portalu usługi Log Analytics](https://mms.microsoft.com).

Następujące działania w portalu usługi Log Analytics również wymagają uprawnień platformy Azure:

| Akcja                                                          | Wymagane uprawnienia platformy Azure | Uwagi |
|-----------------------------------------------------------------|--------------------------|-------|
| Dodawanie i usuwanie rozwiązań do zarządzania                        | Zapis w grupie zasobów <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
| Zmienianie warstwy cenowej                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Wyświetlanie danych w kafelkach rozwiązań *Backup* i *Site Recovery* | Administrator/współadministrator | Uzyskuje dostęp do zasobów wdrożonych przy użyciu klasycznego modelu wdrażania |

### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Zarządzanie dostępem do usługi Log Analytics przy użyciu uprawnień platformy Azure
Aby udzielić dostępu do obszaru roboczego usługi Log Analytics przy użyciu uprawnień platformy Azure, wykonaj kroki opisane w części [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../active-directory/role-based-access-control-configure.md).

Jeśli masz co najmniej uprawnienie platformy Azure do odczytu w obszarze roboczym usługi Log Analytics, możesz otworzyć portal pakietu OMS, klikając zadanie **Portal OMS** podczas przeglądania obszaru roboczego usługi Log Analytics.

Podczas otwierania portalu usługi Log Analytics przełączasz się do użycia starszych ról użytkownika usługi Log Analytics. Jeśli nie masz przypisania roli w portalu usługi Log Analytics, usługa [sprawdza uprawnienia platformy Azure w obszarze roboczym](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
Przypisanie roli w portalu usługi Log Analytics jest określane w następujący sposób:

| Warunki                                                   | Przypisana rola użytkownika usługi Log Analytics | Uwagi |
|--------------------------------------------------------------|----------------------------------|-------|
| Konto należy do starszej roli użytkownika usługi Log Analytics     | Wybrana rola użytkownika usługi Log Analytics | |
| Konto nie należy do starszej roli użytkownika usługi Log Analytics <br> Pełne uprawnienia platformy Azure do obszaru roboczego (uprawnienie `*` <sup>1</sup>) | Administrator ||
| Konto nie należy do starszej roli użytkownika usługi Log Analytics <br> Pełne uprawnienia platformy Azure do obszaru roboczego (uprawnienie `*` <sup>1</sup>) <br> *nie akcje* elementów `Microsoft.Authorization/*/Delete` i `Microsoft.Authorization/*/Write` | Współautor ||
| Konto nie należy do starszej roli użytkownika usługi Log Analytics <br> Uprawnienia platformy Azure do odczytu | Tylko do odczytu ||
| Konto nie należy do starszej roli użytkownika usługi Log Analytics <br> Uprawnienia platformy Azure są niezrozumiałe | Tylko do odczytu ||
| Zarządzane subskrypcje dostawcy rozwiązań w chmurze (CSP) <br> Konto, na którym się zalogowano, należy do usługi Azure Active Directory połączonej z obszarem roboczym | Administrator | Przeważnie klient dostawcy CSP |
| Zarządzane subskrypcje dostawcy rozwiązań w chmurze (CSP) <br> Konto, na którym się zalogowano, nie należy do usługi Azure Active Directory połączonej z obszarem roboczym | Współautor | Przeważnie dostawca CSP |

<sup>1</sup> Zapoznaj się z częścią [Azure permissions](../active-directory/role-based-access-control-custom-roles.md) (Uprawnienia platformy Azure), aby uzyskać więcej informacji na temat definicji ról. Podczas oceny ról akcja `*` nie jest odpowiednikiem `Microsoft.OperationalInsights/workspaces/*`.

Należy pamiętać o kilku kwestiach związanych z witryną Azure Portal:

* Po zalogowaniu się do portalu pakietu OMS na stronie http://mms.microsoft.com zobaczysz listę **Wybierz obszar roboczy**. Ta lista zawiera tylko obszary robocze, w których masz rolę użytkownika usługi Log Analytics. Aby wyświetlić obszary robocze, do których uzyskujesz dostęp za pomocą subskrypcji platformy Azure, musisz podać dzierżawę w adresie URL. Przykład: `mms.microsoft.com/?tenant=contoso.com`. Identyfikator dzierżawy jest często ostatnią częścią adresu e-mail użytego podczas logowania.
* Aby przejść bezpośrednio do portalu, do którego uzyskujesz dostęp przy użyciu uprawnień platformy Azure, musisz podać zasób w adresie URL. Taki adres URL można uzyskać przy użyciu programu PowerShell.

  Na przykład `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  Adres URL wygląda następująco: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Zarządzanie użytkownikami w portalu pakietu OMS
Do zarządzania użytkownikami i grupami służy karta **Zarządzanie użytkownikami** na karcie **Konta** na stronie Ustawienia.   

![zarządzanie użytkownikami](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Dodawanie użytkownika do istniejącego obszaru roboczego
Poniżej przedstawiono procedurę dodawania użytkownika lub grupy do obszaru roboczego.

1. W portalu pakietu OMS kliknij kafelek **Ustawienia**.
2. Kliknij kartę **Konta**, a następnie kliknij kartę **Zarządzanie użytkownikami**.
3. W sekcji **Zarządzanie użytkownikami** wybierz typ konta, które chcesz dodać: **Konto organizacyjne**, **Konto Microsoft** lub **Pomoc techniczna firmy Microsoft**.

   * W przypadku wybrania konta Microsoft wpisz adres e-mail użytkownika skojarzonego z kontem Microsoft.
   * W przypadku wybrania konta organizacyjnego możesz podać część nazwy albo aliasu e-mail użytkownika/grupy, co spowoduje wyświetlenie listy zgodnych użytkowników i grup w polu listy rozwijanej. Wybierz użytkownika lub grupę.
   * Użyj konta Pomoc techniczna firmy Microsoft, aby udzielić inżynierowi pomocy technicznej lub innemu pracownikowi firmy Microsoft tymczasowego dostępu do obszaru roboczego w celu ułatwienia rozwiązywania problemów.

     > [!NOTE]
     > W celu uzyskania optymalnej wydajności zmniejsz liczbę grup usługi Active Directory skojarzonych z kontem pakietu OMS do trzech — jednej dla administratorów, jednej dla współautorów i jednej dla użytkowników tylko do odczytu. Użycie większej liczby grup może mieć zły wpływ na wydajność usługi Log Analytics.
     >
     >
4. Wybierz typ użytkownika lub grupy do dodania: **Administrator**, **Współautor** lub **Użytkownik tylko do odczytu**.  
5. Kliknij pozycję **Dodaj**.

   W przypadku dodawania konta Microsoft na podany adres e-mail zostanie wysłane zaproszenie do dołączenia do obszaru roboczego. Gdy użytkownik wykona instrukcje zawarte w zaproszeniu do dołączenia do pakietu OMS, będzie mógł uzyskać dostęp do tego obszaru roboczego.
   W przypadku dodawania konta organizacyjnego użytkownik będzie miał natychmiast dostęp do usługi Log Analytics.  

#### <a name="edit-an-existing-user-type"></a>Edytowanie istniejącego typu użytkownika
Możesz zmienić rolę konta użytkownika skojarzonego z Twoim kontem pakietu OMS. Dostępne są następujące role:

* *Administrator*: może zarządzać użytkownikami, wyświetlać wszystkie alerty i wykonywać na nich operacje oraz dodawać i usuwać serwery
* *Współautor*: może wyświetlać wszystkie alerty i wykonywać na nich operacje oraz dodawać i usuwać serwery
* *Użytkownik tylko do odczytu*: użytkownicy oznaczeni jako tylko do odczytu mają następujące ograniczenia:

  1. Nie mogą dodawać ani usuwać rozwiązań. Galeria rozwiązań jest ukryta.
  2. Nie mogą dodawać, modyfikować ani usuwać kafelków na stronie **Mój pulpit nawigacyjny**.
  3. Nie mogą wyświetlać stron **Ustawienia**. Są one ukryte.
  4. W widoku wyszukiwania, konfiguracji usługi Power BI, zapisanych wyszukiwaniach i alertach zadania są ukryte.

#### <a name="to-edit-an-account"></a>Aby edytować konto
1. W portalu pakietu OMS kliknij kafelek **Ustawienia**.
2. Kliknij kartę **Konta**, a następnie kliknij kartę **Zarządzanie użytkownikami**.
3. Wybierz rolę użytkownika, którą chcesz zmienić.
4. W oknie dialogowym potwierdzenia kliknij przycisk **Tak**.

### <a name="remove-a-user-from-a-workspace"></a>Usuwanie użytkownika z obszaru roboczego
Poniżej przedstawiono procedurę usuwania użytkownika z obszaru roboczego. Usunięcie użytkownika nie powoduje zamknięcia obszaru roboczego. Powoduje to natomiast usunięcie skojarzenia między użytkownikiem i obszarem roboczym. Jeśli użytkownik został skojarzony z wieloma obszarami roboczymi, będzie nadal mieć możliwość zalogowania się do pakietu OMS i wyświetlania innych obszarów roboczych.

1. W portalu pakietu OMS kliknij kafelek **Ustawienia**.
2. Kliknij kartę **Konta**, a następnie kliknij kartę **Zarządzanie użytkownikami**.
3. Kliknij pozycję **Usuń** obok nazwy użytkownika, którego chcesz usunąć.
4. W oknie dialogowym potwierdzenia kliknij przycisk **Tak**.

### <a name="add-a-group-to-an-existing-workspace"></a>Dodawanie grupy do istniejącego obszaru roboczego
1. Wykonaj kroki 1–4 przedstawione w powyższej sekcji „Dodawanie użytkownika do istniejącego obszaru roboczego”.
2. W obszarze **Wybierz użytkownika/grupę** wybierz pozycję **Grupa**.  
   ![dodawanie grupy do istniejącego obszaru roboczego](./media/log-analytics-manage-access/add-group.png)
3. Wprowadź nazwę wyświetlaną lub adres e-mail grupy, którą chcesz dodać.
4. Wybierz grupę z listy wyników, a następnie kliknij przycisk **Dodaj**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Łączenie istniejącego obszaru roboczego z subskrypcją platformy Azure
Wszystkie obszary robocze utworzone po 26 września 2016 roku muszą być w czasie tworzenia połączone z subskrypcją platformy Azure. Obszary robocze utworzone wcześniej muszą zostać połączone z obszarem roboczym po następnym zalogowaniu. Jeśli utworzysz obszar roboczy z poziomu witryny Azure Portal lub połączysz obszar roboczy z subskrypcją platformy Azure, usługa Azure Active Directory zostanie połączona jako konto organizacyjne.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Aby połączyć obszar roboczy z subskrypcją platformy Azure w portalu pakietu OMS

- Gdy logujesz się do portalu pakietu OMS, jest wyświetlany monit o wybranie subskrypcji platformy Azure. Wybierz subskrypcję, którą chcesz połączyć z obszarem roboczym, a następnie kliknij pozycję **Połącz**.  
    ![łączenie subskrypcji platformy Azure](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > Aby można było połączyć obszar roboczy, konto platformy Azure musi już mieć dostęp do obszaru roboczego, który chcesz połączyć.  Oznacza to, że konto używane do uzyskiwania dostępu do witryny Azure Portal musi być **tym samym kontem**, za pomocą którego jest uzyskiwany dostęp do obszaru roboczego. Jeśli nie jest, zobacz [Dodawanie użytkownika do istniejącego obszaru roboczego](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Aby połączyć obszar roboczy z subskrypcją platformy Azure w witrynie Azure Portal
1. Zaloguj się do [Azure Portal](http://portal.azure.com).
2. Wyszukaj pozycję **Log Analytics** i wybierz ją.
3. Zostanie wyświetlona lista istniejących obszarów roboczych. Kliknij pozycję **Dodaj**.  
   ![lista obszarów roboczych](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. W obszarze **Obszar roboczy pakietu OMS** kliknij pozycję **Lub połącz istniejący**.  
   ![łączenie istniejącego obszaru roboczego](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Kliknij pozycję **Skonfiguruj wymagane ustawienia**.  
   ![konfigurowanie wymaganych ustawień](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Zostanie wyświetlona lista obszarów roboczych, które nie są jeszcze połączone z Twoim kontem platformy Azure. Wybierz obszar roboczy.  
   ![wybieranie obszarów roboczych](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. W razie potrzeby możesz zmienić wartości następujących elementów:
   * Subskrypcja
   * Grupa zasobów
   * Lokalizacja
   * Warstwa cenowa  
     ![zmienianie wartości](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Kliknij przycisk **OK**. Obszar roboczy jest teraz połączony z kontem platformy Azure.

> [!NOTE]
> Jeśli obszar roboczy, który chcesz połączyć, nie jest wyświetlany, oznacza to, że subskrypcja platformy Azure nie ma dostępu do obszaru roboczego utworzonego przy użyciu portalu pakietu OMS.  Aby udzielić dostępu do tego konta z poziomu portalu pakietu OMS, zobacz sekcję [Dodawanie użytkownika do istniejącego obszaru roboczego](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Uaktualnianie obszaru roboczego do płatnego planu
Dostępne są trzy typy planów obszarów roboczych dla pakietu OMS: **Bezpłatny**, **Autonomiczny** i **OMS**.  Jeśli używany jest plan *Bezpłatny*, do usługi Log Analytics można wysłać dziennie maksymalnie 500 MB danych.  Aby po przekroczeniu tego limitu móc nadal zbierać dane, musisz zmienić obszar roboczy na plan płatny. Typ planu można zmienić w dowolnym momencie.  Aby uzyskać więcej informacji o cenach pakietu OMS, zobacz [szczegółowe informacje o cenach](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Używanie uprawnień z subskrypcji pakietu OMS
Aby używać uprawnień wynikających z zakupu pakietu OMS E1, OMS E2 OMS lub dodatku pakietu OMS dla programu System Center, wybierz plan *OMS* dla usługi Log Analytics pakietu OMS.

Po zakupie subskrypcji pakietu OMS uprawnienia zostaną dodane do umowy Enterprise Agreement. Z tych uprawnień mogą korzystać dowolne subskrypcje platformy Azure utworzone w ramach tej umowy. Wszystkie obszary robocze w ramach tych subskrypcji korzystają z uprawnień pakietu OMS.

Aby upewnić się, że uprawnienia subskrypcji pakietu OMS są uwzględniane w danych użycia obszaru roboczego, wykonaj następujące czynności:

1. Połącz obszar roboczy z subskrypcją platformy Azure w ramach umowy Enterprise Agreement, która obejmuje subskrypcję pakietu OMS
2. Wybierz plan *OMS* dla obszaru roboczego

> [!NOTE]
> Jeśli obszar roboczy został utworzony przed 26 września 2016 r., a plan taryfowy usługi Log Analytics to *Premium*, ten obszar roboczy używa uprawnień z dodatku pakietu OMS dla programu System Center. Uprawnień można także użyć, zmieniając warstwę cenową pakietu *OMS*.
>
>

Uprawnienia subskrypcji pakietu OMS nie są widoczne w witrynie Azure Portal lub w portalu pakietu OMS. Uprawnienia i użycie są natomiast widoczne w witrynie Enterprise Portal.  

Jeśli chcesz zmienić subskrypcję platformy Azure, z którą jest połączony obszar roboczy, możesz użyć polecenia cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) programu Azure PowerShell.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Korzystanie z zobowiązania platformy Azure w ramach umowy Enterprise Agreement
Jeśli nie masz subskrypcji pakietu OMS, zapłacisz osobno za każdy składnik tego pakietu, a dane użycia będą widoczne na rachunku dotyczącym platformy Azure.

Jeśli istnieje zobowiązanie pieniężne platformy Azure dotyczące rejestracji przedsiębiorstwa, z którą są połączone subskrypcje platformy Azure, użycie usługi Log Analytics spowoduje automatyczne obciążenie pozostałego zobowiązania pieniężnego.

Jeśli chcesz zmienić subskrypcję platformy Azure, z którą jest połączony obszar roboczy, możesz użyć polecenia cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) programu Azure PowerShell.  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Zmienianie obszaru roboczego na płatną warstwę cenową w witrynie Azure Portal
1. Zaloguj się do [Azure Portal](http://portal.azure.com).
2. Wyszukaj pozycję **Log Analytics** i wybierz ją.
3. Zostanie wyświetlona lista istniejących obszarów roboczych. Wybierz obszar roboczy.  
4. W bloku obszaru roboczego w obszarze **Ogólne** kliknij pozycję **Warstwa cenowa**.  
5. W obszarze **Warstwa cenowa** kliknij warstwę cenową, a następnie kliknij przycisk **Wybierz**.  
    ![wybieranie planu](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Gdy odświeżysz widok w witrynie Azure Portal, zostanie wyświetlony zaktualizowany obszar **Warstwa cenowa** dla wybranej warstwy.  
    ![zaktualizowany plan](./media/log-analytics-manage-access/manage-access-change-plan04.png)

> [!NOTE]
> Jeśli obszar roboczy jest połączony z kontem usługi Automation, przed wybraniem warstwy cenowej *Autonomiczna (za GB)* musisz usunąć wszystkie rozwiązania **Automation and Control** i odłączyć konto usługi Automation. W bloku obszaru roboczego w obszarze **Ogólne** kliknij pozycję **Rozwiązania**, aby wyświetlić i usunąć rozwiązania. Aby odłączyć konto usługi Automation, kliknij nazwę konta usługi Automation w bloku **Warstwa cenowa**.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Zmienianie obszaru roboczego na płatną warstwę cenową w portalu pakietu OMS

Aby zmienić warstwę cenową za pomocą portalu pakietu OMS, musisz mieć subskrypcję platformy Azure.

1. W portalu pakietu OMS kliknij kafelek **Ustawienia**.
2. Kliknij kartę **Konta**, a następnie kliknij kartę **Subskrypcja i plan taryfowy platformy Azure**.
3. Kliknij warstwę cenową, której chcesz użyć.
4. Kliknij pozycję **Zapisz**.  
   ![subskrypcja i plany taryfowe](./media/log-analytics-manage-access/subscription-tab.png)

Nowy plan taryfowy jest wyświetlany na wstążce portalu pakietu OMS w górnej części strony sieci Web.

![Wstążka pakietu OMS](./media/log-analytics-manage-access/data-plan-changed.png)


## <a name="change-how-long-log-analytics-stores-data"></a>Zmiana czasu przechowywania danych przez usługę Log Analytics

W warstwie cenowej Bezpłatna usługa Log Analytics udostępnia dane z ostatnich siedmiu dni.
W warstwie cenowej Standardowa usługa Log Analytics udostępnia dane z ostatnich 30 dni.
W warstwie cenowej Premium usługa Log Analytics udostępnia dane z ostatnich 365 dni.
W warstwach cenowych Autonomiczna i OMS usługa Log Analytics domyślnie udostępnia dane z ostatnich 31 dni.

Korzystając z warstw cenowych Autonomiczna i OMS, możesz przechowywać dane z maksymalnie 2 lat (730 dni). Za dane przechowywane dłużej niż domyślne 31 dni jest naliczana opłata za przechowywanie danych. Aby uzyskać więcej informacji na temat cen, zobacz [opłaty za użycie nadwyżkowe](https://azure.microsoft.com/pricing/details/log-analytics/).

Aby zmienić czas przechowywania danych:

1. Zaloguj się do [Azure Portal](http://portal.azure.com).
2. Wyszukaj pozycję **Log Analytics** i wybierz ją.
3. Zostanie wyświetlona lista istniejących obszarów roboczych. Wybierz obszar roboczy.  
4. W bloku obszaru roboczego w obszarze **Ogólne** kliknij pozycję **Przechowywanie**.  
5. Za pomocą suwaka zwiększ lub zmniejsz liczbę dni przechowywania, a następnie kliknij przycisk **Zapisz**.  
    ![zmiana okresu przechowywania](./media/log-analytics-manage-access/manage-access-change-retention01.png)

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Zmienianie organizacji usługi Azure Active Directory dla obszaru roboczego

Organizację usługi Azure Active Directory obszaru roboczego można zmienić. Zmiana organizacji usługi Azure Active Directory umożliwia dodawanie użytkowników i grup z tego katalogu do obszaru roboczego.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Aby zmienić organizację usługi Azure Active Directory dla obszaru roboczego

1. Na stronie Ustawienia w portalu pakietu OMS kliknij pozycję **Konta**, a następnie kliknij kartę **Zarządzanie użytkownikami**.  
2. Przejrzyj informacje dotyczące konta organizacyjnego, a następnie kliknij pozycję **Zmień organizację**.  
    ![zmienianie organizacji](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Wprowadź informacje o tożsamości administratora domeny usługi Azure Active Directory. Następnie zostanie wyświetlone potwierdzenie z informacją o tym, że obszar roboczy jest połączony z domeną usługi Azure Active Directory.  
    ![potwierdzenie dotyczące połączonego obszaru roboczego](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## <a name="delete-a-log-analytics-workspace"></a>Usuwanie obszaru roboczego usługi Log Analytics
Gdy usuniesz obszar roboczy usługi Log Analytics, wszystkie powiązane z nim dane zostaną usunięte z pakietu OMS w ciągu 30 dni.

Jeśli jesteś administratorem i z tym obszarem roboczym jest skojarzonych wielu użytkowników, skojarzenie między tymi użytkownikami i obszarem roboczym zostanie przerwane. Jeśli użytkownicy są skojarzeni z innymi obszarami roboczymi, mogą nadal korzystać z innych obszarów roboczych w pakiecie OMS. Jeśli jednak nie są oni skojarzeni z innymi obszarami roboczymi, muszą utworzyć obszar roboczy, aby móc korzystać z pakietu OMS.

### <a name="to-delete-a-workspace"></a>Aby usunąć obszar roboczy
1. Zaloguj się do [Azure Portal](http://portal.azure.com).
2. Wyszukaj pozycję **Log Analytics** i wybierz ją.
3. Zostanie wyświetlona lista istniejących obszarów roboczych. Wybierz obszar roboczy, który chcesz usunąć.
4. W bloku obszaru roboczego kliknij pozycję **Usuń**.  
    ![usuwanie](./media/log-analytics-manage-access/delete-workspace01.png)
5. W oknie dialogowym potwierdzenia usunięcia obszaru roboczego kliknij przycisk **Tak**.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się, jak dodać agentów i gromadzić dane, zobacz [Connect Windows computers to Log Analytics](log-analytics-windows-agents.md) (Tworzenie połączenia między komputerami z systemem Windows i usługą Log Analytics).
* [Dodaj rozwiązania Log Analytics z galerii rozwiązań](log-analytics-add-solutions.md), aby dodać funkcje i zebrać dane.
* [Skonfiguruj ustawienia serwera proxy i zapory w usłudze Log Analytics](log-analytics-proxy-firewall.md), jeśli organizacja korzysta z serwera proxy lub zapory, aby agenci mogli komunikować się z usługą Log Analytics.

