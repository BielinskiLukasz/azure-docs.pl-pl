---
title: Wdróż wiele wystąpień zasobów
description: Użyj operacji kopiowania i tablic w szablonie Azure Resource Manager, aby wdrożyć wiele razy typ zasobu.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 38b5bcd38e0dc8ba8c758e9aa8371857541ba55e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210829"
---
# <a name="resource-iteration-in-azure-resource-manager-templates"></a>Iteracja zasobów w szablonach Azure Resource Manager

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie zasobu w szablonie Azure Resource Manager. Dodając element **copy** do sekcji Resources szablonu, można dynamicznie ustawić liczbę zasobów do wdrożenia. Należy również unikać powtarzania składni szablonu.

Można również użyć kopiowania z [właściwościami](copy-properties.md) i [zmiennymi](copy-variables.md).

Jeśli musisz określić, czy zasób został wdrożony w ogóle, zobacz [warunek elementu](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Iteracja zasobu

Element Copy ma następujący format ogólny:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Właściwość **name** jest dowolną wartością, która identyfikuje pętlę. Właściwość **Count** określa liczbę iteracji dla typu zasobu.

Użyj właściwości **mode** i **BatchSize** , aby określić, czy zasoby są wdrażane równolegle czy w kolejności. Te właściwości są opisane w [numerach seryjnych lub równoległych](#serial-or-parallel).

Poniższy przykład tworzy liczbę kont magazynu określonych w parametrze **storageCount** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

Należy zauważyć, że nazwa każdego zasobu zawiera funkcję `copyIndex()`, która zwraca bieżącą iterację w pętli. Funkcja `copyIndex()` rozpoczyna liczenie od zera. Tak więc, Poniższy przykład:

```json
"name": "[concat('storage', copyIndex())]",
```

Tworzy następujące nazwy:

* storage0
* storage1
* storage2.

Aby przesunąć wartość indeksu, możesz przekazać wartość do funkcji copyIndex(). Liczba iteracji jest nadal określona w elemencie Copy, ale wartość funkcji copyindex jest przesunięta przez określoną wartość. Tak więc, Poniższy przykład:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Tworzy następujące nazwy:

* storage1
* storage2
* storage3

Operacja kopiowania jest przydatna podczas pracy z tablicami, ponieważ można wykonać iterację każdego elementu w tablicy. Użyj funkcji `length` w tablicy, aby określić liczbę iteracji i `copyIndex`, aby pobrać bieżący indeks tablicy.

Poniższy przykład tworzy jedno konto magazynu dla każdej nazwy podanej w parametrze.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="serial-or-parallel"></a>Numer seryjny lub równoległy

Domyślnie Menedżer zasobów tworzy zasoby równolegle. Nie ma żadnego limitu liczby zasobów wdrożonych równolegle, poza całkowitym limitem 800 zasobów w szablonie. Kolejność, w której są tworzone, nie jest gwarantowana.

Można jednak określić, że zasoby są wdrażane w sekwencji. Na przykład podczas aktualizowania środowiska produkcyjnego warto rozłożyć aktualizacje, aby w dowolnym momencie zaktualizować tylko określoną liczbę. Aby przeprowadzić szeregowo wdrożenie więcej niż jednego wystąpienia zasobu, należy ustawić `mode` na **port szeregowy** i `batchSize` do liczby wystąpień do wdrożenia w danym momencie. W trybie serial Menedżer zasobów tworzy zależność od wcześniejszych wystąpień w pętli, dlatego nie uruchamia jednej partii do momentu zakończenia poprzedniej partii.

Na przykład aby przeprowadzić sekwencyjne wdrażanie kont magazynu dwa naraz, należy użyć:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Właściwość Mode akceptuje również **Parallel**, która jest wartością domyślną.

## <a name="depend-on-resources-in-a-loop"></a>Zależą od zasobów w pętli

Należy określić, że zasób zostanie wdrożony po innym zasobie przy użyciu elementu `dependsOn`. Aby wdrożyć zasób zależny od kolekcji zasobów w pętli, podaj nazwę pętli kopiowania w elemencie dependsOn. Poniższy przykład pokazuje, jak wdrożyć trzy konta magazynu przed wdrożeniem maszyny wirtualnej. Pełna definicja maszyny wirtualnej nie jest wyświetlana. Zwróć uwagę, że element Copy ma nazwę ustawioną na `storagecopy`, a element dependsOn dla maszyny wirtualnej jest również ustawiony na `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Iteracja zasobu podrzędnego

Nie można użyć pętli kopiowania dla zasobu podrzędnego. Aby utworzyć więcej niż jedno wystąpienie zasobu, który jest zwykle definiowany jako zagnieżdżony w innym zasobie, należy zamiast tego utworzyć ten zasób jako zasób najwyższego poziomu. Relację z zasobem nadrzędnym definiuje się przy użyciu właściwości typu i nazwy.

Załóżmy na przykład, że zwykle zdefiniujesz zestaw danych jako zasób podrzędny w fabryce danych.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Aby utworzyć więcej niż jeden zestaw danych, przenieś go poza fabrykę danych. Zestaw danych musi znajdować się na tym samym poziomie co Fabryka danych, ale nadal jest zasobem podrzędnym fabryki danych. Relację między zestawem danych a fabryką danych można zachować za pomocą właściwości Typ i nazwa. Ponieważ typ nie może być już wywnioskowany na podstawie jego pozycji w szablonie, należy podać w formacie w pełni kwalifikowany typ: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Aby ustanowić relację nadrzędną/podrzędną z wystąpieniem fabryki danych, podaj nazwę zestawu danych, który zawiera nazwę zasobu nadrzędnego. Użyj formatu: `{parent-resource-name}/{child-resource-name}`.

W poniższym przykładzie przedstawiono implementację:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="copy-limits"></a>Limity kopiowania

Liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. Jeśli szablon jest wdrażany z Azure PowerShell 2,6 lub nowszym, interfejs wiersza polecenia platformy Azure 2.0.74 lub nowszy albo interfejs API REST w wersji **2019-05-10** lub nowszej, można ustawić liczbę na zero. We wcześniejszych wersjach programu PowerShell, interfejsu wiersza polecenia i interfejsie API REST nie są obsługiwane wartości zerowe.

Należy zachować ostrożność przy użyciu [wdrożenia trybu kompletnego](deployment-modes.md) z kopią. W przypadku ponownego wdrożenia z trybem kompletnym do grupy zasobów wszystkie zasoby, które nie są określone w szablonie po usunięciu pętli kopiowania, zostaną usunięte.

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach przedstawiono typowe scenariusze tworzenia więcej niż jednego wystąpienia zasobu lub właściwości.

|Szablon  |Opis  |
|---------|---------|
|[Kopiuj magazyn](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Wdraża więcej niż jedno konto magazynu o numerze indeksu w nazwie. |
|[Magazyn kopii seryjnych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Wdraża kilka kont magazynu jeden w czasie. Nazwa zawiera numer indeksu. |
|[Kopiuj magazyn z tablicą](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Wdraża kilka kont magazynu. Nazwa zawiera wartość z tablicy. |
|[Wdrożenie maszyny wirtualnej z zmienną liczbą dysków z danymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Wdraża kilka dysków danych z maszyną wirtualną. |
|[Wiele reguł zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Wdraża kilka reguł zabezpieczeń w sieciowej grupie zabezpieczeń. Konstruuje reguły zabezpieczeń z parametru. Dla parametru zobacz [wiele plików parametrów sieciowej grupy zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów Menedżer zasobów](template-tutorial-create-multiple-instances.md).
* Aby poznać inne zastosowania elementu Copy, zobacz [iteracja właściwości w szablonach Azure Resource Manager](copy-properties.md) i [iteracji zmiennej w szablonach Azure Resource Manager](copy-variables.md).
* Aby uzyskać informacje na temat używania kopiowania z szablonami zagnieżdżonymi, zobacz [using Copy](linked-templates.md#using-copy).
* Jeśli chcesz dowiedzieć się więcej na temat sekcji szablonu, zobacz [Tworzenie szablonów Azure Resource Manager](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](deploy-powershell.md).

