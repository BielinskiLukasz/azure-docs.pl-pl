---
title: Środowiska zarządzania wieloma dzierżawami
description: Zarządzanie zasobami delegowanymi przez platformę Azure umożliwia korzystanie z funkcji zarządzania między dzierżawcami.
ms.date: 11/7/2019
ms.topic: overview
ms.openlocfilehash: 0d04a7a77a3f92cffb185ff829f0d678dac2a9ff
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131918"
---
# <a name="cross-tenant-management-experiences"></a>Środowiska zarządzania wieloma dzierżawami

Jako dostawca usług możesz użyć [delegowanego zarządzania zasobami platformy Azure](../concepts/azure-delegated-resource-management.md) do zarządzania zasobami platformy Azure dla wielu klientów z poziomu dzierżawy w [Azure Portal](https://portal.azure.com). Większość zadań i usług można wykonywać na delegowanych zasobach platformy Azure między zarządzanymi dzierżawcami. W tym artykule opisano niektóre z ulepszonych scenariuszy, w których zarządzanie zasobami delegowanymi przez platformę Azure może być skuteczne.

> [!NOTE]
> Zarządzanie zasobami delegowanymi przez platformę Azure może być również używane w przedsiębiorstwie, które ma wiele dzierżawców, aby uprościć administrację między dzierżawcami.

## <a name="understanding-customer-tenants"></a>Zrozumienie dzierżawców klientów

Dzierżawa usługi Azure Active Directory (Azure AD) jest reprezentacją organizacji. Jest to dedykowane wystąpienie usługi Azure AD, które organizacja otrzymuje podczas tworzenia relacji z firmą Microsoft, rejestrując się na platformie Azure, Microsoft 365 lub innych usługach. Każda dzierżawa usługi Azure AD jest odrębna i oddzielona od innych dzierżaw usługi Azure AD i ma własny identyfikator dzierżawy (GUID). Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

Zazwyczaj, aby zarządzać zasobami platformy Azure dla klienta, dostawcy usług będą musieli zalogować się do Azure Portal przy użyciu konta skojarzonego z dzierżawcą tego klienta, co wymaga od administratora dzierżawy klienta tworzenia kont użytkowników i zarządzania nimi. dla dostawcy usług.

W przypadku zarządzania zasobami delegowanymi przez platformę Azure proces dołączania określa użytkowników w dzierżawie dostawcy usług, którzy będą mogli uzyskiwać dostęp do subskrypcji, grup zasobów i zasobów w dzierżawie klienta oraz zarządzać nimi. Ci użytkownicy mogą następnie zalogować się do Azure Portal przy użyciu własnych poświadczeń. W ramach Azure Portal mogą zarządzać zasobami należącymi do wszystkich klientów, do których mają dostęp. Można to zrobić, odwiedzając stronę [moi klienci](../how-to/view-manage-customers.md) w Azure Portal lub pracując bezpośrednio w kontekście subskrypcji tego klienta w Azure Portal lub za pośrednictwem interfejsów API.

Zarządzanie zasobami delegowanymi przez platformę Azure umożliwia większą elastyczność zarządzania zasobami dla wielu klientów bez konieczności logowania się do różnych kont w różnych dzierżawach. Na przykład dostawca usług może mieć trzech klientów z różnymi zakresami obowiązków i poziomów dostępu, jak pokazano poniżej:

![Trzy dzierżawy klienta pokazujące obowiązki dostawcy usług](../media/azure-delegated-resource-management-customer-tenants.jpg)

Korzystając z funkcji zarządzania zasobami delegowanymi przez platformę Azure, autoryzowani użytkownicy mogą zalogować się do dzierżawy dostawcy usług, aby uzyskać dostęp do tych zasobów, jak pokazano poniżej:

![Zasoby klienta zarządzane za poorednictwem jednej dzierżawy dostawcy usług](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Obsługa interfejsów API i narzędzi do zarządzania

Zadania zarządzania można wykonywać w odniesieniu do zasobów delegowanych bezpośrednio w portalu lub za pomocą interfejsów API i narzędzi do zarządzania (takich jak interfejs wiersza polecenia platformy Azure i Azure PowerShell). Wszystkie istniejące interfejsy API mogą być używane podczas pracy z delegowanymi zasobami, o ile funkcjonalność jest obsługiwana w przypadku zarządzania między dzierżawcami, a użytkownik ma odpowiednie uprawnienia.

Udostępniamy również interfejsy API do wykonywania zadań zarządzania zasobami delegowanymi przez platformę Azure. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą **odwołania** .

## <a name="enhanced-services-and-scenarios"></a>Ulepszone usługi i scenariusze

Większość zadań i usług można wykonać w odniesieniu do zasobów delegowanych między zarządzanymi dzierżawcami. Poniżej przedstawiono niektóre kluczowe scenariusze, w których zarządzanie wieloma dzierżawcami może być skuteczne.

[Usługa Azure ARC dla serwerów (wersja zapoznawcza)](https://docs.microsoft.com/azure/azure-arc/servers/overview):

- [Łączenie maszyn z systemem Windows Server lub Linux poza platformą Azure](https://docs.microsoft.com/azure/azure-arc/servers/quickstart-onboard-portal) z delegowanymi subskrypcjami i/lub grupami zasobów na platformie Azure
- Zarządzanie połączonymi maszynami przy użyciu konstrukcji platformy Azure, takich jak Azure Policy i tagowanie

[Azure Automation](https://docs.microsoft.com/azure/automation/):

- Korzystanie z kont usługi Automation w celu uzyskiwania dostępu do delegowanych zasobów klienta i pracy z nim

[Azure Backup](https://docs.microsoft.com/azure/backup/):

- Tworzenie kopii zapasowych i przywracanie danych klienta w dzierżawach klientów

[Usługa Azure Kubernetes Service (AKS)](https://docs.microsoft.com//azure/aks/):

- Zarządzanie hostowanymi środowiskami Kubernetes oraz wdrażanie aplikacji kontenerowych i zarządzanie nimi w ramach dzierżawców klientów

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Wyświetlanie alertów dla delegowanych subskrypcji z możliwością wyświetlania alertów we wszystkich subskrypcjach
- Wyświetl szczegóły dziennika aktywności dla delegowanych subskrypcji
- Log Analytics: wykonywanie zapytań dotyczących danych ze zdalnych obszarów roboczych klientów w wielu dzierżawcach
- Tworzenie alertów w dzierżawach klientów, które wyzwalają automatyzację, taką jak Azure Automation elementów Runbook lub Azure Functions, w dzierżawie dostawcy usług za pomocą elementów webhook

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/):

- Migawki zgodności pokazują szczegóły przypisanych zasad w ramach delegowanych subskrypcji
- Tworzenie i edytowanie definicji zasad w ramach delegowanej subskrypcji
- Przypisywanie definicji zasad zdefiniowanych przez klienta w ramach delegowanej subskrypcji
- Klienci widzą zasady utworzone przez dostawcę usług wraz ze wszystkimi utworzonymi przez siebie zasadami
- Może [skorygować deployIfNotExists lub zmodyfikować przypisania w ramach dzierżawy klienta](../how-to/deploy-policy-remediation.md)

[Wykres zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/):

- Zawiera teraz identyfikator dzierżawy w zwróconych wynikach zapytania, co pozwala na ustalenie, czy subskrypcja należy do dzierżawy klienta lub dostawcy usług.

[Azure Security Center](https://docs.microsoft.com/azure/security-center/):

- Widoczność między dzierżawami
  - Monitoruj zgodność z zasadami zabezpieczeń i zapewniaj pokrycie zabezpieczeń we wszystkich zasobach dzierżawców
  - Ciągłe monitorowanie zgodności z przepisami dla wielu klientów w jednym widoku
  - Monitoruj, klasyfikacja i ustalaj priorytety rekomendacji dotyczących zabezpieczeń z bezpiecznym obliczaniem wyniku
- Zarządzanie Stanami zabezpieczeń między dzierżawcami
  - Zarządzanie zasadami zabezpieczeń
  - Podejmowanie działań dotyczących zasobów niezgodnych z zaleceniami dotyczącymi zabezpieczeń z możliwością podejmowania działań
  - Zbieranie i przechowywanie danych związanych z zabezpieczeniami
- Wykrywanie zagrożeń i ochrona między dzierżawcami
  - Wykrywanie zagrożeń między zasobami dzierżawców
  - Stosowanie zaawansowanych kontrolek ochrony przed zagrożeniami, takich jak dostęp do maszyny wirtualnej just-in-Time (JIT)
  - Konfiguracja grupy zabezpieczeń sieci z ograniczeniami w ramach adaptacyjnej ochrony sieci
  - Upewnij się, że na serwerach działają tylko aplikacje i procesy, które powinny należeć do adaptacyjnego sterowania aplikacjami
  - Monitorowanie zmian ważnych plików i wpisów rejestru przy użyciu monitorowania integralności plików (FIM)

[Wskaźnik na platformie Azure](https://docs.microsoft.com/azure/sentinel/multiple-tenants-service-providers):

- Zarządzanie zasobami wskaźnikowymi platformy Azure w dzierżawach klientów

[Azure Service Health](https://docs.microsoft.com/azure/service-health/):

- Monitoruj kondycję zasobów klientów za pomocą Azure Resource Health
- Śledzenie kondycji usług platformy Azure używanych przez klientów

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Zarządzanie opcjami odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w dzierżawach klientów (należy pamiętać, że nie można używać kont Uruchom jako do kopiowania rozszerzeń maszyn wirtualnych)

[Virtual Machines platformy Azure](https://docs.microsoft.com/azure/virtual-machines/):

- Korzystanie z rozszerzeń maszyny wirtualnej w celu zapewnienia konfiguracji po wdrożeniu i zadań automatyzacji na maszynach wirtualnych platformy Azure w dzierżawach klientów
- Rozwiązywanie problemów z maszynami wirtualnymi platformy Azure w dzierżawach klientów przy użyciu diagnostyki rozruchu
- Dostęp do maszyn wirtualnych za pomocą konsoli szeregowej w dzierżawach klientów
- Należy pamiętać, że nie można użyć Azure Active Directory do zdalnego logowania do maszyny wirtualnej i nie można zintegrować maszyny wirtualnej z Key Vault hasła, wpisów tajnych lub kluczy kryptograficznych na potrzeby szyfrowania dysków

[Virtual Network platformy Azure](https://docs.microsoft.com/azure/virtual-network/):

- Wdrażaj sieci wirtualne i karty interfejsu sieci wirtualnej (vNICs) w ramach dzierżawców klientów i zarządzaj nimi

Żądania obsługi:

- Otwarte żądania obsługi dla delegowanych zasobów z bloku **Pomoc i obsługa techniczna** w Azure Portal (wybranie planu pomocy technicznej dostępnego dla delegowanego zakresu)

## <a name="current-limitations"></a>Bieżące ograniczenia
We wszystkich scenariuszach należy pamiętać o następujących bieżących ograniczeniach:

- Żądania obsługiwane przez Azure Resource Manager można wykonać przy użyciu funkcji zarządzania zasobami delegowanymi przez platformę Azure. Identyfikatory URI operacji dla tych żądań zaczynają się od `https://management.azure.com`. Jednak żądania, które są obsługiwane przez wystąpienie typu zasobu (takie jak dostęp do magazynu kluczy lub dostęp do danych magazynu), nie są obsługiwane przez delegowane zarządzanie zasobami platformy Azure. Identyfikatory URI operacji dla tych żądań zwykle zaczynają się od adresu, który jest unikatowy dla Twojego wystąpienia, takiego jak `https://myaccount.blob.core.windows.net` lub `https://mykeyvault.vault.azure.net/`. Te ostatnie również są zazwyczaj operacjami na danych, a nie operacjami zarządzania. 
- Przypisania ról muszą używać [wbudowanych ról](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)kontroli dostępu opartej na ROLACH (RBAC). Wszystkie wbudowane role są obecnie obsługiwane przez delegowane zarządzanie zasobami platformy Azure z wyjątkiem właściciela lub wszelkich wbudowanych ról z uprawnieniem [Dataactions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) . Rola Administrator dostępu użytkowników jest obsługiwana tylko w przypadku ograniczonego użycia w [przypisywaniu ról do zarządzanych tożsamości](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Role niestandardowe i [role administratora klasycznej subskrypcji](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) nie są obsługiwane.
- Obecnie nie można dołączyć subskrypcji (lub grupy zasobów w ramach subskrypcji) do zarządzania zasobami delegowanymi przez platformę Azure, jeśli subskrypcja używa Azure Databricks. Podobnie, jeśli subskrypcja została zarejestrowana w celu dołączenia do dostawcy zasobów **Microsoft. ManagedServices** , nie będzie można w tej chwili utworzyć obszaru roboczego dla tej subskrypcji.
- W trakcie dodawania subskrypcji i grup zasobów na potrzeby zarządzania zasobami delegowanymi przez platformę Azure, które mają blokadę zasobów, te blokady nie uniemożliwią wykonywania akcji przez użytkowników w dzierżawie zarządzającej. [Odmów przypisań](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) , które chronią zasoby zarządzane przez system, takie jak te utworzone przez aplikacje zarządzane przez platformę Azure lub plany platformy Azure (przypisań odmowy przypisanych do systemu), uniemożliwiają użytkownikom z dzierżawy zarządzającej wykonywanie tych zasobów. Jednak w tej chwili użytkownicy w dzierżawie klienta nie mogą tworzyć własnych przypisań Odmów (przypisań Odmów przez użytkownika).

## <a name="next-steps"></a>Następne kroki

- Dołączanie klientów do zarządzania zasobami delegowanymi przez platformę Azure za [pomocą szablonów Azure Resource Manager](../how-to/onboard-customer.md) lub [opublikowanie oferty usług zarządzanych w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Wyświetlaj klientów i zarządzaj nimi](../how-to/view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.
