---
title: MultiStorageAccountCombo — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Storage. MultiStorageAccountCombo dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651880"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI element

Grupa kontrolek do tworzenia kilku kont magazynu z nazwami, które zaczynają się od wspólnego prefiksu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Uwagi

- Wartość `defaultValue.prefix` jest łączona z co najmniej jedną liczbą całkowitą w celu wygenerowania sekwencji nazw kont magazynu. Na przykład jeśli `defaultValue.prefix` jest **sa** i `count` ma **2**, generowane są nazwy kont magazynu **SA1** i **SA2** . Nazwy wygenerowanego konta magazynu są sprawdzane automatycznie w celu zapewnienia unikatowości.
- Nazwy kont magazynu są generowane lexicographically na podstawie `count`. Na przykład jeśli `count` wynosi 10, wówczas nazwy kont magazynu kończą się dwoma cyfrowymi liczbami całkowitymi (01, 02, 03).
- Wartość domyślna `defaultValue.prefix` ma **wartość null**, a dla `defaultValue.type` jest **Premium_LRS**.
- Dowolny typ nieokreślony w `constraints.allowedTypes` jest ukryty, a żaden typ nie został określony w `constraints.excludedTypes` jest pokazywany. `constraints.allowedTypes` i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Oprócz generowania nazw kont magazynu, `count` służy do ustawiania odpowiedniego mnożnika dla elementu. Obsługuje ona wartość statyczną, taką jak **2**, lub wartość dynamiczną z innego elementu, np. `[steps('step1').storageAccountCount]`. Wartość domyślna to **1**.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
