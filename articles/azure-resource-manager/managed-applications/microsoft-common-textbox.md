---
title: Element UI TextBox
description: Opisuje element UI Microsoft. Common. TextBox dla Azure Portal. Służy do dodawania niesformatowanego tekstu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652283"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI element

Kontrolka, która może służyć do edycji niesformatowanego tekstu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"my text value"
```

## <a name="remarks"></a>Uwagi

- Jeśli `constraints.required` ma wartość **true**, pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **false**.
- `constraints.regex` jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `constraints.validationMessage` jest ciągiem, który będzie wyświetlany, gdy wartość pola tekstowego nie powiedzie się. Jeśli nie zostanie określony, będą używane wbudowane komunikaty weryfikacyjne pola tekstowego. Wartość domyślna to **null**.
- Możliwe jest określenie wartości `constraints.regex`, gdy `constraints.required` jest ustawiona na **wartość false**. W tym scenariuszu wartość nie jest wymagana, aby pole tekstowe zostało pomyślnie zweryfikowane. Jeśli jest określony, musi być zgodny ze wzorcem wyrażenia regularnego.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
