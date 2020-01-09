---
title: UserNameTextBox — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. COMPUTE. UserNameTextBox dla Azure Portal. Umożliwia użytkownikom dostarczanie nazw użytkowników systemu Windows lub Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651906"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI element

Kontrolka pola tekstowego z wbudowaną walidacją nazw użytkowników systemu Windows i Linux.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Example name"
```

## <a name="remarks"></a>Uwagi

- Jeśli `constraints.required` ma wartość **true**, pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **true**.
- należy określić `osPlatform` i może to być **system Windows** lub **Linux**.
- `constraints.regex` jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `constraints.validationMessage` jest ciągiem, który będzie wyświetlany, gdy wartość pola tekstowego nie powiedzie się walidacji określonej przez `constraints.regex`. Jeśli nie zostanie określony, będą używane wbudowane komunikaty weryfikacyjne pola tekstowego. Wartość domyślna to **null**.
- Ten element ma wbudowaną weryfikację opartą na wartości określonej dla `osPlatform`. Wbudowanej walidacji można używać razem z niestandardowym wyrażeniem regularnym. Jeśli określono wartość `constraints.regex`, wyzwalane są zarówno wbudowane, jak i niestandardowe walidacji.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
