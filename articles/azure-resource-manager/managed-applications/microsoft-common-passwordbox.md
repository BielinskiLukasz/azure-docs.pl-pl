---
title: PasswordBox — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. PasswordBox dla Azure Portal. Umożliwia użytkownikom podanie wartości klucza tajnego podczas wdrażania aplikacji zarządzanych.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652309"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI element

Kontrolka, która może służyć do udostępniania i potwierdzania hasła.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Uwagi

- Ten element nie obsługuje właściwości `defaultValue`.
- Aby uzyskać szczegółowe informacje dotyczące implementacji `constraints`, zobacz [Microsoft. Common. TextBox](microsoft-common-textbox.md).
- Jeśli `options.hideConfirmation` ma **wartość true**, drugie pole tekstowe potwierdzające hasło użytkownika jest ukryte. Wartość domyślna to **false**.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
