---
title: Dostawcy zasobów i typy zasobów
description: Opisuje dostawców zasobów, którzy obsługują Menedżer zasobów, ich schematy i dostępne wersje interfejsu API oraz regiony, które mogą hostować zasoby.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 82b8251006a1a2d4edd198eca843489d3720f8d6
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84702775"
---
# <a name="azure-resource-providers-and-types"></a>Dostawcy i typy zasobów platformy Azure

Podczas wdrażania zasobów często konieczne jest pobranie informacji o dostawcach zasobów i typach. Na przykład w celu przechowywania kluczy i kluczy tajnych należy użyć dostawcy zasobów Microsoft.KeyVault. Ten dostawca zasobów udostępnia typ zasobu o nazwie magazyny umożliwiający utworzenie magazynu kluczy.

Nazwa typu zasobu ma następujący format: **{dostawca_zasobów}/{typ_zasobu}**. Typ zasobu dla magazynu kluczy to **Microsoft.KeyVault/magazyny**.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Wyświetlanie wszystkich dostawców zasobów na platformie Azure
* Sprawdź stan rejestracji dostawcy zasobów
* Rejestrowanie dostawcy zasobów
* Wyświetlanie typów zasobów dla dostawcy zasobów
* Wyświetl prawidłowe lokalizacje dla typu zasobu
* Wyświetl prawidłowe wersje interfejsu API dla typu zasobu

Kroki te można wykonać za pomocą Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać listę, która mapuje dostawców zasobów na usługi platformy Azure, zobacz [dostawcy zasobów dla usług platformy Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Azure Portal

Aby wyświetlić wszystkich dostawców zasobów i status rejestracji dla Twojej subskrypcji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.

    ![Wybieranie subskrypcji](./media/resource-providers-and-types/select-all-services.png)

3. W polu **wszystkie usługi** wprowadź **subskrypcję**, a następnie wybierz pozycję **subskrypcje**.
4. Wybierz subskrypcję z listy subskrypcji do wyświetlenia.
5. Wybierz pozycję **dostawcy zasobów** i Wyświetl listę dostępnych dostawców zasobów.

    ![Pokaż dostawców zasobów](./media/resource-providers-and-types/show-resource-providers.png)

6. Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów. Zakres rejestracji jest zawsze subskrypcją. Domyślnie wielu dostawców zasobów jest automatycznie rejestrowanych. Jednak może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Aby zarejestrować dostawcę zasobów, musisz mieć uprawnienia do wykonania `/register/action` operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Aby zarejestrować dostawcę zasobów, wybierz pozycję **zarejestruj**. Na poprzednim zrzucie ekranu link **rejestru** został wyróżniony dla elementu **Microsoft. plan**.

    Nie można wyrejestrować dostawcy zasobów, gdy nadal masz typy zasobów od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące konkretnego dostawcy zasobów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.
3. W polu **wszystkie usługi** wprowadź **Eksplorator zasobów**, a następnie wybierz pozycję **Eksplorator zasobów**.

    ![Wybierz wszystkie usługi](./media/resource-providers-and-types/select-resource-explorer.png)

4. Rozwiń węzeł **dostawcy** , wybierając strzałkę w prawo.

    ![Wybierz dostawców](./media/resource-providers-and-types/select-providers.png)

5. Rozwiń dostawcę zasobów i typ zasobu, który chcesz wyświetlić.

    ![Wybierz typ zasobu](./media/resource-providers-and-types/select-resource-type.png)

6. Menedżer zasobów jest obsługiwana we wszystkich regionach, ale wdrażane zasoby mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą wystąpić ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób. Eksplorator zasobów wyświetla prawidłowe lokalizacje dla typu zasobu.

    ![Pokaż lokalizacje](./media/resource-providers-and-types/show-locations.png)

7. Wersja interfejsu API odpowiada wersji operacji interfejsu API REST wydanej przez dostawcę zasobów. Ponieważ dostawca zasobów udostępnia nowe funkcje, zwalnia nową wersję interfejsu API REST. Eksplorator zasobów wyświetla prawidłowe wersje interfejsu API dla typu zasobu.

    ![Pokaż wersje interfejsu API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i status rejestracji dla Twojej subskrypcji, użyj:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Zwracające wyniki podobne do:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów. Zakres rejestracji jest zawsze subskrypcją. Domyślnie wielu dostawców zasobów jest automatycznie rejestrowanych. Jednak może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Aby zarejestrować dostawcę zasobów, musisz mieć uprawnienia do wykonania `/register/action` operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Zwracające wyniki podobne do:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Nie można wyrejestrować dostawcy zasobów, gdy nadal masz typy zasobów od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące konkretnego dostawcy zasobów, użyj:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Zwracające wyniki podobne do:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Aby wyświetlić typy zasobów dla dostawcy zasobów, użyj:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Które zwraca:

```output
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST wydanej przez dostawcę zasobów. Ponieważ dostawca zasobów udostępnia nowe funkcje, zwalnia nową wersję interfejsu API REST.

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, użyj:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Które zwraca:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwana we wszystkich regionach, ale wdrażane zasoby mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą wystąpić ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób.

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, użyj.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Które zwraca:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i status rejestracji dla Twojej subskrypcji, użyj:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Zwracające wyniki podobne do:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów. Zakres rejestracji jest zawsze subskrypcją. Domyślnie wielu dostawców zasobów jest automatycznie rejestrowanych. Jednak może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Aby zarejestrować dostawcę zasobów, musisz mieć uprawnienia do wykonania `/register/action` operacji dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel.

```azurecli
az provider register --namespace Microsoft.Batch
```

Zwraca komunikat informujący o tym, że rejestracja jest w toku.

Nie można wyrejestrować dostawcy zasobów, gdy nadal masz typy zasobów od tego dostawcy zasobów w ramach subskrypcji.

Aby wyświetlić informacje dotyczące konkretnego dostawcy zasobów, użyj:

```azurecli
az provider show --namespace Microsoft.Batch
```

Zwracające wyniki podobne do:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Aby wyświetlić typy zasobów dla dostawcy zasobów, użyj:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Które zwraca:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST wydanej przez dostawcę zasobów. Ponieważ dostawca zasobów udostępnia nowe funkcje, zwalnia nową wersję interfejsu API REST.

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, użyj:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Które zwraca:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwana we wszystkich regionach, ale wdrażane zasoby mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą wystąpić ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób.

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, użyj.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Które zwraca:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia szablonów Menedżer zasobów, zobacz [tworzenie Azure Resource Manager szablonów](../templates/template-syntax.md). 
* Aby wyświetlić Schematy szablonów dostawcy zasobów, zobacz [Dokumentacja szablonu](/azure/templates/).
* Aby uzyskać listę, która mapuje dostawców zasobów na usługi platformy Azure, zobacz [dostawcy zasobów dla usług platformy Azure](azure-services-resource-providers.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [interfejs API REST platformy Azure](/rest/api/).
