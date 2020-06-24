---
title: Łączenie konta platformy Azure z identyfikatorem partnera
description: Śledź kontakty z klientami platformy Azure, łącząc identyfikator partnera z kontem użytkownika, którego używasz do zarządzania zasobami klienta.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 05/04/2020
ms.service: cost-management-billing
ms.topic: conceptual
ms.openlocfilehash: 77abfcf300decb3a19da4268d7feb7de1f41f3b5
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743919"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Łączenie identyfikatora partnera z kontami platformy Azure

Partnerzy firmy Microsoft oferują usługi, które pomagają klientom osiągać cele biznesowe i ogólne przy użyciu produktów firmy Microsoft. Podejmując działania w imieniu klienta podczas konfigurowania i obsługiwania usług platformy Azure oraz zarządzania nimi, użytkownicy partnera będą musieli uzyskać dostęp do środowiska klienta. Korzystając z linku administratora partnera, partnerzy mogą powiązać identyfikator sieci partnera z poświadczeniami używanymi do dostarczania usług.

Link administratora partnera pozwala firmie Microsoft identyfikować i rozpoznawać partnerów, którzy prowadzą do sukcesu klientów platformy Azure. Firma Microsoft może przypisywać wpływ i przychody użyte przez platformę Azure w organizacji na podstawie uprawnień konta (rola RBAC) i zakresu (subskrypcja, grupa zasobów, zasób).

## <a name="get-access-from-your-customer"></a>Uzyskanie dostępu od klienta

Przed połączeniem identyfikatora partnera klient musi zapewnić dostęp do swoich zasobów platformy Azure, korzystając z jednego z następujących rozwiązań:

- **Użytkownik-gość**: Klient może dodać Cię jako użytkownika-gościa i przypisać role kontroli dostępu na podstawie ról (RBAC). Aby uzyskać więcej informacji, zobacz temat [Add guest users from another directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (Dodawanie użytkowników-gości z innego katalogu).

- **Konto katalogu**: Klient może utworzyć dla Ciebie konto użytkownika w swoim własnym katalogu i przypisać dowolną rolę RBAC.

- **Jednostka usługi**: Klient może dodać aplikację lub skrypt z organizacji w swoim katalogu i przypisać dowolną rolę RBAC. Tożsamość aplikacji lub skryptu jest znana jako nazwa główna usługi.

- **Azure Lighthouse**: Klient może delegować subskrypcję (lub grupę zasobów), aby Twoi użytkownicy mogli z niej korzystać z poziomu Twojej dzierżawy. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami delegowanymi na platformie Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).

## <a name="link-to-a-partner-id"></a>Łączenie z identyfikatorem partnera

Jeśli masz dostęp do zasobów klienta, użyj witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby połączyć identyfikator MPN (Microsoft Partner Network) z identyfikatorem użytkownika lub nazwą główną usługi. Połącz identyfikator partnera w każdej dzierżawie klienta.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Użycie witryny Azure Portal do łączenia z nowym identyfikatorem partnera

1. Przejdź do sekcji [Łączenie z identyfikatorem partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) w witrynie Azure Portal.

2. Zaloguj się do Portalu Azure.

3. Wprowadź identyfikator partnera firmy Microsoft. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji.

   ![Zrzut ekranu przedstawiający sekcję Łączenie z identyfikatorem partnera](./media/link-partner-id/link-partner-id01.png)

4. Aby połączyć identyfikator partnera innego klienta, przełącz katalog. W obszarze **Przełącz katalog** wybierz swój katalog.

   ![Zrzut ekranu przedstawiający obszar Przełącz katalog](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Używanie programu PowerShell do łączenia z nowym identyfikatorem partnera

1. Zainstaluj moduł [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) programu PowerShell.

2. Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się w programie Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Pobieranie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualizowanie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Usuwanie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure do połączenia z nowym identyfikatorem partnera
1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Zaloguj się do dzierżawy klienta, używając konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Pobieranie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aktualizowanie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Usuwanie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Następne kroki

Dołącz do dyskusji w [społeczności partnerów firmy Microsoft](https://aka.ms/PALdiscussion), aby otrzymywać aktualizacje lub wysłać opinię.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Kto może połączyć identyfikator partnera?**

Identyfikator partnera z kontem może połączyć każdy użytkownik z organizacji partnerskiej, który zarządza zasobami platformy Azure klienta.

**Czy identyfikator partnera można zmienić po jego połączeniu?**

Tak. Połączony identyfikator partnera można zmienić, dodać lub usunąć.

**Co należy zrobić, jeśli użytkownik ma konto w więcej niż jednej dzierżawie klienta?**

Połączenie między identyfikatorem partnera i kontem jest wykonywane dla każdej dzierżawy klienta. Połącz identyfikator partnera w każdej dzierżawie klienta.

**Czy inni partnerzy lub klienci mogą edytować lub usunąć hiperlink do identyfikatora partnera?**

Hiperlink jest skojarzony na poziomie konta użytkownika. Tylko Ty możesz edytować lub usunąć hiperlink do identyfikatora partnera. Klient i inni partnerzy nie mogą zmienić hiperlinku do identyfikatora partnera.


**Którego identyfikatora MPN należy użyć, jeśli moja firma ma ich wiele?**

Do połączenia identyfikatora partnera należy użyć kont Partner Location Account i powiązanych identyfikatorów MPN.  Dowiedz się więcej na temat [kont partnerów](https://docs.microsoft.com/partner-center/account-structure).

**Gdzie można znaleźć uwzględnione raportowanie przychodów dla połączonego identyfikatora partnera?**

Raportowanie wydajności produktu w chmurze jest dostępne dla partnerów na [pulpicie nawigacyjnym Moje usługi Insights](https://partner.microsoft.com/membership/reports/myinsights) w Centrum partnerskim. Jako typ powiązania partnera należy wybrać hiperlink administratora partnera.

**Dlaczego nie widzę mojego klienta w raportach?**

Klient może być niewidoczny w raportach z następujących powodów:

1. Połączone konto użytkownika nie ma [dostępu opartego na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) w żadnej subskrypcji ani zasobie klienta platformy Azure.

2. Subskrypcja platformy Azure, w której użytkownik ma [dostęp oparty na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview), nie ma żadnego użycia.

**Czy identyfikator partnera hiperlinku współdziała z usługą Azure Stack?**

Tak. Identyfikator partnera można połączyć z usługą Azure Stack.

**Jak mogę połączyć swój identyfikator partnera, jeśli moja firma używa rozwiązania [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) w celu uzyskania dostępu do zasobów klienta?**

Jeśli dołączasz klientów do zarządzania zasobami delegowanymi na platformie Azure przez [opublikowanie oferty usług zarządzanych w witrynie Azure Marketplace](https://docs.microsoft.com/azure/lighthouse/how-to/publish-managed-services-offers), Twój identyfikator MPN zostanie automatycznie skojarzony. Jeśli [dołączasz klientów przez wdrożenie szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer), musisz skojarzyć swój identyfikator Microsoft Partner Network (MPN) z co najmniej jednym kontem użytkownika, które ma dostęp do każdej z dołączonych subskrypcji. Musisz pamiętać, że należy to zrobić w dzierżawie dostawcy usług. Dla uproszczenia zalecamy utworzenie konta jednostki usługi w dzierżawie, które będzie skojarzone z identyfikatorem MPN, i przyznanie mu dostępu czytelnika do każdego dołączonego klienta. W tym przykładzie jest używana rola czytelnika kontroli opartej na rolach i jest to jedna z ról, które nie kwalifikują się do środków uzyskanych przez partnerów. Aby uzyskać więcej informacji na temat ról, zobacz [Role i uprawnienia dotyczące środków uzyskanych przez partnerów](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3QuW2).


**Jak mogę wyjaśnić link administratora partnera mojemu klientowi?**

Link administratora partnera pozwala firmie Microsoft identyfikować i rozpoznawać partnerów, którzy pomagają klientom osiągnąć cele biznesowe i zrealizować wartość w chmurze. Klienci muszą najpierw zapewnić dostęp partnera do ich zasobów platformy Azure. Po udzieleniu dostępu identyfikator Microsoft Partner Network partnera (identyfikator MPN) zostaje skojarzony. To skojarzenie pomaga firmie Microsoft zrozumieć ekosystem dostawców usług IT oraz udoskonalać narzędzia i programy niezbędne do najlepszego wsparcia naszych wspólnych klientów.

**Jakie dane są zbierane przez link administratora partnera?**

Skojarzenie linku administratora partnera z istniejącymi poświadczeniami nie udostępnia żadnych nowych danych klienta firmie Microsoft. Po prostu udostępnia dane telemetryczne firmie Microsoft, gdy partner aktywnie uczestniczy w środowisku platformy Azure klienta. Firma Microsoft może przypisywać wpływ i przychody użyte przez platformę Azure ze środowiska klienta do organizacji partnera na podstawie uprawnień konta (rola RBAC) i zakresu (grupa zarządzania, subskrypcja, grupa zasobów, zasób) udostępnionego partnerowi przez klienta. 

**Czy ma to wpływ na bezpieczeństwo środowiska platformy Azure klienta?**

Skojarzenie linku administratora partnera dodaje tylko identyfikator MPN partnera do już ustanowionych poświadczeń i nie zmienia żadnych uprawnień (roli RBAC) ani nie udostępnia dodatkowych danych usługi platformy Azure partnerowi lub firmie Microsoft. 

