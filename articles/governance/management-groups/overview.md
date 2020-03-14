---
title: Organizowanie zasobów przy użyciu grup zarządzania — Zarządzanie platformą Azure
description: Dowiedz się więcej na temat grup zarządzania, sposobu działania ich uprawnień i korzystania z nich.
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.date: 12/18/2019
ms.topic: overview
ms.openlocfilehash: 319f48d4d0f8ce8501fecb74282760340b597188
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240940"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizowanie zasobów przy użyciu grup zarządzania platformy Azure

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania. Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji. Wszystkie subskrypcje w ramach jednej grupy zarządzania muszą ufać tej samej dzierżawie usługi Azure Active Directory.

Na przykład możesz zastosować do grupy zarządzania zasady ograniczające regiony dostępne na potrzeby tworzenia maszyny wirtualnej. Te zasady będą stosowane do wszystkich grup zarządzania, subskrypcji i zasobów w ramach tej grupy zarządzania, zezwalając na tworzenie maszyn wirtualnych tylko w tym regionie.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchia grup zarządzania i subskrypcji

Można utworzyć elastyczną strukturę grup zarządzania i subskrypcji w celu organizowania zasobów w hierarchię na potrzeby ujednoliconego zarządzania zasadami i dostępem. Na poniższym diagramie przedstawiono przykład tworzenia hierarchii dla nadzoru przy użyciu grup zarządzania.

![Przykład drzewa hierarchii grupy zarządzania](./media/tree.png)

Możesz utworzyć hierarchię stosującą zasady, na przykład ograniczające lokalizacje maszyn wirtualnych do regionu Zachodnie stany USA w grupie o nazwie „Produkcja”. Te zasady będą dziedziczyły wszystkie subskrypcje Umowa Enterprise (EA), które są elementami podrzędnymi tej grupy zarządzania i będą miały zastosowanie do wszystkich maszyn wirtualnych w ramach tych subskrypcji. Te zasady zabezpieczeń nie mogą zostać zmienione przez właściciela zasobu lub subskrypcji, co zapewnia ulepszony nadzór.

Innym scenariuszem, w którym można użyć grup zarządzania, jest zapewnienie użytkownikom dostępu do wielu subskrypcji. Przenosząc wiele subskrypcji do tej grupy zarządzania, można utworzyć jedno przypisanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) (RBAC) do grupy zarządzania, które będzie dziedziczyło ten dostęp do wszystkich subskrypcji.
Jedno przypisanie grupy zarządzania może zapewnić użytkownikom dostęp do wszystkiego, czego potrzebują, bez konieczności tworzenia skryptów RBAC dla wielu subskrypcji.

### <a name="important-facts-about-management-groups"></a>Ważne informacje dotyczące grup zarządzania

- W jednym katalogu może być obsługiwane 10000 grup zarządzania.
- Drzewo grupy zarządzania może obsługiwać maksymalnie sześć poziomów głębokości.
  - Ten limit nie obejmuje poziomu głównego lub poziomu subskrypcji.
- Każda grupa zarządzania i subskrypcja może obsługiwać tylko jeden element nadrzędny.
- Każda grupa zarządzania może mieć wiele elementów podrzędnych.
- Wszystkie subskrypcje i grupy zarządzania znajdują się w jednej hierarchii w każdym katalogu. Zobacz [Ważne informacje dotyczące głównej grupy zarządzania](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Główna grupa zarządzania dla każdego katalogu

Do każdego katalogu jest przypisywana jedna grupa zarządzania najwyższego poziomu nazywana „główną” grupą zarządzania.
Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu. [Administrator globalny usługi Azure AD musi podnieść swój poziom uprawnień](../../role-based-access-control/elevate-access-global-admin.md) do roli Administrator dostępu użytkowników, aby początkowo być właścicielem tej głównej grupy. Po podniesieniu poziomu dostępu administrator może przypisać dowolną rolę RBAC innym użytkownikom lub grupom w katalogu, aby zarządzać hierarchią. Jako administrator możesz przypisać własne konto jako właściciela głównej grupy zarządzania.

### <a name="important-facts-about-the-root-management-group"></a>Ważne informacje dotyczące głównej grupy zarządzania

- Domyślnie nazwa wyświetlana głównej grupy zarządzania to **Grupa główna dzierżawy**. Identyfikatorem jest identyfikator usługi Azure Active Directory.
- Aby zmienić nazwę wyświetlaną, Twoje konto musi mieć przypisaną rolę właściciela lub współautora w głównej grupie zarządzania. Aby zaktualizować nazwę grupy zarządzania, zobacz [zmiana nazwy grupy zarządzania](manage.md#change-the-name-of-a-management-group) .
- Głównej grupy zarządzania nie można przenieść ani usunąć, w odróżnieniu od innych grup zarządzania.  
- Wszystkie subskrypcje i grupy zarządzania składają się do jednej głównej grupy zarządzania w katalogu.
  - Wszystkie zasoby w katalogu składają się do głównej grupy zarządzania dla globalnego zarządzania.
  - Nowe subskrypcje są automatycznie domyślnie dodawane do głównej grupy zarządzania podczas tworzenia.
- Główna grupa zarządzania jest widoczna dla wszystkich klientów platformy Azure, ale nie wszyscy klienci mają dostęp do zarządzania tą główną grupą zarządzania.
  - Każdy, kto ma dostęp do subskrypcji, może zobaczyć kontekst, w którym ta subskrypcja znajduje się w hierarchii.  
  - Nikt nie otrzymuje domyślnego dostępu do głównej grupy zarządzania. Administratorzy globalni usługi Azure AD są jedynymi użytkownikami, którzy mogą samodzielnie podnieść swój poziom uprawnień, aby uzyskać dostęp.  Gdy administratorzy globalni mają dostęp do głównej grupy zarządzania, mogą przypisać dowolne role RBAC innym użytkownikom w celu zarządzania.  

> [!IMPORTANT]
> Wszystkie przypisania dostępu użytkowników lub zasad w głównej grupie zarządzania **dotyczą wszystkich zasobów w katalogu**.
> W związku z tym wszyscy klienci powinni ocenić potrzebę posiadania elementów zdefiniowanych w tym zakresie.
> Przypisania dostępu użytkowników i zasad powinny być „niezbędne” tylko w tym zakresie.  

## <a name="initial-setup-of-management-groups"></a>Konfiguracja początkowa grup zarządzania

Kiedy dowolny użytkownik rozpoczyna korzystanie z grup zarządzania, wykonywany jest proces konfiguracji początkowej. Pierwszym jego krokiem jest utworzenie głównej grupy zarządzania w katalogu. Po utworzeniu tej grupy wszystkie istniejące subskrypcje, które znajdują się w katalogu, stają się elementami podrzędnymi głównej grupy zarządzania. Celem tego procesu jest upewnienie się, że istnieje tylko jedna hierarchia grup zarządzania w katalogu. Pojedyncza hierarchia w katalogu umożliwia klientom administracyjnym klientom stosowanie globalnego dostępu i zasad, których inni klienci w katalogu nie mogą obejść. Wszystkie przypisania w głównej grupie będą stosowane do całej hierarchii, która obejmuje wszystkie grupy zarządzania, subskrypcje, grupy zasobów i zasoby w dzierżawie usługi Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Problemy z wyświetlaniem wszystkich subskrypcji

W przypadku niektórych katalogów, które rozpoczęły korzystanie z grup zarządzania niedługo po udostępnieniu wersji zapoznawczej (przed 25 czerwca 2018 r.), mógł wystąpić problem polegający na tym, że nie wszystkie subskrypcje zostały umieszczone w hierarchii. Proces umieszczania subskrypcji w hierarchii był implementowany po przypisaniu roli lub zasad w głównej grupie zarządzania w katalogu. 

### <a name="how-to-resolve-the-issue"></a>Jak rozwiązać ten problem

Istnieją dwie opcje rozwiązania tego problemu.

1. Usuń wszystkie przypisania ról i zasad z głównej grupy zarządzania
   1. Usunięcie wszystkich przypisań ról i zasad z głównej grupy zarządzania spowoduje wypełnienie hierarchii przez usługę wszystkimi subskrypcjami podczas następnego cyklu nocnego.  Ten proces ma na celu zapewnienie, że nie został przypadkowo udzielony dostęp lub nie przypisano przypadkowo zasad do wszystkich subskrypcji dzierżawy.
   1. Najlepszym sposobem wykonania tego procesu bez wpływu na usługi jest zastosowanie przypisań ról lub zasad na poziomie o jeden niższym niż główna grupa zarządzania. Wtedy będzie można usunąć wszystkie przypisania z zakresu głównego.
1. Bezpośrednio wywołaj interfejs API, aby rozpocząć proces wypełniania
   1. Każdy klient w katalogu może wywołać interfejs API *TenantBackfillStatusRequest* lub *StartTenantBackfillRequest*. Wywołanie interfejsu API StartTenantBackfillRequest powoduje rozpoczęcie procesu początkowej konfiguracji, który polega na przeniesieniu wszystkich subskrypcji do hierarchii. Ten proces rozpoczyna również wymuszanie dodawania wszystkich nowych subskrypcji jako elementu podrzędnego głównej grupy zarządzania. Ten proces można wykonać bez zmiany przydziałów na poziomie głównej grupy. Wywołując ten interfejs API, potwierdzasz, że dowolne zasady i przypisania dostępu określone w grupie głównej mogą być stosowane do wszystkich subskrypcji.

Jeśli masz pytania na temat tego procesu wypełniania, napisz na adres managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Dostęp do grupy zarządzania

Grupy zarządzania platformy Azure obsługują [kontrolę dostępu opartą na rolach (RBAC) na platformie Azure](../../role-based-access-control/overview.md) dla wszystkich uprawnień dostępu do zasobów oraz definicji ról.
Te uprawnienia są dziedziczone do zasobów podrzędnych, które istnieją w hierarchii. Każda rola RBAC może być przypisana do grupy zarządzania, która będzie dziedziczyć hierarchię do zasobów.
Na przykład można przypisać do grupy zarządzania rolę RBAC Współautor maszyny wirtualnej. Ta rola nie ma żadnej akcji wykonywanej na grupie zarządzania, ale będzie dziedziczona do wszystkich maszyn wirtualnych w ramach tej grupy zarządzania.

Na poniższym wykresie przedstawiono listę ról i obsługiwane akcje na grupach zarządzania.

| Nazwa roli RBAC             | Tworzenie | Zmiana nazwy | Przenoszenie** | Usuń | Przypisywanie dostępu | Przypisywanie zasad | Odczytywanie  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Właściciel                       | X      | X      | X      | X      | X             | X             | X     |
|Współautor                 | X      | X      | X      | X      |               |               | X     |
|Współautor grupy zarządzania*             | X      | X      | X      | X      |               |               | X     |
|Czytelnik                      |        |        |        |        |               |               | X     |
|Czytelnik grupy zarządzania*                  |        |        |        |        |               |               | X     |
|Współautor zasad zasobów |        |        |        |        |               | X             |       |
|Administrator dostępu użytkowników   |        |        |        |        | X             | X             |       |

*: role Współautor grupy zarządzania i Czytelnik grupy zarządzania zezwalają użytkownikom na wykonywanie tych akcji tylko dla zakresu grupy zarządzania.  
\* *: Przypisania roli w głównej grupie zarządzania nie są wymagane do przeniesienia subskrypcji lub grupy zarządzania do i z niej.  Aby uzyskać szczegółowe informacje o przenoszeniu elementów w hierarchii, zobacz [Zarządzanie zasobami przy użyciu grup zarządzania](manage.md).

## <a name="custom-rbac-role-definition-and-assignment"></a>Niestandardowa definicja roli RBAC i przypisanie

Obsługa niestandardowych ról RBAC dla grup zarządzania jest obecnie w wersji zapoznawczej z pewnymi [ograniczeniami](#limitations).  Zakres grupy zarządzania można zdefiniować w możliwym do przypisania zakresie definicji roli.  Ta niestandardowa rola RBAC będzie wówczas dostępna do przypisania w tej grupie zarządzania oraz we wszystkich zawartych w niej grupach zarządzania, subskrypcjach, grupach zasobów lub zasobach. Ta rola niestandardowa będzie dziedziczyć hierarchię tak jak każda wbudowana rola.    

### <a name="example-definition"></a>Przykładowa definicja
[Definiowanie i tworzenie roli niestandardowej](../../role-based-access-control/custom-roles.md) nie zmienia się wraz z uwzględnieniem grup zarządzania. Użyj pełnej ścieżki, aby zdefiniować grupę zarządzania **/providers/Microsoft.Management/managementgroups/{GroupID}** . 

Użyj identyfikatora grupy zarządzania, a nie nazwy wyświetlanej grupy zarządzania. Ten typowy błąd występuje, ponieważ oba te pola są zdefiniowanymi niestandardowymi podczas tworzenia grupy zarządzania. 

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problemy ze uszkodzeniem definicji roli i ścieżki hierarchii przypisania
Definicje ról są przypisywane w dowolnym miejscu w hierarchii grupy zarządzania. Definicję roli można zdefiniować w nadrzędnej grupie zarządzania, podczas gdy rzeczywiste przypisanie roli istnieje w subskrypcji podrzędnej. Ponieważ istnieje relacja między tymi dwoma elementami, podczas próby oddzielenia przypisania od jego definicji wystąpi błąd. 

Na przykład: Przyjrzyjmy się małej sekcji hierarchii dla wizualizacji. 

![poddrzewo](./media/subtree.png)

Załóżmy, że w grupie zarządzania Marketing zdefiniowano rolę niestandardową. Ta rola niestandardowa jest następnie przypisywana do dwóch subskrypcji bezpłatnych wersji próbnych.  

Jeśli spróbujesz przenieść jedną z tych subskrypcji jako podrzędną grupy zarządzania produkcyjnego, spowoduje to przerwanie ścieżki z przypisania roli subskrypcji do definicji roli grupy zarządzania Marketing. W tym scenariuszu zostanie wyświetlony komunikat o błędzie informujący, że przeniesienie nie jest dozwolone, ponieważ spowoduje przerwanie tej relacji.  

Istnieje kilka różnych opcji umożliwiających rozwiązanie tego scenariusza:
- Przed przeniesieniem subskrypcji do nowego elementu nadrzędnego MG usuń przypisanie roli z subskrypcji.
- Dodaj subskrypcję do zakresu umożliwiającego przypisanie definicji roli.
- Zmień zakres możliwy do przypisania w ramach definicji roli. W powyższym przykładzie można zaktualizować zakresy przypisywane z marketingu do głównej grupy zarządzania, aby można było osiągnąć tę definicję w obu gałęziach hierarchii.   
- Utwórz dodatkową rolę niestandardową, która zostanie zdefiniowana w innej gałęzi.  Ta nowa rola będzie wymagała również zmiany przypisania roli w subskrypcji.  

### <a name="limitations"></a>Ograniczenia  
Istnieją ograniczenia, które istnieją podczas korzystania z ról niestandardowych w grupach zarządzania. 

 - Można zdefiniować tylko jedną grupę zarządzania w przypisywanych zakresach nowej roli.  To ograniczenie jest stosowane w celu zmniejszenia liczby sytuacji, w których definicje ról i przypisania ról są rozłączone.  Dzieje się tak, gdy subskrypcja lub Grupa zarządzania z przypisaniem roli jest przenoszona do innego elementu nadrzędnego, który nie ma definicji roli.   
 - Akcje płaszczyzny danych RBAC nie mogą być zdefiniowane w rolach niestandardowych grupy zarządzania.  To ograniczenie ma miejsce w przypadku, gdy występuje problem z opóźnieniami akcji RBAC aktualizujących dostawców zasobów płaszczyzny danych. Ten problem opóźnienia jest opracowywany, a akcje te zostaną wyłączone z definicji roli w celu ograniczenia ryzyka.
 - Azure Resource Manager nie sprawdza poprawności istnienia grupy zarządzania w zakresie możliwym do przypisania definicji roli.  Jeśli na liście występuje literówka lub nieprawidłowy identyfikator grupy zarządzania, definicja roli nadal zostanie utworzona.   

## <a name="moving-management-groups-and-subscriptions"></a>Przeniesienie grup zarządzania i subskrypcji 

Aby Grupa zarządzania lub subskrypcja była podrzędną inną grupą zarządzania, należy ocenić trzy reguły jako prawdziwe.

Jeśli wykonujesz akcję Przenieś, potrzebujesz: 

-  Uprawnienia Zapis grup zarządzania i przypisywanie ról w podrzędnej subskrypcji lub grupie zarządzania.
   - **Właściciel** przykładu wbudowanej roli
- Dostęp do zapisu grupy zarządzania w docelowej nadrzędnej grupie zarządzania.
   - Wbudowana rola — przykład: **właściciel**, **współautor**, **współautor grupy zarządzania**
- Dostęp do zapisu grupy zarządzania w istniejącej nadrzędnej grupie zarządzania.
   - Wbudowana rola — przykład: **właściciel**, **współautor**, **współautor grupy zarządzania**

**Wyjątek**: Jeśli obiekt docelowy lub istniejąca nadrzędna grupa zarządzania jest główną grupą zarządzania, wymagania dotyczące uprawnień nie są stosowane. Ponieważ główną grupą zarządzania jest domyślny punkt załadunkowy dla wszystkich nowych grup zarządzania i subskrypcji, nie musisz mieć uprawnień do przenoszenia elementu.

Jeśli rola właściciela w subskrypcji jest dziedziczona z bieżącej grupy zarządzania, cele przenoszenia są ograniczone. Subskrypcję można przenieść tylko do innej grupy zarządzania, w której masz rolę właściciela. Nie można przenieść go do grupy zarządzania, w której jesteś współautorem, ponieważ utracisz własność subskrypcji. Jeśli użytkownik jest bezpośrednio przypisany do roli właściciela subskrypcji (niedziedziczonej z grupy zarządzania), można przenieść ją do dowolnej grupy zarządzania, w której jesteś współautorem. 

## <a name="audit-management-groups-using-activity-logs"></a>Inspekcja grup zarządzania przy użyciu dzienników aktywności

Grupy zarządzania są obsługiwane w [dzienniku aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md). Możesz wyszukiwać wszystkie zdarzenia, które wystąpiły w grupie zarządzania w tej samej lokalizacji centralnej co inne zasoby platformy Azure.  Na przykład widoczne są wszystkie przypisania ról i zmiany przypisań zasad w określonej grupie zarządzania.

![Dzienniki aktywności z grupami zarządzania](media/al-mg.png)

Jeśli chcesz wykonać zapytanie dotyczące grup zarządzania spoza witryny Azure Portal, zakres docelowy grup zarządzania wygląda tak: **„/providers/Microsoft.Management/managementGroups/{identyfikator_grupy_zarządzania}”** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](manage.md)
- [Grupy zarządzania w module zasobów programu Azure PowerShell](/powershell/module/az.resources#resources)
- [Grupy zarządzania w interfejsie API REST](/rest/api/resources/managementgroups)
- [Grupy zarządzania w interfejsie wiersza polecenia platformy Azure](/cli/azure/account/management-group)
