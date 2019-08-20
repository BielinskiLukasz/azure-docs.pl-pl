---
title: CreateUiDefinition. JSON dla środowiska tworzenia aplikacji zarządzanej platformy Azure | Microsoft Docs
description: Opisuje sposób tworzenia definicji interfejsu użytkownika dla Azure Managed Applications
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 783c4f5b1f5a7f2be748bc7173da2d068e1425f4
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575655"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition. JSON dla środowiska tworzenia aplikacji zarządzanej przez platformę Azure

W tym dokumencie przedstawiono podstawowe pojęcia dotyczące pliku **createUiDefinition. JSON** , który Azure Portal używany do definiowania interfejsu użytkownika podczas tworzenia zarządzanej aplikacji.

Szablon jest następujący:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

CreateUiDefinition zawsze zawiera trzy właściwości: 

* jścia
* version
* parameters

Program obsługi powinien zawsze mieć `Microsoft.Azure.CreateUIDef`wartość, a Najnowsza obsługiwana wersja to `0.1.2-preview`.

Schemat właściwości Parameters zależy od kombinacji określonej procedury obsługi i wersji. W przypadku aplikacji zarządzanych obsługiwane właściwości to `basics`, `steps`i `outputs`. Właściwości podstawowe i kroki zawierają [elementy](create-uidefinition-elements.md) , takie jak pola tekstowe i listy rozwijane, do wyświetlenia w Azure Portal. Właściwość Outputs służy do mapowania wartości wyjściowych określonych elementów na parametry szablonu wdrażania Azure Resource Manager.

Włączenie `$schema` jest zalecane, ale opcjonalne. Jeśli jest określona, wartość `version` musi być zgodna z wersją `$schema` w identyfikatorze URI.

Możesz użyć edytora JSON do utworzenia createUiDefinition, a następnie przetestowania go w [piaskownicy createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) w celu uzyskania podglądu. Aby uzyskać więcej informacji na temat piaskownicy, zobacz [testowanie interfejsu portalu dla Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Ustawienia podstawowe

Podstawowe to pierwszy krok generowany, gdy Azure Portal przeanalizuje plik. Oprócz wyświetlania elementów określonych w programie `basics`Portal wprowadza elementy dla użytkowników, aby wybrać subskrypcję, grupę zasobów i lokalizację wdrożenia. Jeśli to możliwe, elementy, które wykonują zapytania dotyczące parametrów obejmujących całe wdrożenie, takie jak nazwa klastra lub poświadczenia administratora, powinny przejść w tym kroku.

## <a name="steps"></a>Kroki

Właściwość kroków może zawierać zero lub więcej dodatkowych kroków, które mają być wyświetlane po podstawach, z których każdy zawiera jeden lub więcej elementów. Rozważ dodanie kroków na rolę lub warstwę wdrażanej aplikacji. Na przykład Dodaj krok dla danych wejściowych węzła głównego i krok dla węzłów procesu roboczego w klastrze.

## <a name="outputs"></a>outputs

Azure Portal używa `outputs` właściwości do mapowania elementów z `basics` i `steps` do parametrów szablonu wdrożenia Azure Resource Manager. Klucze tego słownika są nazwami parametrów szablonu, a wartości są właściwościami obiektów wyjściowych z elementów, do których się odwołuje.

Aby ustawić nazwę zasobu aplikacji zarządzanej, należy uwzględnić wartość o nazwie `applicationResourceName` we właściwości Outputs. Jeśli ta wartość nie zostanie ustawiona, aplikacja przypisze identyfikator GUID dla nazwy. W interfejsie użytkownika można uwzględnić pole tekstowe, które żąda nazwy od użytkownika.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Typy zasobów

Aby odfiltrować dostępne lokalizacje tylko do tych lokalizacji, które obsługują typy zasobów do wdrożenia, podaj tablicę typów zasobów. Jeśli podano więcej niż jeden typ zasobu, zwracane są tylko te lokalizacje, które obsługują wszystkie typy zasobów. Ta właściwość jest opcjonalna.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Funkcje

CreateUiDefinition udostępnia [funkcje](create-uidefinition-functions.md) do pracy z danymi wejściowymi i wyjściowymi elementów oraz funkcjami, takimi jak Conditional. Te funkcje są podobne zarówno do składni, jak i funkcji do Azure Resource Manager funkcji szablonu.

## <a name="next-steps"></a>Następne kroki

Sam plik createUiDefinition. JSON ma prosty schemat. Rzeczywista głębokość jest powiązana ze wszystkimi obsługiwanymi elementami i funkcjami. Te elementy są szczegółowo opisane w:

- [Części](create-uidefinition-elements.md)
- [Funkcje](create-uidefinition-functions.md)

Bieżący schemat JSON dla createUiDefinition jest dostępny tutaj: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Przykładowy plik interfejsu użytkownika można znaleźć w pliku [createUiDefinition. JSON](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
