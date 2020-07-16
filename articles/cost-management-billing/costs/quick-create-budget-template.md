---
title: Szybki start — tworzenie budżetu za pomocą szablonu usługi Azure Resource Manager
description: Przewodnik Szybki start, w którym przedstawiono, w jaki sposób można utworzyć budżet za pomocą szablonu usługi Azure Resource Manager.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 06/10/2020
ms.custom: subject-armqs
ms.openlocfilehash: 5bff8e6057475701a2e78835fb5a950dcb8c8fcb
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252440"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>Szybki start: Tworzenie budżetu przy użyciu szablonu usługi ARM

Budżety w usłudze Cost Management ułatwiają planowanie poprawy odpowiedzialności organizacji. Za pomocą budżetów możesz obserwować koszt wykorzystywanych lub subskrybowanych usług platformy Azure w określonym czasie. Ułatwiają one informowanie innych osób o ich wydatkach, co umożliwia proaktywne zarządzanie kosztami oraz monitorowanie wydatków w czasie. Po przekroczeniu utworzonych progów budżetowych wyzwalane są powiadomienia. Nie ma to wpływu na zasoby ani nie następuje zatrzymanie użycia. Budżety umożliwiają porównywanie i śledzenie wydatków, co ułatwia analizę kosztów. W tym przewodniku Szybki start przedstawiono, w jaki sposób można utworzyć budżet za pomocą szablonu usługi Azure Resource Manager (szablonu ARM).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Szablon ARM obsługuje tylko subskrypcje platformy Azure dla umów Enterprise Agreement (EA). Inne typy subskrypcji nie są obsługiwane przez ten szablon.

Aby tworzyć budżety i zarządzać nimi, musisz mieć uprawnienie współautora. Możesz tworzyć indywidualne budżety dla subskrypcji EA i grup zasobów. Nie możesz jednak tworzyć budżetów dla kont rozliczeniowych EA. W przypadku subskrypcji Azure EA wyświetlanie budżetów wymaga dostępu do odczytu.

Aby móc wyświetlać budżety po ich utworzeniu, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure.

Jeśli masz nową subskrypcję, nie możesz od razu tworzyć budżetu ani korzystać z innych funkcji usługi Cost Management. Aby można było korzystać ze wszystkich funkcji usługi Cost Management, może upłynąć do 48 godzin.

Na potrzeby tworzenia budżetów przez użytkownika i grupę w ramach subskrypcji są obsługiwane następujące uprawnienia, czyli zakresy, platformy Azure. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać budżety dla subskrypcji.
- Współautor i współautor usługi Cost Management — może tworzyć, modyfikować i usuwać swoje budżety. Może też modyfikować kwoty budżetów utworzonych przez innych użytkowników.
- Czytelnik i czytelnik usługi Cost Management — może wyświetlać budżety, do których ma uprawnienia.

Aby uzyskać więcej informacji na temat przypisywania uprawnień do danych usługi Cost Management, zobacz [Przypisywanie dostępu do danych usługi Cost Management](assign-access-acm-data.md).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" range="1-146" highlight="110-139":::

Jeden zasób platformy Azure jest zdefiniowany w szablonie:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Tworzenie budżetu platformy Azure.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon tworzy budżet.

   [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

   [![Szablon usługi Resource Manager, tworzenie budżetu, wdrażanie portalu](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową** i wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**, bądź wybierz istniejącą grupę zasobów.
    * **Lokalizacja**: wybierz lokalizację. Na przykład **Środkowe stany USA**.
    * **Nazwa budżetu**: wprowadź nazwę budżetu. Nazwa powinna być unikatowa w obrębie grupy zasobów. Dozwolone są tylko znaki alfanumeryczne, podkreślenia i łączniki.
    * **Kwota**: wprowadź łączną kwotę kosztu lub użycia do śledzenia w ramach budżetu.
    * **Kategoria budżetu**: wybierz kategorię budżetu (czy budżet ma śledzić **Koszt**, czy **Użycie**).
    * **Ziarno czasu**: wprowadź czas objęty budżetem. Dozwolone wartości to Miesięcznie, Kwartalnie i Rocznie. Budżet jest resetowany na koniec ziarna czasu.
    * **Data rozpoczęcia**: wprowadź datę początkową pierwszego dnia miesiąca w formacie RRRR-MM-DD. Data rozpoczęcia w przyszłości nie powinna być późniejsza niż trzy miesiące od dzisiaj. Dla okresu ziarna czasu możesz określić datę rozpoczęcia z przeszłości.
    * **Data zakończenia**: wprowadź datę końcową budżetu w formacie RRRR-MM-DD. Jeśli nie zostanie podana, przyjmowana jest wartość domyślna wynosząca 10 lat od daty rozpoczęcia.
    * **Operator**: wybierz operator porównania. Możliwe wartości to EqualTo (równe), GreaterThan (większe niż) i GreaterThanOrEqualTo (większe lub równe).
    * **Próg**: wprowadź wartość progową powiadomienia. Powiadomienie jest wysyłane, gdy koszt przekroczy wartość progową. Jest to zawsze wartość procentowa z zakresu od 0 do 1000.
    * **Adresy e-mail do kontaktu**: wprowadź listę adresów e-mail, do których zostanie wysłane powiadomienie o budżecie po przekroczeniu progu. Oczekiwany format to `["user1@domain.com","user2@domain.com"]`.
    * **Role do kontaktu**: wprowadź listę ról do kontaktu, do których zostanie wysłane powiadomienie o budżecie po przekroczeniu progu. Wartości domyślne to Owner (Właściciel), Contributor (Współautor) i Reader (Czytelnik). Oczekiwany format to `["Owner","Contributor","Reader"]`.
    * **Grupy do kontaktu**: wprowadź listę identyfikatorów zasobów grup akcji, w postaci pełnych identyfikatorów URI zasobów, do których zostanie wysłane powiadomienie o budżecie po przekroczeniu progu. Akceptowana jest tablica ciągów. Oczekiwany format to `["action group resource ID1","action group resource ID2"]`. Jeśli nie chcesz używać grup akcji, wprowadź `[]`.
    * **Filtr zasobów**: wprowadź listę filtrów dla zasobów. Oczekiwany format to `["Resource Filter Name1","Resource Filter Name2"]`. Jeśli nie chcesz stosować filtru, wprowadź `[]`. Jeśli podasz filtr zasobów, musisz również wprowadzić wartości **filtrów mierników**.
    * **Filtr mierników**: wprowadź listę filtrów mierników, które są obowiązkowe dla budżetów z kategorii **Użycie**. Oczekiwany format to `["Meter Filter Name1","Meter Filter Name2"]`. Jeśli nie chcesz wprowadzać **filtru zasobów**, wpisz `[]`.
    * **Wyrażam zgodę na powyższe warunki i postanowienia**: Zaznacz.

3. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu budżetu otrzymasz powiadomienie:

   ![Szablon usługi Resource Manager, budżet, wdrażanie powiadomienia w portalu](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz witryny Azure Portal do wdrażania szablonów można również użyć programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure oraz interfejsu API REST. Aby dowiedzieć się więcej o innych szablonach wdrażania, zobacz [Szablony wdrażania](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

W witrynie Azure Portal możesz sprawdzić, czy szablon został utworzony. W tym celu przejdź do pozycji **Zarządzanie kosztami i rozliczenia**, wybierz zakres, a następnie wybierz pozycję **Budżety**. Do wyświetlenia budżetów możesz też użyć następujących skryptów interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już budżetu, usuń go, używając jednej z następujących metod:

### <a name="azure-portal"></a>Azure Portal

Przejdź do obszaru **Zarządzanie kosztami i rozliczenia**, wybierz zakres rozliczeniowy, wybierz pozycję **Budżety**, wybierz odpowiedni budżet, a następnie wybierz pozycję **Usuń budżet**.

### <a name="command-line"></a>Wiersz polecenia

Budżet można usunąć przy użyciu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono wdrożenie budżetu platformy Azure. Aby dowiedzieć się więcej na temat usługi Azure Cost Management i rozliczeń oraz usługi Azure Resource Manager, przejdź do artykułów poniżej.

- Przeczytaj przegląd funkcji [Zarządzanie kosztami i rozliczenia](../cost-management-billing-overview.md)
- [Utwórz budżety](tutorial-acm-create-budgets.md) w witrynie Azure Portal
- Dowiedz się więcej o usłudze [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
