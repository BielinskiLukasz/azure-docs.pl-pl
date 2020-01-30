---
title: Samouczek — Dodawanie parametrów do szablonu
description: Dodaj parametry do szablonu Azure Resource Manager, aby umożliwić jego wielokrotne użycie.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 89101a96f4fc228e2d5c45d67e10b52ac5d8aa11
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773208"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Samouczek: Dodawanie parametrów do szablonu Menedżer zasobów

W [poprzednim samouczku](template-tutorial-add-resource.md)przedstawiono sposób dodawania konta magazynu do szablonu i wdrażania go. W tym samouczku dowiesz się, jak poprawić szablon poprzez dodanie parametrów. Ukończenie tego samouczka zajmuje około **14 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego zasobów](template-tutorial-add-resource.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Być może zauważono, że wystąpił problem z tym szablonem. Nazwa konta magazynu jest zakodowana na stałe. Tego szablonu można używać tylko do wdrożenia tego samego konta magazynu za każdym razem. Aby wdrożyć konto magazynu o innej nazwie, należy utworzyć nowy szablon, który oczywiście nie jest praktycznym sposobem automatyzacji wdrożeń.

## <a name="make-template-reusable"></a>Tworzenie szablonu do ponownego użycia

Aby można było ponownie używać szablonu, dodajmy parametr, który służy do przekazywania nazwy konta magazynu. Wyróżniony kod JSON w poniższym przykładzie pokazuje, co zmieniło się w szablonie. Parametr **storagename** jest identyfikowany jako ciąg. Maksymalna długość jest równa 24 znaków, aby zapobiec wszelkim nazwom, które są zbyt długie.

Skopiuj cały plik i Zastąp jego zawartość.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Wdrażanie szablonu

Wdróżmy szablon. Poniższy przykład wdraża szablon przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Zwróć uwagę, że podajesz nazwę konta magazynu jako jedną z wartości w poleceniu wdrożenia. Podaj nazwę konta magazynu o tej samej nazwie, która została użyta w poprzednim samouczku.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie założono, że ustawiono zmienną **TemplateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Omówienie aktualizacji zasobów

W poprzedniej sekcji zostało wdrożone konto magazynu o tej samej nazwie, która została wcześniej utworzona. Możesz zastanawiać się, w jaki sposób zasób ma wpływ na ponowne wdrożenie.

Jeśli zasób już istnieje i żadna zmiana nie zostanie wykryta we właściwościach, nie zostanie podjęta żadna akcja. Jeśli zasób już istnieje, a właściwość została zmieniona, zasób zostanie zaktualizowany. Jeśli zasób nie istnieje, zostanie utworzony.

Ten sposób obsługi aktualizacji oznacza, że szablon może obejmować wszystkie zasoby potrzebne do rozwiązania platformy Azure. Możesz bezpiecznie ponownie wdrożyć szablon i wiedzieć, że zasoby są zmieniane lub tworzone tylko w razie konieczności. Na przykład jeśli dodano pliki do konta magazynu, można ponownie wdrożyć konto magazynu bez utraty tych plików.

## <a name="customize-by-environment"></a>Dostosuj według środowiska

Parametry umożliwiają dostosowanie wdrożenia poprzez podanie wartości dopasowanych do danego środowiska. Na przykład można przekazać różne wartości w zależności od tego, czy wdrażasz do środowiska na potrzeby opracowywania, testowania i produkcji.

Poprzedni szablon zawsze wdrożono konto magazynu Standard_LRS. W zależności od środowiska może być konieczna elastyczność wdrażania różnych jednostek SKU. Poniższy przykład pokazuje zmiany w celu dodania parametru dla jednostki SKU. Skopiuj cały plik i wklej go nad szablonem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

Parametr **storageSKU** ma wartość domyślną. Ta wartość jest używana, gdy wartość nie jest określona podczas wdrażania. Zawiera również listę dozwolonych wartości. Te wartości są zgodne z wartościami, które są konieczne do utworzenia konta magazynu. Nie chcesz, aby użytkownicy szablonu mogli przekazać jednostki SKU, które nie działają.

## <a name="redeploy-template"></a>Ponowne wdrażanie szablonu

Wszystko jest gotowe do ponownego wdrożenia. Ponieważ domyślna jednostka SKU jest ustawiona na **Standard_LRS**, nie trzeba podawać wartości dla tego parametru.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

Aby sprawdzić elastyczność szablonu, wdróż ponownie. Tym razem ustaw parametr SKU na **Standard_GRS**. Możesz przekazać nową nazwę, aby utworzyć inne konto magazynu, lub użyć tej samej nazwy do zaktualizowania istniejącego konta magazynu. Obie opcje działają.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Na koniec Uruchommy jeszcze jeden test i zobacz, co się dzieje po przejściu do jednostki SKU, która nie jest jedną z dozwolonych wartości. W takim przypadku testuje scenariusz, w którym użytkownik szablonu traktuje **podstawową** , jest jedną z jednostek SKU.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Polecenie kończy się niepowodzeniem z komunikatem o błędzie z informacją, które wartości są dozwolone. Menedżer zasobów identyfikuje błąd przed rozpoczęciem wdrażania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Ulepszono szablon utworzony w [pierwszym samouczku](template-tutorial-create-first-template.md) przez dodanie parametrów. W następnym samouczku znajdziesz informacje na temat funkcji szablonu.

> [!div class="nextstepaction"]
> [Dodawanie funkcji szablonu](template-tutorial-add-functions.md)